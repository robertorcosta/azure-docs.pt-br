---
title: Criar e gerenciar o peering público do Azure ExpressRoute
description: Conheça e gerencie o peering público do Azure
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: cherylmc
ms.openlocfilehash: 8c1afac834fb9abb2cbf82f16f046a1624b251f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481126"
---
# <a name="create-and-manage-expressroute-public-peering"></a>Crie e gerencie o peering público expressroute

> [!div class="op_single_selector"]
> * [Artigo - Peering público](about-public-peering.md)
> * [Vídeo – Emparelhamento público](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Artigo - Peering da Microsoft](expressroute-circuit-peerings.md#microsoftpeering)
>

Este artigo ajuda você a criar e gerenciar a configuração de roteamento de peering público para um circuito ExpressRoute. Você também pode verificar o status, atualizar ou excluir e desprovisionar peerings. Este artigo se aplica aos circuitos do Gerenciador de Recursos que foram criados antes de o peering público ser preterido. Se você tiver um circuito anteriormente existente (criado antes de o peering público ser preterido), você pode gerenciar/configurar peering público usando [o Azure PowerShell,](#powershell) [a Cli zure](#cli)e o portal [Azure](#portal).

>[!NOTE]
>O olhar público é preterido. Você não pode criar peering público em novos circuitos ExpressRoute. Se você tiver um novo circuito ExpressRoute, em vez disso, use [o peering da Microsoft](expressroute-circuit-peerings.md#microsoftpeering) para seus serviços do Azure.
>

## <a name="connectivity"></a>Conectividade

A conectividade é sempre iniciada por meio de sua WAN para serviços do Microsoft Azure. Os serviços do Microsoft Azure não poderão iniciar conexões a sua rede por meio desse domínio de roteamento. Se o circuito ExpressRoute estiver habilitado para peering público do Azure, você poderá acessar as [faixas de IP públicas usadas no Azure](../virtual-network/virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) ao longo do circuito.

Uma vez que o peering público esteja ativado, você pode se conectar à maioria dos serviços do Azure. Não permitimos que você escolha seletivamente os serviços para os quais podemos anunciar rotas.

* Serviços como armazenamento azure, bancos de dados SQL e sites são oferecidos em endereços IP públicos.
* Através do domínio de roteamento de peering público, você pode se conectar privadamente a serviços hospedados em endereços IP públicos, incluindo VIPs de seus serviços em nuvem.
* É possível conectar o domínio de emparelhamento público à sua DMZ e a todos os serviços do Azure em seus endereços IP públicos de sua WAN sem precisar se conectar pela Internet.

## <a name="services"></a><a name="services"></a>Serviços

Esta seção mostra os serviços disponíveis em peering público. Como o peering público é preterido, não há nenhum plano para adicionar serviços novos ou adicionais ao peering público. Se você usar peering público e o serviço que deseja usar for suportado apenas por peering da Microsoft, você deve mudar para o peering da Microsoft. Consulte [a Microsoft procurando](expressroute-faqs.md#microsoft-peering) uma lista de serviços suportados.

**Suportado:**

* Power BI
* Há suporte para a maioria dos serviços do Azure. Verifique diretamente com o serviço que deseja usar para verificar o suporte.

**Não compatível:**
  * CDN
  * Porta da frente do Azure
  * Servidor de autenticação multifatorial (legado)
  * Gerenciador de Tráfego

Para validar a disponibilidade de um serviço específico, você pode verificar a documentação desse serviço para ver se há uma faixa reservada publicada para esse serviço. Em seguida, você pode procurar as faixas IP do serviço de destino e comparar com as faixas listadas nas faixas ip e tags de serviço do [Azure – arquivo XML da nuvem pública](https://www.microsoft.com/download/details.aspx?id=56519). Alternativamente, você pode abrir um bilhete de apoio para o serviço em questão para esclarecimentos.

## <a name="peering-comparison"></a><a name="compare"></a>Comparação de emparelhamento

[!INCLUDE [peering comparison](../../includes/expressroute-peering-comparison.md)]

> [!NOTE]
> O peering público do Azure tem 1 endereço IP NAT associado a cada sessão BGP. Para maiores que 2 endereços IP NAT, mude para peering da Microsoft. O peering da Microsoft permite configurar suas próprias alocações de NAT, bem como usar filtros de rota para anúncios de prefixo seletivo. Para obter mais informações, consulte [Mover para o peering da Microsoft](https://docs.microsoft.com/azure/expressroute/how-to-move-peering).
>

## <a name="custom-route-filters"></a>Filtros de rota personalizados

Você pode definir filtros de rota personalizados dentro da sua rede para consumir apenas as rotas que você precisa. Consulte a página [Roteamento](expressroute-routing.md) para obter informações detalhadas sobre a configuração de roteamento.

## <a name="azure-powershell-steps"></a><a name="powershell"></a>Etapas do Azure PowerShell


[!INCLUDE [CloudShell](../../includes/expressroute-cloudshell-powershell-about.md)]

Como o peering público é preterido, você não pode configurar peering público em um novo circuito ExpressRoute.

1. Verifique se você tem um circuito ExpressRoute que está provisionado e também ativado. Use o seguinte exemplo:

   ```azurepowershell-interactive
   Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```

   A resposta é semelhante ao seguinte exemplo:

   ```
   Name                             : ExpressRouteARMCircuit
   ResourceGroupName                : ExpressRouteResourceGroup
   Location                         : westus
   Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
   Etag                             : W/"################################"
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
                                       "ServiceProviderName": "Equinix",
                                       "PeeringLocation": "Silicon Valley",
                                       "BandwidthInMbps": 200
                                     }
   ServiceKey                       : **************************************
   Peerings                         : []
   ```
2. Configure o emparelhamento público do Azure para o circuito. Verifique se você tem as informações a seguir antes de prosseguir.

   * Uma sub-rede /30 para o link principal. Precisa ser um prefixo IPv4 público válido.
   * Uma sub-rede /30 para o link secundário. Precisa ser um prefixo IPv4 público válido.
   * Uma ID válida de VLAN para estabelecer esse emparelhamento. Verifique se nenhum outro emparelhamento no circuito usa a mesma ID de VLAN.
   * Número de AS para emparelhamento. Você pode usar um número de AS de 2 e de 4 bytes.
   * Opcional:
   * Um Hash MD5, se você optar por usar um.

   Executar o seguinte exemplo para configurar o emparelhamento público do Azure para seu circuito

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   Se você optar por usar um hash MD5, use o exemplo a seguir:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100  -SharedKey "A1B2C3D4"

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   > [!IMPORTANT]
   > Especifique o número de AS como um ASN de emparelhamento, não um ASN de cliente.
   > 
   >

### <a name="to-get-azure-public-peering-details"></a><a name="getpublic"></a>Para obter detalhes sobre o emparelhamento público do Azure

Você pode obter detalhes de configuração usando o seguinte cmdlet:

```azurepowershell-interactive
  $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

  Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt
  ```

### <a name="to-update-azure-public-peering-configuration"></a><a name="updatepublic"></a>Atualizar a configuração de emparelhamento público do Azure

Você pode atualizar qualquer parte da configuração usando o exemplo a seguir. Neste exemplo, a ID da VLAN do circuito está sendo atualizada de 200 para 600.

```azurepowershell-interactive
Set-AzExpressRouteCircuitPeeringConfig  -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 600

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-delete-azure-public-peering"></a><a name="deletepublic"></a>Excluir o emparelhamento público do Azure

Você pode remover a configuração de emparelhamento executando o seguinte exemplo:

```azurepowershell-interactive
Remove-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="azure-cli-steps"></a><a name="cli"></a>Passos de Cli do Azure


[!INCLUDE [CloudShell](../../includes/expressroute-cloudshell-powershell-about.md)]

1. Verifique se o circuito do ExpressRoute está provisionado e habilitado. Use o seguinte exemplo:

   ```azurecli-interactive
   az network express-route list
   ```

   A resposta é semelhante ao seguinte exemplo:

   ```output
   "allowClassicOperations": false,
   "authorizations": [],
   "circuitProvisioningState": "Enabled",
   "etag": "W/\"1262c492-ffef-4a63-95a8-a6002736b8c4\"",
   "gatewayManagerEtag": null,
   "id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit",
   "location": "westus",
   "name": "MyCircuit",
   "peerings": [],
   "provisioningState": "Succeeded",
   "resourceGroup": "ExpressRouteResourceGroup",
   "serviceKey": "1d05cf70-1db5-419f-ad86-1ca62c3c125b",
   "serviceProviderNotes": null,
   "serviceProviderProperties": {
    "bandwidthInMbps": 200,
    "peeringLocation": "Silicon Valley",
    "serviceProviderName": "Equinix"
   },
   "serviceProviderProvisioningState": "Provisioned",
   "sku": {
    "family": "UnlimitedData",
    "name": "Standard_MeteredData",
    "tier": "Standard"
   },
   "tags": null,
   "type": "Microsoft.Network/expressRouteCircuits]
   ```

2. Configure o emparelhamento público do Azure para o circuito. Verifique se você tem as informações a seguir antes de prosseguir.

   * Uma sub-rede /30 para o link principal. Precisa ser um prefixo IPv4 público válido.
   * Uma sub-rede /30 para o link secundário. Precisa ser um prefixo IPv4 público válido.
   * Uma ID válida de VLAN para estabelecer esse emparelhamento. Verifique se nenhum outro emparelhamento no circuito usa a mesma ID de VLAN.
   * Número de AS para emparelhamento. Você pode usar um número de AS de 2 e de 4 bytes.
   * **Opcional –** Um hash MD5 se você optar por usar um.

   Executar o seguinte exemplo para configurar o emparelhamento público do Azure para seu circuito:

   ```azurecli-interactive
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 12.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 12.0.0.4/30 --vlan-id 200 --peering-type AzurePublicPeering
   ```

   Se você optar por usar um hash MD5, use o exemplo a seguir:

   ```azurecli-interactive
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 12.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 12.0.0.4/30 --vlan-id 200 --peering-type AzurePublicPeering --SharedKey "A1B2C3D4"
   ```

   > [!IMPORTANT]
   > Especifique o número de AS como um ASN de emparelhamento, não um ASN de cliente.

### <a name="to-view-azure-public-peering-details"></a><a name="getpublic"></a>Para exibir detalhes sobre o emparelhamento público do Azure

Você pode obter detalhes de configuração usando o exemplo a seguir:

```azurecli
az network express-route peering show -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

A saída deverá ser semelhante ao seguinte exemplo:

```output
{
  "azureAsn": 12076,
  "etag": "W/\"2e97be83-a684-4f29-bf3c-96191e270666\"",
  "gatewayManagerEtag": "18",
  "id": "/subscriptions/9a0c2943-e0c2-4608-876c-e0ddffd1211b/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/peerings/AzurePublicPeering",
  "lastModifiedBy": "Customer",
  "microsoftPeeringConfig": null,
  "name": "AzurePublicPeering",
  "peerAsn": 7671,
  "peeringType": "AzurePublicPeering",
  "primaryAzurePort": "",
  "primaryPeerAddressPrefix": "",
  "provisioningState": "Succeeded",
  "resourceGroup": "ExpressRouteResourceGroup",
  "routeFilter": null,
  "secondaryAzurePort": "",
  "secondaryPeerAddressPrefix": "",
  "sharedKey": null,
  "state": "Enabled",
  "stats": null,
  "vlanId": 100
}
```

### <a name="to-update-azure-public-peering-configuration"></a><a name="updatepublic"></a>Atualizar a configuração de emparelhamento público do Azure

Você pode atualizar qualquer parte da configuração usando o exemplo a seguir. Neste exemplo, a ID da VLAN do circuito está sendo atualizada de 200 para 600.

```azurecli-interactive
az network express-route peering update --vlan-id 600 -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

### <a name="to-delete-azure-public-peering"></a><a name="deletepublic"></a>Excluir o emparelhamento público do Azure

Você pode remover a configuração de emparelhamento executando o seguinte exemplo:

```azurecli-interactive
az network express-route peering delete -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

## <a name="azure-portal-steps"></a><a name="portal"></a>etapas do portal do Azure

Para configurar peering, use as etapas PowerShell ou CLI contidas neste artigo. Para gerenciar um peering, você pode usar as seções abaixo. Para referência, essas etapas são semelhantes ao gerenciamento de um [peering microsoft no portal](expressroute-howto-routing-portal-resource-manager.md#msft).

### <a name="to-view-azure-public-peering-details"></a><a name="get"></a>Para exibir detalhes sobre o emparelhamento público do Azure

Veja as propriedades do peering público do Azure selecionando o peering no portal.

### <a name="to-update-azure-public-peering-configuration"></a><a name="update"></a>Atualizar a configuração de emparelhamento público do Azure

Selecione a linha para peering e, em seguida, modifique as propriedades de peering.

### <a name="to-delete-azure-public-peering"></a><a name="delete"></a>Excluir o emparelhamento público do Azure

Remova a configuração de peering selecionando o ícone de exclusão.

## <a name="next-steps"></a>Próximas etapas

Próximo passo, [Vincule uma rede virtual a um circuito ExpressRoute](expressroute-howto-linkvnet-arm.md).

* Para saber mais sobre fluxos de trabalho do ExpressRoute, confira [Fluxos de trabalho do ExpressRoute](expressroute-workflows.md).
* Para obter mais informações sobre o emparelhamento de circuito, veja [Circuitos e domínios de roteamento do ExpressRoute](expressroute-circuit-peerings.md).
* Para saber mais sobre redes virtuais, confira [Visão geral da rede virtual](../virtual-network/virtual-networks-overview.md).