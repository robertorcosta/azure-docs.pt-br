---
title: Adicionar uma camada de bloco para o Azure Mapas | Microsoft Docs
description: How to add a heat map layer to the Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: f7115e7c8b95efd0e3bbc8a788528878c2d1f092
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74484305"
---
# <a name="add-a-heat-map-layer"></a>Adicionar uma camada do mapa de calor

Mapas de calor, também conhecidos como ponto de mapas de densidade, são um tipo de visualização de dados usado para representar a densidade de dados usando um intervalo de cores. Geralmente são usados para mostrar os "pontos de acesso" dos dados em um mapa e são uma ótima maneira de renderizar grandes conjuntos de dados de ponto.  For example, rendering tens of thousands of points within the map view as symbols, covers most of the map area and would result in many symbols overlapping each other, making it difficult to gain much insight into the data. No entanto, visualizar esse mesmo conjunto de dados como um mapa de calor torna mais fácil ver onde os dados de ponto são o densest e a densidade relativa para outras áreas. Há muitos cenários nos quais os mapas de calor são usados. Veja alguns exemplos;

- Dados de temperatura normalmente são renderizados como mapa de calor, pois ele fornece aproximações para a temperatura entre dois pontos de dados.
- Renderização de dados de sensores de ruído, como um mapa de calor não só mostra a intensidade do ruído em que o sensor está, mas também pode fornecer ideias sobre a dissipação de uma distância. O nível de ruído em um site não pode ser alto, no entanto, se a área de cobertura de ruído de vários sensores é sobreposta, é possível que essa área de sobreposição possa enfrentar níveis mais altos de ruído e, portanto, deve estar visível no mapa de calor.
- Visualizing a GPS trace that includes the speed as a weighted height map where the intensity of each data point is based on the speed is a great way to see where the vehicle was speeding.

> [!TIP]
> Por padrão, as camadas do mapa de calor renderizarão as coordenadas de todas as geometrias em uma fonte de dados. To limit the layer so that it only renders point geometry features, set the `filter` property of the layer to `['==', ['geometry-type'], 'Point']` or `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` if you want to include MultiPoint features as well.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Heat-Maps-and-Image-Overlays-in-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="add-a-heat-map-layer"></a>Adicionar uma camada do mapa de calor

Para renderizar uma fonte de dados de pontos como mapa de calor, passe sua fonte de dados para uma instância da classe `HeatMapLayer` e adicione-a ao mapa conforme exibido aqui.

