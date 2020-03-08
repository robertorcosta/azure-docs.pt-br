---
title: Gerenciar conexões no Azure Functions
description: Saiba como evitar problemas de desempenho no Azure Functions usando clientes de conexão estática.
ms.topic: conceptual
ms.date: 02/25/2018
ms.openlocfilehash: 872ad9a1b8f0a7da6fe410e68f08469ac11045a5
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78370455"
---
# <a name="manage-connections-in-azure-functions"></a>Gerenciar conexões no Azure Functions

Funções em um aplicativo de funções compartilham recursos. Entre esses recursos compartilhados estão conexões: conexões HTTP, conexões de banco de dados e conexões com serviços como o armazenamento do Azure. Quando muitas funções estão em execução simultaneamente, é possível ficar sem conexões disponíveis. Este artigo explica como codificar suas funções para evitar o uso de mais conexões do que precisam.

## <a name="connection-limit"></a>Limite de conexão

O número de conexões disponíveis é limitado parcialmente porque um aplicativo de funções é executado em um [ambiente de área restrita](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox). Uma das restrições que a área de proteção impõe em seu código é um limite no número de conexões de saída, que são atualmente 600 conexões ativas (total de 1.200) por instância. Quando você atinge esse limite, o tempo de execução do Functions grava a seguinte mensagem nos logs: `Host thresholds exceeded: Connections`. Para obter mais informações, consulte os [limites de serviço do Functions](functions-scale.md#service-limits).

Esse limite é por instância. Quando o [controlador de escala adiciona instâncias do aplicativo de funções](functions-scale.md#how-the-consumption-and-premium-plans-work) para lidar com mais solicitações, cada instância tem um limite de conexão independente. Isso significa que não há nenhum limite de conexão global e você pode ter muito mais do que 600 conexões ativas em todas as instâncias ativas.

Ao solucionar problemas, certifique-se de ter habilitado Application Insights para seu aplicativo de funções. Application Insights permite exibir métricas para seus aplicativos de funções, como execuções. Para obter mais informações, consulte [Exibir telemetria no Application insights](functions-monitoring.md#view-telemetry-in-application-insights).  

## <a name="static-clients"></a>Clientes estáticos

Para evitar manter mais conexões que o necessário, reutilize as instâncias do cliente em vez de criar novas com cada invocação de função. É recomendável reutilizar conexões de cliente para qualquer idioma em que você possa escrever sua função. Por exemplo, clientes .NET como o [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx), o [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
)e os clientes de armazenamento do Azure podem gerenciar conexões se você usar um único cliente estático.

Aqui estão algumas diretrizes a serem seguidas quando você estiver usando um cliente específico do serviço em um aplicativo Azure Functions:

- *Não crie um* novo cliente com cada invocação de função.
- *Crie um* único cliente estático que cada invocação de função possa usar.
- *Considere* a criação de um único cliente estático em uma classe auxiliar compartilhada se funções diferentes usarem o mesmo serviço.

## <a name="client-code-examples"></a>Exemplos de código do cliente

Esta seção demonstra as melhores práticas para criar e usar clientes de seu código de função.

### <a name="httpclient-example-c"></a>Exemplo do HttpClient (C#)

Aqui está um exemplo de C# código de função que cria uma instância [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) estática:

```cs
// Create a single, static HttpClient
private static HttpClient httpClient = new HttpClient();

public static async Task Run(string input)
{
    var response = await httpClient.GetAsync("https://example.com");
    // Rest of function
}
```

Uma pergunta comum sobre o [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) no .net é "Devo descartar meu cliente?" Em geral, você descartar objetos que implementam `IDisposable` quando você terminar de usá-los. Mas você não descartar um cliente estático porque não está pronto usando-o quando a função termina. Você quer que o cliente estático permaneça durante a duração do aplicativo.

### <a name="http-agent-examples-javascript"></a>Exemplos de agente HTTP (JavaScript)

Como ele fornece melhores opções de gerenciamento de conexão, você deve usar a classe[`http.agent`](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_class_http_agent) nativa em vez de métodos não nativos, como o módulo`node-fetch`. Os parâmetros de conexão são configurados por meio de opções na classe `http.agent`. Para obter opções detalhadas disponíveis com o agente HTTP, consulte [novo agente (\[opções\])](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_new_agent_options).

A classe global `http.globalAgent` usada pelo `http.request()` tem todos esses valores definidos para seus respectivos padrões. A maneira recomendada de configurar limites de conexão em Funções é definir um número máximo globalmente. O exemplo a seguir define o número máximo de soquetes para o aplicativo de função:

```js
http.globalAgent.maxSockets = 200;
```

 O exemplo a seguir cria uma nova solicitação HTTP com um agente HTTP personalizado somente para essa solicitação:

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

### <a name="cosmosclient-code-example-javascript"></a>Exemplo de código CosmosClient (JavaScript)
[CosmosClient](/javascript/api/@azure/cosmos/cosmosclient) conecta-se a uma instância de Azure Cosmos DB. A documentação do Azure Cosmos DB recomenda que você [use um cliente singleton do Azure Cosmos DB pelo tempo de vida do aplicativo](../cosmos-db/performance-tips.md#sdk-usage). O exemplo a seguir mostra um padrão para fazer isso em uma função:

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

Seu código de função pode usar o .NET Framework Provedor de Dados para SQL Server ([SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx)) para fazer conexões com um banco de dados relacional do SQL. Esse também é o provedor subjacente para estruturas de dados que dependem de ADO.NET, como [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx). Ao contrário das conexões [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) e [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
), o ADO.NET implementa o pool de conexões por padrão. Mas como você ainda pode ficar sem conexões, você deve otimizar as conexões com o banco de dados. Para obter mais informações, consulte [Pool de Conexões do SQL Server (ADO.NET)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling).

> [!TIP]
> Algumas estruturas de dados, como Entity Framework, normalmente recebem cadeias de conexão da seção **connectionStrings** de um arquivo de configuração. Nesse caso, é necessário adicionar explicitamente as cadeias de conexão de banco de dados SQL à coleção **Cadeias de conexão** das configurações de aplicativo de funções e no [local.settings.json file](functions-run-local.md#local-settings-file) no projeto local. Se você estiver criando uma instância de [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) em seu código de função, deverá armazenar o valor da cadeia de conexão nas **configurações do aplicativo** com suas outras conexões.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre por que recomendamos clientes estáticos, consulte [antipadrão de instanciação impróprio](https://docs.microsoft.com/azure/architecture/antipatterns/improper-instantiation/).

Para obter mais dicas de desempenho do Azure Functions, consulte [Melhore o desempenho e a confiabilidade do Azure Functions](functions-best-practices.md).
