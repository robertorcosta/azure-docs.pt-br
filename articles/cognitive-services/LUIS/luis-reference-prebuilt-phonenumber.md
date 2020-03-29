---
title: Número de telefone Entidades Pré-Construídas - LUIS
titleSuffix: Azure Cognitive Services
description: Este artigo contém informações sobre a entidade predefinida de número de telefone de LUIS (Serviço Inteligente de Reconhecimento Vocal).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 4e2c8e27c6d4195252c6a5b423fa98b2a4247182
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270465"
---
# <a name="phone-number-prebuilt-entity-for-a-luis-app"></a>Número de telefone entidade pré-construída para um aplicativo LUIS
A entidade `phonenumber` extrai uma variedade de números de telefone, incluindo o código do país. Uma vez que essa entidade já está treinada, não é necessário adicionar enunciados de exemplo ao aplicativo. A entidade `phonenumber` é compatível somente com a cultura `en-us`.

## <a name="types-of-a-phone-number"></a>Tipos de um número de telefone
`Phonenumber`é gerenciado a partir do repositório [GitHub de reconhecimentos-texto](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-PhoneNumbers.yaml)

## <a name="resolution-for-this-prebuilt-entity"></a>Resolução para esta entidade pré-construída

Os seguintes objetos de entidade são devolvidos para a consulta:

`my mobile is 1 (800) 642-7676`

#### <a name="v3-response"></a>[Resposta V3](#tab/V3)

O JSON a `verbose` seguir está `false`com o parâmetro definido para:

```json
"entities": {
    "phonenumber": [
        "1 (800) 642-7676"
    ]
}
```
#### <a name="v3-verbose-response"></a>[Resposta verbosa V3](#tab/V3-verbose)
O JSON a `verbose` seguir está `true`com o parâmetro definido para:

```json
"entities": {
    "phonenumber": [
        "1 (800) 642-7676"
    ],
    "$instance": {

        "phonenumber": [
            {
                "type": "builtin.phonenumber",
                "text": "1 (800) 642-7676",
                "startIndex": 13,
                "length": 16,
                "score": 1.0,
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

O exemplo a seguir mostra a resolução da entidade **builtin.phonenumber**.

```json
"entities": [
    {
        "entity": "1 (800) 642-7676",
        "type": "builtin.phonenumber",
        "startIndex": 13,
        "endIndex": 28,
        "resolution": {
            "score": "1",
            "value": "1 (800) 642-7676"
        }
    }
]
```
* * *

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o [ponto de extremidade de previsão V3](luis-migration-api-v3.md).

Saiba mais sobre as entidades [percentual](luis-reference-prebuilt-percentage.md), [número](luis-reference-prebuilt-number.md) e [temperatura](luis-reference-prebuilt-temperature.md).