In the following code, each heat point has a radius of 10 pixels at all zoom levels. When adding the heat map layer to the map, this sample inserts it below the label layer to create a better user experience as the labels are clearly visible above the heat map. The data in this sample is sourced from the [USGS Earthquake Hazards Program](https://earthquake.usgs.gov/) and represents significant earthquakes that have occurred in the last 30 days.

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Load a data set of points, in this case earthquake data from the USGS.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson');

//Create a heatmap and add it to the map.
map.layers.add(new atlas.layer.HeatMapLayer(datasource, null, {
  radius: 10,
  opacity: 0.8
}), 'labels');
```

Below is the complete running code sample of the above functionality.

<br/>

<iframe height='500' scrolling='no' title='Camada do Mapa de calor simples' src='//codepen.io/azuremaps/embed/gQqdQB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/gQqdQB/'>Camada do Mapa de calor Simples</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customizing-the-heat-map-layer"></a>Personalizando a camada do mapa de calor

O exemplo anterior personalizou o mapa de calor, definindo as opções de raio e opacidade. A camada do mapa de calor fornece várias opções de personalização;

* `radius`: Defines a pixel radius in which to render each data point. O raio pode ser definido como um número fixo ou como uma expressão. Using an expression, it's possible to scale the radius based on the zoom level, that appears to represent a consistent spatial area on the map (for example, 5-mile radius).
* `color`: Specifies how the heat map is colorized. A color gradient is often used for heat maps and can be achieved with an `interpolate` expression. Using a `step` expression for colorizing the heat map breaks up the density visually into ranges that more so resembles a contour or radar style map. Essas paletas de cores definem as cores do valor de densidade mínimo ao máximo. Valores de cor para mapas de calor são especificados como uma expressão sobre o `heatmap-density` valor. The color at index 0 in an interpolation expression or the default color of a step expression, defines the color of the area where there's no data and can be used to define a background color. Muitos preferem definir esse valor como transparente ou semitransparente preto. Aqui estão exemplos de expressões de cores;

| Interpolation Color Expression | Stepped Color Expression | 
|--------------------------------|--------------------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'interpolate',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['linear'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['heatmap-density'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;0, 'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'purple',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.5, '#fb00fb',<br/>&nbsp;&nbsp;&nbsp;&nbsp;1, '#00c3ff'<br/>\] | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'step',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['heatmap-density'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'navy',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.25, 'green',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.50, 'yellow',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.75, 'red'<br/>\] | 

- `opacity`: Specifies how opaque or transparent the heat map layer is.
- `intensity`: Applies a multiplier to the weight of each data point to increase the overall intensity of the heatmap and helps to make the small differences in the weight of data points become easier to visualize.
- `weight`: By default, all data points have a weight of 1, thus all data points are weighted equally. A opção de peso atua como uma multiplicadora e pode ser definida como um número ou uma expressão. Se um número fosse definido como o peso, digamos 2, seria o equivalente a colocação de cada ponto de dados no mapa duas vezes, dobrando a densidade. Definir a opção de peso para um número renderiza o mapa de calor de maneira semelhante a usar a opção de intensidade. However, if an expression is used, the weight of each data point can be based on the properties of each data point. Use os dados de terremoto como exemplo. Cada ponto de dados representa um terremoto. Uma métrica importante que cada ponto de dados de terremoto tem é um valor de magnitude. Terremotos ocorrem o tempo todo, mas a maioria tem uma magnitude baixa e não são sentidos. Using the magnitude value in an expression to assign the weight to each data point will allow more significant earthquakes to be better represented within the heat map.
- Além das opções de camada base; mín./máx. de zoom, visíveis e filtrar, há também uma opção `source` se você deseja atualizar a fonte de dados e uma opção `source-layer` se a fonte de dados é uma fonte de bloco do vetor.

Aqui está uma ferramenta para testar as opções de camada do mapa de calor diferentes.

<br/>

<iframe height='700' scrolling='no' title='Opções da Camada do Mapa de Calor' src='//codepen.io/azuremaps/embed/WYPaXr/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/WYPaXr/'>Opções de Camada de Bloco</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="consistent-zoomable-heat-map"></a>Consistent zoomable heat map

By default, the radii of data points rendered in the heat map layer have a fixed pixel radius for all zoom levels. As the map is zoomed, the data aggregates together and the heat map layer looks different. A `zoom` expression can be used to scale the radius for each zoom level such that each data point covers the same physical area of the map. This will make the heat map layer look more static and consistent. Each zoom level of the map has twice as many pixels vertically and horizontally as the previous zoom level. Scaling the radius such that it doubles with each zoom level will create a heat map that looks consistent on all zoom levels. This can be accomplished by using the `zoom` with a base 2 `exponential interpolation` expression as shown in the sample below. Zoom the map to see how the heat map scales with the zoom level.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Consistent zoomable heat map" src="//codepen.io/azuremaps/embed/OGyMZr/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
See the Pen <a href='https://codepen.io/azuremaps/pen/OGyMZr/'>Consistent zoomable heat map</a> by Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Habilitar o clustering na fonte de dados, os pontos que estão próximos uns dos outros são agrupados como um ponto em cluster. A contagem de ponto de cada cluster pode ser usada como a expressão de peso para o mapa de calor e reduzir significativamente o número de pontos que precisam ser renderizados. The point count of a cluster is stored in a `point_count` property of the point feature as shown below. 
> ```JavaScript
> var layer = new atlas.layer.HeatMapLayer(datasource, null, {
>    weight: ['get', 'point_count']
> });
> ```
> Se o raio de clustering é apenas alguns pixels lá será uma diferença um pouco visual da renderização. Um raio maior agrupará mais pontos em cada cluster e melhorará o desempenho de mapa de calor, mas veja quais serão as diferenças mais notáveis.

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [HeatMapLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HeatMapLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)

Para obter mais exemplos de código para adicionar aos seus mapas, consulte os seguintes artigos:

> [!div class="nextstepaction"]
> [Create a data source](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Usar expressões de estilo controladas por dados](data-driven-style-expressions-web-sdk.md)