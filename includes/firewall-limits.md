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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80813742"
---
| Recurso | Limite |
| --- | --- |
| Taxa de transferência de dados |30 Gbps<sup>1</sup> |
|Regras|10.000. Todos os tipos de regra combinados.|
|Máximo de regras DNAT|298<br>Se o protocolo de uma regra estiver configurado para TCP e UDP, ele conta como duas regras.|
|Tamanho mínimo de AzureFirewallSubnet |/ 26|
|Intervalo de portas em regras de rede e de aplicativo|1 – 65535|
|Endereços IP públicos|100 máximo (atualmente, as portas SNAT são adicionadas somente aos cinco primeiros endereços IP públicos).|
|Endereços IP de grupos de IPS|50 grupos IP ou menos: máximo de 5000 endereços IP individuais cada por instância de firewall.<br>51-100 grupos de IP: 500 endereço IP individual cada instância de firewall.<br><br>Para obter mais informações, consulte [grupos de IPS (versão prévia) no firewall do Azure](../articles/firewall/ip-groups.md#ip-address-limits)
|Tabela de rotas|Por padrão, AzureFirewallSubnet tem uma rota 0.0.0.0/0 com o valor NextHopType definido como **Internet**.<br><br>O Firewall do Azure deve ter conectividade direta com a Internet. Se o seu AzureFirewallSubnet aprende uma rota padrão para sua rede local via BGP, você deve substituí-lo por um UDR 0.0.0.0/0 com o valor **NextHopType** definido como **Internet** para manter a conectividade direta com a Internet. Por padrão, o Firewall do Azure não dá suporte ao túnel forçado para uma rede local.<br><br>No entanto, se sua configuração exigir um túnel forçado para uma rede local, a Microsoft dará suporte de acordo com cada caso. Entre em contato com o suporte para que possamos analisar seu caso. Se ele for aceito, permitiremos sua assinatura e garantiremos que a conectividade necessária do firewall com a Internet seja mantida.|

<sup>1</sup> Se você precisar aumentar esses limites, entre em contato com o suporte do Azure.
