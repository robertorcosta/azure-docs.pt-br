---
title: Modelos de imagem no Web SDK do Azure Maps | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá a usar modelos de imagem com marcadores HTML e várias camadas no Microsoft Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 8/6/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: ee8e8ee4ca64de0390b6fa34e36fb4d06348a8ac
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804802"
---
# <a name="how-to-use-image-templates"></a>Como usar modelos de imagem

As imagens podem ser usadas com marcadores HTML e várias camadas dentro do SDK web do Azure Maps:

 - As camadas de símbolo podem renderizar pontos no mapa com um ícone de imagem. Os símbolos também podem ser renderizados ao longo de um caminho de linhas.
 - As camadas de polígono podem ser renderizadas com uma imagem padrão de preenchimento. 
 - Os marcadores HTML podem renderizar pontos usando imagens e outros elementos HTML.

Para garantir um bom desempenho com camadas, carregue as imagens no recurso sprite de imagem do mapa antes de renderizar. O [IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions), do SymbolLayer, pré-carrega um par de imagens marcadores em um punhado de cores no sprite de imagem do mapa, por padrão. Essas imagens de marcador e muito mais estão disponíveis como modelos SVG. Eles podem ser usados para criar imagens com escalas personalizadas, ou usados como uma cor primária e secundária do cliente. No total, são 42 modelos de imagem fornecidos: 27 ícones de símbolos e 15 padrões de preenchimento de polígonos.

Modelos de imagem podem ser adicionados aos `map.imageSprite.createFromTemplate` recursos de sprite de imagem do mapa usando a função. Esta função permite que até cinco parâmetros sejam passados;

```javascript
createFromTemplate(id: string, templateName: string, color?: string, secondaryColor?: string, scale?: number): Promise<void>
```

O `id` é um identificador único que você cria. O `id` é atribuído à imagem quando é adicionado ao sprite de imagem dos mapas. Use este identificador nas camadas para especificar qual recurso de imagem renderizar. O `templateName` especifica qual modelo de imagem usar. A `color` opção define a cor primária `secondaryColor` da imagem e as opções definem a cor secundária da imagem. A `scale` opção dimensiona o modelo de imagem antes de aplicá-lo ao sprite de imagem. Quando a imagem é aplicada ao sprite de imagem, ela é convertida em um PNG. Para garantir a renderização nítida, é melhor dimensionar o modelo de imagem antes de adicioná-lo ao sprite, do que dimensioná-lo em uma camada.

Esta função carrega assíncronamente a imagem no sprite da imagem. Assim, ele retorna uma promessa de que você pode esperar que esta função seja concluída.

O código a seguir mostra como criar uma imagem a partir de um dos modelos incorporados e usá-la com uma camada de símbolo.

```javascript
map.imageSprite.createFromTemplate('myTemplatedIcon', 'marker-flat', 'teal', '#fff').then(function () {

    //Add a symbol layer that uses the custom created icon.
    map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            image: 'myTemplatedIcon'
        }
    }));
});
```

## <a name="use-an-image-template-with-a-symbol-layer"></a>Use um modelo de imagem com uma camada de símbolo

Uma vez que um modelo de imagem é carregado no sprite de imagem do mapa, ele pode `image` ser renderizado como um símbolo em uma camada de símbolo, fazendo referência ao ID de recurso de imagem na opção do `iconOptions`.

A amostra a seguir renderiza `marker-flat` uma camada de símbolo usando o modelo de imagem com uma cor primária de teal e uma cor secundária branca. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Camada de símbolo com modelo de ícone incorporado" src="//codepen.io/azuremaps/embed/VoQMPp/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte a <a href='https://codepen.io/azuremaps/pen/VoQMPp/'>camada Símbolo da caneta com o modelo de ícone incorporado</a> pelo Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="use-an-image-template-along-a-lines-path"></a>Use um modelo de imagem ao longo de um caminho de linhas

Uma vez que um modelo de imagem é carregado no sprite de imagem do mapa, ele pode ser renderizado `lineSpacing`ao longo do caminho de uma linha `image` adicionando `iconOptions`uma Linha de Dados a uma fonte de dados e usando uma camada de símbolo com uma opção e fazendo referência ao ID do recurso de imagem na opção th . 

A amostra a seguir renderiza uma linha rosa no `car` mapa e usa uma camada de símbolo usando o modelo de imagem com uma cor primária azul dodger e uma cor secundária branca. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Camada de linha com modelo de ícone incorporado" src="//codepen.io/azuremaps/embed/KOQvJe/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte a camada De <a href='https://codepen.io/azuremaps/pen/KOQvJe/'>linha de caneta com modelo de ícone incorporado</a> pelo Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Se o modelo de imagem `rotation` apontar para cima, defina a opção de ícone da camada símbolo para 90 se você quiser que ela aponte na mesma direção que a linha.

