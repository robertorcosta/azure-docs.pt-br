---
title: Materiais de cores
description: Descreve o tipo de material de cor.
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 7cbcaefcc087c9f1c7c09668a27fbdef9a4802d3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681071"
---
# <a name="color-materials"></a>Materiais de cores

*Materiais de cores* são um dos [tipos de material](../../concepts/materials.md) com suporte na renderização remota do Azure. Eles são usados para [malhas](../../concepts/meshes.md) que não devem receber nenhum tipo de iluminação, mas estão sempre com brilho completo. Esse pode ser o caso para materiais "brilhantes", como painéis de carro, lâmpadas ou dados que já incorpora iluminação estática, como modelos obtidos por meio de [Photogrammetry](https://en.wikipedia.org/wiki/Photogrammetry).

Materiais de cor são mais eficientes para renderizar do que os [materiais de PBR](pbr-materials.md) devido ao seu modelo de sombreamento mais simples. Eles também dão suporte a diferentes modos de transparência.

## <a name="common-material-properties"></a>Propriedades do material comum

Essas propriedades são comuns a todos os materiais:

* **albedoColor:** Essa cor é multiplicada por outras cores, como as cores *albedoMap* ou *Vertex*. Se a *transparência* estiver habilitada em um material, o canal alfa será usado para ajustar a opacidade `1` , com significado totalmente `0` opaco e significado totalmente transparente. O padrão é branco.

  > [!NOTE]
  > Como os materiais de cor não refletem o ambiente, um material de cor totalmente transparente torna-se invisível. Isso é diferente para [materiais de PBR](pbr-materials.md).

* **albedoMap:** Uma [textura 2D](../../concepts/textures.md) para valores de albedo por pixel.

* **alphaClipEnabled** e **AlphaClipThreshold:** se *alphaClipEnabled* for true, todos os pixels em que o valor de alfa albedo for menor que *alphaClipThreshold* não serão desenhados. O recorte alfa pode ser usado mesmo sem habilitar a transparência e é muito mais rápido de renderizar. No entanto, os materiais recortados alfa ainda são mais lentos do que os materiais totalmente opacos. Por padrão, o recorte alfa é desabilitado.

* **textureCoordinateScale** e **textureCoordinateOffset:** a escala é multiplicada nas coordenadas de textura UV, o deslocamento é adicionado a ela. Pode ser usado para alongar e deslocar as texturas. A escala padrão é (1, 1) e o deslocamento é (0, 0).

* **useVertexColor:** Se a malha contiver cores de vértice e essa opção estiver habilitada, as cores de vértice das malhas serão multiplicadas em *albedoColor* e *albedoMap*. Por padrão, as cores do vértice são desabilitadas.

* **isDoubleSided:** Se Double-sidedness for definido como true, os triângulos com esse material serão renderizados mesmo se a câmera estiver observando suas faces de apoio. Por padrão, essa opção está desabilitada. Consulte também [renderização de um único lado](single-sided-rendering.md).

## <a name="color-material-properties"></a>Propriedades do material de cor

As propriedades a seguir são específicas para materiais de cores:

* **vertexMix:** Esse valor entre `0` e `1` especifica o quão forte a cor de vértice em uma [malha](../../concepts/meshes.md) contribui para a cor final. No valor padrão de 1, a cor do vértice é multiplicada na cor albedo totalmente. Com um valor de 0, as cores de vértice são ignoradas inteiramente.

* **transparênciamode:** Ao contrário dos [materiais de PBR](pbr-materials.md), materiais de cor distinguem entre diferentes modos de transparência:

  1. **Opaco:** O modo padrão desabilita a transparência. O recorte alfa ainda é possível, porém, e deve ser preferencial, se for suficiente.
  
  1. **AlphaBlended:** Esse modo é semelhante ao modo de transparência para materiais PBR. Ele deve ser usado para materiais de visualização como vidro.

  1. **Aditivo:** Esse modo é o modo de transparência mais simples e eficiente. A contribuição do material é adicionada à imagem renderizada. Esse modo pode ser usado para simular objetos brilhantes (mas ainda transparentes), como marcadores usados para realçar objetos importantes.

## <a name="next-steps"></a>Próximas etapas

* [Materiais de PBR](pbr-materials.md)
* [Texturas](../../concepts/textures.md)
* [Malhas](../../concepts/meshes.md)
