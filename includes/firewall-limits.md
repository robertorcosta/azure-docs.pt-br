---
title: incluir arquivo
description: incluir arquivo
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 04/03/2020
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 4972a80ec7c481ac7bc8860c005c8576ce37a090
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80758338"
---
| Recurso | Limite |
| --- | --- |
| Taxa de transferência de dados |30 Gbps<sup>1</sup> |
|Regras|10,000. Todos os tipos de regras combinadas.|
|Regras máximas de DNAT|298<br>Se o protocolo de uma regra for configurado tanto para TCP quanto para UDP, ele conta como duas regras.|
|Tamanho mínimo de AzureFirewallSubnet |/ 26|
|Intervalo de portas em regras de rede e de aplicativo|0-64.000. Estamos trabalhando para flexibilizar essa limitação.|
|Endereços IP públicos|Máximo de 100 (atualmente, as portas SNAT são adicionadas apenas para os cinco primeiros endereços IP públicos.)|
|Endereços IP de grupos IP|50 Grupos IP ou menos: máximo de 5000 endereços IP individuais cada um por instância de firewall.<br>51 - 100 Grupos IP: 500 endereços IP individuais cada por instância de firewall.<br><br>Para obter mais informações, consulte [Grupos IP (visualização) no Firewall do Azure](../articles/firewall/ip-groups.md#ip-address-limits)
|Tabela de rotas|Por padrão, o AzureFirewallSubnet tem uma rota 0.0.0.0/0 com o valor NextHopType definido para **Internet**.<br><br>O Firewall do Azure deve ter conectividade direta com a Internet. Se o AzureFirewallSubnet aprender uma rota padrão para sua rede local via BGP, você deve substituí-la com um UDR 0.0.0.0/0 com o valor **NextHopType** definido como **Internet** para manter a conectividade direta com a Internet. Por padrão, o Firewall do Azure não dá suporte ao túnel forçado para uma rede local.<br><br>No entanto, se sua configuração exigir um túnel forçado para uma rede local, a Microsoft dará suporte de acordo com cada caso. Entre em contato com o suporte para que possamos analisar seu caso. Se ele for aceito, permitiremos sua assinatura e garantiremos que a conectividade necessária do firewall com a Internet seja mantida.|

<sup>1</sup> Se você precisar aumentar esses limites, entre em contato com o Suporte Azure.
