---
title: Adicione uma camada de imagem a um mapa | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá sobre como sobrepor uma imagem em um mapa usando o Microsoft Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 69bf41f9d88081b9a416b9bee91e8650a84f12c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77209708"
---
# <a name="add-an-image-layer-to-a-map"></a>Adicionar uma camada de imagem ao um mapa

Este artigo mostra como sobrepor uma imagem a um conjunto fixo de coordenadas. Aqui estão alguns exemplos de diferentes tipos de imagens que podem ser sobrepostos em mapas:

* Imagens capturadas de drones
* Plantas de piso de construção
* Imagens de mapas históricos ou outros mapas especializados
* Plantas de locais de trabalho
* Imagens de radar meteorológico

> [!TIP]
> Um [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) é uma maneira fácil de sobrepor uma imagem em um mapa. Observe que os navegadores podem ter dificuldade em carregar uma imagem grande. Neste caso, considere dividir sua imagem em telhas e carregá-las no mapa como um [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest).

A camada de imagem suporta os seguintes formatos de imagem:

- JPEG
- PNG
- BMP
- GIF (sem animações)

## <a name="add-an-image-layer"></a>Adicionar uma camada de imagem

O código a seguir sobrepõe uma imagem de um [mapa de Newark, New Jersey, de 1922](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) no mapa. Um [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) é criado passando uma URL para uma imagem e coordena `[Top Left Corner, Top Right Corner, Bottom Right Corner, Bottom Left Corner]`para os quatro cantos no formato .

```javascript
//Create an image layer and add it to the map.
map.layers.add(new atlas.layer.ImageLayer({
    url: 'newark_nj_1922.jpg',
    coordinates: [
        [-74.22655, 40.773941], //Top Left Corner
        [-74.12544, 40.773941], //Top Right Corner
        [-74.12544, 40.712216], //Bottom Right Corner
        [-74.22655, 40.712216]  //Bottom Left Corner
    ]
}));
```

Aqui está a amostra completa do código de execução do código anterior.

<br/>

<iframe height='500' scrolling='no' title='Adicionar uma Camada de Imagem' src='//codepen.io/azuremaps/embed/eQodRo/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/eQodRo/'>Camada de Imagem Simples</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="import-a-kml-file-as-ground-overlay"></a>Importe um arquivo KML como sobreposição de solo

Esta amostra demonstra como adicionar informações de sobreposição kml como uma camada de imagem no mapa. As sobreposições terrestres KML fornecem coordenadas norte, sul, leste e oeste, e uma rotação no sentido anti-horário. Mas, a camada de imagem espera coordenadas para cada canto da imagem. A sobreposição terrestre KML nesta amostra é para a catedral de Chartres, e é originária da [Wikimedia](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml).

O código usa `getCoordinatesFromEdges` a função estática da classe [ImageLayer.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) Ele calcula os quatro cantos da imagem usando as informações de norte, sul, leste, oeste e rotação da sobreposição do solo KML.

<br/>

<iframe height='500' scrolling='no' title='Sobreposição de terra KML como camada de imagem' src='//codepen.io/azuremaps/embed/EOJgpj/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/EOJgpj/'>Sobreposição de Terra KML como Camada de Imagem</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-an-image-layer"></a>Customizar uma camada de imagem

A camada de imagem tem muitas opções de estilo. Aqui está uma ferramenta para experimentá-los.

<br/>

<iframe height='700' scrolling='no' title='Opções de Camada de Imagem' src='//codepen.io/azuremaps/embed/RqOGzx/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte as <a href='https://codepen.io/azuremaps/pen/RqOGzx/'>Opções de Camada de Imagem</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [ImageLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagelayeroptions?view=azure-iot-typescript-latest)

Consulte os artigos a seguir para obter mais exemplos de código para adicionar aos seus mapas:

> [!div class="nextstepaction"]
> [Adicionar uma camada de bloco](./map-add-tile-layer.md)