## <a name="use-an-image-template-with-a-polygon-layer"></a>Use um modelo de imagem com uma camada de polígono

Uma vez que um modelo de imagem é carregado no sprite de imagem do mapa, ele pode ser renderizado como um padrão de preenchimento em uma camada de polígono, fazendo referência ao ID de recurso de imagem na `fillPattern` opção da camada.

A amostra a seguir renderiza uma `dot` camada de polígono usando o modelo de imagem com uma cor primária vermelha e uma cor secundária transparente.  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Preencha o polígono com o modelo de ícone incorporado" src="//codepen.io/azuremaps/embed/WVMEmz/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte o <a href='https://codepen.io/azuremaps/pen/WVMEmz/'>polígono</a> de preenchimento de caneta com<a href='https://codepen.io/azuremaps'>@azuremaps</a>modelo de ícone incorporado pelo Azure Maps ( ) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Definir a cor secundária dos padrões de preenchimento torna mais fácil ver que o mapa subjacente ainda fornecerá o padrão primário. 

## <a name="use-an-image-template-with-an-html-marker"></a>Use um modelo de imagem com um marcador HTML

Um modelo de imagem pode `altas.getImageTemplate` ser recuperado usando a função e usado como o conteúdo de um marcador HTML. O modelo pode ser `htmlContent` passado para a opção do `color`marcador `secondaryColor`e, em seguida, personalizado usando as opções e `text` opções.

A amostra a `marker-arrow` seguir usa o modelo com uma cor primária vermelha, uma cor secundária rosa e um valor de texto de "00".

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Marcador HTML com modelo de ícone incorporado" src="//codepen.io/azuremaps/embed/EqQvzq/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte o marcador HTML caneta <a href='https://codepen.io/azuremaps/pen/EqQvzq/'>com o modelo de ícone incorporado</a> pelo Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="create-custom-reusable-templates"></a>Crie modelos reutilizáveis personalizados

Se o aplicativo usar o mesmo ícone com ícones diferentes ou se você estiver criando um módulo que adiciona modelos de imagem adicionais, você pode facilmente adicionar e recuperar esses ícones do SDK web do Azure Maps. Use as seguintes funções estáticas no `atlas` namespace.

| Nome | Tipo de retorno | Descrição | 
|-|-|-|
| `addImageTemplate(templateName: string, template: string, override: boolean)` | | Adiciona um modelo de imagem SVG personalizado ao espaço de nome do Atlas. |
|  `getImageTemplate(templateName: string, scale?: number)`| string | Recupera um modelo SVG pelo nome. |
| `getAllImageTemplateNames()` | string[] |  Recupera um modelo SVG pelo nome. |

Os modelos de imagem SVG suportam os seguintes valores de espaço reservado:

| Espaço reservado | Descrição |
|-|-|
| `{color}` | A cor primária. | 
| `{secondaryColor}` | A cor secundária. | 
| `{scale}` | A imagem SVG é convertida em uma imagem png quando adicionada ao sprite de imagem do mapa. Este espaço reservado pode ser usado para dimensionar um modelo antes de ser convertido para garantir que ele renderiza claramente. | 
| `{text}` | O local para renderizar texto quando usado com um marcador HTML. |

O exemplo a seguir mostra como pegar um modelo SVG e adicioná-lo ao SDK web do Azure Maps como um modelo de ícone reutilizável. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Adicionar modelo de ícone personalizado ao espaço de nome do Atlas" src="//codepen.io/azuremaps/embed/NQyvEX/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte o modelo de ícone personalizado Da <a href='https://codepen.io/azuremaps/pen/NQyvEX/'>caneta ao espaço de nome atlas</a> pelo Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="list-of-image-templates"></a>Lista de modelos de imagem

Esta tabela lista todos os modelos de imagem atualmente disponíveis no SDK web do Azure Maps. O nome do modelo está acima de cada imagem. Por padrão, a cor principal é azul e a cor secundária é branca. Para tornar a cor secundária mais fácil de ver em um fundo branco, as imagens a seguir têm a cor secundária definida como preto.

**Modelos de ícones de símbolo**

