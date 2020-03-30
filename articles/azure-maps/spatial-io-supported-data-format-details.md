---
title: Detalhes do formato de dados suportados | Mapas do Microsoft Azure
description: Saiba como os dados espaciais delimitados são analisados no módulo io espacial.
author: philmea
ms.author: philmea
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 3353620f1751e939a04543115fe704555fb3bc21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334095"
---
# <a name="supported-data-format-details"></a>Detalhes do formato de dados suportados

Este artigo fornece detalhes sobre o suporte à leitura e gravação para todas as tags XML e tipos de geometria de texto bem conhecidos. Também detalha como os dados espaciais delimitados são analisados no módulo io espacial.

## <a name="supported-xml-namespaces"></a>Espaços de nomes XML suportados

O módulo IO espacial suporta tags XML dos seguintes namespaces.

| Prefixo de espaço de nome | URI de namespace   | Observações                                                                    |
|:------------------|:-----------------|:----------------------------------------|
| `atom`           | `http://www.w3.org/2005/Atom`   |                                         |
| `geo`            | `http://www.w3.org/2003/01/geo/wgs84_pos#`  | Leia apenas o suporte em arquivos GeoRSS.           |
| `georss`         | `http://www.georss.org/georss`  |                                                |
| `geourl`         | `http://geourl.org/rss/module/` | Leia apenas o suporte em arquivos GeoRSS.                       |
| `gml`            | `http://www.opengis.net/gml`    |                                                        |
| `gpx`            | `http://www.topografix.com/GPX/1/1` |                                                   |
| `gpxx`           | `http://www.garmin.com/xmlschemas/GpxExtensions/v3` | Leia apenas o suporte em arquivos GPX. Analisa e usa DisplayColor. Todas as outras propriedades adicionadas aos metadados de forma. |
| `gpx_style`      | `http://www.topografix.com/GPX/gpx_style/0/2`      | Suportado em arquivos GPX. Usa a cor da linha. |
| `gx`             | `http://www.google.com/kml/ext/2.2` |                                                      |
| `kml`            | `http://www.opengis.net/kml/2.2`    |                                                      |
| `rss`            |                                 | Somente leitura. GeoRSS grava usando o formato Átomo.              |

## <a name="supported-xml-elements"></a>Elementos XML suportados

O módulo IO espacial suporta os seguintes elementos XML. Todas as tags XML que não forem suportadas serão convertidas em um objeto JSON. Em seguida, cada tag será adicionada `properties` como uma propriedade no campo da forma ou camada dos pais.

### <a name="kml-elements"></a>Elementos KML

O módulo IO espacial suporta os seguintes elementos KML.

