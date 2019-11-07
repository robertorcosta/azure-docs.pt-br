---
title: 'Início Rápido: Obter a intenção com o navegador – LUIS'
titleSuffix: Azure Cognitive Services
description: Neste início rápido, use um aplicativo LUIS público disponível para determinar a intenção de um usuário com base no texto de conversa em um navegador.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 10/17/2019
ms.author: diberry
ms.openlocfilehash: a99dbc594b53d00ae02b2581d149fe7b4573ab7d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73495233"
---
# <a name="quickstart-get-intent-with-a-browser"></a>Início Rápido: Obter a intenção com um navegador

Para entender o que um ponto de extremidade de previsão do LUIS retorna, exiba um resultado de previsão em um navegador da Web. 

## <a name="prerequisites"></a>Pré-requisitos

Para consultar um aplicativo público, você precisará de:

* Sua própria chave LUIS (Reconhecimento vocal). Caso você ainda não tenha uma assinatura para criar uma chave, registre-se em uma [conta gratuita](https://azure.microsoft.com/free/). A chave de criação do LUIS não funcionará. 
* A ID do aplicativo público: `df67dcdb-c37d-46af-88e1-8b97951ca1c2`. 

## <a name="use-the-browser-to-see-predictions"></a>Usar o navegador para ver as previsões

1. Abra um navegador da Web. 
1. Use as URLs completas abaixo, substituindo `YOUR-KEY` por sua própria chave LUIS. As solicitações são solicitações GET e incluem a autorização, com a chave LUIS, como um parâmetro de cadeia de consulta.

    #### <a name="v3-prediction-requesttabv3-1-1"></a>[Solicitação de previsão V3](#tab/V3-1-1)
    
    
    O formato da URL V3 para uma solicitação de ponto de extremidade **GET** (por slots) é:
    
    `
    https://westus.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key=YOUR-KEY
    `

    #### <a name="v2-prediction-requesttabv2-1-2"></a>[Solicitação de previsão V2](#tab/V2-1-2)
    
    O formato da URL V2 para uma solicitação de ponto de extremidade **GET** é:
    
    `
    https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=YOUR-KEY&q=turn on all lights
    `

1. Cole a URL em uma janela do navegador e pressione Enter. O navegador exibe um resultado JSON que indica que o LUIS detecta a intenção `HomeAutomation.TurnOn` como a intenção principal e a entidade `HomeAutomation.Operation` com o valor `on`.

    #### <a name="v3-prediction-responsetabv3-2-1"></a>[Resposta de previsão V3](#tab/V3-2-1)

    ```JSON
    {
        "query": "turn on all lights",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOn",
            "intents": {
                "HomeAutomation.TurnOn": {
                    "score": 0.5375382
                }
            },
            "entities": {
                "HomeAutomation.Operation": [
                    "on"
                ]
            }
        }
    }
    ```

    #### <a name="v2-prediction-responsetabv2-2-2"></a>[Resposta de previsão V2](#tab/V2-2-2)

    ```json
    {
        "query": "turn on all lights",
        "topScoringIntent": {
            "intent": "HomeAutomation.TurnOn",
            "score": 0.5375382
        },
        "entities": [
            {
                "entity": "on",
                "type": "HomeAutomation.Operation",
                "startIndex": 5,
                "endIndex": 6,
                "score": 0.724984169
            }
        ]
    }
    ```

    * * *

1. Para ver todas as intenções, adicione o parâmetro de cadeia de consulta apropriado. 

    #### <a name="v3-prediction-endpointtabv3-3-1"></a>[Ponto de extremidade de previsão V3](#tab/V3-3-1)

    Adicione `show-all-intents=true` ao final da cadeia de consulta para **mostrar todas as intenções**:

    `
    https://westus.api.cognitive.microsoft.com/luis/predict/v3.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key=YOUR-KEY&show-all-intents=true
    `

    ```JSON
    {
        "query": "turn off the living room light",
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
                ]
            }
        }
    }
    ```

    #### <a name="v2-prediction-endpointtabv2"></a>[Ponto de extremidade de previsão V2](#tab/V2)

    Adicione `verbose=true` ao final da cadeia de consulta para **mostrar todas as intenções**:

    `
    https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?q=turn on all lights&subscription-key={your-key}&verbose=true
    `

    ```json
    {
        "query": "turn on all lights",
        "topScoringIntent": {
            "intent": "HomeAutomation.TurnOn",
            "score": 0.5375382
        },
        "intents": [
            {
                "intent": "HomeAutomation.TurnOn",
                "score": 0.5375382
            },
            {
                "intent": "None",
                "score": 0.08687421
            },
            {
                "intent": "HomeAutomation.TurnOff",
                "score": 0.0207554
            }
        ],
        "entities": [
            {
                "entity": "on",
                "type": "HomeAutomation.Operation",
                "startIndex": 5,
                "endIndex": 6,
                "score": 0.724984169
            }
        ]
    }
    ```


<!-- FIX - is the public app getting updated for the new prebuilt domain with entities? -->   

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o [ponto de extremidade de previsão V3](luis-migration-api-v3.md).

> [!div class="nextstepaction"]
> [Criar um aplicativo no portal do LUIS](get-started-portal-build-app.md)