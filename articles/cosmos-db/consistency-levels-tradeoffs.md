---
title: Azure Cosmos DB compensações de consistência, disponibilidade e desempenho
description: Equilíbrio entre disponibilidade e desempenho para vários níveis de coerência no Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/23/2020
ms.reviewer: sngun
ms.openlocfilehash: 4e2cb2b93010478338cd40236403da4ca0ca99fc
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/07/2020
ms.locfileid: "91825272"
---
# <a name="latency-availability-and-performance-tradeoffs-with-different-azure-cosmos-db-consistency-levels"></a>Latência, disponibilidade e compensações de desempenho com diferentes níveis de consistência de Azure Cosmos DB

Bancos de dados distribuídos que dependem de replicação para alta disponibilidade, baixa latência ou ambos devem fazer compensações. As compensações estão entre consistência de leitura versus a disponibilidade, latência e taxa de transferência.

O Azure Cosmos DB aborda a coerência de dados como um espectro de opções. Essa abordagem inclui mais opções que os dois extremos de consistência forte e eventual. Você pode escolher entre cinco níveis bem definidos no espectro de consistência. Do mais forte ao mais fraco, os níveis são:

- *Forte*
- *Desatualização limitada*
- *Sessão*
- *Prefixo consistente*
- *Iminente*

Cada nível fornece compensações de desempenho e disponibilidade e é apoiado por SLAs abrangentes.

## <a name="consistency-levels-and-latency"></a>Níveis de coerência e latência

A latência de leitura para todos os níveis de coerência é sempre asseguradamente menor que 10 milissegundos no 99º percentil contando com suporte do SLA. A latência de leitura é garantida por SLA. A latência média de leitura, no 50 º percentil, normalmente é de 4 milissegundos ou menos.

A latência de gravação para todos os níveis de consistência sempre é garantida para menos de 10 milissegundos no 99 º percentil. A latência de gravação é garantida por SLA. A latência média de gravação, 50 º percentil, geralmente é 5 milissegundos ou menos. Contas do Cosmos do Azure que abrangem várias regiões e são configuradas com coerência forte são uma exceção a essa garantia.

### <a name="write-latency-and-strong-consistency"></a>Latência de gravação e consistência forte

Para contas do Azure Cosmos configuradas com consistência forte com mais de uma região, a latência de gravação é igual a duas vezes a RTT (tempo de ida e volta) entre qualquer uma das duas regiões mais distantes, mais 10 milissegundos no 99 º percentil. O RTT de rede alto entre as regiões será convertido em latência mais alta para solicitações de Cosmos DB, uma vez que a consistência forte conclui uma operação somente depois de garantir que ela tenha sido confirmada em todas as regiões de uma conta.

A latência RTT é uma função de distância à velocidade da luz e a topologia de rede exata do Azure. A Rede do Azure não fornece nenhum SLA de latência para o RTT entre nenhum par de regiões do Azure. Para sua conta do Azure Cosmos, latências de replicação são exibidas no portal do Azure. Você pode usar o portal do Azure (vá para a folha métricas, selecione a guia consistência) para monitorar as latências de replicação entre várias regiões associadas à sua conta do Azure Cosmos.

> [!IMPORTANT]
> A consistência forte para contas com regiões que abrangem mais de 5000 milhas (8000 quilômetros) é bloqueada por padrão devido à alta latência de gravação. Para habilitar esse recurso, entre em contato com o suporte.

## <a name="consistency-levels-and-throughput"></a>Níveis de coerência e taxa de transferência

- Para uma desatualização forte e limitada, as leituras são feitas em duas réplicas em um conjunto de quatro réplicas (quorum minoritário) para fornecer garantias de consistência. A sessão, o prefixo consistente e a eventualidade fazem leituras de réplica única. O resultado é que, para o mesmo número de unidades de solicitação, a taxa de transferência de leitura para forte e a desatualização limitada é a metade dos outros níveis de consistência.

- Para um determinado tipo de operação de gravação, como inserir, substituir, submeter, excluir, a taxa de transferência de gravação para unidades de solicitação é idêntica para todos os níveis de consistência.

|**Nível de consistência**|**Leituras de quorum**|**Gravações de quorum**|
|--|--|--|
|**Forte**|Minoria local|Maioria global|
|**Desatualização limitada**|Minoria local|Maioria local|
|**Sessão**|Réplica única (usando o token de sessão)|Maioria local|
|**Prefixo consistente**|Réplica única|Maioria local|
|**Iminente**|Réplica única|Maioria local|

## <a name="consistency-levels-and-data-durability"></a><a id="rto"></a>Níveis de consistência e durabilidade dos dados

Em um ambiente de banco de dados distribuído globalmente, há uma relação direta entre a durabilidade dos dados e o nível de consistência no caso de uma interrupção em toda a região. À medida que você vai desenvolvendo o plano de continuidade dos negócios, precisará saber qual é o tempo máximo aceitável antes que o aplicativo se recupere completamente após um evento de interrupção. O tempo necessário para que um aplicativo se recupere totalmente é conhecido como**RTO**( **objetivo de tempo de recuperação** ). Também é necessário saber o período máximo de atualizações de dados recentes que o aplicativo pode perder sem maiores problemas durante a recuperação após um evento de interrupção. O período de tempo das atualizações que você pode perder é conhecido como**RPO**( **objetivo de ponto de recuperação** ).

A tabela a seguir define a relação entre o modelo de consistência e a durabilidade dos dados na presença de uma interrupção em toda a região. É importante observar que, em um sistema distribuído, mesmo com uma consistência forte, é impossível ter um banco de dados distribuído com RPO e RTO de zero devido à teorema CAP. Para saber mais sobre o porquê, consulte [níveis de consistência em Azure Cosmos DB](consistency-levels.md).

|**Região (ões)**|**Modo de replicação**|**Nível de consistência**|**RPO**|**RTO**|
|---------|---------|---------|---------|---------|
|1|Uma ou várias regiões de gravação|Qualquer nível de consistência|< 240 minutos|< 1 semana|
|> 1|Região de gravação única|Sessão, Prefixo Consistente, Eventual|< 15 minutos|< 15 minutos|
|> 1|Região de gravação única|Desatualização limitada|*K*  &  *T*|< 15 minutos|
|> 1|Região de gravação única|Forte|0|< 15 minutos|
|> 1|Várias regiões de gravação|Sessão, Prefixo Consistente, Eventual|< 15 minutos|0|
|> 1|Várias regiões de gravação|Desatualização limitada|*K*  &  *T*|0|

*K* = o número de versões *"K"* (ou seja, atualizações) de um item.

*T* = o intervalo de tempo *"T"* desde a última atualização.

## <a name="strong-consistency-and-multiple-write-regions"></a>Consistência forte e várias regiões de gravação

As contas do cosmos configuradas com várias regiões de gravação não podem ser configuradas para uma consistência forte, pois não é possível que um sistema distribuído forneça um RPO de zero e um RTO igual a zero. Além disso, não há benefícios de latência de gravação sobre o uso de consistência forte com várias regiões de gravação porque, a solicitação de gravação em qualquer região deve ser replicada e confirmada em todas as regiões configuradas na conta. Isso resulta na mesma latência de gravação que uma única conta de região de gravação.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a distribuição global e compensações de consistência geral nos sistemas distribuídos. Confira os seguintes artigos:

- [Consistency tradeoffs in modern distributed database systems design](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k) (Compensações de coerência no projeto de sistemas de bancos de dados modernos distribuídos)
- [Alta disponibilidade](high-availability.md)
- [SLA do Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/)
