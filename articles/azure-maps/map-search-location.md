---
title: Mostrar resultados da pesquisa em um mapa | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá a executar uma solicitação de pesquisa usando o SDK da Web do Microsoft Azure Maps e exibirá os resultados no mapa.
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 13daf20e364b1e629b3ae13be2c9406da3702211
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209640"
---
# <a name="show-search-results-on-the-map"></a>Mostrar resultados da pesquisa no mapa

Este artigo mostra como fazer uma pesquisa para localização de interesse e mostrar os resultados da pesquisa no mapa.

Há duas maneiras de procurar um local de interesse. Uma maneira é usar um módulo de serviço para fazer uma solicitação de pesquisa. A outra maneira é fazer uma solicitação de pesquisa para a [API de pesquisa difusa do Azure Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) por meio da [API de busca](https://fetch.spec.whatwg.org/). As duas formas são discutidas abaixo.

## <a name="make-a-search-request-via-service-module"></a>Fazer uma solicitação de pesquisa por meio do módulo de serviço

<iframe height='500' scrolling='no' title='Mostrar resultados da pesquisa no mapa (módulo de serviço)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja a Caneta <a href='https://codepen.io/azuremaps/pen/zLdYEB/'>Mostrar resultados de pesquisa em um mapa (módulo de serviço)</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco constrói um objeto de mapa e define o mecanismo de autenticação para usar o token de acesso. Você pode ver [criar um mapa](./map-create.md) para obter instruções.

O segundo bloco de código cria uma `TokenCredential` para autenticar solicitações HTTP para mapas do Azure com o token de acesso. Em seguida, ele passa o `TokenCredential` para `atlas.service.MapsURL.newPipeline()` e cria uma instância de [pipeline](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) . O `searchURL` representa uma URL para as operações de [pesquisa](https://docs.microsoft.com/rest/api/maps/search) do Azure Mapas.

O terceiro bloco de código cria um objeto de fonte de dados usando a classe [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) e adiciona resultados de pesquisa a ele. Uma [camada de símbolo](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) usa texto ou ícones para renderizar dados com base em ponto encapsulados na [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) como símbolos no mapa.  Em seguida, uma camada de símbolo é criada. A fonte de dados é adicionada à camada de símbolo, que é então adicionada ao mapa.

O quarto bloco de código usa o método [SearchFuzzy](/javascript/api/azure-maps-rest/atlas.service.models.searchgetsearchfuzzyoptionalparams) no [módulo de serviço](how-to-use-services-module.md). Ele permite que você execute uma pesquisa de texto de forma livre por meio da [API REST de pesquisa difusa](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) para pesquisar o ponto de interesse. Solicitações GET para a API difusa de pesquisa podem lidar com qualquer combinação de entradas difusas. A coleção do recurso GeoJSON da resposta é então extraída usando o método `geojson.getFeatures()` e adicionado à fonte de dados, que automaticamente faz com que os dados sejam renderizados no mapa através da camada do símbolo.

O último bloco de código ajusta os limites da câmera para o mapa usando a propriedade [setcamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) do mapa.

Os limites de solicitação de pesquisa, fonte de dados, camada de símbolo e câmera estão dentro do [ouvinte de eventos](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) do mapa. Queremos garantir que os resultados sejam exibidos depois que o mapa for totalmente carregado.


## <a name="make-a-search-request-via-fetch-api"></a>Fazer uma solicitação de pesquisa por meio da API de busca

<iframe height='500' scrolling='no' title='Mostrar resultados da pesquisa no mapa' src='//codepen.io/azuremaps/embed/KQbaeM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>Mostrar resultados em um mapa</a> pelo mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco do código constrói um objeto de mapa. Ele define o mecanismo de autenticação para usar o token de acesso. Você pode ver [criar um mapa](./map-create.md) para obter instruções.

O segundo bloco de código cria uma URL para a qual fazer uma solicitação de pesquisa. Ele também cria duas matrizes para armazenar limites e Pins para os resultados da pesquisa.

O terceiro bloco de código usa a [API de busca](https://fetch.spec.whatwg.org/). A [API de busca](https://fetch.spec.whatwg.org/) é usada para fazer uma solicitação para a [API de pesquisa difusa do Azure Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) para pesquisar os pontos de interesse. A API de pesquisa difusa pode manipular qualquer combinação de entradas difusas. Em seguida, ele manipula e analisa a resposta de pesquisa e adiciona os Pins de resultado à matriz searchPins.

O quarto bloco de código cria um objeto de fonte de dados usando a classe [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) . No código, adicionamos os resultados da pesquisa ao objeto de origem. Uma [camada de símbolo](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) usa texto ou ícones para renderizar dados com base em ponto encapsulados na [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) como símbolos no mapa. Em seguida, uma camada de símbolo é criada. A fonte de dados é adicionada à camada de símbolo, que é então adicionada ao mapa.

O último bloco de código cria um objeto [BoundingBox](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.boundingbox?view=azure-iot-typescript-latest) . Ele usa a matriz de resultados e, em seguida, ajusta os limites da câmera para o mapa usando a [microcâmera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-)do mapa. Em seguida, ele renderiza os Pins de resultado.

A solicitação de pesquisa, a fonte de dados, a camada de símbolo e os limites da câmera são definidos dentro do [ouvinte de eventos](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) do mapa para garantir que os resultados sejam exibidos depois que o mapa for totalmente carregado.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a **Pesquisa Difusa**:

> [!div class="nextstepaction"]
> [API de pesquisa difusa do Mapas do Azure](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Consulte os seguintes artigos para obter exemplos de código completo:

> [!div class="nextstepaction"]
> [Obter informações de uma coordenada](./map-get-information-from-coordinate.md)
<!-- Comment added to suppress false positive warning -->
> [!div class="nextstepaction"]
> [Mostrar trajetos de A para B](./map-route.md)
