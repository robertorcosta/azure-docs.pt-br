---
title: Conecte-se a um serviço de Serviço de Recursos Web (WFS) | Mapas do Microsoft Azure
description: Aprenda a se conectar a um serviço WFS e consulte o serviço WFS usando o Web SDK do Azure Maps e o módulo IO espacial.
author: philmea
ms.author: philmea
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 8b511395eb61e8845aaa11e5ca7a490dc461424d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334211"
---
# <a name="connect-to-a-wfs-service"></a>Conecte-se a um serviço WFS

Um Web Feature Service (WFS) é um serviço web para consulta de dados espaciais que tem uma API padronizada que é definida pelo Open Geospatial Consortium (OGC). A `WfsClient` classe no módulo IO espacial permite que os desenvolvedores se conectem a um serviço WFS e consultam dados do serviço.

Os seguintes recursos são `WfsClient` suportados pela classe:

- Versões `1.0.0`suportadas: , `1.1.0`e`2.0.0`
- Operadores de filtro suportados: comparações binárias, lógica, matemática, valor e `bbox`.
- As solicitações `HTTP GET` são feitas apenas usando.
- Operações suportadas:

    | | |
    | :-- | :-- |
    | Getcapabilities | Gera um documento de metadados com operações e parâmetros WFS válidos |
    | GetFeature | Retorna uma seleção de recursos de uma fonte de dados |
    | DescreverFeatureType | Retorna os tipos de recursos suportados |

## <a name="using-the-wfs-client"></a>Usando o cliente WFS

A `atlas.io.ogc.WfsClient` classe no módulo IO espacial facilita a consulta de um serviço WFS e converte as respostas em objetos GeoJSON. Este objeto GeoJSON pode então ser usado para outros fins de mapeamento.

O código a seguir consulta um serviço WFS e renderiza os recursos retornados no mapa.

<br/>

<iframe height='700' scrolling='no' title='Exemplo simples do WFS' src='//codepen.io/azuremaps/embed/MWwvVYY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja o exemplo do Pen <a href='https://codepen.io/azuremaps/pen/MWwvVYY/'>Simple WFS</a> do Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="supported-filters"></a>Filtros com suporte

A especificação para o padrão WFS faz uso de filtros OGC. Os filtros abaixo são suportados pelo cliente WFS, assumindo que o serviço que está sendo chamado também suporta esses filtros. As seqüências de `CustomFilter` filtro personalizadas podem ser passadas para a classe.

**Operadores lógicos**

- `And`
- `Or`
- `Not`

**Operadores de valor**

- `GmlObjectId`
- `ResourceId`

**Operadores de matemática**

- `Add`
- `Sub`
- `Mul`
- `Div`

**Operadores de comparação**

- `PropertyIsEqualTo`
- `PropertyIsNotEqualTo`
- `PropertyIsLessThan`
- `PropertyIsGreaterThan`
- `PropertyIsLessThanOrEqualTo`
- `PropertyIsGreaterThanOrEqualTo`
- `PropertyIsLike`
- `PropertyIsNull`
- `PropertyIsNil`
- `PropertyIsBetween`

O código a seguir demonstra o uso de diferentes filtros com o cliente WFS.

<br/>

<iframe height='500' scrolling='no' title= 'Exemplos de filtros WFS' src='//codepen.io/azuremaps/embed/NWqvYrV/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja os exemplos do filtro Pen <a href='https://codepen.io/azuremaps/pen/NWqvYrV/'>WFS</a> do Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="wfs-service-explorer"></a>Explorador de serviços WFS

O código a seguir usa o cliente WFS para explorar os serviços WFS. Selecione uma camada de tipo de propriedade dentro do serviço e veja a legenda associada.

<br/>

<iframe height='700' style='width: 100%;' scrolling='no' title= 'Explorador de serviços WFS' src='//codepen.io/azuremaps/embed/bGdrvmG/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte o <a href='https://codepen.io/azuremaps/pen/bGdrvmG/'>explorador de serviço SInS</a> da Caneta no<a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'>CodePen</a>( )
</iframe>

Para acessar os serviços WFS hospedados em pontos finais não habilitados para CORS, um serviço proxy habilitado para CORS pode ser passado para a `proxyService` opção do cliente WFS, conforme mostrado abaixo. 

```JavaScript
//Create the WFS client to access the service and use the proxy service settings
client = new atlas.io.ogc.WfsClient({
    url: url,
    proxyService: window.location.origin + '/YourCorsEnabledProxyService.ashx?url='
});
```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [WfsClient](https://docs.microsoft.com/JavaScript/api/azure-maps-spatial-io/atlas.io.ogc.wfsclient)

> [!div class="nextstepaction"]
> [Opções de serviço do Wfs](https://docs.microsoft.com/JavaScript/api/azure-maps-spatial-io/atlas.wfsserviceoptions)

Consulte os artigos a seguir para obter mais exemplos de código para adicionar aos seus mapas:

> [!div class="nextstepaction"]
> [Alavancar operações principais](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Detalhes do formato de dados suportados](spatial-io-supported-data-format-details.md)
