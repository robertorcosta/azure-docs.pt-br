---
title: Adicione uma camada de mapa de calor a um mapa | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá sobre como adicionar uma camada de mapa de calor a um mapa usando o Microsoft Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 19765bd28f365cc6f6d5b06646896613dd3e3e87
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804547"
---
# <a name="add-a-heat-map-layer"></a>Adicionar uma camada do mapa de calor

Mapas de calor, também conhecidos como mapas de densidade de ponto, são um tipo de visualização de dados. Eles são usados para representar a densidade de dados usando uma gama de cores e mostrar os dados "pontos quentes" em um mapa. Mapas de calor são uma ótima maneira de renderizar conjuntos de dados com grande número de pontos. 

Renderizar dezenas de milhares de pontos como símbolos pode cobrir a maior parte da área do mapa. Este caso provavelmente resulta em muitos símbolos sobrepostos. Dificultando a compreensão dos dados. No entanto, visualizar este mesmo conjunto de dados como um mapa de calor torna mais fácil ver a densidade e a densidade relativa de cada ponto de dados.

Você pode usar mapas de calor em muitos cenários diferentes, incluindo:

- **Dados de temperatura**: Fornece aproximações para o que a temperatura está entre dois pontos de dados.
- **Dados para sensores de ruído**: Mostra não apenas a intensidade do ruído onde o sensor está, mas também pode fornecer uma visão da dissipação à distância. O nível de ruído em qualquer local pode não ser alto. Se a área de cobertura de ruído de vários sensores se sobrepõe, é possível que essa área sobreposta possa experimentar níveis de ruído mais altos. Como tal, a área sobreposta seria visível no mapa de calor.
- **Rastreamento gps**: Inclui a velocidade como um mapa de altura ponderado, onde a intensidade de cada ponto de dados é baseada na velocidade. Por exemplo, essa funcionalidade fornece uma maneira de ver onde um veículo estava em alta velocidade.

> [!TIP]
> Camadas de mapa de calor por padrão tornam as coordenadas de todas as geometrias em uma fonte de dados. Para limitar a camada para que ela só produza `filter` recursos de `['==', ['geometry-type'], 'Point']`geometria de ponto, defina a propriedade da camada como . Se você quiser incluir os recursos do `filter` MultiPoint também, defina a propriedade da camada como `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]`.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Heat-Maps-and-Image-Overlays-in-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="add-a-heat-map-layer"></a>Adicionar uma camada do mapa de calor

Para renderizar uma fonte de dados de pontos como um `HeatMapLayer` mapa de calor, passe sua fonte de dados em uma instância da classe e adicione-a ao mapa.

