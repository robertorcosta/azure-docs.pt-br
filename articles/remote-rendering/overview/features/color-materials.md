---
title: Materiais de cor
description: Descreve o tipo de material de cor.
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 7cbcaefcc087c9f1c7c09668a27fbdef9a4802d3
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681071"
---
# <a name="color-materials"></a>Materiais de cor

*Os materiais coloridos* são um dos tipos de [material](../../concepts/materials.md) suportado na renderização remota Do Zure. Eles são [usados para meshes](../../concepts/meshes.md) que não devem receber qualquer tipo de iluminação, mas sim ser brilho total em todos os momentos. Este pode ser o caso de materiais "brilhantes", como painéis de carros, lâmpadas ou para dados que já incorporam iluminação estática, como modelos obtidos através [da fotogrametria.](https://en.wikipedia.org/wiki/Photogrammetry)

Os materiais coloridos são mais eficientes de renderizar do que [os materiais PBR](pbr-materials.md) devido ao seu modelo de sombreamento mais simples. Eles também suportam diferentes modos de transparência.

## <a name="common-material-properties"></a>Propriedades de materiais comuns

Essas propriedades são comuns a todos os materiais:

* **albedoColor:** Essa cor é multiplicada por outras cores, como as cores *albedoMap* ou *vértice*. Se a *transparência* for habilitada em um material, o canal `1` alfa é usado `0` para ajustar a opacidade, com significado totalmente opaco e que significa totalmente transparente. O padrão é branco.

  > [!NOTE]
  > Como os materiais de cor não refletem o ambiente, um material de cor totalmente transparente se torna invisível. Isto é diferente para [materiais PBR.](pbr-materials.md)

* **albedoMap:** Uma [textura 2D](../../concepts/textures.md) para valores de albedo por pixel.

* **alphaClipEnabled** e **alphaClipThreshold:** Se *alphaClipEnabled* for verdadeiro, todos os pixels onde o valor alfa do albedo é menor do que *alphaClipThreshold* não serão desenhados. O recorte alfa pode ser usado mesmo sem permitir transparência e é muito mais rápido de renderizar. Os materiais recortados alfa ainda são mais lentos para renderizar do que materiais totalmente opacos, no entanto. Por padrão, o recorte alfa está desativado.

* **texturaCoordenadaEscala** e **texturaCoordenadaDeslocamento:** A escala é multiplicada nas coordenadas de textura UV, o deslocamento é adicionado a ele. Pode ser usado para esticar e mudar as texturas. A escala padrão é (1, 1) e a compensação é (0, 0).

* **useVertexColor:** Se a malha contiver cores de vértices e esta opção estiver ativada, as cores do vértice das malhas serão multiplicadas no *albedoColor* e *no albedoMap*. Por padrão, as cores do vértice estão desativadas.

* **isDoubleSided:** Se a dupla lateral é definida como verdadeira, triângulos com este material são renderizados mesmo que a câmera esteja olhando para suas faces traseiras. Por padrão, essa opção está desativada. Veja também [renderização unilateral](single-sided-rendering.md).

## <a name="color-material-properties"></a>Propriedades do material de cor

As seguintes propriedades são específicas para materiais de cor:

* **vérticeMix:** Este valor `0` `1` entre e especifica o quão fortemente a cor do vértice em uma [malha](../../concepts/meshes.md) contribui para a cor final. No valor padrão de 1, a cor do vértice é multiplicada na cor albedo totalmente. Com um valor de 0, as cores do vértice são ignoradas inteiramente.

* **transparencyMode:** Ao contrário dos [materiais PBR,](pbr-materials.md)os materiais de cor distinguem entre diferentes modos de transparência:

  1. **Opaco:** O modo padrão desativa a transparência. O recorte alfa ainda é possível, porém, e deve ser preferido, se suficiente.
  
  1. **AlphaBlended:** Este modo é semelhante ao modo de transparência para materiais PBR. Deve ser usado para materiais transparentes como vidro.

  1. **Aditivo:** Este modo é o modo de transparência mais simples e eficiente. A contribuição do material é adicionada à imagem renderizada. Este modo pode ser usado para simular objetos brilhantes (mas ainda transparentes), como marcadores usados para destacar objetos importantes.

## <a name="next-steps"></a>Próximas etapas

* [Materiais PBR](pbr-materials.md)
* [Texturas](../../concepts/textures.md)
* [Malhas](../../concepts/meshes.md)
