---
title: Dados de ponto de clustering em um mapa | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá como agrupar os dados do ponto de cluster e renderizá-los em um mapa usando o SDK da Web do Microsoft Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen
ms.openlocfilehash: ce2891201331ee1efd861d2f13cec78c0551b6ba
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80804564"
---
# <a name="clustering-point-data"></a>Dados do ponto de cluster

Ao Visualizar muitos pontos de dados no mapa, os pontos de dados podem se sobrepor entre si. A sobreposição pode fazer com que o mapa possa tornar-se ilegível e difícil de usar. O clustering de dados de ponto são o processo de combinar dados de ponto que estão próximos uns dos outros e os representar no mapa como um ponto de dados clusterizado individual. À medida que o usuário aplica zoom ao mapa, os clusters se dividem em seus pontos de dados individuais. Quando você trabalha com um grande número de pontos de dados, use os processos de clustering para melhorar a experiência do usuário.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Clustering-point-data-in-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="enabling-clustering-on-a-data-source"></a>Habilitando o clustering em uma fonte de dados

Habilite o clustering `DataSource` na classe definindo a `cluster` opção como true. Definido `ClusterRadius` como seleciona pontos próximos e os combina em um cluster. O valor de `ClusterRadius` é em pixels. Use `clusterMaxZoom` para especificar um nível de zoom no qual desabilitar a lógica de clustering. Aqui está um exemplo de como habilitar o clustering em uma fonte de dados.

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
> Se dois pontos de dados estiverem próximos juntos no chão, é possível que o cluster nunca se divida, independentemente de como fechar o usuário se amplia. Para resolver isso, você pode definir a `clusterMaxZoom` opção para desabilitar a lógica de clustering e simplesmente exibir tudo.

Aqui estão os métodos adicionais que `DataSource` a classe fornece para clustering:

| Método | Tipo de retorno | Descrição |
|--------|-------------|-------------|
| getClusterChildren (clusterid: número) | Geometria&lt;de&lt;recurso&lt;de matriz Promise&gt; \| , qualquer forma&gt;&gt; | Recupera os filhos do cluster fornecido no próximo nível de zoom. Esses filhos podem ser uma combinação de formas e subclusters. Os subclusters serão recursos com propriedades correspondentes a ClusteredProperties. |
| getClusterExpansionZoom (clusterid: número) | Promise&lt;number&gt; | Calcula um nível de zoom no qual o cluster começará a se expandir ou separar. |
| getClusterLeaves (clusterid: número, limite: número, deslocamento: número) | Geometria&lt;de&lt;recurso&lt;de matriz Promise&gt; \| , qualquer forma&gt;&gt; | Recupera todos os pontos em um cluster. Defina o `limit` para retornar um subconjunto dos pontos e use `offset` para paginar os pontos. |

## <a name="display-clusters-using-a-bubble-layer"></a>Exibir clusters usando uma camada de bolha

Uma camada de bolha é uma ótima maneira de renderizar pontos clusterizados. Use expressões para dimensionar o raio e alterar a cor com base no número de pontos no cluster. Se você exibir clusters usando uma camada de bolha, deverá usar uma camada separada para renderizar pontos de dados não clusterizados.

Para exibir o tamanho do cluster na parte superior da bolha, use uma camada de símbolo com texto e não use um ícone.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Clustering de camada de bolha básica" src="//codepen.io/azuremaps/embed/qvzRZY/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte o <a href='https://codepen.io/azuremaps/pen/qvzRZY/'>agrupamento da camada de bolha básica</a> da caneta pelo Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>Maps () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-clusters-using-a-symbol-layer"></a>Exibir clusters usando uma camada de símbolo

Ao visualizar pontos de dados, a camada de símbolo oculta automaticamente os símbolos que se sobrepõem para garantir uma interface de usuário mais limpa. Esse comportamento padrão pode ser indesejável se você quiser mostrar a densidade dos pontos de dados no mapa. No entanto, essas configurações podem ser alteradas. Para exibir todos os símbolos, defina `allowOverlap` a opção da propriedade camadas `iconOptions` de símbolo `true`como. 

Use o clustering para mostrar a densidade dos pontos de dados enquanto mantém uma interface do usuário limpa. O exemplo a seguir mostra como adicionar símbolos personalizados e representar clusters e pontos de dados individuais usando a camada de símbolo.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Camada de símbolo clusterizado" src="//codepen.io/azuremaps/embed/Wmqpzz/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte a <a href='https://codepen.io/azuremaps/pen/Wmqpzz/'>camada de símbolo clusterizado</a> por caneta pelo mapas<a href='https://codepen.io/azuremaps'>@azuremaps</a>do Azure () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="clustering-and-the-heat-maps-layer"></a>Clustering e a camada de mapas de calor