| Nome do elemento         | Ler    | Gravar   | Observações                                                                                                                      |
|----------------------|---------|---------|----------------------------------------------------------------------------------------------------------------------------|
| `address`            | parcial | sim     | O objeto é analisado, mas não é usado para posicionar a forma.                                                                    |
| `AddressDetails`     | parcial | não      | O objeto é analisado, mas não é usado para posicionar a forma.                                                                    |
| `atom:author`        | sim     | sim     |                                                                                                                            |
| `atom:link`          | sim     | sim     |                                                                                                                            |
| `atom:name`          | sim     | sim     |                                                                                                                            |
| `BalloonStyle`       | parcial | parcial | Não há suporte para `displayMode`. Convertido em `PopupTemplate`um . Para escrever, `popupTemplate` adicione uma propriedade do recurso para o que você deseja escrever. |
| `begin`              | sim     | sim     |                                                                                                                            |
| `color`              | sim     | sim     | Inclui `#AABBGGRR` e `#BBGGRR`. Analisado em uma seqüência de cores CSS                                                           |
| `colorMode`          | sim     | não      |                                                                                                                            |
| `coordinates`        | sim     | sim     |                                                                                                                            |
| `Data`               | sim     | sim     |                                                                                                                            |
| `description`        | sim     | sim     |                                                                                                                            |
| `displayName`        | sim     | sim     |                                                                                                                            |
| `Document`           | sim     | sim     |                                                                                                                            |
| `drawOrder`          | parcial | não      | Leia para sobreposições terrestres e usado para classificá-los. 
| `east`               | sim     | sim     |                                                                                                                            |
| `end`                | sim     | sim     |                                                                                                                            |
| `ExtendedData`       | sim     | sim     | Suporta substituições `Data`não `SimpleData` `Schema`digitadas ou de `$[dataName]`entidades do formulário .                      |
| `extrude`            | parcial | parcial | Só apoiado para polígonos. MultiGeometria que tem polígonos de diferentes alturas será dividida em características individuais. Estilos de linha não são suportados. Polígonos com altitude de 0 serão renderizados como um polígono plano. Ao ler, a altitude da primeira coordenada no anel exterior será adicionada como uma propriedade de altura do polígono. Em seguida, a altitude da primeira coordenada será usada para renderizar o polígono no mapa. |
| `fill`               | sim     | sim     |                                                                                                                            |
| `Folder`             | sim     | sim     |                                                                                                                            |
| `GroundOverlay`      | sim     | sim     | `color`não é suportado                                                                                                   |
| `heading`            | parcial | não      | Analisado, mas não prestado `SimpleDataLayer`por . Só grava se os dados forem armazenados na propriedade da forma.                 |
| `hotSpot`            | sim     | parcial | Só grava se os dados forem armazenados na propriedade da forma. As unidades são descontinuadas apenas como "pixels".                         |
| `href`               | sim     | sim     |                                                                                                                            |
| `Icon`               | parcial | parcial | Analisado, mas não prestado `SimpleDataLayer`por . Só grava a propriedade ícone da forma se ela contiver um dado URI. Apenas `href` tem suporte. |
| `IconStyle`          | parcial | parcial | `icon`, `heading` `colorMode`, `hotspots` e valores são analisados, mas eles não são prestados por`SimpleDataLayer`         |
| `innerBoundaryIs`    | sim     | sim     |                                                                                                                            |
| `kml`                | sim     | sim     |                                                                                                                            |
| `LabelStyle`         | não      | não      |                                                                                                                            |
| `LatLonBox`          | sim     | sim     |                                                                                                                            |
| `gx:LatLonQuad`      | sim     | sim     |                                                                                                                            |
| `LinearRing`         | sim     | sim     |                                                                                                                            |
| `LineString`         | sim     | sim     |                                                                                                                            |
| `LineStyle`          | sim     | sim     | Não há suporte para `colorMode`.                                                                                         |
| `Link`               | sim     | não      | Apenas `href` a propriedade é suportada para links de rede.                                                                   |
| `MultiGeometry`      | parcial | parcial | Pode ser dividido em características individuais quando lido.                                                                     |
| `name`               | sim     | sim     |                                                                                                                            |
| `NetworkLink`        | sim     | não      | Os links precisam estar no mesmo domínio do documento.                                                                  |
| `NetworkLinkControl` | não      | não      |                                                                                                                            |
| `north`              | sim     | sim     |                                                                                                                            |
| `open`               | sim     | sim     |                                                                                                                            |
| `outerBoundaryIs`    | sim     | sim     |                                                                                                                            |
| `outline`            | sim     | sim     |                                                                                                                            |
| `overlayXY`          | não      | não      |                                                                                                                            |
| `Pair`               | parcial | não      | Apenas `normal` o estilo `StyleMap` em um é suportado. Não há suporte para `highlight`.                                   |
| `phoneNumber`        | sim     | sim     |                                                                                                                            |
| `PhotoOverlay`       | não      | não      |                                                                                                                            |
| `Placemark`          | sim     | sim     |                                                                                                                            |
| `Point`              | sim     | sim     |                                                                                                                            |
| `Polygon`            | sim     | sim     |                                                                                                                            |
| `PolyStyle`          | sim     | sim     |                                                                                                                            |
| `Region`             | parcial | parcial | `LatLongBox`é suportado no nível do documento.                                                                      |
| `rotation`           | não      | não      |                                                                                                                            |
| `rotationXY`         | não      | não      |                                                                                                                            |
| `scale`              | não      | não      |                                                                                                                            |
| `Schema`             | sim     | sim     |                                                                                                                            |
| `SchemaData`         | sim     | sim     |                                                                                                                            |
| `schemaUrl`          | parcial | sim     | Não suporta estilos de carregamento de documentos externos que não estão incluídos em um KMZ.                             |
| `ScreenOverlay`      | não      | não      |                                                                                                                            |
| `screenXY`           | não      | não      |                                                                                                                            |
| `SimpleData`         | sim     | sim     |                                                                                                                            |
| `SimpleField`        | sim     | sim     |                                                                                                                            |
| `size`               | não      | não      |                                                                                                                            |
| `Snippet`            | parcial | parcial | `maxLines`atributo é ignorado.                                                                                  |
| `south`              | sim     | sim     |                                                                                                                            |
| `Style`              | sim     | sim     |                                                                                                                            |
| `StyleMap`           | parcial | não      | Apenas o estilo `StyleMap` normal em um é suportado.                                                                        |
| `styleUrl`           | parcial | sim     | URLs de estilo externo não são suportados.                                                                         |
| `text`               | sim     | sim     | A `$[geDirections]` substituição não é suportada                                                                          |
| `textColor`          | sim     | sim     |                                                                                                                            |
| `TimeSpan`           | sim     | sim     |                                                                                                                            |
| `TimeStamp`          | sim     | sim     |                                                                                                                            |
| `value`              | sim     | sim     |                                                                                                                            |
| `viewRefreshMode`    | parcial | não      |  Se apontar para um serviço WMS, então só `onStop` é suportado para sobreposições de solo. Será anexado `BBOX=[bboxWest],[bboxSouth],[bboxEast],[bboxNorth]` à URL e atualizará à medida que o mapa se move.  |
| `visibility`         | sim     | sim     |                                                                                                                            |
| `west`               | sim     | sim     |                                                                                                                            |
| `when`               | sim     | sim     |                                                                                                                            |
| `width`              | sim     | sim     |                                                                                                                            |

