---
title: Adicionar uma Camada de Imagem para o Azure Mapas | Microsoft Docs
description: How to add an Image Layer to the Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: fadaaf7c64b11a6d6d94c68234f8288d1b3f8d07
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74480497"
---
# <a name="add-an-image-layer-to-a-map"></a>Adicionar uma camada de imagem ao um mapa

Este artigo mostra como você pode sobrepor uma imagem para um conjunto fixo de coordenadas do mapa. Há muitos cenários em que a sobreposição de uma imagem do mapa é feita. Aqui estão alguns exemplos dos tipos de imagens geralmente sobrepostos em mapas;

* Imagens capturadas de drones.
* Plantas baixas de construção.
* Histórico ou outras imagens de mapa especializado.
* Blueprints de sites de trabalho.
* Imagens de radar de clima.

> [!TIP]
> Uma [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) é uma maneira fácil de sobrepor uma imagem em um mapa. No entanto, se a imagem for grande, o navegador pode apresentar dificuldades para carregá-la. Nesse caso, considere dividir sua imagem em blocos e carregá-las no mapa como uma [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest).

The image layer supports the following image formats:

- JPEG
- PNG
- BMP
- GIF (no animations)

## <a name="add-an-image-layer"></a>Adicionar uma camada de imagem

In the following code overlays an image of a [map of Newark New Jersey from 1922](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) on the map. An [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) is created by passing a URL to an image and coordinates for the four corners in the format `[Top Left Corner, Top Right Corner, Bottom Right Corner, Bottom Left Corner]`.

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

Below is the complete running code sample of the above functionality.

<br/>

<iframe height='500' scrolling='no' title='Adicionar uma Camada de Imagem' src='//codepen.io/azuremaps/embed/eQodRo/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/eQodRo/'>Camada de Imagem Simples</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="import-a-kml-ground-overlay"></a>Importar uma sobreposição de terra KML

Este exemplo mostra como sobrepor informações de Sobreposição de Terra como uma camada de imagem no mapa. KML ground overlays provide north, south, east, and west coordinates and a counter-clockwise rotation, whereas the image layer expects coordinates for each corner of the image. A sobreposição de terra KML neste exemplo é da Catedral de Chartres e originada de [Wikimedia](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml).

The following code uses the static `getCoordinatesFromEdges` function of the [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) class to calculate the four corners of the image from the north, south, east, west and rotation information from the KML ground overlay.

<br/>

<iframe height='500' scrolling='no' title='Sobreposição de terra KML como camada de imagem' src='//codepen.io/azuremaps/embed/EOJgpj/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/EOJgpj/'>Sobreposição de Terra KML como Camada de Imagem</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-an-image-layer"></a>Customizar uma camada de imagem

A camada de imagem tem muitas opções de estilo. Aqui está uma ferramenta para experimentá-las.

<br/>

<iframe height='700' scrolling='no' title='Opções de Camada de Imagem' src='//codepen.io/azuremaps/embed/RqOGzx/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte as <a href='https://codepen.io/azuremaps/pen/RqOGzx/'>Opções de Camada de Imagem</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [ImageLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagelayeroptions?view=azure-iot-typescript-latest)

Consulte os artigos a seguir para obter mais exemplos de código para adicionar aos seus mapas:

> [!div class="nextstepaction"]
> [Adicionar uma camada de bloco](./map-add-tile-layer.md)