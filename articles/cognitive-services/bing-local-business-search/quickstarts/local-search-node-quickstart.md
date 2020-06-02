---
title: Início Rápido – enviar uma consulta à API usando o Node.js – Pesquisa de Negócios Locais do Bing
titleSuffix: Azure Cognitive Services
description: Use este início rápido para começar a enviar solicitações para a API de Pesquisa do Bing Local Business, que é um Serviço Cognitivo do Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: quickstart
ms.date: 05/12/2020
ms.author: aahi
ms.openlocfilehash: 3bb31c36e8c614a72b86f95cb7e7d1c588692f97
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/27/2020
ms.locfileid: "83873071"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-using-nodejs"></a>Início Rápido: envie uma consulta para a API de Pesquisa do Bing Local Business usando o Node.js

Use este início rápido para aprender a enviar solicitações para a API de Pesquisa de Negócios Locais do Bing, que é um Serviço Cognitivo do Azure. Embora esse aplicativo simples seja escrito em Node.js, a API é um serviço Web RESTful compatível com qualquer linguagem de programação capaz de fazer solicitações HTTP e analisar JSON.

Este aplicativo de exemplo obtém dados de resposta local da API para uma consulta de pesquisa.

## <a name="prerequisites"></a>Pré-requisitos

* A versão mais recente do [Node.js](https://nodejs.org/en/download/).
* A [Biblioteca de Solicitações JavaScript](https://github.com/request/request).
* Uma [conta de API dos Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com APIs de Pesquisa do Bing. Para este início rápido, a [avaliação gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) é suficiente. Salve a chave de API fornecida quando você ativa sua avaliação gratuita. Para obter mais informações, confira [Preço dos Serviços Cognitivos – API de Pesquisa do Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).


## <a name="code-scenario"></a>Cenário do código

O seguinte código define e envia a solicitação, que é implementada nas seguintes etapas:

1. Declare variáveis para especificar o ponto de extremidade por host e caminho.
2. Especifique a consulta e adicione o parâmetro de consulta.
3. Crie uma função de manipulador para a resposta.
4. Defina a função Search que cria a solicitação e adiciona o cabeçalho `Ocp-Apim-Subscription-Key`.
5. Execute a função Search.


```javascript
'use strict';

let https = require('https');

// Replace the subscriptionKey string value with your valid subscription key.
let subscriptionKey = 'your-access-key';

let host = 'api.cognitive.microsoft.com/bing';
let path = '/v7.0/localbusinesses/search';

let mkt = 'en-US';
let q = 'hotel in Bellevue';

let params = '?q=' + encodeURI(q) + "&mkt=" + mkt;

let response_handler = function (response) {
    let body = '';
    response.on('data', function (d) {
        body += d;
    });
    response.on('end', function () {
        let body_ = JSON.parse(body);
        let body__ = JSON.stringify(body_, null, '  ');
        console.log(body__);
    });
    response.on('error', function (e) {
        console.log('Error: ' + e.message);
    });
};

let Search = function () {
    let request_params = {
        method: 'GET',
        hostname: host,
        path: path + params,
        headers: {
            'Ocp-Apim-Subscription-Key': subscriptionKey,
        }
    };

    let req = https.request(request_params, response_handler);
    req.end();
}

Search();

```

## <a name="next-steps"></a>Próximas etapas

* [Início rápido do C# de Pesquisa de negócios local](local-quickstart.md)
* [Início Rápido da Pesquisa Java em empresas locais](local-search-java-quickstart.md)
* [Início Rápido do Python em Pesquisa de empresa local](local-search-python-quickstart.md)
