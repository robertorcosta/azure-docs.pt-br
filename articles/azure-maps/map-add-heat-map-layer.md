---
title: Adicionar uma camada de bloco para o Azure Mapas | Microsoft Docs
description: Como adicionar uma camada do mapa de calor ao SDK da Web do Azure Maps.
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

Mapas de calor, também conhecidos como ponto de mapas de densidade, são um tipo de visualização de dados usado para representar a densidade de dados usando um intervalo de cores. Geralmente são usados para mostrar os "pontos de acesso" dos dados em um mapa e são uma ótima maneira de renderizar grandes conjuntos de dados de ponto.  Por exemplo, renderizar dezenas de milhares de pontos dentro da exibição do mapa como símbolos, abrange a maior parte da área do mapa e resultaria em muitos símbolos sobrepostos, dificultando muito a percepção dos dados. No entanto, visualizar esse mesmo conjunto de dados como um mapa de calor torna mais fácil ver onde os dados de ponto são o densest e a densidade relativa para outras áreas. Há muitos cenários nos quais os mapas de calor são usados. Veja alguns exemplos;

- Dados de temperatura normalmente são renderizados como mapa de calor, pois ele fornece aproximações para a temperatura entre dois pontos de dados.
- Renderização de dados de sensores de ruído, como um mapa de calor não só mostra a intensidade do ruído em que o sensor está, mas também pode fornecer ideias sobre a dissipação de uma distância. O nível de ruído em um site não pode ser alto, no entanto, se a área de cobertura de ruído de vários sensores é sobreposta, é possível que essa área de sobreposição possa enfrentar níveis mais altos de ruído e, portanto, deve estar visível no mapa de calor.
- Visualizar um rastreamento de GPS que inclui a velocidade como um mapa de altura ponderado em que a intensidade de cada ponto de dados é baseada na velocidade é uma ótima maneira de ver onde o veículo estava acelerando.

> [!TIP]
> Por padrão, as camadas do mapa de calor renderizarão as coordenadas de todas as geometrias em uma fonte de dados. Para limitar a camada para que ela só processe recursos de geometria de ponto, defina a propriedade `filter` da camada como `['==', ['geometry-type'], 'Point']` ou `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` se você quiser incluir recursos do MultiPoint também.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Heat-Maps-and-Image-Overlays-in-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="add-a-heat-map-layer"></a>Adicionar uma camada do mapa de calor

Para renderizar uma fonte de dados de pontos como mapa de calor, passe sua fonte de dados para uma instância da classe `HeatMapLayer` e adicione-a ao mapa conforme exibido aqui.

