---
title: Níveis de consistência e APIs do Azure Cosmos DB
description: Entendendo o mapeamento do nível de consistência entre diferentes APIs no Azure Cosmos DB e Apache Cassandra, MongoDB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.reviewer: sngun
ms.openlocfilehash: ef7d032d37105549ff7b05f85b953cd420954602
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131467"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>Níveis de consistência e APIs do Azure Cosmos DB

O Azure Cosmos DB fornece suporte nativo para APIs compatíveis com protocolo de fio para bancos de dados populares. Estes incluem mongodb, Apache Cassandra, Gremlin, e a mesa azure armazenamento. Essas bases de dados não oferecem modelos de consistência definidos com precisão ou garantias apoiadas por SLA para os níveis de consistência. Fornecem normalmente apenas um subconjunto dos cincos modelos de consistência oferecidos pelo Azure Cosmos DB. 

Ao usar a API SQL, a API gremlin e a API de tabela, o nível de consistência padrão configurado na conta Do Azure Cosmos é usado. 

Ao usar a API da Cassandra API ou a API do Azure Cosmos DB para O MongoDB, as aplicações recebem um conjunto completo de níveis de consistência oferecidos por Apache Cassandra e MongoDB, respectivamente, com garantias ainda mais fortes de consistência e durabilidade. Este documento mostra os níveis correspondentes de consistência do Azure Cosmos DB para os níveis de consistência Apache Cassandra e MongoDB.

## <a name="mapping-between-apache-cassandra-and-azure-cosmos-db-consistency-levels"></a><a id="cassandra-mapping"></a>O mapeamento entre os níveis de consistência do Apache Cassandra e o Azure Cosmos DB

Ao contrário do Azure Cosmos DB, apache Cassandra não fornece nativamente garantias de consistência definidas precisamente.  Em vez disso, apache Cassandra fornece um nível de consistência de gravação e um nível de consistência de leitura, para permitir a alta disponibilidade, consistência e compensações de latência. Ao usar a API Cassandra do Azure Cosmos DB: 

* O nível de consistência de gravação do Apache Cassandra é mapeado para o nível de consistência padrão configurado em sua conta Do Azure Cosmos. A consistência de uma operação de gravação (CL) não pode ser alterada por solicitação.

* O Azure Cosmos DB mapeará dinamicamente o nível de consistência de leitura especificado pelo driver cliente Cassandra para um dos níveis de consistência do Azure Cosmos DB configurados dinamicamente em uma solicitação de leitura. 

A tabela a seguir ilustra como os níveis de consistência cassandra nativos são mapeados para os níveis de consistência do Azure Cosmos DB ao usar a API de Cassandra:  

[![Mapeamento do modelo de consistência de Cassandra](./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png)](./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png#lightbox)

## <a name="mapping-between-mongodb-and-azure-cosmos-db-consistency-levels"></a><a id="mongo-mapping"></a>Mapeamento entre os níveis de consistência do MongoDB e do Azure Cosmos DB

Ao contrário do Azure Cosmos DB, o MongoDB nativo não fornece garantias de consistência precisamente definidas. Em vez disso, o MongoDB nativo permite que os usuários configurem as seguintes garantias de consistência: uma preocupação de gravação, uma preocupação de leitura e a diretiva isMaster - para direcionar as operações de leitura para réplicas primárias ou secundárias para alcançar o nível de consistência desejado. 

Ao usar a API do Azure Cosmos DB para MongoDB, o driver Do MongoDB trata sua região de gravação como a réplica principal e todas as outras regiões são lidas como réplica. Você pode escolher qual região associada à sua conta do Azure Cosmos como uma réplica primária. 

Ao usar a API do Azure Cosmos DB para MongoDB:

* A preocupação de gravação é mapeada para o nível de consistência padrão configurado em sua conta Do Azure Cosmos.
 
* O Azure Cosmos DB mapeará dinamicamente a preocupação de leitura especificada pelo driver cliente do MongoDB para um dos níveis de consistência do Azure Cosmos DB configurados dinamicamente em uma solicitação de leitura.  

* Você pode anotar uma região específica associada à sua conta do Azure Cosmos como "Mestre" fazendo da região a primeira região gravável. 

A tabela a seguir ilustra como as preocupações nativas de gravação/leitura do MongoDB são mapeadas para os níveis de consistência do Azure Cosmos ao usar a API do Azure Cosmos DB para OMongoDB:

[![Mapeamento do modelo de consistência do MongoDB](./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png)](./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png#lightbox)

## <a name="next-steps"></a>Próximas etapas

Leia mais sobre os níveis de coerência e a compatibilidade entre as APIs do Azure Cosmos DB com as APIs de código aberto. Veja os artigos a seguir:

* [Equilíbrio entre disponibilidade e desempenho para vários níveis de coerência](consistency-levels-tradeoffs.md)
* [Recursos do MongoDB com suporte da API do MongoDB no Azure Cosmos DB](mongodb-feature-support.md)
* [Recursos do Apache Cassandra suportados pela API do Cassandra do Azure Cosmos DB](cassandra-support.md)
