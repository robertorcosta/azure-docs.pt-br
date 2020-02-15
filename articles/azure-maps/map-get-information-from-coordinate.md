---
title: Mostrar informações sobre uma coordenada em um mapa | Mapas do Microsoft Azure
description: Saiba como exibir informações sobre um endereço no mapa quando um usuário seleciona uma coordenada.
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 1a6b3b4665e6141fb4c95508a8d8405268de6d19
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77208515"
---
# <a name="get-information-from-a-coordinate"></a>Obter informações de uma coordenada

Este artigo mostra como fazer uma pesquisa inversa de endereço que mostra o endereço de um local de pop-up clicado.

Há duas maneiras de fazer uma pesquisa inversa de endereço. Uma delas é a consulta da [API de Pesquisa do Endereço Reverso do Azure Mapas](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) por meio de um módulo de serviço. A outra maneira é usar a [API de busca](https://fetch.spec.whatwg.org/) para fazer uma solicitação para a [API de pesquisa de endereço reverso do Azure Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) para localizar um endereço. As duas formas são pesquisadas abaixo.

## <a name="make-a-reverse-search-request-via-service-module"></a>Fazer uma solicitação de pesquisa reversa por meio do módulo de serviço

<iframe height='500' scrolling='no' title='Obter informações de uma coordenada (Módulo de Serviço)' src='//codepen.io/azuremaps/embed/ejEYMZ/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/ejEYMZ/'>Obter informações de uma coordenada (Módulo de Serviço)</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco constrói um objeto de mapa e define o mecanismo de autenticação para usar o token de acesso. Você pode ver [criar um mapa](./map-create.md) para obter instruções.

O segundo bloco de código cria um `TokenCredential` para autenticar solicitações HTTP para mapas do Azure com o token de acesso. Em seguida, ele passa o `TokenCredential` para `atlas.service.MapsURL.newPipeline()` e cria uma instância de [pipeline](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) . O `searchURL` representa uma URL para as operações de [pesquisa](https://docs.microsoft.com/rest/api/maps/search) do Azure Mapas.

O terceiro bloco de código atualiza o estilo do cursor do mouse para um ponteiro e cria um objeto [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) . Você pode ver [adicionar um popup no mapa](./map-add-popup.md) para obter instruções.

O quarto bloco de código adiciona um [ouvinte de eventos](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)de clique do mouse. Quando disparado, ele cria uma consulta de pesquisa com as coordenadas do ponto clicado. Em seguida, ele usa o método [getSearchAddressReverse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest#searchaddressreverse-aborter--geojson-position--searchaddressreverseoptions-)para consultar a [API de obtenção de endereço de pesquisa reversa](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) para o endereço das coordenadas. Uma coleção de recursos geojson é extraída usando o método `geojson.getFeatures()` da resposta.

O quinto bloco de código configura o conteúdo do pop-up HTML para exibir o endereço de resposta para a posição da coordenada clicada.

A alteração de cursor, o objeto popup e o evento Click são todos criados no [ouvinte de eventos Load](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)do mapa. Essa estrutura de código garante que o MAP seja totalmente carregado antes de recuperar as informações de coordenadas.

## <a name="make-a-reverse-search-request-via-fetch-api"></a>Fazer uma solicitação de pesquisa inversa por meio da API de busca

Clique no mapa para fazer uma solicitação de geocódigo inversa para esse local usando Fetch.

<iframe height='500' scrolling='no' title='Obter informações de uma coordenada' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte as informações de Pen <a href='https://codepen.io/azuremaps/pen/ddXzoB/'>Get de uma coordenada </a> pelo Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco de código constrói um objeto de mapa e define o mecanismo de autenticação para usar o token de acesso. Você pode ver [criar um mapa](./map-create.md) para obter instruções.

O segundo bloco de código atualiza o estilo do cursor do mouse para um ponteiro. Ele instancia um objeto [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) . Você pode ver [adicionar um popup no mapa](./map-add-popup.md) para obter instruções.

O terceiro bloco de código adiciona um ouvinte de evento para cliques do mouse. Após um clique do mouse, ele usa a [API de busca](https://fetch.spec.whatwg.org/) para consultar a API de pesquisa de [endereço reverso do Azure Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) para o endereço de coordenadas clicado. Para uma resposta bem-sucedida, ele coleta o endereço para o local clicado. Ele define o conteúdo e a posição do Popup usando a função [SetOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) da classe Popup.

A alteração de cursor, o objeto popup e o evento Click são todos criados no [ouvinte de eventos Load](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)do mapa. Essa estrutura de código garante que o mapa seja totalmente carregado antes de recuperar as informações de coordenadas.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Pop-up](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

Consulte os seguintes artigos para obter exemplos de código completo:

> [!div class="nextstepaction"]
> [Mostrar trajetos de A para B](./map-route.md)

> [!div class="nextstepaction"]
> [Mostrar tráfego](./map-show-traffic.md)
