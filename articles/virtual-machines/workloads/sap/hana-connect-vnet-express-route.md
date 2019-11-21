---
title: Connectivity set up from virtual network to SAP HANA on Azure (large instances) | Microsoft Docs
description: Connectivity set up from virtual network to use SAP HANA on Azure (large instances).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5fbcc6ace4923d8aa2d938cd9fffe7a16c4fc1ff
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74206739"
---
# <a name="connect-a-virtual-network-to-hana-large-instances"></a>Conecte uma rede virtual a instâncias grandes do HANA

Depois de criar uma rede virtual do Azure, você pode conectar essa rede ao SAP HANA em instâncias grandes do Azure. Crie um gateway do Azure ExpressRoute na rede virtual. This gateway enables you to link the virtual network to the ExpressRoute circuit that connects to the customer tenant on the HANA Large Instance stamp.

> [!NOTE] 
> Essa etapa pode levar até 30 minutos para ser concluída. O novo gateway é criado na assinatura do Azure designada e, em seguida, conectado à rede virtual do Azure especificada.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

Se um gateway já existir, verifique se é um gateway do ExpressRoute ou não. If it is not an ExpressRoute gateway, delete the gateway, and re-create it as an ExpressRoute gateway. Se um gateway da Rota Expressa já estiver estabelecido, consulte a seção a seguir deste artigo, "Vincular redes virtuais". 

