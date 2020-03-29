---
title: Entidade pré-construída Ordinal V2 - LUIS
titleSuffix: Azure Cognitive Services
description: Este artigo contém informações de entidades ordinal V2 pré-construídas no Entendimento linguístico (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 5e852313db75e598da647ea0f985e2ee18af16de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270497"
---
# <a name="ordinal-v2-prebuilt-entity-for-a-luis-app"></a>Entidade pré-construída Ordinal V2 para um aplicativo LUIS
O número Ordinal V2 expande [o Ordinal](luis-reference-prebuilt-ordinal.md) para fornecer referências relativas, tais como `next`, `last`e `previous`. Estes não são extraídos usando a entidade ordinal pré-construída.

## <a name="resolution-for-prebuilt-ordinal-v2-entity"></a>Resolução para entidade V2 ordinal pré-construída

Os seguintes objetos de entidade são devolvidos para a consulta:

`what is the second to last choice in the list`

#### <a name="v3-response"></a>[Resposta V3](#tab/V3)

O JSON a `verbose` seguir está `false`com o parâmetro definido para:

```json
"entities": {
    "ordinalV2": [
        {
            "offset": -1,
            "relativeTo": "end"
        }
    ]
}
```

#### <a name="v3-verbose-response"></a>[Resposta verbosa V3](#tab/V3-verbose)

O JSON a `verbose` seguir está `true`com o parâmetro definido para:

```json
"entities": {
    "ordinalV2": [
        {
            "offset": -1,
            "relativeTo": "end"
        }
    ],
    "$instance": {
        "ordinalV2": [
            {
                "type": "builtin.ordinalV2.relative",
                "text": "the second to last",
                "startIndex": 8,
                "length": 18,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```
#### <a name="v2-response"></a>[Resposta V2](#tab/V2)

O exemplo a seguir mostra a resolução da entidade **builtin.ordinalV2.**

```json
"entities": [
    {
        "entity": "the second to last",
        "type": "builtin.ordinalV2.relative",
        "startIndex": 8,
        "endIndex": 25,
        "resolution": {
            "offset": "-1",
            "relativeTo": "end"
        }
    }
]
```
* * *

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o [ponto de extremidade de previsão V3](luis-migration-api-v3.md).

Saiba mais sobre a [porcentagem,](luis-reference-prebuilt-percentage.md) [número de telefone](luis-reference-prebuilt-phonenumber.md)e entidades de [temperatura.](luis-reference-prebuilt-temperature.md)
