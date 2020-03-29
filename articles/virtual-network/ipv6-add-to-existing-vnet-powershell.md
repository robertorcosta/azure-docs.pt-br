---
title: Atualize um aplicativo IPv4 para IPv6 na Rede Virtual Azure - PowerShell
titlesuffix: Azure Virtual Network
description: Este artigo mostra como implantar endereços IPv6 em um aplicativo existente na rede virtual Azure usando o Azure Powershell.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/21/2019
ms.author: kumud
ms.openlocfilehash: d08ce1c382d173ac98a0e61e6117ed50b958ba44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76119798"
---
# <a name="upgrade-an-ipv4-application-to-ipv6-in-azure-virtual-network---powershell-preview"></a>Atualize um aplicativo IPv4 para IPv6 na rede virtual Azure - PowerShell (Preview)

Este artigo mostra como adicionar conectividade IPv6 a um aplicativo IPv4 existente em uma rede virtual Azure com um Balanceador de Carga Padrão e IP público. A atualização no local inclui:
- Espaço de endereço IPv6 para a rede virtual e sub-rede
- um Balanceador de Carga Padrão com configurações frontend IPv4 e IPV6
- VMs com NICs que possuem uma configuração IPv4 + IPv6
- IPv6 Public IP para que o balanceador de carga tenha conectividade IPv6 voltada para a Internet

