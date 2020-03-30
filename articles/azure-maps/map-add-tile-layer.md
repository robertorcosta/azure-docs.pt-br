---
title: Adicione uma camada de azulejo a um mapa | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá a sobrepor uma camada de azulejo em um mapa usando o Microsoft Azure Maps Web SDK. As camadas de azulejo permitem renderizar imagens em um mapa.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 61d7a11df499e6b740adb45968721b6a9bb1af22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76988593"
---
# <a name="add-a-tile-layer-to-a-map"></a>Adicionar uma camada de bloco a um mapa

Este artigo mostra como sobrepor uma camada de Azulejo no mapa. Camadas lado a lado permitem que você sobreponha imagens sobre peças de mapa base do Azure Mapas. Para obter mais informações sobre o sistema de revestimento do Azure Maps, consulte [os níveis de zoom e a grade de azulejos](zoom-levels-and-tile-grid.md).

Uma camada de azulejo é carregada em telhas de um servidor. Essas imagens podem ser pré-renderizadas ou renderizadas dinamicamente. As imagens pré-renderizadas são armazenadas como qualquer outra imagem em um servidor usando uma convenção de nomeação que a camada de ladrilho entende. Imagens renderizadas dinamicamente usam um serviço para carregar as imagens perto do tempo real. Existem três convenções diferentes de nomeação de serviço de ladrilhos suportadas pela classe Azure Maps [TileLayer:](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) 

* Notação X, Y, Zoom - X é a coluna, Y é a posição de linha da telha na grade de ladrilhos, e a notação Zoom um valor baseado no nível de zoom.
* Notação quadkey - Combina informações x, y e zoom em um único valor de seqüência. Esse valor de seqüência torna-se um identificador único para um único ladrilho.
* Caixa de limitador - Especifique uma `{west},{south},{east},{north}`imagem no formato de coordenadas da caixa Delimitador: . Esse formato é comumente usado pelo [Web Mapping Services (WMS)](https://www.opengeospatial.org/standards/wms).

> [!TIP]
> Um [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) é uma ótima maneira para visualizar grandes conjuntos de dados no mapa. Não só uma camada de ladrilho pode ser gerada a partir de uma imagem, os dados vetoriais também podem ser renderizados como uma camada de ladrilho. Ao renderizar dados vetoriais como uma camada de ladrilhos, o controle do mapa só precisa carregar as telhas que são menores em tamanho de arquivo do que os dados vetoriais que eles representam. Esta técnica é comumente usada para renderizar milhões de linhas de dados no mapa.

A URL de ladrilho passada em uma camada de Tile deve ser uma URL http ou https para um recurso TileJSON ou um modelo de URL de ladrilho que usa os seguintes parâmetros: 

* `{x}` - posição X do bloco. Também precisa `{y}` e `{z}`.
* `{y}` - posição Y do bloco. Também precisa `{x}` e `{z}`.
* `{z}` -Nível de zoom do bloco. Também precisa `{x}` e `{y}`.
* `{quadkey}` - identificador quadkey de bloco baseado a convenção de nomenclatura do sistema de blocos Bing Maps.
* `{bbox-epsg-3857}` -Uma cadeia de caracteres de caixa delimitadora com o formato `{west},{south},{east},{north}` no sistema de referência espacial do EPSG 3857.
* `{subdomain}`- Um espaço reservado para os valores `subdomain` do subdomínio, se especificado, será adicionado.

## <a name="add-a-tile-layer"></a>Adicionar uma camada de bloco

 Esta amostra mostra como criar uma camada de ladrilho que aponta para um conjunto de telhas. Esta amostra usa o sistema de revestimento x, y, zoom. A origem dessa camada lado a lado é uma sobreposição de radar clima da [Iowa Environmental Mesonet of Iowa State University](https://mesonet.agron.iastate.edu/ogc/). Ao visualizar dados de radar, o ideal é que os usuários vejam claramente os rótulos das cidades enquanto navegam pelo mapa. Esse comportamento pode ser implementado inserindo a camada de azulejo abaixo da `labels` camada.

```javascript
//Create a tile layer and add it to the map below the label layer.
//Weather radar tiles from Iowa Environmental Mesonet of Iowa State University.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256
}), 'labels');
```

Abaixo está a amostra completa de código de execução da funcionalidade acima.

<br/>

<iframe height='500' scrolling='no' title='Bloco de camada usando X, Y e Z' src='//codepen.io/azuremaps/embed/BGEQjG/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Camada <a href='https://codepen.io/azuremaps/pen/BGEQjG/'>Caneta lado a lado usando X, Y e Z</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-tile-layer"></a>Personalizar uma camada lado a lado

A classe de camada de azulejo tem muitas opções de estilo. Aqui está uma ferramenta para experimentá-las.

<br/>

<iframe height='700' scrolling='no' title='Opções de camada de bloco' src='//codepen.io/azuremaps/embed/xQeRWX/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte as <a href='https://codepen.io/azuremaps/pen/xQeRWX/'>Opções de Camada de Bloco</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [TileLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest)

Consulte os artigos a seguir para obter mais exemplos de código para adicionar aos seus mapas:

> [!div class="nextstepaction"]
> [Adicionar uma camada de imagem](./map-add-image-layer.md)
