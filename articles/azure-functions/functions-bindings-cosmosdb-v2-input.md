---
title: Vinculação de entrada Azure Cosmos DB para Funções 2.x
description: Aprenda a usar a vinculação de entrada Azure Cosmos DB em Funções Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2020
ms.author: cshoe
ms.openlocfilehash: eabcf40e28927919215979ccc46fa029d19adbfe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78943415"
---
# <a name="azure-cosmos-db-input-binding-for-azure-functions-2x"></a>Vinculação de entrada Azure Cosmos DB para funções azure 2.x

A associação de dados de entrada do Azure Cosmos DB usa a API de SQL para recuperar um ou mais documentos do Azure Cosmos DB e passá-los para o parâmetro de entrada da função. A ID do documento ou os parâmetros de consulta podem ser determinados com base no gatilho que invoca a função.

Para obter informações sobre detalhes de configuração e configuração, consulte a [visão geral](./functions-bindings-cosmosdb-v2.md).

> [!NOTE]
> Se a coleção for [particionada,](../cosmos-db/partition-data.md#logical-partitions)as operações de análise também precisarão especificar o valor da chave de partição.
>

<a id="example" name="example"></a>

# <a name="c"></a>[C #](#tab/csharp)

Esta seção contém os seguintes exemplos:

* [Gatilho da fila, pesquisar ID no JSON](#queue-trigger-look-up-id-from-json-c)
* [Gatilho HTTP, pesquisar ID na cadeia de caracteres de consulta](#http-trigger-look-up-id-from-query-string-c)
* [Gatilho HTTP, pesquisar ID nos dados da rota](#http-trigger-look-up-id-from-route-data-c)
* [Gatilho HTTP, pesquisar ID nos dados da rota, usando SqlQuery](#http-trigger-look-up-id-from-route-data-using-sqlquery-c)
* [Gatilho HTTP, obter vários documentos, usando SqlQuery](#http-trigger-get-multiple-docs-using-sqlquery-c)
* [Gatilho HTTP, obter vários documentos, usando DocumentClient](#http-trigger-get-multiple-docs-using-documentclient-c)

Os exemplos se referem a um tipo `ToDoItem` simples:

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string PartitionKey { get; set; }
        public string Description { get; set; }
    }
}
```

<a id="queue-trigger-look-up-id-from-json-c"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>Gatilho da fila, pesquisar ID no JSON 

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que recupera um único documento. A função é disparada por uma mensagem da fila que contém um objeto JSON. O gatilho de fila analisa o JSON `ToDoItemLookup`em um objeto de tipo, que contém o valor da chave de ID e partição para procurar. Esse valor de chave de id `ToDoItem` e partição são usados para recuperar um documento do banco de dados e coleta especificados.

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItemLookup
    {
        public string ToDoItemId { get; set; }

        public string ToDoItemPartitionKeyValue { get; set; }
    }
}
```

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromJSON
    {
        [FunctionName("DocByIdFromJSON")]
        public static void Run(
            [QueueTrigger("todoqueueforlookup")] ToDoItemLookup toDoItemLookup,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{ToDoItemId}",
                PartitionKey = "{ToDoItemPartitionKeyValue}")]ToDoItem toDoItem,
            ILogger log)
        {
            log.LogInformation($"C# Queue trigger function processed Id={toDoItemLookup?.ToDoItemId} Key={toDoItemLookup?.ToDoItemPartitionKeyValue}");

            if (toDoItem == null)
            {
                log.LogInformation($"ToDo item not found");
            }
            else
            {
                log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
            }
        }
    }
}
```

<a id="http-trigger-look-up-id-from-query-string-c"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>Gatilho HTTP, pesquisar ID na cadeia de caracteres de consulta

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que recupera um único documento. A função é acionada por uma solicitação HTTP que usa uma seqüência de consultas para especificar o valor da chave de id e partição para procurar. Esse valor de chave de id `ToDoItem` e partição são usados para recuperar um documento do banco de dados e coleta especificados.

>[!NOTE]
>O parâmetro de cadeia de caracteres de consulta HTTP diferencia maiusculas de minúsculas.
>

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromQueryString
    {
        [FunctionName("DocByIdFromQueryString")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
                HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{Query.id}",
                PartitionKey = "{Query.partitionKey}")] ToDoItem toDoItem,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            if (toDoItem == null)
            {
                log.LogInformation($"ToDo item not found");
            }
            else
            {
                log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
            }
            return new OkResult();
        }
    }
}
```

<a id="http-trigger-look-up-id-from-route-data-c"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>Gatilho HTTP, pesquisar ID nos dados da rota

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que recupera um único documento. A função é acionada por uma solicitação HTTP que usa dados de rota para especificar o valor da chave de ID e partição para procurar. Esse valor de chave de id `ToDoItem` e partição são usados para recuperar um documento do banco de dados e coleta especificados.

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromRouteData
    {
        [FunctionName("DocByIdFromRouteData")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post",
                Route = "todoitems/{partitionKey}/{id}")]HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{id}",
                PartitionKey = "{partitionKey}")] ToDoItem toDoItem,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            if (toDoItem == null)
            {
                log.LogInformation($"ToDo item not found");
            }
            else
            {
                log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
            }
            return new OkResult();
        }
    }
}
```

<a id="http-trigger-look-up-id-from-route-data-using-sqlquery-c"></a>

### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery"></a>Gatilho HTTP, pesquisar ID nos dados da rota, usando SqlQuery

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que recupera um único documento. A função é disparada por uma solicitação HTTP que usa os dados da rota para especificar a ID a pesquisar. Essa ID é usada para recuperar um documento `ToDoItem` no banco de dados e na coleção especificados.

O exemplo mostra como usar uma expressão de associação no parâmetro `SqlQuery`. Você pode passar os dados da rota para o parâmetro `SqlQuery` conforme mostrado, mas atualmente [não é possível passar os valores da cadeia de caracteres de consulta](https://github.com/Azure/azure-functions-host/issues/2554#issuecomment-392084583).

> [!NOTE]
> Se você precisar consultar apenas o ID, recomenda-se usar um look up, como os [exemplos anteriores,](#http-trigger-look-up-id-from-query-string-c)pois consumirá unidades de solicitação menos [solicitadas.](../cosmos-db/request-units.md) As operações de leitura de ponto (GET) são [mais eficientes](../cosmos-db/optimize-cost-queries.md) do que as consultas por ID.
>

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromRouteDataUsingSqlQuery
    {
        [FunctionName("DocByIdFromRouteDataUsingSqlQuery")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post",
                Route = "todoitems2/{id}")]HttpRequest req,
            [CosmosDB("ToDoItems", "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                SqlQuery = "select * from ToDoItems r where r.id = {id}")]
                IEnumerable<ToDoItem> toDoItems,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.LogInformation(toDoItem.Description);
            }
            return new OkResult();
        }
    }
}
```

