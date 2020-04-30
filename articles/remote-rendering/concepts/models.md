---
title: Modelos
description: Descreve o que é um modelo na renderização remota do Azure
author: jakrams
ms.author: jakras
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: 5d737b1e85a28661a7491b8d2822e6472538c7a1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81617956"
---
# <a name="models"></a>Modelos

Um *modelo* na renderização remota do Azure refere-se a uma representação de objeto completa, composta por [entidades](entities.md) e [componentes](components.md). Os modelos são a principal maneira de obter dados personalizados para o serviço de renderização remoto.

## <a name="model-structure"></a>Estrutura do modelo

Um modelo tem exatamente uma [entidade](entities.md) como seu nó raiz. Abaixo disso, ele pode ter uma hierarquia arbitrária de entidades filho. Ao carregar um modelo, uma referência a essa entidade raiz é retornada.

Cada entidade pode ter [componentes](components.md) anexados. No caso mais comum, as entidades têm *MeshComponents*, que referenciam [recursos de malha](meshes.md).

## <a name="creating-models"></a>Criando modelos

A criação de modelos para tempo de execução é obtida com a [conversão de modelos de entrada](../how-tos/conversion/model-conversion.md) de formatos de arquivo, como FBX e GLTF. O processo de conversão extrai todos os recursos, como texturas, materiais e malhas, e os converte em formatos de tempo de execução otimizados. Ele também extrairá as informações estruturais e as converterá na estrutura do grafo de entidade/componente do ARR.

> [!IMPORTANT]
>
> A [conversão de modelo](../how-tos/conversion/model-conversion.md) é a única maneira de criar [malhas](meshes.md). Embora as malhas possam ser compartilhadas entre entidades em tempo de execução, não há nenhuma outra maneira de obter uma malha no tempo de execução, além de carregar um modelo.

## <a name="loading-models"></a>Carregando modelos

Depois que um modelo é convertido, ele pode ser carregado do armazenamento de BLOBs do Azure para o tempo de execução.

Há duas funções de carregamento diferentes que diferem da maneira como o ativo é endereçado no armazenamento de BLOBs:

* O modelo pode ser endereçado por seu URI de SAS. A função de carregamento `LoadModelFromSASAsync` relevante é `LoadModelFromSASParams`com o parâmetro. Use essa variante também ao carregar [modelos internos](../samples/sample-model.md).
* O modelo pode ser endereçado pelos parâmetros de armazenamento de BLOBs diretamente, caso o [armazenamento de BLOBs esteja vinculado à conta](../how-tos/create-an-account.md#link-storage-accounts). A função de carregamento relevante nesse caso `LoadModelAsync` é com `LoadModelParams`o parâmetro.

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

Se você quiser carregar um modelo diretamente usando seus parâmetros de armazenamento de BLOBs, use um código semelhante ao trecho a seguir:

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

Posteriormente, você pode percorrer a hierarquia de entidade e modificar as entidades e os componentes. Carregar o mesmo modelo várias vezes cria várias instâncias, cada uma com sua própria cópia da estrutura de entidade/componente. Como as malhas, os materiais e as texturas são [recursos compartilhados](../concepts/lifetime.md), no entanto, seus dados não serão carregados novamente. Portanto, instanciar um modelo mais de uma vez incorre em uma sobrecarga de memória relativamente pequena.

> [!CAUTION]
> Todas as funções *assíncronas* no Arr retornam objetos de operação assíncrona. Você deve armazenar uma referência a esses objetos até que a operação seja concluída. Caso contrário, o coletor de lixo C# poderá excluir a operação antecipadamente e nunca poderá ser concluído. No código de exemplo acima, o uso de *Await* garante que a variável local ' loadOp ' mantém uma referência até que o carregamento do modelo seja concluído. No entanto, se você usar o evento *Completed* em vez disso, precisaria armazenar a operação assíncrona em uma variável de membro.

## <a name="next-steps"></a>Próximas etapas

* [Entities](entities.md)
* [Malhas](meshes.md)
