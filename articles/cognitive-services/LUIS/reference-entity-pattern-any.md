---
title: Pattern.any entity type - LUIS
titleSuffix: Azure Cognitive Services
description: Pattern.any é um espaço reservado de tamanho variável usado apenas em um enunciado de modelo para marcar onde a entidade começa e termina.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: 5164bf55ef8233cf34a470524da3bc852678d79a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "75979172"
---
# <a name="patternany-entity"></a>Entidade pattern.any

Pattern.any é um espaço reservado de tamanho variável usado apenas em um enunciado de modelo para marcar onde a entidade começa e termina.  

As entidades Pattern.any devem ser marcadas nos exemplos de modelo [Padrão](luis-how-to-model-intent-pattern.md), não exemplos de usuários de intenção.

**A entidade é uma boa opção quando:**

* O final da entidade pode ser confundido com o texto restante do enunciado.

## <a name="usage"></a>Uso

Em um determinado aplicativo cliente que pesquisa livros com base no título, o pattern.any extrai o título completo. Um enunciado de modelo usando pattern.any para essa busca de livro é `Was {BookTitle} written by an American this year[?]`.

Na tabela a seguir, cada linha tem duas versões do enunciado. O enunciado superior é como LUIS inicialmente vê a expressão. Não está claro onde o título do livro começa e termina. O enunciado inferior usa um Padrão.qualquer entidade para marcar o início e o fim da entidade.

|Pronunciamento com entidade em negrito|
|--|
|`Was The Man Who Mistook His Wife for a Hat and Other Clinical Tales written by an American this year?`<br><br>**The Man Who Mistook His Wife for a Hat and Other Clinical Tales** foi escrito por um americano este ano?|
|`Was Half Asleep in Frog Pajamas written by an American this year?`<br><br>**Half Asleep in Frog Pajamas** foi escrito por uma americano este ano?|
|`Was The Particular Sadness of Lemon Cake: A Novel written by an American this year?`<br><br>Foi **A Tristeza Particular do Bolo de Limão: Um Romance** escrito por um americano este ano?|
|`Was There's A Wocket In My Pocket! written by an American this year?`<br><br>Havia **um wocket no meu bolso!** escrito por um americano este ano?|
||



## <a name="example-json"></a>JSON de exemplo

Considere a consulta a seguir.

`where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`

Com o nome do formulário incorporado para extrair como um Padrão.any:

`Understand your responsibilities as a member of the community`

#### <a name="v2-prediction-endpoint-response"></a>[Resposta de ponto de extremidade de previsão V2](#tab/V2)

```JSON
"entities": [
  {
    "entity": "understand your responsibilities as a member of the community",
    "type": "FormName",
    "startIndex": 18,
    "endIndex": 78,
    "role": ""
  }
```


#### <a name="v3-prediction-endpoint-response"></a>[Resposta de ponto de extremidade de previsão V3](#tab/V3)

Este é o JSON se `verbose=false` estiver definido na seqüência de consultas:

```json
"entities": {
    "FormName": [
        "Understand your responsibilities as a member of the community"
    ]
}
```

Este é o JSON se `verbose=true` estiver definido na seqüência de consultas:

```json
"entities": {
    "FormName": [
        "Understand your responsibilities as a member of the community"
    ],
    "$instance": {
        "FormName": [
            {
                "type": "FormName",
                "text": "Understand your responsibilities as a member of the community",
                "startIndex": 18,
                "length": 61,
                "modelTypeId": 7,
                "modelType": "Pattern.Any Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

* * *

## <a name="next-steps"></a>Próximas etapas

Neste [tutorial,](luis-tutorial-pattern.md)use a entidade **Pattern.any** para extrair dados de expressões onde as expressões são bem formatadas e onde o final dos dados pode ser facilmente confundido com as palavras restantes do enunciado.
