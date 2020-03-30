---
title: Gatilho de armazenamento da fila do Azure para funções do Azure
description: Aprenda a executar uma função Azure como alterações de dados de armazenamento do Azure Fila.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 74ca984232bef979062221a451d0ee10a6965bc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277369"
---
# <a name="azure-queue-storage-trigger-for-azure-functions"></a>Gatilho de armazenamento da fila do Azure para funções do Azure

O gatilho de armazenamento na fila executa uma função à medida que as mensagens são adicionadas ao armazenamento do Azure Fila.

## <a name="encoding"></a>Codificação

O Functions espera uma cadeia de caracteres codificada *base64*. Todos os ajustes ao tipo de codificação (para preparar os dados como uma cadeia de caracteres codificada *base64*) precisam ser implementados no serviço de chamada.

## <a name="example"></a>Exemplo

Use o gatilho de fila para iniciar uma função quando um novo item é recebido em uma fila. A mensagem da fila é fornecida como entrada para a função.

# <a name="c"></a>[C #](#tab/csharp)

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que consulta a fila `myqueue-items` e grava um log cada vez que um item de fila é processado.

```csharp
public static class QueueFunctions
{
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
    }
}
```

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

O exemplo a seguir mostra uma associação de gatilho de fila em um arquivo *function.json* e código [script C# (.csx)](functions-reference-csharp.md) que usa a associação. A função controla a `myqueue-items` fila e grava um log cada vez que um item de fila é processado.

Aqui está o arquivo *function.json*:

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ]
}
```

A seção [configuração](#configuration) explica essas propriedades.

Aqui está o código de script do C#:

```csharp
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.Extensions.Logging;
using Microsoft.WindowsAzure.Storage.Queue;
using System;

public static void Run(CloudQueueMessage myQueueItem, 
    DateTimeOffset expirationTime, 
    DateTimeOffset insertionTime, 
    DateTimeOffset nextVisibleTime,
    string queueTrigger,
    string id,
    string popReceipt,
    int dequeueCount,
    ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem.AsString}\n" +
        $"queueTrigger={queueTrigger}\n" +
        $"expirationTime={expirationTime}\n" +
        $"insertionTime={insertionTime}\n" +
        $"nextVisibleTime={nextVisibleTime}\n" +
        $"id={id}\n" +
        $"popReceipt={popReceipt}\n" + 
        $"dequeueCount={dequeueCount}");
}
```

A seção [uso](#usage) explica `myQueueItem`, que é chamado pela `name` propriedade function.json.  A [seção de metadados de mensagem](#message-metadata) explica todas as outras variáveis mostradas.

# <a name="javascript"></a>[Javascript](#tab/javascript)

O exemplo a seguir mostra uma associação de gatilho de fila em um arquivo *function.json* e uma [função JavaScript](functions-reference-node.md) que usa a associação. A função controla a `myqueue-items` fila e grava um log cada vez que um item de fila é processado.

Aqui está o arquivo *function.json*:

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ]
}
```

A seção [configuração](#configuration) explica essas propriedades.

> [!NOTE]
> O parâmetro nome é refletido como `context.bindings.<name>` no código JavaScript que contém a carga útil do item da fila. Essa carga útil também é passada como o segundo parâmetro para a função.

Aqui está o código JavaScript:

```javascript
module.exports = async function (context, message) {
    context.log('Node.js queue trigger function processed work item', message);
    // OR access using context.bindings.<name>
    // context.log('Node.js queue trigger function processed work item', context.bindings.myQueueItem);
    context.log('expirationTime =', context.bindingData.expirationTime);
    context.log('insertionTime =', context.bindingData.insertionTime);
    context.log('nextVisibleTime =', context.bindingData.nextVisibleTime);
    context.log('id =', context.bindingData.id);
    context.log('popReceipt =', context.bindingData.popReceipt);
    context.log('dequeueCount =', context.bindingData.dequeueCount);
    context.done();
};
```

A seção [uso](#usage) explica `myQueueItem`, que é chamado pela `name` propriedade function.json.  A [seção de metadados de mensagem](#message-metadata) explica todas as outras variáveis mostradas.

# <a name="python"></a>[Python](#tab/python)

O exemplo a seguir demonstra como ler uma mensagem de fila passada para uma função através de um gatilho.

Um gatilho de fila de armazenamento é definido em `queueTrigger` *function.json* onde o *tipo* é definido para .

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "msg",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "messages",
      "connection": "AzureStorageQueuesConnectionString"
    }
  ]
}
```

O código * _ \_init_\_.py* declara `func.ServiceBusMessage`um parâmetro como , o que permite que você leia a mensagem de fila em sua função.

```python
import logging
import json