No código a seguir, cada ponto de calor tem um raio de 10 pixels em todos os níveis de zoom. Para garantir uma melhor experiência do usuário, o mapa de calor está abaixo da camada de rótulo. Os rótulos permanecem claramente visíveis. Os dados desta amostra são do [UsGS Earthquake Hazards Program](https://earthquake.usgs.gov/). É para terremotos significativos que ocorreram nos últimos 30 dias.

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Load a dataset of points, in this case earthquake data from the USGS.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson');

//Create a heat map and add it to the map.
map.layers.add(new atlas.layer.HeatMapLayer(datasource, null, {
  radius: 10,
  opacity: 0.8
}), 'labels');
```

Aqui está a amostra completa do código de execução do código anterior.

<br/>

<iframe height='500' scrolling='no' title='Camada do Mapa de calor simples' src='//codepen.io/azuremaps/embed/gQqdQB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/gQqdQB/'>Camada do Mapa de calor Simples</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-the-heat-map-layer"></a>Personalize a camada do mapa de calor

O exemplo anterior personalizou o mapa de calor, definindo as opções de raio e opacidade. A camada do mapa de calor oferece várias opções de personalização, incluindo:

* `radius`: Define um raio de pixel sustais para renderizar cada ponto de dados. Você pode definir o raio como um número fixo ou como uma expressão. Usando uma expressão, você pode dimensionar o raio com base no nível de zoom e representar uma área espacial consistente no mapa (por exemplo, um raio de 5 milhas).
* `color`: Especifica como o mapa de calor é colorido. Um gradiente de cores é uma característica comum de mapas de calor. Você pode alcançar o `interpolate` efeito com uma expressão. Você também pode `step` usar uma expressão para colorir o mapa de calor, quebrando a densidade visualmente em faixas que se assemelham a um contorno ou mapa de estilo de radar. Essas paletas de cores definem as cores do valor de densidade mínimo ao máximo. 

  Você especifica valores de cor para `heatmap-density` mapas de calor como uma expressão no valor. A cor da área onde não há dados é definida no índice 0 da expressão "Interpolação", ou na cor padrão de uma expressão "Pisada". Você pode usar esse valor para definir uma cor de fundo. Muitas vezes, esse valor é definido como transparente, ou um preto semitransparente. 
   
  Aqui estão exemplos de expressões coloridas:

  | Expressão de cor de interpolação | Expressão de cor escaloda | 
  |--------------------------------|--------------------------|
  | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'interpolate',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['linear'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['heatmap-density'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;0, 'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'purple',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.5, '#fb00fb',<br/>&nbsp;&nbsp;&nbsp;&nbsp;1, '#00c3ff'<br/>\] | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'step',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['heatmap-density'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'navy',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.25, 'verde',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,50, 'amarelo',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,75, 'vermelho'<br/>\] |   

- `opacity`: Especifica o quão opaca ou transparente é a camada do mapa de calor.
- `intensity`: Aplica um multiplicador ao peso de cada ponto de dados para aumentar a intensidade geral do mapa de calor. Isso causa uma diferença no peso dos pontos de dados, facilitando a visualização.
- `weight`: Por padrão, todos os pontos de dados têm um peso de 1, e são ponderados igualmente. A opção de peso atua como um multiplicador, e você pode defini-lo como um número ou uma expressão. Se um número é definido como o peso, é a equivalência de colocar cada ponto de dados no mapa duas vezes. Por exemplo, se o peso é 2, então a densidade dobra. Definir a opção de peso para um número renderiza o mapa de calor de maneira semelhante a usar a opção de intensidade. 

  No entanto, se você usar uma expressão, o peso de cada ponto de dados pode ser baseado nas propriedades de cada ponto de dados. Por exemplo, suponha que cada ponto de dados represente um terremoto. O valor da magnitude tem sido uma métrica importante para cada ponto de dados de terremoto. Terremotos acontecem o tempo todo, mas a maioria tem uma magnitude baixa, e não são notados. Use o valor de magnitude em uma expressão para atribuir o peso a cada ponto de dados. Usando o valor de magnitude para atribuir o peso, você tem uma melhor representação do significado dos terremotos dentro do mapa de calor.
- `source`e `source-layer`: Permitir que você atualize a fonte de dados.

Aqui está uma ferramenta para testar as diferentes opções de camada de mapa de calor.

<br/>

<iframe height='700' scrolling='no' title='Opções da Camada do Mapa de Calor' src='//codepen.io/azuremaps/embed/WYPaXr/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/WYPaXr/'>Opções de Camada de Bloco</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="consistent-zoomable-heat-map"></a>Mapa de calor zoom consistente

Por padrão, os raios de pontos de dados renderizados na camada do mapa de calor têm um raio de pixel fixo para todos os níveis de zoom. À medida que você amplia o mapa, os dados se agregam juntos e a camada do mapa de calor parece diferente. 

Use `zoom` uma expressão para dimensionar o raio para cada nível de zoom, de modo que cada ponto de dados cubra a mesma área física do mapa. Esta expressão faz com que a camada do mapa de calor pareça mais estática e consistente. Cada nível de zoom do mapa tem o dobro de pixels vertical e horizontalmente do que o nível de zoom anterior. 

Dimensionar o raio para que ele dobre a cada nível de zoom cria um mapa de calor que parece consistente em todos os níveis de zoom. Para aplicar este `zoom` dimensionamento, `exponential interpolation` use com uma expressão base 2, com o raio de pixel definido `2 * Math.pow(2, minZoom - maxZoom)` para o nível mínimo de zoom e um raio dimensionado para o nível máximo de zoom calculado conforme mostrado na amostra a seguir. Amplie o mapa para ver como o mapa de calor se dimensiona com o nível de zoom.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Mapa de calor zoom consistente" src="//codepen.io/azuremaps/embed/OGyMZr/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Veja o <a href='https://codepen.io/azuremaps/pen/OGyMZr/'>mapa de calor com zoom</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>consistente da Caneta pelo Azure Maps ( ) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Quando você habilita o agrupamento na fonte de dados, os pontos próximos um do outro são agrupados como um ponto agrupado. Você pode usar a contagem de pontos de cada cluster como expressão de peso para o mapa de calor. Isso pode reduzir significativamente o número de pontos a serem prestados. A contagem de pontos de `point_count` um cluster é armazenada em uma propriedade do recurso de ponto: 
> ```JavaScript
> var layer = new atlas.layer.HeatMapLayer(datasource, null, {
>    weight: ['get', 'point_count']
> });
> ```
> Se o raio de agrupamento for de apenas alguns pixels, haveria uma pequena diferença visual na renderização. Um raio maior agrupa mais pontos em cada cluster, e melhora o desempenho do mapa de calor.

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
