---
title: Adicione uma camada de linha a um mapa | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá como adicionar uma camada de linha a um mapa usando o Microsoft Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: c473be25907eb3a761fbccd598bb9b732e5be5b9
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/07/2020
ms.locfileid: "80802341"
---
# <a name="add-a-line-layer-to-the-map"></a>Adicione uma camada de linha ao mapa

Uma camada de linha `LineString` pode `MultiLineString` ser usada para renderizar e recursos como caminhos ou rotas no mapa. Uma camada de `Polygon` linha também pode ser `MultiPolygon` usada para renderizar o contorno e os recursos. Uma fonte de dados é conectada a uma camada de linha para fornecer dados para renderizar. 

> [!TIP]
> Camadas de linha por padrão tornarão as coordenadas dos polígonos, bem como linhas em uma fonte de dados. Para limitar a camada, de tal forma `filter` que ele só `['==', ['geometry-type'], 'LineString']` `['any', ['==', ['geometry-type'], 'LineString'], ['==', ['geometry-type'], 'MultiLineString']]` renderiza os recursos do LineString, defina a propriedade da camada para ou se você quiser incluir recursos MultiLineString também.

O código a seguir mostra como criar uma linha. Adicione a linha a uma fonte de dados e, em seguida, torne-a com uma camada de linha usando a classe [LineLayer.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest)

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a line and add it to the data source.
dataSource.add(new atlas.data.LineString([[-73.972340, 40.743270], [-74.004420, 40.756800]]));
  
//Create a line layer to render the line to the map.
map.layers.add(new atlas.layer.LineLayer(dataSource, null, {
    strokeColor: 'blue',
    strokeWidth: 5
}));
```

Abaixo está a amostra completa de código de execução da funcionalidade acima.

<br/>

<iframe height='500' scrolling='no' title='Adicionar uma linha a um mapa' src='//codepen.io/azuremaps/embed/qomaKv/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/qomaKv/'>Adicionar uma linha a um mapa</a> pelo Mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

As camadas de linha podem ser estilizadas usando [linelayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest) e [usar expressões de estilo baseadas em dados](data-driven-style-expressions-web-sdk.md).

## <a name="add-symbols-along-a-line"></a>Adicionar símbolos ao longo de uma linha

Esta amostra mostra como adicionar ícones de seta ao longo de uma linha no mapa. Ao usar uma camada de símbolo, defina a opção "placement" como "linha". Esta opção renderizará os símbolos ao longo da linha e girará os ícones (0 graus = à direita).

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Mostrar seta ao longo da linha" src="//codepen.io/azuremaps/embed/drBJwX/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Veja a <a href='https://codepen.io/azuremaps/pen/drBJwX/'>seta</a> Do Show da<a href='https://codepen.io/azuremaps'>@azuremaps</a>Caneta ao longo da linha do Azure Maps ( ) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> O SDK web Do Azure Maps fornece vários modelos de imagem personalizáveis que você pode usar com a camada de símbolo. Para obter mais informações, consulte o documento [Como usar modelos de imagem.](how-to-use-image-templates-web-sdk.md)

<a name="line-stroke-gradient"></a>

## <a name="add-a-stroke-gradient-to-a-line"></a>Adicione um gradiente de traçado a uma linha

Você pode aplicar uma única cor de traçado a uma linha. Você também pode preencher uma linha com um gradiente de cores para mostrar a transição de um segmento de linha para o próximo segmento de linha. Por exemplo, gradientes de linha podem ser usados para representar mudanças ao longo do tempo e distância, ou temperaturas diferentes através de uma linha conectada de objetos. Para aplicar esse recurso a uma linha, a `lineMetrics` fonte de dados deve ter a opção definida como `strokeColor` true e, em seguida, uma expressão de gradiente de cor pode ser passada para a opção da linha. A expressão gradiente de `['line-progress']` traçado tem que fazer referência à expressão de dados que expõe as métricas de linha calculadas à expressão.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Linha com gradiente de traçado" src="//codepen.io/azuremaps/embed/wZwWJZ/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte a linha <a href='https://codepen.io/azuremaps/pen/wZwWJZ/'>de caneta com gradiente de traçado</a> por Mapas Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-line-layer"></a>Personalizar uma camada de linha

A camada Linha tem várias opções de estilo. Aqui está uma ferramenta para experimentá-las.

<br/>

<iframe height='700' scrolling='no' title='Opções da Camada de Linha' src='//codepen.io/azuremaps/embed/GwLrgb/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/GwLrgb/'>Opções de Camada de Linha</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Camada de linha](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) 

> [!div class="nextstepaction"]
> [Opções de camadas de linha](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)

Consulte os artigos a seguir para obter mais exemplos de código para adicionar aos seus mapas:

> [!div class="nextstepaction"]
> [Criar uma fonte de dados](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Adicionar um pop-up](map-add-popup.md)

> [!div class="nextstepaction"]
> [Usar expressões de estilo controladas por dados](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Como usar modelos de imagem](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de polígono](map-add-shape.md)
