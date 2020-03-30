---
title: Configure as regras de balanceamento e saída de carga usando o Azure PowerShell
titleSuffix: Azure Load Balancer
description: Este artigo mostra como configurar as regras de balanceamento de carga e saída no Standard Load Balancer usando o Azure PowerShell.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/24/2019
ms.author: allensu
ms.openlocfilehash: 5fd68f4559420ca688b3f4d6f6d66ee52db5191e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74225436"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-by-using-azure-powershell"></a>Configure as regras de balanceamento e saída de carga no Standard Load Balancer usando o Azure PowerShell

Este artigo mostra como configurar regras de saída no Standard Load Balancer usando o Azure PowerShell.  

Quando você termina o cenário deste artigo, o recurso balanceador de carga contém duas extremidades frontais e suas regras associadas. Você tem uma frente para tráfego de entrada e outra frente para o tráfego de saída.  

Cada front-end faz referência a um endereço IP público. Neste cenário, o endereço IP público para tráfego de entrada é diferente do endereço para tráfego de saída.   A regra de balanceamento de carga fornece apenas balanceamento de carga de entrada. A regra de saída controla a tradução de endereço de rede de saída (NAT) para o VM.  

O cenário usa duas piscinas de back-end: uma para tráfego de entrada e outra para tráfego de saída. Esses pools ilustram a capacidade e fornecem flexibilidade para o cenário.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="connect-to-your-azure-account"></a>Conectar-se à sua conta do Azure
Entre na assinatura do Azure usando o comando [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0). Em seguida, execute as instruções na tela.
    
```azurepowershell-interactive
Connect-AzAccount
```
## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos usando [o New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados. Os recursos são então gerenciados a partir do grupo.

O seguinte exemplo cria um grupo de recursos chamado *myresourcegroupoutbound* no local *eastus2*:

```azurepowershell-interactive
New-AzResourceGroup -Name myresourcegroupoutbound -Location eastus
```
## <a name="create-a-virtual-network"></a>Criar uma rede virtual
Crie uma rede virtual chamada *myvnetoutbound*. Nomeie sua sub-rede *mysubnetoutbound*. Coloque-o no *myresourcegroupbound* usando [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=azps-2.6.0) e [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=azps-2.6.0).

```azurepowershell-interactive
$subnet = New-AzVirtualNetworkSubnetConfig -Name mysubnetoutbound -AddressPrefix "192.168.0.0/24"

New-AzVirtualNetwork -Name myvnetoutbound -ResourceGroupName myresourcegroupoutbound -Location eastus -AddressPrefix "192.168.0.0/16" -Subnet $subnet
```

## <a name="create-an-inbound-public-ip-address"></a>Crie um endereço IP público de entrada 

Para acessar seu aplicativo web na internet, você precisa de um endereço IP público para o balanceador de carga. O Standard Load Balancer suporta apenas endereços IP públicos padrão. 

Use [o New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=azps-2.6.0) para criar um endereço IP público padrão chamado *mypublicipinbound* no *myresourcegroupoutbound*.

```azurepowershell-interactive
$pubIPin = New-AzPublicIpAddress -ResourceGroupName myresourcegroupoutbound -Name mypublicipinbound -AllocationMethod Static -Sku Standard -Location eastus
```

## <a name="create-an-outbound-public-ip-address"></a>Criar um endereço IP público de saída 

Crie um endereço IP padrão para a configuração de saída front-end do balanceador de carga usando [o New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=azps-2.6.0).

```azurepowershell-interactive
$pubIPout = New-AzPublicIpAddress -ResourceGroupName myresourcegroupoutbound -Name mypublicipoutbound -AllocationMethod Static -Sku Standard -Location eastus
```

## <a name="create-an-azure-load-balancer"></a>Criar um balanceador de carga do Azure

Esta seção explica como criar e configurar os seguintes componentes do balanceador de carga:
  - Um IP front-end que recebe o tráfego de rede de entrada no balanceador de carga
  - Um pool back-end onde o IP front-end envia o tráfego de rede balanceado de carga
  - Um pool back-end para conectividade de saída
  - Uma sonda de saúde que determina a saúde das instâncias de VM back-end
  - Uma regra de entrada do balanceador de carga que define como o tráfego é distribuído para as VMs
  - Uma regra de saída do balanceador de carga que define como o tráfego é distribuído a partir das VMs

### <a name="create-an-inbound-front-end-ip"></a>Criar um IP front-end de entrada
Crie a configuração IP front-end de entrada para o balanceador de carga usando [o New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig?view=azps-2.6.0). O balanceador de carga deve incluir uma configuração IP front-end de entrada chamada *myfrontendinbound*. Associe essa configuração ao endereço IP público *mypublicipinbound*.

```azurepowershell-interactive
$frontendIPin = New-AzLoadBalancerFrontendIPConfig -Name "myfrontendinbound" -PublicIpAddress $pubIPin
```
### <a name="create-an-outbound-front-end-ip"></a>Criar um IP front-end de saída
Crie a configuração IP front-end de saída para o balanceador de carga usando [o New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig?view=azps-2.6.0). Este balanceador de carga deve incluir uma configuração IP front-end de saída chamada *myfrontendoutbound*. Associe essa configuração ao endereço IP público *mypublicipoutbound*.

```azurepowershell-interactive
$frontendIPout = New-AzLoadBalancerFrontendIPConfig -Name "myfrontendoutbound" -PublicIpAddress $pubIPout
```
### <a name="create-an-inbound-back-end-pool"></a>Criar um pool de back-end de entrada
Crie o pool de entrada back-end para o balanceador de carga usando [new-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig?view=azps-2.6.0). Nomeie o pool *bepoolinbound*.

