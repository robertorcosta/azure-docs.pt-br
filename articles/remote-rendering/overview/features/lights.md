---
title: Luzes
description: Descrição da fonte de luz e propriedades
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.openlocfilehash: 0a4a226af1347b5302b0c3964889fc072f89e7f8
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680941"
---
# <a name="lights"></a>Luzes

Por padrão, os objetos renderizados remotamente são acesos usando uma [luz do céu](sky.md). Para a maioria dos aplicativos isso já é suficiente, mas você pode adicionar mais fontes de luz à cena.

> [!IMPORTANT]
> Apenas [os materiais PBR](pbr-materials.md) são afetados por fontes de luz. [Os materiais coloridos](color-materials.md) sempre parecem totalmente brilhantes.

> [!NOTE]
> As sombras de fundição não são suportadas no momento. A renderização remota do Azure é otimizada para renderizar enormes quantidades de geometria, utilizando várias GPUs, se necessário. Abordagens tradicionais para o casting de sombras não funcionam bem em tais cenários.

## <a name="common-light-component-properties"></a>Propriedades comuns de componentes de luz

Todos os tipos de luz `LightComponent` derivam da classe base abstrata e compartilham essas propriedades:

* **Cor:** A cor da luz no [espaço Gama.](https://en.wikipedia.org/wiki/SRGB) Alpha é ignorado.

* **Intensidade:** O brilho da luz. Para luzes pontuais e pontuais, a intensidade também define o quão longe a luz brilha.

## <a name="point-light"></a>Luz de ponto

Na renderização remota `PointLightComponent` do Azure, o pode não apenas emitir luz de um único ponto, mas também de uma pequena esfera ou de um pequeno tubo, para simular fontes de luz mais suaves.

### <a name="pointlightcomponent-properties"></a>Propriedades PointLightComponent

* **Raio:** O raio padrão é zero, nesse caso a luz age como uma luz de ponto. Se o raio for maior que zero, ele age como fonte de luz esférica, o que muda a aparência dos destaques especular.

* **Comprimento:** Se `Length` ambos `Radius` e não são zero, a luz age como uma luz de tubo. Isto pode ser usado para simular tubos de néon.

* **AtenuaçãoCutoff:** Se deixada para (0,0) a atenuação da luz `Intensity`só depende do seu . No entanto, você pode fornecer distâncias min/max personalizadas sobre as quais a intensidade da luz é dimensionada linearmente até 0. Este recurso pode ser usado para impor uma menor gama de influência de uma luz específica.

* **Cubemap projetado:** Se definido como um [cubemap](../../concepts/textures.md)válido, a textura é projetada na geometria circundante da luz. A cor do cubo é modulada com a cor da luz.

## <a name="spot-light"></a>Luz spot

O `SpotLightComponent` é semelhante `PointLightComponent` ao, mas a luz é restrita à forma de um cone. A orientação do cone é definida pelo *eixo z negativo da entidade proprietária.*

### <a name="spotlightcomponent-properties"></a>Propriedades do SpotLightComponent

* **Raio:** O mesmo `PointLightComponent`que para o .

* **SpotAngleDeg:** Este intervalo define o ângulo interno e externo do cone, medido em grau. Tudo dentro do ângulo interno é iluminado com brilho total. Uma queda é aplicada em direção ao ângulo externo que gera um efeito semelhante à penumbra.

* **FalloffExponent:** Define o quão acentuadamente a queda transita entre o ângulo interno e o cone externo. Um valor mais alto resulta em uma transição mais nítida. A inadimplência do 1.0 resulta em uma transição linear.

* **AtenuaçãoCutoff:** O mesmo `PointLightComponent`que para o .

* **Projeção2dTextura:** Se definida como uma [textura 2D](../../concepts/textures.md)válida, a imagem é projetada na geometria em que a luz brilha. A cor da textura é modulada com a cor da luz.

## <a name="directional-light"></a>Luz direcional

Simula `DirectionalLightComponent` uma fonte de luz que está infinitamente longe. A luz brilha na direção do *eixo z negativo da entidade proprietária.* A posição da entidade é ignorada.

Não há propriedades adicionais.

## <a name="performance-considerations"></a>Considerações sobre o desempenho

Fontes de luz têm um impacto significativo no desempenho de renderização. Use-os com cuidado e somente se necessário pelo aplicativo. Qualquer condição de iluminação global estática, incluindo um componente direcional estático, pode ser alcançada com uma [textura de céu personalizada,](sky.md)sem custo adicional de renderização.

## <a name="next-steps"></a>Próximas etapas

* [Materiais](../../concepts/materials.md)
* [Céu](sky.md)
