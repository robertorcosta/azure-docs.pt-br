---
title: Módulo de ferramentas de desenho | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá como definir dados de opções de desenho usando o Microsoft Azure Maps Web SDK
author: philmea
ms.author: philmea
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 711609f9382e2153cbc738d544933796dbbe2e99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334305"
---
# <a name="use-the-drawing-tools-module"></a>Usar o módulo de ferramentas de desenho

O Web SDK do Azure Maps fornece um *módulo de ferramentas de desenho*. Este módulo facilita a desenho e edição de formas no mapa usando um dispositivo de entrada, como um mouse ou tela sensível ao toque. A classe principal deste módulo é o [gerenciador de desenho](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#setoptions-drawingmanageroptions-). O gerenciador de desenho fornece todos os recursos necessários para desenhar e editar formas no mapa. Ele pode ser usado diretamente, e é integrado com uma ui de barra de ferramentas personalizada. Você também pode usar a classe de barra de ferramentas de [desenho](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest) incorporada. 

## <a name="loading-the-drawing-tools-module-in-a-webpage"></a>Carregando o módulo de ferramentas de desenho em uma página web

1. Crie um novo arquivo HTML e [implemente o mapa como de costume](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control).
2. Carregue o módulo de ferramentas de desenho do Azure Maps. Você pode carregá-lo de duas maneiras:
    - Use a versão globalmente hospedada, Azure Content Delivery Network do módulo de serviços Do Azure Maps. Adicionar referência à folha de estilos JavaScript `<head>` e CSS no elemento do arquivo:

        ```html
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/drawing/0/atlas-drawing.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/drawing/0/atlas-drawing.min.js"></script>
        ```

    - Ou, você pode carregar o módulo de ferramentas de desenho para o código-fonte do Azure Maps Web SDK localmente usando o pacote npm de [ferramentas de desenho de mapas azure](https://www.npmjs.com/package/azure-maps-drawing-tools) e, em seguida, hospedá-lo com o seu aplicativo. Esse pacote também inclui definições de TypeScript. Use este comando:
    
        > **npm instalar azure-mapas-drawing-tools**
    
        Em seguida, adicione uma referência à folha de `<head>` estilos JavaScript e CSS no elemento do arquivo:

         ```html
        <link rel="stylesheet" href="node_modules/azure-maps-drawing-tools/dist/atlas-drawing.min.css" type="text/css" />
        <script src="node_modules/azure-maps-drawing-tools/dist/atlas-drawing.min.js"></script>
         ```

## <a name="use-the-drawing-manager-directly"></a>Use o gerenciador de desenho diretamente

Uma vez que o módulo de ferramentas de desenho esteja carregado em seu aplicativo, você pode habilitar os recursos de desenho e edição usando o [gerenciador de desenho](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#setoptions-drawingmanageroptions-). Você pode especificar opções para o gerenciador de `drawingManager.setOptions()` desenho enquanto instancia-lo ou, alternativamente, usar a função.

### <a name="set-the-drawing-mode"></a>Defina o modo de desenho

O código a seguir cria uma instância do gerenciador de desenho e define a opção **de modo** de desenho. 

```Javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon"
});
```

O código abaixo é um exemplo completo de como definir um modo de desenho do gerenciador de desenho. Clique no mapa para começar a desenhar um polígono.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Desenhe um polígono" src="//codepen.io/azuremaps/embed/YzKVKRa/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Veja a Caneta <a href='https://codepen.io/azuremaps/pen/YzKVKRa/'>Desenhar um polígono</a> pelo Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) no <a href='https://codepen.io'>CodePen</a>.
</iframe>


### <a name="set-the-interaction-type"></a>Defina o tipo de interação

O gerenciador de desenho suporta três maneiras diferentes de interagir com o mapa para desenhar formas.

* `click`- Coordenadas são adicionadas quando o mouse ou o toque são clicados.
* `freehand `- As coordenadas são adicionadas quando o mouse ou o toque são arrastados no mapa. 
* `hybrid`- As coordenadas são adicionadas quando o mouse ou o toque são clicados ou arrastados.

O código a seguir permite o modo de desenho do polígono `freehand`e define o tipo de interação de desenho que o gerenciador de desenho deve aderir . 

```Javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon",
    interactionType: "freehand"
});
```

 Esta amostra de código implementa a funcionalidade de desenhar um polígono no mapa. Basta segurar o botão esquerdo do mouse e arrastá-lo ao redor, livremente.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Desenho à mão livre" src="//codepen.io/azuremaps/embed/ZEzKoaj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Veja o <a href='https://codepen.io/azuremaps/pen/ZEzKoaj/'>desenho de caneta à mão livre</a> do Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) no <a href='https://codepen.io'>CodePen</a>.
</iframe>


### <a name="customizing-drawing-options"></a>Personalização de opções de desenho

Os exemplos anteriores demonstraram como personalizar opções de desenho enquanto instanciavam o Gerenciador de desenho. Você também pode definir as opções do Gerenciador de desenho usando a `drawingManager.setOptions()` função. Abaixo está uma ferramenta para testar a personalização de todas as opções para o gerenciador de desenho usando a função setOptions.

<br/>

<iframe height="685" title="Personalizar gerenciador de desenho" src="//codepen.io/azuremaps/embed/LYPyrxR/?height=600&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" style='width: 100%;'>Consulte os <a href='https://codepen.io/azuremaps/pen/LYPyrxR/'>dados de forma</a> da<a href='https://codepen.io/azuremaps'>@azuremaps</a>caneta Obter por Azure Maps ( ) no <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Próximas etapas

Saiba como usar recursos adicionais do módulo de ferramentas de desenho:

> [!div class="nextstepaction"]
> [Adicionar uma barra de ferramentas de desenho](map-add-drawing-toolbar.md)

> [!div class="nextstepaction"]
> [Obter dados da forma](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [Reagir a eventos de desenho](drawing-tools-events.md)

> [!div class="nextstepaction"]
> [Tipos de interação e atalhos de teclado](drawing-tools-interactions-keyboard-shortcuts.md)

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Gerente de desenho](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Barra de ferramentas de desenho](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)
