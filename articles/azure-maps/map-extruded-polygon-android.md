---
title: Adicionar uma camada de extrusão de polígono a um mapa do Android | Mapas do Microsoft Azure
description: Como adicionar uma camada de extrusão de polígono ao SDK do Android de mapas de Microsoft Azure.
author: rbrundritt
ms.author: richbrun
ms.date: 02/19/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: b62c2540dc8cf2c7d4f67d465b2d464cbc0c3091
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102054466"
---
# <a name="add-a-polygon-extrusion-layer-to-the-map-android-sdk"></a>Adicionar uma camada de extrusão de polígono ao mapa (SDK do Android)

Este artigo mostra como usar a camada de extrusão de polígono para renderizar áreas `Polygon` de `MultiPolygon` geometrias de recursos como formas extrudadas.

## <a name="use-a-polygon-extrusion-layer"></a>Usar uma camada de extrusão de polígono

Conecte a camada de extrusão do polígono a uma fonte de dados. Em seguida, o carregou no mapa. A camada de extrusão do polígono renderiza as áreas de a `Polygon` e os `MultiPolygon` recursos como formas de extrusão. As `height` `base` Propriedades e da camada de extrusão do polígono definem a distância base do chão e da altura da forma extrudada em **metros**. O código a seguir mostra como criar um polígono, adicioná-lo a uma fonte de dados e renderizá-lo usando a classe da camada de extrusão do polígono.

> [!Note]
> O `base` valor definido na camada de extrusão do polígono deve ser menor ou igual ao do `height` .

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a polygon.
source.add(Polygon.fromLngLats(
    Arrays.asList(
        Arrays.asList(
            Point.fromLngLat(-73.95838379859924, 40.80027995478159),
            Point.fromLngLat(-73.98154735565186, 40.76845986171129),
            Point.fromLngLat(-73.98124694824219, 40.767761062136955),
            Point.fromLngLat(-73.97361874580382, 40.76461637311633),
            Point.fromLngLat(-73.97306084632874, 40.76512830937617),
            Point.fromLngLat(-73.97259950637817, 40.76490890860481),
            Point.fromLngLat(-73.9494466781616, 40.79658450499243),
            Point.fromLngLat(-73.94966125488281, 40.79708807289436),
            Point.fromLngLat(-73.95781517028809, 40.80052360358227),
            Point.fromLngLat(-73.95838379859924, 40.80027995478159)
        )
    )
));

//Create and add a polygon extrusion layer to the map below the labels so that they are still readable.
PolygonExtrusionLayer layer = new PolygonExtrusionLayer(source,
    fillColor("#fc0303"),
    fillOpacity(0.7f),
    height(500f)
);

//Create and add a polygon extrusion layer to the map below the labels so that they are still readable.
map.layers.add(layer, "labels");
```

A captura de tela a seguir mostra o código acima renderização um polígono alongado verticalmente usando uma camada de extrusão de polígono.

![Mapa com polígono alongado verticalmente usando uma camada de extrusão de polígono](media/map-extruded-polygon-android/polygon-extrusion-layer.jpg)

## <a name="add-data-driven-polygons"></a>Adicionar polígonos controlados por dados

Um mapa coropléticos pode ser renderizado usando a camada de extrusão do polígono. Defina as `height` `fillColor` Propriedades e da camada de extrusão com a medida da variável estatística nas `Polygon` `MultiPolygon` geometrias de recurso. O exemplo de código a seguir mostra um mapa coropléticos extrudada do Estados Unidos com base na medida da densidade da população por Estado.

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Import the geojson data and add it to the data source.
Utils.importData("https://azuremapscodesamples.azurewebsites.net/Common/data/geojson/US_States_Population_Density.json", this,  (String result) -> {
    //Parse the data as a GeoJSON Feature Collection.
    FeatureCollection fc = FeatureCollection.fromJson(result);

    //Add the feature collection to the data source.
    source.add(fc);
});

//Create and add a polygon extrusion layer to the map below the labels so that they are still readable.
PolygonExtrusionLayer layer = new PolygonExtrusionLayer(source,
    fillOpacity(0.7f),
    fillColor(
        step(
            get("density"),
            literal("rgba(0, 255, 128, 1)"),
            stop(10, "rgba(9, 224, 118, 1)"),
            stop(20, "rgba(11, 191, 103, 1)"),
            stop(50, "rgba(247, 227, 5, 1)"),
            stop(100, "rgba(247, 199, 7, 1)"),
            stop(200, "rgba(247, 130, 5, 1)"),
            stop(500, "rgba(247, 94, 5, 1)"),
            stop(1000, "rgba(247, 37, 5, 1)")
        )
    ),
    height(
        interpolate(
            linear(),
            get("density"),
            stop(0, 100),
            stop(1200, 960000)
        )
    )
);

//Create and add a polygon extrusion layer to the map below the labels so that they are still readable.
map.layers.add(layer, "labels");
```

A captura de tela a seguir mostra um mapa coropléticos de Estados dos EUA colorido e alongado verticalmente como polígonos extrudada com base na densidade da população.

![Um mapa coropléticos de Estados dos EUA é colorido e alongado verticalmente como polígonos extrudada com base na densidade da população](media/map-extruded-polygon-android/android-extruded-choropleth.jpg)

## <a name="next-steps"></a>Próximas etapas

Consulte os artigos a seguir para obter mais exemplos de código para adicionar aos seus mapas:

> [!div class="nextstepaction"]
> [Criar uma fonte de dados](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Usar expressões de estilo controladas por dados](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de polígono](how-to-add-shapes-to-android-map.md)
