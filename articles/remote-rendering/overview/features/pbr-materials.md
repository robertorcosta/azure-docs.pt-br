---
title: Materiais PBR
description: Descreve o tipo de material PBR.
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 64553506f75451c50a87932904f00a7275ea9286
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680252"
---
# <a name="pbr-materials"></a>Materiais PBR

*Os materiais PBR* são um dos tipos de [material](../../concepts/materials.md) suportado na renderização remota Do Zure. Eles são [usados para metes](../../concepts/meshes.md) que devem receber iluminação realista.

PBR significa **P**hysically **B**ased **R**endering e significa que o material descreve as propriedades visuais de uma superfície de forma fisicamente plausível, de modo que resultados realistas são possíveis sob todas as condições de iluminação. A maioria dos motores de jogos modernos e ferramentas de criação de conteúdo suportam materiais PBR porque são considerados a melhor aproximação de cenários do mundo real para renderização em tempo real.

![Modelo de amostra de capacete glTF renderizado por ARR](media/helmet.png)

No entanto, os materiais PBR não são uma solução universal. Existem materiais que refletem a cor de forma diferente dependendo do ângulo de visão. Por exemplo, alguns tecidos ou tintas de carro. Esses tipos de materiais não são manuseados pelo modelo Padrão PBR e atualmente não são suportados pela Renderização Remota Azure. Isso inclui extensões PBR, como *O Thin-Film* (superfícies de várias camadas) e *Clear-Coat* (para pinturas de carros).

## <a name="common-material-properties"></a>Propriedades de materiais comuns

Essas propriedades são comuns a todos os materiais:

* **albedoColor:** Essa cor é multiplicada por outras cores, como as cores *albedoMap* ou *vértice*. Se a *transparência* for habilitada em um material, o canal `1` alfa é usado `0` para ajustar a opacidade, com significado totalmente opaco e que significa totalmente transparente. O padrão é branco.

  > [!NOTE]
  > Quando um material PBR é totalmente transparente, como um pedaço de vidro perfeitamente limpo, ele ainda reflete o ambiente. Pontos brilhantes como o sol ainda são visíveis no reflexo. Isto é diferente para [materiais de cor.](color-materials.md)

* **albedoMap:** Uma [textura 2D](../../concepts/textures.md) para valores de albedo por pixel.

* **alphaClipEnabled** e **alphaClipThreshold:** Se *alphaClipEnabled* for verdadeiro, todos os pixels onde o valor alfa do albedo é menor do que *alphaClipThreshold* não serão desenhados. O recorte alfa pode ser usado mesmo sem permitir transparência e é muito mais rápido de renderizar. Os materiais recortados alfa ainda são mais lentos para renderizar do que materiais totalmente opacos, no entanto. Por padrão, o recorte alfa está desativado.

* **texturaCoordenadaEscala** e **texturaCoordenadaDeslocamento:** A escala é multiplicada nas coordenadas de textura UV, o deslocamento é adicionado a ele. Pode ser usado para esticar e mudar as texturas. A escala padrão é (1, 1) e a compensação é (0, 0).

* **useVertexColor:** Se a malha contiver cores de vértices e esta opção estiver ativada, as cores do vértice das malhas serão multiplicadas no *albedoColor* e *no albedoMap*. Por padrão, as cores do vértice estão desativadas.

* **isDoubleSided:** Se a dupla lateral é definida como verdadeira, triângulos com este material são renderizados mesmo que a câmera esteja olhando para suas faces traseiras. Para materiais PBR a iluminação também é computada adequadamente para faces traseiras. Por padrão, essa opção está desativada. Veja também [renderização unilateral](single-sided-rendering.md).

## <a name="pbr-material-properties"></a>Propriedades de materiais PBR