<a id="http-trigger-get-multiple-docs-using-sqlquery-c"></a>

### <a name="http-trigger-get-multiple-docs-using-sqlquery"></a>Gatilho HTTP, obter vários documentos, usando SqlQuery

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que recupera uma lista de documentos. A função é disparada por uma solicitação HTTP. A consulta é especificada na propriedade do atributo `SqlQuery`.

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocsBySqlQuery
    {
        [FunctionName("DocsBySqlQuery")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
                HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                SqlQuery = "SELECT top 2 * FROM c order by c._ts desc")]
                IEnumerable<ToDoItem> toDoItems,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.LogInformation(toDoItem.Description);
            }
            return new OkResult();
        }
    }
}

```

<a id="http-trigger-get-multiple-docs-using-documentclient-c"></a>

### <a name="http-trigger-get-multiple-docs-using-documentclient"></a>Gatilho HTTP, obter vários documentos, usando DocumentClient

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que recupera uma lista de documentos. A função é disparada por uma solicitação HTTP. O código usa uma instância `DocumentClient` fornecida pela associação do Azure Cosmos DB para ler uma lista de documentos. A instância `DocumentClient` também pode ser usada para as operações de gravação.

> [!NOTE]
> Você também pode usar a interface [IDocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.idocumentclient?view=azure-dotnet) para facilitar o teste.

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.Documents.Client;
using Microsoft.Azure.Documents.Linq;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using System;
using System.Linq;
using System.Threading.Tasks;

namespace CosmosDBSamplesV2
{
    public static class DocsByUsingDocumentClient
    {
        [FunctionName("DocsByUsingDocumentClient")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post",
                Route = null)]HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")] DocumentClient client,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            var searchterm = req.Query["searchterm"];
            if (string.IsNullOrWhiteSpace(searchterm))
            {
                return (ActionResult)new NotFoundResult();
            }

            Uri collectionUri = UriFactory.CreateDocumentCollectionUri("ToDoItems", "Items");

            log.LogInformation($"Searching for: {searchterm}");

            IDocumentQuery<ToDoItem> query = client.CreateDocumentQuery<ToDoItem>(collectionUri)
                .Where(p => p.Description.Contains(searchterm))
                .AsDocumentQuery();

            while (query.HasMoreResults)
            {
                foreach (ToDoItem result in await query.ExecuteNextAsync())
                {
                    log.LogInformation(result.Description);
                }
            }
            return new OkResult();
        }
    }
}
```

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

Esta seção contém os seguintes exemplos:

