---
title: 'Início Rápido: criar aplicativo – LUIS'
titleSuffix: Azure Cognitive Services
description: Crie um aplicativo LUIS que use o domínio predefinido `HomeAutomation` para ligar e desligar luzes e eletrodomésticos. Este domínio predefinido fornece intenções, entidades e exemplos de enunciados a você. Quando terminar, você terá um ponto de extremidade do LUIS em execução na nuvem.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 748c51e74db20ac101dc2dff0d924567acded114
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703233"
---
# <a name="quickstart-use-prebuilt-home-automation-app"></a>Início Rápido: usar aplicativo de automação Inicial predefinido

Neste início rápido, crie um aplicativo LUIS que use o domínio predefinido `HomeAutomation` para ligar e desligar luzes e dispositivos. Este domínio predefinido fornece intenções, entidades e exemplos de enunciados a você. Quando terminar, você terá um ponto de extremidade do LUIS em execução na nuvem.

## <a name="prerequisites"></a>Pré-requisitos

Para este artigo, você precisa de uma conta gratuita do LUIS, criada no portal do LUIS em [https://www.luis.ai](https://www.luis.ai). 

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-a-new-app"></a>Criar um novo aplicativo
Você pode criar e gerenciar seus aplicativos em **Meus Aplicativos**. 

1. Selecione **Criar novo aplicativo**.

    [![Captura de tela da lista de aplicativos](media/luis-quickstart-new-app/app-list.png "Captura de tela da lista de aplicativos")](media/luis-quickstart-new-app/app-list.png)

1. Na caixa de diálogo, dê ao seu aplicativo o nome "Automação Residencial".

    [![Captura de tela da caixa de diálogo pop-up Criar novo aplicativo](media/luis-quickstart-new-app/create-new-app-dialog.png "Captura de tela da caixa de diálogo pop-up Criar novo aplicativo")](media/luis-quickstart-new-app/create-new-app-dialog.png)

1. Escolha a cultura do aplicativo. Para este aplicativo de Automação Residencial, escolha inglês. Em seguida, selecione **Concluído**. O LUIS cria o aplicativo de Automação Residencial. 

    >[!NOTE]
    >A cultura não poderá ser alterada depois que o aplicativo for criado. 

## <a name="add-prebuilt-domain"></a>Adicionar domínio predefinido

Selecione **Domínios predefinidos** no painel de navegação à esquerda. Em seguida, pesquise "Início". Selecione **Adicionar domínio**.

[![Captura de tela de Domínio de Automação Inicial chamado no menu de domínio predefinido](media/luis-quickstart-new-app/home-automation.png "Captura de tela de Domínio de Automação Inicial chamado no menu de domínio predefinido")](media/luis-quickstart-new-app/home-automation.png)

Quando o domínio for adicionado com êxito, a caixa de domínio predefinido exibirá um botão **Remover domínio**.

[![Captura de tela de Domínio de Automação Inicial com o botão Remover](media/luis-quickstart-new-app/remove-domain.png "Captura de tela de Domínio de Automação Inicial com o botão Remover")](media/luis-quickstart-new-app/remove-domain.png)

## <a name="intents-and-entities"></a>Intenções e entidades

Selecione **Intenções** no painel de navegação à esquerda para examinar as intenções do domínio HomeAutomation. Cada intenção tem exemplos de enunciados.

![Captura de tela de lista de tentativas de HomeAutomation](media/luis-quickstart-new-app/home-automation-intents.png "Captura de tela de lista de tentativas de HomeAutomation")]

> [!NOTE]
> **None** é uma intenção fornecida por todos os aplicativos LUIS. Você pode usá-la para lidar com enunciados que não correspondem à funcionalidade que seu aplicativo fornece. 

Selecione a intenção **HomeAutomation.TurnOff**. Você pode ver que a intenção contém uma lista de enunciados que rotulados com entidades.

[![Captura de tela da intenção HomeAutomation.TurnOff](media/luis-quickstart-new-app/home-automation-turnoff.png "Captura de tela da intenção HomeAutomation.TurnOff")](media/luis-quickstart-new-app/home-automation-turnoff.png)

## <a name="train-the-luis-app"></a>Treinar o aplicativo LUIS

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="test-your-app"></a>Testar seu aplicativo
Depois de treinar o aplicativo, você pode testá-lo. Selecione **Testar** na navegação superior. Digite uma expressão de teste, como "Desligar as luzes" no painel de Teste Interativo e pressione Enter. 

```
Turn off the lights
```

Verifique se a intenção de pontuação superior corresponde à intenção esperada para cada enunciado de teste.

Neste exemplo, `Turn off the lights` é identificado corretamente como a intenção de pontuação superior de **HomeAutomation.TurnOff**.

[![Captura de tela do painel Teste com declaração destacada](media/luis-quickstart-new-app/test.png "Captura de tela do painel Teste com declaração destacada")](media/luis-quickstart-new-app/test.png)


Selecione **Inspecionar** para examinar mais informações sobre a previsão.

![Captura de tela do painel de Teste com o enunciado realçado](media/luis-quickstart-new-app/review-test-inspection-pane-in-portal.png)

Selecione **Testar** novamente para recolher o painel de teste. 

<a name="publish-your-app"></a>

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publicar o aplicativo para obter a URL do ponto de extremidade

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-v2-api-prediction-endpoint"></a>Consultar o ponto de extremidade de previsão da API V2

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

1. Vá até o final da URL no endereço, digite `turn off the living room light` e pressione Enter. 

    #### <a name="v2-prediction-endpointtabv2"></a>[Ponto de extremidade de previsão V2](#tab/V2)

    `https://<region>.api.cognitive.microsoft.com/luis/**v2.0**/apps/<appID>?subscription-key=<YOUR_KEY>&**q=<user-utterance-text>**`

    O navegador exibe a versão da **API V2** da resposta JSON de seu ponto de extremidade HTTP.

    ```json
    {
      "query": "turn off the lights",
      "topScoringIntent": {
        "intent": "HomeAutomation.TurnOff",
        "score": 0.995867
      },
      "entities": [
        {
          "entity": "lights",
          "type": "HomeAutomation.DeviceType",
          "startIndex": 13,
          "endIndex": 18,
          "resolution": {
            "values": [
              "light"
            ]
          }
        }
      ]
    }
    ```
    
    #### <a name="v3-prediction-endpointtabv3"></a>[Ponto de extremidade de previsão V3](#tab/V3)

    Para uma [consulta à API V3](luis-migration-api-v3.md), no navegador, altere a solicitação HTTPS do método GET, alterando os valores entre colchetes angulares para seus próprios valores.     

    `https://<region>.api.cognitive.microsoft.com/luis/**v3.0-preview**/apps/<appID>/**slots**/**production**/**predict**?subscription-key=<YOUR_KEY>&**query=<user-utterance-text>**`

    ```json
    {
        "query": "turn off the lights",
        "prediction": {
            "normalizedQuery": "turn off the lights",
            "topIntent": "HomeAutomation.TurnOff",
            "intents": {
                "HomeAutomation.TurnOff": {
                    "score": 0.99649024
                }
            },
            "entities": {
                "HomeAutomation.DeviceType": [
                    [
                        "light"
                    ]
                ]
            }
        }
    }
    ```


    Saiba mais sobre o [ponto de extremidade de previsão V3](luis-migration-api-v3.md).
    
    * * * 

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Próximas etapas

Você pode chamar o ponto de extremidade do código:

> [!div class="nextstepaction"]
> [Chamar um ponto de extremidade do LUIS usando código](luis-get-started-cs-get-intent.md)
