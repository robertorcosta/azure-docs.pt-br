---
title: Consultar contêineres no Azure Cosmos DB
description: Saiba como consultar contêineres em Azure Cosmos DB usando consultas de partição cruzada na partição, entre partições e parciais
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 88ebb8bb80ec3406c98b77db481994d415b04373
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74872019"
---
# <a name="query-an-azure-cosmos-container"></a>Consultar um contêiner do Azure Cosmos

Este artigo explica como consultar um contêiner (coleção, grafo ou tabela) no Azure Cosmos DB.

## <a name="in-partition-query"></a>Consulta na partição

Quando você consulta dados de contêineres, se a consulta tiver um filtro de chave de partição especificado, o Azure Cosmos DB lidará com a consulta automaticamente. Ele encaminha a consulta para as partições correspondentes aos valores de chave de partição especificados no filtro. Por exemplo, a consulta a seguir é roteada para a partição `DeviceId`, que contém todos os documentos correspondentes ao valor de chave de partição `XMS-0001`.

```csharp
// Query using partition key into a class called, DeviceReading
IQueryable<DeviceReading> query = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"))
    .Where(m => m.MetricType == "Temperature" && m.DeviceId == "XMS-0001");
```

## <a name="cross-partition-query"></a>Consulta entre partições

A consulta a seguir não tem um filtro na chave de partição (`DeviceId`) e é distribuída para todas as partições em que ela é executada no índice da partição. Para executar uma consulta entre partições, defina `EnableCrossPartitionQuery` como true (ou `x-ms-documentdb-query-enablecrosspartition` na API REST).

A propriedade EnablecrossPartitionQuery aceita um valor booliano. Quando ela for definida como verdadeiro e se a consulta não tiver uma chave de partição, o Azure Cosmos DB realizará o fan-out da consulta entre as partições. O fan-out é realizado pela emissão de consultas individuais para todas as partições. Para ler os resultados da consulta, os aplicativos cliente devem consumir os resultados da FeedResponse e verificar a propriedade ContinuationToken. Para ler todos os resultados, mantenha a iteração nos dados até que o ContinuationToken seja nulo. 

```csharp
// Query across partition keys into a class called, DeviceReading
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"),
    new FeedOptions { EnableCrossPartitionQuery = true })
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100);
```

O Azure Cosmos DB dá suporte a funções de agregação COUNT, MIN, MAX e AVG em contêineres usando o SQL. As funções de agregação de contêineres começa na versão 1.12.0 do SDK e posteriores. As consultas devem incluir um único operador de agregação e um único valor na projeção.

## <a name="parallel-cross-partition-query"></a>Consulta entre partições em paralelo

Os SDKs do Azure Cosmos DB 1.9.0 e versões superiores dão suporte a opções de execução de consultas paralelas. Consultas entre partições em paralelo permitem que você execute consultas entre partições de baixa latência. Por exemplo, a consulta a seguir é configurada para ser executada paralelamente entre partições.

```csharp
// Cross-partition Order By Query with parallel execution
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"),  
    new FeedOptions { EnableCrossPartitionQuery = true, MaxDegreeOfParallelism = 10, MaxBufferedItemCount = 100})
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100)
    .OrderBy(m => m.MetricValue);
```

Você pode gerenciar a execução de consulta paralela ajustando os seguintes parâmetros:

- **MaxDegreeOfParallelism**: define o número máximo de conexões de rede simultâneas com as partições do contêiner. Se você definir essa propriedade como -1, o SDK gerenciará o grau de paralelismo. Se `MaxDegreeOfParallelism` não estiver especificado ou definido como 0, que é o valor padrão, haverá uma conexão de rede individual com as partições do contêiner.

- **MaxBufferedItemCount**: negocia a latência da consulta em comparação com a utilização de memória do lado do cliente. Se a opção for omitida ou definida como -1, o SDK gerenciará o número de itens no buffer durante a execução de consultas paralelas.

Com o mesmo estado da coleção, uma consulta paralela retorna resultados na mesma ordem de uma execução serial. Ao executar uma consulta entre partições que inclui operadores de classificação (ORDER BY, TOP), o SDK do Azure Cosmos DB emite a consulta paralelamente entre partições. Ela mescla parcialmente os resultados classificados no lado do cliente para produzir resultados ordenados globalmente.

## <a name="next-steps"></a>Próximos passos

Consulte os seguintes artigos para saber mais sobre o particionamento no Azure Cosmos DB:

- [Particionamento no Azure Cosmos DB](partitioning-overview.md)
- [Chaves de partição sintética no Azure Cosmos DB](synthetic-partition-keys.md)
