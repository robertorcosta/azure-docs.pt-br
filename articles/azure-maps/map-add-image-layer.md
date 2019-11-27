---
title: Adicionar uma Camada de Imagem para o Azure Mapas | Microsoft Docs
description: Como adicionar uma camada de imagem ao SDK da Web do Azure Maps.
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

A camada de imagem dá suporte aos seguintes formatos de imagem:

- JPEG
- PNG
- BMP
- GIF (sem animações)

## <a name="add-an-image-layer"></a>Adicionar uma camada de imagem

No código a seguir, sobrepõe uma imagem de um [mapa de Newark New Jersey de 1922](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) no mapa. Um [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) é criado passando uma URL para uma imagem e coordenadas para os quatro cantos no formato `[Top Left Corner, Top Right Corner, Bottom Right Corner, Bottom Left Corner]`.

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

Abaixo está o exemplo de código completo em execução da funcionalidade acima.

<br/>

<iframe height='500' scrolling='no' title='Adicionar uma Camada de Imagem' src='//codepen.io/azuremaps/embed/eQodRo/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/eQodRo/'>Camada de Imagem Simples</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="import-a-kml-ground-overlay"></a>Importar uma sobreposição de terra KML

Este exemplo mostra como sobrepor informações de Sobreposição de Terra como uma camada de imagem no mapa. As sobreposições de aterramento KML fornecem coordenadas norte, Sul, leste e oeste e uma rotação no sentido anti-horário, enquanto a camada de imagem espera coordenadas para cada canto da imagem. A sobreposição de terra KML neste exemplo é da Catedral de Chartres e originada de [Wikimedia](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml).

O código a seguir usa a função estática `getCoordinatesFromEdges` da classe [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) para calcular os quatro cantos da imagem das informações norte, Sul, leste, oeste e de rotação da sobreposição de aterramento KML.

<br/>

<iframe height='500' scrolling='no' title='Sobreposição de terra KML como camada de imagem' src='//codepen.io/azuremaps/embed/EOJgpj/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/EOJgpj/'>Sobreposição de Terra KML como Camada de Imagem</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-an-image-layer"></a>Customizar uma camada de imagem

A camada de imagem tem muitas opções de estilo. Aqui está uma ferramenta para experimentá-las.

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