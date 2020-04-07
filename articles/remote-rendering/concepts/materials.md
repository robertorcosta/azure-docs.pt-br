---
title: Materiais
description: Renderização de descrição do material e propriedades do material
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: conceptual
ms.openlocfilehash: 8551e17ddd71e76aca0c85b9768f564ae0e5f049
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681838"
---
# <a name="materials"></a>Materiais

Os materiais são [recursos compartilhados](../concepts/lifetime.md) que definem como [as meshes](meshes.md) são renderizadas. Os materiais são usados para especificar quais [texturas](textures.md) aplicar, se tornar os objetos transparentes e como a iluminação será calculada.

Os materiais são criados automaticamente durante [a conversão do modelo](../how-tos/conversion/model-conversion.md) e são acessíveis em tempo de execução. Você também pode criar materiais personalizados a partir do código e substituir os existentes. Este cenário faz especialmente sentido se você quiser compartilhar o mesmo material em muitas meshes. Uma vez que as modificações de um material são visíveis em cada malha que o faz referência, este método pode ser usado para aplicar facilmente alterações.

> [!NOTE]
> Alguns casos de uso, como destacar um objeto escolhido, podem ser feitos modificando materiais, mas são muito mais fáceis de serem alcançados através do [HierarchicalStateOverrideComponent](../overview/features/override-hierarchical-state.md).

## <a name="material-types"></a>Tipos de materiais

A renderização remota do Azure tem dois tipos de materiais distintos:

* [Os materiais PBR](../overview/features/pbr-materials.md) são utilizados para superfícies que devem ser renderizadas da forma mais correta possível. A iluminação realista é calculada para esses materiais usando *renderização fisicamente baseada* (PBR). Para aproveitar ao máximo esse tipo de material, é importante fornecer dados de entrada de alta qualidade, como rugosidade e mapas normais.

* [Materiais coloridos](../overview/features/color-materials.md) são usados para casos em que nenhuma iluminação adicional é desejada. Esses materiais são sempre totalmente brilhantes e são mais fáceis de configurar. Materiais coloridos são usados para dados que não devem ter iluminação alguma, ou já incorporam iluminação estática, como modelos obtidos através [da fotogrametria.](https://en.wikipedia.org/wiki/Photogrammetry)

## <a name="mesh-vs-meshcomponent-material-assignment"></a>Atribuição de material Mesh vs. MeshComponent

[As metês](meshes.md) têm um ou mais submeshes. Cada submalha faz referência a um material. Você pode alterar o material para usar diretamente na malha, ou pode substituir qual material usar para uma submalha em um [MeshComponent](meshes.md#meshcomponent).

Quando você modifica um material diretamente no recurso de malha, essa alteração afeta todas as instâncias dessa malha. Alterá-lo no MeshComponent, no entanto, só afeta essa instância de malha. Qual método é mais apropriado depende do comportamento desejado, mas modificar um MeshComponent é a abordagem mais comum.

## <a name="material-classes"></a>Aulas de material

Todos os materiais fornecidos pela API derivam da classe `Material`base . Seu tipo pode ser `Material.MaterialSubType` consultado através ou lançando-os diretamente:

``` cs
void SetMaterialColorToGreen(Material material)
{
    if (material.MaterialSubType == MaterialType.Color)
    {
        ColorMaterial colorMaterial = material as ColorMaterial;
        colorMaterial.AlbedoColor = new Color4(0, 1, 0, 1);
        return;
    }

    PbrMaterial pbrMat = material as PbrMaterial;
    if( pbrMat!= null )
    {
        PbrMaterial pbrMaterial = material.PbrMaterial.Value;
        pbrMaterial.AlbedoColor = new Color4(0, 1, 0, 1);
        return;
    }
}
```

## <a name="next-steps"></a>Próximas etapas

* [Materiais PBR](../overview/features/pbr-materials.md)
* [Materiais de cor](../overview/features/color-materials.md)
