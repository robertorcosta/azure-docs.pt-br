---
title: Criar uma fonte de dados para um mapa | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá como criar uma fonte de dados e adicioná-la a um mapa usando o Microsoft Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen
ms.openlocfilehash: 1675d63fd3a65beda46042f4a78535bb4e066e62
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190224"
---
# <a name="create-a-data-source"></a>Criar uma fonte de dados

O Azure Maps Web SDK armazena dados em fontes de dados. O uso de fontes de dados otimiza as operações de dados para consulta e renderização. Atualmente existem dois tipos de fontes de dados:

**Fonte de dados GeoJSON**

Uma fonte de dados baseada em GeoJSON `DataSource` carrega e armazena dados localmente usando a classe. Os dados geoJSON podem ser criados manualmente ou criados usando as classes de ajudante sustais no espaço de nome [atlas.data.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data) A `DataSource` classe fornece funções para importar arquivos GeoJSON locais ou remotos. Os arquivos GeoJSON remotos devem ser hospedados em um ponto final habilitado para CORs. A `DataSource` classe fornece funcionalidade para dados de ponto de clustering. E, os dados podem ser facilmente adicionados, removidos e atualizados com a `DataSource` classe.


> [!TIP]
> Digamos que você queira substituir todos `DataSource`os dados em um . Se você fizer `clear` chamadas `add` para as funções em seguida, o mapa pode rerenderizar duas vezes, o que pode causar um pouco de atraso. Em vez `setShapes` disso, use a função, que removerá e substituirá todos os dados na fonte de dados e somente acionará uma única rerenderização do mapa.

**Fonte de telha vetorial**

Uma fonte de telha vetorial descreve como acessar uma camada de telha vetorial. Use a classe [VectorTileSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.vectortilesource) para instanciar uma fonte de telha vetorial. As camadas de ladrilhos vetoriais são semelhantes às camadas de ladrilhos, mas não são as mesmas. Uma camada de azulejo é uma imagem rasterizada. As camadas de ladrilhos vetoriais são um arquivo compactado, no formato PBF. Este arquivo compactado contém dados do mapa vetorial e uma ou mais camadas. O arquivo pode ser renderizado e estilizado no cliente, com base no estilo de cada camada. Os dados em uma telha vetorial contêm características geográficas na forma de pontos, linhas e polígonos. Existem várias vantagens de usar camadas de ladrilhos vetoriais em vez de camadas de ladrilhos raster:

 - Um tamanho de arquivo de uma telha vetorial é tipicamente muito menor do que uma ladrilho rasterequivalente. Como tal, menos largura de banda é usada. Isso significa menor latência, um mapa mais rápido e uma melhor experiência do usuário.
 - Uma vez que as telhas vetoriais são renderizadas no cliente, elas se adaptam à resolução do dispositivo em que estão sendo exibidas. Como resultado, os mapas renderizados parecem mais bem definidos, com rótulos cristalinos.
 - Alterar o estilo dos dados nos mapas vetoriais não requer baixar os dados novamente, uma vez que o novo estilo pode ser aplicado no cliente. Em contraste, alterar o estilo de uma camada de ladrilho rasternormal normalmente requer carregar telhas do servidor e, em seguida, aplicar o novo estilo.
 - Uma vez que os dados são entregues em forma de vetor, há menos processamento do lado do servidor necessário para preparar os dados. Como resultado, os dados mais novos podem ser disponibilizados mais rapidamente.

Todas as camadas que usam `sourceLayer` uma fonte vetorial devem especificar um valor.

