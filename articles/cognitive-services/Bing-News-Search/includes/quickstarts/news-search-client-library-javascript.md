---
title: Guia de início rápido da biblioteca de clientes JavaScript da Pesquisa de Notícias do Bing
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/12/2020
ms.author: aahi
ms.openlocfilehash: 858e6b9e0e40ab988a4cdf04b31580c1ca28d40a
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "79503522"
---
Use este guia de início rápido para começar a pesquisar notícias com a biblioteca de clientes da Pesquisa de Notícias do Bing para o JavaScript. Embora a Pesquisa de Notícias do Bing tenha uma API REST compatível com a maioria das linguagens de programação, a biblioteca de clientes é uma forma fácil de integrar o serviço aos seus aplicativos. O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/newsSearch.js).

## <a name="prerequisites"></a>Pré-requisitos

* [Node.js](https://nodejs.org/en/)

Para configurar um aplicativo de console usando a biblioteca de clientes da Pesquisa de Notícias do Bing:
1. Execute `npm install ms-rest-azure` no seu ambiente de desenvolvimento.
2. Execute `npm install azure-cognitiveservices-newssearch` no seu ambiente de desenvolvimento.


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](~/includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Criar e inicializar o aplicativo

1. Crie uma instância de `CognitiveServicesCredentials`. Crie variáveis para a chave da assinatura e um termo de pesquisa.

    ```javascript
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let search_term = 'Winter Olympics'
    ```

2. crie uma instância do cliente:
    
    ```javascript
    const NewsSearchAPIClient = require('azure-cognitiveservices-newssearch');
    let client = new NewsSearchAPIClient(credentials);
    ```

## <a name="send-a-search-query"></a>Enviar uma consulta de pesquisa

1. Use o cliente para pesquisar com um texto de consulta, nesse caso, "Olimpíadas de Inverno":
    
    ```javascript
    client.newsOperations.search(search_term).then((result) => {
        console.log(result.value);
    }).catch((err) => {
        throw err;
    });
    ```

O código imprime `result.value` itens para o console sem analisar qualquer texto. Os resultados por categoria, se houver, incluirão:

- `_type: 'NewsArticle'`
- `_type: 'WebPage'`
- `_type: 'VideoObject'`
- `_type: 'ImageObject'`

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar um aplicativo Web de página única](../../tutorial-bing-news-search-single-page-app.md)
