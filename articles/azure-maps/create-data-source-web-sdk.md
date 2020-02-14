---
title: Criar uma fonte de dados para um mapa | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá a criar uma fonte de dados e a adicioná-la a um mapa usando o SDK da Web do Microsoft Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen
ms.openlocfilehash: 1675d63fd3a65beda46042f4a78535bb4e066e62
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190224"
---
# <a name="create-a-data-source"></a>Criar uma fonte de dados

O SDK da Web do Azure Maps armazena dados em fontes de dados. O uso de fontes de dados otimiza as operações de dados para consulta e renderização. Atualmente, há dois tipos de fontes de dados:

**Fonte de dados geojson**

Uma fonte de dados baseada em geojson carrega e armazena dados localmente usando a classe `DataSource`. Os dados geojson podem ser criados ou criados manualmente usando as classes auxiliares no namespace do [Atlas. Data](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data) . A classe `DataSource` fornece funções para importar arquivos geojson locais ou remotos. Arquivos geojson remotos devem ser hospedados em um ponto de extremidade habilitado para CORs. A classe `DataSource` fornece funcionalidade para dados de ponto de cluster. E, os dados podem ser facilmente adicionados, removidos e atualizados com a classe `DataSource`.


> [!TIP]
> Digamos que você deseja substituir todos os dados em um `DataSource`. Se você fizer chamadas para a `clear` `add` funções, o mapa poderá renderizar novamente duas vezes, o que pode causar um pouco de atraso. Em vez disso, use a função `setShapes`, que removerá e substituirá todos os dados na fonte de dados e disparará apenas uma única rerenderização do mapa.

**Origem do bloco de vetor**

Uma fonte de bloco de vetor descreve como acessar uma camada de bloco de vetor. Use a classe [VectorTileSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.vectortilesource) para criar uma instância de uma fonte de bloco de vetor. As camadas de bloco de vetor são semelhantes às camadas de bloco, mas não são as mesmas. Uma camada de peça é uma imagem rasterizada. As camadas de bloco de vetor são um arquivo compactado, no formato PBF. Esse arquivo compactado contém dados de mapa de vetor e uma ou mais camadas. O arquivo pode ser renderizado e estilizado no cliente, com base no estilo de cada camada. Os dados em um bloco de vetor contêm recursos geográficos na forma de pontos, linhas e polígonos. Há várias vantagens de usar camadas de bloco vetorial em vez de camadas de bloco rasterizadas:

 - Um tamanho de arquivo de um bloco de vetor normalmente é muito menor do que um bloco rasterizado equivalente. Dessa forma, menos largura de banda é usada. Isso significa menor latência, um mapa mais rápido e uma melhor experiência do usuário.
 - Como os blocos de vetor são renderizados no cliente, eles se adaptam à resolução do dispositivo em que estão sendo exibidos. Como resultado, os mapas renderizados aparecem mais bem definidos, com rótulos de cristal claro.
 - Alterar o estilo dos dados nos mapas de vetor não exige o download dos dados novamente, já que o novo estilo pode ser aplicado ao cliente. Por outro lado, alterar o estilo de uma camada de bloco de varredura normalmente requer o carregamento de blocos do servidor, aplicando o novo estilo.
 - Como os dados são entregues na forma de vetor, há menos processamento no lado do servidor necessário para preparar os dados. Como resultado, os dados mais recentes podem ser disponibilizados mais rapidamente.

Todas as camadas que usam uma fonte de vetor devem especificar um valor de `sourceLayer`.

Depois de criadas, as fontes de dados podem ser adicionadas ao mapa por meio da propriedade `map.sources`, que é um [sourcemanager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.sourcemanager). O código a seguir mostra como criar um `DataSource` e adicioná-lo ao mapa.

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);
```

O mapas do Azure segue a [especificação de bloco de vetor Mapbox](https://github.com/mapbox/vector-tile-spec), um padrão aberto.

## <a name="connecting-a-data-source-to-a-layer"></a>Conectando uma fonte de dados a uma camada

Os dados são renderizados no mapa usando camadas de renderização. Uma única fonte de dados pode ser referenciada por uma ou mais camadas de renderização. As seguintes camadas de renderização exigem uma fonte de dados:

- [Camada de bolha](map-add-bubble-layer.md) – renderiza dados de ponto como círculos em escala no mapa.
- [Camada de símbolo](map-add-pin.md) – renderiza dados de ponto como ícones ou texto.
- [Camada do mapa de calor](map-add-heat-map-layer.md) – renderiza dados de ponto como um mapa de calor de densidade.
- [Camada de linha](map-add-shape.md) – renderizar uma linha e ou renderizar a estrutura de polígonos. 
- [Camada de polígono](map-add-shape.md) – preenche a área de um polígono com uma cor sólida ou padrão de imagem.

O código a seguir mostra como criar uma fonte de dados, adicioná-la ao mapa e conectá-la a uma camada de bolha. Em seguida, importe dados do ponto geojson de um local remoto para a fonte de dados. 

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a layer that defines how to render points in the data source and add it to the map.
map.layers.add(new atlas.layer.BubbleLayer(datasource));

//Load the earthquake data.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/significant_month.geojson');
```

Há camadas de renderização adicionais que não se conectam a essas fontes de dados, mas carregam diretamente os dados para renderização. 

- [Camada de imagem](map-add-image-layer.md) – sobrepõe uma única imagem na parte superior do mapa e associa seus cantos a um conjunto de coordenadas especificadas.
- [Camada de bloco](map-add-tile-layer.md) – sobrepõe uma camada de bloco de rasterização na parte superior do mapa.

## <a name="one-data-source-with-multiple-layers"></a>Uma fonte de dados com várias camadas

Várias camadas podem ser conectadas a uma única fonte de dados. Há vários cenários diferentes nos quais essa opção é útil. Por exemplo, considere o cenário no qual um usuário desenha um polígono. Devemos renderizar e preencher a área do polígono, pois o usuário adiciona pontos ao mapa. A adição de uma linha com estilo para descrever o polígono facilita a visualização das bordas do polígono, conforme o usuário desenha. Para editar convenientemente uma posição individual no polígono, podemos adicionar um identificador, como um PIN ou um marcador, acima de cada posição.

![Mapa mostrando várias camadas Renderizando dados de uma única fonte de dados](media/create-data-source-web-sdk/multiple-layers-one-datasource.png)

Na maioria das plataformas de mapeamento, você precisaria de um objeto Polygon, de um objeto line e de um PIN para cada posição no polígono. À medida que o polígono é modificado, você precisa atualizar manualmente a linha e os Pins, o que pode se tornar rapidamente complexo.

Com o Azure Maps, tudo o que você precisa é de um único polígono em uma fonte de dados, conforme mostrado no código abaixo.

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
> [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [DataSourceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [VectorTileSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.vectortilesource?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [VectorTileSourceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.vectortilesourceoptions?view=azure-maps-typescript-latest)

Consulte os artigos a seguir para obter mais exemplos de código para adicionar aos seus mapas:

> [!div class="nextstepaction"]
> [Adicionar um pop-up](map-add-popup.md)

> [!div class="nextstepaction"]
> [Usar expressões de estilo controladas por dados](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de símbolo](map-add-pin.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de bolhas](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de linha](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de polígono](map-add-shape.md)

> [!div class="nextstepaction"]
> [Adicionar um mapa de calor](map-add-heat-map-layer.md)

> [!div class="nextstepaction"]
> [Exemplos de código](https://docs.microsoft.com/samples/browse/?products=azure-maps)