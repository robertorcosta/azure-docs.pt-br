---
title: Dados de ponto de agrupamento em um mapa | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá como agrupar dados de pontos e renderiza-los em um mapa usando o Microsoft Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen
ms.openlocfilehash: e65681aefc047ba540d4ad0d91ef6e4d2af5f3ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190271"
---
# <a name="clustering-point-data"></a>Dados de ponto de agrupamento

Ao visualizar muitos pontos de dados no mapa, os pontos de dados podem se sobrepor uns sobre os outros. A sobreposição pode fazer com que o mapa se torne ilegível e difícil de usar. O clustering de dados de ponto são o processo de combinar dados de ponto que estão próximos uns dos outros e os representar no mapa como um ponto de dados clusterizado individual. À medida que o usuário aplica zoom ao mapa, os clusters se dividem em seus pontos de dados individuais. Quando você trabalha com um grande número de pontos de dados, use os processos de clustering para melhorar sua experiência de usuário.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Clustering-point-data-in-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="enabling-clustering-on-a-data-source"></a>Habilitando o clustering em uma fonte de dados

Habilite o `DataSource` cluster na `cluster` classe definindo a opção como true. Definido `ClusterRadius` para selecionar pontos próximos e combiná-los em um cluster. O valor `ClusterRadius` está em pixels. Use `clusterMaxZoom` para especificar um nível de zoom para desativar a lógica de clustering. Aqui está um exemplo de como habilitar o clustering em uma fonte de dados.

```javascript
//Create a data source and enable clustering.
var datasource = new atlas.source.DataSource(null, {
    //Tell the data source to cluster point data.
    cluster: true,

    //The radius in pixels to cluster points together.
    clusterRadius: 45,

    //The maximum zoom level in which clustering occurs.
    //If you zoom in more than this, all points are rendered as symbols.
    clusterMaxZoom: 15
});
```

> [!TIP]
> Se dois pontos de dados estiverem próximos no chão, é possível que o cluster nunca se separe, não importa o quão perto o usuário se aproxime. Para resolver isso, você `clusterMaxZoom` pode definir a opção de desativar a lógica de clustering e simplesmente exibir tudo.

Aqui estão os métodos adicionais que a `DataSource` classe fornece para agrupamento:

| Método | Tipo de retorno | Descrição |
|--------|-------------|-------------|
| getClusterChildren (clusterId: número) | Geometria&lt;do recurso&gt; \| &lt;do array&lt;de promessa, qualquer forma&gt;&gt; | Recupera os filhos do cluster fornecido no próximo nível de zoom. Esses filhos podem ser uma combinação de formas e subclusters. Os subclusters serão recursos com propriedades correspondentes a ClusteredProperties. |
| getClusterExpansionZoom (clusterId: número) | Promise&lt;number&gt; | Calcula um nível de zoom no qual o cluster começará a se expandir ou separar. |
| getClusterLeaves (clusterId: número, limite: número, deslocamento: número) | Geometria&lt;do recurso&gt; \| &lt;do array&lt;de promessa, qualquer forma&gt;&gt; | Recupera todos os pontos em um cluster. Defina o `limit` para retornar um subconjunto dos pontos e use `offset` para paginar os pontos. |

## <a name="display-clusters-using-a-bubble-layer"></a>Exibir clusters usando uma camada de bolha

Uma camada de bolha é uma ótima maneira de renderizar pontos agrupados. Use expressões para dimensionar o raio e alterar a cor com base no número de pontos no cluster. Se você exibir clusters usando uma camada de bolha, então você deve usar uma camada separada para renderizar pontos de dados não agrupados.

Para exibir o tamanho do cluster no topo da bolha, use uma camada de símbolo com texto e não use um ícone.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Agrupamento básico de camadas de bolha" src="//codepen.io/azuremaps/embed/qvzRZY/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte o <a href='https://codepen.io/azuremaps/pen/qvzRZY/'>agrupamento de camadas de</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>bolha Base da Caneta pelo Azure Maps ( ) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-clusters-using-a-symbol-layer"></a>Exibir clusters usando uma camada de símbolo

Ao visualizar pontos de dados, a camada de símbolos oculta automaticamente símbolos que se sobrepõem uns aos outros para garantir uma interface de usuário mais limpa. Esse comportamento padrão pode ser indesejável se você quiser mostrar a densidade de pontos de dados no mapa. No entanto, essas configurações podem ser alteradas. Para exibir todos os `allowOverlap` símbolos, defina a opção da propriedade 'Camadas `iconOptions` símbolo' como `true`. 

Use clustering para mostrar a densidade dos pontos de dados enquanto mantém uma interface de usuário limpa. A amostra abaixo mostra como adicionar símbolos personalizados e representar clusters e pontos de dados individuais usando a camada de símbolos.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Camada de símbolo agrupado" src="//codepen.io/azuremaps/embed/Wmqpzz/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte a <a href='https://codepen.io/azuremaps/pen/Wmqpzz/'>camada Símbolo agrupado de canetas</a> pelo Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="clustering-and-the-heat-maps-layer"></a>Agrupamento e a camada de mapas de calor

