---
title: Gatilho de armazenamento Do Zure Blob para funções do Azure
description: Aprenda a executar uma função Azure como alterações de dados de armazenamento Do Azure Blob.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: 61fbaf37577efdab0b147d437ae78fc4df0764cb
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084950"
---
# <a name="azure-blob-storage-trigger-for-azure-functions"></a>Gatilho de armazenamento Do Zure Blob para funções do Azure

O gatilho de armazenamento de Blob inicia uma função quando é detectado um blob novo ou atualizado. O conteúdo da bolha é fornecido como [entrada para a função](./functions-bindings-storage-blob-input.md).

O gatilho de armazenamento Do Azure Blob requer uma conta de armazenamento de uso geral. Para usar uma conta somente blob, ou se sua aplicação tiver necessidades especializadas, revise as alternativas para usar este gatilho.

Para obter informações sobre detalhes de configuração e configuração, consulte a [visão geral](./functions-bindings-storage-blob.md).

## <a name="alternatives"></a>Alternativas

### <a name="event-grid-trigger"></a>Gatilho da grade de eventos

O [gatilho Event Grid](functions-bindings-event-grid.md) também tem suporte embutido para eventos [blob](../storage/blobs/storage-blob-event-overview.md). Use a Grade de Eventos em vez do disparador de armazenamento de Blobs para os seguintes cenários:

- **Contas de armazenamento somente blob**: As [contas de armazenamento somente blob](../storage/common/storage-account-overview.md#types-of-storage-accounts) são suportadas para vinculações de entrada e saída de bolha, mas não para gatilhos blob.

- **Alta escala**: A alta escala pode ser definida como recipientes que possuem mais de 100.000 blobs neles ou contas de armazenamento que têm mais de 100 atualizações por segundo.

- **Minimizando a latência**: Se o seu aplicativo de função estiver no plano de consumo, pode haver até um atraso de 10 minutos no processamento de novas bolhas se um aplicativo de função ficou ocioso. Para evitar essa latência, você pode alternar para um plano do serviço de aplicativo com o Always On habilitado. Você também pode usar um [gatilho da Grade de Eventos](functions-bindings-event-grid.md) com sua conta de armazenamento de Blob. Para obter um exemplo, confira o [tutorial da Grade de Eventos](../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json).

Veja o [redimensionamento](../event-grid/resize-images-on-storage-blob-upload-event.md) da imagem com o tutorial da Grade de Eventos de um exemplo da Grade de Eventos.

### <a name="queue-storage-trigger"></a>Gatilho de armazenamento de filas

Outra abordagem para processar bolhas é escrever mensagens de fila que correspondam a blobs sendo criadas ou modificadas e, em seguida, usar um [gatilho de armazenamento de fila](./functions-bindings-storage-queue.md) para começar a processar.

## <a name="example"></a>Exemplo

# <a name="c"></a>[C#](#tab/csharp)

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que grava um log quando um blob é adicionado ou atualizado no contêiner `samples-workitems`.

```csharp
[FunctionName("BlobTriggerCSharp")]        
public static void Run([BlobTrigger("samples-workitems/{name}")] Stream myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

A cadeia de caracteres `{name}` no caminho do disparador de blob `samples-workitems/{name}` cria uma [expressão de associação](./functions-bindings-expressions-patterns.md) que você pode usar no código de função para acessar o nome de arquivo do blob disparando. Para obter mais informações, consulte [Padrões de nome do blob](#blob-name-patterns) a seguir neste artigo.

Para obter mais `BlobTrigger` informações sobre o atributo, consulte [atributos e anotações](#attributes-and-annotations).

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

O exemplo a seguir mostra uma vinculação do gatilho blob em um arquivo e código *function.json* que usa a vinculação. A função grava um registro quando uma bolha `samples-workitems` é adicionada ou atualizada no [recipiente](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources).

Aqui estão os dados de associação no arquivo *function.json*:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

A cadeia de caracteres `{name}` no caminho do disparador de blob `samples-workitems/{name}` cria uma [expressão de associação](./functions-bindings-expressions-patterns.md) que você pode usar no código de função para acessar o nome de arquivo do blob disparando. Para obter mais informações, consulte [Padrões de nome do blob](#blob-name-patterns) a seguir neste artigo.

Para obter mais informações sobre as propriedades do arquivo *function.json*, consulte a seção [Configuração](#configuration) que explica essas propriedades.

Aqui está o código script C# que associa a um `Stream`:

```cs
public static void Run(Stream myBlob, string name, ILogger log)
{
   log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Aqui está o código script C# que associa a um `CloudBlockBlob`:

```cs
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Blob;

public static void Run(CloudBlockBlob myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name}\nURI:{myBlob.StorageUri}");
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O exemplo a seguir mostra uma associação de gatilho de blob em um arquivo *function.json* e [código JavaScript](functions-reference-node.md) que usa a associação. A função grava um log quando um blob é adicionado ou atualizado no `samples-workitems` contêiner.

Aqui está o arquivo *function.json*:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

A cadeia de caracteres `{name}` no caminho do disparador de blob `samples-workitems/{name}` cria uma [expressão de associação](./functions-bindings-expressions-patterns.md) que você pode usar no código de função para acessar o nome de arquivo do blob disparando. Para obter mais informações, consulte [Padrões de nome do blob](#blob-name-patterns) a seguir neste artigo.

Para obter mais informações sobre as propriedades do arquivo *function.json*, consulte a seção [Configuração](#configuration) que explica essas propriedades.

Aqui está o código JavaScript:

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

O exemplo a seguir mostra uma associação de disparo de blob em um arquivo *function.json* e [código Python](functions-reference-python.md) que usa a associação. A função grava um registro quando uma bolha `samples-workitems` é adicionada ou atualizada no [recipiente](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources).

Aqui está o arquivo *function.json*:

```json
{
    "scriptFile": "__init__.py",
    "disabled": false,
    "bindings": [
        {
            "name": "myblob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

A cadeia de caracteres `{name}` no caminho do disparador de blob `samples-workitems/{name}` cria uma [expressão de associação](./functions-bindings-expressions-patterns.md) que você pode usar no código de função para acessar o nome de arquivo do blob disparando. Para obter mais informações, consulte [Padrões de nome do blob](#blob-name-patterns) a seguir neste artigo.

Para obter mais informações sobre as propriedades do arquivo *function.json*, consulte a seção [Configuração](#configuration) que explica essas propriedades.

Aqui está o código Python:

```python
import logging
import azure.functions as func


def main(myblob: func.InputStream):
    logging.info('Python Blob trigger function processed %s', myblob.name)
```

# <a name="java"></a>[Java](#tab/java)

Esta função grava um registro quando uma bolha `myblob` é adicionada ou atualizada no recipiente.

```java
@FunctionName("blobprocessor")
public void run(
  @BlobTrigger(name = "file",
               dataType = "binary",
               path = "myblob/{name}",
               connection = "MyStorageAccountAppSetting") byte[] content,
  @BindingName("name") String filename,
  final ExecutionContext context
) {
  context.getLogger().info("Name: " + filename + " Size: " + content.length + " bytes");
}
```

---

## <a name="attributes-and-annotations"></a>Atributos e anotações

# <a name="c"></a>[C#](#tab/csharp)

Em [bibliotecas de classe C#](functions-dotnet-class-library.md), use os seguintes atributos para configurar um gatilho de blob:

* [BlobTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobTriggerAttribute.cs)

  O construtor do atributo usa uma cadeia de caracteres de caminho que indica o contêiner para inspecionar e, opcionalmente, um [padrão de nome de blob](#blob-name-patterns). Aqui está um exemplo:

  ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}")] Stream image,
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
  ```

  Você pode definir a `Connection` propriedade para especificar a conta de armazenamento para usar, conforme mostrado no exemplo a seguir:

   ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}", Connection = "StorageConnectionAppSetting")] Stream image,
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
   ```

  Para um exemplo completo, consulte [Exemplo de gatilho](#example).

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Oferece uma maneira de especificar a conta de armazenamento para usar. O construtor toma o nome de uma configuração de aplicativo que contenha uma cadeia de conexão de armazenamento. O atributo pode ser aplicado no nível de classe, método ou parâmetro. O exemplo a seguir mostra o nível de classe e método:

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("BlobTrigger")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ....
  }
  ```

A conta de armazenamento a ser usada é determinada na seguinte ordem:

* A propriedade `BlobTrigger` do atributo`Connection`.
* O `StorageAccount` atributo aplicado ao mesmo parâmetro do `BlobTrigger` atributo.
* O `StorageAccount` atributo aplicado à função.
* O `StorageAccount` atributo aplicado à classe.
* A conta de armazenamento padrão para a função de aplicativo (configuração de aplicativo "AzureWebJobsStorage").

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

Os atributos não são suportados pelo script C#.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Os atributos não são suportados pelo JavaScript.

# <a name="python"></a>[Python](#tab/python)

Os atributos não são suportados pelo Python.

# <a name="java"></a>[Java](#tab/java)

O `@BlobTrigger` atributo é usado para lhe dar acesso à bolha que desencadeou a função. Consulte o exemplo do [gatilho](#example) para obter detalhes.

---

## <a name="configuration"></a>Configuração

A tabela a seguir explica as propriedades de configuração de `BlobTrigger` vinculação que você definiu no arquivo *function.json* e no atributo.

|Propriedade function.json | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d | Deve ser definido como `blobTrigger`. Essa propriedade é definida automaticamente quando você cria o gatilho no portal do Azure.|
|**Direção** | n/d | Deve ser definido como `in`. Essa propriedade é definida automaticamente quando você cria o gatilho no portal do Azure. As exceções são mencionadas na seção [uso](#usage). |
|**name** | n/d | O nome da variável que representa o blob no código de função. |
|**path** | **BlobPath** |O [contêiner](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources) para monitorar.  Pode ser um [padrão de nome de blob](#blob-name-patterns). |
|**Conexão** | **Conexão** | O nome de uma configuração de aplicativo que contém uma cadeia de conexão de Armazenamento para usar para essa associação. Se o nome de configuração do aplicativo começar com "AzureWebJobs", você pode especificar apenas o resto do nome aqui. Por exemplo, se você configurar `connection` para “MyStorage”, o runtime do Functions procura por uma configuração de aplicativo que esteja nomeada “AzureWebJobsMyStorage." Se você deixar `connection` vazio, o runtime de Functions usa a cadeia de caracteres de conexão de Armazenamento padrão na configuração de aplicativo chamada `AzureWebJobsStorage`.<br><br>A cadeia de conexão deve ser uma conta de armazenamento para uso geral e não uma [conta de Armazenamento de Blobs](../storage/common/storage-account-overview.md#types-of-storage-accounts).|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Uso

# <a name="c"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-trigger.md)]

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-trigger.md)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Acessar dados blob usando `context.bindings.<NAME>` onde `<NAME>` corresponde ao valor definido em *function.json*.

# <a name="python"></a>[Python](#tab/python)

Acesse os dados blob através do parâmetro digitado como [InputStream](https://docs.microsoft.com/python/api/azure-functions/azure.functions.inputstream?view=azure-python). Consulte o exemplo do [gatilho](#example) para obter detalhes.

# <a name="java"></a>[Java](#tab/java)

O `@BlobTrigger` atributo é usado para lhe dar acesso à bolha que desencadeou a função. Consulte o exemplo do [gatilho](#example) para obter detalhes.

---

## <a name="blob-name-patterns"></a>Padrões de nomes blob

Você pode especificar um padrão de nome de blob na `path` propriedade em *function.json* ou no `BlobTrigger` construtor de atributo. O nome padrão pode ser uma [expressão de associação ou filtro](./functions-bindings-expressions-patterns.md). As seções a seguir fornecem exemplos.

### <a name="get-file-name-and-extension"></a>Obtenha o nome de arquivo e extensão

O exemplo a seguir mostra como associar ao nome do arquivo de blob e extensão separadamente:

```json
"path": "input/{blobname}.{blobextension}",
```

Se um blob é nomeado *original-Blob1.txt* o valor das variáveis `blobname` e `blobextension` no código de função é *original-Blob1* e *txt*.

### <a name="filter-on-blob-name"></a>Filtre por nome de blob

O exemplo a seguir gatilha apenas em blobs no `input` contêiner que iniciam com a cadeia de caracteres "original-":

```json
"path": "input/original-{name}",
```

Se o nome do blob for *original-Blob1.txt*, o valor da `name` variável no código da função é `Blob1`.

### <a name="filter-on-file-type"></a>Filtre por tipo de arquivo

O exemplo a seguir é disparado apenas em arquivos *.png*:

```json
"path": "samples/{name}.png",
```

### <a name="filter-on-curly-braces-in-file-names"></a>Filtre em chaves em nomes de arquivos

Para procurar as chaves em nomes de arquivos, escape as chaves usando duas chaves. O exemplo a seguir filtra por blobs que têm chaves no nome:

```json
"path": "images/{{20140101}}-{name}",
```

Se a bolha for nomeada `name` * {20140101}-soundfile.mp3*, o valor variável no código de função é *soundfile.mp3*.

## <a name="metadata"></a>Metadados

# <a name="c"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-metadata.md)]

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-metadata.md)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
module.exports = function (context, myBlob) {
    context.log("Full blob path:", context.bindingData.blobTrigger);
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

Metadados não estão disponíveis no Python.

# <a name="java"></a>[Java](#tab/java)

Metadados não estão disponíveis em Java.

---

## <a name="blob-receipts"></a>Recebimentos de blob

O Azure Functions runtime garante que nenhuma função de gatilho de blob seja chamada mais de uma vez para o mesmo blob novo ou atualizado. Para determinar se uma versão de determinado blob foi processada, ele mantém os *recebimentos de blob*.

O Azure Functions armazena recibos do blob em um contêiner denominado *azure-webjobs-hosts* na conta de armazenamento do Azure do seu aplicativo de funções (definido na configuração do aplicativo `AzureWebJobsStorage`). Um recebimento de blob tem as seguintes informações:

* A função acionada*&lt; *(" nome do aplicativo de função>. Funções. nome da função>", por exemplo: "MyFunctionApp.Functions.CopyBlob") * &lt; *
* O nome do contêiner
* O tipo de blob ("BlockBlob" ou "PageBlob")
* O nome do blob
* O ETag (um identificador de versão de blob, por exemplo: "0x8D1DC6E70A277EF")

Para forçar o reprocessamento de um blob, exclua manualmente o recebimento desse blob do contêiner *azure-webjobs-hosts*. Embora o reprocessamento possa não ocorrer imediatamente, é garantido que ocorra em um momento posterior. Para reprocessar imediatamente, o *blob scaninfo* em *azure-webjobs-hosts/blobscaninfo* pode ser atualizado. Quaisquer bolhas com um último carimbo `LatestScan` de tempo modificado após a propriedade serão digitalizadas novamente.

## <a name="poison-blobs"></a>Bolhas venenosas

Quando uma função de gatilho de blob falhar para um determinado blob, o Azure Functions repete essa função até cinco vezes por padrão.

Se todas as cinco tentativas falharem, o Azure Functions adiciona uma mensagem para uma fila de armazenamento denominada *webjobs-blobtrigger-poison*. O número máximo de novas tentativas é configurável. A mesma MaxDequeueCount é usada para manipular blob suspeitos e manipular mensagens de filas suspeitas. A mensagem da fila para blobs suspeitos é um objeto JSON que contém as seguintes propriedades:

* FunctionId (no * &lt;nome *do aplicativo de função de formato>. Funções. nome da função>) * &lt; *
* BlobType ("BlockBlob" ou "PageBlob")
* ContainerName
* BlobName
* ETag (um identificador de versão de blob, por exemplo: "0x8D1DC6E70A277EF")

## <a name="concurrency-and-memory-usage"></a>Conmoeda e uso da memória

O gatilho de blob usa uma fila internamente, portanto, o número máximo de invocações de função concorrente é controlado pela [configuração de filas em host.json](functions-host-json.md#queues). As configurações padrão limitam a concorrência a 24 invocações. Esse limite aplica-se separadamente a cada função que usa um gatilho de blob.

[O plano Consumo](functions-scale.md#how-the-consumption-and-premium-plans-work) limita um aplicativo de função em uma máquina virtual (VM) a 1,5 GB de memória. A memória é usada por cada instância de execução de execução simultânea e pelo próprio runtime de Funções. Se uma função disparada por blob carregar todo o blob na memória, a memória máxima usada por essa função apenas para blobs será tamanho máximo de blob 24 *. Por exemplo, um aplicativo de funções com três funções disparadas por blob e as configurações padrão teriam uma concorrência máxima por VM de 3*24 = 72 invocações de função.

As funções de JavaScript e Java carregam todo o blob na memória, e as funções C# fazem isso se você associar a `string`, `Byte[]` ou POCO.

## <a name="polling"></a>Sondagem

A pesquisa funciona como um híbrido entre inspecionar troncos e executar varreduras periódicas de contêineres. Blobs são escaneados em grupos de 10.000 de cada vez com um token de continuação usado entre intervalos.

> [!WARNING]
> Além disso, [logs de armazenamento são criados da "melhor forma dentro do possível"](/rest/api/storageservices/About-Storage-Analytics-Logging). Não há nenhuma garantia de que todos os eventos são capturados. Sob algumas condições, logs poderão ser perdidos.
> 
> Se você precisar de um processamento de blob mais rápido ou confiável, crie uma [mensagem de fila](../storage/queues/storage-dotnet-how-to-use-queues.md) ao criar o blob. Em seguida, use um [gatilho de fila](functions-bindings-storage-queue.md) em vez de um gatilho de blob para processar o blob. Outra opção é usar a Grade de Eventos; consulte o tutorial [Automatize redimensionamento de imagens carregadas usando a Grade de Eventos](../event-grid/resize-images-on-storage-blob-upload-event.md).
>

## <a name="next-steps"></a>Próximas etapas

- [Leia os dados de armazenamento blob quando uma função é executada](./functions-bindings-storage-blob-input.md)
- [Escreva dados de armazenamento blob de uma função](./functions-bindings-storage-blob-output.md)
