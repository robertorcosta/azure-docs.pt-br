---
title: Adicionar uma camada de mapa de Open Geospatial Consortium (OGC) | Mapas do Microsoft Azure
description: Saiba como sobrepor uma camada do mapa do OGC no mapa e como usar as diferentes opções na classe OgcMapLayer.
author: farah-alyasari
ms.author: v-faalya
ms.date: 03/02/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: c41ee293c853572ec9e1f9dd3edf001c805924d3
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78402779"
---
# <a name="add-a-map-layer-from-the-open-geospatial-consortium-ogc"></a>Adicionar uma camada do mapa do Open Geospatial Consortium (OGC)

A classe `atlas.layer.OgcMapLayer` pode sobrepor imagens do WMS (serviços de mapeamento da Web) e imagens WMTS (serviços de blocos de mapeamento da Web) no mapa. O WMS é um protocolo padrão desenvolvido pelo OGC para servir imagens de mapa georeferenciadas pela Internet. A Image Georeference é o processo de associar uma imagem a uma localização geográfica. WMTS também é um protocolo padrão desenvolvido pelo OGC. Ele foi projetado para fornecer blocos de mapa previamente renderizados e georeferenciados.

As seções a seguir descrevem os recursos do serviço de mapeamento da Web que têm suporte na classe `OgcMapLayer`.

**Serviço de mapeamento da Web (WMS)**

- Versões com suporte: `1.0.0`, `1.1.0`, `1.1.1`e `1.3.0`
- O serviço deve dar suporte ao sistema de projeção `EPSG:3857` ou o serviço deve ser capaz de lidar com as reprojeções.
- O GetFeatureInfo requer que o serviço ofereça suporte a `EPSG:4326` ou a reprojeções de identificador. 
- Operações com suporte:

    | | |
    | :-- | :-- |
    | GetCapabilities | Recupera metadados sobre o serviço com os recursos com suporte |
    | GetMap | Recupera uma imagem de mapa para uma região especificada |
    | GetFeatureInfo | Recupera `feature_info`, que contém dados subjacentes sobre o recurso |

**Serviço de bloco de mapeamento da Web (WMTS)**

- Versões com suporte: `1.0.0`
- Os blocos devem ser quadrados, de modo que `TileWidth == TileHeight`.
- Com suporte para CRS: `EPSG:3857` ou `GoogleMapsCompatible` 
- O identificador TileMatrix deve ser um valor inteiro que corresponda a um nível de zoom no mapa. Em um mapa do Azure, o nível de zoom é um valor entre `"0"` e `"22"`. Portanto, há suporte para `"0"`, mas não há suporte para `"00"`.
- Operações com suporte:

    | | |
    | :-- | :-- |
    | GetCapabilities | Recupera as operações e os recursos com suporte |
    | GetTile | Recupera imagens para um bloco específico |

## <a name="overlay-an-ogc-map-layer"></a>Sobrepor uma camada do mapa do OGC

O `url` pode ser a URL base para o serviço ou uma URL completa com a consulta para obter os recursos do serviço. Dependendo dos detalhes fornecidos, o cliente WFS pode tentar vários formatos de URL padrão para determinar como acessar inicialmente o serviço.

O código a seguir mostra como sobrepor uma camada do mapa do OGC no mapa.

<br/>

<iframe height='700' scrolling='no' title='Exemplo de camada do mapa OGC' src='//codepen.io/azuremaps/embed/xxGLZWB/?height=700&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte o <a href='https://codepen.io/azuremaps/pen/xxGLZWB/'>exemplo de camada do mapa de OGC</a> de caneta pelo Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="ogc-map-layer-options"></a>Opções de camada do mapa OGC

O exemplo abaixo demonstra as diferentes opções de camada do mapa OGC. Você pode clicar no botão de caneta de código no canto superior direito para editar a caneta de código.

<br/>

<iframe height='700' scrolling='no' title='Opções de camada do mapa OGC' src='//codepen.io/azuremaps/embed/abOyEVQ/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte as <a href='https://codepen.io/azuremaps/pen/abOyEVQ/'>Opções de camada do mapa de OGC</a> de caneta pelo Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="ogc-web-map-service-explorer"></a>Gerenciador de serviços do mapa da Web do OGC

A ferramenta a seguir sobrepõe as imagens dos serviços do mapa da Web (WMS) e dos serviços de bloco do mapa da Web (WMTS) como camadas. Você pode selecionar quais camadas no serviço são renderizadas no mapa. Você também pode exibir as legendas associadas para essas camadas.

<br/>

<iframe height='750' style='width: 100%;' scrolling='no' title='Gerenciador de serviços do mapa da Web do OGC' src='//codepen.io/azuremaps/embed/YzXxYdX/?height=750&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Confira a caneta <a href='https://codepen.io/azuremaps/pen/YzXxYdX/'>OGC Web Map Service Explorer</a> pelo Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

Você também pode especificar as configurações de mapa para usar um serviço de proxy. O serviço de proxy permite que você carregue recursos hospedados em domínios que não têm o CORs habilitado.

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [OgcMapLayer](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.layer.ogcmaplayer)

> [!div class="nextstepaction"]
> [OgcMapLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.ogcmaplayeroptions)

Consulte os artigos a seguir, que contêm exemplos de código que você pode adicionar a seus mapas:

> [!div class="nextstepaction"]
> [Conectar-se a um serviço WFS](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Aproveitar as principais operações](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Detalhes de formato de dados com suporte](spatial-io-supported-data-format-details.md)
