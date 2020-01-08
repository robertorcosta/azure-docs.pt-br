---
title: 'Tutorial: Entidade de lista – LUIS'
titleSuffix: Azure Cognitive Services
description: Obtenha dados que correspondam a uma lista predefinida de itens. Cada item na lista pode ter sinônimos que também tenham correspondência exata
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/17/2019
ms.author: diberry
ms.openlocfilehash: 056c64657f42d56879928f518598206d45493f60
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75447777"
---
# <a name="tutorial-get-exact-text-matched-data-from-an-utterance-with-list-entity"></a>Tutorial: Obter dados de correspondência de texto exata de um enunciado com a entidade de lista

Neste tutorial, entenda como obter dados que correspondam exatamente a uma lista predefinida de itens.

**Neste tutorial, você aprenderá a:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importar aplicativo e usar a intenção existente
> * Adicionar listar de entidades
> * Treinar, publicar e consultar o aplicativo para obter dados extraídos

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="what-is-a-list-entity"></a>O que é uma entidade de lista?

Uma entidade de lista é uma correspondência exata do texto com as palavras na declaração. Cada item na lista pode incluir uma lista de sinônimos. Use uma entidade de lista quando quiser uma correspondência exata.

Para esse aplicativo de pizza importado, crie uma entidade de lista para os diferentes tipos de massa de pizza.

Uma entidade de lista é uma boa escolha para esse tipo de dados quando:

* Os valores de dados são um conjunto conhecido.
* O conjunto não excede os [limites](luis-boundaries.md) máximos do LUIS para esse tipo de entidade.
* O texto no enunciado corresponde exatamente a um sinônimo ou ao nome canônico. LUIS não usa a lista além das correspondências de texto exatas. Lematização, plurais e outras variações não são resolvidas com apenas uma entidade de lista. Para gerenciar variações, considere usar um [padrão](reference-pattern-syntax.md#syntax-to-mark-optional-text-in-a-template-utterance) com a sintaxe de texto opcional.

> [!CAUTION]
> Se você não tiver certeza se deseja uma entidade de lista ou uma entidade aprendida por computador com uma lista de frases como um descritor, a melhor prática e a opção flexível é usar uma entidade aprendida por computador com uma lista de frases como um descritor. Esse método permite que o LUIS aprenda e estenda os valores dos dados a serem extraídos.

## <a name="import-example-json-and-add-utterances"></a>Importar .json de exemplo e adicionar enunciados

1.  Baixe e salve o [arquivo JSON do aplicativo](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-tutorial-with-entities.json).

    [!INCLUDE [Import app steps](includes/import-app-steps.md)]

1. O aplicativo importado tem uma intenção de `OrderPizza`. Selecione essa intenção e adicione alguns enunciados com os novos tipos massa:

    |Novos enunciados|
    |--|--|
    |peça uma pizza com massa pan pequena sabor pepperoni|
    |três pizzas com massa fina sabor havaiano|
    |entregar duas pizzas com massa recheada com palitos de pão|
    |uma pizza com massa grossa para retirada|
    |uma pizza sabor pepperoni em prato fundo|

## <a name="crust-list-entity"></a>Entidade de lista de massas

Agora que a intenção de **OrderPizza** tem enunciados de exemplo com tipos de massa, o LUIS precisa entender quais palavras representam os tipos de massa.

Exemplos de nome primário e sinônimos são:

|Nome canônico|Sinônimos|
|--|--|
|Prato fundo|fundo<br>massa em prato fundo<br>grossa<br>massa grossa|
|Pan|regular<br>original<br>normal<br>massa regular<br>massa original<br>massa normal|
|Recheada|massa recheada|
|Fina|massa fina<br>seca<br>massa seca|

1. Selecione **Entidades** no painel esquerdo.

1. Selecione **+ Criar**.

1. No diálogo pop-up da entidade, insira `CrustList` como o nome de entidade e **List** como o tipo de entidade. Selecione **Avançar**.

    > [!div class="mx-imgBorder"]
    > ![Captura de tela do diálogo pop-up de criação de nova entidade](media/luis-quickstart-intent-and-list-entity/create-pizza-crust-list-entity.png)

1. Na página **Criar uma entidade de lista**, insira os nomes canônicos e sinônimos para cada nome canônico e, em seguida, selecione **Criar**.

    > [!div class="mx-imgBorder"]
    > ![Captura de tela da adição de itens à entidade de lista](media/luis-quickstart-intent-and-list-entity/add-pizza-crust-items-list-entity.png)

    Ao adicionar uma entidade de lista a um aplicativo LUIS, você não precisa [rotular](label-entity-example-utterance.md) o texto com a entidade de lista. Aplica-se a todos os enunciados em todas as intenções.

## <a name="train-the-app-before-testing-or-publishing"></a>Treinar o aplicativo antes do teste ou da publicação

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-query-from-the-endpoint"></a>Publicar o aplicativo para consulta do ponto de extremidade

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Obter a previsão de intenção e de entidade do ponto de extremidade

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

2. Vá até o final da URL no endereço e insira o seguinte enunciado:

    `Deliver 2 deep dish hawaiian pizzas and a thin pepperoni`

    O último parâmetro de querystring é `query`, o enunciado **consulta**.


    ```json
    {
        "query": "Deliver 2 deep dish hawaiian pizzas and a thin pepperoni",
        "prediction": {
            "topIntent": "OrderPizza",
            "intents": {
                "OrderPizza": {
                    "score": 0.9957229
                },
                "None": {
                    "score": 0.016832687
                },
                "Confirm": {
                    "score": 0.0015708931
                },
                "Greeting": {
                    "score": 0.00057060417
                }
            },
            "entities": {
                "number": [
                    2
                ],
                "CrustList": [
                    [
                        "Deep dish"
                    ],
                    [
                        "Thin"
                    ]
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
                    ],
                    "CrustList": [
                        {
                            "type": "CrustList",
                            "text": "deep dish",
                            "startIndex": 10,
                            "length": 9,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        },
                        {
                            "type": "CrustList",
                            "text": "thin",
                            "startIndex": 42,
                            "length": 4,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
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

    Os tipos de massa foram encontrados como correspondências exatas de texto e retornados na resposta JSON. Essas informações são usadas pelo aplicativo cliente para processar o pedido.

[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Informações relacionadas

* Informações conceituais de [entidade de lista](luis-concept-entity-types.md#list-entity)
* [Como treinar](luis-how-to-train.md)
* [Como publicar](luis-how-to-publish-app.md)
* [Como testar no portal do LUIS](luis-interactive-test.md)
* [Conceito – entidades](luis-concept-entity-types.md)
* [Referência do JSON de entidade de expressão regular](reference-entity-regular-expression.md?tabs=V3)
* [Como adicionar entidades para extrair dados](luis-how-to-add-entities.md)

## <a name="next-steps"></a>Próximas etapas
Este tutorial adicionou exemplos de enunciados e criou uma entidade de lista para extrair correspondências de texto dos enunciados. Após o treinamento e a publicação do aplicativo, uma consulta para o ponto de extremidade identificou a intenção e retornou os dados extraídos.

> [!div class="nextstepaction"]
> [Adicionar entidade predefinida com uma função](tutorial-entity-roles.md)

