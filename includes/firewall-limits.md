---
title: incluir arquivo
description: incluir arquivo
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 05/14/2020
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 4e00b0c46c79dc058d19076a396d055ba1d41b87
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83673519"
---
| Recurso | Limite |
| --- | --- |
| Taxa de transferência de dados |30 Gbps<sup>1</sup> |
|Regras|10.000. Todos os tipos de regra combinados.|
|Máximo de regras DNAT|298<br>Se o protocolo de uma regra estiver configurado para TCP e UDP, ele contará como duas regras.|
|Tamanho mínimo de AzureFirewallSubnet |/ 26|
|Intervalo de portas em regras de rede e de aplicativo|1 – 65535|
|Endereços IP públicos|250 no máximo. Todos os endereços IP públicos podem ser usados em regras DNAT e todos contribuem para as portas SNAT disponíveis.|
|Endereços IP em grupos de IP|50 grupos de IP ou menos: máximo de 5.000 endereços IP individuais cada por instância de firewall.<br>51 – 100 grupos de IP: 500 endereços IP individuais cada por instância de firewall.<br><br>Para obter mais informações, confira [Grupos de IP (versão prévia) no Firewall do Azure](../articles/firewall/ip-groups.md#ip-address-limits)
|Tabela de rotas|Por padrão, o AzureFirewallSubnet tem uma rota 0.0.0.0/0 com o valor NextHopType definido como **Internet**.<br><br>O Firewall do Azure deve ter conectividade direta com a Internet. Se o seu AzureFirewallSubnet aprender uma rota padrão para sua rede local via BGP, você precisará substituir isso por um UDR 0.0.0.0/0 com o valor **NextHopType** definido como **Internet** para manter a conectividade direta com a Internet. Por padrão, o Firewall do Azure não dá suporte ao túnel forçado para uma rede local.<br><br>No entanto, se sua configuração exigir um túnel forçado para uma rede local, a Microsoft dará suporte de acordo com cada caso. Entre em contato com o suporte para que possamos analisar seu caso. Se ele for aceito, permitiremos sua assinatura e garantiremos que a conectividade necessária do firewall com a Internet seja mantida.|

<sup>1</sup>Se precisar aumentar esses limites, entre em contato com o Suporte do Azure.
