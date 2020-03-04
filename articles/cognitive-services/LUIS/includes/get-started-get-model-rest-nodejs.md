---
title: Obter o modelo com chamadas REST em C#
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/31/2020
ms.author: diberry
ms.openlocfilehash: bbb2ae0b10af795d71f0a78c045bec0c216ee378
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/15/2020
ms.locfileid: "77368435"
---
## <a name="prerequisites"></a>Prerequisites

* Reconhecimento vocal do Azure – Criar chave de 32 caracteres do recurso e a URL de ponto de extremidade. Crie com o [portal do Azure](../luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) ou com a [CLI do Azure](../luis-how-to-azure-subscription.md#create-resources-in-azure-cli).
* Importe o aplicativo [TravelAgent](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/change-model/TravelAgent.json) do repositório GitHub cognitive-services-language-understanding.
* A ID do aplicativo LUIS para o aplicativo TravelAgent importado. A ID do aplicativo é mostrada no painel do aplicativo.
* A ID da versão no aplicativo que recebe os enunciados. A ID padrão é “0.1”.
* Linguagem de programação [Node.js](https://nodejs.org/)
* [Visual Studio Code](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>Exemplo de arquivo JSON de enunciados

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]


## <a name="change-model-programmatically"></a>Alterar o modelo de maneira programática

1. Crie um arquivo chamado `model.js`. Adicione os códigos a seguir:

    ```javascript
    var request = require('request');
    var requestpromise = require('request-promise');

    // 32 character key value
    const LUIS_authoringKey = "YOUR-KEY";

    // endpoint example: your-resource-name.api.cognitive.microsoft.com
    const LUIS_endpoint = "YOUR-ENDPOINT";
    const LUIS_appId = "YOUR-APP-ID";
    const LUIS_versionId = "0.1";
    const addUtterancesURI = `https://${LUIS_endpoint}/luis/authoring/v3.0-preview/apps/${LUIS_appId}/versions/${LUIS_versionId}/examples`;
    const addTrainURI = `https://${LUIS_endpoint}/luis/authoring/v3.0-preview/apps/${LUIS_appId}/versions/${LUIS_versionId}/train`;

    const utterances = [
            {
              'text': 'go to Seattle today',
              'intentName': 'BookFlight',
              'entityLabels': [
                {
                  'entityName': 'Location::LocationTo',
                  'startCharIndex': 6,
                  'endCharIndex': 12
                }
              ]
            },
            {
                'text': 'a barking dog is annoying',
                'intentName': 'None',
                'entityLabels': []
            }
          ];

    const main = async() =>{


        await addUtterance();
        await train("POST");
        await trainStatus("GET");

    }
    const addUtterance = async () => {

        const options = {
            uri: addUtterancesURI,
            method: 'POST',
            headers: {
                'Ocp-Apim-Subscription-Key': LUIS_authoringKey
            },
            json: true,
            body: utterances
        };

        const response = await requestpromise(options)
        console.log(response.body);
    }
    const train = async (verb) => {

        const options = {
            uri: addTrainURI,
            method: verb,
            headers: {
                'Ocp-Apim-Subscription-Key': LUIS_authoringKey
            },
            json: true,
            body: null // The body can be empty for a training request
        };

        const response = await requestpromise(options)
        console.log(response.body);
    }

    // MAIN
    main().then(() => console.log("done")).catch((err)=> console.log(err returned));
    ```

1. Substitua os valores que começam com `YOUR-` por seus valores.

    |Informações|Finalidade|
    |--|--|
    |`YOUR-KEY`|Sua chave de criação de 32 caracteres.|
    |`YOUR-ENDPOINT`| Seu ponto de extremidade da URL de criação. Por exemplo, `replace-with-your-resource-name.api.cognitive.microsoft.com`. Você definiu o nome do recurso quando você criou o recurso.|
    |`YOUR-APP-ID`| Sua ID do aplicativo LUIS. |

    As chaves e os recursos atribuídos estão visíveis no portal do LUIS na seção Gerenciar, na página **recursos do Azure**. A ID do aplicativo está disponível na mesma seção Gerenciar, na página **Configurações do Aplicativo**.

1. Com um prompt de comando no mesmo diretório em que você criou o arquivo, insira o seguinte comando para executar o arquivo:

    ```console
    node model.js
    ```

## <a name="clean-up-resources"></a>Limpar os recursos

Ao concluir este guia de início rápido, exclua o arquivo do sistema de arquivos.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Práticas recomendadas para um aplicativo](../luis-concept-best-practices.md)