Mapas de calor são uma ótima maneira de exibir a densidade dos dados no mapa. Esse método de visualização pode lidar com um grande número de pontos de dados por conta própria. Se os pontos de dados forem clusterizados e o tamanho do cluster for usado como o peso do mapa de calor, o mapa de calor poderá lidar com ainda mais dados. Para obter essa opção, defina a `weight` opção da camada do mapa de calor `['get', 'point_count']`como. Quando o raio do cluster for pequeno, o mapa de calor será praticamente idêntico a um mapa de calor usando os pontos de dados não clusterizados, mas ele será muito melhor. No entanto, quanto menor for o raio do cluster, mais preciso será o mapa de calor, mas com menos benefícios de desempenho.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Mapa de calor ponderado do cluster" src="//codepen.io/azuremaps/embed/VRJrgO/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte o <a href='https://codepen.io/azuremaps/pen/VRJrgO/'>mapa de calor ponderado do cluster</a> de caneta pelo<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="mouse-events-on-clustered-data-points"></a>Eventos de mouse em pontos de dados clusterizados

Quando ocorrem eventos de mouse em uma camada que contém pontos de dados clusterizados, o ponto de dados clusterizado retorna ao evento como um objeto de recurso de ponto geojson. Esse recurso de ponto terá as seguintes propriedades:

| Nome da propriedade             | Type    | Descrição   |
|---------------------------|---------|---------------|
| `cluster`                 | booleano | Indica se o recurso representa um cluster. |
| `cluster_id`              | cadeia de caracteres  | Uma ID exclusiva para o cluster que pode ser usada com os métodos DataSource `getClusterExpansionZoom`, `getClusterChildren` e `getClusterLeaves`. |
| `point_count`             | número  | O número de pontos que o cluster contém.  |
| `point_count_abbreviated` | cadeia de caracteres  | Uma cadeia de caracteres que abrevia `point_count` o valor, se for longo. (por exemplo, 4.000 torna-se 4K)  |

Este exemplo usa uma camada de bolha que renderiza pontos de cluster e adiciona um evento de clique. Quando o evento Click é disparado, o código calcula e amplia o mapa para o próximo nível de zoom, no qual o cluster se separa. Essa funcionalidade é implementada usando `getClusterExpansionZoom` o método da `DataSource` classe e a `cluster_id` Propriedade do ponto de dados clusterizado clicado.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="GetClusterExpansionZoom de cluster" src="//codepen.io/azuremaps/embed/moZWeV/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte o <a href='https://codepen.io/azuremaps/pen/moZWeV/'>getClusterExpansionZoom de cluster</a> de caneta pelo mapas<a href='https://codepen.io/azuremaps'>@azuremaps</a>do Azure () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-cluster-area"></a>Exibir área do cluster 

Os dados de ponto que um cluster representa são distribuídos por uma área. Neste exemplo, quando o mouse é focalizado em um cluster, ocorrem dois comportamentos principais. Primeiro, os pontos de dados individuais contidos no cluster serão usados para calcular um convexa envoltória. Em seguida, o convexa envoltória será exibido no mapa para mostrar uma área.  Um convexa envoltória é um polígono que encapsula um conjunto de pontos como uma banda elástica e pode ser calculado usando o `atlas.math.getConvexHull` método. Todos os pontos contidos em um cluster podem ser recuperados da fonte de dados `getClusterLeaves` usando o método.

<br/>

 <iframe height="500" style="width: 100%;" scrolling="no" title="Área de cluster convexa envoltória" src="//codepen.io/azuremaps/embed/QoXqWJ/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte a área de cluster de caneta <a href='https://codepen.io/azuremaps/pen/QoXqWJ/'>convexa envoltória</a> pelo Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>Maps () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="aggregating-data-in-clusters"></a>Agregando dados em clusters

Geralmente, os clusters são representados usando um símbolo com o número de pontos que estão dentro do cluster. Mas, às vezes, é desejável personalizar o estilo de clusters com métricas adicionais. Com as agregações de cluster, as propriedades personalizadas podem ser criadas e populadas usando um cálculo de [expressão de agregação](data-driven-style-expressions-web-sdk.md#aggregate-expression) .  As `DataSource`agregações de cluster podem ser `clusterProperties` definidas na opção do.

O exemplo a seguir usa uma expressão de agregação. O código calcula uma contagem com base na propriedade de tipo de entidade de cada ponto de dados em um cluster. Quando um usuário clica em um cluster, um pop-up é exibido com informações adicionais sobre o cluster.

<iframe height="500" style="width: 100%;" scrolling="no" title="Agregações de cluster" src="//codepen.io/azuremaps/embed/jgYyRL/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte o cluster de caneta que <a href='https://codepen.io/azuremaps/pen/jgYyRL/'>agrega</a> pelo Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Classe DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Objeto dataSourceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [namespace do Atlas. Math](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.math?view=azure-iot-typescript-latest)

Consulte exemplos de código para adicionar funcionalidade ao seu aplicativo:

> [!div class="nextstepaction"]
> [Adicionar uma camada de bolha](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de símbolo](map-add-pin.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada do mapa de calor](map-add-heat-map-layer.md)
