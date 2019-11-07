---
title: 'Tutorial: Correspondência exata do texto – LUIS'
titleSuffix: Azure Cognitive Services
description: Obtenha dados que correspondam a uma lista predefinida de itens. Cada item na lista pode ter sinônimos que também tenham correspondência exata
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 9531e86e63fa8d944aa216fddd0c8215b73038a9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73492707"
---
# <a name="tutorial-get-exact-text-matched-data-from-an-utterance"></a>Tutorial: Obter dados de correspondência de texto exata de uma expressão

Neste tutorial, entenda como obter dados de entidade que correspondam a uma lista predefinida de itens. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

**Neste tutorial, você aprenderá a:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Criar aplicativo
> * Adicionar intenção
> * Adicionar listar de entidades 
> * Treinar 
> * Publicar
> * Obter intenções e entidades do ponto de extremidade

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="what-is-a-list-entity"></a>O que é uma entidade de lista?

Uma entidade de lista é uma correspondência exata do texto com as palavras na declaração. 

Cada item na lista pode incluir uma lista de sinônimos. Para o aplicativo de recursos humanos, um departamento da empresa pode ser identificado por várias partes importantes de informações como um nome oficial, acrônimos comuns e códigos de departamento de cobrança. 

O aplicativo de recursos humanos precisa determinar o departamento ao qual um funcionário está sendo transferido. 

Uma entidade de lista é uma boa escolha para esse tipo de dados quando:

* Os valores de dados são um conjunto conhecido.
* O conjunto não excede os [limites](luis-boundaries.md) máximos do LUIS para esse tipo de entidade.
* O texto no enunciado corresponde exatamente a um sinônimo ou ao nome canônico. O LUIS não usa a lista além de correspondências exatas do texto. Lematização, plurais e outras variações não são resolvidas com apenas uma entidade de lista. Para gerenciar variações, considere a possibilidade de usar um [padrão](luis-concept-patterns.md#syntax-to-mark-optional-text-in-a-template-utterance) com a sintaxe de texto opcional. 

## <a name="create-a-new-app"></a>Criar um novo aplicativo

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-an-intent-to-transfer-employees-to-a-different-department"></a>Criar uma intenção para transferir os funcionários para um outro departamento

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Selecione **Criar nova intenção**. 

3. Insira `TransferEmployeeToDepartment` na caixa de diálogo pop-up, depois selecione **Concluído**. 

    ![Captura de tela do diálogo Criar nova intenção com](./media/luis-quickstart-intent-and-list-entity/hr-create-new-intent-ddl.png)

4. Adicione enunciados de exemplo para a intenção.

    |Exemplo de enunciados|
    |--|
    |mover John W. Smith para o departamento de contabilidade|
    |transferir de Jill Jones para P e D|
    |O dept. 1234 tem um novo membro denominado Bill Bradstreet|
    |Colocar John Jackson em engenharia |
    |transferir Debra Doughtery para vendas internas|
    |transferir Jill Jones para TI|
    |Mudar Alice Anderson para DevOps|
    |Carl Chamerlin para finanças|
    |Steve Standish para 1234|
    |Tanner Thompson para 3456|

    [![Captura de tela de intenção com exemplos de enunciados](media/luis-quickstart-intent-and-list-entity/intent-transfer-employee-to-department.png "Captura de tela de intenção com exemplos de enunciados")](media/luis-quickstart-intent-and-list-entity/intent-transfer-employee-to-department.png#lightbox)

    [!INCLUDE [Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]  

## <a name="department-list-entity"></a>Entidade de lista de departamento

Agora que a intenção **TransferEmployeeToDepartment** tem enunciados de exemplo, o LUIS precisa entender o que é um departamento. 

O nome primário _canônico_ para cada item é o nome do departamento. Exemplos dos sinônimos de cada nome canônico são: 

|Nome canônico|Sinônimos|
|--|--|
|Contabilidade|cont<br>ctbl<br>3456|
|Operações de desenvolvimento|Devops<br>4949|
|Engenharia|eng<br>engnr<br>4567|
|Finanças|fin<br>2020|
|Tecnologia da informação|IT<br>2323|
|Vendas internas|vint<br>vendint<br>1414|
|Pesquisa e desenvolvimento|P e D<br>1234|

1. Selecione **Entidades** no painel esquerdo.

1. Selecione **Criar nova entidade**.

1. No diálogo pop-up da entidade, insira `Department` como o nome de entidade e **List** como o tipo de entidade. Selecione **Concluído**.  

    [![Captura de tela do diálogo pop-up de criação de nova entidade](media/luis-quickstart-intent-and-list-entity/create-new-list-entity-named-department.png "Captura de tela do diálogo pop-up de criação de nova entidade")](media/luis-quickstart-intent-and-list-entity/create-new-list-entity-named-department.png#lightbox)

1. Na página da entidade Departamento, digite `Accounting` como o novo valor.

1. Para sinônimos, adicione os sinônimos da tabela anterior.

1. Continue adicionando todos os nomes canônicos e seus sinônimos. 

## <a name="add-example-utterances-to-the-none-intent"></a>Adicionar enunciados de exemplo à intenção None 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Treinar o aplicativo para que as alterações à intenção possam ser testadas 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Publicar o aplicativo para que o modelo em que foi feito o treinamento possa ser consultado por meio do ponto de extremidade

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Obter a previsão de intenção e de entidade do ponto de extremidade

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

1. Vá até o final da URL no endereço e insira `shift Joe Smith to IT`. O último parâmetro de querystring é `q`, o enunciado **q**uery. Esse enunciado não é igual a nenhum dos enunciados rotulados, portanto, é um bom teste e deve retornar a intenção `TransferEmployeeToDepartment` com `Department` extraído.

   ```json
    {
      "query": "shift Joe Smith to IT",
      "topScoringIntent": {
        "intent": "TransferEmployeeToDepartment",
        "score": 0.9775754
      },
      "intents": [
        {
          "intent": "TransferEmployeeToDepartment",
          "score": 0.9775754
        },
        {
          "intent": "None",
          "score": 0.0154493852
        }
      ],
      "entities": [
        {
          "entity": "it",
          "type": "Department",
          "startIndex": 19,
          "endIndex": 20,
          "resolution": {
            "values": [
              "Information Technology"
            ]
          }
        }
      ]
    }
   ```

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Informações relacionadas

* Informações conceituais de [entidade de lista](luis-concept-entity-types.md#list-entity)
* [Como treinar](luis-how-to-train.md)
* [Como publicar](luis-how-to-publish-app.md)
* [Como testar no portal do LUIS](luis-interactive-test.md)


## <a name="next-steps"></a>Próximas etapas
Este tutorial criou uma nova intenção, adicionou exemplos de enunciados e criou uma entidade de lista para extrair correspondências de texto de enunciados. Após o treinamento e a publicação do aplicativo, uma consulta para o ponto de extremidade identificou a intenção e retornou os dados extraídos.

Continue com este aplicativo, [adicionando uma entidade composta](luis-tutorial-composite-entity.md).

> [!div class="nextstepaction"]
> [Adicionar entidade predefinida com uma função ao aplicativo](tutorial-entity-roles.md)

