---
title: Adicione uma camada de extrusão de polígono a um mapa | Mapas do Microsoft Azure
description: Como adicionar uma camada de extrusão de polígono ao Microsoft Azure Maps Web SDK.
author: philmea
ms.author: philmea
ms.date: 10/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 7405098bd4924333aafcd1c285eb2f37bb1d4f75
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334551"
---
# <a name="add-a-polygon-extrusion-layer-to-the-map"></a>Adicione uma camada de extrusão de polígono ao mapa

Este artigo mostra como usar a camada de extrusão do `Polygon` `MultiPolygon` polígono para renderizar áreas e apresentar geometrias como formas extrudadas. O Azure Maps Web SDK suporta renderização de geometrias de círculo conforme definido no [esquema GeoJSON estendido](extend-geojson.md#circle). Esses círculos podem ser transformados em polígonos quando renderizados no mapa. Todas as geometrias de recursos podem ser atualizadas facilmente quando embrulhadas com o [atlas. Classe de forma.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest)

## <a name="use-a-polygon-extrusion-layer"></a>Use uma camada de extrusão de polígono

Conecte a [camada de extrusão do polígono](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer?view=azure-maps-typescript-latest) a uma fonte de dados. Então, carregou no mapa. A camada de extrusão do polígono `Polygon` `MultiPolygon` torna as áreas de a e características como formas extrudadas. As `height` `base` propriedades da camada de extrusão do polígono definem a distância base do solo e a altura da forma extrudada em **metros**. O código a seguir mostra como criar um polígono, adicioná-lo a uma fonte de dados e torná-lo usando a classe de camada de extrusão Polygon.

> [!Note]
> O `base` valor definido na camada de extrusão do polígono deve `height`ser menor ou igual ao do .

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Polígono extrudado" src="https://codepen.io/azuremaps/embed/wvvBpvE?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte o <a href='https://codepen.io/azuremaps/pen/wvvBpvE'>polígono pen extrudado</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>pelo Azure Maps ( ) no <a href='https://codepen.io'>CodePen</a>.</iframe>


## <a name="add-data-driven-polygons"></a>Adicionar polígonos baseados em dados

Um mapa de choropleth pode ser renderizado usando a camada de extrusão do polígono. Defina `height` `fillColor` as propriedades da camada de extrusão para a `Polygon` medição `MultiPolygon` da variável estatística nas geometrias e características. A amostra de código a seguir mostra um mapa de choroplita extrudado dos EUA com base na medição da densidade populacional por estado.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Mapa do choroplérico extrudado" src="https://codepen.io/azuremaps/embed/eYYYNox?height=265&theme-id=0&default-tab=result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Veja o mapa de <a href='https://codepen.io/azuremaps/pen/eYYYNox'>choropleth pen extrudado</a> do Azure Maps no<a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'>CodePen.</a>
</iframe>

## <a name="add-a-circle-to-the-map"></a>Adicionar um círculo ao mapa

O Azure Maps usa uma versão estendida do esquema GeoJSON que fornece uma definição para círculos como observado [aqui](https://docs.microsoft.com/azure/azure-maps/extend-geojson#circle). Um círculo extrudado pode ser renderizado `point` no mapa `subType` criando `Circle` um recurso `Radius` com uma propriedade e uma propriedade numerada representando o raio em **metros**. Por exemplo: 

```Javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-105.203135, 39.664087]
    },
    "properties": {
        "subType": "Circle",
        "radius": 1000
    }
} 
```

O Azure Maps Web SDK converte esses `Point` recursos em `Polygon` recursos o capô. Esses `Point` recursos podem ser renderizados no mapa usando a camada de extrusão do polígono, conforme mostrado na amostra de código a seguir.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Polígono do espaço aéreo drone" src="https://codepen.io/azuremaps/embed/zYYYrxo?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Veja o <a href='https://codepen.io/azuremaps/pen/zYYYrxo'>polígono do espaço</a> aéreo<a href='https://codepen.io/azuremaps'>@azuremaps</a>Pen Drone por Azure Maps ( ) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-polygon-extrusion-layer"></a>Personalize uma camada de extrusão de polígono

A camada de extrusão polygon tem várias opções de estilo. Aqui está uma ferramenta para experimentá-las.

<br/>

<iframe height='700' scrolling='no' title='PoogBRJ' src='//codepen.io/azuremaps/embed/PoogBRJ/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja o Pen <a href='https://codepen.io/azuremaps/pen/PoogBRJ/'>PoogBRJ</a> do<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps ( ) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Polígono](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [camada de extrusão de polígono](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer?view=azure-maps-typescript-latest)

Recursos adicionais:

> [!div class="nextstepaction"]
> [Extensão de especificação Do Azure Maps GeoJSON](extend-geojson.md#circle)
