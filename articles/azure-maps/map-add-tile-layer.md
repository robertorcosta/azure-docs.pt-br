---
title: Adicionar uma camada de bloco a um mapa | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá a sobrepor uma camada de bloco em um mapa usando o SDK da Web do Microsoft Azure Maps. As camadas de bloco permitem renderizar imagens em um mapa.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 83e8f6d684d6d39102fd682653cd19816a9f7b10
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911102"
---
# <a name="add-a-tile-layer-to-a-map"></a>Adicionar uma camada de bloco a um mapa

Este artigo mostra como você pode sobrepor uma camada de peças do mapa. Camadas lado a lado permitem que você sobreponha imagens sobre peças de mapa base do Azure Mapas. Obtenha mais informações sobre o sistema de mosaico do Azure Mapas na documentação [Níveis de Zoom e grade lado a lado](zoom-levels-and-tile-grid.md).

Uma camada de bloco é carregada em blocos de um servidor. Essas imagens podem ser renderizadas previamente e armazenadas como qualquer outra imagem em um servidor usando uma convenção de nomenclatura que compreende a camada de peça ou um serviço dinâmico que gera as imagens em tempo real. Há três convenções de nomenclatura de serviço de bloco diferentes com suporte pela classe [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) do Azure Maps: 

* X, Y, notação de Zoom - com base no nível de zoom, x é a coluna e y é a posição da linha do bloco na grade lado a lado.
* Notação Quadkey - combinação x, y, informações de zoom em um valor de cadeia de caracteres única que é um identificador exclusivo para um bloco.
* Caixa delimitadora - as coordenadas da caixa de delimitação podem ser usadas para especificar uma imagem no formato `{west},{south},{east},{north}` que é normalmente usada por [serviços de mapeamento de Web (WMS)](https://www.opengeospatial.org/standards/wms).

> [!TIP]
> Um [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) é uma ótima maneira para visualizar grandes conjuntos de dados no mapa. Não apenas uma camada de bloco pode ser gerada de uma imagem, mas os dados de vetor podem também ser renderizados como uma camada de peça. Por meio do processamento de dados de vetor, como uma camada lado a lado, o controle de mapa só precisa carregar os blocos que podem ser muito menores em tamanho de arquivo do que os dados vetoriais que representam. Essa técnica é usada por muitas pessoas que precisam renderizar milhões de linhas de dados no mapa.

A URL do bloco passada para uma camada de peça deve ser uma URL http/https para um recurso de TileJSON ou um modelo de URL de bloco que usa os seguintes parâmetros: 

* `{x}` - posição X do bloco. Também precisa `{y}` e `{z}`.
* `{y}` - posição Y do bloco. Também precisa `{x}` e `{z}`.
* `{z}` -Nível de zoom do bloco. Também precisa `{x}` e `{y}`.
* `{quadkey}` - identificador quadkey de bloco baseado a convenção de nomenclatura do sistema de blocos Bing Maps.
* `{bbox-epsg-3857}` -Uma cadeia de caracteres de caixa delimitadora com o formato `{west},{south},{east},{north}` no sistema de referência espacial do EPSG 3857.
* `{subdomain}` -Um espaço reservado para onde os valores de subdomínio, se especificado serão adicionados.

## <a name="add-a-tile-layer"></a>Adicionar uma camada de bloco

 Este exemplo mostra como criar uma camada lado a lado que aponta para um conjunto de blocos que usam o sistema lado a lado do zoom, x,y. A origem dessa camada lado a lado é uma sobreposição de radar clima da [Iowa Environmental Mesonet of Iowa State University](https://mesonet.agron.iastate.edu/ogc/). Ao exibir dados de radar, o ideal é que os usuários possam ver claramente os rótulos das cidades à medida que navegam no mapa, o que pode ser feito inserindo-se a camada de peça abaixo da camada de `labels`.

```javascript
//Create a tile layer and add it to the map below the label layer.
//Weather radar tiles from Iowa Environmental Mesonet of Iowa State University.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256
}), 'labels');
```

Abaixo está o exemplo de código completo em execução da funcionalidade acima.

<br/>

<iframe height='500' scrolling='no' title='Bloco de camada usando X, Y e Z' src='//codepen.io/azuremaps/embed/BGEQjG/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Camada <a href='https://codepen.io/azuremaps/pen/BGEQjG/'>Caneta lado a lado usando X, Y e Z</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-tile-layer"></a>Personalizar uma camada lado a lado

A classe da camada do bloco tem muitas opções de estilo. Aqui está uma ferramenta para experimentá-las.

<br/>

<iframe height='700' scrolling='no' title='Opções de camada de bloco' src='//codepen.io/azuremaps/embed/xQeRWX/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte as <a href='https://codepen.io/azuremaps/pen/xQeRWX/'>Opções de Camada de Bloco</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [TileLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest)

Consulte os artigos a seguir para obter mais exemplos de código para adicionar aos seus mapas:

> [!div class="nextstepaction"]
> [Adicionar uma camada de imagem](./map-add-image-layer.md)
