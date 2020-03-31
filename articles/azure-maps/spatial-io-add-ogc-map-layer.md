---
title: Adicionar uma camada de mapa do Consórcio Geoespacial Aberto (OGC) | Mapas do Microsoft Azure
description: Saiba como sobrepor uma camada de mapa OGC no mapa e como usar as diferentes opções na classe OgcMapLayer.
author: philmea
ms.author: philmea
ms.date: 03/02/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: b753ecfc07cfb3806838f8a05dbe33ef0bb92730
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334294"
---
# <a name="add-a-map-layer-from-the-open-geospatial-consortium-ogc"></a>Adicione uma camada de mapa do Consórcio Geoespacial Aberto (OGC)

A `atlas.layer.OgcMapLayer` classe pode sobrepor imagens do Web Map Services (WMS) e imagens de Web Map Tile Services (WMTS) no mapa. WMS é um protocolo padrão desenvolvido pela OGC para servir imagens de mapa georreferenciadas pela internet. Georreferenciamento de imagens são os processos de associar uma imagem a uma localização geográfica. WMTS também é um protocolo padrão desenvolvido pela OGC. Foi projetado para servir telhas de mapa pré-renderizadas e georreferenciadas.

As seções a seguir descrevem os recursos de `OgcMapLayer` serviço do mapa web que são suportados pela classe.

**Serviço de Mapa web (WMS)**

- Versões `1.0.0`suportadas: , `1.1.0`, `1.1.1`e`1.3.0`
- O serviço deve `EPSG:3857` suportar o sistema de projeção, ou lidar com reprojeções.
- GetFeatureInfo requer o `EPSG:4326` serviço para suportar ou lidar com reprojeções. 
- Operações suportadas:

    | | |
    | :-- | :-- |
    | Getcapabilities | Recupera metadados sobre o serviço com os recursos suportados |
    | GetMap | Recupera uma imagem de mapa para uma região especificada |
    | GetFeatureInfo | Recupera `feature_info`, que contém dados subjacentes sobre o recurso |

**Serviço de telha do mapa web (WMTS)**

- Versões suportadas:`1.0.0`
- As telhas devem `TileWidth == TileHeight`ser quadradas, de tal forma que.
- CRS suportado: `EPSG:3857` ou`GoogleMapsCompatible` 
- O identificador TileMatrix deve ser um valor inteiro que corresponde a um nível de zoom no mapa. Em um mapa azul, o nível de `"0"` `"22"`zoom é um valor entre e . Então, `"0"` é apoiado, `"00"` mas não é suportado.
- Operações suportadas:

    | | |
    | :-- | :-- |
    | Getcapabilities | Recupera as operações e recursos suportados |
    | GetTile | Recupera imagens para um determinado ladrilho |

## <a name="overlay-an-ogc-map-layer"></a>Sobrepor uma camada de mapa OGC

Pode `url` ser a URL base do serviço ou uma URL completa com a consulta para obter os recursos do serviço. Dependendo dos detalhes fornecidos, o cliente WFS pode tentar vários formatos de URL padrão para determinar como acessar inicialmente o serviço.

O código a seguir mostra como sobrepor uma camada de mapa OGC no mapa.

<br/>

<iframe height='700' scrolling='no' title='Exemplo de camada do Mapa oGC' src='//codepen.io/azuremaps/embed/xxGLZWB/?height=700&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja o exemplo da camada De mapa<a href='https://codepen.io/azuremaps'>@azuremaps</a>Pen <a href='https://codepen.io/azuremaps/pen/xxGLZWB/'>OGC</a> por Azure Maps ( ) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="ogc-map-layer-options"></a>Opções de camada de mapa OGC

A amostra abaixo demonstra as diferentes opções de camada de mapa OGC. Você pode clicar no botão de caneta de código no canto superior direito para editar a caneta de código.

<br/>

<iframe height='700' scrolling='no' title='Opções de camada de mapa OGC' src='//codepen.io/azuremaps/embed/abOyEVQ/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte as opções de camada de mapa<a href='https://codepen.io/azuremaps'>@azuremaps</a>Pen <a href='https://codepen.io/azuremaps/pen/abOyEVQ/'>OGC</a> pelo Azure Maps ( ) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="ogc-web-map-service-explorer"></a>Ogc Web Map Service explorer

A ferramenta a seguir sobrepõe imagens dos Serviços de Mapa da Web (WMS) e do Web Map Tile Services (WMTS) como camadas. Você pode selecionar quais camadas no serviço são renderizadas no mapa. Você também pode ver as lendas associadas para essas camadas.

<br/>

<iframe height='750' style='width: 100%;' scrolling='no' title='Ogc Web Map Service explorer' src='//codepen.io/azuremaps/embed/YzXxYdX/?height=750&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte o explorador do Serviço de Mapas web<a href='https://codepen.io/azuremaps'>@azuremaps</a>do Pen <a href='https://codepen.io/azuremaps/pen/YzXxYdX/'>OGC</a> pelo Azure Maps ( ) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

Você também pode especificar as configurações do mapa para usar um serviço proxy. O serviço proxy permite carregar recursos hospedados em domínios que não possuem CORS ativado.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [OgcMapLayer](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.layer.ogcmaplayer)

> [!div class="nextstepaction"]
> [Opções de camadas ogcMap](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.ogcmaplayeroptions)

Veja os seguintes artigos, que contêm amostras de código que você pode adicionar aos seus mapas:

> [!div class="nextstepaction"]
> [Conecte-se a um serviço WFS](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Alavancar operações principais](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Detalhes do formato de dados suportados](spatial-io-supported-data-format-details.md)
