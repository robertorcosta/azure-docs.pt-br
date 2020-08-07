---
title: Associações de armazenamento de tabelas do Azure Functions
description: Entenda como usar as associações de armazenamento da Tabela do Azure no Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.custom: devx-track-python
ms.openlocfilehash: 7616f6c502f0b5402aa784f707d0dafa31268d78
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87853051"
---
# <a name="azure-table-storage-bindings-for-azure-functions"></a>Associações de armazenamento de tabelas do Azure Functions

Este artigo explica como trabalhar com associações de armazenamento de Tabela do Azure no Azure Functions. O Azure Functions dá suporte a associações de entrada e saída para armazenamento de Tabelas do Azure.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pacotes - Functions 1. x

As associações de armazenamento de Tabelas são fornecidas no pacote NuGet [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs), versão 2.x. O código-fonte do pacote está no repositório GitHub [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Table).

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x-and-higher"></a>Pacotes-funções 2. x e superior

As associações de Armazenamento de Tabelas são fornecidas no pacote NuGet [Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage), versão 3.x. O código-fonte do pacote está no repositório GitHub [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables).

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="input"></a>Entrada

Use a associação de entrada de armazenamento de Tabela do Azure para ler uma tabela em uma conta de Armazenamento do Azure.

# <a name="c"></a>[C#](#tab/csharp)

### <a name="one-entity"></a>Uma entidade

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que lê uma linha da tabela. Para cada mensagem enviada para a fila, a função será disparada.

O valor de chave de linha “{queueTrigger}” indica que a chave de linha foi obtida da cadeia de caracteres da mensagem da fila.

```csharp
public class TableStorage
{
    public class MyPoco
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Text { get; set; }
    }

    [FunctionName("TableInput")]
    public static void TableInput(
        [QueueTrigger("table-items")] string input, 
        [Table("MyTable", "MyPartition", "{queueTrigger}")] MyPoco poco, 
        ILogger log)
    {
        log.LogInformation($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}");
    }
}
```

### <a name="iqueryable"></a>IQueryable

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que lê várias linhas de tabela `MyPoco` das quais a classe deriva `TableEntity` .

```csharp
public class TableStorage
{
    public class MyPoco : TableEntity
    {
        public string Text { get; set; }
    }

    [FunctionName("TableInput")]
    public static void TableInput(
        [QueueTrigger("table-items")] string input, 
        [Table("MyTable", "MyPartition")] IQueryable<MyPoco> pocos, 
        ILogger log)
    {
        foreach (MyPoco poco in pocos)
        {
            log.LogInformation($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}");
        }
    }
}
```

### <a name="cloudtable"></a>CloudTable

`IQueryable` não tem suporte no [runtime do Functions v2](functions-versions.md). Uma alternativa é usar um `CloudTable` parâmetro do método para ler a tabela usando o SDK de Armazenamento do Azure. Veja um exemplo de uma função que consulta uma tabela de log de Azure Functions:

```csharp
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using Microsoft.WindowsAzure.Storage.Table;
using System;
using System.Threading.Tasks;

namespace FunctionAppCloudTable2
{
    public class LogEntity : TableEntity
    {
        public string OriginalName { get; set; }
    }
    public static class CloudTableDemo
    {
        [FunctionName("CloudTableDemo")]
        public static async Task Run(
            [TimerTrigger("0 */1 * * * *")] TimerInfo myTimer, 
            [Table("AzureWebJobsHostLogscommon")] CloudTable cloudTable,
            ILogger log)
        {
            log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");

            TableQuery<LogEntity> rangeQuery = new TableQuery<LogEntity>().Where(
                TableQuery.CombineFilters(
                    TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, 
                        "FD2"),
                    TableOperators.And,
                    TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, 
                        "t")));

            // Execute the query and loop through the results
            foreach (LogEntity entity in 
                await cloudTable.ExecuteQuerySegmentedAsync(rangeQuery, null))
            {
                log.LogInformation(
                    $"{entity.PartitionKey}\t{entity.RowKey}\t{entity.Timestamp}\t{entity.OriginalName}");
            }
        }
    }
}
```

