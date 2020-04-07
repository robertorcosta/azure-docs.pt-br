---
title: Texturas
description: Fluxo de trabalho de recursos de textura
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: 09fa22d33377dfcbafd84f0caeb5f33a575b1bce
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681656"
---
# <a name="textures"></a>Texturas

Texturas são um [recurso compartilhado](../concepts/lifetime.md)imutável. As texturas podem ser carregadas a partir do [armazenamento blob](../how-tos/conversion/blob-storage.md) e aplicadas diretamente aos modelos, como demonstrado no [Tutorial: Changing the environment and materials](../tutorials/unity/changing-environment-and-materials.md). Mais comumente, porém, as texturas farão parte de um [modelo convertido,](../how-tos/conversion/model-conversion.md)onde são referenciados por seus [materiais.](materials.md)

## <a name="texture-types"></a>Tipos de textura

Diferentes tipos de textura têm diferentes casos de uso:

* **Texturas 2D** são usadas principalmente em [materiais](materials.md).
* **Cubemaps** podem ser usados para o [céu](../overview/features/sky.md).

## <a name="supported-texture-formats"></a>Formatos de textura suportados

Todas as texturas dadas ao ARR devem estar no [formato DDS](https://en.wikipedia.org/wiki/DirectDraw_Surface). De preferência com mipmaps e compressão de textura. Consulte [a ferramenta de linha de comando TexConv](../resources/tools/tex-conv.md) se quiser automatizar o processo de conversão.

## <a name="loading-textures"></a>Carregando texturas

Ao carregar uma textura, você tem que especificar o tipo esperado. Se o tipo não corresponder, a carga de textura falha.
Carregar uma textura com o mesmo URI duas vezes retornará o mesmo objeto de textura, pois é um [recurso compartilhado](../concepts/lifetime.md).

Semelhante aos modelos de carregamento, existem duas variantes de abordar um ativo de textura no armazenamento de blob de origem:

* O ativo de textura pode ser abordado pelo seu Uri SAS. A função `LoadTextureFromSASAsync` de carregamento `LoadTextureFromSASParams`relevante é com parâmetro . Use esta variante também ao carregar [texturas incorporadas](../overview/features/sky.md#built-in-environment-maps).
* A textura pode ser tratada diretamente pelos parâmetros de armazenamento blob, caso o [armazenamento blob esteja vinculado à conta](../how-tos/create-an-account.md#link-storage-accounts). A função de carregamento `LoadTextureAsync` relevante `LoadTextureParams`neste caso é com parâmetro .

O código de amostra a seguir mostra como carregar uma textura através de seu Uri SAS (ou textura incorporada) - observe que apenas a função/parâmetro de carregamento difere para o outro caso:

``` cs
LoadTextureAsync _textureLoad = null;
void LoadMyTexture(AzureSession session, string textureUri)
{
    _textureLoad = session.Actions.LoadTextureAsync(new LoadTextureParams(textureUri, TextureType.Texture2D));
    _textureLoad.Completed +=
        (LoadTextureAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                System.Console.WriteLine("Texture loading failed!");
            }
            _textureLoad = null;
        };
}
```

Dependendo do que a textura deve ser usada, pode haver restrições para o tipo de textura e conteúdo. Por exemplo, o mapa de rugosidade de um [material PBR](../overview/features/pbr-materials.md) deve ser em escala de cinza.

> [!CAUTION]
> Todas as funções *assíncronas* em arr retornam objetos de operação assíncronos. Você deve armazenar uma referência a esses objetos até que a operação seja concluída. Caso contrário, o coletor de lixo C# pode excluir a operação mais cedo e nunca poderá terminar. No código de amostra acima, a variável membro '_textureLoad' é usada para manter uma referência até que o evento *Concluído* chegue.

## <a name="next-steps"></a>Próximas etapas

* [Materiais](materials.md)
* [Céu](../overview/features/sky.md)
