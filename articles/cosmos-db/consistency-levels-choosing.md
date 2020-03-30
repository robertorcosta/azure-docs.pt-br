---
title: Escolha o nível de consistência certo para o seu aplicativo Azure Cosmos DB
description: Como escolher o nível certo de coerência para o seu aplicativo no Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.reviewer: sngun
ms.openlocfilehash: cea157e272a2bf464141e592b8e742f314a83233
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75441912"
---
# <a name="choose-the-right-consistency-level"></a>Escolher nível certo de consistência 

Bancos de dados distribuídos que dependem de replicação para alta disponibilidade, baixa latência ou ambos realizam o equilíbrio fundamental entre a coerência de leitura versus a disponibilidade, a latência e a taxa de transferência. A maioria dos bancos de dados distribuídos disponíveis comercialmente pede aos desenvolvedores que escolham entre os dois modelos de consistência extrema: *consistência forte* e *eventual* consistência. O Azure Cosmos DB permite que os desenvolvedores escolham entre os cinco modelos de consistência bem definidos: *forte,* *delimitado,* *sessão,* *prefixo consistente* e *eventual*. Cada um desses modelos de coerência é bem definido, intuitivo e pode ser usado para cenários específicos do mundo real. Cada um dos cinco modelos de consistência fornece disponibilidade precisa e compensações de desempenho e são apoiados por SLAs [abrangentes.](consistency-levels-tradeoffs.md) As considerações simples a seguir lhe ajudarão a fazer a escolha certa em muitos cenários comuns.

## <a name="sql-api-and-table-api"></a>SQL API e a API de Tabela

Considere os seguintes pontos se o aplicativo for construído usando a API SQL ou a API de tabela:

- Para muitos cenários do mundo real, a coerência de sessão é ideal e é a opção recomendada. Para obter mais informações, consulte [Como gerenciar o token de sessão para sua aplicação](how-to-manage-consistency.md#utilize-session-tokens).

- Se o seu aplicativo exigir consistência forte, é recomendável que você use um nível de consistência de stality limitado.

- Se você precisar de garantias de consistência mais rigorosas do que as fornecidas pela consistência da sessão e pela latência de um dígito de milissegundo para gravações, é recomendável usar o nível de consistência do stality limitado.  

- Se o seu aplicativo exigir consistência eventual, é recomendável usar um nível consistente de consistência de prefixo.

- Se você precisar de garantias de consistência menos rígidas do que as fornecidas pela consistência da sessão, é recomendável usar um nível consistente de consistência de prefixo.

- Se você precisar da maior disponibilidade e da menor latência, use um eventual nível de consistência.

- Se você precisar de mais durabilidade de dados ainda sem perder desempenho, poderá criar um nível de consistência personalizado na camada de aplicativo. Para obter mais informações, confira [Como implementar a sincronização personalizada em seus aplicativos](how-to-custom-synchronization.md).

## <a name="cassandra-mongodb-and-gremlin-apis"></a>APIs de Cassandra, MongoDB e Gremlin

- Para obter detalhes sobre o mapeamento entre o “Read Consistency Level” oferecido nos níveis de consistência do Apache Cassandra e do Cosmos DB, consulte [Níveis de consistência e as APIs do Cosmos DB](consistency-levels-across-apis.md#cassandra-mapping).

- Para obter detalhes sobre o mapeamento entre o "Problema de leitura" do MongoDB e os níveis de coerência do Azure Cosmos DB, confira [Níveis de coerência e APIs do Cosmos DB](consistency-levels-across-apis.md#mongo-mapping).

## <a name="consistency-guarantees-in-practice"></a>Garantias de consistência na prática

Na prática, muitas vezes você pode obter garantias de consistência mais fortes. Garantias de consistência para uma operação de leitura correspondem à atualização e ordenação do estado do banco de dados que você solicita. Consistência de leitura é vinculada ao pedido e a propagação das operações de gravação/atualização.  

* Quando o nível de coerência é definido como **desatualização limitada**, o Cosmos DB garante que os clientes sempre leiam o valor de uma gravação anterior, com um limitado pela janela de desatualização.

* Quando o nível de consistência é definido como **strong**, a janela de staleness é equivalente a zero e os clientes têm a garantia de ler o último valor confirmado da operação de gravação.

* Para os três níveis de coerência restantes, a janela de desatualização limitada depende muito da carga de trabalho. Por exemplo, se não houver operações de gravação no banco de dados, é provável que uma operação de leitura com **eventuais**, **sessão** ou **consistência consistente prefira** resultados como uma operação de leitura com um nível de consistência forte.

Se sua conta do Azure Cosmos estiver configurada com um nível de consistência diferente da consistência forte, você poderá descobrir a probabilidade de que seus clientes possam obter leituras fortes e consistentes para suas cargas de trabalho olhando para a métrica *de sobismo desatrilado (PBS) probabilisticamente.* Essa métrica é exposta no portal do Azure, para obter mais informações, consulte [métrica Monitor Probabilistic Bounded Staleness (PBS)](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric).

A desatualização limitada probabilística mostra o quão eventual é a sua coerência eventual. Essa métrica fornece uma visão de quantas vezes você pode obter uma consistência mais forte do que o nível de consistência que você configurou atualmente em sua conta do Azure Cosmos. Em outras palavras, você pode ver a probabilidade (medida em milissegundos) de obter leituras fortemente consistentes para uma combinação de regiões de gravação e leitura.

## <a name="next-steps"></a>Próximas etapas

Leia mais sobre os níveis de coerência nos seguintes artigos:

* [Mapeamento dos níveis de coerência entre as APIs do Cosmos DB](consistency-levels-across-apis.md)
* [Equilíbrio entre disponibilidade e desempenho para vários níveis de coerência](consistency-levels-tradeoffs.md)
* [Como gerenciar o token de sessão para o seu aplicativo](how-to-manage-consistency.md#utilize-session-tokens)
* [Monitorar métrica PBS (Desatualização Limitada Probabilística)](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric)
