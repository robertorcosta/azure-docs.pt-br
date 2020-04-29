---
title: Adicionar uma camada de extrusão de polígono a um mapa | Mapas do Microsoft Azure
description: Como adicionar uma camada de extrusão de polígono ao SDK da Web do Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 10/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 7405098bd4924333aafcd1c285eb2f37bb1d4f75
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80334551"
---
# <a name="add-a-polygon-extrusion-layer-to-the-map"></a>Adicionar uma camada de extrusão de polígono ao mapa

Este artigo mostra como usar a camada de extrusão de polígono para renderizar áreas `Polygon` de `MultiPolygon` geometrias de recursos como formas extrudadas. O SDK da Web do Azure Maps dá suporte à renderização de geometrias de círculo conforme definido no [esquema geojson estendido](extend-geojson.md#circle). Esses círculos podem ser transformados em polígonos quando renderizados no mapa. Todas as geometrias de recursos podem ser atualizadas facilmente quando encapsuladas com o [Atlas. ](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest)Classe de forma.

## <a name="use-a-polygon-extrusion-layer"></a>Usar uma camada de extrusão de polígono

Conecte a [camada de extrusão do polígono](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer?view=azure-maps-typescript-latest) a uma fonte de dados. Em seguida, o carregou no mapa. A camada de extrusão do polígono renderiza as áreas de `Polygon` a `MultiPolygon` e os recursos como formas de extrusão. As `height` propriedades `base` e da camada de extrusão do polígono definem a distância base do chão e da altura da forma extrudada em **metros**. O código a seguir mostra como criar um polígono, adicioná-lo a uma fonte de dados e renderizá-lo usando a classe da camada de extrusão do polígono.

> [!Note]
> O `base` valor definido na camada de extrusão do polígono deve ser menor ou igual ao do `height`.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Polígono extrudada" src="https://codepen.io/azuremaps/embed/wvvBpvE?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte o <a href='https://codepen.io/azuremaps/pen/wvvBpvE'>polígono extrudada</a> por caneta pelo mapas do Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>() em <a href='https://codepen.io'>CodePen</a>.</iframe>


## <a name="add-data-driven-polygons"></a>Adicionar polígonos controlados por dados

Um mapa coropléticos pode ser renderizado usando a camada de extrusão do polígono. Defina as `height` propriedades `fillColor` e da camada de extrusão com a medida da variável estatística nas `Polygon` geometrias de `MultiPolygon` recurso. O exemplo de código a seguir mostra um mapa coropléticos extrudada dos U. S com base na medida da densidade da população por Estado.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Mapa de coropléticos extrudada" src="https://codepen.io/azuremaps/embed/eYYYNox?height=265&theme-id=0&default-tab=result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Confira o <a href='https://codepen.io/azuremaps/pen/eYYYNox'>mapa coropléticos de extrusão</a> de caneta pelo Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>Maps () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-a-circle-to-the-map"></a>Adicionar um círculo ao mapa

O Azure Maps usa uma versão estendida do esquema geojson que fornece uma definição para círculos, conforme observado [aqui](https://docs.microsoft.com/azure/azure-maps/extend-geojson#circle). Um círculo com extrusão pode ser renderizado no mapa criando `point` um recurso com uma `subType` propriedade de `Circle` e uma `Radius` Propriedade numerada que representa o raio em **metros**. Por exemplo:

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

O SDK da Web do Azure Maps `Point` converte esses `Polygon` recursos em recursos nos bastidores. Esses `Point` recursos podem ser renderizados no mapa usando a camada de extrusão de polígono, conforme mostrado no exemplo de código a seguir.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Polígono do espaço aéreo do drone" src="https://codepen.io/azuremaps/embed/zYYYrxo?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte o <a href='https://codepen.io/azuremaps/pen/zYYYrxo'>polígono do drone de área</a> de tinta pelo Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>Maps () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-polygon-extrusion-layer"></a>Personalizar uma camada de extrusão de polígono

A camada de extrusão do polígono tem várias opções de estilo. Aqui está uma ferramenta para experimentá-las.

<br/>

<iframe height='700' scrolling='no' title='PoogBRJ' src='//codepen.io/azuremaps/embed/PoogBRJ/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a <a href='https://codepen.io/azuremaps/pen/PoogBRJ/'>PoogBRJ</a> de caneta por mapas do<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [camada de extrusão do polígono](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer?view=azure-maps-typescript-latest)

Recursos adicionais:

> [!div class="nextstepaction"]
> [Extensão de especificação geojson do Azure Maps](extend-geojson.md#circle)
