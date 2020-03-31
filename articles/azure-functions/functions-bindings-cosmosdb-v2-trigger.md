---
title: Gatilho Azure Cosmos DB para Funções 2.x
description: Aprenda a usar o gatilho Azure Cosmos DB em Funções Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2020
ms.author: cshoe
ms.openlocfilehash: de8ad39ef731af3dc272d700eeee346acda64b53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277564"
---
# <a name="azure-cosmos-db-trigger-for-azure-functions-2x"></a>Gatilho Azure Cosmos DB para Funções Azure 2.x

O Gatilho do Azure Cosmos DB usa o [Feed de Alterações do Azure Cosmos DB](../cosmos-db/change-feed.md) para escutar as inserções e atualizações nas partições. O feed de alteração publica inserções e atualizações, não exclusões.

Para obter informações sobre detalhes de configuração e configuração, consulte a [visão geral](./functions-bindings-cosmosdb-v2.md).

<a id="example" name="example"></a>

# <a name="c"></a>[C #](#tab/csharp)

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que é chamada quando há inserções ou atualizações na coleção e no banco de dados especificados.

```cs
using Microsoft.Azure.Documents;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class CosmosTrigger
    {
        [FunctionName("CosmosTrigger")]
        public static void Run([CosmosDBTrigger(
            databaseName: "ToDoItems",
            collectionName: "Items",
            ConnectionStringSetting = "CosmosDBConnection",
            LeaseCollectionName = "leases",
            CreateLeaseCollectionIfNotExists = true)]IReadOnlyList<Document> documents,
            ILogger log)
        {
            if (documents != null && documents.Count > 0)
            {
                log.LogInformation($"Documents modified: {documents.Count}");
                log.LogInformation($"First document Id: {documents[0].Id}");
            }
        }
    }
}
```

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