* [Gatilho da fila, pesquisar ID na cadeia de caracteres](#queue-trigger-look-up-id-from-string-c-script)
* [Gatilho da fila, obter vários documentos, usando SqlQuery](#queue-trigger-get-multiple-docs-using-sqlquery-c-script)
* [Gatilho HTTP, pesquisar ID na cadeia de caracteres de consulta](#http-trigger-look-up-id-from-query-string-c-script)
* [Gatilho HTTP, pesquisar ID nos dados da rota](#http-trigger-look-up-id-from-route-data-c-script)
* [Gatilho HTTP, obter vários documentos, usando SqlQuery](#http-trigger-get-multiple-docs-using-sqlquery-c-script)
* [Gatilho HTTP, obter vários documentos, usando DocumentClient](#http-trigger-get-multiple-docs-using-documentclient-c-script)

Os exemplos de gatilho HTTP se referem a um tipo `ToDoItem` simples:

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

<a id="queue-trigger-look-up-id-from-string-c-script"></a>

### <a name="queue-trigger-look-up-id-from-string"></a>Gatilho da fila, pesquisar ID na cadeia de caracteres

O exemplo a seguir mostra uma associação de entrada do Cosmos DB em um arquivo *function.json* e uma [função script C#](functions-reference-csharp.md) que usa a associação. A função lê um documento único e atualiza o valor de texto do documento.

Aqui estão os dados de associação no arquivo *function.json*:

```json
{
    "name": "inputDocument",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger}",
    "partitionKey": "{partition key value}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "in"
}
```
A seção [configuração](#configuration) explica essas propriedades.

Aqui está o código de script do C#:

```cs
    using System;

    // Change input document contents using Azure Cosmos DB input binding
    public static void Run(string myQueueItem, dynamic inputDocument)
    {
      inputDocument.text = "This has changed.";
    }
```

<a id="queue-trigger-get-multiple-docs-using-sqlquery-c-script"></a>

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>Gatilho da fila, obter vários documentos, usando SqlQuery

O exemplo a seguir mostra uma associação de dados de entrada do Cosmos DB em um arquivo *function.json* e uma [função script C#](functions-reference-csharp.md) que usa a associação de dados. A função recupera vários documentos especificados por uma consulta SQL usando um gatilho de fila para personalizar os parâmetros de consulta.

O gatilho de consulta fornece um parâmetro `departmentId`. Uma mensagem de consulta de `{ "departmentId" : "Finance" }` retornará todos os registros ao departamento financeiro.

Aqui estão os dados de associação no arquivo *function.json*:

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connectionStringSetting": "CosmosDBConnection"
}
```

A seção [configuração](#configuration) explica essas propriedades.

Aqui está o código de script do C#:

```csharp
    public static void Run(QueuePayload myQueueItem, IEnumerable<dynamic> documents)
    {
        foreach (var doc in documents)
        {
            // operate on each document
        }
    }

    public class QueuePayload
    {
        public string departmentId { get; set; }
    }
```

<a id="http-trigger-look-up-id-from-query-string-c-script"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>Gatilho HTTP, pesquisar ID na cadeia de caracteres de consulta

O exemplo a seguir mostra uma [função de script C#](functions-reference-csharp.md) que recupera um único documento. A função é acionada por uma solicitação HTTP que usa uma seqüência de consultas para especificar o valor da chave de id e partição para procurar. Esse valor de chave de id `ToDoItem` e partição são usados para recuperar um documento do banco de dados e coleta especificados.

Aqui está o arquivo *function.json*:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}",
      "PartitionKey" : "{Query.partitionKeyValue}"
    }
  ],
  "disabled": false
}
```

Aqui está o código de script do C#:

```cs
using System.Net;
using Microsoft.Extensions.Logging;

public static HttpResponseMessage Run(HttpRequestMessage req, ToDoItem toDoItem, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    if (toDoItem == null)
    {
         log.LogInformation($"ToDo item not found");
    }
    else
    {
        log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a id="http-trigger-look-up-id-from-route-data-c-script"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>Gatilho HTTP, pesquisar ID nos dados da rota

O exemplo a seguir mostra uma [função de script C#](functions-reference-csharp.md) que recupera um único documento. A função é acionada por uma solicitação HTTP que usa dados de rota para especificar o valor da chave de ID e partição para procurar. Esse valor de chave de id `ToDoItem` e partição são usados para recuperar um documento do banco de dados e coleta especificados.

Aqui está o arquivo *function.json*:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ],
      "route":"todoitems/{partitionKeyValue}/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}",
      "PartitionKey": "{partitionKeyValue}"
    }
  ],
  "disabled": false
}
```

Aqui está o código de script do C#:

```cs
using System.Net;
using Microsoft.Extensions.Logging;

public static HttpResponseMessage Run(HttpRequestMessage req, ToDoItem toDoItem, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    if (toDoItem == null)
    {
         log.LogInformation($"ToDo item not found");
    }
    else
    {
        log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a id="http-trigger-get-multiple-docs-using-sqlquery-c-script"></a>

### <a name="http-trigger-get-multiple-docs-using-sqlquery"></a>Gatilho HTTP, obter vários documentos, usando SqlQuery

O exemplo a seguir mostra uma [função de script C#](functions-reference-csharp.md) que recupera uma lista de documentos. A função é disparada por uma solicitação HTTP. A consulta é especificada na propriedade do atributo `SqlQuery`.

Aqui está o arquivo *function.json*:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "sqlQuery": "SELECT top 2 * FROM c order by c._ts desc"
    }
  ],
  "disabled": false
}
```

Aqui está o código de script do C#:

```cs
using System.Net;
using Microsoft.Extensions.Logging;

public static HttpResponseMessage Run(HttpRequestMessage req, IEnumerable<ToDoItem> toDoItems, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    foreach (ToDoItem toDoItem in toDoItems)
    {
        log.LogInformation(toDoItem.Description);
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a id="http-trigger-get-multiple-docs-using-documentclient-c-script"></a>

### <a name="http-trigger-get-multiple-docs-using-documentclient"></a>Gatilho HTTP, obter vários documentos, usando DocumentClient

O exemplo a seguir mostra uma [função de script C#](functions-reference-csharp.md) que recupera uma lista de documentos. A função é disparada por uma solicitação HTTP. O código usa uma instância `DocumentClient` fornecida pela associação do Azure Cosmos DB para ler uma lista de documentos. A instância `DocumentClient` também pode ser usada para as operações de gravação.

Aqui está o arquivo *function.json*:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "client",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "inout"
    }
  ],
  "disabled": false
}
```

Aqui está o código de script do C#:

```cs
#r "Microsoft.Azure.Documents.Client"

using System.Net;
using Microsoft.Azure.Documents.Client;
using Microsoft.Azure.Documents.Linq;
using Microsoft.Extensions.Logging;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, DocumentClient client, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    Uri collectionUri = UriFactory.CreateDocumentCollectionUri("ToDoItems", "Items");
    string searchterm = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "searchterm", true) == 0)
        .Value;

    if (searchterm == null)
    {
        return req.CreateResponse(HttpStatusCode.NotFound);
    }

    log.LogInformation($"Searching for word: {searchterm} using Uri: {collectionUri.ToString()}");
    IDocumentQuery<ToDoItem> query = client.CreateDocumentQuery<ToDoItem>(collectionUri)
        .Where(p => p.Description.Contains(searchterm))
        .AsDocumentQuery();

    while (query.HasMoreResults)
    {
        foreach (ToDoItem result in await query.ExecuteNextAsync())
        {
            log.LogInformation(result.Description);
        }
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

Esta seção contém os seguintes exemplos que leem um documento especificando um valor de ID de várias fontes:

* [Gatilho da fila, pesquisar ID no JSON](#queue-trigger-look-up-id-from-json-javascript)
* [Gatilho HTTP, pesquisar ID na cadeia de caracteres de consulta](#http-trigger-look-up-id-from-query-string-javascript)
* [Gatilho HTTP, pesquisar ID nos dados da rota](#http-trigger-look-up-id-from-route-data-javascript)
* [Gatilho da fila, obter vários documentos, usando SqlQuery](#queue-trigger-get-multiple-docs-using-sqlquery-javascript)

<a id="queue-trigger-look-up-id-from-json-javascript"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>Gatilho da fila, pesquisar ID no JSON

O exemplo a seguir mostra uma associação de entrada do Cosmos DB em um arquivo *function.json* e uma [função JavaScript](functions-reference-node.md) que usa a associação. A função lê um documento único e atualiza o valor de texto do documento.

Aqui estão os dados de associação no arquivo *function.json*:

```json
{
    "name": "inputDocumentIn",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger_payload_property}",
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "in"
},
{
    "name": "inputDocumentOut",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": false,
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

A seção [configuração](#configuration) explica essas propriedades.

Aqui está o código JavaScript:

```javascript
    // Change input document contents using Azure Cosmos DB input binding, using context.bindings.inputDocumentOut
    module.exports = function (context) {
    context.bindings.inputDocumentOut = context.bindings.inputDocumentIn;
    context.bindings.inputDocumentOut.text = "This was updated!";
    context.done();
    };
```

<a id="http-trigger-look-up-id-from-query-string-javascript"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>Gatilho HTTP, pesquisar ID na cadeia de caracteres de consulta

O exemplo a seguir mostra uma [função de script JavaScript](functions-reference-node.md) que recupera um único documento. A função é acionada por uma solicitação HTTP que usa uma seqüência de consultas para especificar o valor da chave de id e partição para procurar. Esse valor de chave de id `ToDoItem` e partição são usados para recuperar um documento do banco de dados e coleta especificados.

Aqui está o arquivo *function.json*:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}",
      "PartitionKey": "{Query.partitionKeyValue}"
    }
  ],
  "disabled": false
}
```

Aqui está o código JavaScript:

```javascript
module.exports = function (context, req, toDoItem) {
    context.log('JavaScript queue trigger function processed work item');
    if (!toDoItem)
    {
        context.log("ToDo item not found");
    }
    else
    {
        context.log("Found ToDo item, Description=" + toDoItem.Description);
    }

    context.done();
};
```

<a id="http-trigger-look-up-id-from-route-data-javascript"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>Gatilho HTTP, pesquisar ID nos dados da rota

O exemplo a seguir mostra uma [função de script JavaScript](functions-reference-node.md) que recupera um único documento. A função é acionada por uma solicitação HTTP que usa dados de rota para especificar o valor da chave de ID e partição para procurar. Esse valor de chave de id `ToDoItem` e partição são usados para recuperar um documento do banco de dados e coleta especificados.

Aqui está o arquivo *function.json*:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ],
      "route":"todoitems/{partitionKeyValue}/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}",
      "PartitionKey": "{partitionKeyValue}"
    }
  ],
  "disabled": false
}
```

Aqui está o código JavaScript:

```javascript
module.exports = function (context, req, toDoItem) {
    context.log('JavaScript queue trigger function processed work item');
    if (!toDoItem)
    {
        context.log("ToDo item not found");
    }
    else
    {
        context.log("Found ToDo item, Description=" + toDoItem.Description);
    }

    context.done();
};
```

<a id="queue-trigger-get-multiple-docs-using-sqlquery-javascript"></a>

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>Gatilho da fila, obter vários documentos, usando SqlQuery

O exemplo a seguir mostra uma associação de dados de entrada do Cosmos DB em um arquivo *function.json* e uma [função script C#](functions-reference-node.md) que usa a associação de dados. A função recupera vários documentos especificados por uma consulta SQL usando um gatilho de fila para personalizar os parâmetros de consulta.

O gatilho de consulta fornece um parâmetro `departmentId`. Uma mensagem de consulta de `{ "departmentId" : "Finance" }` retornará todos os registros ao departamento financeiro.

Aqui estão os dados de associação no arquivo *function.json*:

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connectionStringSetting": "CosmosDBConnection"
}
```

A seção [configuração](#configuration) explica essas propriedades.

Aqui está o código JavaScript:

```javascript
    module.exports = function (context, input) {
        var documents = context.bindings.documents;
        for (var i = 0; i < documents.length; i++) {
            var document = documents[i];
            // operate on each document
        }
        context.done();
    };
```

# <a name="python"></a>[Python](#tab/python)

Esta seção contém os seguintes exemplos que leem um documento especificando um valor de ID de várias fontes:

* [Gatilho da fila, pesquisar ID no JSON](#queue-trigger-look-up-id-from-json-python)
* [Gatilho HTTP, pesquisar ID na cadeia de caracteres de consulta](#http-trigger-look-up-id-from-query-string-python)
* [Gatilho HTTP, pesquisar ID nos dados da rota](#http-trigger-look-up-id-from-route-data-python)
* [Gatilho da fila, obter vários documentos, usando SqlQuery](#queue-trigger-get-multiple-docs-using-sqlquery-python)

<a id="queue-trigger-look-up-id-from-json-python"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>Gatilho da fila, pesquisar ID no JSON

O exemplo a seguir mostra uma associação de entrada do Cosmos DB em um arquivo *function.json* e uma [função Python](functions-reference-python.md) que usa a associação de dados. A função lê um documento único e atualiza o valor de texto do documento.

Aqui estão os dados de associação no arquivo *function.json*:

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger_payload_property}",
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "in"
},
{
    "name": "$return",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": false,
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

A seção [configuração](#configuration) explica essas propriedades.

Aqui está o código Python:

```python
import azure.functions as func


def main(queuemsg: func.QueueMessage, documents: func.DocumentList) -> func.Document:
    if documents:
        document = documents[0]
        document['text'] = 'This was updated!'
        return document
```

<a id="http-trigger-look-up-id-from-query-string-python"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>Gatilho HTTP, pesquisar ID na cadeia de caracteres de consulta

O exemplo a seguir mostra uma [função Python](functions-reference-python.md) que recupera um único documento. A função é acionada por uma solicitação HTTP que usa uma seqüência de consultas para especificar o valor da chave de id e partição para procurar. Esse valor de chave de id `ToDoItem` e partição são usados para recuperar um documento do banco de dados e coleta especificados.

Aqui está o arquivo *function.json*:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "todoitems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}",
      "PartitionKey": "{Query.partitionKeyValue}"
    }
  ],
  "disabled": true,
  "scriptFile": "__init__.py"
}
```

Aqui está o código Python:

```python
import logging
import azure.functions as func


def main(req: func.HttpRequest, todoitems: func.DocumentList) -> str:
    if not todoitems:
        logging.warning("ToDo item not found")
    else:
        logging.info("Found ToDo item, Description=%s",
                     todoitems[0]['description'])

    return 'OK'
```

<a id="http-trigger-look-up-id-from-route-data-python"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>Gatilho HTTP, pesquisar ID nos dados da rota

O exemplo a seguir mostra uma [função Python](functions-reference-python.md) que recupera um único documento. A função é acionada por uma solicitação HTTP que usa dados de rota para especificar o valor da chave de ID e partição para procurar. Esse valor de chave de id `ToDoItem` e partição são usados para recuperar um documento do banco de dados e coleta especificados.

Aqui está o arquivo *function.json*:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ],
      "route":"todoitems/{partitionKeyValue}/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "todoitems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}",
      "PartitionKey": "{partitionKeyValue}"
    }
  ],
  "disabled": false,
  "scriptFile": "__init__.py"
}
```

Aqui está o código Python:

```python
import logging
import azure.functions as func


