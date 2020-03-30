---
title: 'Azure ExpressRoute: Reset circuit peering'
description: Como desabilitar e habilitar emparelhamentos de circuitos do ExpressRoute.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: conceptual
ms.date: 01/13/2018
ms.author: charwen
ms.openlocfilehash: 9f32eb439872de9e4687d046745c03bafd86b2fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75941748"
---
# <a name="reset-expressroute-circuit-peerings"></a>Redefinir emparelhamentos de circuitos do ExpressRoute

Este artigo descreve como desabilitar e habilitar peerings de um circuito da Rota Expressa usando o PowerShell. Quando você desativa um emparelhamento, a sessão do BGP na conexão principal e na conexão secundária do circuito da ExpressRoute será encerrada. Você perderá conectividade por meio desse peering para a Microsoft. Quando você ativa um emparelhamento, a sessão do BGP na conexão principal e na conexão secundária do circuito da ExpressRoute será ativada. Você vai recuperar a conectividade através deste peering para a Microsoft. Você pode habilitar e desabilitar o Microsoft Peering e o Azure Private Peering em um circuito do ExpressRoute de forma independente. Quando você configura primeiro os peerings em seu circuito da Rota Expressa, os peerings são ativados por padrão.

Há alguns cenários em que você pode achar útil redefinir seus peerings da ExpressRoute.
* Teste seu design de recuperação de desastres e implementação. Por exemplo, você tem dois circuitos ExpressRoute. Você pode desabilitar os peerings de um circuito e forçar o tráfego de sua rede a falhar no outro circuito.
* Habilite a Detecção de Encaminhamento Bidirecional (BFD) no Azure Private Peering ou no Microsoft Peering do seu circuito ExpressRoute. O BFD é habilitado por padrão no Azure Private Peering se o seu circuito ExpressRoute for criado após 1 de agosto de 2018 e no Microsoft Peering se o seu circuito ExpressRoute for criado após 10 de janeiro de 2020. Se o circuito foi criado antes dessa, BFD não foi habilitado. Você pode habilitar BFD desabilitando o emparelhamento e reabilitando a ele. 

### <a name="working-with-azure-powershell"></a>Trabalhando com o Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="reset-a-peering"></a>Redefinir um emparelhamento

1. Se você estiver executando o PowerShell localmente, abra o console do PowerShell com privilégios elevados e conecte-se à sua conta. Use o exemplo a seguir para ajudar a se conectar:

   ```azurepowershell
   Connect-AzAccount
   ```
2. Se você tiver várias assinaturas do Azure, verifique as assinaturas para a conta.

   ```azurepowershell-interactive
   Get-AzSubscription
   ```
3. Especifique a assinatura que você deseja usar.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
   ```
4. Execute os seguintes comandos para recuperar seu circuito da Rota Expressa.

   ```azurepowershell-interactive
   $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```
5. Identifique o emparelhamento que você deseja desativar ou ativar. *Emparelhamentos* é uma matriz. No exemplo a seguir, Peerings [0] é Peering e Peerings privados do Azure [1] Microsoft Peering.

   ```azurepowershell-interactive
   Name                             : ExpressRouteARMCircuit
   ResourceGroupName                : ExpressRouteResourceGroup
   Location                         : westus
   Id                               : /subscriptions/########-####-####-####-############/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
   Etag                             : W/"cd011bef-dc79-49eb-b4c6-81fb6ea5d178"
   ProvisioningState                : Succeeded
   Sku                              : {
                                     "Name": "Standard_MeteredData",
                                     "Tier": "Standard",
                                     "Family": "MeteredData"
                                   }
   CircuitProvisioningState         : Enabled
   ServiceProviderProvisioningState : Provisioned
   ServiceProviderNotes             :
   ServiceProviderProperties        : {
                                     "ServiceProviderName": "Coresite",
                                     "PeeringLocation": "Los Angeles",
                                     "BandwidthInMbps": 50
                                   }
   ServiceKey                       : ########-####-####-####-############
   Peerings                         : [
                                     {
                                       "Name": "AzurePrivatePeering",
                                       "Etag": "W/\"cd011bef-dc79-49eb-b4c6-81fb6ea5d178\"",
                                       "Id": "/subscriptions/########-####-####-####-############/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit/peerings/AzurePrivatePeering",
                                       "PeeringType": "AzurePrivatePeering",
                                       "State": "Enabled",
                                       "AzureASN": 12076,
                                       "PeerASN": 123,
                                       "PrimaryPeerAddressPrefix": "10.0.0.0/30",
                                       "SecondaryPeerAddressPrefix": "10.0.0.4/30",
                                       "PrimaryAzurePort": "",
                                       "SecondaryAzurePort": "",
                                       "VlanId": 789,
                                       "MicrosoftPeeringConfig": {
                                         "AdvertisedPublicPrefixes": [],
                                         "AdvertisedCommunities": [],
                                         "AdvertisedPublicPrefixesState": "NotConfigured",
                                         "CustomerASN": 0,
                                         "LegacyMode": 0,
                                         "RoutingRegistryName": "NONE"
                                       },
                                       "ProvisioningState": "Succeeded",
                                       "GatewayManagerEtag": "",
                                       "LastModifiedBy": "Customer",
                                       "Connections": []
                                     },
                                     {
                                       "Name": "MicrosoftPeering",
                                       "Etag": "W/\"cd011bef-dc79-49eb-b4c6-81fb6ea5d178\"",
                                       "Id": "/subscriptions/########-####-####-####-############/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit/peerings/MicrosoftPeering",
                                       "PeeringType": "MicrosoftPeering",
                                       "State": "Enabled",
                                       "AzureASN": 12076,
                                       "PeerASN": 123,
                                       "PrimaryPeerAddressPrefix": "3.0.0.0/30",
                                       "SecondaryPeerAddressPrefix": "3.0.0.4/30",
                                       "PrimaryAzurePort": "",
                                       "SecondaryAzurePort": "",
                                       "VlanId": 345,
                                       "MicrosoftPeeringConfig": {
                                         "AdvertisedPublicPrefixes": [
                                           "3.0.0.3/32"
                                         ],
                                         "AdvertisedCommunities": [],
                                         "AdvertisedPublicPrefixesState": "ValidationNeeded",
                                         "CustomerASN": 0,
                                         "LegacyMode": 0,
                                         "RoutingRegistryName": "NONE"
                                       },
                                       "ProvisioningState": "Succeeded",
                                       "GatewayManagerEtag": "",
                                       "LastModifiedBy": "Customer",
                                       "Connections": []
                                     }
                                   ]
   Authorizations                   : []
   AllowClassicOperations           : False
   GatewayManagerEtag               :
   ```
6. Execute os seguintes comandos para alterar o estado do peering.

   ```azurepowershell-interactive
   $ckt.Peerings[0].State = "Disabled"
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```
   O emparelhamento deve estar em um estado que você definir. 

## <a name="next-steps"></a>Próximas etapas
Se você precisar de ajuda para solucionar um problema do ExpressRoute, confira os seguintes artigos:
* [Verificando a conectividade do ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
* [Solucionando problemas de desempenho de rede](expressroute-troubleshooting-network-performance.md)
