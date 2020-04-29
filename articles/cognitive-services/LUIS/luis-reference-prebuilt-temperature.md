---
title: Entidade predefinida de temperatura – LUIS
titleSuffix: Azure Cognitive Services
description: Este artigo contém informações sobre a entidade predefinida de temperatura de LUIS (Serviço Inteligente de Reconhecimento Vocal).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 7e2b48c6353f56ab2269a8718146cb765797adba
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "78270363"
---
# <a name="temperature-prebuilt-entity-for-a-luis-app"></a>Entidade de temperatura predefinida para um aplicativo LUIS
Temperatura extrai uma variedade de tipos de temperatura. Uma vez que essa entidade já está treinada, não é necessário adicionar enunciados de exemplo contendo temperatura ao aplicativo. A entidade de temperatura é compatível com [muitas culturas](luis-reference-prebuilt-entities.md).

## <a name="types-of-temperature"></a>Tipos de temperatura
A temperatura é gerenciada do repositório GitHub de [texto de reconhecedores](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L819)

## <a name="resolution-for-prebuilt-temperature-entity"></a>Resolução de entidade de temperatura predefinida

Os seguintes objetos de entidade são retornados para a consulta:

`set the temperature to 30 degrees`


#### <a name="v3-response"></a>[Resposta v3](#tab/V3)

O JSON a seguir é com `verbose` o parâmetro definido `false`como:

```json
"entities": {
    "temperature": [
        {
            "number": 30,
            "units": "Degree"
        }
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3, resposta detalhada](#tab/V3-verbose)
O JSON a seguir é com `verbose` o parâmetro definido `true`como:

```json
"entities": {
    "temperature": [
        {
            "number": 30,
            "units": "Degree"
        }
    ],
    "$instance": {
        "temperature": [
            {
                "type": "builtin.temperature",
                "text": "30 degrees",
                "startIndex": 23,
                "length": 10,
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
#### <a name="v2-response"></a>[Resposta v2](#tab/V2)

O exemplo a seguir mostra a resolução da entidade **builtin.temperature**.

```json
"entities": [
    {
        "entity": "30 degrees",
        "type": "builtin.temperature",
        "startIndex": 23,
        "endIndex": 32,
        "resolution": {
        "unit": "Degree",
        "value": "30"
        }
    }
]
```
* * *

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o [ponto de extremidade de previsão V3](luis-migration-api-v3.md).

Saiba mais sobre as entidades [percentual](luis-reference-prebuilt-percentage.md), [número](luis-reference-prebuilt-number.md) e [idade](luis-reference-prebuilt-age.md).