import azure.functions as func

def main(msg: func.QueueMessage):
    logging.info('Python queue trigger function processed a queue item.')

    result = json.dumps({
        'id': msg.id,
        'body': msg.get_body().decode('utf-8'),
        'expiration_time': (msg.expiration_time.isoformat()
                            if msg.expiration_time else None),
        'insertion_time': (msg.insertion_time.isoformat()
                           if msg.insertion_time else None),
        'time_next_visible': (msg.time_next_visible.isoformat()
                              if msg.time_next_visible else None),
        'pop_receipt': msg.pop_receipt,
        'dequeue_count': msg.dequeue_count
    })

    logging.info(result)
```

# <a name="java"></a>[Java](#tab/java)

O exemplo java a seguir mostra uma função de gatilho de fila `myqueuename`de armazenamento, que registra a mensagem ativada colocada na fila .

 ```java
 @FunctionName("queueprocessor")
 public void run(
    @QueueTrigger(name = "msg",
                   queueName = "myqueuename",
                   connection = "myconnvarname") String message,
     final ExecutionContext context
 ) {
     context.getLogger().info(message);
 }
 ```

 ---

## <a name="attributes-and-annotations"></a>Atributos e anotações

# <a name="c"></a>[C #](#tab/csharp)

Em [bibliotecas de classes C#](functions-dotnet-class-library.md), use os seguintes atributos para configurar um gatilho de fila:

* [QueueTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues/QueueTriggerAttribute.cs)

  O construtor do atributo usa o nome da fila para monitorar, conforme mostrado no exemplo a seguir:

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items")] string myQueueItem, 
      ILogger log)
  {
      ...
  }
  ```

  Você pode `Connection` definir a propriedade para especificar a configuração do aplicativo que contém a seqüência de conexão da conta de armazenamento a ser usada, conforme mostrado no exemplo a seguir:

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items", Connection = "StorageConnectionAppSetting")] string myQueueItem, 
      ILogger log)
  {
      ....
  }
  ```

  Para um exemplo completo, veja [o exemplo](#example).

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Oferece uma maneira de especificar a conta de armazenamento para usar. O construtor toma o nome de uma configuração de aplicativo que contenha uma cadeia de conexão de armazenamento. O atributo pode ser aplicado no nível de classe, método ou parâmetro. O exemplo a seguir mostra o nível de classe e método:

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("QueueTrigger")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ...
  }
  ```

A conta de armazenamento a ser usada é determinada na seguinte ordem:

* A propriedade `QueueTrigger` do atributo`Connection`.
* O `StorageAccount` atributo aplicado ao mesmo parâmetro do `QueueTrigger` atributo.
* O `StorageAccount` atributo aplicado à função.
* O `StorageAccount` atributo aplicado à classe.
* A configuração do aplicativo "AzureWebJobsStorage".

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

Os atributos não são suportados pelo script C#.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Os atributos não são suportados pelo JavaScript.

# <a name="python"></a>[Python](#tab/python)

Os atributos não são suportados pelo Python.

# <a name="java"></a>[Java](#tab/java)

A `QueueTrigger` anotação lhe dá acesso à fila que aciona a função. O exemplo a seguir disponibiliza a mensagem de fila para a função através do `message` parâmetro.

```java
package com.function;
import com.microsoft.azure.functions.annotation.*;
import java.util.Queue;
import com.microsoft.azure.functions.*;

public class QueueTriggerDemo {
    @FunctionName("QueueTriggerDemo")
    public void run(
        @QueueTrigger(name = "message", queueName = "messages", connection = "MyStorageConnectionAppSetting") String message,
        final ExecutionContext context
    ) {
        context.getLogger().info("Queue message: " + message);
    }
}
```

