---
title: Adicione uma camada de símbolo a um mapa | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá sobre como usar a camada Símbolo para personalizar um símbolo e adicionar símbolos em um mapa usando o Microsoft Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: b8d131dcc798fb2fe1d4bb650cd5b0a68903381b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77209691"
---
# <a name="add-a-symbol-layer-to-a-map"></a>Adicionar uma camada de símbolo a um mapa

Conecte um símbolo a uma fonte de dados e use-o para renderizar um ícone ou um texto em um dado ponto. 

As camadas de símbolo são renderizadas usando o WebGL. Use uma camada de símbolo para renderizar grandes coleções de pontos no mapa. Em comparação com o marcador HTML, a camada de símbolo renderiza um grande número de dados de ponto no mapa, com melhor desempenho. No entanto, a camada de símbolo não suporta elementos CSS e HTML tradicionais para estilo.  

> [!TIP]
> Por padrão, as camadas de Símbolo renderizarão as coordenadas de todas as geometrias em uma fonte de dados. Para limitar a camada de tal forma que `filter` ela só renderiza `['==', ['geometry-type'], 'Point']` `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` recursos de geometria de ponto definir a propriedade da camada para ou se você quiser, você pode incluir recursos do MultiPoint também.

O gerenciador de sprite de imagem de mapas carrega imagens personalizadas usadas pela camada de símbolo. Ele suporta os seguintes formatos de imagem:

- JPEG
- PNG
- SVG
- BMP
- GIF (sem animações)

## <a name="add-a-symbol-layer"></a>Adicionar uma camada de símbolo

Antes de adicionar uma camada de símbolo ao mapa, você precisa dar alguns passos. Primeiro, crie uma fonte de dados e adicione-a ao mapa. Crie uma camada de símbolo. Em seguida, passe a fonte de dados para a camada de símbolos, para recuperar os dados da fonte de dados. Finalmente, adicione dados na fonte de dados, para que haja algo a ser renderizado. 

O código abaixo demonstra o que deve ser adicionado ao mapa depois de carregado. Esta amostra renderiza um único ponto no mapa usando uma camada de símbolo. 

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a symbol layer to render icons and/or text at points on the map.
var layer = new atlas.layer.SymbolLayer(dataSource);

//Add the layer to the map.
map.layers.add(layer);

//Create a point and add it to the data source.
dataSource.add(new atlas.data.Point([0, 0]));
```

Existem quatro tipos diferentes de dados de ponto que podem ser adicionados ao mapa:

- Geometria do ponto GeoJSON - Este objeto contém apenas uma coordenada de um ponto e nada mais. A `atlas.data.Point` classe auxiliar pode ser usada para criar facilmente esses objetos.
- Geometria GeoJSON MultiPoint - Este objeto contém as coordenadas de vários pontos e nada mais. A `atlas.data.MultiPoint` classe auxiliar pode ser usada para criar facilmente esses objetos.
- GeoJSON Feature - Este objeto consiste em qualquer geometria GeoJSON e um conjunto de propriedades que contêm metadados associados à geometria. A `atlas.data.Feature` classe auxiliar pode ser usada para criar facilmente esses objetos.
- `atlas.Shape`classe é semelhante ao recurso GeoJSON. Ambos consistem em uma geometria GeoJSON e um conjunto de propriedades que contêm metadados associados à geometria. Se um objeto GeoJSON for adicionado a uma fonte de dados, ele pode ser facilmente renderizado em uma camada. No entanto, se a propriedade coordenada desse objeto GeoJSON for atualizada, a fonte de dados e o mapa não mudarão. Isso porque não há mecanismo no objeto JSON para ativar uma atualização. A classe de forma fornece funções para atualizar os dados que contém. Quando uma alteração é feita, a fonte de dados e o mapa são automaticamente notificados e atualizados. 

A amostra de código a seguir cria uma geometria GeoJSON Point e passa-a para a `atlas.Shape` classe para facilitar a atualização. O centro do mapa é inicialmente usado para renderizar um símbolo. Um evento de clique é adicionado ao mapa de modo que, quando ele `setCoordinates` é acionado, as coordenadas do mouse são usadas com a função de formas. As coordenadas do mouse são gravadas no momento do evento do clique. Em seguida, as `setCoordinates` atualizações da localização do símbolo no mapa.

<br/>

<iframe height='500' scrolling='no' title='Mudar localização de fixação' src='//codepen.io/azuremaps/embed/ZqJjRP/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Confira a Caneta <a href='https://codepen.io/azuremaps/pen/ZqJjRP/'>Mudar localização de fixação</a> por Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Por padrão, as camadas de símbolos otimizam a renderização de símbolos ocultando símbolos que se sobrepõem. À medida que você amplia, os símbolos ocultos se tornam visíveis. Para desativar esse recurso e renderizar todos os `allowOverlap` símbolos `iconOptions` o `true`tempo todo, defina a propriedade das opções para .

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>Adicionar um ícone personalizado a uma camada de símbolo

As camadas de símbolo são renderizadas usando o WebGL. Assim, todos os recursos, como imagens de ícone, precisam ser carregados no contexto do WebGL. Esta amostra mostra como adicionar um ícone personalizado aos recursos do mapa. Este ícone é então usado para renderizar dados de ponto com um símbolo personalizado no mapa. A propriedade `textField` da camada de símbolo exige a especificação de uma expressão. Neste caso, queremos tornar a propriedade da temperatura. Uma vez que a temperatura é um número, ele precisa ser convertido em uma seqüência. Além disso, queremos anexar "°F" a ele. Uma expressão pode ser usada para fazer essa concatenação; `['concat', ['to-string', ['get', 'temperature']], '°F']`. 

<br/>

<iframe height='500' scrolling='no' title='Ícone de Imagem de Símbolo Personalizado' src='//codepen.io/azuremaps/embed/WYWRWZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Confira a Caneta <a href='https://codepen.io/azuremaps/pen/WYWRWZ/'>Ícone de Imagem do Símbolo Personalizado</a> do Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> O SDK web Do Azure Maps fornece vários modelos de imagem personalizáveis que você pode usar com a camada de símbolo. Para obter mais informações, consulte o documento [Como usar modelos de imagem.](how-to-use-image-templates-web-sdk.md)

## <a name="customize-a-symbol-layer"></a>Personalizar uma camada de símbolo 

A camada de símbolo tem muitas opções de estilo disponíveis. Veja a seguir uma ferramenta para testar essas várias opções de estilo.

<br/>

<iframe height='700' scrolling='no' title='Opções da camada de símbolo' src='//codepen.io/azuremaps/embed/PxVXje/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Confira a Caneta <a href='https://codepen.io/azuremaps/pen/PxVXje/'>Opções de Camada de Símbolo</a> do Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Quando você deseja renderizar apenas texto com uma camada de `image` símbolo, você `'none'`pode ocultar o ícone definindo a propriedade das opções de íconepara .

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [SymbolLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Textoptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)

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
> [Adicionar uma camada de linha](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de polígono](map-add-shape.md)

> [!div class="nextstepaction"]
> [Adicione uma camada de bolha](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Adicionar marcadores HTML](map-add-bubble-layer.md)
