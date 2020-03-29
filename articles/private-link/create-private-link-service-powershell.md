---
title: Crie um serviço de link privado do Azure usando o Azure PowerShell| Microsoft Docs
description: Saiba como criar um serviço de link privado do Azure usando o Azure PowerShell
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 225ae9d07cc6df2fa809e250083ee6007ab2f945
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76932076"
---
# <a name="create-a-private-link-service-using-azure-powershell"></a>Crie um serviço de link privado usando o Azure PowerShell
Este artigo mostra como criar um serviço de link privado no Azure usando o Azure PowerShell.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar o PowerShell localmente, este artigo requer a versão mais recente do módulo Azure PowerShell. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-Az-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Connect-AzAccount` para criar uma conexão com o Azure.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Antes de criar seu link privado, você deve criar um grupo de recursos com [o New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *westcentralUS:*

```azurepowershell
$location = "westcentralus"
$rgName = "myResourceGroup"
New-AzResourceGroup `
  -ResourceGroupName $rgName `
  -Location $location
```
## <a name="create-a-virtual-network"></a>Criar uma rede virtual
Crie uma rede virtual para o seu link privado com [o New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). O exemplo a seguir cria uma rede virtual chamada *myvnet* com sub-rede para*frontend (frontendSubnet),* backend *(backendSubnet),* link privado *(outraSubnet*):

```azurepowershell
$virtualNetworkName = "myvnet"


# Create subnet config

$frontendSubnet = New-AzVirtualNetworkSubnetConfig `
-Name frontendSubnet `
-AddressPrefix "10.0.1.0/24"  

$backendSubnet = New-AzVirtualNetworkSubnetConfig `
-Name backendSubnet `
-AddressPrefix "10.0.2.0/24"  

$otherSubnet = New-AzVirtualNetworkSubnetConfig `
-Name otherSubnet `
-AddressPrefix "10.0.3.0/24" `
-PrivateLinkServiceNetworkPolicies "Disabled" 

# Create the virtual network
$vnet = New-AzVirtualNetwork `
-Name $virtualNetworkName `
-ResourceGroupName $rgName `
-Location $location `
-AddressPrefix "10.0.0.0/16" `
-Subnet $frontendSubnet,$backendSubnet,$otherSubnet 
```
## <a name="create-internal-load-balancer"></a>Criar balanceador de carga interna
Crie um balanceador de carga padrão interno com [o New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer). O exemplo a seguir cria um Balancer de carga padrão interno usando a configuração de IP frontend, teste, regra e pool de back-end que você criou nas etapas anteriores:

```azurepowershell

$lbBackendName = "LB-backend" 
$lbFrontName = "LB-frontend" 
$lbName = "lb"
 
#Create Internal Load Balancer
$frontendIP = New-AzLoadBalancerFrontendIpConfig -Name $lbFrontName -PrivateIpAddress 10.0.1.5 -SubnetId $vnet.subnets[0].Id 
$beaddresspool= New-AzLoadBalancerBackendAddressPoolConfig -Name $lbBackendName 
$probe = New-AzLoadBalancerProbeConfig -Name 'myHealthProbe' -Protocol Http -Port 80 `
  -RequestPath / -IntervalInSeconds 360 -ProbeCount 5
$rule = New-AzLoadBalancerRuleConfig -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool  $beaddresspool -Probe $probe -Protocol Tcp -FrontendPort 80 -BackendPort 80
$NRPLB = New-AzLoadBalancer -ResourceGroupName $rgName -Name $lbName -Location $location -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $probe -LoadBalancingRule $rule -Sku Standard 
```
## <a name="create-a-private-link-service"></a>Criar um serviço de Link Privado
Crie um serviço de link privado com [o New-AzPrivateLinkService](/powershell/module/az.network/new-azloadbalancer).  Este exemplo cria um serviço de link privado chamado *myPLS* usando o Standard Load Balancer no grupo de recursos chamado *myResourceGroup*. 
```azurepowershell

$plsIpConfigName = "PLS-ipconfig" 
$plsName = "pls"
$peName = "pe" 
  
$IPConfig = New-AzPrivateLinkServiceIpConfig `
-Name $plsIpConfigName `
-Subnet $vnet.subnets[2] `
-PrivateIpAddress 10.0.3.5 