> [!Important]
> O suporte ao IPv6 para a Rede Virtual Azure está atualmente em visualização pública. Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Veja os [Termos de Uso Adicionais para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter detalhes.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar o PowerShell localmente, este artigo requer a versão 6.9.0 ou posterior do módulo Azure PowerShell. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-Az-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Connect-AzAccount` para criar uma conexão com o Azure.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="register-the-service"></a>Registre o serviço

Antes de implantar um aplicativo de pilha dupla no Azure, você deve configurar sua assinatura para este recurso de visualização usando o seguinte Azure PowerShell:

Registre-se da seguinte forma:
```azurepowershell
Register-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
Register-AzProviderFeature -FeatureName AllowIPv6CAOnStandardLB -ProviderNamespace Microsoft.Network
```
Demora até 30 minutos para a conclusão do registro de recursos. Você pode verificar seu status de registro executando o seguinte comando Azure PowerShell: Verifique o registro da seguinte forma:
```azurepowershell
Get-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
Get-AzProviderFeature -FeatureName AllowIPv6CAOnStandardLB -ProviderNamespace Microsoft.Network
```
Após a conclusão do registro, execute o seguinte comando:

```azurepowershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

### <a name="create-a-standard-load-balancer"></a>Criar um Balanceador de Carga Standard
Este artigo pressupõe que você implantou um Balancer de Carga Padrão como descrito no [Quickstart: Crie um Balancer de Carga Padrão - Azure PowerShell](../load-balancer/quickstart-create-standard-load-balancer-powershell.md).

## <a name="retrieve-the-resource-group"></a>Recuperar o grupo de recursos

Antes de criar sua rede virtual de pilha dupla, você deve recuperar o grupo de recursos com [o Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup).

```azurepowershell
 $rg = Get-AzResourceGroup  -ResourceGroupName "myResourceGroupSLB"
```

## <a name="create-an-ipv6-ip-addresses"></a>Crie um endereço IP IPv6

Crie um endereço IPv6 público com [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) para o balanceador de carga padrão. O exemplo a seguir cria um endereço IP público IPv6 chamado *PublicIP_v6* no grupo de recursos *myResourceGroupSLB:*

```azurepowershell
  
  $PublicIP_v6 = New-AzPublicIpAddress `
  -Name "PublicIP_v6" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Sku Standard  `
  -AllocationMethod Static `
  -IpAddressVersion IPv6
```

## <a name="configure-load-balancer-frontend"></a>Configure frontend do balanceador de carga

Recupere a configuração do balanceador de carga existente e adicione o novo endereço IP IPv6 usando [Add-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/Add-AzLoadBalancerFrontendIpConfig) da seguinte forma:

```azurepowershell
# Retrieve the load balancer configuration
$lb = Get-AzLoadBalancer -ResourceGroupName $rg.ResourceGroupName -Name "MyLoadBalancer"
# Add IPv6 components to the local copy of the load balancer configuration
$lb | Add-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v6" `
  -PublicIpAddress $PublicIP_v6
#Update the running load balancer with the new frontend
$lb | Set-AzLoadBalancer
```

## <a name="configure-load-balancer-backend-pool"></a>Configurar o pool de backend do balanceador de carga

Crie o pool de back-end na cópia local da configuração do balanceador de carga e atualize o balanceador de carga em execução com a nova configuração do pool de backend da seguinte forma:

```azurepowershell
$lb | Add-AzLoadBalancerBackendAddressPoolConfig -Name "LbBackEndPool_v6"
# Update the running load balancer with the new backend pool
$lb | Set-AzLoadBalancer
```

## <a name="configure-load-balancer-rules"></a>Configurar as regras do balanceador de carga
Recupere a configuração existente do frontend e do pool de back-end do balanceador de carga e adicione novas regras de balanceamento de carga usando [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/Add-AzLoadBalancerRuleConfig).

```azurepowershell
# Retrieve the updated (live) versions of the frontend and backend pool
$frontendIPv6 = Get-AzLoadBalancerFrontendIpConfig -Name "dsLbFrontEnd_v6" -LoadBalancer $lb
$backendPoolv6 = Get-AzLoadBalancerBackendAddressPoolConfig -Name "LbBackEndPool_v6" -LoadBalancer $lb
# Create new LB rule with the frontend and backend
$lb | Add-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v6" `
  -FrontendIpConfiguration $frontendIPv6 `
  -BackendAddressPool $backendPoolv6 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80
#Finalize all the load balancer updates on the running load balancer
$lb | Set-AzLoadBalancer
```
## <a name="add-ipv6-address-ranges"></a>Adicionar intervalos de endereços IPv6

Adicionar faixas de endereço IPv6 à rede virtual e à sub-rede que hospeda as VMs da seguinte forma:

```azurepowershell
#Add IPv6 ranges to the VNET and subnet
#Retreive the VNET object
$vnet = Get-AzVirtualNetwork  -ResourceGroupName $rg.ResourceGroupName -Name "myVnet" 
#Add IPv6 prefix to the VNET
$vnet.addressspace.addressprefixes.add("ace:cab:deca::/48")
#Update the running VNET
$vnet |  Set-AzVirtualNetwork

#Retrieve the subnet object from the local copy of the VNET
$subnet= $vnet.subnets[0]
#Add IPv6 prefix to the Subnet (subnet of the VNET prefix, of course)
$subnet.addressprefix.add("ace:cab:deca::/64")
#Update the running VNET with the new subnet configuration
$vnet |  Set-AzVirtualNetwork

```
## <a name="add-ipv6-configuration-to-nic"></a>Adicionar configuração IPv6 à NIC

Configure todos os NICs VM com um endereço IPv6 usando [Add-AzNetworkInterfaceIpConfig](/powershell/module/az.network/Add-AzNetworkInterfaceIpConfig) da seguinte forma:

```azurepowershell

#Retrieve the NIC objects
$NIC_1 = Get-AzNetworkInterface -Name "myNic1" -ResourceGroupName $rg.ResourceGroupName
$NIC_2 = Get-AzNetworkInterface -Name "myNic2" -ResourceGroupName $rg.ResourceGroupName
$NIC_3 = Get-AzNetworkInterface -Name "myNic3" -ResourceGroupName $rg.ResourceGroupName
#Add an IPv6 IPconfig to NIC_1 and update the NIC on the running VM
$NIC_1 | Add-AzNetworkInterfaceIpConfig -Name MyIPv6Config -Subnet $vnet.Subnets[0]  -PrivateIpAddressVersion IPv6 -LoadBalancerBackendAddressPool $backendPoolv6 
$NIC_1 | Set-AzNetworkInterface
#Add an IPv6 IPconfig to NIC_2 and update the NIC on the running VM
$NIC_2 | Add-AzNetworkInterfaceIpConfig -Name MyIPv6Config -Subnet $vnet.Subnets[0]  -PrivateIpAddressVersion IPv6 -LoadBalancerBackendAddressPool $backendPoolv6 
$NIC_2 | Set-AzNetworkInterface
#Add an IPv6 IPconfig to NIC_3 and update the NIC on the running VM
$NIC_3 | Add-AzNetworkInterfaceIpConfig -Name MyIPv6Config -Subnet $vnet.Subnets[0]  -PrivateIpAddressVersion IPv6 -LoadBalancerBackendAddressPool $backendPoolv6 
$NIC_3 | Set-AzNetworkInterface

```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Exibir rede virtual de pilha dupla IPv6 no portal Azure
Você pode visualizar a rede virtual iPv6 dual stack no portal Azure da seguinte forma:
1. Na barra de pesquisa do portal, digite *myVnet*.
2. Quando **o myVnet** aparecer nos resultados da pesquisa, selecione-o. Isso lança a página **Visão Geral** da rede virtual dual stack chamada *myVNet*. A rede virtual dual stack mostra os três NICs com as configurações IPv4 e IPv6 localizadas na sub-rede de pilha dupla chamada *mySubnet*.

  ![Rede virtual de pilha dupla IPv6 no Azure](./media/ipv6-add-to-existing-vnet-powershell/ipv6-dual-stack-vnet.png)

> [!NOTE]
> A rede virtual IPv6 for Azure está disponível no portal Azure apenas para esta versão de pré-visualização.

## <a name="clean-up-resources"></a>Limpar recursos

Quando não forem mais necessários, você poderá usar o comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos, a VM e todos os recursos relacionados.

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyAzureResourceGroupSLB
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você atualizou um Balancer de Carga Padrão existente com uma configuração IP frontend IPv4 para uma configuração de pilha dupla (IPv4 e IPv6). Você também adicionou configurações IPv6 aos NICs das VMs no pool de backend e à Rede Virtual que as hospeda. Para saber mais sobre o suporte ao IPv6 nas redes virtuais do Azure, veja [o que é IPv6 para Azure Virtual Network?](ipv6-overview.md)
