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
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 6282e768ebc51d0d4ec2b15f057727f207a7d81a
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703598"
---
# <a name="quickstart-get-intent-with-a-browser"></a>Início Rápido: Obter a intenção com um navegador

Para entender o que um ponto de extremidade de previsão do LUIS retorna, exiba um resultado de previsão em um navegador da Web. 

## <a name="prerequisites"></a>Pré-requisitos

Para consultar um aplicativo público, você precisará de:

* Sua própria chave LUIS (Reconhecimento vocal). Caso você ainda não tenha uma assinatura para criar uma chave, registre-se em uma [conta gratuita](https://azure.microsoft.com/free/).
* A ID do aplicativo público: `df67dcdb-c37d-46af-88e1-8b97951ca1c2`. 

## <a name="use-the-browser-to-see-predictions"></a>Usar o navegador para ver as previsões

1. Abra um navegador da Web. 
1. Use as URLs completas abaixo, substituindo `{your-key}` por sua própria chave LUIS. As solicitações são solicitações GET e incluem a autorização, com a chave LUIS, como um parâmetro de cadeia de consulta.

    #### <a name="v2-prediction-endpoint-requesttabv2"></a>[Solicitação de ponto de extremidade de previsão V2](#tab/V2)
    
    O formato da URL V2 para uma solicitação de ponto de extremidade **GET** é:
    
    `
    https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key={your-key}&q=turn on all lights
    `
    
    #### <a name="v3-prediction-endpoint-requesttabv3"></a>[Solicitação de ponto de extremidade de previsão V3](#tab/V3)
    
    
    O formato da URL V3 para uma solicitação de ponto de extremidade **GET** (por slots) é:
    
    `
    https://westus.api.cognitive.microsoft.com/luis/v3.0-preview/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key={your-key}
    `
    
    * * *

1. Cole a URL em uma janela do navegador e pressione Enter. O navegador exibe um resultado JSON que indica que o LUIS detecta a intenção `HomeAutomation.TurnOn` como a intenção principal e a entidade `HomeAutomation.Operation` com o valor `on`.

    #### <a name="v2-prediction-endpoint-responsetabv2"></a>[Resposta de ponto de extremidade de previsão V2](#tab/V2)

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

    #### <a name="v3-prediction-endpoint-responsetabv3"></a>[Resposta de ponto de extremidade de previsão V3](#tab/V3)

    ```JSON
    {
        "query": "turn on all lights",
        "prediction": {
            "normalizedQuery": "turn on all lights",
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

    * * *

1. Para ver todas as intenções, adicione o parâmetro de cadeia de consulta apropriado. 

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

    #### <a name="v3-prediction-endpointtabv3"></a>[Ponto de extremidade de previsão V3](#tab/V3)

    Adicione `show-all-intents=true` ao final da cadeia de consulta para **mostrar todas as intenções**:

    `
    https://westus.api.cognitive.microsoft.com/luis/v3.0-preview/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key={your-key}&show-all-intents=true
    `

    ```JSON
    {
        "query": "turn on all lights",
        "prediction": {
            "normalizedQuery": "turn on all lights",
            "topIntent": "HomeAutomation.TurnOn",
            "intents": {
                "HomeAutomation.TurnOn": {
                    "score": 0.5375382
                },
                "HomeAutomation.TurnOff": {
                     "score": 0.0207554
                },
                "None": {
                     "score": 0.08687421
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

    * * * 

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o [ponto de extremidade de previsão V3](luis-migration-api-v3.md).

> [!div class="nextstepaction"]
> [Criar um aplicativo no portal do LUIS](get-started-portal-build-app.md)