### <a name="georss-elements"></a>Elementos GeoRSS

O módulo IO espacial suporta os seguintes elementos GeoRSS.

| Nome do elemento             | Ler    | Gravar | Observações                                                                                          |
|--------------------------|---------|-------|------------------------------------------------------------------------------------------------|
| `atom:author`            | sim     | sim   |                                                                                                |
| `atom:category`          | sim     | sim   |                                                                                                |
| `atom:content`           | sim     | sim   |                                                                                                |
| `atom:contributor`       | sim     | sim   |                                                                                                |
| `atom:email`             | sim     | sim   |                                                                                                |
| `atom:entry`             | sim     | sim   |                                                                                                |
| `atom:feed`              | sim     | sim   |                                                                                                |
| `atom:icon`              | sim     | sim   |                                                                                                |
| `atom:id`                | sim     | sim   |                                                                                                |
| `atom:link`              | sim     | sim   |                                                                                                |
| `atom:logo`              | sim     | sim   |                                                                                                |
| `atom:name`              | sim     | sim   |                                                                                                |
| `atom:published`         | sim     | sim   |                                                                                                |
| `atom:rights`            | sim     | sim   |                                                                                                |
| `atom:source`            | sim     | sim   |                                                                                                |
| `atom:subtitle`          | sim     | sim   |                                                                                                |
| `atom:summary`           | sim     | sim   |                                                                                                |
| `atom:title`             | sim     | sim   |                                                                                                |
| `atom:updated`           | sim     | sim   |                                                                                                |
| `atom:uri`               | sim     | sim   |                                                                                                |
| `geo:lat`                | sim     | não    | Escrito como `georss:point`um .                                                                   |
| `geo:lon`                | sim     | não    | Escrito como `georss:point`um .                                                                   |
| `geo:long`               | sim     | não    | Escrito como `georss:point`um .                                                                   |
| `georss:box`             | sim     | não    | Leia como um polígono e dada uma `subType` propriedade de "Retângulo"                                |
| `georss:circle`          | sim     | sim   |                                                                                                |
| `georss:elev`            | sim     | sim   |                                                                                                |
| `georss:featurename`     | sim     | sim   |                                                                                                |
| `georss:featuretypetag`  | sim     | sim   |                                                                                                |
| `georss:floor`           | sim     | sim   |                                                                                                |
| `georss:line`            | sim     | sim   |                                                                                                |
| `georss:point`           | sim     | sim   |                                                                                                |
| `georss:polygon`         | sim     | sim   |                                                                                                |
| `georss:radius`          | sim     | sim   |                                                                                                |
| `georss:relationshiptag` | sim     | sim   |                                                                                                |
| `georss:where`           | sim     | sim   |                                                                                                |
| `geourl:latitude`        | sim     | não    | Escrito como `georss:point`um .                                                                   |
| `geourl:longitude`       | sim     | não    | Escrito como `georss:point`um .                                                                   |
| `position`               | sim     | não    | Alguns feeds XML envolverão o GML com `georss:where` uma etiqueta de posição em vez de envolvê-la com uma etiqueta. Lere-o esta tag, `georss:where` mas escreverá usando uma tag. |
| `rss`                    | sim     | não    | GeoRSS escrito em formato ATOM.                                                                 |
| `rss:author`             | sim     | parcial | Escrito como `atom:author`um.                                                                 |
| `rss:category`           | sim     | parcial | Escrito como `atom:category`um.                                                               |
| `rss:channel`            | sim     | não    |                                                                                                |
| `rss:cloud`              | sim     | não    |                                                                                                |
| `rss:comments`           | sim     | não    |                                                                                                |
| `rss:copyright`          | sim     | parcial | Escrito como `atom:rights` uma forma se `rights` `properties` não tem uma propriedade já.       |
| `rss:description`        | sim     | parcial | Escrito como `atom:content` uma forma se `content` `properties` não tem uma propriedade já.      |
| `rss:docs`               | sim     | não    |                                                                                                |
| `rss:enclosure`          | sim     | não    |                                                                                                |
| `rss:generator`          | sim     | não    |                                                                                                |
| `rss:guid`               | sim     | parcial | Escrito como `atom:id` uma forma se `id` `properties` não tem uma propriedade já.         |
| `rss:image`              | sim     | parcial | Escrito como `atom:logo` uma forma se `logo` `properties` não tem uma propriedade já.      |
| `rss:item`               | sim     | parcial | Escrito como `atom:entry`um.                                                                  |
| `rss:language`           | sim     | não    |                                                                                                |
| `rss:lastBuildDate`      | sim     | parcial | Escrito como `atom:updated` uma forma se `updated` `properties` não tem uma propriedade já.     |
| `rss:link`               | sim     | parcial | Escrito como `atom:link`um.                                                                   |
| `rss:managingEditor`     | sim     | parcial | Escrito como `atom:contributor`um.                                                            |
| `rss:pubDate`            | sim     | parcial | Escrito como `atom:published` uma forma se `published` `properties` não tem uma propriedade já.  |
| `rss:rating`             | sim     | não    |                                                                                                |
| `rss:skipDays`           | sim     | não    |                                                                                                |
| `rss:skipHours`          | sim     | não    |                                                                                                |
| `rss:source`             | sim     | parcial | Escrito como `atom:source` um `atom:link`contendo um .                                       |
| `rss:textInput`          | sim     | não    |                                                                                                |
| `rss:title`              | sim     | parcial | Escrito como `atom:title`um.                                                                  |
| `rss:ttl`                | sim     | não    |                                                                                                |
| `rss:webMaster`          | sim     | não    |                                                                                                |

