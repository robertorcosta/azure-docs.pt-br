---
title: Implantar um aplicativo de pilha dupla IPv6 usando o Balancer de carga interna padrão no Azure - PowerShell
titlesuffix: Azure Virtual Network
description: Este artigo mostra como implantar um aplicativo de pilha dupla IPv6 com o Standard Internal Load Balancer na rede virtual Azure usando o Azure Powershell.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/14/2019
ms.author: kumud
ms.openlocfilehash: fdf726fd31e8b92a04a1c136eb5cd7110e0c6d5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72333359"
---
# <a name="deploy-an-ipv6-dual-stack-application-using-standard-internal-load-balancer-in-azure---powershell-preview"></a>Implantar um aplicativo de pilha dupla IPv6 usando o Balancer de carga interna padrão no Azure - PowerShell (Preview)

Este artigo mostra como implantar um aplicativo dual stack (IPv4 + IPv6) no Azure que inclui uma rede virtual e uma sub-rede de pilha dupla, um Balancer de carga interna padrão com configurações front-end duplas (IPv4 + IPv6), VMs com NICs que possuem um IP duplo configuração, grupo de segurança de rede e IPs públicos.

> [!Important]
> O suporte ao IPv6 para a Rede Virtual Azure está atualmente em visualização pública. Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Veja os [Termos de Uso Adicionais para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter detalhes.

O procedimento para criar um Balanceador de Carga Interna com capacidade para IPv6 é quase idêntico ao processo de criação de um Balanceador de Carga IPv6 voltado para a Internet descrito [aqui](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md). As únicas diferenças para criar um balanceador de carga interna estão na configuração front-end, conforme ilustrado no exemplo do PowerShell abaixo:

```azurepowershell
 $frontendIPv6 = New-AzLoadBalancerFrontendIpConfig `
 -Name "dsLbFrontEnd_v6" `
 -PrivateIpAddress "ace:cab:deca:deed::100" `
 -PrivateIpAddressVersion "IPv6" `
 -Subnet $DsSubnet
```

As alterações que tornam a configuração front-end do balanceador de carga acima uma configuração front-end do balanceador de carga são:
- O `PrivateIpAddressVersion` é especificado como "IPv6"
- O `-PublicIpAddress` argumento foi omitido ou `-PrivateIpAddress`substituído por . Observe que o endereço privado deve estar no intervalo do espaço IP da sub-rede no qual o balanceador de carga interno será implantado. Se uma `-PrivateIpAddress` estática for omitida, o próximo endereço IPv6 gratuito será selecionado na sub-rede na qual o Balancer de carga interna é implantado.
- A sub-rede de pilha dupla na qual o balanceador `-Subnet` `-SubnetId` de carga interna será implantado é especificada com um ou argumento.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar o PowerShell localmente, este artigo requer a versão 6.9.0 ou posterior do módulo Azure PowerShell. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-Az-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Connect-AzAccount` para criar uma conexão com o Azure.

## <a name="prerequisites"></a>Pré-requisitos
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

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Antes de criar sua rede virtual de pilha dupla, você deve criar um grupo de recursos com [o New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). O exemplo a seguir cria um grupo de recursos chamado *dsStd_ILB_RG* na localização leste *dos EUA:*

```azurepowershell
$rg = New-AzResourceGroup `
  -ResourceGroupName "dsStd_ILB_RG"  `
  -Location "east us"
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses"></a>Crie endereços IP públicos IPv4 e IPv6
Para acessar suas máquinas virtuais a partir da Internet, você precisa de endereços IP públicos IPv4 e IPv6 para as VMs. Crie endereços IP públicos com [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). O exemplo a seguir cria endereço IP público IPv4 e IPv6 chamado *RdpPublicIP_1* e *RdpPublicIP_2* no grupo de recursos *dsStd_ILB_RG:*

```azurepowershell
$RdpPublicIP_1 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_1" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Static `
  -IpAddressVersion IPv4  `
  -sku Standard
  
$RdpPublicIP_2 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_2" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Static `
  -IpAddressVersion IPv4  `
  -sku Standard
```
## <a name="create-the-virtual-network-and-the-subnet"></a>Crie a rede virtual e a sub-rede

Crie uma rede virtual usando [a New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) com a configuração de uma sub-rede dupla usando [o New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig). O exemplo a seguir cria uma rede virtual chamada *dsVnet* com *dsSubnet*.

```azurepowershell
# Create dual stack subnet config
$DsSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name "dsSubnet" `
  -AddressPrefix "10.0.0.0/24","ace:cab:deca:deed::/64"

# Create the virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsVnet" `
  -AddressPrefix "10.0.0.0/16","ace:cab:deca::/48"  `
  -Subnet $DsSubnet

#Refresh the fully populated subnet for use in load balancer frontend configuration
$DsSubnet = get-AzVirtualNetworkSubnetconfig -name dsSubnet -VirtualNetwork $vnet
```
## <a name="create-standard-load-balancer"></a>Criar o Standard Load Balancer

Nesta seção, você configura o IP frontend duplo (IPv4 e IPv6) e o pool de endereços back-end para o balanceador de carga e, em seguida, cria um Balanceador de carga padrão.

### <a name="create-front-end-ip"></a>Criar IP de front-end

Crie um IP de front-end com [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig). O exemplo a seguir cria configurações IP frontend IPv4 e IPv6 chamadas *dsLbFrontEnd_v4* e *dsLbFrontEnd_v6:*

```azurepowershell
$frontendIPv4 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v4" `
  -PrivateIpAddress "10.0.0.100"  `
  -PrivateIpAddressVersion "IPv4"   `
  -Subnet $DsSubnet

$frontendIPv6 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v6" `
  -PrivateIpAddress "ace:cab:deca:deed::100"  `
  -PrivateIpAddressVersion "IPv6"   `
  -Subnet $DsSubnet

```

### <a name="configure-back-end-address-pool"></a>Configurar o pool de endereços de back-end

Crie um pool de endereços de back-end com [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig). As VMs são anexadas a este pool de back-end nas etapas restantes. O exemplo a seguir cria pools de endereços back-end chamados *dsLbBackEndPool_v4* e *dsLbBackEndPool_v6* para incluir VMs com configurações IPV4 e IPv6 NIC:

```azurepowershell
$backendPoolv4 = New-AzLoadBalancerBackendAddressPoolConfig -Name "dsLbBackEndPool_v4"

$backendPoolv6 = New-AzLoadBalancerBackendAddressPoolConfig -Name "dsLbBackEndPool_v6"
```

### <a name="create-a-load-balancer-rule"></a>Criar uma regra de balanceador de carga

Uma regra de balanceador de carga é usada para definir como o tráfego é distribuído para as VMs. Defina a configuração de IP de front-end para o tráfego de entrada e o pool de IPs de back-end para receber o tráfego, junto com as portas de origem e de destino necessárias. Para garantir que apenas vMs saudáveis recebam tráfego, você pode definir opcionalmente uma sonda de saúde. O balanceador de carga básica usa uma sonda IPv4 para avaliar a saúde tanto para os pontos finais IPv4 quanto IPv6 nos VMs. O balanceador de carga padrão inclui suporte para sondas de saúde Explicitamente IPv6.

Crie uma regra de balanceador de carga com [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig). O exemplo a seguir cria regras de balanceador de carga chamadas *dsLBrule_v4* e *dsLBrule_v6* e equilibra o tráfego na porta *TCP* *80* para as configurações IP frontend IPv4 e IPv6:

```azurepowershell
$lbrule_v4 = New-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v4" `
  -FrontendIpConfiguration $frontendIPv4 `
  -BackendAddressPool $backendPoolv4 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80

$lbrule_v6 = New-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v6" `
  -FrontendIpConfiguration $frontendIPv6 `
  -BackendAddressPool $backendPoolv6 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80
```

### <a name="create-load-balancer"></a>Criar um balanceador de carga

Crie um Balanceador de carga padrão com [o New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer). O exemplo a seguir cria um Balancer de carga padrão público chamado *myInternalLoadBalancer* usando as configurações ip frontend IPv4 e IPv6, pools de back-end e regras de balanceamento de carga que você criou nas etapas anteriores:

```azurepowershell
$lb = New-AzLoadBalancer  `
  -ResourceGroupName $rg.ResourceGroupName  `
  -Location $rg.Location  `
  -Name  "MyInternalLoadBalancer"  `
  -Sku "Standard"  `
  -FrontendIpConfiguration  $frontendIPv4,$frontendIPv6  `
  -BackendAddressPool  $backendPoolv4,$backendPoolv6  `
  -LoadBalancingRule  $lbrule_v4,$lbrule_v6

```

## <a name="create-network-resources"></a>Criar recursos da rede
Antes de implantar algumas VMs e testar seu balanceador, você deve criar recursos de rede de suporte - conjunto de disponibilidade, grupo de segurança de rede e NICs virtuais. 

### <a name="create-an-availability-set"></a>Criar um conjunto de disponibilidade
Para melhorar a alta disponibilidade do seu aplicativo, coloque suas VMs em um conjunto de disponibilidade.

Crie um conjunto de disponibilidade com [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset). O exemplo a seguir cria um conjunto de disponibilidade chamado *dsAVset:*

```azurepowershell
$avset = New-AzAvailabilitySet `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsAVset" `
  -PlatformFaultDomainCount 2 `
  -PlatformUpdateDomainCount 2 `
  -Sku aligned
```

### <a name="create-network-security-group"></a>Criar um grupo de segurança de rede

Crie um grupo de segurança de rede para as regras que regem a comunicação de entrada e saída em seu VNet.

#### <a name="create-a-network-security-group-rule-for-port-3389"></a>Criar uma regra do grupo de segurança de rede para a porta 3389

Crie uma regra do grupo de segurança de rede para permitir conexões RDP pela porta 3389 com [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

```azurepowershell
$rule1 = New-AzNetworkSecurityRuleConfig `
  -Name 'myNetworkSecurityGroupRuleRDP' `
  -Description 'Allow RDP' `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 100 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 3389
```
#### <a name="create-a-network-security-group-rule-for-port-80"></a>Criar uma regra do grupo de segurança de rede para a porta 80

Crie uma regra de grupo de segurança de rede para permitir conexões de internet através da porta 80 com [new-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

```azurepowershell
$rule2 = New-AzNetworkSecurityRuleConfig `
  -Name 'myNetworkSecurityGroupRuleHTTP' `
  -Description 'Allow HTTP' `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 200 `
  -SourceAddressPrefix * `
  -SourcePortRange 80 `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80
```
#### <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

Crie um grupo de segurança de rede com [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup).

```azurepowershell
$nsg = New-AzNetworkSecurityGroup `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsNSG1"  `
  -SecurityRules $rule1,$rule2
```
### <a name="create-nics"></a>Criar NICs

Crie NICs virtuais com [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface). O exemplo a seguir cria dois NICs virtuais com configurações IPv4 e IPv6. (Uma NIC virtual para cada VM criada para seu aplicativo nas etapas a seguir).

```azurepowershell

# Create the IPv4 configuration for NIC 1
$Ip4Config=New-AzNetworkInterfaceIpConfig `
  -Name dsIp4Config `
  -Subnet $vnet.subnets[0] `
  -PrivateIpAddressVersion IPv4 `
  -LoadBalancerBackendAddressPool $backendPoolv4 `
  -PublicIpAddress  $RdpPublicIP_1

# Create the IPv6 configuration
$Ip6Config=New-AzNetworkInterfaceIpConfig `
  -Name dsIp6Config `
  -Subnet $vnet.subnets[0] `
  -PrivateIpAddressVersion IPv6 `
  -LoadBalancerBackendAddressPool $backendPoolv6

# Create NIC 1
$NIC_1 = New-AzNetworkInterface `
  -Name "dsNIC1" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -NetworkSecurityGroupId $nsg.Id `
  -IpConfiguration $Ip4Config,$Ip6Config

# Create the IPv4 configuration for NIC 2
$Ip4Config=New-AzNetworkInterfaceIpConfig `
  -Name dsIp4Config `
  -Subnet $vnet.subnets[0] `
  -PrivateIpAddressVersion IPv4 `
  -LoadBalancerBackendAddressPool $backendPoolv4 `
  -PublicIpAddress  $RdpPublicIP_2

# Create NIC 2 reusing the IPv6 configuration from NIC 1
$NIC_2 = New-AzNetworkInterface `
  -Name "dsNIC2" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -NetworkSecurityGroupId $nsg.Id `
  -IpConfiguration $Ip4Config,$Ip6Config

```

### <a name="create-virtual-machines"></a>Criar máquinas virtuais

Defina um nome de usuário e senha do administrador para as VMs com [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell
$cred = get-credential -Message "DUAL STACK VNET SAMPLE:  Please enter the Administrator credential to log into the VM's"
```

Agora, é possível criar as VMs com [New-AzVM](/powershell/module/az.compute/new-azvm). O exemplo a seguir cria duas VMs e os componentes de rede virtual necessários, caso ainda não existam.

```azurepowershell
$vmsize = "Standard_A2"
$ImagePublisher = "MicrosoftWindowsServer"
$imageOffer = "WindowsServer"
$imageSKU = "2019-Datacenter"

$vmName= "dsVM1"
$VMconfig1 = New-AzVMConfig -VMName $vmName -VMSize $vmsize -AvailabilitySetId $avset.Id 3> $null | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent 3> $null | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" 3> $null | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption fromImage  3> $null | Add-AzVMNetworkInterface -Id $NIC_1.Id  3> $null
$VM1 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $VMconfig1


$vmName= "dsVM2"
$VMconfig2 = New-AzVMConfig -VMName $vmName -VMSize $vmsize -AvailabilitySetId $avset.Id 3> $null | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent 3> $null | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" 3> $null | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption fromImage  3> $null | Add-AzVMNetworkInterface -Id $NIC_2.Id  3> $null
$VM2 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $VMconfig2
```
## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Exibir rede virtual de pilha dupla IPv6 no portal Azure
Você pode visualizar a rede virtual iPv6 dual stack no portal Azure da seguinte forma:
1. Na barra de pesquisa do portal, digite *dsVnet*.
2. Quando **o dsVnet** aparecer nos resultados da pesquisa, selecione-o. Isso lança a página **Visão Geral** da rede virtual dual stack chamada *dsVnet*. A rede virtual dual stack mostra os dois NICs com as configurações IPv4 e IPv6 localizadas na sub-rede de pilha dupla chamada *dsSubnet*.

![Rede virtual Dual Stack IPv6 com balanceador de carga interna padrão](./media/ipv6-dual-stack-standard-internal-load-balancer-powershell/ipv6-dual-stack-virtual-network.png)


> [!NOTE]
> A rede virtual IPv6 for Azure está disponível no portal Azure apenas para esta versão de pré-visualização.

## <a name="clean-up-resources"></a>Limpar recursos

Quando não forem mais necessários, você poderá usar o comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos, a VM e todos os recursos relacionados.

```azurepowershell
Remove-AzResourceGroup -Name dsStd_ILB_RG
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você criou um Balancer de Carga Padrão com uma configuração IP frontend dupla (IPv4 e IPv6). Você também criou duas máquinas virtuais que incluíam NICs com configurações ip duplas (IPV4 + IPv6) que foram adicionadas ao pool de back-end do balanceador de carga. Para saber mais sobre o suporte ao IPv6 nas redes virtuais do Azure, veja [o que é IPv6 para Azure Virtual Network?](ipv6-overview.md)