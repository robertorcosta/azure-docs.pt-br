---
title: Modelos
description: Descreve o que é um modelo no Azure Remote Rendering
author: jakrams
ms.author: jakras
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: 7832f999de2f6f16cfe816c061925e371f90662e
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.contentlocale: pt-BR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83758683"
---
# <a name="models"></a>Modelos

Um *modelo* no Azure Remote Rendering refere-se a uma representação de objeto completa, composta de [entidades](entities.md) e [componentes](components.md). Os modelos são a principal maneira de obter dados personalizados para o serviço de renderização remoto.

## <a name="model-structure"></a>Estrutura do modelo

Um modelo tem exatamente um [entidade](entities.md) como seu nó raiz. Abaixo disso, ele pode ter uma hierarquia arbitrária de entidades filho. Ao carregar um modelo, uma referência a essa entidade raiz é retornada.

Cada entidade pode ter [componentes](components.md) anexados. No caso mais comum, as entidades têm *MeshComponents*, que fazem referência a [recursos de malha](meshes.md).

## <a name="creating-models"></a>Criar modelos

A criação de modelos para runtime é obtida ao [converter modelos de entrada](../how-tos/conversion/model-conversion.md) a partir de formatos de arquivo, como FBX e GLTF. O processo de conversão extrai todos os recursos, como texturas, materiais e malhas, e os converte em formatos de runtime otimizados. Ele também extrairá as informações estruturais e as converterá na estrutura do grafo de entidade/componente do Application Request Routing.

> [!IMPORTANT]
>
> A [conversão de modelo](../how-tos/conversion/model-conversion.md) é a única maneira de criar [malhas](meshes.md). Embora as malhas possam ser compartilhadas entre entidades no runtime, não há outra maneira de obter uma malha no runtime, a não ser ao carregar um modelo.

## <a name="loading-models"></a>Carregar modelos

Depois que um modelo é convertido, ele pode ser carregado a partir do armazenamento de blobs do Azure para o runtime.

Há duas funções de carregamento distintas que diferem na maneira como o ativo é endereçado no armazenamento de blobs:

* O modelo pode ser endereçado por seu URI de SAS. A função de carregamento relevante é `LoadModelFromSASAsync` com o parâmetro `LoadModelFromSASParams`. Use essa variante também ao carregar [modelos integrados](../samples/sample-model.md).
* O modelo pode ser tratado pelos parâmetros de armazenamento de blobs, caso o [Armazenamento de Blobs esteja vinculado à conta](../how-tos/create-an-account.md#link-storage-accounts). A função de carregamento relevante nesse caso é `LoadModelAsync` com o parâmetro `LoadModelParams`.

Os trechos de código a seguir mostram como carregar modelos com uma das funções. Para carregar um modelo usando o URI de SAS, use um código como o mostrado abaixo:

```csharp
async void LoadModel(AzureSession session, Entity modelParent, string modelUri)
{
    // load a model that will be parented to modelParent
    var modelParams = new LoadModelFromSASParams(modelUri, modelParent);

    var loadOp = session.Actions.LoadModelFromSASAsync(modelParams);

    loadOp.ProgressUpdated += (float progress) =>
    {
        Debug.Log($"Loading: {progress * 100.0f}%");
    };

    await loadOp.AsTask();
}
```

```cpp
ApiHandle<LoadModelAsync> LoadModel(ApiHandle<AzureSession> session, ApiHandle<Entity> modelParent, std::string modelUri)
{
    LoadModelFromSASParams modelParams;
    modelParams.ModelUrl = modelUri;
    modelParams.Parent = modelParent;

    ApiHandle<LoadModelAsync> loadOp = *session->Actions()->LoadModelFromSASAsync(modelParams);

    loadOp->Completed([](const ApiHandle<LoadModelAsync>& async)
    {
        printf("Loading: finished.");
    });
    loadOp->ProgressUpdated([](float progress)
    {
        printf("Loading: %.1f%%", progress*100.f);
    });

    return loadOp;
}
```

Se quiser carregar um modelo diretamente usando seus parâmetros de armazenamento de blobs, use um código semelhante ao trecho a seguir:

```csharp
async void LoadModel(AzureSession session, Entity modelParent, string storageAccount, string containerName, string assetFilePath)
{
    // load a model that will be parented to modelParent
    var modelParams = new LoadModelParams(
        storageAccount, // storage account name + '.blob.core.windows.net', e.g., 'mystorageaccount.blob.core.windows.net'
        containerName,  // name of the container in your storage account, e.g., 'mytestcontainer'
        assetFilePath,  // the file path to the asset within the container, e.g., 'path/to/file/myAsset.arrAsset'
        modelParent
    );

    var loadOp = session.Actions.LoadModelAsync(modelParams);

    // ... (identical to the SAS URI snippet above)
}
```

```cpp
ApiHandle<LoadModelAsync> LoadModel(ApiHandle<AzureSession> session, ApiHandle<Entity> modelParent, std::string storageAccount, std::string containerName, std::string assetFilePath)
{
    LoadModelParams modelParams;
    modelParams.Parent = modelParent;
    modelParams.Blob.StorageAccountName = std::move(storageAccount);
    modelParams.Blob.BlobContainerName = std::move(containerName);
    modelParams.Blob.AssetPath = std::move(assetFilePath);

    ApiHandle<LoadModelAsync> loadOp = *session->Actions()->LoadModelAsync(modelParams);
    // ... (identical to the SAS URI snippet above)
}
```

Posteriormente, é possível percorrer a hierarquia de entidade e mudar as entidades e os componentes. Carregar o mesmo modelo várias vezes cria várias instâncias, cada uma com sua própria cópia da estrutura de entidade/componente. Como as malhas, os materiais e as texturas são [recursos compartilhados](../concepts/lifetime.md), os dados não serão carregados novamente. Portanto, instanciar um modelo mais de uma vez gera uma sobrecarga de memória relativamente pequena.

> [!CAUTION]
> Todas as funções *assíncronas* no Application Request Routing retornam objetos de operação assíncronos. É preciso armazenar uma referência a esses objetos até que a operação seja concluída. Caso contrário, o coletor de lixo do C# pode excluir a operação antecipadamente e pode nunca ser concluído. No código de exemplo acima, o uso de *await* garante que a variável local "loadOp" mantenha uma referência até que o carregamento do modelo seja concluído. No entanto, se você usar o evento *Concluído* no lugar, precisará armazenar a operação assíncrona em uma variável do membro.

## <a name="next-steps"></a>Próximas etapas

* [Entidades](entities.md)
* [Malhas](meshes.md)
