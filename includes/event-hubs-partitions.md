---
title: incluir arquivo
description: incluir arquivo
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 05/22/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: cbd2d3b46ec339328e0d0889a722d39070104953
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92347068"
---
Os Hubs de Evento fornecem streaming de mensagens por meio de um padrão de consumidor particionado no qual cada consumidor lê somente um subconjunto específico, ou partição, do fluxo de mensagens. Esse padrão permite a escala horizontal para processamento de eventos e fornece outros recursos centrados no fluxo que não estão disponíveis em filas e tópicos.

Uma partição é uma sequência ordenada de eventos que é mantida em um hub de eventos. À medida que novos eventos chegam, eles são adicionados ao final dessa sequência. Uma partição pode ser pensada como "log de confirmação".

![Diagrama que exibe a sequência de eventos mais antigos para os mais recentes.](./media/event-hubs-partitions/partition.png)

Os Hubs de Eventos mantêm os dados por um período de retenção configurado que se aplica a todas as partições no hub de eventos. Eventos expiram periodicamente; não é possível excluí-los explicitamente. Como as partições são independentes e contêm sua própria sequência de dados, elas geralmente crescem a taxas diferentes.

![Hubs de Eventos](./media/event-hubs-partitions/multiple-partitions.png)

O número de partições é especificado na criação do Hub de Eventos e deve estar entre 2 e 32. A contagem de partições não é mutável, portanto você deve considerar a escala de longo prazo ao definir a contagem de partições. As partições são um mecanismo de organização de dados relacionados ao paralelismo de downstream necessário no consumo de aplicativos. O número de partições em um hub de eventos está diretamente relacionado ao número de leitores simultâneos que você espera ter. Você pode aumentar o número de partições para mais de 32 entrando em contato com a equipe de Hubs de Eventos.

Talvez você queira defini-la como o maior valor possível, que é 32, no momento da criação. Lembre-se de que ter mais de uma partição resultará em eventos enviados a várias partições sem reter a ordem, a menos que você configure que os remetentes enviem somente para uma única partição fora das 32, deixando as 31 restantes de partições redundantes. No primeiro caso, será necessário ler os eventos nas 32 partições. No último caso, não há nenhum custo adicional aparente, além da configuração extra que você deverá fazer no Host do Processador de Eventos.

Embora as partições possam ser identificadas e seja possível enviar diretamente a elas, não é recomendável enviar diretamente a uma partição. Em vez disso, será possível usar os constructos de nível superior apresentados na seção [Editores de eventos](../articles/event-hubs/event-hubs-features.md#event-publishers). 

As partições são preenchidas com uma sequência de dados de evento que contêm o corpo do evento, um recipiente de propriedades definidas pelo usuário e metadados como seu deslocamento na partição e seu número na sequência de fluxo.

Recomendamos equilibrar unidades de produtividade e partições de maneira individual para obter uma escala ideal. Uma partição tem entrada e saída de até uma unidade de produtividade garantidas. Embora seja possível obter uma taxa de transferência mais alta em uma partição, o desempenho não será garantido. Por esse motivo, é altamente recomendável que o número de partições em um hub de eventos seja maior ou igual ao número de unidades de produtividade.

Considerando a taxa de transferência total que você planeja usar, será possível saber o número de unidades de produtividade necessárias e o número mínimo de partições. No entanto, quantas partições serão necessárias? Escolha o número de partições com base no paralelismo downstream que você deseja obter, bem como em suas necessidades futuras de taxa de transferência. Não há nenhum custo para obter o número de partições que você tem em um Hub de Eventos.

Para saber mais sobre partições e a compensação entre disponibilidade e confiabilidade, consulte os artigos [Guia de programação dos Hubs de Eventos](../articles/event-hubs/event-hubs-programming-guide.md#partition-key) e [Disponibilidade e consistência em Hubs de Eventos](../articles/event-hubs/event-hubs-availability-and-consistency.md).