Uma vez criadas, as fontes de `map.sources` dados podem ser adicionadas ao mapa através da propriedade, que é um [SourceManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.sourcemanager). O código a seguir `DataSource` mostra como criar um e adicioná-lo ao mapa.

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);
```

O Azure Maps adere à [Especificação de Ladrilhos Vetoriais mapbox](https://github.com/mapbox/vector-tile-spec), um padrão aberto.

## <a name="connecting-a-data-source-to-a-layer"></a>Conectando uma fonte de dados a uma camada

Os dados são renderizados no mapa usando camadas de renderização. Uma única fonte de dados pode ser referenciada por uma ou mais camadas de renderização. As seguintes camadas de renderização requerem uma fonte de dados:

- [Camada de bolha](map-add-bubble-layer.md) - renderiza dados de pontos como círculos dimensionados no mapa.
- [Camada de símbolo](map-add-pin.md) - renderiza dados de pontos como ícones ou texto.
- [Camada de mapa de](map-add-heat-map-layer.md) calor - renderiza dados de ponto como um mapa de calor de densidade.
- [Camada de linha](map-add-shape.md) - renderizar uma linha e ou renderizar o contorno de polígonos. 
- [Camada de polígono](map-add-shape.md) - preenche a área de um polígono com uma cor sólida ou padrão de imagem.

O código a seguir mostra como criar uma fonte de dados, adicioná-la ao mapa e conectá-la a uma camada de bolha. E, em seguida, importar dados de ponto GeoJSON de um local remoto para a fonte de dados. 

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a layer that defines how to render points in the data source and add it to the map.
map.layers.add(new atlas.layer.BubbleLayer(datasource));

//Load the earthquake data.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/significant_month.geojson');
```

Existem camadas de renderização adicionais que não se conectam a essas fontes de dados, mas carregam diretamente os dados para renderização. 

- [Camada de imagem](map-add-image-layer.md) - sobrepõe uma única imagem no topo do mapa e liga seus cantos a um conjunto de coordenadas especificadas.
- [Camada de ladrilho](map-add-tile-layer.md) - sobrepõe uma camada de ladrilho rasterno no topo do mapa.

## <a name="one-data-source-with-multiple-layers"></a>Uma fonte de dados com várias camadas

Várias camadas podem ser conectadas a uma única fonte de dados. Existem muitos cenários diferentes em que essa opção é útil. Por exemplo, considere o cenário em que um usuário desenha um polígono. Devemos renderizar e preencher a área do polígono à medida que o usuário adiciona pontos ao mapa. Adicionar uma linha estilizada para delinear o polígono facilita a ver as bordas do polígono, à medida que o usuário desenha. Para editar convenientemente uma posição individual no polígono, podemos adicionar uma alça, como um pino ou um marcador, acima de cada posição.

![Mapa mostrando várias camadas de renderização de dados de uma única fonte de dados](media/create-data-source-web-sdk/multiple-layers-one-datasource.png)

Na maioria das plataformas de mapeamento, você precisaria de um objeto polígono, um objeto de linha e um pino para cada posição no polígono. Como o polígono é modificado, você precisaria atualizar manualmente a linha e os pinos, que podem rapidamente se tornar complexos.

Com o Azure Maps, tudo o que você precisa é de um único polígono em uma fonte de dados, como mostrado no código abaixo.

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a polygon and add it to the data source.
dataSource.add(new atlas.data.Polygon([[[/* Coordinates for polygon */]]]));

//Create a polygon layer to render the filled in area of the polygon.
var polygonLayer = new atlas.layer.PolygonLayer(dataSource, 'myPolygonLayer', {
     fillColor: 'rgba(255,165,0,0.2)'
});

//Create a line layer for greater control of rendering the outline of the polygon.
var lineLayer = new atlas.layer.LineLayer(dataSource, 'myLineLayer', {
     color: 'orange',
     width: 2
});

//Create a bubble layer to render the vertices of the polygon as scaled circles.
var bubbleLayer = new atlas.layer.BubbleLayer(dataSource, 'myBubbleLayer', {
     color: 'orange',
     radius: 5,
     outlineColor: 'white',
     outlineWidth: 2
});

//Add all layers to the map.
map.layers.add([polygonLayer, lineLayer, bubbleLayer]);
```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Datasource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [Opções de origem de dados](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [Fonte de vetorial](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.vectortilesource?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [Opções de origem do VectorTile](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.vectortilesourceoptions?view=azure-maps-typescript-latest)

Consulte os artigos a seguir para obter mais exemplos de código para adicionar aos seus mapas:

> [!div class="nextstepaction"]
> [Adicionar um pop-up](map-add-popup.md)

> [!div class="nextstepaction"]
> [Usar expressões de estilo controladas por dados](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Adicione uma camada de símbolo](map-add-pin.md)

> [!div class="nextstepaction"]
> [Adicione uma camada de bolha](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de linha](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de polígono](map-add-shape.md)

> [!div class="nextstepaction"]
> [Adicionar um mapa de calor](map-add-heat-map-layer.md)

> [!div class="nextstepaction"]
> [Exemplos de código](https://docs.microsoft.com/samples/browse/?products=azure-maps)