Para obter mais informações sobre como usar o CloudTable, consulte [Introdução ao Armazenamento de Tabelas do Azure](../cosmos-db/tutorial-develop-table-dotnet.md).

Se você tentar associar `CloudTable` e receber uma mensagem de erro, certifique-se de ter uma referência para [a versão correta do SDK do Armazenamento](#azure-storage-sdk-version-in-functions-1x).

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

### <a name="one-entity"></a>Uma entidade

O exemplo a seguir mostra uma associação de entrada de tabela em um arquivo *function.json* e código [script C#](functions-reference-csharp.md) que usa a associação. A função usa um gatilho de fila para ler uma linha da tabela. 

O arquivo *function.json* especifica um `partitionKey` e um `rowKey`. O `rowKey` valor “{queueTrigger}” indica que a chave de linha foi obtida da cadeia de caracteres da mensagem da fila.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

A seção [configuração](#input---configuration) explica essas propriedades.

Aqui está o código de script do C#:

```csharp
public static void Run(string myQueueItem, Person personEntity, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    log.LogInformation($"Name in Person entity: {personEntity.Name}");
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}
```

### <a name="iqueryable"></a>IQueryable

O exemplo a seguir mostra uma associação de entrada de tabela em um arquivo *function.json* e código [script C#](functions-reference-csharp.md) que usa a associação. A função lê entidades para uma chave de partição especificada em uma mensagem da fila.

Aqui está o arquivo *function.json*:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "tableBinding",
      "type": "table",
      "connection": "MyStorageConnectionAppSetting",
      "tableName": "Person",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

A seção [configuração](#input---configuration) explica essas propriedades.

O código script C# adiciona uma referência ao SDK de Armazenamento do Azure para que o tipo de entidade possa derivar de `TableEntity`:

```csharp
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;
using Microsoft.Extensions.Logging;

public static void Run(string myQueueItem, IQueryable<Person> tableBinding, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    foreach (Person person in tableBinding.Where(p => p.PartitionKey == myQueueItem).ToList())
    {
        log.LogInformation($"Name: {person.Name}");
    }
}

public class Person : TableEntity
{
    public string Name { get; set; }
}
```

### <a name="cloudtable"></a>CloudTable

`IQueryable`Não tem suporte no tempo de execução do Functions para [as versões 2. x e superior)](functions-versions.md). Uma alternativa é usar um `CloudTable` parâmetro do método para ler a tabela usando o SDK de Armazenamento do Azure. Veja um exemplo de uma função que consulta uma tabela de log de Azure Functions:

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 */1 * * * *"
    },
    {
      "name": "cloudTable",
      "type": "table",
      "connection": "AzureWebJobsStorage",
      "tableName": "AzureWebJobsHostLogscommon",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

```csharp
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;
using System;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static async Task Run(TimerInfo myTimer, CloudTable cloudTable, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");

    TableQuery<LogEntity> rangeQuery = new TableQuery<LogEntity>().Where(
    TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, 
            "FD2"),
        TableOperators.And,
        TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, 
            "a")));

    // Execute the query and loop through the results
    foreach (LogEntity entity in 
    await cloudTable.ExecuteQuerySegmentedAsync(rangeQuery, null))
    {
        log.LogInformation(
            $"{entity.PartitionKey}\t{entity.RowKey}\t{entity.Timestamp}\t{entity.OriginalName}");
    }
}

public class LogEntity : TableEntity
{
    public string OriginalName { get; set; }
}
```

Para obter mais informações sobre como usar o CloudTable, consulte [Introdução ao Armazenamento de Tabelas do Azure](../cosmos-db/tutorial-develop-table-dotnet.md).

Se você tentar associar `CloudTable` e receber uma mensagem de erro, certifique-se de ter uma referência para [a versão correta do SDK do Armazenamento](#azure-storage-sdk-version-in-functions-1x).


# <a name="javascript"></a>[JavaScript](#tab/javascript)

O exemplo a seguir mostra uma associação de entrada de tabela em um arquivo*function.json* e o código [JavaScript](functions-reference-node.md) que usa a associação. A função usa um gatilho de fila para ler uma linha da tabela. 

O arquivo *function.json* especifica um `partitionKey` e um `rowKey`. O `rowKey` valor “{queueTrigger}” indica que a chave de linha foi obtida da cadeia de caracteres da mensagem da fila.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

A seção [configuração](#input---configuration) explica essas propriedades.

Aqui está o código JavaScript:

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

Linha de tabela única 

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "messageJSON",
      "type": "table",
      "tableName": "messages",
      "partitionKey": "message",
      "rowKey": "{id}",
      "connection": "AzureWebJobsStorage",
      "direction": "in"
    },
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ],
      "route": "messages/{id}"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ],
  "disabled": false
}
```

```python
import json

import azure.functions as func

def main(req: func.HttpRequest, messageJSON) -> func.HttpResponse:

    message = json.loads(messageJSON)
    return func.HttpResponse(f"Table row: {messageJSON}")
```

# <a name="java"></a>[Java](#tab/java)

O exemplo a seguir mostra uma função disparada por HTTP que retorna uma lista de objetos Person que estão em uma partição especificada no armazenamento de tabela. No exemplo, a chave de partição é extraída da rota http e o TableName e a conexão são das configurações de função. 

```java
public class Person {
    private String PartitionKey;
    private String RowKey;
    private String Name;

    public String getPartitionKey() { return this.PartitionKey; }
    public void setPartitionKey(String key) { this.PartitionKey = key; }
    public String getRowKey() { return this.RowKey; }
    public void setRowKey(String key) { this.RowKey = key; }
    public String getName() { return this.Name; }
    public void setName(String name) { this.Name = name; }
}

@FunctionName("getPersonsByPartitionKey")
public Person[] get(
        @HttpTrigger(name = "getPersons", methods = {HttpMethod.GET}, authLevel = AuthorizationLevel.FUNCTION, route="persons/{partitionKey}") HttpRequestMessage<Optional<String>> request,
        @BindingName("partitionKey") String partitionKey,
        @TableInput(name="persons", partitionKey="{partitionKey}", tableName="%MyTableName%", connection="MyConnectionString") Person[] persons,
        final ExecutionContext context) {

    context.getLogger().info("Got query for person related to persons with partition key: " + partitionKey);

    return persons;
}
```

A anotação TableInput também pode extrair as associações do corpo JSON da solicitação, como mostra o exemplo a seguir.

```java
@FunctionName("GetPersonsByKeysFromRequest")
public HttpResponseMessage get(
        @HttpTrigger(name = "getPerson", methods = {HttpMethod.GET}, authLevel = AuthorizationLevel.FUNCTION, route="query") HttpRequestMessage<Optional<String>> request,
        @TableInput(name="persons", partitionKey="{partitionKey}", rowKey = "{rowKey}", tableName="%MyTableName%", connection="MyConnectionString") Person person,
        final ExecutionContext context) {

    if (person == null) {
        return request.createResponseBuilder(HttpStatus.NOT_FOUND)
                    .body("Person not found.")
                    .build();
    }

    return request.createResponseBuilder(HttpStatus.OK)
                    .header("Content-Type", "application/json")
                    .body(person)
                    .build();
}
```

Os exemplos a seguir usam o filtro para consultar pessoas com um nome específico em uma tabela do Azure e limita o número de possíveis correspondências a 10 resultados.

```java
@FunctionName("getPersonsByName")
public Person[] get(
        @HttpTrigger(name = "getPersons", methods = {HttpMethod.GET}, authLevel = AuthorizationLevel.FUNCTION, route="filter/{name}") HttpRequestMessage<Optional<String>> request,
        @BindingName("name") String name,
        @TableInput(name="persons", filter="Name eq '{name}'", take = "10", tableName="%MyTableName%", connection="MyConnectionString") Person[] persons,
        final ExecutionContext context) {

    context.getLogger().info("Got query for person related to persons with name: " + name);

    return persons;
}
```

---

## <a name="input---attributes-and-annotations"></a>Entrada-atributos e anotações

# <a name="c"></a>[C#](#tab/csharp)

 Em [bibliotecas de classes C#](functions-dotnet-class-library.md), use os seguintes atributos para configurar uma associação de entrada da tabela:

* [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs)

  O construtor do atributo usa o nome da tabela, a chave de partição e a chave de linha. O atributo pode ser usado em um `out` parâmetro ou no valor de retorno da função, conforme mostrado no exemplo a seguir:

  ```csharp
  [FunctionName("TableInput")]
  public static void Run(
      [QueueTrigger("table-items")] string input, 
      [Table("MyTable", "Http", "{queueTrigger}")] MyPoco poco, 
      ILogger log)
  {
      ...
  }
  ```

  Você pode definir a `Connection` propriedade para especificar a conta de armazenamento para usar, conforme mostrado no exemplo a seguir:

  ```csharp
  [FunctionName("TableInput")]
  public static void Run(
      [QueueTrigger("table-items")] string input, 
      [Table("MyTable", "Http", "{queueTrigger}", Connection = "StorageConnectionAppSetting")] MyPoco poco, 
      ILogger log)
  {
      ...
  }
  ```

  Para ver um exemplo completo, confira Entrada - exemplo de C#.

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Oferece uma maneira de especificar a conta de armazenamento para usar. O construtor toma o nome de uma configuração de aplicativo que contenha uma cadeia de conexão de armazenamento. O atributo pode ser aplicado no nível de classe, método ou parâmetro. O exemplo a seguir mostra o nível de classe e método:

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("TableInput")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ...
  }
  ```

A conta de armazenamento a ser usada é determinada na seguinte ordem:

* A propriedade `Table` do atributo`Connection`.
* O `StorageAccount` atributo aplicado ao mesmo parâmetro do `Table` atributo.
* O `StorageAccount` atributo aplicado à função.
* O `StorageAccount` atributo aplicado à classe.
* A conta de armazenamento padrão para a função de aplicativo (configuração de aplicativo "AzureWebJobsStorage").

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

O script C# não dá suporte a atributos.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O JavaScript não dá suporte a atributos.

# <a name="python"></a>[Python](#tab/python)

O Python não dá suporte a atributos.

# <a name="java"></a>[Java](#tab/java)

Na [biblioteca de runtime de funções Java](/java/api/overview/azure/functions/runtime), use a anotação `@TableInput` nos parâmetros cujo valor possa ser proveniente do Armazenamento de Tabelas.  Essa anotação pode ser usada com tipos nativos do Java, POJOs ou valores que permitem valor nulos usando `Optional<T>`.

---

## <a name="input---configuration"></a>Entrada - configuração

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *function.json* e no atributo `Table`.

|Propriedade function.json | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d | Deve ser definido como `table`. Esse parâmetro é definido automaticamente quando você cria a associação no portal do Azure.|
|**direction** | n/d | Deve ser definido como `in`. Esse parâmetro é definido automaticamente quando você cria a associação no portal do Azure. |
|**name** | n/d | O nome da variável que representa a tabela ou entidade no código de função. | 
|**tableName** | **TableName** | O nome da tabela.| 
|**partitionKey** | **PartitionKey** |Opcional. Chave de partição da entidade de tabela para leitura. Consulte a seção [uso](#input---usage) para obter orientação sobre como usar essa propriedade.| 
|**rowKey** |**RowKey** | Opcional. Chave de linha da entidade de tabela para leitura. Consulte a seção [uso](#input---usage) para obter orientação sobre como usar essa propriedade.| 
|**take** |**Take** | Opcional. O número máximo de entidades para ler em JavaScript. Consulte a seção [uso](#input---usage) para obter orientação sobre como usar essa propriedade.| 
|**sem** |**Filter** | Opcional. Uma expressão de filtro OData para a entrada de tabela em JavaScript. Consulte a seção [uso](#input---usage) para obter orientação sobre como usar essa propriedade.| 
|**connection** |**Conexão** | O nome de uma configuração de aplicativo que contém uma cadeia de conexão de Armazenamento para usar para essa associação. A configuração pode ser o nome de uma configuração do aplicativo prefixado "AzureWebJobs" ou nome da cadeia de conexão. Por exemplo, se o nome da sua configuração for "AzureWebJobsMyStorage", você poderá especificar "mystorage" aqui. O tempo de execução do Functions procurará automaticamente uma configuração de aplicativo denominada "AzureWebJobsMyStorage". Se você deixar `connection` vazio, o runtime de Functions usa a cadeia de caracteres de conexão de Armazenamento padrão na configuração de aplicativo chamada `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Entrada - uso

# <a name="c"></a>[C#](#tab/csharp)

* **Ler uma linha em**

  Definir `partitionKey` e `rowKey`. Acessar os dados da tabela usando um parâmetro de método `T <paramName>`. No script do C#, `paramName` é o valor especificado na propriedade `name` de *function.json*. `T` geralmente é um tipo que implementa `ITableEntity` ou deriva de `TableEntity`. As propriedades `filter` e `take` não são usadas neste cenário.

* **Ler uma ou mais linhas**

  Acessar os dados da tabela usando um parâmetro de método `IQueryable<T> <paramName>`. No script do C#, `paramName` é o valor especificado na propriedade `name` de *function.json*. `T` geralmente é um tipo que implementa `ITableEntity` ou deriva de `TableEntity`. Você pode usar `IQueryable` métodos para fazer a filtragem necessária. As propriedades `partitionKey`, `rowKey`, `filter` e `take` não são usadas neste cenário.  

  > [!NOTE]
  > `IQueryable` não tem suporte no [runtime do Functions v2](functions-versions.md). Uma alternativa é [usar um parâmetro do método paramName de CloudTable](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) para ler a tabela usando o SDK de Armazenamento do Azure. Se você tentar associar `CloudTable` e receber uma mensagem de erro, certifique-se de ter uma referência para [a versão correta do SDK do Armazenamento](#azure-storage-sdk-version-in-functions-1x).

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

* **Ler uma linha em**

  Definir `partitionKey` e `rowKey`. Acessar os dados da tabela usando um parâmetro de método `T <paramName>`. No script do C#, `paramName` é o valor especificado na propriedade `name` de *function.json*. `T` geralmente é um tipo que implementa `ITableEntity` ou deriva de `TableEntity`. As propriedades `filter` e `take` não são usadas neste cenário.

* **Ler uma ou mais linhas**

  Acessar os dados da tabela usando um parâmetro de método `IQueryable<T> <paramName>`. No script do C#, `paramName` é o valor especificado na propriedade `name` de *function.json*. `T` geralmente é um tipo que implementa `ITableEntity` ou deriva de `TableEntity`. Você pode usar `IQueryable` métodos para fazer a filtragem necessária. As propriedades `partitionKey`, `rowKey`, `filter` e `take` não são usadas neste cenário.  

  > [!NOTE]
  > `IQueryable` não tem suporte no [runtime do Functions v2](functions-versions.md). Uma alternativa é [usar um parâmetro do método paramName de CloudTable](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) para ler a tabela usando o SDK de Armazenamento do Azure. Se você tentar associar `CloudTable` e receber uma mensagem de erro, certifique-se de ter uma referência para [a versão correta do SDK do Armazenamento](#azure-storage-sdk-version-in-functions-1x).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Definir as propriedades `filter` e `take`. Não definir `partitionKey` ou `rowKey`. Acesse a entidade (ou entidades) de tabela de entrada usando `context.bindings.<BINDING_NAME>`. Os objetos desserializados têm propriedades `RowKey` e `PartitionKey`.

# <a name="python"></a>[Python](#tab/python)

Os dados da tabela são passados para a função como uma cadeia de caracteres JSON. Desserializar a mensagem chamando `json.loads` conforme mostrado no [exemplo](#input)de entrada.

# <a name="java"></a>[Java](#tab/java)

O atributo [TableInput](/java/api/com.microsoft.azure.functions.annotation.tableinput) fornece acesso à linha da tabela que disparou a função.

---

## <a name="output"></a>Saída

Use uma associação de saída de armazenamento de Tabela do Azure para gravar entidades para uma tabela em uma conta de Armazenamento do Azure.

> [!NOTE]
> Essa associação de saída não dá suporte para atualização de entidades existentes. Use a `TableOperation.Replace` operação [do SDK do Armazenamento do Microsoft Azure](../cosmos-db/tutorial-develop-table-dotnet.md#delete-an-entity) para atualizar uma entidade existente.

# <a name="c"></a>[C#](#tab/csharp)

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que usa um gatilho HTTP para gravar uma única linha de tabela. 

```csharp
public class TableStorage
{
    public class MyPoco
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Text { get; set; }
    }

    [FunctionName("TableOutput")]
    [return: Table("MyTable")]
    public static MyPoco TableOutput([HttpTrigger] dynamic input, ILogger log)
    {
        log.LogInformation($"C# http trigger function processed: {input.Text}");
        return new MyPoco { PartitionKey = "Http", RowKey = Guid.NewGuid().ToString(), Text = input.Text };
    }
}
```

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

O exemplo a seguir mostra uma associação de saída de tabela em um arquivo *function.json* e código [script C#](functions-reference-csharp.md) que usa a associação. A função escreve múltiplas entidades de tabela.

Aqui está o arquivo *function.json*:

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

A seção [configuração](#output---configuration) explica essas propriedades.

Aqui está o código de script do C#:

```csharp
public static void Run(string input, ICollector<Person> tableBinding, ILogger log)
{
    for (int i = 1; i < 10; i++)
        {
            log.LogInformation($"Adding Person entity {i}");
            tableBinding.Add(
                new Person() { 
                    PartitionKey = "Test", 
                    RowKey = i.ToString(), 
                    Name = "Name" + i.ToString() }
                );
        }

}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}

```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O exemplo a seguir mostra uma associação de saída de tabela em um arquivo *function.json* e código [script C#](functions-reference-node.md) que usa a associação. A função escreve múltiplas entidades de tabela.

Aqui está o arquivo *function.json*:

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

A seção [configuração](#output---configuration) explica essas propriedades.

Aqui está o código JavaScript:

```javascript
module.exports = function (context) {

    context.bindings.tableBinding = [];

    for (var i = 1; i < 10; i++) {
        context.bindings.tableBinding.push({
            PartitionKey: "Test",
            RowKey: i.toString(),
            Name: "Name " + i
        });
    }
    
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

O exemplo a seguir demonstra como usar a associação de saída de armazenamento de tabela. A `table` associação é configurada no *function.jsem* atribuindo valores a `name` , `tableName` , `partitionKey` e `connection` :

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "message",
      "type": "table",
      "tableName": "messages",
      "partitionKey": "message",
      "connection": "AzureWebJobsStorage",
      "direction": "out"
    },
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
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

A função a seguir gera um UUI exclusivo para o `rowKey` valor e persiste a mensagem no armazenamento de tabelas.

```python
import logging
import uuid
import json

import azure.functions as func

def main(req: func.HttpRequest, message: func.Out[str]) -> func.HttpResponse:

    rowKey = str(uuid.uuid4())

    data = {
        "Name": "Output binding message",
        "PartitionKey": "message",
        "RowKey": rowKey
    }

    message.set(json.dumps(data))

    return func.HttpResponse(f"Message created with the rowKey: {rowKey}")
```

# <a name="java"></a>[Java](#tab/java)

O exemplo a seguir mostra uma função Java que usa um gatilho HTTP para gravar uma única linha de tabela.

```java
public class Person {
    private String PartitionKey;
    private String RowKey;
    private String Name;

    public String getPartitionKey() {return this.PartitionKey;}
    public void setPartitionKey(String key) {this.PartitionKey = key; }
    public String getRowKey() {return this.RowKey;}
    public void setRowKey(String key) {this.RowKey = key; }
    public String getName() {return this.Name;}
    public void setName(String name) {this.Name = name; }
}

public class AddPerson {

    @FunctionName("addPerson")
    public HttpResponseMessage get(
            @HttpTrigger(name = "postPerson", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION, route="persons/{partitionKey}/{rowKey}") HttpRequestMessage<Optional<Person>> request,
            @BindingName("partitionKey") String partitionKey,
            @BindingName("rowKey") String rowKey,
            @TableOutput(name="person", partitionKey="{partitionKey}", rowKey = "{rowKey}", tableName="%MyTableName%", connection="MyConnectionString") OutputBinding<Person> person,
            final ExecutionContext context) {

        Person outPerson = new Person();
        outPerson.setPartitionKey(partitionKey);
        outPerson.setRowKey(rowKey);
        outPerson.setName(request.getBody().get().getName());

        person.setValue(outPerson);

        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(outPerson)
                        .build();
    }
}
```

O exemplo a seguir mostra uma função Java que usa um gatilho HTTP para gravar várias linhas da tabela.

```java
public class Person {
    private String PartitionKey;
    private String RowKey;
    private String Name;

    public String getPartitionKey() {return this.PartitionKey;}
    public void setPartitionKey(String key) {this.PartitionKey = key; }
    public String getRowKey() {return this.RowKey;}
    public void setRowKey(String key) {this.RowKey = key; }
    public String getName() {return this.Name;}
    public void setName(String name) {this.Name = name; }
}

public class AddPersons {

    @FunctionName("addPersons")
    public HttpResponseMessage get(
            @HttpTrigger(name = "postPersons", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION, route="persons/") HttpRequestMessage<Optional<Person[]>> request,
            @TableOutput(name="person", tableName="%MyTableName%", connection="MyConnectionString") OutputBinding<Person[]> persons,
            final ExecutionContext context) {

        persons.setValue(request.getBody().get());

        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(request.getBody().get())
                        .build();
    }
}
```

---

## <a name="output---attributes-and-annotations"></a>Saída-atributos e anotações

# <a name="c"></a>[C#](#tab/csharp)

Em [bibliotecas de classes do C#](functions-dotnet-class-library.md), use o [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs).

O construtor do atributo usa o nome da tabela. O atributo pode ser usado em um `out` parâmetro ou no valor de retorno da função, conforme mostrado no exemplo a seguir:

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    ILogger log)
{
    ...
}
```

Você pode definir a `Connection` propriedade para especificar a conta de armazenamento para usar, conforme mostrado no exemplo a seguir:

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable", Connection = "StorageConnectionAppSetting")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    ILogger log)
{
    ...
}
```

Para ver um exemplo completo, consulte [Saída – exemplo de C#](#output).

Você pode usar o `StorageAccount` atributo para especificar a conta de armazenamento no nível de classe, método ou parâmetro. Para obter mais informações, consulte [Entrada - atributos](#input---attributes-and-annotations).

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

O script C# não dá suporte a atributos.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O JavaScript não dá suporte a atributos.

# <a name="python"></a>[Python](#tab/python)

O Python não dá suporte a atributos.

# <a name="java"></a>[Java](#tab/java)

Na [biblioteca de tempo de execução de funções Java](/java/api/overview/azure/functions/runtime), use a anotação [TableOutput](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/annotation/TableOutput.java/) em parâmetros para gravar valores no armazenamento de tabelas.

Consulte o [exemplo para obter mais detalhes](#output).

---

## <a name="output---configuration"></a>Saída - configuração

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *function.json* e no atributo `Table`.

|Propriedade function.json | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d | Deve ser definido como `table`. Esse parâmetro é definido automaticamente quando você cria a associação no portal do Azure.|
|**direction** | n/d | Deve ser definido como `out`. Esse parâmetro é definido automaticamente quando você cria a associação no portal do Azure. |
|**name** | n/d | O nome da variável usada no código da função que representa a tabela ou entidade. Definido como `$return` para referenciar o valor de retorno da função.| 
|**tableName** |**TableName** | O nome da tabela.| 
|**partitionKey** |**PartitionKey** | Chave de partição da entidade de tabela para gravar. Consulte a [seção de uso](#output---usage) para obter orientação sobre como usar essa propriedade.| 
|**rowKey** |**RowKey** | A chave de linha da entidade de tabela para gravar. Consulte a [seção de uso](#output---usage) para obter orientação sobre como usar essa propriedade.| 
|**connection** |**Conexão** | O nome de uma configuração de aplicativo que contém uma cadeia de conexão de Armazenamento para usar para essa associação. Se o nome de configuração do aplicativo começar com "AzureWebJobs", você pode especificar apenas o resto do nome aqui. Por exemplo, se você definir `connection` como "mystorage", o tempo de execução do Functions procurará uma configuração de aplicativo chamada "mystorage". Se você deixar `connection` vazio, o runtime de Functions usa a cadeia de caracteres de conexão de Armazenamento padrão na configuração de aplicativo chamada `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Saída - uso

# <a name="c"></a>[C#](#tab/csharp)

Acesse a entidade da tabela de saída usando um parâmetro de método `ICollector<T> paramName` ou `IAsyncCollector<T> paramName` onde `T` inclui as `PartitionKey` `RowKey` Propriedades e. Essas propriedades são geralmente acompanhadas pela implementação `ITableEntity` ou herança `TableEntity` .

Como alternativa, você pode usar um `CloudTable` parâmetro de método para gravar na tabela usando o SDK do armazenamento do Azure. Se você tentar associar `CloudTable` e receber uma mensagem de erro, certifique-se de ter uma referência para [a versão correta do SDK do Armazenamento](#azure-storage-sdk-version-in-functions-1x).

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

Acesse a entidade da tabela de saída usando um parâmetro de método `ICollector<T> paramName` ou `IAsyncCollector<T> paramName` onde `T` inclui as `PartitionKey` `RowKey` Propriedades e. Essas propriedades são geralmente acompanhadas pela implementação `ITableEntity` ou herança `TableEntity` . O `paramName` valor é especificado na `name` propriedade de *function.jsem*.

Como alternativa, você pode usar um `CloudTable` parâmetro de método para gravar na tabela usando o SDK do armazenamento do Azure. Se você tentar associar `CloudTable` e receber uma mensagem de erro, certifique-se de ter uma referência para [a versão correta do SDK do Armazenamento](#azure-storage-sdk-version-in-functions-1x).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Acesse o evento de saída usando `context.bindings.<name>`, em que `<name>` é o valor especificado na propriedade `name` de *function.json*.

# <a name="python"></a>[Python](#tab/python)

Há duas opções para a saída de uma mensagem de linha de armazenamento de tabela de uma função:

- **Valor de retorno**: defina a `name` propriedade no *function.js* como `$return` . Com essa configuração, o valor de retorno da função é persistido como uma linha de armazenamento de tabela.

- **Imperativo**: passe um valor para o método [set](/python/api/azure-functions/azure.functions.out?view=azure-python#set-val--t-----none) do parâmetro declarado como um tipo [out](/python/api/azure-functions/azure.functions.out?view=azure-python) . O valor passado para `set` é persistido como uma mensagem do hub de eventos.

# <a name="java"></a>[Java](#tab/java)

Há duas opções para a saída de uma linha de armazenamento de tabela de uma função usando a anotação [TableStorageOutput](/java/api/com.microsoft.azure.functions.annotation.tableoutput?view=azure-java-stablet) :

- **Valor de retorno**: ao aplicar a anotação à própria função, o valor de retorno da função é persistido como uma linha de armazenamento de tabela.

- **Imperativo**: para definir explicitamente o valor da mensagem, aplique a anotação a um parâmetro específico do tipo [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) , onde `T` inclui `PartitionKey` as `RowKey` Propriedades e. Essas propriedades são geralmente acompanhadas pela implementação `ITableEntity` ou herança `TableEntity` .

---

## <a name="exceptions-and-return-codes"></a>Exceções e códigos de retorno

| Associação | Referência |
|---|---|
| Tabela | [Códigos de erro de tabelas](/rest/api/storageservices/fileservices/table-service-error-codes) |
| Blob, tabela, fila | [Códigos de erro de armazenamento](/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Blob, tabela, fila | [Solução de problemas](/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Aprenda mais sobre gatilhos e de associações do Azure Functions](functions-triggers-bindings.md)