- Use o [Portal do Azure](https://portal.azure.com/) ou o PowerShell para criar um gateway de VPN do ExpressRoute conectado à sua rede virtual.
  - Se você usar o portal do Azure, adicione um novo **Gateway de Rede Virtual** e, em seguida, selecione **ExpressRoute** como o tipo de gateway.
  - Se você usar o PowerShell, primeiro Baixe e use a versão mais recente [SDK do Azure PowerShell](https://azure.microsoft.com/downloads/). 
 
Os comandos a seguir criam um gateway de ExpressRoute. The texts preceded by a _$_ are user-defined variables that should be updated with your specific information.

```powershell
# These Values should already exist, update to match your environment
$myAzureRegion = "eastus"
$myGroupName = "SAP-East-Coast"
$myVNetName = "VNet01"

# These values are used to create the gateway, update for how you wish the GW components to be named
$myGWName = "VNet01GW"
$myGWConfig = "VNet01GWConfig"
$myGWPIPName = "VNet01GWPIP"
$myGWSku = "HighPerformance" # Supported values for HANA large instances are: HighPerformance or UltraPerformance

# These Commands create the Public IP and ExpressRoute Gateway
$vnet = Get-AzVirtualNetwork -Name $myVNetName -ResourceGroupName $myGroupName
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
New-AzPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName `
-Location $myAzureRegion -AllocationMethod Dynamic
$gwpip = Get-AzPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName
$gwipconfig = New-AzVirtualNetworkGatewayIpConfig -Name $myGWConfig -SubnetId $subnet.Id `
-PublicIpAddressId $gwpip.Id

New-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName -Location $myAzureRegion `
-IpConfigurations $gwipconfig -GatewayType ExpressRoute `
-GatewaySku $myGWSku -VpnType PolicyBased -EnableBgp $true
```

Neste exemplo, a SKU de gateway HighPerformance foi usada. Suas opções são HighPerformance ou UltraPerformance como as únicas SKUs de gateway suportadas para o SAP HANA no Azure (grandes instâncias).

> [!IMPORTANT]
> Para instâncias grandes do HANA do SKU de classe Tipo II, você deve usar o SKU do Gateway UltraPerformance.

## <a name="link-virtual-networks"></a>Vincular redes virtuais

A rede virtual do Azure agora tem um gateway da Rota Expressa. Use the authorization information provided by Microsoft to connect the ExpressRoute gateway to the SAP HANA Large Instances ExpressRoute circuit. Você pode se conectar usando o portal do Azure ou o PowerShell. The PowerShell instructions are as follows. 

Run the following commands for each ExpressRoute gateway by using a different AuthGUID for each connection. As duas primeiras entradas mostradas no script a seguir são provenientes das informações fornecidas pela Microsoft. Além disso, o AuthGUID é específico para cada rede virtual e seu gateway. If you want to add another Azure virtual network, you need to get another AuthID for your ExpressRoute circuit that connects HANA large instances into Azure from Microsoft. 

```powershell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID -ExpressRouteGatewayBypass
```

> [!NOTE]
> The last parameter in the command New-AzVirtualNetworkGatewayConnection, **ExpressRouteGatewayBypass** is a new parameter that enables ExpressRoute Fast Path. A functionality that reduces network latency between your HANA Large Instance units and Azure VMs. The functionality got added in May 2019. For more details, check the article [SAP HANA (Large Instances) network architecture](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture). Make sure that you are running the latest version of PowerShell cmdlets before running the commands.

Para conectar o gateway a mais de um circuito do ExpressRoute associado à sua assinatura, talvez seja necessário executar essa etapa mais de uma vez. Por exemplo, você provavelmente conectará o mesmo gateway de rede virtual ao circuito da Rota Expressa que conecta a rede virtual à sua rede local.

## <a name="applying-expressroute-fast-path-to-existing-hana-large-instance-expressroute-circuits"></a>Applying ExpressRoute Fast Path to existing HANA Large Instance ExpressRoute circuits
The documentation so far explained how to connect a new ExpressRoute circuit that got created with a HANA Large Instance deployment to an Azure ExpressRoute gateway of one of your Azure virtual networks. But many customers already have their ExpressRoute circuits setup already and have their virtual networks connected to HANA Large Instances already. As the new ExpressRoute Fast Path is reducing network latency, it is recommended that you apply the change to use this functionality. The commands to connect a new ExpreesRoute circuit and to change an existing ExpressRoute Circuit are the same. As a result you need to run this sequence of PowerShell commands to change an existing circuit to use 

```powershell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID -ExpressRouteGatewayBypass
```

It is important that you add the last parameter as displayed above to enable the ExpressRoute Fast Path functionality


## <a name="expressroute-global-reach"></a>Alcance Global do ExpressRoute
As you want to enable Global Reach for one or both of the two scenarios:

 - HANA System Replication without any additional proxies or firewalls
 - Copying backups between HANA Large Instance units in two different regions to perform system copies or system refreshes

you need consider that:

- You need to provide an address space range of a /29 address space. That address range may not overlap with any of the other address space ranges that you used so far connecting HANA Large Instances to Azure and may not overlap with any of your IP address ranges you used somewhere else in Azure or on-premises.
- There is a limitation on the ASNs (Autonomous System Number) that can be used to advertise your on-premises routes to HANA Large Instances. Your on-premises must not advertise any routes with private ASNs in the range of 65000 – 65020 or 65515. 
- For the scenario of connecting on-premises direct access to HANA Large instances, you need to calculate a fee for the circuit that connects you to Azure. For prices, check the prices for [Global Reach Add-On](https://azure.microsoft.com/pricing/details/expressroute/).

To get one or both of the scenarios applied to your deployment, open a support message with Azure as described in [Open a support request for HANA large Instances](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#open-a-support-request-for-hana-large-instances)

Data that is needed and keywords that you need to use for Microsoft to be able to route and execute on your request, looks like:

- Service: SAP HANA Large Instance
- Problem type: Configuration and Setup
- Problem subtype: My problem is not listed above
- Subject 'Modify my Network - add Global Reach'
- Details: 'Add Global Reach to HANA Large Instance to HANA Large Instance tenant or 'Add Global Reach to on-premises to HANA Large Instance tenant.
- Additional details for the HANA Large Instance to HANA Large Instance tenant case: You need to define the **two Azure regions** where the two tenants to connect are located **AND** you need to submit the **/29 IP address range**
- Additional details for the on-premises to HANA Large Instance tenant case: You need to define the **Azure Region** where the HANA Large Instance tenant is deployed you want to connect to directly. Additionally you need to provide the **Auth GUID** and **Circuit Peer ID** that you received when you established your ExpressRoute circuit between on-premises and Azure. Additionally, you need to name your **ASN**. The last deliverable is a **/29 IP address range** for ExpressRoute Global Reach.

> [!NOTE]
> If you want to have both cases handled, you need to supply two different /29 IP address ranges that do not overlap with any other IP address range used so far. 




## <a name="next-steps"></a>Próximos passos

- [Requisitos de rede adicionais para HLI](hana-additional-network-requirements.md)