O exemplo a seguir mostra uma associação de gatilho do Cosmos DB em um arquivo *function.json* e uma [função script C#](functions-reference-csharp.md) que usa a associação. A função grava mensagens de registro quando os registros do Cosmos DB são adicionados ou modificados.

Aqui estão os dados de associação no arquivo *function.json*:

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

Aqui está o código de script do C#:

```cs
    #r "Microsoft.Azure.DocumentDB.Core"

    using System;
    using Microsoft.Azure.Documents;
    using System.Collections.Generic;
    using Microsoft.Extensions.Logging;

    public static void Run(IReadOnlyList<Document> documents, ILogger log)
    {
      log.LogInformation("Documents modified " + documents.Count);
      log.LogInformation("First document Id " + documents[0].Id);
    }
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

O exemplo a seguir mostra uma associação de gatilho do Cosmos DB em um arquivo *function.json* e uma [função JavaScript](functions-reference-node.md) que usa a associação. A função grava mensagens de registro quando os registros do Cosmos DB são adicionados ou modificados.

Aqui estão os dados de associação no arquivo *function.json*:

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

Aqui está o código JavaScript:

```javascript
    module.exports = function (context, documents) {
      context.log('First document Id modified : ', documents[0].id);

      context.done();
    }
```

# <a name="python"></a>[Python](#tab/python)

O exemplo a seguir mostra uma associação de gatilho do Cosmos DB em um arquivo *function.json* e uma [função Python](functions-reference-python.md) que usa a associação. A função grava mensagens de log quando registros do Cosmos DB são modificados.

Aqui estão os dados de associação no arquivo *function.json*:

```json
{
    "name": "documents",
    "type": "cosmosDBTrigger",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

Aqui está o código Python:

```python
    import logging
    import azure.functions as func


    def main(documents: func.DocumentList) -> str:
        if documents:
            logging.info('First document Id modified: %s', documents[0]['id'])
```

# <a name="java"></a>[Java](#tab/java)

Esta função é invocada quando há inserções ou atualizações no banco de dados e coleta especificados.

```java
    @FunctionName("cosmosDBMonitor")
    public void cosmosDbProcessor(
        @CosmosDBTrigger(name = "items",
            databaseName = "ToDoList",
            collectionName = "Items",
            leaseCollectionName = "leases",
            createLeaseCollectionIfNotExists = true,
            connectionStringSetting = "AzureCosmosDBConnection") String[] items,
            final ExecutionContext context ) {
                context.getLogger().info(items.length + "item(s) is/are changed.");
            }
```


Na biblioteca de runtime de funções [Java](/java/api/overview/azure/functions/runtime), use a anotação `@CosmosDBTrigger` nos parâmetros cujo valor seria proveniente do Cosmos DB.  Essa anotação pode ser usada com tipos nativos do Java, POJOs ou valores que permitem valor nulos usando `Optional<T>`.

---

## <a name="attributes-and-annotations"></a>Atributos e anotações

# <a name="c"></a>[C #](#tab/csharp)

Em [bibliotecas de classes de C#](functions-dotnet-class-library.md), utilize o atributo [CosmosDBTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs).

O construtor do atributo toma o nome do banco de dados e o nome da coleção. Para obter informações sobre essas configurações e outras propriedades que podem ser configuradas, consulte [Gatilho – configurações](#configuration). Aqui está um exemplo de atributo `CosmosDBTrigger` em uma assinatura de método:

```csharp
    [FunctionName("DocumentUpdates")]
    public static void Run(
        [CosmosDBTrigger("database", "collection", ConnectionStringSetting = "myCosmosDB")]
    IReadOnlyList<Document> documents,
        ILogger log)
    {
        ...
    }
```

Para um exemplo completo, consulte [Trigger](#example).

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

Os atributos não são suportados pelo script C#.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Os atributos não são suportados pelo JavaScript.

# <a name="python"></a>[Python](#tab/python)

Os atributos não são suportados pelo Python.

# <a name="java"></a>[Java](#tab/java)

A partir da biblioteca de tempo `@CosmosDBInput` de [execução de funções Java,](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)use a anotação em parâmetros que lêem dados do Cosmos DB.

---

## <a name="configuration"></a>Configuração

A tabela a seguir explica as propriedades de configuração de `CosmosDBTrigger` vinculação que você definiu no arquivo *function.json* e no atributo.

|Propriedade function.json | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**type** | n/d | Deve ser definido como `cosmosDBTrigger`. |
|**direction** | n/d | Deve ser definido como `in`. Esse parâmetro é definido automaticamente quando você cria o gatilho no portal do Azure. |
|**name** | n/d | O nome da variável usado no código de função que representa a lista de documentos com alterações. |
|**conexãoConfiguração de string**|**ConnectionStringSetting** | O nome de uma configuração de aplicativo que contém a cadeia de conexão usada para conectar-se à conta do Azure Cosmos DB que está sendo monitorada. |
|**databaseName**|**Databasename**  | O nome do banco de dados do Azure Cosmos DB com a coleção que está sendo monitorada. |
|**Nomedacoleta** |**CollectionName** | O nome da coleção que está sendo monitorada. |
|**leaseConexStringSetting** | **LeaseConnectionStringSetting** | (Opcional) O nome de uma configuração de aplicativo que contém a seqüência de conexão para a conta Azure Cosmos DB que detém a coleção de locação. Quando não definido, o valor `connectionStringSetting` é usado. Esse parâmetro é definido automaticamente quando a associação é criada no portal. A cadeia de conexão da coleção de concessões deve ter permissões de gravação.|
|**leaseDatabaseName** |**LeaseDatabaseName** | (Opcional) O nome do banco de dados que contém a coleção usada para armazenar as concessões. Quando não definido, o valor da configuração `databaseName` é usado. Esse parâmetro é definido automaticamente quando a associação é criada no portal. |
|**leaseCollectionName** | **LeaseCollectionName** | (Opcional) O nome da coleção usada para armazenar as concessões. Quando não definido, o valor `leases` é usado. |
|**createLeaseCollectionIfNotExist** | **CreateLeaseCollectionIfNotExists** | (Opcional) Quando definido como `true`, a coleção de concessões é criada automaticamente quando ela ainda não existe. O valor padrão é `false`. |
|**leasesCollectionThroughput**| **LeasesCollectionThroughput**| (Opcional) Define o número de Unidades de Solicitação a serem atribuídas quando a coleta de arrendamentos é criada. Esta configuração só `createLeaseCollectionIfNotExists` é `true`usada quando está definida como . Esse parâmetro é definido automaticamente quando a associação é criada usando o portal.
|**leaseCollectionPrefix**| **LeaseCollectionPrefix**| (Opcional) Quando definido, o valor é adicionado como um prefixo às locações criadas na coleção Delocação para esta Função. O uso de um prefixo permite que duas funções azure separadas compartilhem a mesma coleção de Locação usando prefixos diferentes.
|**feedPollDelay**| **FeedPollDelay**| (Opcional) O tempo (em milissegundos) para o atraso entre a votação de uma partição para novas alterações no feed, depois que todas as alterações atuais são drenadas. O padrão é de 5.000 milissegundos, ou 5 segundos.
|**leaseAcquireInterval**| **LeaseAcquireInterval**| (Opcional) Quando definido, ele define, em milissegundos, o intervalo para disparar uma tarefa para computar se as partições são distribuídas uniformemente entre as instâncias de host conhecidas. O padrão é 13000 (13 segundos).
|**leaseExpirationInterval**| **LeaseExpirationInterval**| (Opcional), Quando definido, ele define, em milissegundos, o intervalo para o qual a concessão é tomada em uma concessão que representa uma partição. Se a concessão não for renovada dentro deste intervalo, ela será expirada e a propriedade da partição será movida para outra instância. O padrão é 60000 (60 segundos).
|**leaseRenewInterval**| **LeaseRenewInterval**| (Opcional) Quando definido, ele define, em milissegundos, o intervalo de renovação para todas as concessões para partições atualmente mantidas por uma instância. O padrão é 17000 (17 segundos).
|**checkpointFrequency**| **CheckpointFrequency**| (Opcional) Quando definido, ele define, em milissegundos, o intervalo entre os pontos de verificação de concessão. O padrão é sempre após cada chamada de Função.
|**maxItemsPerInvocation**| **MaxItemsPerInvocação**| (Opcional) Quando definido, esta propriedade define o número máximo de itens recebidos por chamada função. Se as operações na coleta monitorada forem realizadas através de procedimentos armazenados, o [escopo da transação](../cosmos-db/stored-procedures-triggers-udfs.md#transactions) será preservado ao ler itens do Feed de alteração. Como resultado, o número de itens recebidos pode ser maior do que o valor especificado para que os itens alterados pela mesma transação sejam devolvidos como parte de um lote atômico.
|**startFromBeginning**| **StartFromBeginning**| (Opcional) Esta opção diz ao Trigger para ler alterações desde o início do histórico de alterações da coleção em vez de começar no momento atual. A leitura desde o início só funciona na primeira vez que o Trigger é iniciado, pois nas corridas subsequentes, os pontos de verificação já estão armazenados. Definir essa `true` opção para quando há arrendamentos já criados não tem efeito. |
|**preferredLocalizaçãos**| **PreferredLocalizaçãos**| (Opcional) Define locais preferenciais (regiões) para contas de banco de dados replicadas geo-replicadas no serviço Azure Cosmos DB. Os valores devem ser separados por comma. Por exemplo, "Leste dos EUA, Centro-Sul dos EUA, Norte da Europa". |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Uso

O gatilho requer uma segunda coleção que ele usa para armazenar _concessões_ sobre as partições. Tanto a coleção que está sendo monitorada quanto a coleção que contém as concessões devem estar disponíveis para o gatilho funcionar.

>[!IMPORTANT]
> Se várias funções estiverem configuradas para usar um gatilho do Cosmos DB para a mesma coleção, cada uma das funções deverá usar uma coleção de concessões dedicada ou especificar uma diferente `LeaseCollectionPrefix` para cada função. Caso contrário, apenas uma das funções será disparada. Para obter informações sobre o prefixo, veja a [seção de Configuração](#configuration).

O gatilho não indica se um documento foi atualizado ou inserido, ele fornece apenas o documento em si. Se você precisa lidar com inserções e atualizações de forma diferente, você pode fazer isso com a implementação de campos de carimbo de hora de inserção ou atualização.

## <a name="next-steps"></a>Próximas etapas

- [Leia um documento Azure Cosmos DB (vinculação de entrada)](./functions-bindings-cosmosdb-v2-input.md)
- [Salvar alterações em um documento DB do Azure Cosmos (vinculação de saída)](./functions-bindings-cosmosdb-v2-output.md)