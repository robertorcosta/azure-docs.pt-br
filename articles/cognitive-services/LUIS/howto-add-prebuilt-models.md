---
title: Modelos predefinidos para Reconhecimento vocal
titleSuffix: Azure Cognitive Services
description: O LUIS inclui um conjunto de modelos predefinidos para adicionar rapidamente cenários de usuário de conversação comuns.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 2e90a5f3f7cd2cfde2a2ead26674d2348a24ae6f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73507776"
---
# <a name="add-prebuilt-models-for-common-usage-scenarios"></a>Adicionar modelos predefinidos para cenários de uso comuns 

O LUIS inclui um conjunto de intenções predefinidas dos domínios predefinidos para adicionar rapidamente intenções e enunciados comuns. Essa é uma maneira rápida e fácil de adicionar habilidades ao aplicativo cliente de conversação sem a necessidade de projetar os modelos para essas habilidades. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="add-a-prebuilt-domain"></a>Adicionar um domínio predefinido

1. Na página **Meus aplicativos**, selecione o aplicativo. Isso abre o aplicativo na seção **Compilar** do aplicativo. 

1. Na página **Intenções**, selecione **Adicionar domínios predefinidos** na barra de ferramentas inferior esquerda. 

1. Selecione a intenção **Calendário** e, em seguida, selecione o botão **Adicionar domínio**.

    ![Adicionar domínio predefinido do Calendário](./media/luis-prebuilt-domains/add-prebuilt-domain.png)

1. Selecione **Intenções** na navegação esquerda para ver as intenções do Calendário. Cada intenção deste domínio é prefixada com `Calendar.`. Juntamente com enunciados, duas entidades para esse domínio são adicionadas ao aplicativo: `Calendar.Location` e `Calendar.Subject`. 

### <a name="train-and-publish"></a>Treinar e publicar

1. Depois que o domínio for adicionado, treine o aplicativo selecionando **Treinar** na barra de ferramentas superior direita. 

1. Na barra de ferramentas superior, selecione **Publicar**. Publique em **Produção**. 

1. Quando a notificação de êxito verde aparecer, selecione o link **Referir-se à lista de pontos de extremidade** para ver os pontos de extremidade.

1. Selecionar um ponto de extremidade. Uma nova guia do navegador é aberta para esse ponto de extremidade. Mantenha a guia do navegador aberta e continue na seção **Testar**.

### <a name="test"></a>Teste

Teste a nova intenção no ponto de extremidade adicionando um valor para o parâmetro **q**: `Schedule a meeting with John Smith in Seattle next week`.

O LUIS retorna a intenção correta e o assunto da reunião:

```json
{
  "query": "Schedule a meeting with John Smith in Seattle next week",
  "topScoringIntent": {
    "intent": "Calendar.Add",
    "score": 0.824783146
  },
  "entities": [
    {
      "entity": "a meeting with john smith",
      "type": "Calendar.Subject",
      "startIndex": 9,
      "endIndex": 33,
      "score": 0.484055847
    }
  ]
}
```

## <a name="add-a-prebuilt-intent"></a>Adicionar uma intenção predefinida

1. Na página **Meus aplicativos**, selecione o aplicativo. Isso abre o aplicativo na seção **Compilar** do aplicativo. 

1. Na página **Intenções**, selecione **Adicionar intenção predefinida** na barra de ferramentas acima da lista de tentativas. 

1. Selecione a intenção **Utilities.Cancel** na caixa de diálogo pop-up. 

    ![Adicionar intenção predefinida](./media/luis-prebuilt-intents/prebuilt-intents-ddl.png)

1. Selecione o botão **Concluído**.

### <a name="train-and-test"></a>Treinar e testar o aplicativo

1. Depois que a intenção for adicionada, treine o aplicativo selecionando **Treinar** na barra de ferramentas superior direita. 

1. Testar a nova intenção, selecionando **Teste** na barra de ferramentas à direita. 

1. Na caixa de texto, insira as declarações para cancelar:

    |Enunciado do teste|Pontuação de previsão|
    |--|:--|
    |Desejo cancelar meu voo.|0.67|
    |Cancelar a compra.|0.52|
    |Cancelar a reunião.|0.56|

    ![Testar intenção predefinida](./media/luis-prebuilt-intents/test.png)

## <a name="add-a-prebuilt-entity"></a>Adicionar uma entidade predefinida

1. Abra seu aplicativo clicando no nome na página **Meus Aplicativos** e, em seguida, clicando em **Entidades** no lado esquerdo. 

1. Na página **Entidades**, clique em **Adicionar entidades predefinidas**.

1. Na caixa de diálogo **Adicionar as entidades predefinidas**, selecione o datetimeV2 entidade predefinida. 

    ![Adicionar caixa de diálogo de entidade predefinida](./media/luis-use-prebuilt-entity/add-prebuilt-entity-dialog.png)

1. Selecione **Concluído**. Depois que a entidade é adicionada, você precisa treinar o aplicativo. 

## <a name="publish-to-view-prebuilt-model-from-prediction-endpoint"></a>Publicar para exibir o modelo predefinido do ponto de extremidade de previsão

A maneira mais fácil de exibir o valor de um modelo predefinido é consultar o ponto de extremidade publicado. 

## <a name="marking-entities-containing-a-prebuilt-entity-token"></a>Marcação de entidades que contêm um token de entidade pré-criado
 Se você tiver texto, como `HH-1234`, que você deseja marcar como uma entidade personalizada _e_ e tiver um [número pré-criado](luis-reference-prebuilt-number.md) adicionado ao modelo, não será possível marcar a entidade personalizada no portal do LUIS. Você pode marcá-la com a API. 

 Para marcar esse tipo de token, em que parte dele já está marcada com uma entidade pré-criada, remova a entidade pré-criada do aplicativo LUIS. Você não precisa treinar o aplicativo. Em seguida, marque o token com sua própria entidade personalizada. Em seguida, adicione a entidade pré-criada de volta ao aplicativo LUIS.

 Para outro exemplo, considere o expressão como uma lista de preferências de classe: `I want first year spanish, second year calculus, and fourth year english lit.` se o aplicativo LUIS tiver o ordinal de compilação adicionado, `first`, `second`e `fourth` já serão marcados com ordinais. Se você deseja capturar o ordinal e a classe, pode criar uma entidade de composição e encapsulá-la no ordinal pré-criado e na entidade personalizada para o nome de classe.

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Compilar modelo a partir de. csv com APIs REST](./luis-tutorial-node-import-utterances-csv.md)
