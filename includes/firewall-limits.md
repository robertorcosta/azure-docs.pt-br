---
title: incluir arquivo
description: incluir arquivo
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 04/07/2020
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: fb95301d4faa958a677c42bc8092ac52a7c95c8b
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/07/2020
ms.locfileid: "80813742"
---
| Recurso | Limite |
| --- | --- |
| Taxa de transferência de dados |30 Gbps<sup>1</sup> |
|Regras|10,000. Todos os tipos de regras combinadas.|
|Regras máximas de DNAT|298<br>Se o protocolo de uma regra for configurado tanto para TCP quanto para UDP, ele conta como duas regras.|
|Tamanho mínimo de AzureFirewallSubnet |/ 26|
|Intervalo de portas em regras de rede e de aplicativo|1 – 65535|
|Endereços IP públicos|Máximo de 100 (atualmente, as portas SNAT são adicionadas apenas para os cinco primeiros endereços IP públicos.)|
|Endereços IP de grupos IP|50 Grupos IP ou menos: máximo de 5000 endereços IP individuais cada um por instância de firewall.<br>51 - 100 Grupos IP: 500 endereços IP individuais cada por instância de firewall.<br><br>Para obter mais informações, consulte [Grupos IP (visualização) no Firewall do Azure](../articles/firewall/ip-groups.md#ip-address-limits)
|Tabela de rotas|Por padrão, o AzureFirewallSubnet tem uma rota 0.0.0.0/0 com o valor NextHopType definido para **Internet**.<br><br>O Firewall do Azure deve ter conectividade direta com a Internet. Se o AzureFirewallSubnet aprender uma rota padrão para sua rede local via BGP, você deve substituí-la com um UDR 0.0.0.0/0 com o valor **NextHopType** definido como **Internet** para manter a conectividade direta com a Internet. Por padrão, o Firewall do Azure não dá suporte ao túnel forçado para uma rede local.<br><br>No entanto, se sua configuração exigir um túnel forçado para uma rede local, a Microsoft dará suporte de acordo com cada caso. Entre em contato com o suporte para que possamos analisar seu caso. Se ele for aceito, permitiremos sua assinatura e garantiremos que a conectividade necessária do firewall com a Internet seja mantida.|

<sup>1</sup> Se você precisar aumentar esses limites, entre em contato com o Suporte Azure.
