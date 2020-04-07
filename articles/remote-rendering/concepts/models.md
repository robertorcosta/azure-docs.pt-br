---
title: Modelos
description: Descreve o que é um modelo na renderização remota do Azure
author: jakrams
ms.author: jakras
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: 770c88ddfb44004b76633cbeb726d28e1626a72c
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681721"
---
# <a name="models"></a>Modelos

Um *modelo* em Renderização Remota Azure refere-se a uma representação completa de objetos, composta por [entidades](entities.md) e [componentes](components.md). Os modelos são a principal maneira de obter dados personalizados no serviço de renderização remota.

## <a name="model-structure"></a>Estrutura do modelo

Um modelo tem exatamente uma [entidade](entities.md) como seu nó raiz. Abaixo disso, pode ter uma hierarquia arbitrária de entidades infantis. Ao carregar um modelo, uma referência a essa entidade raiz é devolvida.

Cada entidade pode ter [componentes](components.md) anexados. No caso mais comum, as entidades possuem *MeshComponents*, que fazem referência aos [recursos da malha.](meshes.md)

## <a name="creating-models"></a>Criando modelos

A criação de modelos para tempo de execução é alcançada [convertendo modelos](../how-tos/conversion/model-conversion.md) de entrada de formatos de arquivo, como FBX e GLTF. O processo de conversão extrai todos os recursos, como texturas, materiais e malhas, e os converte em formatos de tempo de execução otimizados. Ele também extrairá as informações estruturais e converterá-as na estrutura de gráficos de entidade/componente da ARR.

> [!IMPORTANT]
>
> [A conversão de](../how-tos/conversion/model-conversion.md) modelos é a única maneira de criar [meshes](meshes.md). Embora as malhas possam ser compartilhadas entre entidades em tempo de execução, não há outra maneira de colocar uma malha no tempo de execução, além de carregar um modelo.

## <a name="loading-models"></a>Modelos de carregamento

Uma vez que um modelo é convertido, ele pode ser carregado a partir do armazenamento blob do Azure no tempo de execução.

Existem duas funções de carregamento distintas que diferem pela forma como o ativo é abordado no armazenamento blob:

* O modelo pode ser abordado pelo seu Uri SAS. A função `LoadModelFromSASAsync` de carregamento `LoadModelFromSASParams`relevante é com parâmetro . Use esta variante também ao carregar [modelos embutidos](../samples/sample-model.md).
* O modelo pode ser abordado diretamente pelos parâmetros de armazenamento blob, caso o [armazenamento blob esteja vinculado à conta.](../how-tos/create-an-account.md#link-storage-accounts) A função de carregamento `LoadModelAsync` relevante `LoadModelParams`neste caso é com parâmetro .

Os seguintes trechos de código mostram como carregar modelos com qualquer função. Para carregar um modelo usando o Uri SAS, use o código abaixo:

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

Se você quiser carregar um modelo usando diretamente seus parâmetros de armazenamento blob, use um código semelhante ao seguinte trecho:

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

    // ... (identical to the SAS URI snippet above)
}
```

Depois você pode atravessar a hierarquia da entidade e modificar as entidades e componentes. Carregar o mesmo modelo várias vezes cria várias instâncias, cada uma com sua própria cópia da estrutura entidade/componente. Uma vez que malhas, materiais e texturas são [recursos compartilhados,](../concepts/lifetime.md)seus dados não serão carregados novamente, no entanto. Portanto, a instanciação de um modelo mais de uma vez incorre relativamente pouca sobrecarga de memória.

> [!CAUTION]
> Todas as funções *assíncronas* em arr retornam objetos de operação assíncronos. Você deve armazenar uma referência a esses objetos até que a operação seja concluída. Caso contrário, o coletor de lixo C# pode excluir a operação mais cedo e nunca poderá terminar. No código de amostra acima, o uso do *aguarde* garante que a variável local 'loadOp' mantém uma referência até que o carregamento do modelo seja concluído. No entanto, se você usar o evento *Concluído* em vez disso, você precisará armazenar a operação assíncrona em uma variável de membro.

## <a name="next-steps"></a>Próximas etapas

* [Entities](entities.md)
* [Malhas](meshes.md)
