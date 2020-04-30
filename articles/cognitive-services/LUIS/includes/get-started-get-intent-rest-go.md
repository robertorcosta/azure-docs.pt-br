---
title: Obter a intenção com chamada REST em Go
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/20/2020
ms.author: diberry
ms.openlocfilehash: d9af9f788e2309cdf687e0a13b77220c150a0e1e
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733290"
---
## <a name="prerequisites"></a>Pré-requisitos

* Linguagem de programação [Go](https://golang.org/)
* [Visual Studio Code](https://code.visualstudio.com/)
* Uma ID do aplicativo LUIS – use a ID do aplicativo IoT público de `df67dcdb-c37d-46af-88e1-8b97951ca1c2`. A consulta de usuário usada no código de início rápido é específica para esse aplicativo.

## <a name="create-luis-runtime-key-for-predictions"></a>Criar uma chave de runtime do LUIS para previsões

1. Entre no [Portal do Azure](https://portal.azure.com)
1. Clique em [Criar **Reconhecimento vocal**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
1. Insira todas as configurações necessárias para a chave de **Runtime**:

    |Configuração|Valor|
    |--|--|
    |Nome|Nome desejado (2 a 64 caracteres)|
    |Subscription|Selecione a assinatura apropriada|
    |Location|Selecione qualquer localização próxima e disponível|
    |Camada de preços|`F0` – o tipo de preço mínimo|
    |Grupo de recursos|Selecione um grupo de recursos disponível|

1. Clique em **Criar** e aguarde até que o recurso seja criado. Após a criação dele, navegue até a página do recurso.
1. Colete o `endpoint` configurado e uma `key`.

## <a name="get-intent-programmatically"></a>Obter a intenção de forma programática

Use o Go para consultar o [ponto de extremidade de previsão](https://aka.ms/luis-apim-v3-prediction) para obter o resultado da previsão.

1. Crie um arquivo chamado `predict.go`. Adicione os códigos a seguir:

    ```go
    package main

    /* Do dependencies */
    import (
        "fmt"
        "net/http"
        "net/url"
        "io/ioutil"
        "log"
    )
    func main() {

        // public app
        var appID = "df67dcdb-c37d-46af-88e1-8b97951ca1c2"

        // utterance for public app
        var utterance = "turn on all lights"

        // YOUR-KEY - your **Runtime** key
        var endpointKey = "YOUR-KEY"

        // YOUR-ENDPOINT - example is your-resource-name.api.cognitive.microsoft.com
        var endpoint = "YOUR-ENDPOINT"

        endpointPrediction(appID, endpointKey, endpoint, utterance)
    }
    func endpointPrediction(appID string, endpointKey string, endpoint string, utterance string) {

        var endpointUrl = fmt.Sprintf("https://%s/luis/prediction/v3.0/apps/%s/slots/production/predict?subscription-key=%s&verbose=true&show-all-intents=true&query=%s", endpoint, appID, endpointKey, url.QueryEscape(utterance))

        response, err := http.Get(endpointUrl)

        if err!=nil {
            // handle error
            fmt.Println("error from Get")
            log.Fatal(err)
        }

        response2, err2 := ioutil.ReadAll(response.Body)

        if err2!=nil {
            // handle error
            fmt.Println("error from ReadAll")
            log.Fatal(err2)
        }

        fmt.Println("response")
        fmt.Println(string(response2))
    }
    ```

1. Substitua os valores `YOUR-KEY` e `YOUR-ENDPOINT` pelo próprio ponto de extremidade e pela própria chave de **Runtime** de previsão.

    |Informações|Finalidade|
    |--|--|
    |`YOUR-KEY`|A chave de **Runtime** de previsão de 32 caracteres.|
    |`YOUR-ENDPOINT`| O ponto de extremidade da URL de previsão. Por exemplo, `replace-with-your-resource-name.api.cognitive.microsoft.com`.|

1. Com um prompt de comando no mesmo diretório em que você criou o arquivo, insira o seguinte comando para compilar o arquivo Go:

    ```console
    go build predict.go
    ```

1. Execute o aplicativo GO da linha de comando inserindo o texto a seguir no prompt de comando:

    ```console
    go run predict.go
    ```

    A resposta do prompt de comando é:

    ```console
    appID has value df67dcdb-c37d-46af-88e1-8b97951ca1c2
    endpointKey has value a7b206911f714e71a1ddae36928a61cc
    endpoint has value westus2.api.cognitive.microsoft.com
    utterance has value turn on all lights
    response
    {"query":"turn on all lights","prediction":{"topIntent":"HomeAutomation.TurnOn","intents":{"HomeAutomation.TurnOn":{"score":0.5375382},"None":{"score":0.08687421},"HomeAutomation.TurnOff":{"score":0.0207554}},"entities":{"HomeAutomation.Operation":["on"],"$instance":{"HomeAutomation.Operation":[{"type":"HomeAutomation.Operation","text":"on","startIndex":5,"length":2,"score":0.724984169,"modelTypeId":-1,"modelType":"Unknown","recognitionSources":["model"]}]}}}}
    ```

    Formatado em JSON para facilitar a leitura:

    ```json
    {
        "query": "turn on all lights",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOn",
            "intents": {
                "HomeAutomation.TurnOn": {
                    "score": 0.5375382
                },
                "None": {
                    "score": 0.08687421
                },
                "HomeAutomation.TurnOff": {
                    "score": 0.0207554
                }
            },
            "entities": {
                "HomeAutomation.Operation": [
                    "on"
                ],
                "$instance": {
                    "HomeAutomation.Operation": [
                        {
                            "type": "HomeAutomation.Operation",
                            "text": "on",
                            "startIndex": 5,
                            "length": 2,
                            "score": 0.724984169,
                            "modelTypeId": -1,
                            "modelType": "Unknown",
                            "recognitionSources": [
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

Ao concluir este guia de início rápido, exclua o arquivo do sistema de arquivos.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Adicionar enunciados e treinar](../get-started-get-model-rest-apis.md)