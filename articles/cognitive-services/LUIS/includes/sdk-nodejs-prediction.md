---
title: incluir arquivo
description: incluir arquivo
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 01/23/2020
ms.author: diberry
ms.openlocfilehash: 0539f4a8080056f96319a7a75a355782ee80018d
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2020
ms.locfileid: "76772369"
---
Use a biblioteca de clientes de runtime do LUIS (Reconhecimento vocal) para Node.js com o objetivo de:

* Previsão por slot
* Previsão por versão

[Documentação de referência](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/?view=azure-node-latest) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-luis-runtime) | [Pacote de Runtime (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime) | [Exemplos](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/LUIS/luis_prediction.js)

## <a name="prerequisites"></a>Prerequisites

* Recurso de runtime do Reconhecimento vocal: [Criar um no portal do Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
* [Node.js](https://nodejs.org)

## <a name="setting-up"></a>Configurando

### <a name="get-your-language-understanding-luis-runtime-key"></a>Obtenha sua chave de runtime do LUIS (Reconhecimento vocal)

Obtenha sua [chave de runtime](../luis-how-to-azure-subscription.md) criando um recurso de runtime do LUIS. Mantenha sua chave e o ponto de extremidade da chave para a próxima etapa.

[!INCLUDE [Set up environment variables for prediction quickstart](sdk-prediction-environment-variables.md)]

### <a name="create-a-new-javascript-nodejs-file"></a>Criar um arquivo javaScript (Node.js)

Crie um arquivo javascript em no IDE ou no editor de sua preferência, denominado `luis_prediction.js`.

### <a name="install-the-npm-library-for-the-luis-runtime"></a>Instalar a biblioteca do NPM para o runtime do LUIS

No diretório do aplicativo, instale as dependências com o seguinte comando:

```console
npm install @azure/cognitiveservices-luis-runtime @azure/ms-rest-js
```

## <a name="object-model"></a>Modelo de objeto

O cliente de criação do LUIS (Reconhecimento Vocal) é um objeto [LUISAuthoringClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/luisruntimeclient?view=azure-node-latest) que se autentica no Azure, que contém sua chave de criação.

Depois que o cliente for criado, use-o para acessar a funcionalidade, incluindo:

* [Previsão](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getslotprediction-string--string--predictionrequest--models-predictiongetslotpredictionoptionalparams-) por slot de `staging` ou `production`
* [Previsão por versão](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getversionprediction-string--string--predictionrequest--models-predictiongetversionpredictionoptionalparams-)

## <a name="code-examples"></a>Exemplos de código

Estes snippets de códigos mostram como fazer o seguinte com a biblioteca de clientes de runtime de previsão do LUIS (Reconhecimento vocal):

* [Previsão por slot](#get-prediction-from-runtime)

## <a name="add-the-dependencies"></a>Adicionar as dependências

No diretório do projeto, abra o arquivo `luis_prediction.js` no IDE ou no editor de sua preferência. Adicione as seguintes dependências:

[!code-javascript [Dependencies](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=Dependencies)]

## <a name="authenticate-the-client"></a>Autenticar o cliente

1. Crie variáveis para suas informações necessárias do LUIS:

    Adicione variáveis para gerenciar sua chave de previsão extraída de uma variável de ambiente chamada `LUIS_RUNTIME_KEY`. Se você criou a variável de ambiente depois de iniciar o aplicativo, será necessário fechar e recarregar o editor, o IDE ou o shell em execução para acessar a variável. Os métodos serão criados posteriormente.

    Crie uma variável para conter o nome do recurso `LUIS_RUNTIME_ENDPOINT`.

    [!code-javascript [Azure resource variables](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=Variables)]

1. Crie uma variável para a ID do aplicativo como uma variável de ambiente chamada `LUIS_APP_ID`. Defina a variável de ambiente para o aplicativo de IoT público, **`df67dcdb-c37d-46af-88e1-8b97951ca1c2`** . Crie uma variável para definir o slot publicado `production`.

    [!code-javascript [LUIS app variables](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=OtherVariables)]


1. Crie um objeto msRest.ApiKeyCredentials com a sua chave e use-o com o ponto de extremidade para criar um objeto [LUIS.LUISRuntimeClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/luisruntimeclient?view=azure-node-latest).

    [!code-javascript [LUIS Runtime client is required to access predictions for LUIS apps](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=AuthoringCreateClient)]

## <a name="get-prediction-from-runtime"></a>Obter uma previsão do runtime

Adicione o método a seguir para criar a solicitação para o runtime de previsão.

O enunciado do usuário faz parte do objeto [predictionRequest](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionrequest?view=azure-node-latest).

O método **[luisRuntimeClient.prediction.getSlotPrediction](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getslotprediction-string--string--predictionrequest--models-predictiongetslotpredictionoptionalparams-)** precisa de vários parâmetros, como a ID do aplicativo, o nome do slot e o objeto de solicitação de previsão, para atender à solicitação. As outras opções, como detalhada, mostram todas as intenções e log são opcionais.

[!code-javascript [LUIS prediction request and response in Node.js NPM SDK](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=predict)]

## <a name="main-code-for-the-prediction"></a>Código principal para a previsão

Use o método principal a seguir para vincular as variáveis e os métodos para obter a previsão.

[!code-javascript [Main method and main call](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=Main)]

## <a name="run-the-application"></a>Executar o aplicativo

Execute o aplicativo com o comando `node luis_prediction.js` do seu próprio diretório de aplicativo.

```console
node luis_prediction.js
```

O resultado da previsão retorna um objeto JSON:

```console
{
   "query":"turn on all lights",
   "prediction":{
      "topIntent":"HomeAutomation.TurnOn",
      "intents":{
         "HomeAutomation.TurnOn":{
            "score":0.5375382
         },
         "None":{
            "score":0.08687421
         },
         "HomeAutomation.TurnOff":{
            "score":0.0207554
         }
      },
      "entities":{
         "HomeAutomation.Operation":[
            "on"
         ],
         "$instance":{
            "HomeAutomation.Operation":[
               {
                  "type":"HomeAutomation.Operation",
                  "text":"on",
                  "startIndex":5,
                  "length":2,
                  "score":0.724984169,
                  "modelTypeId":-1,
                  "modelType":"Unknown",
                  "recognitionSources":[
                     "model"
                  ]
               }
            ]
         }
      }
   }
}
```


## <a name="clean-up-resources"></a>Limpar os recursos

Ao concluir suas previsões, limpe o trabalho deste início rápido excluindo o arquivo e os subdiretórios dele.
