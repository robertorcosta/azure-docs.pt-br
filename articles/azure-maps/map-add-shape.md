---
title: Adicione uma camada de polígono a um mapa | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá como renderizar um polígono e um polígono multi em um mapa no Microsoft Azure Maps Web SDK.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 9191f054ca3c7374bcbc7bec46573289a512612c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535045"
---
# <a name="add-a-polygon-layer-to-the-map"></a>Adicione uma camada de polígono ao mapa

Este artigo mostra como renderizar `Polygon` `MultiPolygon` as áreas e apresentar geometrias no mapa usando uma camada de polígono. O Azure Maps Web SDK também suporta a criação de geometrias de círculo definidas no [esquema GeoJSON estendido](extend-geojson.md#circle). Esses círculos são transformados em polígonos quando renderizados no mapa. Todas as geometrias de recursos podem ser facilmente atualizadas quando embrulhadas com o [atlas. Classe de forma.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest)

## <a name="use-a-polygon-layer"></a>Use uma camada de polígono 

Quando uma camada de polígono é conectada a uma fonte de `Polygon` dados `MultiPolygon` e carregada no mapa, ela renderiza a área com e recursos. Para criar um polígono, adicione-o a uma fonte de dados e torne-o com uma camada de polígono usando a classe [PolygonLayer.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a rectangular polygon.
dataSource.add(new atlas.data.Feature(
    new atlas.data.Polygon([[
        [-73.98235, 40.76799],
        [-73.95785, 40.80044],
        [-73.94928, 40.7968],
        [-73.97317, 40.76437],
        [-73.98235, 40.76799]
    ]])
));

//Create and add a polygon layer to render the polygon to the map, below the label layer.
map.layers.add(new atlas.layer.PolygonLayer(dataSource, null,{
    fillColor: 'red',
    fillOpacity: 0.7
}), 'labels');
```

Abaixo está a amostra completa e em execução do código acima.

<br/>

<iframe height='500' scrolling='no' title='Adicionar um polígono a um mapa ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>Adicionar um polígono a um mapa</a> pelo Mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="use-a-polygon-and-line-layer-together"></a>Use um polígono e uma camada de linha juntos

Uma camada de linha é usada para renderizar o contorno dos polígonos. A amostra de código a seguir renderiza um polígono como o exemplo anterior, mas agora adiciona uma camada de linha. Esta camada de linha é uma segunda camada conectada à fonte de dados.  

<iframe height='500' scrolling='no' title='Camada de polígonos e linhas para adicionar o polígono' src='//codepen.io/azuremaps/embed/aRyEPy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja o Pen <a href='https://codepen.io/azuremaps/pen/aRyEPy/'>Polígono e a camada de linha para adicionar o polígono</a> pelos Mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="fill-a-polygon-with-a-pattern"></a>Preencha um polígono com um padrão

Além de encher um polígono com uma cor, você pode usar um padrão de imagem para preencher o polígono. Carregue um padrão de imagem nos recursos de sprite `fillPattern` de imagem dos mapas e, em seguida, referenciar esta imagem com a propriedade da camada de polígono.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Padrão de preenchimento de polígono" src="//codepen.io/azuremaps/embed/JzQpYX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Veja o padrão de preenchimento pen <a href='https://codepen.io/azuremaps/pen/JzQpYX/'>polygon</a> por Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) no <a href='https://codepen.io'>CodePen</a>.
</iframe>


> [!TIP]
> O SDK web do Azure Maps fornece vários modelos de imagem personalizáveis que você pode usar como padrões de preenchimento. Para obter mais informações, consulte o documento [Como usar modelos de imagem.](how-to-use-image-templates-web-sdk.md)

## <a name="customize-a-polygon-layer"></a>Personalizar uma camada de polígono

A camada de polígono tem apenas algumas opções de estilo. Aqui está uma ferramenta para experimentá-las.

<br/>

<iframe height='700' scrolling='no' title='LXvxpg' src='//codepen.io/azuremaps/embed/LXvxpg/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja a Caneta <a href='https://codepen.io/azuremaps/pen/LXvxpg/'>LXvxpg</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="addACircle"></a>

## <a name="add-a-circle-to-the-map"></a>Adicionar um círculo ao mapa

O Azure Maps usa uma versão estendida do esquema GeoJSON que fornece uma definição para círculos, como observado [aqui](extend-geojson.md#circle). Um círculo é renderizado no `Point` mapa criando um recurso. Este `Point` tem `subType` um imóvel `"Circle"` com `radius` um valor de e um imóvel com um número que representa o raio em metros. 

```javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.126986, 47.639754]
    },
    "properties": {
        "subType": "Circle",
        "radius": 100
    }
}  
```

O Azure Maps Web SDK converte esses `Point` recursos em `Polygon` recursos. Em seguida, esses recursos são renderizados no mapa usando camadas de polígono e linha, conforme mostrado na amostra de código a seguir.

<br/>

<iframe height='500' scrolling='no' title='Adicionar um círculo a um mapa' src='//codepen.io/azuremaps/embed/PRmzJX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>Adicionar um círculo a um mapa</a> pelo Mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="make-a-geometry-easy-to-update"></a>Faça uma geometria fácil de atualizar

Uma `Shape` classe envolve uma [Geometria](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.geometry?view=azure-iot-typescript-latest) ou [Recurso](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) e facilita a atualização e a manutenção desses recursos. Para instanciar uma variável de forma, passe uma geometria ou um conjunto de propriedades para o construtor de formas.

```javascript
//Creating a shape by passing in a geometry and a object containing properties.
var shape1 = new atlas.Shape(new atlas.data.Point[0,0], { myProperty: 1 });

//Creating a shape using a feature.
var shape2 = new atlas.Shape(new atlas.data.Feature(new atlas.data.Point[0,0], { myProperty: 1 });
```

A amostra de código a seguir mostra como envolver um objeto GeoJSON de círculo com uma classe de forma. À medida que o valor do raio muda na forma, o círculo renderiza automaticamente no mapa.

<br/>

<iframe height='500' scrolling='no' title='Atualizar propriedades de forma' src='//codepen.io/azuremaps/embed/ZqMeQY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Pena <a href='https://codepen.io/azuremaps/pen/ZqMeQY/'>Atualizar propriedades de forma</a> pelos Mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Polígono](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Camada de polígono](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Opções de camadas de polígono](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)

Para obter mais exemplos de código para adicionar aos seus mapas, consulte os seguintes artigos:

> [!div class="nextstepaction"]
> [Criar uma fonte de dados](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Adicionar um pop-up](map-add-popup.md)

> [!div class="nextstepaction"]
> [Usar expressões de estilo controladas por dados](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Como usar modelos de imagem](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de linha](map-add-line-layer.md)

Recursos adicionais:

> [!div class="nextstepaction"]
> [Extensão de especificação Do Azure Maps GeoJSON](extend-geojson.md#circle)