|||||
|:-:|:-:|:-:|:-:|
| marcador | marcador de espessura | marcador-círculo | marcador-plana |
|![ícone marcador](./media/image-templates/marker.png)|![ícone de espessura de marcadores](./media/image-templates/marker-thick.png)|![ícone do círculo de marcadores](./media/image-templates/marker-circle.png)|![ícone marcador-plano](./media/image-templates/marker-flat.png)|
||||
| marcador-quadrado | marcador-quadrado-cluster | marcador-seta | marcador-bola-pino | 
|![ícone marcador-quadrado](./media/image-templates/marker-square.png)|![ícone de cluster quadrado de marcador](./media/image-templates/marker-square-cluster.png)|![ícone de seta de marcador](./media/image-templates/marker-arrow.png)|![ícone marcador-bola-pino](./media/image-templates/marker-ball-pin.png)|
||||
| marcador-quadrado-arredondado | marcador-quadrado-rounded-cluster | flag | bandeira triângulo |
| ![ícone de marcador quadrado-arredondado](./media/image-templates/marker-square-rounded.png) | ![ícone de cluster quadrado-marcador](./media/image-templates/marker-square-rounded-cluster.png) | ![ícone da bandeira](./media/image-templates/flag.png) | ![ícone de triângulo de bandeira](./media/image-templates/flag-triangle.png) |
||||
| triangle | triângulo de espessura | triângulo-seta-up | triângulo-seta-esquerda |
| ![ícone triângulo](./media/image-templates/triangle.png) | ![ícone de espessura de triângulo](./media/image-templates/triangle-thick.png) | ![triângulo-seta-up ícone](./media/image-templates/triangle-arrow-up.png) | ![triângulo-seta-esquerda ícone](./media/image-templates/triangle-arrow-left.png) |
||||
| Hexágono | hexágono-grosso | hexágono arredondado | hexágono-arredondado-grosso |
| ![ícone hexágono](./media/image-templates/hexagon.png) | ![ícone hexágono-grosso](./media/image-templates/hexagon-thick.png) | ![ícone hexágono-arredondado](./media/image-templates/hexagon-rounded.png) | ![hexágono-arredondado-grosso ícone](./media/image-templates/hexagon-rounded-thick.png) |
||||
| pin | pin-round | arredondado quadrado | arredondado quadrado-grosso |
| ![ícone fixar](./media/image-templates/pin.png) | ![ícone pin-round](./media/image-templates/pin-round.png) | ![ícone quadrado arredondado](./media/image-templates/rounded-square.png) | ![ícone de espessura quadrada arredondada](./media/image-templates/rounded-square-thick.png) |
||||
| seta-up | seta-up-thin | carro ||
| ![ícone seta-up](./media/image-templates/arrow-up.png) | ![seta-up-thin ícone](./media/image-templates/arrow-up-thin.png) | ![ícone do carro](./media/image-templates/car.png) | |

**Modelos de padrão de preenchimento de polígonos**

|||||
|:-:|:-:|:-:|:-:|
| Verificador | verificador-girado | círculos | círculos espaçados |
| ![ícone de verificador](./media/image-templates/checker.png) | ![ícone girado por checker](./media/image-templates/checker-rotated.png) | ![ícone círculos](./media/image-templates/circles.png) | ![ícone espaçado em círculos](./media/image-templates/circles-spaced.png) |
|||||
| linhas diagonais | diagonal-linhas para baixo | diagonal-listras-up | diagonal-listras para baixo |
| ![ícone de linhas diagonais](./media/image-templates/diagonal-lines-up.png) | ![ícone diagonal-linhas para baixo](./media/image-templates/diagonal-lines-down.png) | ![ícone diagonal-listras-up](./media/image-templates/diagonal-stripes-up.png) | ![ícone diagonal-listras para baixo](./media/image-templates/diagonal-stripes-down.png) |
|||||
| linhas de grade | linhas de grade giratória | rotações de grade-listras | x-fill |
| ![ícone de linhas de grade](./media/image-templates/grid-lines.png) | ![ícone de linhas de grade giratória](./media/image-templates/rotated-grid-lines.png) | ![ícone de listras de grade giratória](./media/image-templates/rotated-grid-stripes.png) | ![ícone de preenchimento x](./media/image-templates/x-fill.png) |
|||||
| zig-zag | zig-zag-vertical | Pontos |  |
| ![ícone zig-zag](./media/image-templates/zig-zag.png) | ![ícone zig-zag-vertical](./media/image-templates/zig-zag-vertical.png) | ![ícone pontos](./media/image-templates/dots.png) | |

## <a name="try-it-now-tool"></a>Experimente agora ferramenta

Com a seguinte ferramenta, você pode renderizar os diferentes modelos de imagem incorporados de várias maneiras e personalizar as cores e escala primárias e secundárias.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Opções de modelo de ícone" src="//codepen.io/azuremaps/embed/NQyaaO/?height=500&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte as <a href='https://codepen.io/azuremaps/pen/NQyaaO/'>opções de modelo de ícone de caneta</a> pelo Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [ImageSpriteManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagespritemanager)

> [!div class="nextstepaction"]
> [espaço de nome atlas](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-maps-typescript-latest#functions
)

Consulte os artigos a seguir para obter mais amostras de código onde os modelos de imagem podem ser usados:

> [!div class="nextstepaction"]
> [Adicione uma camada de símbolo](map-add-pin.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de linha](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de polígono](map-add-shape.md)

> [!div class="nextstepaction"]
> [Adicionar marcadores HTML](map-add-bubble-layer.md)