No código a seguir, cada ponto de calor tem um raio de 10 pixels em todos os níveis de zoom. Ao adicionar a camada do mapa de calor ao mapa, este exemplo o insere abaixo da camada de rótulo para criar uma melhor experiência do usuário, pois os rótulos são claramente visíveis acima do mapa de calor. Os dados neste exemplo são originados do programa de [riscos de terremoto USGS](https://earthquake.usgs.gov/) e representam terremotos significativos que ocorreram nos últimos 30 dias.

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

Abaixo está o exemplo de código completo em execução da funcionalidade acima.

<br/>

<iframe height='500' scrolling='no' title='Camada do Mapa de calor simples' src='//codepen.io/azuremaps/embed/gQqdQB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/gQqdQB/'>Camada do Mapa de calor Simples</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customizing-the-heat-map-layer"></a>Personalizando a camada do mapa de calor

O exemplo anterior personalizou o mapa de calor, definindo as opções de raio e opacidade. A camada do mapa de calor fornece várias opções de personalização;

* `radius`: define um raio de pixel no qual processar cada ponto de dados. O raio pode ser definido como um número fixo ou como uma expressão. Usando uma expressão, é possível dimensionar o raio com base no nível de zoom, que parece representar uma área espacial consistente no mapa (por exemplo, raio de 5 km).
* `color`: especifica como o mapa de calor é colorido. Um gradiente de cor geralmente é usado para mapas de calor e pode ser obtido com uma expressão `interpolate`. O uso de uma `step` expressão para colorir o mapa de calor divide a densidade visualmente em intervalos que mais se assemelham a uma delimitação ou ao mapa de estilo de radar. Essas paletas de cores definem as cores do valor de densidade mínimo ao máximo. Valores de cor para mapas de calor são especificados como uma expressão sobre o `heatmap-density` valor. A cor no índice 0 em uma expressão de interpolação ou a cor padrão de uma expressão de etapa define a cor da área em que não há dados e pode ser usada para definir uma cor de plano de fundo. Muitos preferem definir esse valor como transparente ou semitransparente preto. Aqui estão exemplos de expressões de cores;

| Expressão de cor de interpolação | Stepped Color Expression | 
|--------------------------------|--------------------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'interpolate',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['linear'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['heatmap-density'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;0, 'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'purple',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.5, '#fb00fb',<br/>&nbsp;&nbsp;&nbsp;&nbsp;1, '#00c3ff'<br/>\] | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'step',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['heatmap-density'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'navy',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,25, ' verde ',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,50, ' amarelo ',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,75, ' vermelho '<br/>\] | 

- `opacity`: especifica o quão opaco ou transparente a camada do mapa de calor é.
- `intensity`: aplica um multiplicador ao peso de cada ponto de dados para aumentar a intensidade geral do calor e ajuda a fazer as pequenas diferenças no peso dos pontos de dados se tornarem mais fáceis de Visualizar.
- `weight`: por padrão, todos os pontos de dados têm um peso de 1, portanto, todos os pontos de dados são ponderados igualmente. A opção de peso atua como uma multiplicadora e pode ser definida como um número ou uma expressão. Se um número fosse definido como o peso, digamos 2, seria o equivalente a colocação de cada ponto de dados no mapa duas vezes, dobrando a densidade. Definir a opção de peso para um número renderiza o mapa de calor de maneira semelhante a usar a opção de intensidade. No entanto, se uma expressão for usada, o peso de cada ponto de dados poderá ser baseado nas propriedades de cada ponto de dados. Use os dados de terremoto como exemplo. Cada ponto de dados representa um terremoto. Uma métrica importante que cada ponto de dados de terremoto tem é um valor de magnitude. Terremotos ocorrem o tempo todo, mas a maioria tem uma magnitude baixa e não são sentidos. Usar o valor de magnitude em uma expressão para atribuir o peso a cada ponto de dados permitirá que os terremotos mais significativos sejam mais bem representados no mapa de calor.
- Além das opções de camada base; mín./máx. de zoom, visíveis e filtrar, há também uma opção `source` se você deseja atualizar a fonte de dados e uma opção `source-layer` se a fonte de dados é uma fonte de bloco do vetor.

Aqui está uma ferramenta para testar as opções de camada do mapa de calor diferentes.

<br/>

<iframe height='700' scrolling='no' title='Opções da Camada do Mapa de Calor' src='//codepen.io/azuremaps/embed/WYPaXr/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/WYPaXr/'>Opções de Camada de Bloco</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="consistent-zoomable-heat-map"></a>Mapa de calor com zoom consistente

Por padrão, o raios de pontos de dados renderizados na camada do mapa de calor têm um raio de pixel fixo para todos os níveis de zoom. À medida que o mapa é ampliado, os dados são agregados juntos e a camada do mapa de calor é diferente. Uma expressão `zoom` pode ser usada para dimensionar o raio para cada nível de zoom, de modo que cada ponto de dados cubra a mesma área física do mapa. Isso fará com que a camada do mapa de calor pareça mais estática e consistente. Cada nível de zoom do mapa tem duas vezes mais pixels vertical e horizontalmente como o nível de zoom anterior. Dimensionar o raio de forma a dobrar com cada nível de zoom criará um mapa de calor que se parece consistente em todos os níveis de zoom. Isso pode ser feito usando o `zoom` com uma expressão de `exponential interpolation` base 2, conforme mostrado no exemplo abaixo. Aplique zoom no mapa para ver como o mapa de calor é dimensionado com o nível de zoom.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Mapa de calor com zoom consistente" src="//codepen.io/azuremaps/embed/OGyMZr/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte o <a href='https://codepen.io/azuremaps/pen/OGyMZr/'>mapa de calor com zoom consistente</a> com a caneta pelo mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Habilitar o clustering na fonte de dados, os pontos que estão próximos uns dos outros são agrupados como um ponto em cluster. A contagem de ponto de cada cluster pode ser usada como a expressão de peso para o mapa de calor e reduzir significativamente o número de pontos que precisam ser renderizados. A contagem de pontos de um cluster é armazenada em uma propriedade `point_count` do recurso de ponto, conforme mostrado abaixo. 
> ```JavaScript
> var layer = new atlas.layer.HeatMapLayer(datasource, null, {
>    weight: ['get', 'point_count']
> });
> ```
> Se o raio de clustering é apenas alguns pixels lá será uma diferença um pouco visual da renderização. Um raio maior agrupará mais pontos em cada cluster e melhorará o desempenho de mapa de calor, mas veja quais serão as diferenças mais notáveis.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [HeatMapLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HeatMapLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)

Para obter mais exemplos de código para adicionar aos seus mapas, consulte os seguintes artigos:

> [!div class="nextstepaction"]
> [Criar uma fonte de dados](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Usar expressões de estilo controladas por dados](data-driven-style-expressions-web-sdk.md)