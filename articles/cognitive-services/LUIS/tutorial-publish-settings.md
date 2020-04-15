---
title: 'Tutorial: Publicar configurações – LUIS'
description: Neste tutorial, altere as configurações de publicação para melhorar as previsões.
ms.topic: tutorial
ms.date: 04/01/2020
ms.openlocfilehash: 19913d16ecb1457ad4edb93ea34e4b96a590aca0
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80545767"
---
# <a name="tutorial--add-sentiment-analysis-as-a-publishing-setting"></a>Tutorial:  Adicionar análise de sentimento como uma configuração de publicação

Neste tutorial, modifique as configurações de publicação para extrair a análise de sentimentos e, em seguida, consulte o ponto de extremidade do LUIS para ver o sentimento retornado com base em um enunciado do usuário.

**Neste tutorial, você aprenderá a:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Adicionar análise de sentimento como uma configuração de publicação
> * Obtenha o sentimento com base em um enunciado do ponto de extremidade publicado

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="sentiment-analysis-is-a-publish-setting"></a>A análise de sentimento é uma configuração de publicação

Os enunciados a seguir mostram exemplos de sentimentos:

|Sentimento|Pontuação|Enunciado|
|:--|:--|:--|
|negativo|0,01 |A pizza estava horrível.|
|positivo|0,97 |A pizzaria do queijo estava incrível.|

A análise de sentimento é uma configuração de publicação que se aplica a cada enunciado. Uma vez configurado, o aplicativo retorna o sentimento de um enunciado sem que você precise rotular os dados.

Como se trata de uma configuração de publicação, ela não aparece rotulada nas páginas de intenções ou de entidades. Ela aparece no painel [teste interativo](luis-interactive-test.md#view-sentiment-results) ou ao testar a URL de ponto de extremidade.

## <a name="import-example-json-to-begin-app"></a>Importar .json de exemplo para começar o aplicativo

1.  Baixe e salve o [arquivo JSON do aplicativo](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-tutorial-with-entities.json).

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="train-the-app"></a>Treinar o aplicativo

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="configure-app-to-include-sentiment-analysis"></a>Configurar aplicativo para incluir a análise de sentimento

1. Selecione **Publicar** no menu superior. A análise de sentimento é uma configuração de publicação.

1. Selecione **Slot de produção** e, em seguida, selecione **Alterar configurações**.
1. Defina a configuração da Análise de Sentimento como **Ativado**.

    ![Ativar Análise de Sentimento como configuração de publicação](./media/luis-quickstart-intent-and-sentiment-analysis/select-sentiment-publishing-setting.png)

## <a name="get-the-sentiment-of-an-utterance-from-the-endpoint"></a>Obter o sentimento de um enunciado do ponto de extremidade

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Vá para o final da URL na barra de endereços e substitua _YOUR_QUERY_HERE_ por:

    `Deliver 2 of the best cheese pizzas ever!!!`

    Esse enunciado não é igual a nenhum dos enunciados rotulados, portanto, ele é um bom teste e deve retornar a intenção `OrderPizza` com a extração da análise de sentimento.

    ```json
    {
        "query": "Deliver 2 of the best cheese pizzas ever!!!",
        "prediction": {
            "topIntent": "OrderPizza",
            "intents": {
                "OrderPizza": {
                    "score": 0.4659952
                },
                "None": {
                    "score": 0.16901511
                },
                "Confirm": {
                    "score": 0.00421415
                },
                "Greeting": {
                    "score": 0.00109914376
                }
            },
            "entities": {
                "number": [
                    2
                ],
                "$instance": {
                    "number": [
                        {
                            "type": "builtin.number",
                            "text": "2",
                            "startIndex": 8,
                            "length": 1,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            },
            "sentiment": {
                "label": "positive",
                "score": 0.861665964
            }
        }
    }
    ```

    A análise de sentimento é positiva, com uma pontuação de 86%.

[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Informações relacionadas

* A Análise de Sentimento é fornecida pelo serviço cognitivo [Análise de Texto](../Text-Analytics/index.yml). O recurso é restrito aos [idiomas com suporte](luis-language-support.md#languages-supported) da Análise de Texto.
* [Como treinar](luis-how-to-train.md)
* [Como publicar](luis-how-to-publish-app.md)
* [Como testar no portal do LUIS](luis-interactive-test.md)


## <a name="next-steps"></a>Próximas etapas
Este tutorial adiciona a análise de sentimento como uma configuração de publicação para extrair valores de sentimento do enunciado como um todo.

> [!div class="nextstepaction"]
> [Examinar enunciados de ponto de extremidade no aplicativo de RH](luis-tutorial-review-endpoint-utterances.md)

