---
title: incluir arquivo
description: incluir arquivo
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 06/22/2020
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: b317c2a43352a750d4700fad56d5f7d741b2cc7d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85805708"
---
| Recurso | Limite |
| --- | --- |
| Taxa de transferência de dados |30 Gbps<sup>1</sup> |
|Regras|10.000. Todos os tipos de regra combinados.|
|Máximo de regras DNAT|298 para um único endereço IP público.<br>Quaisquer endereços IP públicos adicionais contribuem para as portas SNAT disponíveis, mas reduzem o número de regras de DNAT disponíveis. Por exemplo, dois endereços IP públicos permitem regras DNAT de 297. Se o protocolo de uma regra estiver configurado para TCP e UDP, ele contará como duas regras.|
|Tamanho mínimo de AzureFirewallSubnet |/ 26|
|Intervalo de portas em regras de rede e de aplicativo|1 – 65535|
|Endereços IP públicos|250 no máximo. Todos os endereços IP públicos podem ser usados em regras DNAT e todos contribuem para as portas SNAT disponíveis.|
|Endereços IP em grupos de IP|Máximo de 100 grupos IP por firewall.<br>Máximo de 5000 endereços IP individuais ou prefixos IP por cada grupo de IP.<br><br>Para obter mais informações, consulte [grupos de IPs no firewall do Azure](../articles/firewall/ip-groups.md#ip-address-limits).
|Tabela de rotas|Por padrão, o AzureFirewallSubnet tem uma rota 0.0.0.0/0 com o valor NextHopType definido como **Internet**.<br><br>O Firewall do Azure deve ter conectividade direta com a Internet. Se o seu AzureFirewallSubnet aprender uma rota padrão para sua rede local via BGP, você precisará substituir isso por um UDR 0.0.0.0/0 com o valor **NextHopType** definido como **Internet** para manter a conectividade direta com a Internet. Por padrão, o Firewall do Azure não dá suporte ao túnel forçado para uma rede local.<br><br>No entanto, se sua configuração exigir um túnel forçado para uma rede local, a Microsoft dará suporte de acordo com cada caso. Entre em contato com o suporte para que possamos analisar seu caso. Se ele for aceito, permitiremos sua assinatura e garantiremos que a conectividade necessária do firewall com a Internet seja mantida.|

<sup>1</sup>Se precisar aumentar esses limites, entre em contato com o Suporte do Azure.
