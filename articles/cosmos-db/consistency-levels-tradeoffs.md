---
title: Azure Cosmos DB consistência, disponibilidade e compensações de desempenho
description: Equilíbrio entre disponibilidade e desempenho para vários níveis de coerência no Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/06/2020
ms.reviewer: sngun
ms.openlocfilehash: 7cdaa9699b15000359c438bcc410e300415b759a
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81379952"
---
# <a name="consistency-availability-and-performance-tradeoffs"></a>Compensações de consistência, disponibilidade e desempenho

Bancos de dados distribuídos que dependem de replicação para alta disponibilidade, baixa latência ou ambos devem fazer compensações. As compensações estão entre consistência de leitura versus a disponibilidade, latência e taxa de transferência.

O Azure Cosmos DB aborda a coerência de dados como um espectro de opções. Essa abordagem inclui mais opções que os dois extremos de consistência forte e eventual. Você pode escolher entre cinco níveis bem definidos no espectro de consistência. Do mais forte ao mais fraco, os níveis são:

- *Forte*
- *Impasse delimitado*
- *Session*
- *Prefixo consistente*
- *Eventual*

Cada nível fornece negociações de disponibilidade e desempenho e é apoiado por SLAs abrangentes.

## <a name="consistency-levels-and-latency"></a>Níveis de coerência e latência

A latência de leitura para todos os níveis de coerência é sempre asseguradamente menor que 10 milissegundos no 99º percentil contando com suporte do SLA. A latência de leitura é garantida por SLA. A latência média de leitura, no percentil 50, é tipicamente de 4 milissegundos ou menos.

A latência de escrita para todos os níveis de consistência é sempre garantida a menos de 10 milissegundos no percentil 99. A latência de gravação é garantida por SLA. A latência média de gravação, 50 º percentil, geralmente é 5 milissegundos ou menos. Contas do Cosmos do Azure que abrangem várias regiões e são configuradas com coerência forte são uma exceção a essa garantia.

### <a name="write-latency-and-strong-consistency"></a>Escrever latência e consistência forte

Para contas do Azure Cosmos configuradas com forte consistência com mais de uma região, a latência de gravação é igual a duas vezes o tempo de ida e volta (RTT) entre qualquer uma das duas regiões mais distantes, mais 10 milissegundos no percentil 99. O RTT de alta rede entre as regiões se traduzirá em maior latência para solicitações do Cosmos DB, uma vez que a forte consistência completa uma operação somente depois de garantir que ela foi comprometida com todas as regiões dentro de uma conta.

A latência RTT é uma função de distância à velocidade da luz e a topologia de rede exata do Azure. A Rede do Azure não fornece nenhum SLA de latência para o RTT entre nenhum par de regiões do Azure. Para sua conta do Azure Cosmos, latências de replicação são exibidas no portal do Azure. Você pode usar o portal Azure (vá para a lâmina Métricas, selecione Guia Consistência) para monitorar as latências de replicação entre várias regiões que estão associadas à sua conta do Azure Cosmos.

> [!IMPORTANT]
> A forte consistência das contas com regiões que abrangem mais de 8.000 quilômetros é bloqueada por padrão devido à alta latência de gravação. Para habilitar esse recurso, entre em contato com o suporte.

## <a name="consistency-levels-and-throughput"></a>Níveis de coerência e taxa de transferência

- Para oobsoleto forte e limitado, as leituras são feitas contra duas réplicas em um conjunto de quatro réplicas (quórum minoritário) para fornecer garantias de consistência. Sessão, prefixo consistente e eventual leituras de réplica única. O resultado é que, para o mesmo número de unidades de solicitação, o rendimento de leitura para o obsoleto forte e limitado é metade dos outros níveis de consistência.

- Para um determinado tipo de operação de gravação, como inserir, substituir, submeter, excluir, a taxa de transferência de gravação para unidades de solicitação é idêntica para todos os níveis de consistência.

|**Nível de Consistência**|**Leituras de quórum**|**Escreve quórum**|
|--|--|--|
|**Forte**|Minoria Local|Maioria Global|
|**Desatualização Limitada**|Minoria Local|Maioria Local|
|**Session**|Réplica única (usando token de sessão)|Maioria Local|
|**Prefixo Coerente**|Réplica única|Maioria Local|
|**Eventual**|Réplica única|Maioria Local|

## <a name="consistency-levels-and-data-durability"></a><a id="rto"></a>Níveis de consistência e durabilidade dos dados

Em um ambiente de banco de dados distribuído globalmente, há uma relação direta entre a durabilidade dos dados e o nível de consistência no caso de uma interrupção em toda a região. À medida que você vai desenvolvendo o plano de continuidade dos negócios, precisará saber qual é o tempo máximo aceitável antes que o aplicativo se recupere completamente após um evento de interrupção. O tempo necessário para um aplicativo de recuperação completa é conhecido como **Objetivo do Tempo de Recuperação** (**RTO).** Também é necessário saber o período máximo de atualizações de dados recentes que o aplicativo pode perder sem maiores problemas durante a recuperação após um evento de interrupção. O período de tempo de atualizações que você pode dar ao luxo de perder é conhecido como **objetivo de ponto de recuperação** (**RPO).**

A tabela abaixo define a relação entre modelo de consistência e durabilidade dos dados na presença de uma paralisação de toda a região. É importante notar que em um sistema distribuído, mesmo com forte consistência, é impossível ter um banco de dados distribuído com um RPO e RTO de zero devido ao Teorema do CAP. Para saber mais sobre o porquê, consulte [níveis de consistência no Azure Cosmos DB](consistency-levels.md).

|**Região**|**Modo de replicação**|**Nível de consistência**|**RPO**|**RTO**|
|---------|---------|---------|---------|---------|
|1|Único ou vários mestres|Qualquer nível de consistência|< 240 minutos|< 1 semana|
|> 1|Único mestre|Sessão, Prefixo Consistente, Eventual|< 15 minutos|< 15 minutos|
|> 1|Único mestre|Bounded staleness|*K* & *T*|< 15 minutos|
|> 1|Único mestre|Strong|0|< 15 minutos|
|> 1|Vários mestres|Sessão, Prefixo Consistente, Eventual|< 15 minutos|0|
|> 1|Vários mestres|Bounded staleness|*K* & *T*|0|

*K* = O número de versões *"K"* (ou seja, atualizações) de um item.

*T* = O intervalo de tempo *"T"* desde a última atualização.

## <a name="strong-consistency-and-multi-master"></a>Forte consistência e multi-mestre

As contas cosmos configuradas para multimestre não podem ser configuradas para uma forte consistência, pois não é possível que um sistema distribuído forneça um RPO de zero e um RTO de zero. Além disso, não há benefícios de latência de gravação para o uso de forte consistência com multi-mestre, pois qualquer gravação em qualquer região deve ser replicada e comprometida com todas as regiões configuradas dentro da conta. Isso resulta na mesma latência de escrita que uma única conta mestra.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a distribuição global e compensações de consistência geral nos sistemas distribuídos. Veja os artigos a seguir:

- [Consistency tradeoffs in modern distributed database systems design](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k) (Compensações de coerência no projeto de sistemas de bancos de dados modernos distribuídos)
- [Alta disponibilidade](high-availability.md)
- [SLA do Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