A idéia central da renderização baseada fisicamente é usar propriedades *BaseColor,* *Metalness*e *Roughness* para emular uma ampla gama de materiais do mundo real. Uma descrição detalhada da PBR está além do escopo deste artigo. Para obter mais informações sobre a PBR, consulte [outras fontes](http://www.pbr-book.org). As seguintes propriedades são específicas para materiais PBR:

* **baseColor:** Em materiais PBR, a *cor albedo* é referida como a *cor base*. No Azure Remote Rendering, a propriedade *de cor albedo* já está presente através das propriedades de material comum, portanto não há nenhuma propriedade adicional de cor base.

* **rugosidade** e **rugosidadeMapa:** Rugosidade define o quão áspera ou lisa é a superfície. Superfícies ásperas espalham a luz em mais direções do que superfícies lisas, que tornam os reflexos embaçados em vez de afiados. A faixa de `0.0` `1.0`valor é de . Quando `roughness` `0.0`iguais, os reflexos serão afiados. Quando `roughness` `0.5`iguais, as reflexões ficarão embaçadas.

  Se um valor de rugosidade e um mapa de rugosidade forem fornecidos, o valor final será o produto dos dois.

* **metalness** e **metalnessMap:** Na física, esta propriedade corresponde a se uma superfície é condutiva ou dielétrica. Materiais condutores têm diferentes propriedades reflexivas, e tendem a ser reflexivos sem cor albedo. Em materiais PBR, essa propriedade afeta o quanto uma superfície reflete o ambiente circundante. Os valores variam de `0.0` . `1.0` Quando a `0.0`metalúrgica é, a cor do albedo é totalmente visível e o material se parece com plástico ou cerâmica. Quando a `0.5`metalúrgica é, parece metal pintado. Quando a `1.0`metalúrgica é, a superfície perde quase completamente sua cor albedo e reflete apenas o ambiente. Por exemplo, `metalness` `1.0` se `roughness` `0.0` é e é, então uma superfície se parece com espelho do mundo real.

  Se um valor de metalee um mapa de metalidade for fornecido, o valor final será o produto dos dois.

  ![metalúrgica e rugosidade](./media/metalness-roughness.png)

  Na imagem acima, a esfera no canto inferior direito parece um material metálico real, a parte inferior esquerda parece cerâmica ou plástico. A cor do albedo também está mudando de acordo com as propriedades físicas. Com o aumento da rugosidade, o material perde a nitidez do reflexo.

* **normalMap:** Para simular detalhes finos de grãos, um [mapa normal](https://en.wikipedia.org/wiki/Normal_mapping) pode ser fornecido.

* **oclusionMap** e **aoScale:** [A oclusão ambiente](https://en.wikipedia.org/wiki/Ambient_occlusion) faz com que objetos com fendas pareçam mais realistas adicionando sombras a áreas ocluídas. O valor da oclusão `1.0`varia `0.0` de `0.0` até , onde `1.0` significa escuridão (oclusão) e significa nenhuma oclusão. Se uma textura 2D for fornecida como um mapa de oclusão, o efeito é ativado e *aoScale* age como um multiplicador.

  ![Mapa de Oclusão](./media/boom-box-ao2.gif)

* **transparente:** Para materiais PBR, há apenas uma configuração de transparência: está ativada ou não. A opacidade é definida pelo canal alfa da cor albedo. Quando ativado, um gasoduto de renderização mais complexo é invocado para desenhar superfícies semitransparentes. A Renderização Remota do Azure implementa [a transparência independente da ordem](https://en.wikipedia.org/wiki/Order-independent_transparency) verdadeira (OIT).

  Geometria transparente é cara de renderização. Se você só precisa de orifícios em uma superfície, por exemplo, para as folhas de uma árvore, é melhor usar o recorte alfa em vez disso.

  ![Transparência](./media/transparency.png) Observe na imagem acima, como a esfera mais correta é totalmente transparente, mas o reflexo ainda é visível.

  > [!IMPORTANT]
  > Se algum material deve ser trocado de opaco para transparente em tempo de execução, o renderizador deve usar o [modo de renderização](../../concepts/rendering-modes.md) *TileBasedComposition* . Esta limitação não se aplica a materiais que são convertidos como materiais transparentes para começar.

## <a name="technical-details"></a>Detalhes técnicos

A renderização remota azure usa a microfaceta Cook-Torrance BRDF com GGX NDF, Schlick Fresnel, e um termo de visibilidade correlacionado GGX Smith com um termo difuso Lambert. Este modelo é o padrão da indústria de fato no momento. Para obter detalhes mais aprofundados, consulte este artigo: [Renderização fisicamente baseada - Cook Torrance](http://www.codinglabs.net/article_physically_based_rendering_cook_torrance.aspx)

 Uma alternativa ao modelo *PBR metalness-roughness* usado na renderização remota azure é o modelo *PBR Specular-Glossiness.* Este modelo pode representar uma gama mais ampla de materiais. No entanto, é mais caro, e geralmente não funciona bem para casos em tempo real.
Nem sempre é possível converter de *Specular-Glossiness* para *Metalness-Roughness,* pois existem pares *de valores (Difusos, Especulários)* que não podem ser convertidos em *(BaseColor, Metalness)*. A conversão na outra direção é mais simples e precisa, já que todos os pares *(BaseColor, Metalness)* correspondem a pares bem definidos *(Difusos, Especular).*

## <a name="next-steps"></a>Próximas etapas

* [Materiais de cor](color-materials.md)
* [Texturas](../../concepts/textures.md)
* [Malhas](../../concepts/meshes.md)
