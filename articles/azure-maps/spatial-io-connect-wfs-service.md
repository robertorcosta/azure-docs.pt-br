---
title: Conectar-se a um serviço do serviço de recurso da Web (WFS) | Mapas do Microsoft Azure
description: Saiba como se conectar a um serviço WFS e consulte o serviço WFS usando o SDK da Web do Azure Maps e o módulo de e/s espacial.
author: farah-alyasari
ms.author: v-faalya
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 0f50fe51f9c1cdef3c3f07c91640f5b9b9616229
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78370954"
---
# <a name="connect-to-a-wfs-service"></a>Conectar-se a um serviço WFS

Um serviço de recurso da Web (WFS) é um serviço Web para consultar dados espaciais que têm uma API padronizada, que foi definida pelo Open Geospatial Consortium (OGC). A classe `WfsClient` no módulo de e/s espacial permite que os desenvolvedores se conectem a um serviço WFS e consultem dados do serviço.

Os recursos a seguir têm suporte na classe `WfsClient`:

- Versões com suporte: `1.0.0`, `1.1.0`e `2.0.0`
- Operadores de filtro com suporte: comparações binárias, lógica, matemática, valor e `bbox`.
- As solicitações são feitas usando apenas `HTTP GET`.
- Operações com suporte:

    | | |
    | :-- | :-- |
    | GetCapabilities | Gera um documento de metadados com operações e parâmetros WFS válidos |
    | GetFeature | Retorna uma seleção de recursos de uma fonte de dados |
    | DescribeFeatureType | Retorna os tipos de recurso com suporte |

## <a name="using-the-wfs-client"></a>Usando o cliente WFS

A classe `atlas.io.ogc.WfsClient` no módulo de e/s espacial torna mais fácil consultar um serviço WFS e converter as respostas em objetos geojson. Esse objeto geojson pode ser usado para outras finalidades de mapeamento.

O código a seguir consulta um serviço WFS e renderiza os recursos retornados no mapa.

<br/>

<iframe height='700' scrolling='no' title='Exemplo de WFS simples' src='//codepen.io/azuremaps/embed/MWwvVYY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte o <a href='https://codepen.io/azuremaps/pen/MWwvVYY/'>exemplo de WFS simples</a> de caneta pelo mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="supported-filters"></a>Filtros com suporte

A especificação para o padrão WFS usa filtros OGC. Os filtros a seguir têm suporte pelo cliente WFS, supondo que o serviço que está sendo chamado também ofereça suporte a esses filtros. Cadeias de caracteres de filtro personalizado podem ser passadas para a classe `CustomFilter`.

**Operadores lógicos**

- `And`
- `Or`
- `Not`

**Operadores de valor**

- `GmlObjectId`
- `ResourceId`

**Operadores matemáticos**

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

<iframe height='500' scrolling='no' title= 'Exemplos de filtros WFS' src='//codepen.io/azuremaps/embed/NWqvYrV/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte os <a href='https://codepen.io/azuremaps/pen/NWqvYrV/'>exemplos de filtro WFS</a> de caneta pelo mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="wfs-service-explorer"></a>Gerenciador de serviços do WFS

O código a seguir usa o cliente WFS para explorar os serviços do WFS. Selecione uma camada de tipo de propriedade dentro do serviço e veja a legenda associada.

<br/>

<iframe height='700' scrolling='no' title= 'Gerenciador de serviços do WFS' src='//codepen.io/azuremaps/embed/bGdrvmG/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte o <a href='https://codepen.io/azuremaps/pen/bGdrvmG/'>Gerenciador de serviços de WFS</a> de caneta pelo Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

Você também pode usar um serviço de proxy para carregar recursos que são hospedados em domínios que não estão habilitados para CORs. Primeiro, você deve definir uma variável para conter a URL do serviço de proxy e definir a opção `proxyService` para o cliente WFS. Para renderizar uma opção de serviço de proxy para o usuário, adicione uma entrada de usuário à interface. Carregue a URL do serviço quando a entrada for clicada. Os trechos de código a seguir mostram como usar o serviço de proxy.

```JavaScript

//A variable to hold the URL of the proxy service
var proxyServiceUrl = window.location.origin + 'CorsEnabledProxyService.ashx?url=';

//Create the WFS client to access the service and use the proxy service settings
client = new atlas.io.ogc.WfsClient({
    url: url,
    proxyService: (document.getElementById('useProxyService').checked) ? proxyServiceUrl : null
});

function proxyOptionChanged() {
    if (currentServiceUrl) {
        loadClient(currentServiceUrl);
    }
}

```

O trecho de código HTML abaixo corresponde ao código JavaScript acima:

```html
<!-- use the proxy service -->
<input id="useProxyService" type="checkbox" onclick="proxyOptionChanged()"/>
```

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [WfsClient](https://docs.microsoft.com/JavaScript/api/azure-maps-spatial-io/atlas.io.ogc.wfsclient)

> [!div class="nextstepaction"]
> [WfsServiceOptions](https://docs.microsoft.com/JavaScript/api/azure-maps-spatial-io/atlas.wfsserviceoptions)

Consulte os artigos a seguir para obter mais exemplos de código para adicionar aos seus mapas:

> [!div class="nextstepaction"]
> [Aproveitar as principais operações](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Detalhes de formato de dados com suporte](spatial-io-supported-data-format-details.md)
