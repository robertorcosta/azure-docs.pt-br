---
title: Tipo de entidade composta - LUIS
titleSuffix: Azure Cognitive Services
description: Uma entidade composta é composta por outras entidades, como entidades pré-construídas, simples, expressão regular e entidades de lista. As entidades separadas formam uma entidade inteira.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: a5a1ad467074ee0aa55d14d50ae153ac68304e6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "71695162"
---
# <a name="composite-entity"></a>Entidade composta 

Uma entidade composta é composta por outras entidades, como entidades pré-construídas, simples, expressão regular e entidades de lista. As entidades separadas formam uma entidade inteira. 

**Essa entidade é uma boa opção quando os dados:**

* Estão relacionados uns aos outros. 
* Estão relacionadas entre si no contexto do enunciado.
* Usam uma variedade de tipos de entidade.
* Precisam ser agrupados e processados pelo aplicativo cliente como uma unidade de informação.
* Tenham uma variedade de enunciados de usuário que exigem aprendizado de máquina.

![entidade composta](./media/luis-concept-entities/composite-entity.png)

## <a name="example-json"></a>JSON de exemplo

Considere uma entidade composta `number` `Location::ToLocation` de pré-construída e com a seguinte expressão:

`book 2 tickets to cairo`

Observe que `2`, o número e `cairo`, o ToLocation tem palavras entre eles que não fazem parte de nenhuma entidade. O sublinhado verde, usado em uma declaração rotulada no site do [LUIS](luis-reference-regions.md), indica uma entidade composta.

![Entidade composta](./media/luis-concept-data-extraction/composite-entity.png)

#### <a name="v2-prediction-endpoint-response"></a>[Resposta de ponto de extremidade de previsão V2](#tab/V2)

Entidades compostas são retornadas em uma matriz `compositeEntities` e todas as entidades com a composta também são retornadas na matriz `entities`:

```JSON
  "entities": [
    {
      "entity": "2 tickets to cairo",
      "type": "ticketinfo",
      "startIndex": 5,
      "endIndex": 22,
      "score": 0.9214487
    },
    {
      "entity": "cairo",
      "type": "builtin.geographyV2.city",
      "startIndex": 18,
      "endIndex": 22
    },
    {
      "entity": "2",
      "type": "builtin.number",
      "startIndex": 5,
      "endIndex": 5,
      "resolution": {
        "subtype": "integer",
        "value": "2"
      }
    }
  ],
  "compositeEntities": [
    {
      "parentType": "ticketinfo",
      "value": "2 tickets to cairo",
      "children": [
        {
          "type": "builtin.number",
          "value": "2"
        },
        {
          "type": "builtin.geographyV2.city",
          "value": "cairo"
        }
      ]
    }
  ]
```    

#### <a name="v3-prediction-endpoint-response"></a>[Resposta de ponto de extremidade de previsão V3](#tab/V3)

Este é o JSON se `verbose=false` estiver definido na seqüência de consultas:

```json
"entities": {
    "ticketinfo": [
        {
            "number": [
                2
            ],
            "geographyV2": [
                "cairo"
            ]
        }
    ]
}
```

Este é o JSON se `verbose=true` estiver definido na seqüência de consultas:

```json
"entities": {
    "ticketinfo": [
        {
            "number": [
                2
            ],
            "geographyV2": [
                "cairo"
            ],
            "$instance": {
                "number": [
                    {
                        "type": "builtin.number",
                        "text": "2",
                        "startIndex": 5,
                        "length": 1,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ],
                "geographyV2": [
                    {
                        "type": "builtin.geographyV2.city",
                        "text": "cairo",
                        "startIndex": 18,
                        "length": 5,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ]
            }
        }
    ],
    "$instance": {
        "ticketinfo": [
            {
                "type": "ticketinfo",
                "text": "2 tickets to cairo",
                "startIndex": 5,
                "length": 18,
                "score": 0.9214487,
                "modelTypeId": 4,
                "modelType": "Composite Entity Extractor",
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
|Entidade predefinida – número|"builtin.number"|"2"|
|Entidade Pré-Construída - GeografiaV2|"Location::ToLocation"|"Cairo"|

## <a name="next-steps"></a>Próximas etapas

Neste [tutorial,](luis-tutorial-composite-entity.md)adicione uma **entidade composta** para agrupar dados extraídos de vários tipos em uma única entidade contendo. Agrupando os dados, o aplicativo cliente poderá extrair com facilidade dados relacionados em diferentes tipos de dados.
