---
title: 'Tutorial: Entidade de expressão regular — LUIS'
description: Extrair dados formatados consistentemente de um enunciado usando a entidade de Expressão Regular.
ms.topic: tutorial
ms.date: 04/01/2020
ms.openlocfilehash: 5b585ee52880c474d3f2736b34a267012b390aad
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80545829"
---
# <a name="tutorial-get-well-formatted-data-from-the-utterance"></a>Tutorial: Obter dados bem formatados do enunciado
Neste tutorial, crie uma entidade de expressão regular para extrair dados formatados de forma consistente de um enunciado.

**Neste tutorial, você aprenderá a:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importar aplicativo
> * Adicionar intenção
> * Adicionar entidade de expressão regular
> * Treinar, publicar e consultar o aplicativo para obter dados extraídos

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="regular-expression-entities"></a>Entidades de expressão regular

Use a entidade expressão regular para extrair texto bem formatado de um enunciado. Embora a intenção do enunciado sempre seja determinada com aprendizado de máquina, esse tipo de entidade específico não é de aprendizado de máquina. Um bom uso para a entidade de expressão regular é qualquer texto que possa ser representado de modo consistente por uma [expressão regular](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference).

`Send pizza delivery time to x123432`

Este exemplo usa um _código curto_ para enviar mensagens de texto. Esse código curto é um código numérico de cinco ou seis dígitos, prefixado com um x, e pode ser descrito com a expressão regular `x\d{5,6}`.

Ao adicionar uma entidade de expressão regular a um aplicativo LUIS, você não precisa [rotular](label-entity-example-utterance.md) o texto com a entidade expressa normal. Aplica-se a todos os enunciados em todas as intenções.

## <a name="import-example-json-to-begin-app"></a>Importar .json de exemplo para começar o aplicativo

1.  Baixe e salve o [arquivo JSON do aplicativo](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-tutorial-with-entities.json).

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="create-intent-for-sending-confirmation-text-messages"></a>Criar intenção para enviar mensagens de texto de confirmação

1. Selecione **+ Criar** para criar uma intenção de classificar a intenção de um enunciado para enviar um texto de confirmação.

1. Insira `ConfirmationText` na caixa de diálogo pop-up, depois selecione **Concluído**.

1. Adicione enunciados de exemplo para a intenção.

    |Exemplo de enunciados|
    |--|
    |Enviar a hora de entrega de pizza para x123432|
    |Txt x234567 para hora|
    |x23987 para o aviso|

    Para extrair entidades aprendidas por computador, você deve fornecer exemplos que incluam a entidade em diversos enunciados, mas com essa entidade não aprendida por computador, a variação não é importante. Desde que o texto corresponda à expressão regular, ele será extraído.

## <a name="use-the-regular-expression-entity-for-well-formatted-data"></a>Usar a entidade de expressão regular para dados bem formatados
Crie uma entidade de expressão regular para corresponder ao número do texto. Essa expressão regular corresponde ao texto, mas ignora variantes de maiúsculas e minúsculas e cultura.

1. Selecione **Entidades** no painel esquerdo.

1. Selecione **+ Criar** na página de lista de Entidades.

1. Na caixa de diálogo pop-up, insira o novo nome de entidade `ConfirmationTextRegEx`, selecione **RegEx** como o tipo de entidade e selecione **Avançar**.

    > [!div class="mx-imgBorder"]
    > ![Iniciar as etapas de criação de entidade para entidade de expressão regular](./media/luis-quickstart-intents-regex-entity/pizza-create-new-entity.png)

1. Em **Criar uma entidade regex**, insira `x\d{5,6}` como o valor de **Regex** e selecione **Criar**.

    > [!div class="mx-imgBorder"]
    > ![Insira uma expressão regular para extrair dados do enunciado de exemplo](./media/luis-quickstart-intents-regex-entity/pizza-set-regular-expression-for-new-entity.png)

1. Selecione **Intenções** no menu à esquerda e, em seguida, a intenção **ConfirmationText** para ver a expressão regular rotulada nos enunciados.

    > [!div class="mx-imgBorder"]
    > ![Exibir expressão regular rotulada nos enunciados de exemplo](./media/luis-quickstart-intents-regex-entity/pizza-reg-ex-entity-shown-example-utterances-intent.png)

    Como a entidade não é uma entidade de aprendizado de máquina, a entidade é aplicada aos enunciados e exibida no portal do LUIS assim que ela é criada.

## <a name="train-the-app-before-testing-or-publishing"></a>Treinar o aplicativo antes do teste ou da publicação

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-query-from-the-endpoint"></a>Publicar o aplicativo para consulta do ponto de extremidade

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Obter a previsão de intenção e de entidade do ponto de extremidade

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Vá para o final da URL na barra de endereços e substitua _YOUR_QUERY_HERE_ por:

    `Text my pizza delivery to x23456 x234567 x12345`

    ```json
    {
        "query": "Text my pizza delivery to x23456 x234567 x12345",
        "prediction": {
            "topIntent": "ConfirmationText",
            "intents": {
                "ConfirmationText": {
                    "score": 0.7061845
                },
                "ModifyOrder": {
                    "score": 0.196021989
                },
                "None": {
                    "score": 0.02342912
                },
                "Test-Pizza": {
                    "score": 0.01213586
                },
                "CancelOrder": {
                    "score": 0.0063042324
                },
                "Confirmation": {
                    "score": 0.0058615827
                },
                "Greetings": {
                    "score": 0.00398947531
                }
            },
            "entities": {
                "ConfirmationTextRegEx": [
                    "x23456",
                    "x234567",
                    "x12345"
                ],
                "$instance": {
                    "ConfirmationTextRegEx": [
                        {
                            "type": "ConfirmationTextRegEx",
                            "text": "x23456",
                            "startIndex": 26,
                            "length": 6,
                            "modelTypeId": 8,
                            "modelType": "Regex Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        },
                        {
                            "type": "ConfirmationTextRegEx",
                            "text": "x234567",
                            "startIndex": 33,
                            "length": 7,
                            "modelTypeId": 8,
                            "modelType": "Regex Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        },
                        {
                            "type": "ConfirmationTextRegEx",
                            "text": "x12345",
                            "startIndex": 41,
                            "length": 6,
                            "modelTypeId": 8,
                            "modelType": "Regex Entity Extractor",
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

    Usando uma entidade de expressão regular, o LUIS extrai dados nomeados, o que é mais programaticamente útil para o aplicativo cliente recebendo a resposta JSON.


[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Informações relacionadas

* [Conceito – entidades](luis-concept-entity-types.md)
* [Referência do JSON de entidade de expressão regular](reference-entity-regular-expression.md?tabs=V3)
* [Como adicionar entidades para extrair dados](luis-how-to-add-entities.md)

## <a name="next-steps"></a>Próximas etapas
Este tutorial criou uma nova intenção, adicionou exemplos de enunciados e criou uma entidade de expressão regular para extrair dados bem formatados de enunciados. Após o treinamento e a publicação do aplicativo, uma consulta para o ponto de extremidade identificou a intenção e retornou os dados extraídos.

> [!div class="nextstepaction"]
> [Saiba mais sobre a entidade de lista](tutorial-list-entity.md)

