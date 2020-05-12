---
title: Adicionar um marcador HTML ao mapa | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá a adicionar um marcador HTML a um mapa usando o SDK da Web do Microsoft Azure Maps.
author: Philmea
ms.author: philmea
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 30d9cf9848a8d99505bf9f9c97bef5aaa31065ee
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83119289"
---
# <a name="add-html-markers-to-the-map"></a>Adicionar marcadores HTML ao mapa

Este artigo mostra como adicionar um HTML personalizado, como um arquivo de imagem, ao mapa como um marcador HTML.

> [!NOTE]
> Os marcadores HTML não conectam fontes de dados. Em vez disso, as informações de posição são adicionadas diretamente ao marcador e o marcador é adicionado à propriedade `markers` do mapa que é um [HtmlMarkerManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkermanager?view=azure-iot-typescript-latest).

> [!IMPORTANT]
> Ao contrário da maioria das camadas no controle de Web do Azure Mapas que usam WebGL para renderização, os marcadores HTML usam elementos DOM tradicionais para renderização. Assim, quanto mais marcadores HTML foram adicionados a uma página, mais elementos DOM existem. O desempenho poderá ser prejudicado após adicionar algumas centenas de marcadores HTML. Para conjuntos de dados maiores, considere o clustering de dados ou utilizar uma camada de Símbolos ou Bolhas.

## <a name="add-an-html-marker"></a>Adicionar um marcador HTML

A classe [HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest) tem um estilo padrão. É possível personalizar o marcador, definindo as opções de cor e texto do marcador. O estilo padrão da classe de marcador HTML é um modelo SVG que tem um `{color}` `{text}` espaço reservado e. Defina as propriedades de cor e texto nas opções de marcador HTML para uma personalização rápida. 

O código a seguir cria um marcador HTML e define a Propriedade Color como "DodgerBlue" e a propriedade Text como "10". Um popup é anexado ao marcador e o `click` evento é usado para alternar a visibilidade do pop-up.

```javascript
//Create an HTML marker and add it to the map.
var marker = new atlas.HtmlMarker({
    color: 'DodgerBlue',
    text: '10',
    position: [0, 0],
    popup: new atlas.Popup({
        content: '<div style="padding:10px">Hello World</div>',
        pixelOffset: [0, -30]
    })
});

map.markers.add(marker);

//Add a click event to toggle the popup.
map.events.add('click',marker, () => {
    marker.togglePopup();
});
```

Abaixo está o exemplo de código completo em execução da funcionalidade acima.

<br/>

<iframe height='500' scrolling='no' title='Adicionar um marcador HTML a um mapa' src='//codepen.io/azuremaps/embed/MVoeVw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/MVoeVw/'>Adicionar um marcador HTML a um mapa</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="create-svg-templated-html-marker"></a>Criar um marcador HTML com modelo de SVG

O padrão `htmlContent` de um marcador HTML é um modelo de SVG contendo as pastas de local `{color}` e `{text}`. É possível criar cadeia de caracteres de SVG personalizadas e adicionar esses mesmos espaços reservados ao SVG, de forma que a configuração das opções `color` e `text` do marcador atualize esses espaços reservados no SVG.

<br/>

<iframe height='500' scrolling='no' title='Marcador HTML com modelo de SVG personalizado' src='//codepen.io/azuremaps/embed/LXqMWx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/LXqMWx/'>Marcador HTML com modelo de SVG personalizado</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> O SDK da Web do Azure Maps fornece vários modelos de imagem SVG que podem ser usados com marcadores de HTML. Para obter mais informações, consulte o documento [como usar modelos de imagem](how-to-use-image-templates-web-sdk.md) .

## <a name="add-a-css-styled-html-marker"></a>Adicionar um marcador HTML com estilo CSS

Um dos benefícios dos marcadores HTML é que há muitas ótimas personalizações que podem ser obtidas usando CSS. Nesse exemplo, o conteúdo do HtmlMarker consiste em HTML e CSS que criam um marcador animado que cai no local e pulsa.

<br/>

<iframe height='500' scrolling='no' title='DataSource HTML' src='//codepen.io/azuremaps/embed/qJVgMx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja a Caneta <a href='https://codepen.io/azuremaps/pen/qJVgMx/'>DataSource HTML</a> pelos Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="draggable-html-markers"></a>Marcadores HTML arrastáveis

Esse exemplo mostra como tornar um marcador HTML arrastável. Os marcadores de HTML dão suporte a `drag` `dragstart` eventos, e `dragend` .

<br/>

<iframe height='500' scrolling='no' title='Marcador HTML arrastável' src='//codepen.io/azuremaps/embed/wQZoEV/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/wQZoEV/'>Marcador HTML arrastável</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-mouse-events-to-html-markers"></a>Adicionar eventos do mouse a marcadores HTML

Estes exemplos mostram como adicionar eventos do mouse e de arrastar a um marcador HTML.

<br/>

<iframe height='500' scrolling='no' title='Adicionando eventos do mouse a marcadores HTML' src='//codepen.io/azuremaps/embed/RqOKRz/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/RqOKRz/'>Adicionando eventos do mouse a marcadores HTML</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HtmlMarkerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HtmlMarkerManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkermanager?view=azure-iot-typescript-latest)

Para obter mais exemplos de código para adicionar aos seus mapas, consulte os seguintes artigos:

> [!div class="nextstepaction"]
> [Como usar modelos de imagem](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de símbolo](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de bolha](./map-add-bubble-layer.md)
