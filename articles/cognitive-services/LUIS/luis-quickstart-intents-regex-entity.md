---
title: 'Tutorial: Entidade de expressão regular — LUIS'
titleSuffix: Azure Cognitive Services
description: Extrair dados formatados consistentemente de um enunciado usando a entidade de Expressão Regular.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 0f24721272c85d658a46ab1319e6f4af79b29364
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73465163"
---
# <a name="tutorial-get-well-formatted-data-from-the-utterance"></a>Tutorial: Obter dados bem formatados do enunciado
Neste tutorial, crie um aplicativo para extrair dados formatados de forma consistente de um enunciado usando a entidade de **Expressão Regular**.

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

**Neste tutorial, você aprenderá a:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Criar um novo aplicativo 
> * Adicionar intenção
> * Adicionar entidade de expressão regular 
> * Treinar
> * Publicar
> * Obter intenções e entidades do ponto de extremidade

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="regular-expression-entities"></a>Entidades de expressão regular

O uso da entidade de expressão regular por esse aplicativo destina-se a efetuar pull de números bem formatados de formulário de RH (Recursos Humanos) de um enunciado. Embora a intenção do enunciado sempre seja determinada com aprendizado de máquina, esse tipo de entidade específico não é de aprendizado de máquina. 

**Enunciados de exemplo incluem:**

|Exemplo de enunciados|
|--|
|Onde está o HRF-123456?|
|Quem criou o HRF-123234?|
|O HRF-456098 foi publicado em francês?|
|HRF-456098|
|Data do HRF-456098?|
 
Uma expressão regular é uma boa escolha para esse tipo de dados quando:

* os dados são bem formatados.


## <a name="create-a-new-app"></a>Criar um novo aplicativo

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-intent-for-finding-form"></a>Criar uma intenção para localizar o formulário

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. Selecione **Criar nova intenção**. 

1. Insira `FindForm` na caixa de diálogo pop-up, depois selecione **Concluído**. 

    ![Captura de tela da caixa de diálogo Criar nova tentativa com utilitários na caixa de pesquisa](./media/luis-quickstart-intents-regex-entity/create-new-intent-ddl.png)

1. Adicione enunciados de exemplo para a intenção.

    |Exemplo de enunciados|
    |--|
    |Qual é a URL para hrf-123456?|
    |Onde está a hrf-345678?|
    |Quando hrf-456098 foi atualizada?|
    |John Smith atualizou a hrf-234639 na semana passada?|
    |Quantas versões do hrf-345123 existem?|
    |Quem deve autorizar o formulário hrf-123456?|
    |Quantas pessoas precisam se desconectar da hrf-345678?|
    |Data da hrf-234123?|
    |Autor da hrf-546234?|
    |Título da hrf-456234?|

    [![Captura de tela da página Intenção com novos enunciados realçados](./media/luis-quickstart-intents-regex-entity/findform-intent.png)](./media/luis-quickstart-intents-regex-entity/findform-intent.png#lightbox)

    [!INCLUDE [Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]  

## <a name="use-the-regular-expression-entity-for-well-formatted-data"></a>Usar a entidade de expressão regular para dados bem formatados
A entidade de expressão regular para corresponder ao número de formulário é `hrf-[0-9]{6}`. Essa expressão regular corresponde aos caracteres literais `hrf-`, mas ignora variantes de caixa e cultura. Ela corresponde aos dígitos 0-9 de exatamente 6 dígitos.

Representa o HRF para `human resources form`.

O LUIS cria tokens do enunciado quando é adicionada a uma intenção. A geração de tokens desses enunciados adiciona espaços antes e depois do hífen `Where is HRF - 123456?` A expressão regular é aplicada ao enunciado em formato bruto antes que sejam criados tokens para ele. Por ser aplicada ao formulário _bruto_, a expressão regular não precisa lidar com limites de palavras. 

Crie uma entidade de expressão regular para informar ao LUIS o que é um formato HRF-número nas etapas a seguir:

1. Selecione **Entidades** no painel esquerdo.

1. Selecione o botão **Criar nova entidade** na página Entidades. 

1. Na caixa de diálogo pop-up, insira o novo nome de entidade `HRF-number`, selecione **RegEx** como o tipo de entidade, insira `hrf-[0-9]{6}` como o **Regex** e, depois, selecione **Concluído**.

    ![Captura de tela com caixa de diálogo pop-up configurando as propriedades da entidade](./media/luis-quickstart-intents-regex-entity/create-regex-entity.png)

1. Selecione **Intenções** no menu à esquerda, em seguida, a intenção **FindForm** para ver a expressão regular rotulada nos enunciados. 

    [![Captura de tela do enunciado Rótulo com um padrão existente de entidade e regex](./media/luis-quickstart-intents-regex-entity/labeled-utterances-for-entity.png)](./media/luis-quickstart-intents-regex-entity/labeled-utterances-for-entity.png#lightbox)

    Como a entidade não é uma entidade de aprendizado de máquina, a entidade é aplicada aos enunciados e exibida no site do LUIS assim que ela é criada.

## <a name="add-example-utterances-to-the-none-intent"></a>Adicionar enunciados de exemplo à intenção None 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-before-testing-or-publishing"></a>Treinar o aplicativo antes do teste ou da publicação

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-query-from-the-endpoint"></a>Publicar o aplicativo para consulta do ponto de extremidade

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Obter a previsão de intenção e de entidade do ponto de extremidade

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Vá até o final da URL no endereço e insira o seguinte enunciado:

    `When were HRF-123456 and hrf-234567 published in the last year?`

    O último parâmetro de querystring é `q`, o enunciado **consulta**. Esse enunciado não é igual a nenhum dos enunciados rotulados, portanto, ele é um bom teste e deve retornar a intenção `FindForm` com os números de formulário de `HRF-123456` e `hrf-234567`.

    ```json
    {
      "query": "When were HRF-123456 and hrf-234567 published in the last year?",
      "topScoringIntent": {
        "intent": "FindForm",
        "score": 0.9988884
      },
      "intents": [
        {
          "intent": "FindForm",
          "score": 0.9988884
        },
        {
          "intent": "None",
          "score": 0.00204812363
        }
      ],
      "entities": [
        {
          "entity": "hrf-123456",
          "type": "HRF-number",
          "startIndex": 10,
          "endIndex": 19
        },
        {
          "entity": "hrf-234567",
          "type": "HRF-number",
          "startIndex": 25,
          "endIndex": 34
        }
      ]
    }
    ```

    Usando uma entidade de expressão regular, o LUIS extrai dados nomeados, o que é mais programaticamente útil para o aplicativo cliente recebendo a resposta JSON.


## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Informações relacionadas

* Conceitos da entidade de [expressão regular](luis-concept-entity-types.md#regular-expression-entity)
* [Como treinar](luis-how-to-train.md)
* [Como publicar](luis-how-to-publish-app.md)
* [Como testar no portal do LUIS](luis-interactive-test.md)

## <a name="next-steps"></a>Próximas etapas
Este tutorial criou uma nova intenção, adicionou exemplos de enunciados e criou uma entidade de expressão regular para extrair dados bem formatados de enunciados. Após o treinamento e a publicação do aplicativo, uma consulta para o ponto de extremidade identificou a intenção e retornou os dados extraídos.

> [!div class="nextstepaction"]
> [Saiba mais sobre a entidade de lista](luis-quickstart-intent-and-list-entity.md)

