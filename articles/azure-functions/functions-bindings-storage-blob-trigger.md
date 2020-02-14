---
title: Gatilho do armazenamento de BLOBs do Azure para Azure Functions
description: Saiba como executar uma função do Azure como alterações de dados do armazenamento de BLOBs do Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: 0cdff3ac6eb2faed0c0b6b8796fdb3b6b0411018
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77202080"
---
# <a name="azure-blob-storage-trigger-for-azure-functions"></a>Gatilho do armazenamento de BLOBs do Azure para Azure Functions

O gatilho de armazenamento de Blob inicia uma função quando é detectado um blob novo ou atualizado. O conteúdo do blob é fornecido como [entrada para a função](./functions-bindings-storage-blob-input.md).

O gatilho do armazenamento de BLOBs do Azure requer uma conta de armazenamento de uso geral. Para usar uma conta somente de BLOB ou se seu aplicativo tiver necessidades especializadas, examine as alternativas para usar esse gatilho.

Para obter informações sobre configuração e detalhes de configuração, consulte a [visão geral](./functions-bindings-storage-blob.md).

## <a name="alternatives"></a>Alternativas

### <a name="event-grid-trigger"></a>Gatilho de grade de eventos

O [gatilho de grade de eventos](functions-bindings-event-grid.md) também tem suporte interno para [eventos de blob](../storage/blobs/storage-blob-event-overview.md). Use a Grade de Eventos em vez do disparador de armazenamento de Blobs para os seguintes cenários:

- **Contas de armazenamento somente blob**: [as contas de armazenamento somente blob](../storage/common/storage-account-overview.md#types-of-storage-accounts) têm suporte para associações de entrada e saída de BLOB, mas não para gatilhos de BLOB.

- **Alta escala**: a alta escala pode ser definida livremente como contêineres que têm mais de 100.000 BLOBs ou contas de armazenamento que têm mais de 100 atualizações de blob por segundo.

- **Minimizando a latência**: se seu aplicativo de funções estiver no plano de consumo, poderá haver um atraso de até 10 minutos no processamento de novos BLOBs se um aplicativo de funções tiver ficado ocioso. Para evitar essa latência, você pode alternar para um plano do serviço de aplicativo com o Always On habilitado. Você também pode usar um [gatilho da Grade de Eventos](functions-bindings-event-grid.md) com sua conta de armazenamento de Blob. Para obter um exemplo, confira o [tutorial da Grade de Eventos](../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json).

Consulte o tutorial [redimensionar imagem com grade de eventos](../event-grid/resize-images-on-storage-blob-upload-event.md) de um exemplo de grade de eventos.

### <a name="queue-storage-trigger"></a>Gatilho de armazenamento de filas

Outra abordagem para o processamento de BLOBs é gravar mensagens da fila que correspondam aos BLOBs que estão sendo criados ou modificados e, em seguida, usar um [gatilho de armazenamento de fila](./functions-bindings-storage-queue.md) para iniciar o processamento.

## <a name="example"></a>Exemplo

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que grava um log quando um blob é adicionado ou atualizado no contêiner `samples-workitems`.

```csharp
[FunctionName("BlobTriggerCSharp")]        
public static void Run([BlobTrigger("samples-workitems/{name}")] Stream myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

A cadeia de caracteres `{name}` no caminho do disparador de blob `samples-workitems/{name}` cria uma [expressão de associação](./functions-bindings-expressions-patterns.md) que você pode usar no código de função para acessar o nome de arquivo do blob disparando. Para obter mais informações, consulte [Padrões de nome do blob](#blob-name-patterns) a seguir neste artigo.

Para obter mais informações sobre o atributo `BlobTrigger`, consulte [atributos e anotações](#attributes-and-annotations).

# <a name="c-scripttabcsharp-script"></a>[C#Prescritiva](#tab/csharp-script)

O exemplo a seguir mostra uma associação de gatilho de BLOB em um arquivo *Function. JSON* e um código que usa a associação. A função grava um log quando um blob é adicionado ou atualizado no [contêiner](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources)de `samples-workitems`.

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

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

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

# <a name="pythontabpython"></a>[Python](#tab/python)

O exemplo a seguir mostra uma associação de disparo de blob em um arquivo *function.json* e [código Python](functions-reference-python.md) que usa a associação. A função grava um log quando um blob é adicionado ou atualizado no [contêiner](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources)de `samples-workitems`.

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

# <a name="javatabjava"></a>[Java](#tab/java)

Essa função grava um log quando um blob é adicionado ou atualizado no contêiner de `myblob`.

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

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

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

  Para obter um exemplo completo, consulte [exemplo de gatilho](#example).

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

# <a name="c-scripttabcsharp-script"></a>[C#Prescritiva](#tab/csharp-script)

O script não dá suporte C# a atributos.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Não há suporte para atributos pelo JavaScript.

# <a name="pythontabpython"></a>[Python](#tab/python)

Não há suporte para atributos no Python.

# <a name="javatabjava"></a>[Java](#tab/java)

O atributo `@BlobTrigger` é usado para fornecer acesso ao blob que disparou a função. Consulte o [exemplo de gatilho](#example) para obter detalhes.

---

## <a name="configuration"></a>Configuração

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *function.json* e no atributo `BlobTrigger`.

|Propriedade function.json | Propriedade de atributo |DESCRIÇÃO|
|---------|---------|----------------------|
|**tipo** | n/d | Deve ser definido como `blobTrigger`. Essa propriedade é definida automaticamente quando você cria o gatilho no portal do Azure.|
|**direction** | n/d | Deve ser definido como `in`. Essa propriedade é definida automaticamente quando você cria o gatilho no portal do Azure. As exceções são mencionadas na seção [uso](#usage). |
|**name** | n/d | O nome da variável que representa o blob no código de função. |
|**path** | **BlobPath** |O [contêiner](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources) para monitorar.  Pode ser um [padrão de nome de blob](#blob-name-patterns). |
|**conexão** | **Conexão** | O nome de uma configuração de aplicativo que contém uma cadeia de conexão de Armazenamento para usar para essa associação. Se o nome de configuração do aplicativo começar com "AzureWebJobs", você pode especificar apenas o resto do nome aqui. Por exemplo, se você configurar `connection` para “MyStorage”, o runtime do Functions procura por uma configuração de aplicativo que esteja nomeada “AzureWebJobsMyStorage." Se você deixar `connection` vazio, o runtime de Functions usa a cadeia de caracteres de conexão de Armazenamento padrão na configuração de aplicativo chamada `AzureWebJobsStorage`.<br><br>A cadeia de conexão deve ser uma conta de armazenamento para uso geral e não uma [conta de Armazenamento de Blobs](../storage/common/storage-account-overview.md#types-of-storage-accounts).|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Uso

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-trigger.md)]

# <a name="c-scripttabcsharp-script"></a>[C#Prescritiva](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-trigger.md)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Acessar dados de BLOB usando `context.bindings.<NAME>` em que `<NAME>` corresponde ao valor definido em *Function. JSON*.

# <a name="pythontabpython"></a>[Python](#tab/python)

Acessar dados de blob por meio do parâmetro digitado como [InputStream](https://docs.microsoft.com/python/api/azure-functions/azure.functions.inputstream?view=azure-python). Consulte o [exemplo de gatilho](#example) para obter detalhes.

# <a name="javatabjava"></a>[Java](#tab/java)

O atributo `@BlobTrigger` é usado para fornecer acesso ao blob que disparou a função. Consulte o [exemplo de gatilho](#example) para obter detalhes.

---

## <a name="blob-name-patterns"></a>Padrões de nome de BLOB

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

Se o blob é nomeado *{20140101}soundfile.mp3*, o valor da variável `name` no código da função é *soundfile.mp3*.

## <a name="metadata"></a>Metadados

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-metadata.md)]

# <a name="c-scripttabcsharp-script"></a>[C#Prescritiva](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-metadata.md)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
module.exports = function (context, myBlob) {
    context.log("Full blob path:", context.bindingData.blobTrigger);
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Os metadados não estão disponíveis no Python.

# <a name="javatabjava"></a>[Java](#tab/java)

Os metadados não estão disponíveis em Java.

---

## <a name="blob-receipts"></a>Recebimentos de blob

O Azure Functions runtime garante que nenhuma função de gatilho de blob seja chamada mais de uma vez para o mesmo blob novo ou atualizado. Para determinar se uma versão de determinado blob foi processada, ele mantém os *recebimentos de blob*.

O Azure Functions armazena recibos do blob em um contêiner denominado *azure-webjobs-hosts* na conta de armazenamento do Azure do seu aplicativo de funções (definido na configuração do aplicativo `AzureWebJobsStorage`). Um recebimento de blob tem as seguintes informações:

* A função disparada (" *&lt;nome do aplicativo de funções>* .Functions. *&lt;nome da função>* ", por exemplo: "MyFunctionApp.Functions.CopyBlob")
* O nome do contêiner
* O tipo de blob ("BlockBlob" ou "PageBlob")
* O nome do blob
* O ETag (um identificador de versão de blob, por exemplo: "0x8D1DC6E70A277EF")

Para forçar o reprocessamento de um blob, exclua manualmente o recebimento desse blob do contêiner *azure-webjobs-hosts*. Embora o reprocessamento possa não ocorrer imediatamente, é garantido que ocorra em um momento posterior.

## <a name="poison-blobs"></a>BLOBs inviabilizados

Quando uma função de gatilho de blob falhar para um determinado blob, o Azure Functions repete essa função até cinco vezes por padrão.

Se todas as cinco tentativas falharem, o Azure Functions adiciona uma mensagem para uma fila de armazenamento denominada *webjobs-blobtrigger-poison*. A mensagem da fila para blobs suspeitos é um objeto JSON que contém as seguintes propriedades:

* FunctionId (no formato *&lt;nome do aplicativo de funções>* .Functions. *&lt;nome da função>* )
* BlobType ("BlockBlob" ou "PageBlob")
* ContainerName
* BlobName
* ETag (um identificador de versão de blob, por exemplo: "0x8D1DC6E70A277EF")

## <a name="concurrency-and-memory-usage"></a>Uso de memória e simultaneidade

O gatilho de blob usa uma fila internamente, portanto, o número máximo de invocações de função concorrente é controlado pela [configuração de filas em host.json](functions-host-json.md#queues). As configurações padrão limitam a concorrência a 24 invocações. Esse limite aplica-se separadamente a cada função que usa um gatilho de blob.

[O plano de consumo](functions-scale.md#how-the-consumption-and-premium-plans-work) limita um aplicativo de funções em uma VM (máquina virtual) a 1,5 GB de memória. A memória é usada por cada instância de execução de execução simultânea e pelo próprio runtime de Funções. Se uma função disparada por blob carregar todo o blob na memória, a memória máxima usada por essa função apenas para blobs será tamanho máximo de blob 24 *. Por exemplo, um aplicativo de funções com três funções disparadas por blob e as configurações padrão teriam uma concorrência máxima por VM de 3*24 = 72 invocações de função.

As funções de JavaScript e Java carregam todo o blob na memória, e as funções C# fazem isso se você associar a `string`, `Byte[]` ou POCO.

## <a name="polling"></a>Poll

A sondagem funciona como um híbrido entre a inspeção de logs e a execução de verificações de contêiner periódicas. Os BLOBs são verificados em grupos de 10.000 por vez com um token de continuação usado entre intervalos.

> [!WARNING]
> Além disso, [logs de armazenamento são criados da "melhor forma dentro do possível"](/rest/api/storageservices/About-Storage-Analytics-Logging). Não há nenhuma garantia de que todos os eventos são capturados. Sob algumas condições, logs poderão ser perdidos.
> 
> Se você precisar de um processamento de blob mais rápido ou confiável, crie uma [mensagem de fila](../storage/queues/storage-dotnet-how-to-use-queues.md) ao criar o blob. Em seguida, use um [gatilho de fila](functions-bindings-storage-queue.md) em vez de um gatilho de blob para processar o blob. Outra opção é usar a Grade de Eventos; consulte o tutorial [Automatize redimensionamento de imagens carregadas usando a Grade de Eventos](../event-grid/resize-images-on-storage-blob-upload-event.md).
>

## <a name="next-steps"></a>Próximas etapas

- [Ler dados do armazenamento de BLOBs quando uma função é executada](./functions-bindings-storage-blob-input.md)
- [Gravar dados de armazenamento de blobs de uma função](./functions-bindings-storage-blob-output.md)