Mapas de calor são uma ótima maneira de exibir a densidade de dados no mapa. Este método de visualização pode lidar com um grande número de pontos de dados por conta própria. Se os pontos de dados forem agrupados e o tamanho do cluster for usado como o peso do mapa de calor, então o mapa de calor pode lidar com ainda mais dados. Para obter essa opção, defina a `weight` `['get', 'point_count']`opção da camada do mapa de calor como . Quando o raio de cluster é pequeno, o mapa de calor parecerá quase idêntico a um mapa de calor usando os pontos de dados não agrupados, mas terá um desempenho muito melhor. No entanto, quanto menor o raio de cluster, mais preciso será o mapa de calor, mas com menos benefícios de desempenho.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Mapa de calor ponderado em cluster" src="//codepen.io/azuremaps/embed/VRJrgO/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte o Mapa de <a href='https://codepen.io/azuremaps/pen/VRJrgO/'>Calor ponderado</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>do Cluster caneta por Mapas Azure ( ) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="mouse-events-on-clustered-data-points"></a>Eventos do mouse em pontos de dados agrupados

Quando os eventos do mouse ocorrem em uma camada que contém pontos de dados agrupados, o ponto de dados agrupado retorna ao evento como um objeto de recurso de ponto GeoJSON. Este recurso de ponto terá as seguintes propriedades:

| Nome da propriedade             | Type    | Descrição   |
|---------------------------|---------|---------------|
| `cluster`                 | booleano | Indica se o recurso representa um cluster. |
| `cluster_id`              | string  | Uma ID exclusiva para o cluster que pode ser usada com os métodos DataSource `getClusterExpansionZoom`, `getClusterChildren` e `getClusterLeaves`. |
| `point_count`             | número  | O número de pontos que o cluster contém.  |
| `point_count_abbreviated` | string  | Uma seqüência que abrevia `point_count` o valor se for longo. (por exemplo, 4.000 torna-se 4K)  |

Este exemplo pega uma camada de bolha que renderiza pontos de cluster e adiciona um evento de clique. Quando o evento de clique é acionado, o código calcula e amplia o mapa para o próximo nível de zoom, no qual o cluster se separa. Essa funcionalidade é implementada `getClusterExpansionZoom` usando `DataSource` o método `cluster_id` da classe e a propriedade do ponto de dados clustered clicado.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Cluster getClusterExpansionZoom" src="//codepen.io/azuremaps/embed/moZWeV/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte o Cluster caneta <a href='https://codepen.io/azuremaps/pen/moZWeV/'>getClusterExpansionZoom</a> by<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps ( ) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-cluster-area"></a>Área de cluster de exibição 

Os dados de ponto que um cluster representa estão espalhados por uma área. Nesta amostra, quando o mouse é pairado sobre um cluster, dois comportamentos principais ocorrem. Primeiro, os pontos de dados individuais contidos no cluster serão usados para calcular um casco convexo. Em seguida, o casco convexo será exibido no mapa para mostrar uma área.  Um casco convexo é um polígono que envolve um conjunto de `atlas.math.getConvexHull` pontos como uma faixa elástica e pode ser calculado usando o método. Todos os pontos contidos em um cluster podem `getClusterLeaves` ser recuperados da fonte de dados usando o método.

<br/>

 <iframe height="500" style="width: 100%;" scrolling="no" title="Casco convexo da área de cluster" src="//codepen.io/azuremaps/embed/QoXqWJ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte o <a href='https://codepen.io/azuremaps/pen/QoXqWJ/'>casco convexo da área do Cluster</a> da Caneta pelo Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="aggregating-data-in-clusters"></a>Agregando dados em clusters

Muitas vezes os clusters são representados usando um símbolo com o número de pontos que estão dentro do cluster. Mas, às vezes, é desejável personalizar o estilo de clusters com métricas adicionais. Com agregados de cluster, propriedades personalizadas podem ser criadas e preenchidas usando um cálculo [de expressão agregado.](data-driven-style-expressions-web-sdk.md#aggregate-expression)  Os agregados de `clusterProperties` cluster podem `DataSource`ser definidos em opção do .

A amostra a seguir usa uma expressão agregada. O código calcula uma contagem com base na propriedade do tipo entidade de cada ponto de dados em um cluster. Quando um usuário clica em um cluster, um pop-up aparece com informações adicionais sobre o cluster.

<iframe height="500" style="width: 100%;" scrolling="no" title="Agregados de cluster" src="//codepen.io/azuremaps/embed/jgYyRL/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte os <a href='https://codepen.io/azuremaps/pen/jgYyRL/'>agregados</a> do Cluster de<a href='https://codepen.io/azuremaps'>@azuremaps</a>Caneta saqueados pelo Azure Maps ( ) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Classe DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Objeto DataSourceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [espaço de nome atlas.math](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.math?view=azure-iot-typescript-latest)

Consulte exemplos de código para adicionar funcionalidade ao seu aplicativo:

> [!div class="nextstepaction"]
> [Adicione uma camada de bolha](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Adicione uma camada de símbolo](map-add-pin.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada do mapa de calor](map-add-heat-map-layer.md)
