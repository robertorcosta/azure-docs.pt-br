---
title: Texturas
description: Fluxo de trabalho de recurso de textura
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: 09fa22d33377dfcbafd84f0caeb5f33a575b1bce
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681656"
---
# <a name="textures"></a>Texturas

As texturas são um [recurso compartilhado](../concepts/lifetime.md)imutável. As texturas podem ser carregadas do [armazenamento de BLOBs](../how-tos/conversion/blob-storage.md) e aplicadas aos modelos diretamente, como demonstrado no [tutorial: alterando o ambiente e os materiais](../tutorials/unity/changing-environment-and-materials.md). No entanto, geralmente as texturas serão parte de um [modelo convertido](../how-tos/conversion/model-conversion.md), onde são referenciadas por seus [materiais](materials.md).

## <a name="texture-types"></a>Tipos de textura

Tipos de textura diferentes têm diferentes casos de uso:

* as **texturas 2D** são usadas principalmente em [materiais](materials.md).
* **Cubemaps** pode ser usado para o [céu](../overview/features/sky.md).

## <a name="supported-texture-formats"></a>Formatos de textura com suporte

Todas as texturas dadas a ARR devem estar no [formato DDS](https://en.wikipedia.org/wiki/DirectDraw_Surface). Preferencialmente com mipmaps e a compactação de textura. Consulte [a ferramenta de linha de comando TexConv](../resources/tools/tex-conv.md) se você quiser automatizar o processo de conversão.

## <a name="loading-textures"></a>Carregando texturas

Ao carregar uma textura, você precisa especificar seu tipo esperado. Se o tipo não corresponder, o carregamento da textura falhará.
Carregar uma textura com o mesmo URI duas vezes retornará o mesmo objeto de textura, pois ele é um [recurso compartilhado](../concepts/lifetime.md).

De forma semelhante ao carregamento de modelos, há duas variantes de endereçamento de um ativo de textura no armazenamento de blob de origem:

* O ativo de textura pode ser endereçado por seu URI de SAS. A função de carregamento `LoadTextureFromSASAsync` relevante é `LoadTextureFromSASParams`com o parâmetro. Use essa variante também ao carregar [texturas internas](../overview/features/sky.md#built-in-environment-maps).
* A textura pode ser resolvida pelos parâmetros de armazenamento de BLOBs diretamente, caso o [armazenamento de BLOBs esteja vinculado à conta](../how-tos/create-an-account.md#link-storage-accounts). A função de carregamento relevante nesse caso `LoadTextureAsync` é com `LoadTextureParams`o parâmetro.

O código de exemplo a seguir mostra como carregar uma textura por meio de seu URI de SAS (ou textura interna) – Observe que apenas a função/parâmetro de carregamento difere para o outro caso:

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

Dependendo do que a textura deve ser usada, pode haver restrições para o tipo de textura e o conteúdo. Por exemplo, o mapa de comparações de um [material PBR](../overview/features/pbr-materials.md) deve ser em escala de cinza.

> [!CAUTION]
> Todas as funções *assíncronas* no Arr retornam objetos de operação assíncrona. Você deve armazenar uma referência a esses objetos até que a operação seja concluída. Caso contrário, o coletor de lixo C# poderá excluir a operação antecipadamente e nunca poderá ser concluído. No código de exemplo acima, a variável de membro ' _textureLoad ' é usada para manter uma referência até que o evento *concluído* chegue.

## <a name="next-steps"></a>Próximas etapas

* [Materiais](materials.md)
* [Celeste](../overview/features/sky.md)
