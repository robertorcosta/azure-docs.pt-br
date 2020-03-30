---
title: Vinculação de saída do Azure Cosmos DB para Funções 2.x
description: Aprenda a usar a vinculação de saída azure Cosmos DB em Funções Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2020
ms.author: cshoe
ms.openlocfilehash: 636903c20e07f11a2fd919654cfaa62037171f20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277759"
---
# <a name="azure-cosmos-db-output-binding-for-azure-functions-2x"></a>Vinculação de saída do Azure Cosmos DB para funções azure 2.x

Com a associação de saída do Azure Cosmos DB, você pode gravar um novo documento para um banco de dados do Azure Cosmos DB usando a API de SQL.

Para obter informações sobre detalhes de configuração e configuração, consulte a [visão geral](./functions-bindings-cosmosdb-v2.md).

<a id="example" name="example"></a>

# <a name="c"></a>[C #](#tab/csharp)

Esta seção contém os seguintes exemplos:

* [Gatilho da fila, gravar um documento](#queue-trigger-write-one-doc-c)
* [Gatilho da fila, gravar documentos usando IAsyncCollector](#queue-trigger-write-docs-using-iasynccollector-c)

Os exemplos se referem a um tipo `ToDoItem` simples:

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

<a id="queue-trigger-write-one-doc-c"></a>

### <a name="queue-trigger-write-one-doc"></a>Gatilho da fila, gravar um documento

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que adiciona um documento a um banco de dados, usando os dados fornecidos na mensagem do armazenamento de fila.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using System;

namespace CosmosDBSamplesV2
{
    public static class WriteOneDoc
    {
        [FunctionName("WriteOneDoc")]
        public static void Run(
            [QueueTrigger("todoqueueforwrite")] string queueMessage,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")]out dynamic document,
            ILogger log)
        {
            document = new { Description = queueMessage, id = Guid.NewGuid() };

            log.LogInformation($"C# Queue trigger function inserted one row");
            log.LogInformation($"Description={queueMessage}");
        }
    }
}
```

<a id="queue-trigger-write-docs-using-iasynccollector-c"></a>

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Gatilho da fila, gravar documentos usando IAsyncCollector

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que adiciona uma coleção de documentos a um banco de dados, usando os dados fornecidos em um JSON de mensagem da fila.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class WriteDocsIAsyncCollector
    {
        [FunctionName("WriteDocsIAsyncCollector")]
        public static async Task Run(
            [QueueTrigger("todoqueueforwritemulti")] ToDoItem[] toDoItemsIn,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")]
                IAsyncCollector<ToDoItem> toDoItemsOut,
            ILogger log)
        {
            log.LogInformation($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

            foreach (ToDoItem toDoItem in toDoItemsIn)
            {
                log.LogInformation($"Description={toDoItem.Description}");
                await toDoItemsOut.AddAsync(toDoItem);
            }
        }
    }
}
```

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

Esta seção contém os seguintes exemplos:

* [Gatilho da fila, gravar um documento](#queue-trigger-write-one-doc-c-script)
* [Gatilho da fila, gravar documentos usando IAsyncCollector](#queue-trigger-write-docs-using-iasynccollector-c-script)


<a id="queue-trigger-write-one-doc-c-script"></a>

### <a name="queue-trigger-write-one-doc"></a>Gatilho da fila, gravar um documento

O exemplo a seguir mostra uma associação de saída do Azure Cosmos DB em um arquivo *function.json* e uma [função de script C#](functions-reference-csharp.md) que usa a associação. A função usa uma associação de entrada de fila para uma fila que recebe o JSON no seguinte formato:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

A função cria documentos do Azure Cosmos DB no formato a seguir para cada registro:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Aqui estão os dados de associação no arquivo *function.json*:

```json
{
    "name": "employeeDocument",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

A seção [configuração](#configuration) explica essas propriedades.

Aqui está o código de script do C#:

```cs
    #r "Newtonsoft.Json"

    using Microsoft.Azure.WebJobs.Host;
    using Newtonsoft.Json.Linq;
    using Microsoft.Extensions.Logging;

    public static void Run(string myQueueItem, out object employeeDocument, ILogger log)
    {
      log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");

      dynamic employee = JObject.Parse(myQueueItem);

      employeeDocument = new {
        id = employee.name + "-" + employee.employeeId,
        name = employee.name,
        employeeId = employee.employeeId,
        address = employee.address
      };
    }
```

<a id="queue-trigger-write-docs-using-iasynccollector-c-script"></a>

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Gatilho da fila, gravar documentos usando IAsyncCollector

Para criar vários documentos, você também pode associar a `ICollector<T>` ou `IAsyncCollector<T>`, sendo `T` um dos tipos com suporte.

Este exemplo se refere a um tipo `ToDoItem` simples:

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

Aqui está o arquivo function.json:

```json
{
  "bindings": [
    {
      "name": "toDoItemsIn",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "todoqueueforwritemulti",
      "connectionStringSetting": "AzureWebJobsStorage"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItemsOut",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Aqui está o código de script do C#:

```cs
using System;
using Microsoft.Extensions.Logging;

public static async Task Run(ToDoItem[] toDoItemsIn, IAsyncCollector<ToDoItem> toDoItemsOut, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

    foreach (ToDoItem toDoItem in toDoItemsIn)
    {
        log.LogInformation($"Description={toDoItem.Description}");
        await toDoItemsOut.AddAsync(toDoItem);
    }
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

O exemplo a seguir mostra uma associação de saída do Azure Cosmos DB em um arquivo *function.json* e uma [função de script C#](functions-reference-node.md) que usa a associação. A função usa uma associação de entrada de fila para uma fila que recebe o JSON no seguinte formato:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

A função cria documentos do Azure Cosmos DB no formato a seguir para cada registro:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Aqui estão os dados de associação no arquivo *function.json*:

```json
{
    "name": "employeeDocument",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

A seção [configuração](#configuration) explica essas propriedades.

Aqui está o código JavaScript:

```javascript
    module.exports = function (context) {

      context.bindings.employeeDocument = JSON.stringify({
        id: context.bindings.myQueueItem.name + "-" + context.bindings.myQueueItem.employeeId,
        name: context.bindings.myQueueItem.name,
        employeeId: context.bindings.myQueueItem.employeeId,
        address: context.bindings.myQueueItem.address
      });

      context.done();
    };
```

# <a name="python"></a>[Python](#tab/python)

O exemplo a seguir demonstra como escrever um documento em um banco de dados do Azure CosmosDB como a saída de uma função.

A definição de vinculação é definida em *function.json* onde *o tipo* é definido como `cosmosDB`.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "cosmosDB",
      "direction": "out",
      "name": "doc",
      "databaseName": "demodb",
      "collectionName": "data",
      "createIfNotExists": "true",
      "connectionStringSetting": "AzureCosmosDBConnectionString"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

Para escrever no banco de dados, `set` passe um objeto de documento para o método do parâmetro do banco de dados.

```python
import azure.functions as func

def main(req: func.HttpRequest, doc: func.Out[func.Document]) -> func.HttpResponse:

    request_body = req.get_body()

    doc.set(func.Document.from_json(request_body))

    return 'OK'
```

# <a name="java"></a>[Java](#tab/java)

* [Gatilho de fila, salva a mensagem ao banco de dados por meio do valor de retorno](#queue-trigger-save-message-to-database-via-return-value-java)
* [Gatilho HTTP, salva um documento ao banco de dados por meio do valor de retorno](#http-trigger-save-one-document-to-database-via-return-value-java)
* [Gatilho HTTP, salva um documento ao banco de dados por meio do valor de OutputBinding](#http-trigger-save-one-document-to-database-via-outputbinding-java)
* [Gatilho HTTP, salva vários documentos ao banco de dados por meio do valor de OutputBinding](#http-trigger-save-multiple-documents-to-database-via-outputbinding-java)


<a id="queue-trigger-save-message-to-database-via-return-value-java"></a>

### <a name="queue-trigger-save-message-to-database-via-return-value"></a>Gatilho de fila, salva a mensagem ao banco de dados por meio do valor de retorno

O exemplo a seguir mostra uma função Java que adiciona um documento a um banco de dados com dados de uma mensagem no armazenamento de Filas.

```java
@FunctionName("getItem")
@CosmosDBOutput(name = "database",
  databaseName = "ToDoList",
  collectionName = "Items",
  connectionStringSetting = "AzureCosmosDBConnection")
public String cosmosDbQueryById(
    @QueueTrigger(name = "msg",
      queueName = "myqueue-items",
      connection = "AzureWebJobsStorage")
    String message,
    final ExecutionContext context)  {
     return "{ id: \"" + System.currentTimeMillis() + "\", Description: " + message + " }";
   }
```
<a id="http-trigger-save-one-document-to-database-via-return-value-java"></a>

#### <a name="http-trigger-save-one-document-to-database-via-return-value"></a>Gatilho HTTP, salva um documento ao banco de dados por meio do valor de retorno

O exemplo a seguir mostra uma função de Java cuja assinatura é anotada com ```@CosmosDBOutput``` e tem o valor retornado do tipo ```String```. O documento JSON retornado pela função será gravado automaticamente à coleção do CosmosDB correspondente.

```java
    @FunctionName("WriteOneDoc")
    @CosmosDBOutput(name = "database",
      databaseName = "ToDoList",
      collectionName = "Items",
      connectionStringSetting = "Cosmos_DB_Connection_String")
    public String run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());

        // Parse query parameter
        String query = request.getQueryParameters().get("desc");
        String name = request.getBody().orElse(query);

        // Generate random ID
        final int id = Math.abs(new Random().nextInt());

        // Generate document
        final String jsonDocument = "{\"id\":\"" + id + "\", " +
                                    "\"description\": \"" + name + "\"}";

        context.getLogger().info("Document to be saved: " + jsonDocument);

        return jsonDocument;
    }
```

<a id="http-trigger-save-one-document-to-database-via-outputbinding-java"></a>

### <a name="http-trigger-save-one-document-to-database-via-outputbinding"></a>Gatilho HTTP, salva um documento ao banco de dados por meio do valor de OutputBinding

O exemplo a seguir mostra uma função de Java que grava um documento do CosmosDB por meio de um parâmetro de saída ```OutputBinding<T>```. Neste exemplo, ```outputItem``` o parâmetro precisa ser anotado ```@CosmosDBOutput```com , não a assinatura da função. Usando ```OutputBinding<T>``` permite que sua função tire proveito da associação para gravar o documento para CosmosDB, permitindo também retornar um valor diferente para o chamador da função, como um documento XML ou JSON.

```java
    @FunctionName("WriteOneDocOutputBinding")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBOutput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            OutputBinding<String> outputItem,
            final ExecutionContext context) {

        // Parse query parameter
        String query = request.getQueryParameters().get("desc");
        String name = request.getBody().orElse(query);

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());

        // Generate random ID
        final int id = Math.abs(new Random().nextInt());

        // Generate document
        final String jsonDocument = "{\"id\":\"" + id + "\", " +
                                    "\"description\": \"" + name + "\"}";

        context.getLogger().info("Document to be saved: " + jsonDocument);

        // Set outputItem's value to the JSON document to be saved
        outputItem.setValue(jsonDocument);

        // return a different document to the browser or calling client.
        return request.createResponseBuilder(HttpStatus.OK)
                      .body("Document created successfully.")
                      .build();
    }
```

<a id="http-trigger-save-multiple-documents-to-database-via-outputbinding-java"></a>

### <a name="http-trigger-save-multiple-documents-to-database-via-outputbinding"></a>Gatilho HTTP, salva vários documentos ao banco de dados por meio do valor de OutputBinding

O exemplo a seguir mostra uma função de Java que grava vários documentos do CosmosDB por meio de um parâmetro de saída ```OutputBinding<T>```. Neste exemplo, ```outputItem``` o parâmetro é anotado ```@CosmosDBOutput```com , não a assinatura da função. O parâmetro de saída ```outputItem``` tem uma lista de objetos ```ToDoItem``` como seu tipo de parâmetro de modelo. Usando ```OutputBinding<T>``` permite que sua função tire proveito da associação para gravar os documentos para CosmosDB, permitindo também retornar um valor diferente para o chamador da função, como um documento XML ou JSON.

```java
    @FunctionName("WriteMultipleDocsOutputBinding")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBOutput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            OutputBinding<List<ToDoItem>> outputItem,
            final ExecutionContext context) {

        // Parse query parameter
        String query = request.getQueryParameters().get("desc");
        String name = request.getBody().orElse(query);

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());

        // Generate documents
        List<ToDoItem> items = new ArrayList<>();

        for (int i = 0; i < 5; i ++) {
          // Generate random ID
          final int id = Math.abs(new Random().nextInt());

          // Create ToDoItem
          ToDoItem item = new ToDoItem(String.valueOf(id), name);

          items.add(item);
        }

        // Set outputItem's value to the list of POJOs to be saved
        outputItem.setValue(items);
        context.getLogger().info("Document to be saved: " + items);

        // return a different document to the browser or calling client.
        return request.createResponseBuilder(HttpStatus.OK)
                      .body("Documents created successfully.")
                      .build();
    }
```

Na biblioteca de runtime das funções [Java](/java/api/overview/azure/functions/runtime), use a anotação `@CosmosDBOutput` nos parâmetros que serão gravados no Cosmos DB.  O tipo de parâmetro de anotação deve ser ```OutputBinding<T>```, em que T é um tipo Java nativo ou um POJO.

---

## <a name="attributes-and-annotations"></a>Atributos e anotações

# <a name="c"></a>[C #](#tab/csharp)

Em [Bibliotecas de classes C#](functions-dotnet-class-library.md), utilize o atributo [CosmosDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/master/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs).

O construtor do atributo toma o nome do banco de dados e o nome da coleção. Para obter informações sobre essas configurações e outras propriedades que podem ser configuradas, consulte [Saída – configurações](#configuration). Aqui está um exemplo de atributo `CosmosDB` em uma assinatura de método:

```csharp
    [FunctionName("QueueToDocDB")]
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [CosmosDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        ...
    }
```

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

Os atributos não são suportados pelo script C#.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Os atributos não são suportados pelo JavaScript.

# <a name="python"></a>[Python](#tab/python)

Os atributos não são suportados pelo Python.

# <a name="java"></a>[Java](#tab/java)

A `CosmosDBOutput` anotação está disponível para gravar dados no Cosmos DB. Você pode aplicar a anotação na função ou em um parâmetro de função individual. Quando usado no método de função, o valor de retorno da função é o que é escrito para Cosmos DB. Se você usar a anotação com um parâmetro, o tipo do `OutputBinding<T>` `T` parâmetro deve ser declarado como um tipo java nativo ou um POJO.

---

## <a name="configuration"></a>Configuração

A tabela a seguir explica as propriedades de configuração de `CosmosDB` vinculação que você definiu no arquivo *function.json* e no atributo.

|Propriedade function.json | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**type**     | n/d | Deve ser definido como `cosmosDB`.        |
|**direction**     | n/d | Deve ser definido como `out`.         |
|**name**     | n/d | Nome do parâmetro de associação que representa o documento na função.  |
|**databaseName** | **Databasename**|O banco de dados que contém a coleção na qual o documento será criado.     |
|**Nomedacoleta** |**CollectionName**  | O nome da coleção na qual o documento será criado. |
|**criarIfNotExiste**  |**CreateIfNotExists**    | É um valor booliano para indicar se a coleção será criada quando não existir. O padrão é *false* porque as novas coleções são criadas com a taxa de transferência reservada, o que tem implicações de preço. Para obter mais informações, consulte a [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/).  |
|**Partitionkey**|**PartitionKey** |Quando `CreateIfNotExists` é verdade, ele define o caminho-chave de partição para a coleção criada.|
|**coleçãoThroughput**|**CollectionThroughput**| Quando `CreateIfNotExists` é verdade, define o [throughput](../cosmos-db/set-throughput.md) da coleção criada.|
|**conexãoConfiguração de string**    |**ConnectionStringSetting** |O nome da configuração do aplicativo que contém a cadeia de conexão do Azure Cosmos DB.        |
|**preferredLocalizaçãos**| **PreferredLocalizaçãos**| (Opcional) Define locais preferenciais (regiões) para contas de banco de dados replicadas geo-replicadas no serviço Azure Cosmos DB. Os valores devem ser separados por comma. Por exemplo, "Leste dos EUA, Centro-Sul dos EUA, Norte da Europa". |
|**usarMultipleWriteLocalização**| **UseLocalizaçãodegravação deváriosanos**| (Opcional) Quando definido `true` para, juntamente com, `PreferredLocations`ele pode aproveitar [gravações de várias regiões](../cosmos-db/how-to-manage-database-account.md#configure-multiple-write-regions) no serviço Azure Cosmos DB. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Uso

Por padrão, quando você grava no parâmetro de saída em sua função, um documento é criado no banco de dados. Este documento contém um GUID gerado automaticamente como a ID do documento. Você pode especificar a ID do documento de saída especificando a propriedade `id` no objeto JSON enviado ao parâmetro de saída.

> [!Note]
> Ao especificar a ID de um documento existente, ela é substituída pelo novo documento de saída.

## <a name="exceptions-and-return-codes"></a>Exceções e códigos de retorno

| Associação | Referência |
|---|---|
| CosmosDB | [Códigos de erro CosmosDB](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb) |

<a name="host-json"></a>

## <a name="hostjson-settings"></a>configurações de host.json

Esta seção descreve as definições de configuração globais disponíveis para esta associação na versão 2.x. Para obter mais informações sobre as configurações globais na versão 2.x, confira a [referência de host.json para o Azure Functions versão 2.x](functions-host-json.md).

```json
{
    "version": "2.0",
    "extensions": {
        "cosmosDB": {
            "connectionMode": "Gateway",
            "protocol": "Https",
            "leaseOptions": {
                "leasePrefix": "prefix1"
            }
        }
    }
}
```

|Propriedade  |Padrão | Descrição |
|---------|---------|---------|
|GatewayMode|Gateway|O modo de conexão usado pela função ao se conectar ao serviço do Azure Cosmos DB. As opções são `Direct` e `Gateway`|
|Protocolo|Https|O protocolo de conexão usado pela função ao se conectar ao serviço do Azure Cosmos DB.  Leia [aqui para obter uma explicação de ambos os modos](../cosmos-db/performance-tips.md#networking)|
|leasePrefix|n/d|Prefixo de concessão a ser usado em todas as funções em um aplicativo.|

## <a name="next-steps"></a>Próximas etapas

- [Execute uma função quando um documento Azure Cosmos DB for criado ou modificado (Trigger)](./functions-bindings-cosmosdb-v2-trigger.md)
- [Leia um documento Azure Cosmos DB (vinculação de entrada)](./functions-bindings-cosmosdb-v2-input.md)