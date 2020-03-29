---
title: Tipo de entidade de lista - LUIS
description: As entidades de lista representam um conjunto fixo e fechado de palavras relacionadas, juntamente com seus sinônimos. O LUIS não descobre valores adicionais para entidades de lista. Use o recurso Recomendado para consultar sugestões de novas palavras com base na lista atual.
ms.topic: reference
ms.date: 03/12/2020
ms.openlocfilehash: 795d16bc2e0c4223ff3ac283a72493923d3ab355
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79297230"
---
# <a name="list-entity"></a>Entidade de lista

As entidades de lista representam um conjunto fixo e fechado de palavras relacionadas, juntamente com seus sinônimos. O LUIS não descobre valores adicionais para entidades de lista. Use o recurso **Recomendado** para consultar sugestões de novas palavras com base na lista atual. Se houver mais de uma entidade de lista com o mesmo valor, cada entidade será retornada na consulta de ponto de extremidade.

Uma entidade lista não é de aprendizado de máquina. É uma correspondência exata do texto. O LUIS marca qualquer correspondência a um item em qualquer lista como uma entidade na resposta.

**A entidade é uma boa opção quando os dados de texto:**

* São um conjunto conhecido.
* Não muda muitas vezes. Se você precisa alterar a lista com frequência ou quer que a lista se expanda, uma entidade simples impulsionada com uma lista de frases é uma escolha melhor.
* O conjunto não excede os [limites](luis-boundaries.md) máximos do LUIS para esse tipo de entidade.
* O texto no enunciado é uma correspondência insensível a casos com um sinônimo ou o nome canônico. LUIS não usa a lista além do jogo. Correspondência sustal, decorre, plural e outras variações não são resolvidas com uma entidade de lista. Para gerenciar variações, considere usar um [padrão](reference-pattern-syntax.md#syntax-to-mark-optional-text-in-a-template-utterance) com a sintaxe de texto opcional.

![entidade de lista](./media/luis-concept-entities/list-entity.png)

## <a name="example-json-to-import-into-list-entity"></a>Exemplo .json para importar para entidade de lista

  Você pode importar valores para uma entidade de lista existente usando o seguinte formato .json:

  ```JSON
  [
      {
          "canonicalForm": "Blue",
          "list": [
              "navy",
              "royal",
              "baby"
          ]
      },
      {
          "canonicalForm": "Green",
          "list": [
              "kelly",
              "forest",
              "avacado"
          ]
      }
  ]
  ```

## <a name="example-json-response"></a>Resposta JSON de exemplo

Suponha que o aplicativo tem uma lista, chamada `Cities`, que permite variações de nomes de cidade que incluem a cidade do aeroporto (SEA), o código do aeroporto (SEA), o CEP (98101) e o código da área de telefone (206).

|Item de lista|Sinônimos do item|
|---|---|
|`Seattle`|`sea-tac`, `sea`, `98101`, `206`, `+1` |
|`Paris`|`cdg`, `roissy`, `ory`, `75001`, `1`, `+33`|

`book 2 tickets to paris`

Na declaração anterior, a palavra `paris` é mapeada para o item paris como parte da entidade de lista `Cities`. A entidade de lista corresponde ao nome normalizado do item, assim como aos sinônimos do item.

#### <a name="v2-prediction-endpoint-response"></a>[Resposta de ponto de extremidade de previsão V2](#tab/V2)

```JSON
  "entities": [
    {
      "entity": "paris",
      "type": "Cities",
      "startIndex": 18,
      "endIndex": 22,
      "resolution": {
        "values": [
          "Paris"
        ]
      }
    }
  ]
```

#### <a name="v3-prediction-endpoint-response"></a>[Resposta de ponto de extremidade de previsão V3](#tab/V3)


Este é o JSON se `verbose=false` estiver definido na seqüência de consultas:

```json
"entities": {
    "Cities": [
        [
            "Paris"
        ]
    ]
}
```

Este é o JSON se `verbose=true` estiver definido na seqüência de consultas:

```json
"entities": {
    "Cities": [
        [
            "Paris"
        ]
    ],
    "$instance": {
        "Cities": [
            {
                "type": "Cities",
                "text": "paris",
                "startIndex": 18,
                "length": 5,
                "modelTypeId": 5,
                "modelType": "List Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

* * *

|Objeto de dados|Nome da entidade|Valor|
|--|--|--|
|Entidade de Lista|`Cities`|`paris`|


## <a name="next-steps"></a>Próximas etapas

Neste [tutorial,](tutorial-list-entity.md)aprenda a usar uma **entidade de lista** para extrair correspondências exatas de texto de uma lista de itens conhecidos.
