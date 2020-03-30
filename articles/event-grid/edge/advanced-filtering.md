---
title: Filtragem avançada - Azure Event Grid IoT Edge | Microsoft Docs
description: Filtragem avançada em Event Grid em IoT Edge.
author: HiteshMadan
manager: rajarv
ms.author: himad
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: d7fdc5074f3c92eea4f236a9b1f7c823b930f391
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72992555"
---
# <a name="advanced-filtering"></a>Filtragem avançada
Event Grid permite especificar filtros em qualquer propriedade na carga útil json. Estes filtros são modelados como conjunto de `AND` condições, com cada condição externa tendo condições internas `OR` opcionais. Para `AND` cada condição, você especifica os seguintes valores:

* `OperatorType`- O tipo de comparação.
* `Key`- O caminho json para a propriedade em que aplicar o filtro.
* `Value`- O valor de referência contra o `Values` qual o filtro é executado (ou) - O conjunto de valores de referência contra os quais o filtro é executado.

## <a name="json-syntax"></a>Sintaxe JSON

A sintaxe JSON para um filtro avançado é a seguinte:

```json
{
    "filter": {
        "advancedFilters": [{
                "operatorType": "NumberGreaterThanOrEquals",
                "key": "Data.Key1",
                "value": 5
            }, {
                "operatorType": "StringContains",
                "key": "Subject",
                "values": ["container1", "container2"]
            }
        ]
    }
}
```

## <a name="filtering-on-array-values"></a>Filtragem de valores de matriz

Event Grid não suporta filtragem em uma matriz de valores hoje. Se um evento de entrada tiver um valor de matriz para a chave do filtro avançado, a operação correspondente falhará. O evento de entrada acaba não correspondendo com a assinatura do evento.

## <a name="and-or-not-semantics"></a>E-OU-NÃO semântica

Observe que no exemplo json `AdvancedFilters` dado anteriormente, é uma matriz. Pense em `AdvancedFilter` cada elemento `AND` da matriz como uma condição.

Para os operadores que suportam `NumberIn` `NumberNotIn`múltiplos valores (como , `StringIn` `OR` , , etc.), cada valor é tratado como uma condição. Assim, um `StringBeginsWith("a", "b", "c")` vai corresponder a qualquer `a` valor `b` `c`de string que começa com um ou ou ou .

> [!CAUTION]
> Os operadores `NumberNotIn` NOT `StringNotIn` - e se comportam como `Values` e condições em cada valor dado no campo.
>
> Não fazê-lo fará do filtro um filtro Aceitar-Tudo e derrotará o propósito de filtrar.

## <a name="floating-point-rounding-behavior"></a>Comportamento de arredondamento de ponto flutuante

Event Grid `decimal` usa o tipo .NET para lidar com todos os valores numéricos. Os valores numéres especificados na assinatura do evento JSON não estão sujeitos ao comportamento de arredondamento de ponto flutuante.

## <a name="case-sensitivity-of-string-filters"></a>Sensibilidade do caso dos filtros de corda

Todas as comparações de cordas são insensíveis a casos. Não há como mudar esse comportamento hoje.

## <a name="allowed-advanced-filter-keys"></a>Permite chaves de filtro avançadas

A `Key` propriedade pode ser uma propriedade de alto nível bem conhecida, ou ser um caminho json com vários pontos, onde cada ponto significa pisar em um objeto json aninhado.

Event Grid não tem nenhum significado `$` especial para o personagem na chave, ao contrário da especificação JSONPath.

### <a name="event-grid-schema"></a>Esquema da grade de eventos

Para eventos no esquema event grid:

* ID
* Tópico
* Assunto
* EventType
* DataVersion
* Data.Prop1
* Data.Prop*Prop2.Prop3.Prop4.Prop5

### <a name="custom-event-schema"></a>Esquema de eventos personalizado

Não há restrição no `Key` esquema de eventos personalizados, uma vez que a Event Grid não aplica nenhum esquema de envelope na carga.

## <a name="numeric-single-value-filter-examples"></a>Exemplos numéricos de filtro de valor único

* NumberGreaterThan
* NumberGreaterThanOrEquals
* NumberLessThan
* NumberLessThanOrEquals

```json
{
    "filter": {
        "advancedFilters": [
            {
                "operatorType": "NumberGreaterThan",
                "key": "Data.Key1",
                "value": 5
            },
            {
                "operatorType": "NumberGreaterThanOrEquals",
                "key": "Data.Key2",
                "value": *456
            },
            {
                "operatorType": "NumberLessThan",
                "key": "Data.P*P2.P3",
                "value": 1000
            },
            {
                "operatorType": "NumberLessThanOrEquals",
                "key": "Data.P*P2",
                "value": 999
            }
        ]
    }
}
```

## <a name="numeric-range-value-filter-examples"></a>Exemplos de filtro de valor de intervalo numérico

* NumberIn
* NumberNotIn

```json
{
    "filter": {
        "advancedFilters": [
            {
                "operatorType": "NumberIn",
                "key": "Data.Key1",
                "values": [1, 10, 100]
            },
            {
                "operatorType": "NumberNotIn",
                "key": "Data.Key2",
                "values": [2, 3, 4.56]
            }
        ]
    }
}
```

## <a name="string-range-value-filter-examples"></a>Exemplos de filtro de valor de faixa de cordas

* StringContains
* StringBeginsWith
* StringEndsWith
* StringIn
* StringNotIn

```json
{
    "filter": {
        "advancedFilters": [
            {
                "operatorType": "StringContains",
                "key": "Data.Key1",
                "values": ["microsoft", "azure"]
            },
            {
                "operatorType": "StringBeginsWith",
                "key": "Data.Key2",
                "values": ["event", "grid"]
            },
            {
                "operatorType": "StringEndsWith",
                "key": "Data.P3.P4",
                "values": ["jpg", "jpeg", "png"]
            },
            {
                "operatorType": "StringIn",
                "key": "RootKey",
                "values": ["exact", "string", "matches"]
            },
            {
                "operatorType": "StringNotIn",
                "key": "RootKey",
                "values": ["aws", "bridge"]
            }
        ]
    }
}
```

## <a name="boolean-single-value-filter-examples"></a>Exemplos de filtro de valor único booleano

* BoolEquals

```json
{
    "filter": {
        "advancedFilters": [
            {
                "operatorType": "BoolEquals",
                "key": "BoolKey1",
                "value": true
            },
            {
                "operatorType": "BoolEquals",
                "key": "BoolKey2",
                "value": false
            }
        ]
    }
}
```