| Propriedade    | Descrição |
|-------------|-----------------------------|
|`name`       | Declara o nome do parâmetro na assinatura da função. Quando a função é acionada, o valor deste parâmetro tem o conteúdo da mensagem de fila. |
|`queueName`  | Declara o nome da fila na conta de armazenamento. |
|`connection` | Aponta para a seqüência de conexão da conta de armazenamento. |

---

## <a name="configuration"></a>Configuração

A tabela a seguir explica as propriedades de configuração de `QueueTrigger` vinculação que você definiu no arquivo *function.json* e no atributo.

|Propriedade function.json | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**type** | n/d| Deve ser definido como `queueTrigger`. Essa propriedade é definida automaticamente quando você cria o gatilho no portal do Azure.|
|**direction**| n/d | Apenas no arquivo *function.json*. Deve ser definido como `in`. Essa propriedade é definida automaticamente quando você cria o gatilho no portal do Azure. |
|**name** | n/d |O nome da variável que contém o conteúdo do item de fila no código da função.  |
|**queueName** | **Queuename**| O nome da fila a ser controlada. |
|**Conexão** | **Conexão** |O nome de uma configuração de aplicativo que contém uma cadeia de conexão de Armazenamento para usar para essa associação. Se o nome de configuração do aplicativo começar com "AzureWebJobs", você pode especificar apenas o resto do nome aqui. Por exemplo, se `connection` você definir como "MyStorage", o tempo de execução funções procurará uma configuração de aplicativo chamada "MyStorage". Se você deixar `connection` vazio, o runtime de Functions usa a cadeia de caracteres de conexão de Armazenamento padrão na configuração de aplicativo chamada `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Uso

# <a name="c"></a>[C #](#tab/csharp)

Acesse os dados da mensagem usando `string paramName`um parâmetro de método, como . É possível associar a qualquer um dos seguintes tipos:

* Objeto - o runtime do Functions desserializa um conteúdo JSON em uma instância de uma classe arbitrária definida em seu código. 
* `string`
* `byte[]`
* [CloudQueueMessage]

Se você tentar associar `CloudQueueMessage` e receber uma mensagem de erro, certifique-se de ter uma referência para [a versão correta do SDK do Armazenamento](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x).

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

Acesse os dados da mensagem usando `string paramName`um parâmetro de método, como . O `paramName` é o valor `name` especificado na propriedade de *function.json*. É possível associar a qualquer um dos seguintes tipos:

* Objeto - o runtime do Functions desserializa um conteúdo JSON em uma instância de uma classe arbitrária definida em seu código. 
* `string`
* `byte[]`
* [CloudQueueMessage]

Se você tentar associar `CloudQueueMessage` e receber uma mensagem de erro, certifique-se de ter uma referência para [a versão correta do SDK do Armazenamento](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x).

# <a name="javascript"></a>[Javascript](#tab/javascript)

A carga útil do item `context.bindings.<NAME>` `<NAME>` de fila está disponível através de onde corresponde o nome definido em *function.json*. Se a carga for JSON, o valor será desserializado em um objeto.

# <a name="python"></a>[Python](#tab/python)

Acesse a mensagem de fila através do parâmetro digitado como [QueueMessage](https://docs.microsoft.com/python/api/azure-functions/azure.functions.queuemessage?view=azure-python).

# <a name="java"></a>[Java](#tab/java)

A anotação [QueueTrigger](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.queuetrigger?view=azure-java-stable) dá acesso à mensagem de fila que acionou a função.

---

## <a name="message-metadata"></a>Metadados de mensagem

O gatilho de fila fornece várias propriedades de [metadados](./functions-bindings-expressions-patterns.md#trigger-metadata). Essas propriedades podem ser usadas como parte de expressões de associação em outras associações ou como parâmetros em seu código. As propriedades são membros da classe [CloudQueueMessage.](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage)

|Propriedade|Type|Descrição|
|--------|----|-----------|
|`QueueTrigger`|`string`|Conteúdo da fila (se for uma cadeia de caracteres válida). Se a carga de mensagem `QueueTrigger` de fila for uma string, `name` tem o mesmo valor que a variável nomeada pela propriedade em *function.json*.|
|`DequeueCount`|`int`|O número de vezes que essa mensagem foi removida da fila.|
|`ExpirationTime`|`DateTimeOffset`|A hora em que a mensagem expira.|
|`Id`|`string`|ID da mensagem da fila.|
|`InsertionTime`|`DateTimeOffset`|A hora em que a mensagem foi adicionada à fila.|
|`NextVisibleTime`|`DateTimeOffset`|A hora em que a mensagem estará visível.|
|`PopReceipt`|`string`|Recebimento pop da mensagem.|

## <a name="poison-messages"></a>Mensagens venenosas

Quando uma função do gatilho de fila falhar, o Azure Functions repetirá essa função até cinco vezes para uma determinada mensagem da fila, incluindo a primeira tentativa. Se todas as cinco tentativas falharem, o tempo de execução das funções adicionará uma mensagem a uma fila chamada * &lt;originalquecha>-veneno*. Você pode gravar uma função para processar as mensagens da fila de mensagens suspeitas registrando-as ou enviando uma notificação de que a atenção manual é necessária.

Para tratar mensagens suspeitas manualmente, verifique o [dequeueCount](#message-metadata) da mensagem de fila.

## <a name="polling-algorithm"></a>Algoritmo de sondagem

O gatilho de fila implementa um algoritmo exponencial aleatório de retirada para reduzir o efeito de sondagem de fila ociosa nos custos das transações de armazenamento.

O algoritmo usa a seguinte lógica:

- Quando uma mensagem é encontrada, o tempo de execução espera dois segundos e, em seguida, verifica se há outra mensagem
- Quando nenhuma mensagem é encontrada, ela espera cerca de quatro segundos antes de tentar novamente.
- Após subsequentes tentativas falhas para obter uma mensagem da fila, o tempo de espera continua a aumentar até atingir o tempo de espera máximo, cujo padrão é um minuto.
- O tempo de espera máximo é configurável por meio da propriedade `maxPollingInterval` no [arquivo host.json](functions-host-json.md#queues).

Para o desenvolvimento local, o intervalo máximo de votação é de dois segundos.

Em relação ao faturamento, o tempo gasto na votação pelo tempo de execução é "gratuito" e não conta do que conta.

## <a name="concurrency"></a>Concorrência

Quando há várias mensagens de fila aguardando, o gatilho de fila recupera um lote de mensagens e invoca as instâncias de função ao mesmo tempo para processá-las. Por padrão, o tamanho do lote é 16. Quando o número que está sendo processado chega até 8, o runtime obtém outro lote e começa a processar as mensagens. Portanto, o número máximo de mensagens simultâneas que estão sendo processadas por função em uma máquina virtual (VM) é 24. Esse limite se aplica separadamente a cada função acionada por fila em cada VM. Se aplicativo de função for escalado horizontalmente para várias VMs, cada VM aguardará gatilhos e tentará executar funções. Por exemplo, se um aplicativo de função for escalado horizontalmente para 3 VMs, o número de máximo padrão de instâncias simultâneas de uma função acionada por fila será 72.

O tamanho do lote e o limite para obtenção de um novo lote são configuráveis no [arquivo host.json](functions-host-json.md#queues). Se quiser minimizar a execução paralela para funções acionadas por fila em um aplicativo de função, você poder definir o tamanho do lote para 1. Essa configuração elimina a simultaneidade, desde que seu aplicativo de função seja executado em uma única máquina virtual (VM). 

O gatilho de fila impede automaticamente que uma função processe uma mensagem da fila várias vezes; as funções não precisam ser escritas para ser idempotentes.

## <a name="hostjson-properties"></a>propriedades host.json

O arquivo [host.json](functions-host-json.md#queues) contém configurações que controlam o comportamento de gatilho de fila. Consulte a seção [de configurações host.json](functions-bindings-storage-queue-output.md#hostjson-settings) para obter detalhes sobre as configurações disponíveis.

## <a name="next-steps"></a>Próximas etapas

- [Escrever mensagens de armazenamento na fila (vinculação de saída)](./functions-bindings-storage-blob-output.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage
