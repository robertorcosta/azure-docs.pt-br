---
title: 'Tutorial: Análise de sentimento – LUIS'
titleSuffix: Azure Cognitive Services
description: Neste tutorial, crie um aplicativo que demonstra como obter o sentimento positivo, negativo e neutro de enunciados. O sentimento é determinado pelo enunciado inteiro.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 07afd197e514adb0f2fc65c11e9fec552aa05b99
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73492654"
---
# <a name="tutorial--get-sentiment-of-utterance"></a>Tutorial:  Obter o sentimento do enunciado

Neste tutorial, crie um aplicativo que demonstre como determinar o sentimento positivo, negativo e neutro dos enunciados. O sentimento é determinado pelo enunciado inteiro.

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

**Neste tutorial, você aprenderá a:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Criar um novo aplicativo
> * Adicionar a análise de sentimento como uma configuração de publicação
> * Treinar o aplicativo
> * Publicar aplicativo
> * Obter o sentimento do enunciado do ponto de extremidade

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="sentiment-analysis-is-a-publish-setting"></a>A análise de sentimento é uma configuração de publicação

Os enunciados a seguir mostram exemplos de sentimentos:

|Sentimento|Pontuação|Enunciado|
|:--|:--|:--|
|positivo|0.91 |John W. Smith fez um ótimo trabalho na apresentação em Paris.|
|positivo|0.84 |Os engenheiros de Seattle fizeram um trabalho incrível na apresentação de vendas da Parker.|

A análise de sentimento é uma configuração de publicação que se aplica a cada enunciado. Não é necessário localizar as palavras que indicam o sentimento no enunciado e marcá-las. 

Como se trata de uma configuração de publicação, ela não aparece nas páginas de intenções ou de entidades. Ela aparece no painel [teste interativo](luis-interactive-test.md#view-sentiment-results) ou ao testar a URL de ponto de extremidade. 


## <a name="create-a-new-app"></a>Criar um novo aplicativo

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="add-personname-prebuilt-entity"></a>Adicionar a entidade predefinida PersonName 

1. Selecione **Criar** no menu de navegação.

1. Selecione **Entidades** no menu de navegação à esquerda.

1. Selecione o botão **Adicionar entidade predefinida**.

1. Escolha a entidade a seguir na lista de entidades predefinidas e selecione **Pronto**:

   * **[PersonName](luis-reference-prebuilt-person.md)** 

     ![Captura de tela do número selecionado no diálogo de entidades predefinidas](./media/luis-quickstart-intent-and-sentiment-analysis/add-personname-prebuilt-entity.png)

## <a name="create-an-intent-to-determine-employee-feedback"></a>Criar uma intenção para determinar os comentários do funcionário

Adicione uma nova intenção para capturar comentários do funcionário entre os membros da empresa. 

1. Selecione **Intenções** no painel esquerdo.

1. Selecione **Criar nova intenção**.

1. Insira o nome da nova intenção `EmployeeFeedback`.

    ![Criar nova caixa de diálogo de intenção com EmployeeFeedback como nome](./media/luis-quickstart-intent-and-sentiment-analysis/hr-create-new-intent-ddl.png)

4. Adicione vários enunciados que indicam que um funcionário está fazendo algo bem ou uma área que precise de melhorias:

    |Declarações|
    |--|
    |Guilherme Sarmento fez um ótimo trabalho de boas-vindas para uma colega de trabalho que estava de licença-maternidade|
    |Samuel Ferreira fez um ótimo trabalho confortando um colega de trabalho que estava de luto.|
    |Bob Barnes não tinha todas as faturas necessárias para a documentação.|
    |Tiago Ribeiro entregou os formulários necessários com um mês de atraso, sem nenhuma assinatura|
    |Clara Barbosa não compareceu à importante reunião externa de marketing.|
    |Mila Moraes perdeu as reuniões de revisão de junho.|
    |Julio Pinto arrasou na apresentação de vendas em Harvard|
    |Valerio Pereira fez um bom trabalho na apresentação em Stanford|

    Selecione as **Opções de Exibição**, selecione **Exibir valores de entidade** para ver os nomes.

    [![Captura de tela do aplicativo LUIS com enunciados de exemplo na intenção EmployeeFeedback](./media/luis-quickstart-intent-and-sentiment-analysis/hr-utterance-examples.png)](./media/luis-quickstart-intent-and-sentiment-analysis/hr-utterance-examples.png#lightbox)

## <a name="add-example-utterances-to-the-none-intent"></a>Adicionar enunciados de exemplo à intenção None 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Treinar o aplicativo para que as alterações à intenção possam ser testadas 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="configure-app-to-include-sentiment-analysis"></a>Configurar aplicativo para incluir a análise de sentimento

1. Selecione **Gerenciar** na navegação superior à direita, em seguida, selecione **Configurações de publicação** no menu à esquerda.

1. Selecione **Usar análise de sentimento para determinar se o enunciado de um usuário é positivo, negativo ou neutro.** para habilitar essa configuração. 

    ![Ativar Análise de Sentimento como configuração de publicação](./media/luis-quickstart-intent-and-sentiment-analysis/turn-on-sentiment-analysis-as-publish-setting.png)

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Publicar o aplicativo para que o modelo treinado possa ser consultado por meio do ponto de extremidade

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-the-sentiment-of-an-utterance-from-the-endpoint"></a>Obter o sentimento de um enunciado do ponto de extremidade

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

1. Vá até o final da URL no endereço e insira o seguinte enunciado:

    `Jill Jones work with the media team on the public portal was amazing` 

    O último parâmetro de querystring é `q`, o enunciado **consulta**. Esse enunciado não é igual a nenhum dos enunciados rotulados, portanto, ele é um bom teste e deve retornar a intenção `EmployeeFeedback` com a extração da análise de sentimento.
    
    ```json
    {
      "query": "Jill Jones work with the media team on the public portal was amazing",
      "topScoringIntent": {
        "intent": "EmployeeFeedback",
        "score": 0.9616192
      },
      "intents": [
        {
          "intent": "EmployeeFeedback",
          "score": 0.9616192
        },
        {
          "intent": "None",
          "score": 0.09347677
        }
      ],
      "entities": [
        {
          "entity": "jill jones",
          "type": "builtin.personName",
          "startIndex": 0,
          "endIndex": 9
        }
      ],
      "sentimentAnalysis": {
        "label": "positive",
        "score": 0.8694164
      }
    }
    ```

    O sentimentAnalysis é positivo com uma pontuação de 86%. 

    Tente outro enunciado removendo o valor para `q` na barra de endereços do navegador: `William Jones did a terrible job presenting his ideas.` A pontuação de sentimento indica um sentimento negativo retornando uma pontuação baixa `0.18597582`.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Informações relacionadas

* A Análise de Sentimento é fornecida pelo serviço cognitivo [Análise de Texto](../Text-Analytics/index.yml). O recurso é restrito aos [idiomas com suporte](luis-language-support.md##languages-supported) da Análise de Texto.
* [Como treinar](luis-how-to-train.md)
* [Como publicar](luis-how-to-publish-app.md)
* [Como testar no portal do LUIS](luis-interactive-test.md)


## <a name="next-steps"></a>Próximas etapas
Este tutorial adiciona a análise de sentimento como uma configuração de publicação para extrair valores de sentimento do enunciado como um todo.

> [!div class="nextstepaction"] 
> [Examinar enunciados de ponto de extremidade no aplicativo de RH](luis-tutorial-review-endpoint-utterances.md) 

