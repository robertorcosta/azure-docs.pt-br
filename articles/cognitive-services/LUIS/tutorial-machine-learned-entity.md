---
title: 'Tutorial: extrair dados estruturados com entidades de aprendizado de máquina – LUIS'
titleSuffix: Azure Cognitive Services
description: Extrair dados estruturados de um enunciado usando a entidade de aprendizado de máquina. Para aumentar a precisão da extração, adicione subcomponentes com descritores e restrições.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/17/2019
ms.author: diberry
ms.openlocfilehash: e1709a5e86c8fed8d7f724ad1b105bd02df9fa56
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75381759"
---
# <a name="tutorial-extract-structured-data-from-user-utterance-with-machine-learned-entities-in-language-understanding-luis"></a>Tutorial: Extrair dados estruturados do enunciado do usuário usando entidades de aprendizado de máquina no LUIS (Reconhecimento vocal)

Neste tutorial, extraia dados estruturados de um enunciado usando a entidade de aprendizado de máquina.

A entidade de aprendizado de máquina dá suporte ao [conceito de decomposição de modelo](luis-concept-model.md#v3-authoring-model-decomposition) fornecendo entidades de subcomponente com descritores e restrições.

**Neste tutorial, você aprenderá a:**

> [!div class="checklist"]
> * Importar o aplicativo de exemplo
> * Adicionar entidade de aprendizado de máquina
> * Adicionar subcomponente
> * Adicionar descritor do subcomponente
> * Adicionar restrição do subcomponente
> * Treinar o aplicativo
> * Aplicativo de teste
> * Publicar aplicativo
> * Obter uma previsão de entidade do ponto de extremidade

[!INCLUDE [LUIS Free account](includes/quickstart-tutorial-use-free-starter-key.md)]


## <a name="why-use-a-machine-learned-entity"></a>Por que usar uma entidade de aprendizado de máquina?

Este tutorial adiciona uma entidade de aprendizado de máquina para extrair dados de um enunciado.

A finalidade de uma entidade é definir os dados a serem extraídos. Isso inclui fornecer aos dados um nome, um tipo (se possível), qualquer resolução dos dados, em caso de ambiguidade, bem como o texto exato que compõe os dados.

Para definir a entidade, você precisa criar a entidade e, em seguida, rotular o texto que representa a entidade no enunciado de exemplo. Esses exemplos rotulados ensinam ao LUIS qual é a entidade e em que local ela pode ser encontrada em um enunciado.

## <a name="entity-decomposability-is-important"></a>A decomponibilidade da entidade é importante

A decomponibilidade da entidade é importante para a previsão da intenção e a extração de dados.

Comece com uma entidade de aprendizado de máquina, que é a entidade inicial e de nível superior para extração de dados. Em seguida, decomponha a entidade nas partes necessárias para o aplicativo cliente.

Embora você possa não saber o nível de detalhes que deseja que a entidade tenha ao começar seu aplicativo, uma melhor prática é começar com uma entidade de aprendizado de máquina e, em seguida, decompô-la com subcomponentes à medida que o aplicativo amadurecer.

Em termos práticos, você criará uma entidade de aprendizado de máquina para representar um pedido em um aplicativo de pizza. O pedido deve ter todas as partes necessárias para cumprir o pedido. Para começar, a entidade extrairá o texto relacionado ao pedido, o tamanho de extração e a quantidade.

Um enunciado para `Please deliver one large cheese pizza to me` deve extrair `one large cheese pizza` como o pedido e, em seguida, extrair também `1` e `large`.

Há uma decomposição adicional que você pode adicionar, como a criação de subcomponentes para coberturas ou massas. Após este tutorial, você deverá se sentir confiante para adicionar esses subcomponentes à entidade `Order` existente.

## <a name="import-example-json-to-begin-app"></a>Importar .json de exemplo para começar o aplicativo

1.  Baixe e salve o [arquivo JSON do aplicativo](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-intents-only.json).

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="label-text-as-entities-in-example-utterances"></a>Rotular o texto como entidades nos enunciados de exemplo

Para extrair os detalhes sobre um pedido de pizza, crie uma entidade `Order` de aprendizado de máquina de nível superior.

1. Na página **Intenções**, selecione a intenção **OrderPizza**.

1. Na lista de enunciados de exemplo, selecione o enunciado a seguir.

    |Enunciado de exemplo de pedido|
    |--|
    |`pickup a cheddar cheese pizza large with extra anchovies`|

    Comece a seleção um pouco antes do texto mais à esquerda de `pickup` (nº 1) e, em seguida, vá um pouco além do texto mais à direita, `anchovies` (nº 2 – isso encerra o processo de rotulagem). Um menu pop-up é exibido. Na caixa pop-up, insira o nome da entidade como `Order` (nº 3). Em seguida, selecione `Order - Create new entity` na lista (nº 4).

    ![Rotular o início e o fim do texto do pedido concluído](media/tutorial-machine-learned-entity/mark-complete-order.png)

    > [!NOTE]
    > Uma entidade nem sempre será o enunciado inteiro. Nesse caso específico, `pickup` indica como o pedido deve ser recebido. Do ponto de vista conceitual, `pickup` deve fazer parte da entidade rotulada para o pedido.

1. Na caixa **Escolher um tipo de entidade**, selecione **Adicionar Estrutura** e, em seguida, selecione **Avançar**. A estrutura é necessária para adicionar subcomponentes como tamanho e quantidade.

    ![Adicionar uma estrutura à entidade](media/tutorial-machine-learned-entity/add-structure-to-entity.png)

1. Na caixa **Criar uma entidade de aprendizado de máquina**, na caixa **Estrutura**, adicione `Size` e, em seguida, selecione Enter.
1. Para adicionar um **descritor**, selecione o `+` na área **Descritores de Tamanho** e, em seguida, selecione **Criar lista de frases**.

1. Na caixa **Criar descritor de lista de frases**, insira o nome `SizeDescriptor` e, em seguida, insira os valores `small`, `medium` e `large`. Quando a caixa **Sugestões** for preenchida, selecione `extra large` e `xl`. Selecione **Concluído** para criar a nova lista de frases.

    Esse descritor de lista de frases ajuda o subcomponente `Size` a localizar palavras relacionadas ao tamanho, fornecendo a ele palavras de exemplo. Essa lista não precisa incluir todas as palavras de tamanho, mas deve incluir palavras que precisam indicar o tamanho.

    ![Criar um descritor para o subcomponente de tamanho](media/tutorial-machine-learned-entity/size-entity-size-descriptor-phrase-list.png)

1. Na janela **Criar uma entidade de aprendizado de máquina**, selecione **Criar** para concluir a criação do subcomponente `Size`.

    A entidade `Order` com um componente `Size` é criada, mas apenas a entidade `Order` foi aplicada ao enunciado. Você precisa rotular o texto da entidade `Size` no enunciado de exemplo.

1. No mesmo enunciado de exemplo, rotule o subcomponente de **Tamanho** de `large` selecionando a palavra e, em seguida, selecionando a entidade **Tamanho** na lista suspensa.

    ![Rotule a entidade de tamanho para o texto no enunciado.](media/tutorial-machine-learned-entity/mark-and-create-size-entity.png)

    A linha está sólida abaixo do texto porque a rotulagem e a previsão correspondem, pois você rotulou explicitamente o texto.

1. Rotule a entidade `Order` nos enunciados restantes junto com a entidade de tamanho. Os colchetes no texto indicam a entidade `Order` rotulada e a entidade `Size` na parte interna.

    |Enunciados de exemplo de pedido|
    |--|
    |`can i get [a pepperoni pizza and a can of coke] please`|
    |`can i get [a [small] pizza with onions peppers and olives]`|
    |`[delivery for a [small] pepperoni pizza]`|
    |`i need [2 [large] cheese pizzas 6 [large] pepperoni pizzas and 1 [large] supreme pizza]`|

    ![Crie a entidade e os subcomponentes em todos os enunciados de exemplo restantes.](media/tutorial-machine-learned-entity/entity-subentity-labeled-not-trained.png)

    > [!CAUTION]
    > Como tratar dados implícitos, como a letra `a` implicando em uma única pizza? Ou a falta de `pickup` e `delivery` para indicar em que local a pizza é esperada? Ou a falta de um tamanho para indicar o tamanho padrão, pequeno ou grande? Considere tratados os dados implícitos como parte de suas regras empresariais no aplicativo cliente, em vez de ou além de no LUIS.

1. Para treinar o aplicativo, selecione **Treinar**. O treinamento aplica as alterações, como as novas entidades e os enunciados rotulados, ao modelo ativo.

1. Após o treinamento, adicione um novo enunciado de exemplo à intenção para entender o nível de entendimento do LUIS da entidade de aprendizado de máquina.

    |Enunciado de exemplo de pedido|
    |--|
    |`pickup XL meat lovers pizza`|

    A entidade superior geral, `Order`, é rotulada e o subcomponente `Size` também é rotulado com linhas pontilhadas. Essa é uma previsão bem-sucedida.

    ![Novo enunciado de exemplo previsto com a entidade](media/tutorial-machine-learned-entity/new-example-utterance-predicted-with-entity.png)

    A linha pontilhada indica a previsão.

1. Para alterar a previsão para uma entidade rotulada, selecione a linha e, em seguida, selecione **Confirmar previsões de entidade**.

    ![Aceite a previsão selecionando Confirmar previsão de entidade.](media/tutorial-machine-learned-entity/confirm-entity-prediction-for-new-example-utterance.png)

    Neste ponto, a entidade de aprendizado de máquina está funcionando porque pode encontrar a entidade em um novo enunciado de exemplo. Ao adicionar o enunciado de exemplo, se a entidade não for prevista corretamente, rotule a entidade e os subcomponentes. Se a entidade for prevista corretamente, você precisará confirmar as previsões.

## <a name="add-prebuilt-number-to-help-extract-data"></a>Adicionar um número predefinido para ajudar a extrair dados

As informações do pedido também devem incluir a quantidade de um determinado item no pedido, como a quantidade de pizzas. Para extrair esses dados, um novo subcomponente de aprendizado de máquina precisa ser adicionado ao `Order` e esse componente precisa de uma restrição de um número predefinido. Ao restringir a entidade a um número predefinido, a entidade encontrará e extrairá números se o texto for um dígito, `2`, ou um texto, `two`.

Comece adicionando a entidade de número predefinido ao aplicativo.

1. Selecione **Entidades** no menu à esquerda e, em seguida, selecione **+ Adicionar entidade predefinida**.

1. Na caixa **Adicionar entidades predefinidas**, pesquise e selecione **número** e, em seguida, selecione **Concluído**.

    ![Adicionar entidade pré-compilada](media/tutorial-machine-learned-entity/add-prebuilt-entity-as-constraint-to-quantity-subcomponent.png)

    A entidade predefinida é adicionada ao aplicativo, mas ainda não é uma restrição.

## <a name="create-subcomponent-entity-with-constraint-to-help-extract-data"></a>Criar uma entidade de subcomponente com restrição para ajudar a extrair dados

A entidade `Order` deve ter um subcomponente `Quantity` para determinar a quantidade de um item no pedido. A quantidade deve ser restrita a um número, de modo que os dados extraídos sejam imediatamente utilizáveis pelo aplicativo cliente.

Uma restrição é aplicada como uma correspondência de texto, seja com a correspondência exata (como uma entidade de lista) ou por meio de expressões regulares (como uma entidade de expressão regular ou uma entidade predefinida).

Usando uma restrição, somente o texto que corresponde a essa restrição é extraído.

1. Selecione **Entidades** e, em seguida, a entidade `Order`.
1. Selecione **+ Adicionar Componente** e, em seguida, insira o nome `Quantity` e selecione Enter para adicionar a nova entidade ao aplicativo.
1. Após a notificação de êxito, selecione o subcomponente `Quantity` e, em seguida, selecione o lápis de Restrição.
1. Na lista suspensa, selecione o número predefinido.

    ![Crie uma entidade de quantidade com um número predefinido como restrição.](media/tutorial-machine-learned-entity/create-constraint-from-prebuilt-number.png)

    A entidade `Quantity` será aplicada se e somente se o texto correspondente à entidade de número predefinido for encontrado.

    A entidade com a restrição é criada, mas ainda não foi aplicada aos enunciados de exemplo.

    > [!NOTE]
    > Um subcomponente pode ser aninhado em um subcomponente em até cinco níveis. Embora isso não seja mostrado neste artigo, ele está disponível no portal e na API.

## <a name="label-example-utterance-to-teach-luis-about-the-entity"></a>Rotular o enunciado de exemplo para ensinar o LUIS sobre a entidade

1. Selecione **Intenções** no painel de navegação à esquerda e, em seguida, selecione a intenção **OrderPizza**. Os três números nos enunciados a seguir são rotulados, mas estão visualmente abaixo da linha da entidade `Order`. Esse nível inferior significa que as entidades foram encontradas, mas não são consideradas separadas da entidade `Order`.

    ![O número predefinido foi encontrado, mas ainda não é considerado separado da entidade Order.](media/tutorial-machine-learned-entity/prebuilt-number-not-part-of-order-entity.png)

1. Rotule os números com a entidade `Quantity` selecionando o `2` no enunciado de exemplo e, em seguida, selecionando `Quantity` na lista. Rotule o `6` e o `1` no mesmo enunciado de exemplo.

    ![Texto do rótulo com entidade de quantidade.](media/tutorial-machine-learned-entity/mark-example-utterance-with-quantity-entity.png)

## <a name="train-the-app-to-apply-the-entity-changes-to-the-app"></a>Treinar o aplicativo para aplicar as alterações de entidade ao aplicativo

Selecione **Treinar** para treinar o aplicativo com esses novos enunciados.

![Treine o aplicativo e examine os enunciados de exemplo.](media/tutorial-machine-learned-entity/trained-example-utterances.png)

Neste ponto, o pedido tem alguns detalhes que podem ser extraídos (tamanho, quantidade e texto do pedido total). Há um refinamento ainda maior da entidade `Order`, como coberturas da pizza, tipo de borda e acompanhamentos. Cada um deles deve ser criado como subcomponentes da entidade `Order`.

## <a name="test-the-app-to-validate-the-changes"></a>Testar o aplicativo para validar as alterações

Teste o aplicativo usando o painel **Teste** interativo. Esse processo permite que você insira um novo enunciado e exiba os resultados da previsão para ver se o aplicativo ativo e treinado está funcionando corretamente. A previsão da intenção deve ser razoavelmente confiante (acima de 70%) e a extração de entidade deve selecionar, pelo menos, a entidade `Order`. Os detalhes da entidade de pedido podem estar ausentes porque cinco enunciados não são suficientes para tratar todos os casos.

1. Selecione **Testar** na navegação superior.
1. Insira o enunciado `deliver a medium veggie pizza` e selecione Enter. O modelo ativo previu a intenção correta com mais de 70% de confiança.

    ![Insira um novo enunciado para testar a intenção.](media/tutorial-machine-learned-entity/interactive-test-panel-with-first-utterance.png)

1. Selecione **Inspecionar** para ver as previsões de entidade.

    ![Exiba as previsões de entidade no painel de teste interativo.](media/tutorial-machine-learned-entity/interactive-test-panel-with-first-utterance-and-entity-predictions.png)

    O tamanho foi identificado corretamente. Lembre-se de que o enunciado de exemplo na intenção `OrderPizza` não tem um exemplo de `medium` como tamanho, mas usa um descritor de uma lista de frases `SizeDescriptor` que inclui médio.

    A quantidade não foi prevista corretamente. Para corrigir isso, você pode adicionar mais enunciados de exemplo usando essa palavra para indicar a quantidade e rotular a palavra como uma entidade `Quantity`.

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>Publicar o aplicativo para acessá-lo por meio do ponto de extremidade HTTP

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-and-entity-prediction-from-http-endpoint"></a>Obter a previsão de intenção e de entidade do ponto de extremidade HTTP

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Vá para o final da URL no endereço e insira a mesma consulta que você inseriu no painel de teste interativo.

    `deliver a medium veggie pizza`

    O último parâmetro de querystring é `query`, o enunciado **consulta**.

    ```json
    {
        "query": "deliver a medium veggie pizza",
        "prediction": {
            "topIntent": "OrderPizza",
            "intents": {
                "OrderPizza": {
                    "score": 0.7812769
                },
                "None": {
                    "score": 0.0314020254
                },
                "Confirm": {
                    "score": 0.009299271
                },
                "Greeting": {
                    "score": 0.007551549
                }
            },
            "entities": {
                "Order": [
                    {
                        "Size": [
                            "medium"
                        ],
                        "$instance": {
                            "Size": [
                                {
                                    "type": "Size",
                                    "text": "medium",
                                    "startIndex": 10,
                                    "length": 6,
                                    "score": 0.9955588,
                                    "modelTypeId": 1,
                                    "modelType": "Entity Extractor",
                                    "recognitionSources": [
                                        "model"
                                    ]
                                }
                            ]
                        }
                    }
                ],
                "$instance": {
                    "Order": [
                        {
                            "type": "Order",
                            "text": "a medium veggie pizza",
                            "startIndex": 8,
                            "length": 21,
                            "score": 0.7983857,
                            "modelTypeId": 1,
                            "modelType": "Entity Extractor",
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


[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Informações relacionadas

* [Tutorial – intenções](luis-quickstart-intents-only.md)
* [Conceito – informações conceituais sobre entidades](luis-concept-entity-types.md)
* [Conceito – informações conceituais sobre recursos](luis-concept-feature.md)
* [Como treinar](luis-how-to-train.md)
* [Como publicar](luis-how-to-publish-app.md)
* [Como testar no portal do LUIS](luis-interactive-test.md)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, o aplicativo usa uma entidade de aprendizado de máquina para descobrir a intenção do enunciado de um usuário e extrair detalhes desse enunciado. O uso da entidade de aprendizado de máquina permite decompor os detalhes da entidade.

> [!div class="nextstepaction"]
> [Adicionar uma entidade de keyphrase predefinida](luis-quickstart-intent-and-key-phrase.md)
