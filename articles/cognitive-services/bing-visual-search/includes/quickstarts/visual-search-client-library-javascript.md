---
title: Início rápido da biblioteca de clientes JavaScript da Pesquisa Visual do Bing
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/26/2020
ms.author: aahi
ms.openlocfilehash: 404529bf269f899603b92c6c23b0d95cd2749ee5
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80573125"
---
Use este início rápido para começar a obter insights de imagens do serviço de Pesquisa Visual do Bing, usando a biblioteca de clientes JavaScript. Embora a Pesquisa Visual do Bing tenha uma API REST compatível com a maioria das linguagens de programação, a biblioteca de clientes oferece uma forma fácil de integrar o serviço aos seus aplicativos. O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/visualSearch.js). 

[Documentação de referência](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-visualsearch/?view=azure-node-latest) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-visualsearch) | [Pacote (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-visualsearch) | [Exemplos](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/)

## <a name="prerequisites"></a>Pré-requisitos
* [Node.js](https://www.nodejs.org/)
* A biblioteca de clientes da Pesquisa Visual do Bing para JavaScript
    * Para configurar um aplicativo de console usando a biblioteca de clientes da Pesquisa Visual do Bing, execute os seguintes comandos:
        1. `npm install ms-rest-azure`
        2. `npm install azure-cognitiveservices-visualsearch`.


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](~/includes/cognitive-services-bing-visual-search-signup-requirements.md)]

<a name="client"></a>

## <a name="create-and-initialize-the-application"></a>Criar e inicializar o aplicativo

1. Crie um novo arquivo JavaScript em seu IDE ou editor favorito e adicione os seguintes requisitos. Em seguida, crie variáveis para sua chave de assinatura, uma ID de configuração personalizada e o caminho do arquivo para a imagem que você deseja carregar. 

    ```javascript
    const os = require("os");
    const async = require('async');
    const fs = require('fs');
    const Search = require('azure-cognitiveservices-visualsearch');
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    
    let keyVar = 'YOUR-VISUAL-SEARCH-ACCESS-KEY';
    let credentials = new CognitiveServicesCredentials(keyVar);
    let filePath = "../Data/image.jpg";
    ```

2. Instancie o cliente.

    ```javascript
    let visualSearchClient = new Search.VisualSearchClient(credentials);
    ```

## <a name="search-for-images"></a>Pesquisar imagens

1. Use `fs.createReadStream()` para ler em seu arquivo de imagem e crie variáveis para sua solicitação de pesquisa e resultados. Em seguida, use o cliente para pesquisar imagens.

    ```javascript
    let fileStream = fs.createReadStream(filePath);
    let visualSearchRequest = JSON.stringify({});
    let visualSearchResults;
    try {
        visualSearchResults = await visualSearchClient.images.visualSearch({
            image: fileStream,
            knowledgeRequest: visualSearchRequest
        });
        console.log("Search visual search request with binary of image");
    } catch (err) {
        console.log("Encountered exception. " + err.message);
    }
    ```

2. Analise os resultados das consultas anteriores:

    ```javascript
    // Visual Search results
    if (visualSearchResults.image.imageInsightsToken) {
        console.log(`Uploaded image insights token: ${visualSearchResults.image.imageInsightsToken}`);
    }
    else {
        console.log("Couldn't find image insights token!");
    }
    
    // List of tags
    if (visualSearchResults.tags.length > 0) {
        let firstTagResult = visualSearchResults.tags[0];
        console.log(`Visual search tag count: ${visualSearchResults.tags.length}`);
    
        // List of actions in first tag
        if (firstTagResult.actions.length > 0) {
            let firstActionResult = firstTagResult.actions[0];
            console.log(`First tag action count: ${firstTagResult.actions.length}`);
            console.log(`First tag action type: ${firstActionResult.actionType}`);
        }
        else {
            console.log("Couldn't find tag actions!");
        }
    
    }
    else {
        console.log("Couldn't find image tags!");
    }
    
    ```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar um aplicativo Web de página única](../../tutorial-bing-visual-search-single-page-app.md)