$fe = Get-AzLoadBalancer -Name $lbName | Get-AzLoadBalancerFrontendIpConfig 

$privateLinkService = New-AzPrivateLinkService `
-ServiceName $plsName `
-ResourceGroupName $rgName `
-Location $location `
-LoadBalancerFrontendIpConfiguration $frontendIP `
-IpConfiguration $IPConfig 
```

### <a name="get-private-link-service"></a>Obtenha serviço de link privado
Obtenha detalhes sobre seu serviço de link privado com [o Get-AzPrivateLinkService](/powershell/module/az.network/get-azprivatelinkservice) da seguinte forma:

```azurepowershell
$pls = Get-AzPrivateLinkService -Name $plsName -ResourceGroupName $rgName 
```

Nesta fase, seu Serviço de Link Privado é criado com sucesso e está pronto para receber o tráfego. Observe que o exemplo acima é apenas para demonstrar a criação do Serviço de Link Privado usando o PowerShell.  Não configuramos os pools de backend do balanceador de carga ou qualquer aplicativo nos pools de backend para ouvir o tráfego. Se você quiser ver fluxos de tráfego de ponta a ponta, é fortemente aconselhado a configurar seu aplicativo atrás do balanceador de carga padrão. 

Em seguida, demonstraremos como mapear este serviço para um ponto final privado em diferentes VNet usando PowerShell. Novamente, o exemplo se limita a criar o Private Endpoint e conectar-se ao Serviço de Link Privado criado acima. Você pode criar Máquinas Virtuais na Rede Virtual para enviar/receber tráfego para o ponto final privado para construir seu cenário. 

## <a name="create-a-private-endpoint"></a>Criar um ponto de extremidade privado
### <a name="create-a-virtual-network"></a>Criar uma rede virtual
Crie uma rede virtual para o seu ponto final privado com [o New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Este exemplo cria uma rede virtual chamada *vnetPE* no grupo de recursos chamado *myResourceGroup*:
 
```azurepowershell
$virtualNetworkNamePE = "vnetPE"
 
# Create VNet for private endpoint
$peSubnet = New-AzVirtualNetworkSubnetConfig `
-Name peSubnet `
-AddressPrefix "11.0.1.0/24" `
-PrivateEndpointNetworkPolicies "Disabled" 

$vnetPE = New-AzVirtualNetwork `
-Name $virtualNetworkNamePE `
-ResourceGroupName $rgName `
-Location $location `
-AddressPrefix "11.0.0.0/16" `
-Subnet $peSubnet 
```

### <a name="create-a-private-endpoint"></a>Criar um ponto de extremidade privado
Crie um ponto final privado para consumir o serviço de link privado criado acima em sua rede virtual:
 
```azurepowershell
 
$plsConnection= New-AzPrivateLinkServiceConnection `
-Name plsConnection `
-PrivateLinkServiceId  $privateLinkService.Id  

$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $rgName -Name $peName -Location $location -Subnet $vnetPE.subnets[0] -PrivateLinkServiceConnection $plsConnection -ByManualRequest 
```
 
### <a name="get-private-endpoint"></a>Obter ponto final privado
Obtenha o endereço IP do `Get-AzPrivateEndpoint` ponto final privado com o seguinte:

```azurepowershell
# Get Private Endpoint and its IP Address 
$pe =  Get-AzPrivateEndpoint `
-Name $peName `
-ResourceGroupName $rgName  `
-ExpandResource networkinterfaces

$pe.NetworkInterfaces[0].IpConfigurations[0].PrivateIpAddress 

```

### <a name="approve-the-private-endpoint-connection"></a>Aprove a conexão de ponto final privado
Aprove a conexão de ponto final privado ao serviço de link privado com 'Approve-AzPrivateEndpointConnection'.

```azurepowershell   

$pls = Get-AzPrivateLinkService `
-Name $plsName `
-ResourceGroupName $rgName 

Approve-AzPrivateEndpointConnection -ResourceId $pls.PrivateEndpointConnections[0].Id -Description "Approved" 

``` 

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre [o link privado do Azure](private-link-overview.md)
 