```azurepowershell-interactive
$bepoolin = New-AzLoadBalancerBackendAddressPoolConfig -Name bepoolinbound
``` 

### <a name="create-an-outbound-back-end-pool"></a>Criar um pool de back-end de saída
Use o comando a seguir para criar outro pool de endereços back-end para definir conectividade de saída para um pool de VMs usando [New-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig?view=azps-2.6.0). Nomeie este pool *bepooloutbound*. 

Ao criar um pool de saída separado, você oferece a máxima flexibilidade. Mas você pode omiti-lo este passo e usar apenas o *bepoolinbound* de entrada, se preferir.  

```azurepowershell-interactive
$bepoolout = New-AzLoadBalancerBackendAddressPoolConfig -Name bepooloutbound
``` 

### <a name="create-a-health-probe"></a>Criar uma investigação de integridade

Um teste de saúde verifica todas as instâncias de VM para se certificar de que eles podem enviar tráfego de rede. A instância vm que falha nas verificações do teste é removida do balanceador de carga até que ele volte a funcionar e uma verificação do teste determina que ele é saudável. 

Para monitorar a saúde das VMs, crie um teste de saúde usando [o New-AzLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerprobeconfig?view=azps-2.6.0). 

```azurepowershell-interactive
$probe = New-AzLoadBalancerProbeConfig -Name http -Protocol "http" -Port 80 -IntervalInSeconds 15 -ProbeCount 2 -RequestPath /
```
### <a name="create-a-load-balancer-rule"></a>Crie uma regra de balanceador de carga

Uma regra de balanceador de carga define a configuração de IP front-end para o tráfego de entrada e o pool de back-end para receber o tráfego. Também define a fonte e o porto de destino necessários. 

Crie uma regra de balanceador de carga chamada *myinboundlbrule* usando [New-AzLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerruleconfig?view=azps-2.6.0). Esta regra ouvirá a porta 80 no pool front-end *myfrontendinbound*. Ele também usará a porta 80 para enviar tráfego de rede balanceado de carga para o pool de *endereços de back-end bepoolinbound*. 

```azurepowershell-interactive
$inboundRule = New-AzLoadBalancerRuleConfig -Name inboundlbrule -FrontendIPConfiguration $frontendIPin -BackendAddressPool $bepoolin -Probe $probe -Protocol "Tcp" -FrontendPort 80 -BackendPort 80 -IdleTimeoutInMinutes 15 -EnableFloatingIP -LoadDistribution SourceIP -DisableOutboundSNAT
```

>[!NOTE]
>Esta regra de balanceamento de carga desativa o NAT (Automatic Outbound Secure NAT) por causa do parâmetro **-DisableOutboundSNAT.** O NAT de saída é fornecido apenas pela regra de saída.

### <a name="create-an-outbound-rule"></a>Criar uma regra de saída

Uma regra de saída define o IP público front-end, que é representado pelo *front-end myfrontendbound*. Este front-end será usado para todo o tráfego NAT de saída, bem como para o pool back-end ao qual a regra se aplica.  

Use o comando a seguir para criar uma regra de saída *myoutboundrule* para a tradução de rede de saída de todas as VMs (em configurações DE NIC IP) no pool de back-end *do bepool.*  O comando altera o tempo de saída de 4 para 15 minutos. Ele aloca 10.000 portas SNAT em vez de 1.024. Para obter mais informações, consulte [New-AzLoadBalancerOutboundRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalanceroutboundruleconfig?view=azps-2.7.0).

```azurepowershell-interactive
 $outboundRule = New-AzLoadBalancerOutBoundRuleConfig -Name outboundrule -FrontendIPConfiguration $frontendIPout -BackendAddressPool $bepoolout -Protocol All -IdleTimeoutInMinutes 15 -AllocatedOutboundPort 10000
```
Se você não quiser usar um pool de saída separado, você pode alterar o argumento do pool de endereços no comando anterior para especificar *$bepoolin* em vez disso.  Recomendamos o uso de piscinas separadas para tornar a configuração resultante flexível e legível.

### <a name="create-a-load-balancer"></a>Criar um balanceador de carga

Use o seguinte comando para criar um balanceador de carga para o endereço IP de entrada usando [o New-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancer?view=azps-2.6.0). Nomeie o balanceador de carga *lb*. Ele deve incluir uma configuração ip front-end de entrada. Seu *pool de back-end deve* ser associado ao endereço IP público *mypublicipinbound* que você criou na etapa anterior.

```azurepowershell-interactive
New-AzLoadBalancer -Name lb -Sku Standard -ResourceGroupName myresourcegroupoutbound -Location eastus -FrontendIpConfiguration $frontendIPin,$frontendIPout -BackendAddressPool $bepoolin,$bepoolout -Probe $probe -LoadBalancingRule $inboundrule -OutboundRule $outboundrule 
```

Neste ponto, você pode continuar adicionando suas VMs aos pools de back-end *bepoolinbound* e *bepooloutbound* atualizando a configuração IP dos respectivos recursos de NIC. Atualize a configuração de recursos usando [Add-AzNetworkInterfaceIpConfig](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest).

## <a name="clean-up-resources"></a>Limpar recursos

Quando você não precisar mais do grupo de recursos, do balanceador de carga e dos recursos relacionados, você pode [removê-los usando o Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.7.0).

```azurepowershell-interactive 
  Remove-AzResourceGroup -Name myresourcegroupoutbound
```

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você criou um balanceador de carga padrão, configurou as regras de tráfego de tráfego de entrada e saída e configurou um teste de saúde para as VMs no pool de back-end. 

Para saber mais, continue com os [tutoriais do Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md).
