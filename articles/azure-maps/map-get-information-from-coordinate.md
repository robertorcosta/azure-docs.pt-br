---
title: Mostrar informações sobre uma coordenada em um mapa | Mapas do Microsoft Azure
description: Saiba como exibir informações sobre um endereço no mapa quando um usuário selecionar uma coordenada.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 174bdc496e52a6ac8f2a2d631db92e0f21a819be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371431"
---
# <a name="get-information-from-a-coordinate"></a>Obter informações de uma coordenada

Este artigo mostra como fazer uma pesquisa inversa de endereço que mostra o endereço de um local de pop-up clicado.

Há duas maneiras de fazer uma pesquisa inversa de endereço. Uma delas é a consulta da [API de Pesquisa do Endereço Reverso do Azure Mapas](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) por meio de um módulo de serviço. A outra maneira é usar a [API Fetch](https://fetch.spec.whatwg.org/) para fazer uma solicitação à [API de pesquisa de endereçoreverso reverso do Azure Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) para encontrar um endereço. As duas formas são pesquisadas abaixo.

## <a name="make-a-reverse-search-request-via-service-module"></a>Fazer uma solicitação de pesquisa reversa por meio do módulo de serviço

<iframe height='500' scrolling='no' title='Obter informações de uma coordenada (Módulo de Serviço)' src='//codepen.io/azuremaps/embed/ejEYMZ/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/ejEYMZ/'>Obter informações de uma coordenada (Módulo de Serviço)</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco constrói um objeto de mapa e define o mecanismo de autenticação para usar o token de acesso. Você pode ver [criar um mapa](./map-create.md) para obter instruções.

O segundo bloco `TokenCredential` de código cria um para autenticar solicitações HTTP ao Azure Maps com o token de acesso. Em seguida, `TokenCredential` `atlas.service.MapsURL.newPipeline()` passa o para e cria uma instância [pipeline.](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) O `searchURL` representa uma URL para as operações de [pesquisa](https://docs.microsoft.com/rest/api/maps/search) do Azure Mapas.

O terceiro bloco de código atualiza o estilo do cursor do mouse para um ponteiro e cria um objeto [pop-up.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) Você pode ver [adicionar um popup no mapa](./map-add-popup.md) para obter instruções.

O quarto bloco de código adiciona um ouvinte de [evento](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)do mouse click . Quando acionado, ele cria uma consulta de pesquisa com as coordenadas do ponto clicado. Em seguida, ele usa o método [getSearchAddressReverse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest#searchaddressreverse-aborter--geojson-position--searchaddressreverseoptions-)para consultar a [API inverter](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) o endereço de pesquisa para o endereço das coordenadas. Uma coleção de recursos GeoJSON `geojson.getFeatures()` é então extraída usando o método a partir da resposta.

O quinto bloco de código configura o conteúdo pop-up HTML para exibir o endereço de resposta para a posição de coordenada clicada.

A mudança do cursor, o objeto pop-up e o evento de clique são todos criados no ouvinte de evento de carga do [mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events). Esta estrutura de código garante que o mapa carregue totalmente antes de recuperar as informações das coordenadas.

## <a name="make-a-reverse-search-request-via-fetch-api"></a>Faça uma solicitação de pesquisa reversa via API Fetch

Clique no mapa para fazer uma solicitação de geocódigo reverso para esse local usando fetch.

<iframe height='500' scrolling='no' title='Obter informações de uma coordenada' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte as informações de Pen <a href='https://codepen.io/azuremaps/pen/ddXzoB/'>Get de uma coordenada </a> pelo Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco de código constrói um objeto de mapa e define o mecanismo de autenticação para usar o token de acesso. Você pode ver [criar um mapa](./map-create.md) para obter instruções.

O segundo bloco de código atualiza o estilo do cursor do mouse para um ponteiro. Ele instancia um objeto [pop-up.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) Você pode ver [adicionar um popup no mapa](./map-add-popup.md) para obter instruções.

O terceiro bloco de código adiciona um ouvinte de evento para cliques do mouse. Após um clique no mouse, ele usa a [API Fetch](https://fetch.spec.whatwg.org/) para consultar a [API de pesquisa de endereço reverso do Azure Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) para o endereço de coordenadas clicado. Para uma resposta bem-sucedida, ele coleta o endereço para o local clicado. Ele define o conteúdo pop-up e a posição usando a função [setOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) da classe popup.

A mudança do cursor, o objeto pop-up e o evento de clique são todos criados no ouvinte de evento de carga do [mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events). Esta estrutura de código garante que o mapa seja totalmente carregado antes de recuperar as informações das coordenadas.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Melhores práticas para usar o serviço de pesquisa](how-to-use-best-practices-for-search.md)

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Pop-up](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

Consulte os seguintes artigos para obter exemplos de código completo:

> [!div class="nextstepaction"]
> [Mostrar direções de A para B](./map-route.md)

> [!div class="nextstepaction"]
> [Mostrar o tráfego](./map-show-traffic.md)