### <a name="gml-elements"></a>Elementos GML

O módulo IO espacial suporta os seguintes elementos GML. 

| Nome do elemento            | Ler | Gravar | Observações                                                                                  |
|-------------------------|------|-------|----------------------------------------------------------------------------------------|
| `gml:coordinates`       | sim  | não    | Escrito `gml:posList`como .                                                              |
| `gml:curveMember`       | sim  | não    |                                                                                        |
| `gml:curveMembers`      | sim  | não    |                                                                                        |
| `gml:Box`               | sim  | não    | Escrito `gml:Envelope`como .                                                             |
| `gml:description`       | sim  | sim   |                                                                                        |
| `gml:Envelope`          | sim  | sim   |                                                                                        |
| `gml:exterior`          | sim  | sim   |                                                                                        |
| `gml:Feature`           | sim  | não    | Escrito como uma forma.                                                                    |
| `gml:FeatureCollection` | sim  | não    | Escrito como uma coleção de geometria.                                                      |
| `gml:featureMember`     | sim  | não    | Escrito como uma coleção de geometria.                                                      |
| `gml:geometry`          | sim  | não    | Escrito como uma forma.                                                                    |
| `gml:geometryMember`    | sim  | sim   |                                                                                        |
| `gml:geometryMembers`   | sim  | sim   |                                                                                        |
| `gml:identifier`        | sim  | sim   |                                                                                        |
| `gml:innerBoundaryIs`   | sim  | não    | Escrito `gml.interior`usando .                                                          |
| `gml:interior`          | sim  | sim   |                                                                                        |
| `gml:LinearRing`        | sim  | sim   |                                                                                        |
| `gml:LineString`        | sim  | sim   |                                                                                        |
| `gml:lineStringMember`  | sim  | sim   |                                                                                        |
| `gml:lineStringMembers` | sim  | não    |                                                                                        |
| `gml:MultiCurve`        | sim  | não    | Só lê `gml:LineString` membros. Escrito como`gml.MultiLineString`                  |
| `gml:MultiGeometry`     | parcial  | parcial   | Leia apenas como uma Coleção de Recursos.                                              |
| `gml:MultiLineString`   | sim  | sim   |                                                                                        |
| `gml:MultiPoint`        | sim  | sim   |                                                                                        |
| `gml:MultiPolygon`      | sim  | sim   |                                                                                        |
| `gml:MultiSurface`      | sim  | não    | Só lê `gml:Polygon` membros. Escrito como`gml.MultiPolygon`                        |
| `gml:name`              | sim  | sim   |                                                                                        |
| `gml:outerBoundaryIs`   | sim  | não    | Escrito `gml.exterior`usando .                                                          |
| `gml:Point`             | sim  | sim   |                                                                                        |
| `gml:pointMember`       | sim  | sim   |                                                                                        |
| `gml:pointMembers`      | sim  | não    |                                                                                        |
| `gml:Polygon`           | sim  | sim   |                                                                                        |
| `gml:polygonMember`     | sim  | sim   |                                                                                        |
| `gml:polygonMembers`    | sim  | não    |                                                                                        |
| `gml:pos`               | sim  | sim   |                                                                                        |
| `gml:posList`           | sim  | sim   |                                                                                        |
| `gml:surfaceMember`     | sim  | sim   |                                                                                        |

