---
title: 'Exibir rotas efetivas de um hub virtual: WAN virtual do Azure | Microsoft Docs'
description: Como exibir rotas efetivas para um hub virtual na WAN virtual do Azure
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 20cdc55b474034480392f9dfb05b20ad25df6939
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86037759"
---
# <a name="view-virtual-hub-effective-routes"></a>Exibir rotas efetivas do hub virtual

Você pode exibir todas as rotas do seu hub de WAN virtual no portal do Azure. Este artigo orienta você pelas etapas para exibir rotas efetivas. Para obter mais informações sobre roteamento de Hub virtual, consulte [sobre roteamento de Hub virtual](about-virtual-hub-routing.md).

> [!NOTE]
> No portal do Azure, alguns desses recursos ainda podem estar sendo distribuídos e não disponíveis até a semana de agosto. 
>

## <a name="select-connections-or-route-tables"></a><a name="routing"></a>Selecionar conexões ou tabelas de rotas

1. Navegue até o Hub virtual e selecione **Roteamento**. Na página roteamento, selecione **rotas efetivas**.
1. No menu suspenso, você pode selecionar o **tipo de conexão** ou uma tabela de **rotas**. Se você não vir uma opção de tabela de rotas, isso significa que você não tem uma tabela de rotas personalizada ou padrão configurada nesse Hub virtual.
1. No menu suspenso de **conexões/tabelas de rotas**, você pode selecionar um dos seguintes itens:

   * Conexão de rede virtual
   * Conexão de site VPN
   * Conexão ExpressRoute
   * Conexão ponto a site
   * Tabela de rotas

   :::image type="content" source="./media/effective-routes-virtual-hub/routing.png" alt-text="Roteamento":::

## <a name="view-output"></a><a name="output"></a>Exibir saída

A saída da página mostra os seguintes campos:

* **Prefixo**: prefixo de endereço conhecido pela entidade atual.
* **Tipo do próximo salto**: pode ser conexão de rede Virtual, VPN_S2S_Gateway, ExpressRouteGateway, Hub remoto ou firewall do Azure.
* **Próximo salto**: esse é o IP ou simplesmente mostra o no link para implicar o Hub atual.
* **Origem**: a ID de recurso da fonte de roteamento.
* **Como caminho**: o atributo BGP como caminho (sistema autônomo) lista todos os números de as que precisam ser percorridos para alcançar o local onde o prefixo ao qual o caminho está anexado, é anunciado.

### <a name="example"></a><a name="example"></a>Exemplo

Os valores na tabela de exemplo a seguir sugerem que a conexão do Hub virtual ou a tabela de rotas aprendeu a rota de 10.2.0.0/24 (um prefixo de ramificação). Ele aprendeu a rota devido ao tipo do **próximo salto da VPN** VPN_S2S_Gateway com a ID de recurso do gateway de VPN do **próximo salto** . A **origem da rota** aponta para a ID de recurso da conexão/tabela de rota/gateway de VPN de origem. As **path** indica o caminho as para a ramificação.

Use a barra de rolagem na parte inferior da tabela para exibir o "caminho AS".

| **Prefixo** |  **Tipo do próximo salto** | **Próximo salto** |  **Origem da rota** |**Caminho AS** |
| ---        | ---                | ---          | ---               | ---         |
| 10.2.0.0/24| VPN_S2S_Gateway |10.1.0.6, 10.1.0.7|/subscriptions/ `<sub id>` /ResourceGroups/ `<resource group name>` /Providers/Microsoft.Network/vpnGateways/vpngw| 20000|

**Considere**

* Se você vir 0.0.0.0/0 na saída **obter rotas efetivas** , ele implica que a rota existe em uma das tabelas de rotas. No entanto, se essa rota foi configurada para Internet, um sinalizador adicional **"enableInternetSecurity": true** é necessário na conexão. A rota efetiva na NIC da VM não mostrará a rota se o sinalizador "enableInternetSecurity" na conexão for "false".

* O campo **propagar a rota padrão** é visto no portal de WAN virtual do Azure quando você edita uma conexão de rede virtual, uma conexão VPN ou uma conexão de ExpressRoute. Esse campo indica o sinalizador **enableInternetSecurity** , que é sempre por padrão "false" para conexões de EXPRESSROUTE e VPN, mas "true" para conexões de rede virtual.

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre WAN virtual, consulte [visão geral da WAN virtual](virtual-wan-about.md).
* Para obter mais informações sobre roteamento de Hub virtual, consulte [sobre roteamento de Hub virtual](about-virtual-hub-routing.md).
