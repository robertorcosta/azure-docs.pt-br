---
title: Sobre gateways de rede virtual do ExpressRoute – Azure | Microsoft Docs
description: Saiba mais sobre os gateways de rede virtual para ExpressRoute. Este artigo inclui informações sobre tipos e SKUs de gateway.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: mialdrid
ms.openlocfilehash: 58e75e4efecf390c4c1449b7ec59684554fa7516
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281412"
---
# <a name="about-expressroute-virtual-network-gateways"></a>Sobre os gateways de rede virtual expressRoute

Para conectar sua rede virtual Azure e sua rede local via ExpressRoute, você deve criar um gateway de rede virtual primeiro. Um gateway de rede virtual serve a dois propósitos: trocar rotas IP entre as redes e direcionar o tráfego da rede de rotas. Este artigo explica os tipos de gateway, as SKUs de gateway e o desempenho estimado pelo SKU. Este artigo também explica o ExpressRoute [FastPath](#fastpath), um recurso que permite que o tráfego de rede de sua rede local contorne o gateway de rede virtual para melhorar o desempenho.

## <a name="gateway-types"></a>Tipos de gateway

Quando você cria um gateway de rede virtual, precisa especificar várias configurações. Uma das configurações necessárias, '-GatewayType', especifica se o gateway é usado para tráfego de VPN ou ExpressRoute. Os dois tipos de gateway são:

* **Vpn** - Para enviar tráfego criptografado pela Internet pública, use o tipo de gateway 'Vpn'. Isso também é chamado de gateway de VPN. As conexões Site a Site, Ponto a Site e VNet a VNet usam um gateway VPN.

* **ExpressRoute** - Para enviar tráfego em uma conexão privada, use o tipo de gateway 'ExpressRoute'. Isso também é chamado de gateway ExpressRoute e é o tipo de gateway usado na configuração do ExpressRoute.

Cada rede virtual pode ter apenas um gateway de rede virtual por tipo de gateway. Por exemplo, você pode ter um gateway de rede virtual que usa - GatewayType Vpn, e outro que usa -GatewayType ExpressRoute.

## <a name="gateway-skus"></a><a name="gwsku"></a>SKUs do Gateway
[!INCLUDE [expressroute-gwsku-include](../../includes/expressroute-gwsku-include.md)]

Se você quiser atualizar seu gateway para um SKU de gateway mais poderoso, na maioria dos casos você pode usar o cmdlet PowerShell 'Resize-AzVirtualNetworkGateway'. Isso funcionará em atualizações para as SKUs Standard e HighPerformance. No entanto, para atualizar para a SKU UltraPerformance, você precisará recriar o gateway. Recriar um gateway incorre em tempo de inatividade.

### <a name="estimated-performances-by-gateway-sku"></a><a name="aggthroughput"></a>Desempenhos estimados por SKU de gateway
A tabela a seguir mostra os tipos de gateway e os desempenhos estimados. Esta tabela aplica-se a ambos os modelos de implantação do Gerenciador de Recursos e clássico.

[!INCLUDE [expressroute-table-aggthroughput](../../includes/expressroute-table-aggtput-include.md)]

> [!IMPORTANT]
> O desempenho do aplicativo depende de vários fatores, como a latência de ponta a ponta e o número de fluxos de tráfego abertos pelo aplicativo. Os números na tabela representam o limite superior que o aplicativo, teoricamente, pode atingir em um ambiente ideal.
>
>

## <a name="gateway-subnet"></a><a name="gwsub"></a>Sub-rede gateway

Antes de criar um gateway ExpressRoute, você deve criar uma sub-rede de gateway. A sub-rede de gateway contém os endereços IP que as VMs do gateway de rede virtual e os serviços usam. Quando você cria o gateway de rede virtual, as VMs do gateway são implantadas na sub-rede do gateway e configuradas com as configurações de gateway ExpressaRoute necessárias. Nunca implante mais nada (por exemplo, VMs adicionais) na sub-rede do gateway. A sub-rede do gateway deve ser nomeada como GatewaySubnet para funcionar corretamente. Chamar a sub-rede de gateway de 'GatewaySubnet' permite que o Azure saiba que essa é a sub-rede para implantação nas VMs de gateway de rede virtual e nos serviços.

>[!NOTE]
>[!INCLUDE [vpn-gateway-gwudr-warning.md](../../includes/vpn-gateway-gwudr-warning.md)]
>

Quando você cria a sub-rede de gateway, pode especificar o número de endereços IP que contém a sub-rede. Os endereços IP na sub-rede do gateway são alocados para as VMs de gateway e para os serviços de gateway. Algumas configurações exigem mais endereços IP do que outras. 

Quando estiver planejando o tamanho da sub-rede do gateway, consulte a documentação da configuração que você está planejando criar. Por exemplo, a configuração coexista do ExpressRoute/VPN Gateway requer uma sub-rede de gateway maior do que a maioria das outras configurações. Além disso, convém certificar-se de que sua sub-rede de gateway contenha endereços IP suficientes para acomodar possíveis configurações adicionais futuras. Embora você possa criar uma sub-rede de gateway tão pequena quanto /29, recomendamos que você crie uma sub-rede de gateway de /27 ou maior (/27, /26 etc.) se você tiver o espaço de endereço disponível para fazê-lo. Isso acomodará a maioria das configurações.

O exemplo de PowerShell do Resource Manager a seguir mostra uma sub-rede de gateway chamada GatewaySubnet. Você pode ver que a notação CIDR especifica /27, que permite endereços IP suficientes para a maioria das configurações existentes no momento.

```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="zone-redundant-gateway-skus"></a><a name="zrgw"></a>SKUs de gateway redundantes de zona

Também é possível implantar gateways do ExpressRoute em Zonas de Disponibilidade do Azure. Isso separa fisicamente e logicamente em Zonas de disponibilidade diferentes, protegendo a conectividade de rede local com o Azure de falhas de nível de zona.

![Gateway do ExpressRoute com redundância de zona](./media/expressroute-about-virtual-network-gateways/zone-redundant.png)

Gateways com redundância de zona usam novas SKUs de gateways específicas para gateway do ExpressRoute.

* ErGw1AZ
* ErGw2AZ
* ErGw3AZ

As novas SKUs de gateway também dão suporte a outras opções de implantação para melhor atender às suas necessidades. Ao criar um gateway de rede virtual usando as novas SKUs de gateway, você também terá a opção de implantar o gateway em uma zona específica. Isso é referido como um gateway de VPN. Ao implantar um gateway em zona, todas as instâncias do gateway são implantadas na mesma zona de disponibilidade.

## <a name="fastpath"></a><a name="fastpath"></a>FastPath

O gateway de rede virtual ExpressRoute foi projetado para trocar rotas de rede e direcionar o tráfego da rede. O FastPath foi projetado para melhorar o desempenho do caminho de dados entre sua rede local e sua rede virtual. Quando ativado, o FastPath envia o tráfego de rede diretamente para máquinas virtuais na rede virtual, contornando o gateway.

Para obter mais informações sobre o FastPath, incluindo limitações e requisitos, consulte [Sobre o FastPath](about-fastpath.md).

## <a name="rest-apis-and-powershell-cmdlets"></a><a name="resources"></a>Cmdlets do PowerShell e APIs REST
Para obter recursos técnicos adicionais e requisitos de sintaxe específicos ao usar cmdlets do PowerShell e APIs REST para configurações do gateway de rede virtual, veja as páginas a seguir:

| **Clássico** | **Gerenciador de recursos** |
| --- | --- |
| [Powershell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/?view=azuresmps-4.0.0#azure) |[Powershell](https://docs.microsoft.com/powershell/module/az.network#networking) |
| [REST API](https://msdn.microsoft.com/library/jj154113.aspx) |[REST API](https://msdn.microsoft.com/library/mt163859.aspx) |

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre as configurações de conexão disponíveis, consulte [Visão geral do ExpressRoute](expressroute-introduction.md).

Para obter mais informações sobre a criação de gateways ExpressRoute, consulte [Criar um gateway de rede virtual para expressroute](expressroute-howto-add-gateway-resource-manager.md).

Para obter mais informações sobre a configuração de gateways redundantes de zona, consulte [Criar um gateway de rede virtual redundante de zona](../../articles/vpn-gateway/create-zone-redundant-vnet-gateway.md).

Para obter mais informações sobre o FastPath, consulte [Sobre o FastPath](about-fastpath.md).