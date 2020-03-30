---
title: 'Veja rotas efetivas de um hub virtual: Azure Virtual WAN | Microsoft Docs'
description: Vie rotas eficazes para um hub virtual no Azure Virtual WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: cherylmc
ms.openlocfilehash: 1173da81736661048d1e4e12d9919bc2aadf73ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73515844"
---
# <a name="view-effective-routes-of-a-virtual-hub"></a>Veja rotas eficazes de um hub virtual

Você pode ver todas as rotas do seu hub WAN virtual no portal Azure. Para visualizar as rotas, navegue até o hub virtual e selecione **Routing -> Exibir Rotas Eficazes**.

## <a name="understanding-routes"></a><a name="understand"></a>Entendendo rotas

O exemplo a seguir pode ajudá-lo a entender melhor como o roteamento DE WAN virtual é exibido.

Neste exemplo, temos uma WAN virtual com três hubs. O primeiro hub está na região leste dos EUA, o segundo hub está na região da Europa Ocidental, e o terceiro hub está na região oeste dos EUA. Em uma WAN virtual, todos os hubs estão interconectados. Neste exemplo, assumiremos que os hubs dos EUA e da Europa Ocidental têm conexões de filiais locais (raios) e redes virtuais Azure (raios).

Um azure VNet falou (10.4.0.0/16) com um Aparelho Virtual de Rede (10.4.0.6) é ainda mais olhado para um VNet (10.5.0.0/16). Consulte [informações adicionais](#abouthubroute) mais tarde neste artigo para obter mais informações sobre a tabela de rotas do hub.

Neste exemplo, também assumimos que o Ramo 1 da Europa Ocidental está conectado ao hub dos EUA Oriental, bem como ao hub da Europa Ocidental. Um circuito ExpressRoute no Leste dos EUA conecta o Ramo 2 ao hub leste dos EUA.

![diagrama](./media/effective-routes-virtual-hub/diagram.png)

## <a name="view-effective-routes"></a><a name="view"></a>Ver rotas eficazes

Quando você seleciona 'Exibir rotas eficazes' no portal, ele produz a saída mostrada na [tabela de rotas do Hub](#routetable) para o Hub dos EUA Leste.

Para colocar isso em perspectiva, a primeira linha implica que o hub do Leste dos EUA aprendeu a rota de 10.20.1.0/24 (Branch 1) devido à conexão *do tipo* DE HOP VPN Next ('Next hop' VPN Gateway Instance0 IP 10.1.0.6, Instance1 IP 10.1.0.7). *Route Origin* aponta para o ID de recurso. *O CAMINHO AS* indica o caminho AS para o Ramo 1.

### <a name="hub-route-table"></a><a name="routetable"></a>Tabela de rotas do hub

Use a barra de rolagem na parte inferior da tabela para visualizar o "CAMINHO AS".

| **Prefixo** |  **Próximo tipo de salto** | **Próximo salto** |  **Origem da rota** |**CAMINHO AS** |
| ---        | ---                | ---          | ---               | ---         |
| 10.20.1.0/24|VPN |10.1.0.6, 10.1.0.7| /assinaturas/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/vpnGateways/343a19aa6ac74e4d81f05ccccf1536cf-eastus-gw| 20000|
|10.21.1.0/24 |ExpressRoute|10.1.0.10, 10.1.0.11|/assinaturas/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/expressRouteGateways/4444ac74e4d85555-eastus-gw|21000|
|10.23.1.0/24| VPN |10.1.0.6, 10.1.0.7|/assinaturas/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/vpnGateways/343a19aa6ac74e4d81f05ccccf1536cf-eastus-gw|23000|
|10.4.0.0/16|Conexão de rede virtual| No link |  |  |
|10.5.0.0/16| Endereço IP| 10.4.0.6|/assinaturas/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/virtualHubs/easthub_1/routeTables/table_1| |
|0.0.0.0/0| Endereço IP| `<Azure Firewall IP>` |/assinaturas/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/virtualHubs/easthub_1/routeTables/table_1| |
|10.22.1.0/16| Hub remoto|10.8.0.6, 10.8.0.7|/assinaturas/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/virtualHubs/westhub_| 4848-22000 |
|10.9.0.0/16| Hub remoto|  No link |/assinaturas/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/virtualHubs/westhub_1| |

>[!NOTE]
> Se os centros do Leste dos EUA e da Europa Ocidental não estivessem se comunicando entre si no exemplo topologia, a rota aprendida (10.9.0.0/16) não existiria. Os hubs só anunciam redes que estão diretamente conectadas a elas.
>

## <a name="additional-information"></a><a name="additional"></a>Informações adicionais

### <a name="about-the-hub-route-table"></a><a name="abouthubroute"></a>Sobre a tabela de rotas do hub

Você pode criar uma rota de hub virtual e aplicar a rota à tabela de rotas do hub virtual. Você pode aplicar várias rotas à tabela de rotas do hub virtual. Isso permite definir uma rota para o vnet de destino através de um endereço IP (tipicamente o Network Virtual Appliance (NVA) em um VNet falado). Para obter mais informações sobre NVAs, consulte [Route traffic de um hub virtual para um NVA](virtual-wan-route-table-portal.md).

### <a name="about-default-route-00000"></a><a name="aboutdefaultroute"></a>Sobre a rota padrão (0.0.0.0/0)

Um hub virtual tem a capacidade de propagar uma rota padrão aprendida para uma rede virtual, uma VPN site-para-site e uma conexão ExpressRoute se o sinalizador estiver 'Ativado' na conexão. Este sinalizador é visível quando você edita uma conexão de rede virtual, uma conexão VPN ou uma conexão ExpressRoute. O 'EnableInternetSecurity' é sempre falso por padrão nas conexões Hub VNet, ExpressRoute e VPN.

A rota padrão não se origina no hub WAN virtual. A rota padrão é propagada se já for aprendida pelo hub WAN virtual como resultado da implantação de um firewall no hub ou se outro site conectado forçou o tunelamento ativado.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre WAN Virtual, confira a [Visão geral de WAN Virtual](virtual-wan-about.md).