def main(req: func.HttpRequest, todoitems: func.DocumentList) -> str:
    if not todoitems:
        logging.warning("ToDo item not found")
    else:
        logging.info("Found ToDo item, Description=%s",
                     todoitems[0]['description'])
    return 'OK'
```

<a id="queue-trigger-get-multiple-docs-using-sqlquery-python"></a>

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>Gatilho da fila, obter vários documentos, usando SqlQuery

O exemplo a seguir mostra uma associação de dados de entrada do Azure Cosmos DB em um arquivo *function.json* e uma [função Python](functions-reference-python.md) que usa a associação de dados. A função recupera vários documentos especificados por uma consulta SQL usando um gatilho de fila para personalizar os parâmetros de consulta.

O gatilho de consulta fornece um parâmetro `departmentId`. Uma mensagem de consulta de `{ "departmentId" : "Finance" }` retornará todos os registros ao departamento financeiro.

Aqui estão os dados de associação no arquivo *function.json*:

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connectionStringSetting": "CosmosDBConnection"
}
```

A seção [configuração](#configuration) explica essas propriedades.

Aqui está o código Python:

```python
import azure.functions as func

def main(queuemsg: func.QueueMessage, documents: func.DocumentList):
    for document in documents:
        # operate on each document
```

# <a name="java"></a>[Java](#tab/java)

Esta seção contém os seguintes exemplos:

* [Gatilho HTTP, pesquisar ID na cadeia de caracteres de consulta - parâmetro cadeia de caracteres](#http-trigger-look-up-id-from-query-string---string-parameter-java)
* [Gatilho HTTP, pesquisar ID na cadeia de caracteres de consulta - parâmetro POJO](#http-trigger-look-up-id-from-query-string---pojo-parameter-java)
* [Gatilho HTTP, pesquisar ID nos dados da rota](#http-trigger-look-up-id-from-route-data-java)
* [Gatilho HTTP, pesquisar ID nos dados da rota, usando SqlQuery](#http-trigger-look-up-id-from-route-data-using-sqlquery-java)
* [Gatilho HTTP, obter vários documentos dos dados da rota, usando SqlQuery](#http-trigger-get-multiple-docs-from-route-data-using-sqlquery-java)

Os exemplos se referem a um tipo `ToDoItem` simples:

```java
public class ToDoItem {

  private String id;
  private String description;

  public String getId() {
    return id;
  }

  public String getDescription() {
    return description;
  }

  @Override
  public String toString() {
    return "ToDoItem={id=" + id + ",description=" + description + "}";
  }
}
```

<a id="http-trigger-look-up-id-from-query-string---string-parameter-java"></a>

### <a name="http-trigger-look-up-id-from-query-string---string-parameter"></a>Gatilho HTTP, pesquisar ID na cadeia de caracteres de consulta - parâmetro cadeia de caracteres

O exemplo a seguir mostra uma função Java que recupera um único documento. A função é acionada por uma solicitação HTTP que usa uma seqüência de consultas para especificar o valor da chave de id e partição para procurar. Esse valor de chave de id e partição são usados para recuperar um documento do banco de dados e coleta especificados, no formulário String.

```java
public class DocByIdFromQueryString {

    @FunctionName("DocByIdFromQueryString")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              id = "{Query.id}",
              partitionKey = "{Query.partitionKeyValue}",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            Optional<String> item,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("String from the database is " + (item.isPresent() ? item.get() : null));

        // Convert and display
        if (!item.isPresent()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        }
        else {
            // return JSON from Cosmos. Alternatively, we can parse the JSON string
            // and return an enriched JSON object.
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item.get())
                          .build();
        }
    }
}
 ```

Na biblioteca de runtime de funções [Java](/java/api/overview/azure/functions/runtime), use a anotação `@CosmosDBInput` em parâmetros de função cujo valor seria proveniente do Cosmos DB.  Essa anotação pode ser usada com tipos nativos do Java, POJOs ou valores que permitem valor nulos usando `Optional<T>`.

<a id="http-trigger-look-up-id-from-query-string---pojo-parameter-java"></a>

### <a name="http-trigger-look-up-id-from-query-string---pojo-parameter"></a>Gatilho HTTP, pesquisar ID na cadeia de caracteres de consulta - parâmetro POJO

O exemplo a seguir mostra uma função Java que recupera um único documento. A função é acionada por uma solicitação HTTP que usa uma seqüência de consultas para especificar o valor da chave de id e partição para procurar. Esse valor de chave de identificação e partição usado para recuperar um documento do banco de dados e coleta especificados. O documento é então convertido em uma instância de ```ToDoItem``` POJO criado anteriormente e passada como um argumento para a função.

```java
public class DocByIdFromQueryStringPojo {

    @FunctionName("DocByIdFromQueryStringPojo")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              id = "{Query.id}",
              partitionKey = "{Query.partitionKeyValue}",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            ToDoItem item,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("Item from the database is " + item);

        // Convert and display
        if (item == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        }
        else {
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item)
                          .build();
        }
    }
}
 ```

<a id="http-trigger-look-up-id-from-route-data-java"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>Gatilho HTTP, pesquisar ID nos dados da rota

O exemplo a seguir mostra uma função Java que recupera um único documento. A função é acionada por uma solicitação HTTP que usa um parâmetro de rota para especificar o valor da chave de id e partição para procurar. Esse valor de chave de identificação e partição é usado para recuperar um ```Optional<String>```documento do banco de dados e coleta especificados, devolvendo-o como um .

```java
public class DocByIdFromRoute {

    @FunctionName("DocByIdFromRoute")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS,
              route = "todoitems/{partitionKeyValue}/{id}")
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              id = "{id}",
              partitionKey = "{partitionKeyValue}",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            Optional<String> item,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("String from the database is " + (item.isPresent() ? item.get() : null));

        // Convert and display
        if (!item.isPresent()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        }
        else {
            // return JSON from Cosmos. Alternatively, we can parse the JSON string
            // and return an enriched JSON object.
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item.get())
                          .build();
        }
    }
}
 ```

 <a id="http-trigger-look-up-id-from-route-data-using-sqlquery-java"></a>

### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery"></a>Gatilho HTTP, pesquisar ID nos dados da rota, usando SqlQuery

O exemplo a seguir mostra uma função Java que recupera um único documento. A função é acionada por uma solicitação HTTP que usa um parâmetro de rota para especificar o ID para procurar. Essa ID é usada para recuperar um documento da coleção e banco de dados especificado, converter o resultado definido como um ```ToDoItem[]```, já que muitos documentos podem ser retornados, dependendo dos critérios de consulta.

> [!NOTE]
> Se você precisar consultar apenas o ID, recomenda-se usar um look up, como os [exemplos anteriores,](#http-trigger-look-up-id-from-query-string---pojo-parameter-java)pois consumirá unidades de solicitação menos [solicitadas.](../cosmos-db/request-units.md) As operações de leitura de ponto (GET) são [mais eficientes](../cosmos-db/optimize-cost-queries.md) do que as consultas por ID.
>

```java
public class DocByIdFromRouteSqlQuery {

    @FunctionName("DocByIdFromRouteSqlQuery")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS,
              route = "todoitems2/{id}")
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              sqlQuery = "select * from Items r where r.id = {id}",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            ToDoItem[] item,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("Items from the database are " + item);

        // Convert and display
        if (item == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        }
        else {
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item)
                          .build();
        }
    }
}
 ```

 <a id="http-trigger-get-multiple-docs-from-route-data-using-sqlquery-java"></a>

### <a name="http-trigger-get-multiple-docs-from-route-data-using-sqlquery"></a>Gatilho HTTP, obter vários documentos dos dados da rota, usando SqlQuery

O exemplo a seguir mostra uma função Java que recupera vários documentos. A função é acionada por uma solicitação HTTP ```desc``` que usa um parâmetro ```description``` de rota para especificar a string a ser pesquisada no campo. O termo de pesquisa é usado para recuperar uma coleção de documentos do banco de dados e coleção especificados, convertendo o conjunto de resultados para um ```ToDoItem[]``` e passando-o como um argumento para a função.

```java
public class DocsFromRouteSqlQuery {

    @FunctionName("DocsFromRouteSqlQuery")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET},
              authLevel = AuthorizationLevel.ANONYMOUS,
              route = "todoitems3/{desc}")
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              sqlQuery = "select * from Items r where contains(r.description, {desc})",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            ToDoItem[] items,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("Number of items from the database is " + (items == null ? 0 : items.length));

        // Convert and display
        if (items == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("No documents found.")
                          .build();
        }
        else {
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(items)
                          .build();
        }
    }
}
 ```

 ---

## <a name="attributes-and-annotations"></a>Atributos e anotações

# <a name="c"></a>[C #](#tab/csharp)

Em [Bibliotecas de classes C#](functions-dotnet-class-library.md), utilize o atributo [CosmosDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs).

O construtor do atributo toma o nome do banco de dados e o nome da coleção. Para obter informações sobre essas configurações e outras propriedades que podem ser configuradas, consulte [a seção de configuração a seguir](#configuration).

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

Os atributos não são suportados pelo script C#.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Os atributos não são suportados pelo JavaScript.

# <a name="python"></a>[Python](#tab/python)

Os atributos não são suportados pelo Python.

# <a name="java"></a>[Java](#tab/java)

A partir da biblioteca de tempo `@CosmosDBOutput` de [execução de funções Java,](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)use a anotação nos parâmetros que escrevem para Cosmos DB. O tipo de parâmetro de `OutputBinding<T>`anotação deve ser , onde `T` é um tipo Java nativo ou um POJO.

---

## <a name="configuration"></a>Configuração

A tabela a seguir explica as propriedades de configuração de `CosmosDB` vinculação que você definiu no arquivo *function.json* e no atributo.

|Propriedade function.json | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**type**     | n/d | Deve ser definido como `cosmosDB`.        |
|**direction**     | n/d | Deve ser definido como `in`.         |
|**name**     | n/d | Nome do parâmetro de associação que representa o documento na função.  |
|**databaseName** |**Databasename** |O banco de dados que contém o documento.        |
|**Nomedacoleta** |**CollectionName** | O nome da coleção que contém o documento. |
|**id**    | **Id** | A ID do documento a ser recuperado. Essa propriedade dá suporte a [expressões de associação](./functions-bindings-expressions-patterns.md). Não defina as `id` propriedades e **sqlQuery.** Se você não definir uma ou outra, toda a coleção é recuperada. |
|**sqlQuery**  |**SqlQuery**  | Uma consulta SQL do Azure Cosmos DB usada para recuperar vários documentos. A propriedade dá suporte a associações em tempo de execução, como neste exemplo: `SELECT * FROM c where c.departmentId = {departmentId}`. Não defina as `id` `sqlQuery` propriedades e as propriedades. Se você não definir uma ou outra, toda a coleção é recuperada.|
|**conexãoConfiguração de string**     |**ConnectionStringSetting**|O nome da configuração do aplicativo que contém a cadeia de conexão do Azure Cosmos DB. |
|**Partitionkey**|**PartitionKey**|Especifica o valor da chave de partição para a pesquisa. Pode incluir parâmetros de associação. É necessário para maquinações em coleções [particionadas.](../cosmos-db/partition-data.md#logical-partitions)|
|**preferredLocalizaçãos**| **PreferredLocalizaçãos**| (Opcional) Define locais preferenciais (regiões) para contas de banco de dados replicadas geo-replicadas no serviço Azure Cosmos DB. Os valores devem ser separados por comma. Por exemplo, "Leste dos EUA, Centro-Sul dos EUA, Norte da Europa". |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Uso

# <a name="c"></a>[C #](#tab/csharp)

Quando a função sai com sucesso, quaisquer alterações feitas no documento de entrada através de parâmetros de entrada nomeados são automaticamente persistidas.

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

Quando a função sai com sucesso, quaisquer alterações feitas no documento de entrada através de parâmetros de entrada nomeados são automaticamente persistidas.

# <a name="javascript"></a>[Javascript](#tab/javascript)

As atualizações não são feitas automaticamente na saída da função. Em vez disso, use `context.bindings.<documentName>In` e `context.bindings.<documentName>Out` para fazer atualizações. Confira o exemplo de JavaScript.

# <a name="python"></a>[Python](#tab/python)

Os dados são disponibilizados `DocumentList` para a função através de um parâmetro. As alterações feitas no documento não são automaticamente persistidas.

# <a name="java"></a>[Java](#tab/java)

A partir da biblioteca de [@CosmosDBInput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.cosmosdbinput) tempo de [execução de funções Java,](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)a anotação expõe os dados do Cosmos DB à função. Essa anotação pode ser usada com tipos nativos do Java, POJOs ou valores que permitem valor nulos usando `Optional<T>`.

---

## <a name="next-steps"></a>Próximas etapas

- [Execute uma função quando um documento Azure Cosmos DB for criado ou modificado (Trigger)](./functions-bindings-cosmosdb-v2-trigger.md)
- [Salvar alterações em um documento DB do Azure Cosmos (vinculação de saída)](./functions-bindings-cosmosdb-v2-output.md)