---
title: Unidades de Solicitação e taxa de transferência no Azure Cosmos DB
description: Saiba mais sobre como especificar e estimar os requisitos da Unidade de Solicitação no Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: a0058bf309e0ff4fbe687731d676e907d1c3fd82
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383102"
---
# <a name="request-units-in-azure-cosmos-db"></a>Unidades de Solicitação no Azure Cosmos DB

Com o Azure Cosmos DB, você paga pela taxa de transferência provisionada e pelo armazenamento que consome por hora. Throughput must be provisioned to ensure that sufficient system resources are available for your Azure Cosmos database at all times. You need enough resources to meet or exceed the [Azure Cosmos DB SLAs](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/).

O Azure Cosmos DB dá suporte a muitas APIs como SQL, MongoDB, Cassandra, Gremlin e Table. Cada API tem seu próprio conjunto de operações de banco de dados. Essas operações variam de simples leituras e gravações de pontos a consultas complexas. Cada operação de banco de dados consome recursos do sistema com base na complexidade da operação. 

The cost of all database operations is normalized by Azure Cosmos DB and is expressed by *Request Units* (or RUs, for short). É possível pensar em RUs por segundo como a moeda para a taxa de transferência. RUs por segundo é uma moeda baseada em taxa. Elas extraem os recursos do sistema como CPU, IOPS e memória que são necessários para executar as operações do banco de dados com suporte no Azure Cosmos DB. 

The cost to read a 1 KB item is 1 Request Unit (or 1 RU). A minimum of 10 RU/s is required to store each 1 GB of data. All other database operations are similarly assigned a cost using RUs. Independentemente da API usada para interagir com o contêiner do Azure Cosmos DB, os custos sempre serão medidos por RUs. Independentemente da operação do banco de dados ser uma gravação, leitura ou consulta, os custos sempre serão medidos em RUs.

The following image shows the high-level idea of RUs:

![Operações do banco de dados consomem Unidades de Solicitação](./media/request-units/request-units.png)

Para gerenciar e planejar a capacidade, o Azure Cosmos DB garante que o número de RUs para uma determinada operação de banco de dados em um determinado conjunto de dados seja determinístico. You can examine the response header to track the number of RUs that are consumed by any database operation. When you understand the [factors that affect RU charges](request-units.md#request-unit-considerations) and your application's throughput requirements, you can run your application cost effectively.

Você provisiona o número de RUs para o aplicativo em uma base por segundo em incrementos de 100 RUs por segundo. Para dimensionar a taxa de transferência provisionada ao aplicativo, você poderá aumentar ou diminuir o número de RUs a qualquer momento. You can scale in increments or decrements of 100 RUs. É possível fazer as alterações programaticamente ou usando o portal do Azure. You are billed on an hourly basis.

Você pode provisionar a taxa de transferência em duas granularidades distintas: 

* **Containers**: For more information, see [Provision throughput on an Azure Cosmos container](how-to-provision-container-throughput.md).
* **Databases**: For more information, see [Provision throughput on an Azure Cosmos database](how-to-provision-database-throughput.md).

## <a name="request-unit-considerations"></a>Considerações sobre Unidade de Solicitação

Enquanto você estima o número de RUs por segundo para provisionar, considere os seguintes fatores:

* **Item size**: As the size of an item increases, the number of RUs consumed to read or write the item also increases.

* **Item indexing**: By default, each item is automatically indexed. Menos RUs serão consumidas se você optar por não indexar alguns dos itens em um contêiner.

* **Item property count**: Assuming the default indexing is on all properties, the number of RUs consumed to write an item increases as the item property count increases.

* **Indexed properties**: An index policy on each container determines which properties are indexed by default. Para reduzir o consumo de RU para operações de gravação, limite o número de propriedades indexadas.

* **Data consistency**: The strong and bounded staleness consistency levels consume approximately two times more RUs while performing read operations when compared to that of other relaxed consistency levels.

* **Query patterns**: The complexity of a query affects how many RUs are consumed for an operation. Fatores que afetam o custo das operações de consulta incluem: 
    
    - The number of query results
    - The number of predicates
    - The nature of the predicates
    - The number of user-defined functions
    - The size of the source data
    - The size of the result set
    - Projeções

  O Azure Cosmos DB garante que a mesma consulta nos mesmos dados sempre custa o mesmo número de RUs em execuções repetidas.

* **Script usage**: As with queries, stored procedures and triggers consume RUs based on the complexity of the operations that are performed. As you develop your application, inspect the [request charge header](optimize-cost-queries.md#evaluate-request-unit-charge-for-a-query) to better understand how much RU capacity each operation consumes.

## <a name="next-steps"></a>Próximos passos

* Saiba mais sobre como [provisionar taxa de transferência para contêineres e bancos de dados do Azure Cosmos DB](set-throughput.md).
* Saiba mais sobre [partições lógicas](partition-data.md).
* Saiba mais sobre como [dimensionar taxa de transferência provisionada globalmente](scaling-throughput.md).
* Saiba como [provisionar taxa de transferência em um contêiner do Azure Cosmos](how-to-provision-container-throughput.md).
* Saiba como [provisionar taxa de transferência em um banco de dados do Azure Cosmos DB](how-to-provision-database-throughput.md).
* Learn how to [find the request unit charge for an operation](find-request-unit-charge.md).
* Learn how to [optimize provisioned throughput cost in Azure Cosmos DB](optimize-cost-throughput.md).
* Learn how to [optimize reads and writes cost in Azure Cosmos DB](optimize-cost-reads-writes.md).
* Learn how to [optimize query cost in Azure Cosmos DB](optimize-cost-queries.md).
* Learn how to [use metrics to monitor throughput](use-metrics.md).
