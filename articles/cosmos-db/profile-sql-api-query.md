---
title: Obtenha métricas de execução & de desempenho de consulta SQL
description: Saiba como recuperar métricas de execução de consulta SQL e perfilar o desempenho da consulta SQL das solicitações do Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: girobins
ms.openlocfilehash: 48b9a67de5c870a187ee008bd97265760ca6c341
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "70998377"
---
# <a name="get-sql-query-execution-metrics-and-analyze-query-performance-using-net-sdk"></a>Obtenha métricas de execução de consulta SQL e analise o desempenho da consulta usando o .NET SDK

Este artigo apresenta como perfilar o desempenho da consulta SQL no Azure Cosmos DB. Este perfil pode ser `QueryMetrics` feito usando recuperado do .NET SDK e é detalhado aqui. [QueryMetrics](https://msdn.microsoft.com/library/microsoft.azure.documents.querymetrics.aspx) é um objeto fortemente digitado com informações sobre a execução da consulta backend. Essas métricas são documentadas com mais detalhes no artigo [Tune Query Performance.](https://docs.microsoft.com/azure/cosmos-db/documentdb-sql-query-metrics)

## <a name="set-the-feedoptions-parameter"></a>Defina o parâmetro FeedOptions

Todas as sobrecargas para [DocumentClient.CreateDocumentQuery](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentquery.aspx) tomam um parâmetro opcional [feedOptions.](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.aspx) Esta opção é o que permite que a execução da consulta seja ajustada e parametrizada. 

Para coletar as métricas de execução da consulta Sql, você deve definir `true`o parâmetro [PopulateQueryMetrics](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.populatequerymetrics.aspx#P:Microsoft.Azure.Documents.Client.FeedOptions.PopulateQueryMetrics) no [FeedOptions](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.aspx) para . A `PopulateQueryMetrics` definição de verdade fará `FeedResponse` com que `QueryMetrics`o testamento contenha o relevante . 

## <a name="get-query-metrics-with-asdocumentquery"></a>Obtenha métricas de consulta com AsDocumentQuery()
A amostra de código a seguir mostra como fazer métricas de recuperação ao usar o método [AsDocumentQuery(:](https://msdn.microsoft.com/library/microsoft.azure.documents.linq.documentqueryable.asdocumentquery.aspx)

```csharp
// Initialize this DocumentClient and Collection
DocumentClient documentClient = null;
DocumentCollection collection = null;

// Setting PopulateQueryMetrics to true in the FeedOptions
FeedOptions feedOptions = new FeedOptions
{
    PopulateQueryMetrics = true
};

string query = "SELECT TOP 5 * FROM c";
IDocumentQuery<dynamic> documentQuery = documentClient.CreateDocumentQuery(Collection.SelfLink, query, feedOptions).AsDocumentQuery();

while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    
    // This dictionary maps the partitionId to the QueryMetrics of that query
    IReadOnlyDictionary<string, QueryMetrics> partitionIdToQueryMetrics = feedResponse.QueryMetrics;
    
    // At this point you have QueryMetrics which you can serialize using .ToString()
    foreach (KeyValuePair<string, QueryMetrics> kvp in partitionIdToQueryMetrics)
    {
        string partitionId = kvp.Key;
        QueryMetrics queryMetrics = kvp.Value;
        
        // Do whatever logging you need
        DoSomeLoggingOfQueryMetrics(query, partitionId, queryMetrics);
    }
}
```
## <a name="aggregating-querymetrics"></a>Agregando QueryMetrics

Na seção anterior, observe que havia várias chamadas para o método [ExecuteNextAsync.](https://msdn.microsoft.com/library/azure/dn850294.aspx) Cada chamada retornou um `FeedResponse` objeto `QueryMetrics`que tem um dicionário de ; um para cada continuação da consulta. O exemplo a seguir `QueryMetrics` mostra como agregá-los usando LINQ:

```csharp
List<QueryMetrics> queryMetricsList = new List<QueryMetrics>();

while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    
    // This dictionary maps the partitionId to the QueryMetrics of that query
    IReadOnlyDictionary<string, QueryMetrics> partitionIdToQueryMetrics = feedResponse.QueryMetrics;
    queryMetricsList.AddRange(partitionIdToQueryMetrics.Values);
}

// Aggregate the QueryMetrics using the + operator overload of the QueryMetrics class.
QueryMetrics aggregatedQueryMetrics = queryMetricsList.Aggregate((curr, acc) => curr + acc);
Console.WriteLine(aggregatedQueryMetrics);
```

## <a name="grouping-query-metrics-by-partition-id"></a>Agrupamento de métricas de consulta por ID de partição

Você pode `QueryMetrics` agrupar o pelo ID de partição. O agrupamento por ID de partição permite ver se uma partição específica está causando problemas de desempenho quando comparada a outras. O exemplo a seguir `QueryMetrics` mostra como agrupar com linq:

```csharp
List<KeyValuePair<string, QueryMetrics>> partitionedQueryMetrics = new List<KeyValuePair<string, QueryMetrics>>();
while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    
    // This dictionary is maps the partitionId to the QueryMetrics of that query
    IReadOnlyDictionary<string, QueryMetrics> partitionIdToQueryMetrics = feedResponse.QueryMetrics;
    partitionedQueryMetrics.AddRange(partitionIdToQueryMetrics.ToList());
}

// Now we are able to group the query metrics by partitionId
IEnumerable<IGrouping<string, KeyValuePair<string, QueryMetrics>>> groupedByQueryMetrics = partitionedQueryMetrics
    .GroupBy(kvp => kvp.Key);

// If we wanted to we could even aggregate the groupedby QueryMetrics
foreach(IGrouping<string, KeyValuePair<string, QueryMetrics>> grouping in groupedByQueryMetrics)
{
    string partitionId = grouping.Key;
    QueryMetrics aggregatedQueryMetricsForPartition = grouping
        .Select(kvp => kvp.Value)
        .Aggregate((curr, acc) => curr + acc);
    DoSomeLoggingOfQueryMetrics(query, partitionId, aggregatedQueryMetricsForPartition);
}
```

## <a name="linq-on-documentquery"></a>LINQ em DocumentQuery

Você também pode `FeedResponse` obter a de uma `AsDocumentQuery()` consulta LINQ usando o método:

```csharp
IDocumentQuery<Document> linqQuery = client.CreateDocumentQuery(collection.SelfLink, feedOptions)
    .Take(1)
    .Where(document => document.Id == "42")
    .OrderBy(document => document.Timestamp)
    .AsDocumentQuery();
FeedResponse<Document> feedResponse = await linqQuery.ExecuteNextAsync<Document>();
IReadOnlyDictionary<string, QueryMetrics> queryMetrics = feedResponse.QueryMetrics;
```

## <a name="expensive-queries"></a>Consultas Dispendiosas

Você pode capturar as unidades de solicitação consumidas por cada consulta para investigar consultas ou consultas caras que consomem alto throughput. Você pode obter a taxa de solicitação `FeedResponse`usando a propriedade [RequestCharge](https://msdn.microsoft.com/library/azure/dn948712.aspx) em . Para saber mais sobre como obter a taxa de solicitação usando o portal Azure e diferentes SDKs, consulte o artigo [de cobrança da unidade de solicitação.](find-request-unit-charge.md)

```csharp
string query = "SELECT * FROM c";
IDocumentQuery<dynamic> documentQuery = documentClient.CreateDocumentQuery(Collection.SelfLink, query, feedOptions).AsDocumentQuery();

while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    double requestCharge = feedResponse.RequestCharge
    
    // Log the RequestCharge how ever you want.
    DoSomeLogging(requestCharge);
}
```

## <a name="get-the-query-execution-time"></a>Obtenha o tempo de execução da consulta

Ao calcular o tempo necessário para executar uma consulta do lado do cliente, certifique-se de que você inclua apenas o tempo para chamar o `ExecuteNextAsync` método e não outras partes da sua base de código. Apenas essas chamadas ajudam você a calcular quanto tempo a execução da consulta levou como mostrado no exemplo a seguir:

```csharp
string query = "SELECT * FROM c";
IDocumentQuery<dynamic> documentQuery = documentClient.CreateDocumentQuery(Collection.SelfLink, query, feedOptions).AsDocumentQuery();
Stopwatch queryExecutionTimeEndToEndTotal = new Stopwatch();
while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    queryExecutionTimeEndToEndTotal.Start();
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    queryExecutionTimeEndToEndTotal.Stop();
}

// Log the elapsed time
DoSomeLogging(queryExecutionTimeEndToEndTotal.Elapsed);
```

## <a name="scan-queries-commonly-slow-and-expensive"></a>Consultas de varredura (comumente lentas e caras)

Uma consulta de varredura refere-se a uma consulta que não foi atendida pelo índice, devido à qual, muitos documentos são carregados antes de retornar o conjunto de resultados.

Abaixo está um exemplo de uma consulta de varredura:

```sql
SELECT VALUE c.description 
FROM   c 
WHERE UPPER(c.description) = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

O filtro desta consulta usa a função do sistema UPPER, que não é servida a partir do índice. A execução desta consulta contra uma grande coleção produziu as seguintes métricas de consulta para a primeira continuação:

```
QueryMetrics

Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
Output Document Count                    :               7
Output Document Size                     :             510 bytes
Index Utilization                        :            0.00 %
Total Query Execution Time               :        4,500.34 milliseconds
  Query Preparation Times
    Query Compilation Time               :            0.09 milliseconds
    Logical Plan Build Time              :            0.05 milliseconds
    Physical Plan Build Time             :            0.04 milliseconds
    Query Optimization Time              :            0.01 milliseconds
  Index Lookup Time                      :            0.01 milliseconds
  Document Load Time                     :        4,177.66 milliseconds
  Runtime Execution Times
    Query Engine Times                   :          322.16 milliseconds
    System Function Execution Time       :           85.74 milliseconds
    User-defined Function Execution Time :            0.00 milliseconds
  Document Write Time                    :            0.01 milliseconds
Client Side Metrics
  Retry Count                            :               0
  Request Charge                         :        4,059.95 RUs
```

Observe os seguintes valores na saída das métricas de consulta:

```
Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
```

Essa consulta carregou 60.951 documentos, totalizando 399.998.938 bytes. Carregar estes muitos bytes resulta em alto custo ou solicitação de custo unitário. Também leva muito tempo para executar a consulta, o que é claro com o tempo total gasto de propriedade:

```
Total Query Execution Time               :        4,500.34 milliseconds
```

O que significa que a consulta levou 4,5 segundos para ser executada (e esta foi apenas uma continuação).

Para otimizar esta consulta de exemplo, evite o uso de UPPER no filtro. Em vez disso, quando os `c.description` documentos são criados ou atualizados, os valores devem ser inseridos em todos os caracteres maiúsculos. A consulta então se torna: 

```sql
SELECT VALUE c.description 
FROM   c 
WHERE c.description = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

Esta consulta agora pode ser atendida a partir do índice.

Para saber mais sobre o desempenho da consulta de ajuste, consulte o artigo [Tune Query Performance.](https://docs.microsoft.com/azure/cosmos-db/documentdb-sql-query-metrics)

## <a name="references"></a><a id="References"></a>Referências

- [Especificação do SQL no Azure Cosmos DB](https://go.microsoft.com/fwlink/p/?LinkID=510612)
- [ANSI SQL 2011](https://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
- [JSON](https://json.org/)
- [LINQ](/previous-versions/dotnet/articles/bb308959(v=msdn.10)) 

## <a name="next-steps"></a>Próximas etapas

- [Desempenho de consulta de ajuste](sql-api-query-metrics.md)
- [Visão geral de indexação](index-overview.md)
- [Amostras do .NET no Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
