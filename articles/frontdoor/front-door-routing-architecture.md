---
title: Arquitetura de roteamento de porta frontal do Azure | Microsoft Docs
description: Este artigo ajuda você a entender o aspecto de exibição global da arquitetura do Front Door.
services: front-door
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: duau
ms.openlocfilehash: b36852e27f6aa3a909dd645c19a12c55e082b761
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89399320"
---
# <a name="routing-architecture-overview"></a>Visão geral da arquitetura de roteamento

A porta frontal do Azure quando recebe suas solicitações de cliente, ela as responde (se o Caching estiver habilitado) ou as encaminha para o back-end do aplicativo apropriado (como um proxy reverso).

</br>Há oportunidades de otimizar o tráfego ao encaminhar para o Azure Front Door, bem como ao encaminhar para back-ends.

## <a name="selecting-the-front-door-environment-for-traffic-routing-anycast"></a><a name = "anycast"></a>Selecionando o ambiente do Front Door para roteamento de tráfego (Anycast)

O roteamento para ambientes do Azure Front Door aproveita o [Anycast](https://en.wikipedia.org/wiki/Anycast) para tráfego do DNS (Sistema de Nomes de Domínio) e do protocolo HTTP, portanto, o tráfego do usuário vai para o ambiente mais próximo em termos de topologia de rede (menos saltos). Normalmente, essa arquitetura oferece um tempo de viagem de ida e volta melhor aos usuários finais (maximizando os benefícios do TCP de divisão). O Front Door organiza seus ambientes em "anéis" primários e de fallback.  O anel externo contém ambientes mais próximos aos usuários, oferecendo latências menores.  O anel interno contém ambientes que podem manipular o failover para o ambiente do anel externo em caso de problemas. O anel externo é o destino preferido para todo o tráfego, mas o anel interno é necessário para manipular o estouro de tráfego do anel externo. Em termos de VIPs (endereços IP virtuais), cada host de front-end ou domínio atendido pelo Front Door é atribuído a um VIP primário, que é anunciado pelos ambientes dos anéis interno e externo, bem como um VIP de fallback, que é anunciado somente pelos ambientes do anel interno. 

</br>Essa estratégia geral garantirá que as solicitações dos usuários finais sempre acessem o ambiente do Front Door mais próximo e que, mesmo se o ambiente preferencial do Front Door não estiver íntegro, o tráfego passe automaticamente para o próximo ambiente mais próximo.

## <a name="connecting-to-front-door-environment-split-tcp"></a><a name = "splittcp"></a>Conectando ao ambiente do Front Door (TCP de divisão)

[TCP de divisão](https://en.wikipedia.org/wiki/Performance-enhancing_proxy) é uma técnica para reduzir latências e problemas de TCP dividindo em partes menores uma conexão que poderia apresentar um tempo longo de viagem de ida e volta.  Colocando os ambientes do Front Door mais próximos dos usuários finais e encerrando as conexões TCP dentro do ambiente do Front Door, uma conexão TCP com um RTT (tempo da viagem de ida e volta) longo até o back-end do aplicativo é dividida em duas conexões TCP. A conexão curta entre o usuário final e o ambiente de porta de front-end significa que a conexão é estabelecida em três viagens curtas em vez de três idas longas, economizando latência.  A conexão longa entre o ambiente do Front Door e o back-end pode ser pré-estabelecida e reutilizada em várias chamadas do usuário final, novamente reduzindo o tempo de conexão TCP.  O efeito é multiplicado ao estabelecer uma conexão SSL/protocolo TLS, pois há mais viagens de ida e volta para proteger a conexão.

## <a name="processing-request-to-match-a-routing-rule"></a>Processando a solicitação para corresponder a uma regra de roteamento
Depois de estabelecer uma conexão e fazer um handshake de TLS, quando uma solicitação chega em um ambiente de porta de front-end, a correspondência de uma regra de roteamento é a primeira etapa. Essa correspondência é basicamente determinar de todas as configurações no Front Door, a qual regra de roteamento específica a solicitação corresponde. Leia sobre como o Front Door faz a [correspondência de rota](front-door-route-matching.md) para saber mais.

## <a name="identifying-available-backends-in-the-backend-pool-for-the-routing-rule"></a>Identificando back-ends disponíveis no pool de back-ends para a regra de roteamento
Depois que o Front Door encontrar uma correspondência a uma regra de roteamento com base na solicitação de entrada e se não houver nenhum cache, a próxima etapa será obter o status de investigação de integridade do pool de back-end associado à rota correspondente. Leia sobre como o Front Door monitora a integridade do back-end usando [Investigações de Integridade](front-door-health-probes.md) para saber mais.

## <a name="forwarding-the-request-to-your-application-backend"></a>Encaminhando a solicitação ao back-end do aplicativo
Por fim, supondo que não haja nenhum cache configurado, a solicitação do usuário é encaminhada para o "melhor" back-end com base na configuração do [método de roteamento do Front Door](front-door-routing-methods.md).

## <a name="next-steps"></a>Próximas etapas

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
