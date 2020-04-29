---
title: Materiais
description: Renderizando Propriedades de material e descrição do material
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: conceptual
ms.openlocfilehash: 8551e17ddd71e76aca0c85b9768f564ae0e5f049
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681838"
---
# <a name="materials"></a>Materiais

Os materiais são [recursos compartilhados](../concepts/lifetime.md) que definem como as [malhas](meshes.md) são renderizadas. Os materiais são usados para especificar quais [texturas](textures.md) aplicar, se os objetos serão transparentes e como a iluminação será calculada.

Os materiais são criados automaticamente durante a [conversão do modelo](../how-tos/conversion/model-conversion.md) e podem ser acessados em tempo de execução. Você também pode criar materiais personalizados do código e substituir os existentes. Esse cenário faz especialmente sentido se você quiser compartilhar o mesmo material em várias malhas. Como as modificações de um material são visíveis em todas as malhas que fazem referência a ela, esse método pode ser usado para aplicar alterações com facilidade.

> [!NOTE]
> Alguns casos de uso, como realçar um objeto separado, podem ser feitos por meio da modificação de materiais, mas são muito mais fáceis de se obter por meio do [HierarchicalStateOverrideComponent](../overview/features/override-hierarchical-state.md).

## <a name="material-types"></a>Tipos de material

A renderização remota do Azure tem dois tipos de material distintos:

* Os [materiais de PBR](../overview/features/pbr-materials.md) são usados para superfícies que devem ser renderizadas como fisicamente corretas, quanto possível. A iluminação realista é calculada para esses materiais usando o PBR ( *processamento com base fisicamente* ). Para aproveitar ao máximo esse tipo de material, é importante fornecer dados de entrada de alta qualidade, como esboços e mapas normais.

* [Materiais de cor](../overview/features/color-materials.md) são usados para casos em que nenhuma iluminação adicional é desejada. Esses materiais são sempre brilhantes e mais fáceis de configurar. Materiais de cor são usados para dados que não devem ter nenhuma iluminação ou que já incorpora iluminação estática, como modelos obtidos por meio de [Photogrammetry](https://en.wikipedia.org/wiki/Photogrammetry).

## <a name="mesh-vs-meshcomponent-material-assignment"></a>Atribuição de material de malha versus MeshComponent

As [malhas](meshes.md) têm uma ou mais submalhas. Cada submalha faz referência a um material. Você pode alterar o material para usar diretamente na malha ou pode substituir o material a ser usado para uma submalha em um [MeshComponent](meshes.md#meshcomponent).

Quando você modifica um material diretamente no recurso de malha, essa alteração afeta todas as instâncias dessa malha. Alterá-lo no MeshComponent, no entanto, afeta apenas uma instância de malha. O método mais apropriado depende do comportamento desejado, mas a modificação de um MeshComponent é a abordagem mais comum.

## <a name="material-classes"></a>Classes de material

Todos os materiais fornecidos pela API derivam da classe `Material`base. Seu tipo pode ser consultado por `Material.MaterialSubType` meio de ou convertendo-os diretamente:

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

* [Materiais de PBR](../overview/features/pbr-materials.md)
* [Materiais de cores](../overview/features/color-materials.md)