#### <a name="additional-notes"></a>notas adicionais

- Os elementos membros serão procurados por uma geometria que pode ser enterrada dentro de elementos infantis. Esta operação de pesquisa é necessária, pois muitos formatos XML que se estendem a partir de GML podem não colocar uma geometria como um filho direto de um elemento membro.
- `srsName`é parcialmente suportado para coordenadas WGS84 e os seguintes códigos:[EPSG:4326](https://epsg.io/4326)), e web Mercator ([EPSG:3857](https://epsg.io/3857) ou um de seus códigos alternativos. Qualquer outro sistema de coordenadas será analisado como WGS84 como está.
- A menos que especificado ao ler uma alimentação XML, a ordem do eixo é determinada com base em dicas na alimentação XML. Uma preferência é dada para a ordem do eixo "latitude, longitude".
- A menos que um namespace GML personalizado seja especificado para as propriedades ao escrever em um arquivo GML, informações adicionais de propriedade não serão adicionadas.

### <a name="gpx-elements"></a>Elementos GPX

O módulo IO espacial suporta os seguintes elementos GPX.

| Nome do elemento             | Ler    | Gravar   | Observações                                                                                       |
|--------------------------|---------|---------|---------------------------------------------------------------------------------------------|
| `gpx:ageofdgpsdata`      | sim     | sim     |                                                                                             |
| `gpx:author`             | sim     | sim     |                                                                                             |
| `gpx:bounds`             | sim     | sim     | Convertido em um LocationRect quando lido.                                                    |
| `gpx:cmt`                | sim     | sim     |                                                                                             |
| `gpx:copyright`          | sim     | sim     |                                                                                             |
| `gpx:desc`               | sim     | sim     | Copiado para uma propriedade de descrição quando lido para alinhar com outros formatos XML.               |
| `gpx:dgpsid`             | sim     | sim     |                                                                                             |
| `gpx:ele`                | sim     | sim     |                                                                                             |
| `gpx:extensions`         | parcial | parcial | Quando lido, as informações de estilo são extraídas. Todas as outras extensões serão achatadas em um simples objeto JSON. Apenas informações de estilo de forma são escritas. |
| `gpx:geoidheight`        | sim     | sim     |                                                                                             |
| `gpx:gpx`                | sim     | sim     |                                                                                             |
| `gpx:hdop`               | sim     | sim     |                                                                                             |
| `gpx:link`               | sim     | sim     |                                                                                             |
| `gpx:magvar`             | sim     | sim     |                                                                                             |
| `gpx:metadata`           | sim     | sim     |                                                                                             |
| `gpx:name`               | sim     | sim     |                                                                                             |
| `gpx:pdop`               | sim     | sim     |                                                                                             |
| `gpx:rte`                | sim     | sim     |                                                                                             |
| `gpx:rtept`              | sim     | sim     |                                                                                             |
| `gpx:sat`                | sim     | sim     |                                                                                             |
| `gpx:src`                | sim     | sim     |                                                                                             |
| `gpx:sym`                | sim     | sim     | O valor é capturado, mas não é usado para alterar o ícone do pushpin.                               |
| `gpx:text`               | sim     | sim     |                                                                                             |
| `gpx:time`               | sim     | sim     |                                                                                             |
| `gpx:trk`                | sim     | sim     |                                                                                             |
| `gpx:trkpt`              | sim     | sim     |                                                                                             |
| `gpx:trkseg`             | sim     | sim     |                                                                                             |
| `gpx:type`               | sim     | sim     |                                                                                             |
| `gpx:vdop`               | sim     | sim     |                                                                                             |
| `gpx:wpt`                | sim     | sim     |                                                                                             |
| `gpx_style:color`        | sim     | sim     |                                                                                             |
| `gpx_style:line`         | parcial | parcial | `color`, `opacity` `width`, `lineCap` , são apoiados.                                           |
| `gpx_style:opacity`      | sim     | sim     |                                                                                             |
| `gpx_style:width`        | sim     | sim     |                                                                                             |
| `gpxx:DisplayColor`      | sim     | não      | Usado para especificar a cor de uma forma. Ao escrever, `gpx_style:line` a cor será usada em vez disso.  |
| `gpxx:RouteExtension`    | parcial | não      | Todas as propriedades `properties`são lidas em . Somente `DisplayColor` é usado.                     |
| `gpxx:TrackExtension`    | parcial | não      | Todas as propriedades `properties`são lidas em . Somente `DisplayColor` é usado.                     |
| `gpxx:WaypointExtension` | parcial | não      | Todas as propriedades `properties`são lidas em . Somente `DisplayColor` é usado.                     |
| `gpx:keywords`           | sim     | sim     |                                                                                             |
| `gpx:fix`                | sim     | sim     |                                                                                             |

#### <a name="additional-notes"></a>notas adicionais

Ao escrever;

- Os MultiPoints serão divididos em pontos de passagem individuais.
- Polígonos e MultiPolifígos serão escritos como faixas. 
  
## <a name="supported-well-known-text-geometry-types"></a>Tipos de geometria de texto bem conhecidos suportados

| Tipo de geometria | Ler | Gravar |
|--------------|:----:|:-----:|
| Ponto | x | x |
| PONTO Z | x | x | 
| PONTO M | x | x<sup>[2]</sup> |
| PONTO ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| Linestring | x | x |
| LINHASTRING Z | x | x | 
| LINHA STRING M | x | x<sup>[2]</sup> |
| LINESTRING ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| Polígono | x | x |
| POLÍgono Z | x | x |
| POLÍGONO M | x | x<sup>[2]</sup> |
| POLÍGONO ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| Multiponto | x | x |
| MULTIPONTO Z | x | x | 
| MULTIPONTO M | x | x<sup>[2]</sup> |
| POMULTIPOINTINT ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| Multilinestring | x | x |
| MULTILINESTRING Z | x | x | 
| MULTILINESTRING M | x | x<sup>[2]</sup> |
| MULTILINESTRING ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| Multipolygon | x | x |
| MULTIPOLYGON Z | x | x | 
| MULTIPOLYGON M | x | x<sup>[2]</sup> |
| MULTIPOLYGON ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| Geometrycollection | x | x |
| GEOMETRYCOLLECTION Z | x | x | 
| GEOMETRYCOLLECTION M | x | x<sup>[2]</sup> | 
| GEOMETRYCOLLECTION ZM | x<sup>[1]</sup><sup>[2]</sup> | x | 

\[1\] Apenas o parâmetro Z é capturado e adicionado como um terceiro valor no valor de Posição.

\[O\] parâmetro de 2 M não foi capturado.

## <a name="delimited-spatial-data-support"></a>Suporte dedados espaciais delimitados

Dados espaciais delimitados, como arquivos de valor separados por vírgula (CSV), geralmente têm colunas que contêm dados espaciais. Por exemplo, pode haver colunas que contêm informações de latitude e longitude. Em formato de texto bem conhecido, pode haver uma coluna que contenha dados de geometria espacial.

### <a name="spatial-data-column-detection"></a>Detecção de colunade dados espaciais

Ao ler um arquivo delimitado que contém dados espaciais, o cabeçalho será analisado para determinar quais colunas contêm campos de localização. Se o cabeçalho contiver informações de tipo, ele será usado para lançar os valores celulares para o tipo apropriado. Se nenhum cabeçalho for especificado, a primeira linha será analisada e usada para gerar um cabeçalho. Ao analisar a primeira linha, uma verificação é executada para combinar nomes de coluna com os seguintes nomes de forma insensível ao caso. A ordem dos nomes é a prioridade, caso existam dois ou mais nomes em um arquivo.

#### <a name="latitude"></a>Latitude

- `latitude`
- `lat`
- `latdd`
- `lat_dd`
- `latitude83`
- `latdecdeg`
- `y`
- `ycenter`
- `point-y`

#### <a name="longitude"></a>Longitude

- `longitude`
- `lon`
- `lng`
- `long`
- `longdd`
- `long_dd`
- `longitude83`
- `longdecdeg`
- `x`
- `xcenter`
- `point-x`

#### <a name="elevation"></a>Elevação

- `elevation`
- `elv`
- `altitude`
- `alt`
- `z`

#### <a name="geography"></a>painel Geografia do app&#39;s selecionado

A primeira linha de dados será digitalizada para obter strings em formato de texto bem conhecido. 

### <a name="delimited-data-column-types"></a>Tipos de colunas de dados delimitados

Ao digitalizar a linha de cabeçalho, qualquer informação de tipo que esteja no nome da coluna será extraída e usada para lançar as células nessa coluna. Aqui está um exemplo de um nome de coluna que tem um valor de tipo: "ColumnName (typeName)". Os seguintes nomes de tipos insensíveis a casos são suportados:

#### <a name="numbers"></a>Números

- edm.int64
- INT
- long
- edm.double
- FLOAT
- double
- número

#### <a name="booleans"></a>Boolianos

- edm.boolean
- bool
- booleano

#### <a name="dates"></a>Datas

- edm.datetime
- date
- DATETIME

#### <a name="geography"></a>painel Geografia do app&#39;s selecionado

- edm.geografia
- geografia

#### <a name="strings"></a>Cadeias de caracteres

- edm.string
- varchar
- text
- string

Se nenhuma informação de tipo puder ser extraída do cabeçalho e a opção de digitação dinâmica for habilitada durante a leitura, cada célula será analisada individualmente para determinar qual tipo de dados é mais adequado para ser lançado.

## <a name="next-steps"></a>Próximas etapas

Consulte os artigos a seguir para obter mais exemplos de código para adicionar aos seus mapas:

> [!div class="nextstepaction"]
> [Ler e escrever dados espaciais](spatial-io-read-write-spatial-data.md)
