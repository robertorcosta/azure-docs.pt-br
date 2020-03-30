---
title: Gerenciar conexões em funções do Azure
description: Saiba como evitar problemas de desempenho no Azure Functions usando clientes de conexão estática.
ms.topic: conceptual
ms.date: 02/25/2018
ms.openlocfilehash: 872ad9a1b8f0a7da6fe410e68f08469ac11045a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276446"
---
# <a name="manage-connections-in-azure-functions"></a>Gerenciar conexões em funções do Azure

Funções em um aplicativo de função compartilham recursos. Entre esses recursos compartilhados estão conexões: conexões HTTP, conexões de banco de dados e conexões com serviços como o Azure Storage. Quando muitas funções estão em execução simultaneamente, é possível ficar sem conexões disponíveis. Este artigo explica como codificar suas funções para evitar o uso de mais conexões do que elas precisam.

## <a name="connection-limit"></a>Limite de conexão

O número de conexões disponíveis é limitado em parte porque um aplicativo de função é executado em um [ambiente de caixa de areia](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox). Uma das restrições que a caixa de areia impõe ao seu código é um limite no número de conexões de saída, que atualmente é de 600 conexões ativas (1.200 no total) por instância. Quando você atinge esse limite, as funções em tempo `Host thresholds exceeded: Connections`de execução gravam a seguinte mensagem nos registros: . Para obter mais informações, consulte os [limites de serviço Functions](functions-scale.md#service-limits).

Esse limite é por instância. Quando o [controlador de escala adiciona instâncias de aplicativo de função](functions-scale.md#how-the-consumption-and-premium-plans-work) para lidar com mais solicitações, cada instância tem um limite de conexão independente. Isso significa que não há limite de conexão global, e você pode ter muito mais de 600 conexões ativas em todas as instâncias ativas.

Ao solucionar problemas, certifique-se de que você habilitou o Application Insights para o seu aplicativo de função. O Application Insights permite visualizar métricas para seus aplicativos de função, como execuções. Para obter mais informações, consulte [Exibir telemetria em Insights de Aplicativos](functions-monitoring.md#view-telemetry-in-application-insights).  

## <a name="static-clients"></a>Clientes estáticos

Para evitar manter mais conexões que o necessário, reutilize as instâncias do cliente em vez de criar novas com cada invocação de função. Recomendamos reutilizar conexões de clientes para qualquer idioma em que você possa escrever sua função. Por exemplo, clientes .NET como os clientes [HttpClient,](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
)e Azure Storage podem gerenciar conexões se você usar um único cliente estático.

Aqui estão algumas diretrizes a seguir quando você estiver usando um cliente específico de serviço em um aplicativo de funções do Azure:

- *Não* crie um novo cliente com cada invocação de função.
- *Crie* um único cliente estático que toda invocação de função pode usar.
- *Considere* criar um cliente único e estático em uma classe de ajudante sumido se diferentes funções usarem o mesmo serviço.

## <a name="client-code-examples"></a>Exemplos de código do cliente

Esta seção demonstra as melhores práticas para criar e usar clientes de seu código de função.

### <a name="httpclient-example-c"></a>Exemplo do HttpClient (C#)

Aqui está um exemplo do código de função C# que cria uma instância [httpclient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) estática:

```cs
// Create a single, static HttpClient
private static HttpClient httpClient = new HttpClient();

public static async Task Run(string input)
{
    var response = await httpClient.GetAsync("https://example.com");
    // Rest of function
}
```

Uma pergunta comum sobre [httpclient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) em .NET é "Devo me livrar do meu cliente?" Em geral, você descarta `IDisposable` objetos que implementam quando você terminar de usá-los. Mas você não descarta um cliente estático porque você não terminou de usá-lo quando a função termina. Você quer que o cliente estático permaneça durante a duração do aplicativo.

### <a name="http-agent-examples-javascript"></a>Exemplos de agente HTTP (JavaScript)

Como ele fornece melhores opções de [`http.agent`](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_class_http_agent) gerenciamento de conexão, você deve `node-fetch` usar a classe nativa em vez de métodos não nativos, como o módulo. Os parâmetros de conexão `http.agent` são configurados através de opções na classe. Para obter opções detalhadas disponíveis com o agente HTTP, consulte [o novo\[Agente(opções).\]](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_new_agent_options)

A `http.globalAgent` classe global `http.request()` utilizada por tem todos esses valores definidos para seus respectivos padrões. A maneira recomendada de configurar limites de conexão em Funções é definir um número máximo globalmente. O exemplo a seguir define o número máximo de soquetes para o aplicativo de função:

```js
http.globalAgent.maxSockets = 200;
```

 O exemplo a seguir cria uma nova solicitação HTTP com um agente HTTP personalizado apenas para essa solicitação:

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
[CosmosClient](/javascript/api/@azure/cosmos/cosmosclient) se conecta a uma instância DB do Azure Cosmos. A documentação do Azure Cosmos DB recomenda que você [use um cliente singleton do Azure Cosmos DB pelo tempo de vida do aplicativo](../cosmos-db/performance-tips.md#sdk-usage). O exemplo a seguir mostra um padrão para fazer isso em uma função:

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

Seu código de função pode usar o .NET Framework Data Provider for[SQL Server (SqlClient)](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx)para fazer conexões a um banco de dados relacional SQL. Este também é o provedor subjacente para frameworks de dados que dependem de ADO.NET, como [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx). Ao contrário das conexões [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) e [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
), o ADO.NET implementa o pool de conexões por padrão. Mas como você ainda pode ficar sem conexões, você deve otimizar conexões ao banco de dados. Para obter mais informações, consulte [Pool de Conexões do SQL Server (ADO.NET)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling).

> [!TIP]
> Algumas estruturas de dados, como entity framework, normalmente obtêm strings de conexão da seção **ConnectionStrings** de um arquivo de configuração. Nesse caso, é necessário adicionar explicitamente as cadeias de conexão de banco de dados SQL à coleção **Cadeias de conexão** das configurações de aplicativo de funções e no [local.settings.json file](functions-run-local.md#local-settings-file) no projeto local. Se você estiver criando uma instância de [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) em seu código de função, você deve armazenar o valor da seqüência de conexão nas **configurações do aplicativo** com suas outras conexões.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre por que recomendamos clientes estáticos, consulte [Antipadrão de instanciação inadequada](https://docs.microsoft.com/azure/architecture/antipatterns/improper-instantiation/).

Para obter mais dicas de desempenho do Azure Functions, consulte [Melhore o desempenho e a confiabilidade do Azure Functions](functions-best-practices.md).
