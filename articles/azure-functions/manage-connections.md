---
title: Manage connections in Azure Functions
description: Saiba como evitar problemas de desempenho no Azure Functions usando clientes de conexão estática.
ms.topic: conceptual
ms.date: 02/25/2018
ms.openlocfilehash: 872ad9a1b8f0a7da6fe410e68f08469ac11045a5
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226492"
---
# <a name="manage-connections-in-azure-functions"></a>Manage connections in Azure Functions

Functions in a function app share resources. Among those shared resources are connections: HTTP connections, database connections, and connections to services such as Azure Storage. Quando muitas funções estão em execução simultaneamente, é possível ficar sem conexões disponíveis. This article explains how to code your functions to avoid using more connections than they need.

## <a name="connection-limit"></a>Connection limit

The number of available connections is limited partly because a function app runs in a [sandbox environment](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox). One of the restrictions that the sandbox imposes on your code is a limit on the number of outbound connections, which is currently 600 active (1,200 total) connections per instance. When you reach this limit, the functions runtime writes the following message to the logs: `Host thresholds exceeded: Connections`. For more information, see the [Functions service limits](functions-scale.md#service-limits).

This limit is per instance. When the [scale controller adds function app instances](functions-scale.md#how-the-consumption-and-premium-plans-work) to handle more requests, each instance has an independent connection limit. That means there's no global connection limit, and you can have much more than 600 active connections across all active instances.

When troubleshooting, make sure that you have enabled Application Insights for your function app. Application Insights lets you view metrics for your function apps like executions. For more information, see [View telemetry in Application Insights](functions-monitoring.md#view-telemetry-in-application-insights).  

## <a name="static-clients"></a>Static clients

Para evitar manter mais conexões que o necessário, reutilize as instâncias do cliente em vez de criar novas com cada invocação de função. We recommend reusing client connections for any language that you might write your function in. For example, .NET clients like the [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx), [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
), and Azure Storage clients can manage connections if you use a single, static client.

Here are some guidelines to follow when you're using a service-specific client in an Azure Functions application:

- *Do not* create a new client with every function invocation.
- *Do* create a single, static client that every function invocation can use.
- *Consider* creating a single, static client in a shared helper class if different functions use the same service.

## <a name="client-code-examples"></a>Exemplos de código do cliente

Esta seção demonstra as melhores práticas para criar e usar clientes de seu código de função.

### <a name="httpclient-example-c"></a>Exemplo do HttpClient (C#)

Here's an example of C# function code that creates a static [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) instance:

```cs
// Create a single, static HttpClient
private static HttpClient httpClient = new HttpClient();

public static async Task Run(string input)
{
    var response = await httpClient.GetAsync("https://example.com");
    // Rest of function
}
```

A common question about [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) in .NET is "Should I dispose of my client?" In general, you dispose of objects that implement `IDisposable` when you're done using them. But you don't dispose of a static client because you aren't done using it when the function ends. Você quer que o cliente estático permaneça durante a duração do aplicativo.

### <a name="http-agent-examples-javascript"></a>HTTP agent examples (JavaScript)

Como ele fornece melhores opções de gerenciamento de conexão, você deve usar a classe[`http.agent`](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_class_http_agent) nativa em vez de métodos não nativos, como o módulo`node-fetch`. Connection parameters are configured through options on the `http.agent` class. For detailed options available with the HTTP agent, see [new Agent(\[options\])](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_new_agent_options).

The global `http.globalAgent` class used by `http.request()` has all of these values set to their respective defaults. A maneira recomendada de configurar limites de conexão em Funções é definir um número máximo globalmente. O exemplo a seguir define o número máximo de soquetes para o aplicativo de função:

```js
http.globalAgent.maxSockets = 200;
```

 The following example creates a new HTTP request with a custom HTTP agent only for that request:

```js
var http = require('http');
var httpAgent = new http.Agent();
httpAgent.maxSockets = 200;
options.agent = httpAgent;
http.request(options, onResponseCallback);
```

### <a name="documentclient-code-example-c"></a>Exemplo de código DocumentClient (C#)

O [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
) conecta uma instância do Azure Cosmos DB. A documentação do Azure Cosmos DB recomenda que você [use um cliente singleton do Azure Cosmos DB pelo tempo de vida do aplicativo](https://docs.microsoft.com/azure/cosmos-db/performance-tips#sdk-usage). O exemplo a seguir mostra um padrão para fazer isso em uma função:

```cs
#r "Microsoft.Azure.Documents.Client"
using Microsoft.Azure.Documents.Client;

private static Lazy<DocumentClient> lazyClient = new Lazy<DocumentClient>(InitializeDocumentClient);
private static DocumentClient documentClient => lazyClient.Value;

private static DocumentClient InitializeDocumentClient()
{
    // Perform any initialization here
    var uri = new Uri("example");
    var authKey = "authKey";
    
    return new DocumentClient(uri, authKey);
}

public static async Task Run(string input)
{
    Uri collectionUri = UriFactory.CreateDocumentCollectionUri("database", "collection");
    object document = new { Data = "example" };
    await documentClient.UpsertDocumentAsync(collectionUri, document);
    
    // Rest of function
}
```

### <a name="cosmosclient-code-example-javascript"></a>CosmosClient code example (JavaScript)
[CosmosClient](/javascript/api/@azure/cosmos/cosmosclient) connects to an Azure Cosmos DB instance. A documentação do Azure Cosmos DB recomenda que você [use um cliente singleton do Azure Cosmos DB pelo tempo de vida do aplicativo](../cosmos-db/performance-tips.md#sdk-usage). O exemplo a seguir mostra um padrão para fazer isso em uma função:

```javascript
const cosmos = require('@azure/cosmos');
const endpoint = process.env.COSMOS_API_URL;
const key = process.env.COSMOS_API_KEY;
const { CosmosClient } = cosmos;

const client = new CosmosClient({ endpoint, key });
// All function invocations also reference the same database and container.
const container = client.database("MyDatabaseName").container("MyContainerName");

module.exports = async function (context) {
    const { resources: itemArray } = await container.items.readAll().fetchAll();
    context.log(itemArray);
}
```

## <a name="sqlclient-connections"></a>Conexões SqlClient

Your function code can use the .NET Framework Data Provider for SQL Server ([SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx)) to make connections to a SQL relational database. This is also the underlying provider for data frameworks that rely on ADO.NET, such as [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx). Ao contrário das conexões [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) e [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
), o ADO.NET implementa o pool de conexões por padrão. But because you can still run out of connections, you should optimize connections to the database. Para obter mais informações, consulte [Pool de Conexões do SQL Server (ADO.NET)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling).

> [!TIP]
> Some data frameworks, such as Entity Framework, typically get connection strings from the **ConnectionStrings** section of a configuration file. Nesse caso, é necessário adicionar explicitamente as cadeias de conexão de banco de dados SQL à coleção **Cadeias de conexão** das configurações de aplicativo de funções e no [local.settings.json file](functions-run-local.md#local-settings-file) no projeto local. If you're creating an instance of [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) in your function code, you should store the connection string value in **Application settings** with your other connections.

## <a name="next-steps"></a>Próximos passos

For more information about why we recommend static clients, see [Improper instantiation antipattern](https://docs.microsoft.com/azure/architecture/antipatterns/improper-instantiation/).

Para obter mais dicas de desempenho do Azure Functions, consulte [Melhore o desempenho e a confiabilidade do Azure Functions](functions-best-practices.md).
