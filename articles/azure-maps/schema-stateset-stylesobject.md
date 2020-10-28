---
title: Styleobject para mapas dinâmicos do Azure
description: Guia de referência para o esquema JSON e a sintaxe para o Styleobject usado na criação em mapas dinâmicos do Azure.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/19/2020
ms.topic: reference
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 8eb4e49e6c0e3f011015d40b8eca036d5218674c
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92891692"
---
# <a name="stylesobject-schema-reference-guide-for-dynamic-maps"></a>Guia de referência de esquema styleobject para mapas dinâmicos

Este artigo é um guia de referência para o esquema JSON e a sintaxe para o `StylesObject` . O `StylesObject` é uma `StyleObject` matriz que representa os estilos de estadoset. Use o [serviço de estado de recurso](/rest/api/maps/featurestate) do Azure Maps Creator para aplicar seus estilos de estadoset a recursos de dados de mapa em destaque. Depois de criar seus estilos de estado e os associados aos recursos de mapa interno, você pode usá-los para criar mapas de interno dinâmico. Para obter mais informações sobre a criação de mapas de interno dinâmico, consulte [implementar o estilo dinâmico para mapas de criador interno](indoor-map-dynamic-styling.md).

## <a name="styleobject"></a>Estilo de

Um `StyleObject` é o como um [`BooleanTypeStyleRule`](#booleantypestylerule) ou um [`NumericTypeStyleRule`](#numerictypestylerule) .

O JSON abaixo mostra um `BooleanTypeStyleRule` nome `occupied` e um `NumericTypeStyleRule` nomeado `temperature` .

```json
 "styles": [
     {
        "keyname": "occupied",
        "type": "boolean",
        "rules": [
            {
                "true": "#FF0000",
                "false": "#00FF00"
            }
        ]
    },
    {
        "keyname": "temperature",
        "type": "number",
        "rules": [
             {
                "range": {
                "minimum": 50,
                "exclusiveMaximum": 70
                },
                "color": "#343deb"
            },
            {
                "range": {
                "maximum": 70,
                "exclusiveMinimum": 30
              },
              "color": "#eba834"
            }
        ]
    }
]
```

## <a name="numerictypestylerule"></a>NumericTypeStyleRule

 Um `NumericTypeStyleRule` é um  [`StyleObject`](#styleobject) e consiste nas seguintes propriedades:

| Propriedade | Tipo | Descrição | Obrigatório |
|-----------|----------|-------------|-------------|
| `keyName` | string | O *estado* ou o nome da propriedade dinâmica. Um `keyName` deve ser exclusivo dentro da `StyleObject` matriz.| Sim |
| `type` | string | O valor é "numeric". | Sim |
| `rules` | [`NumberRuleObject`](#numberruleobject)[]| Uma matriz de intervalos de estilo numérico com cores associadas. Cada intervalo define uma cor que deve ser usada quando o valor de *estado* satisfizer o intervalo.| Sim |

### <a name="numberruleobject"></a>NumberRuleObject

Um `NumberRuleObject` consiste em uma [`RangeObject`](#rangeobject) e uma `color` propriedade. Se o valor de *estado* cair no intervalo, sua cor de exibição será a cor especificada na `color` propriedade.

Se você definir vários intervalos sobrepostos, a cor escolhida será a cor definida no primeiro intervalo que é satisfeita.

No exemplo JSON a seguir, os dois intervalos terão true quando o valor de *estado* estiver entre 50-60. No entanto, a cor que será usada é `#343deb` o primeiro intervalo na lista que foi satisfeita.

```json

    {
        "rules":[
            {
                "range": {
                "minimum": 50,
                "exclusiveMaximum": 70
                },
                "color": "#343deb"
            },
            {
                "range": {
                "minimum": 50,
                "maximum": 60
                },
                "color": "#eba834"
            }
        ]
    }
]
```

| Propriedade | Tipo | Descrição | Obrigatório |
|-----------|----------|-------------|-------------|
| `range` | [Intervalo de](#rangeobject) | O [rangeobject](#rangeobject) define um conjunto de condições de intervalo lógico, que, se `true` , altera a cor de exibição do *estado* para a cor especificada na `color` propriedade. Se `range` não for especificado, a cor definida na `color` propriedade sempre será usada.   | Não |
| `color` | string | A cor a ser usada quando o valor do estado se enquadrar no intervalo. A `color` propriedade é uma cadeia de caracteres JSON em qualquer um dos seguintes formatos: <ul><li> Valores hexadecimais de estilo HTML </li><li> RGB ("#ff0", "#ffff00", "RGB (255, 255, 0)")</li><li> RGBA ("RGBA (255, 255, 0, 1)")</li><li> HSL ("HSL (100, 50%, 50%)")</li><li> HSLA ("HSLA (100, 50%, 50%, 1)")</li><li> Nomes predefinidos de cores HTML, como amarelo e azul.</li></ul> | Sim |

### <a name="rangeobject"></a>Intervalo de

O `RangeObject` define um valor de intervalo numérico de um [`NumberRuleObject`](#numberruleobject) . Para que o valor de *estado* se enquadrar no intervalo, todas as condições definidas devem ser verdadeiras. 

| Propriedade | Tipo | Descrição | Obrigatório |
|-----------|----------|-------------|-------------|
| `minimum` | double | Todo o número x que x ≥ `minimum` .| Não |
| `maximum` | double | Todo o número x que x ≤ `maximum` . | Não |
| `exclusiveMinimum` | double | Todo o número x > x `exclusiveMinimum` .| Não |
| `exclusiveMaximum` | double | Todo o número x < x `exclusiveMaximum` .| Não |

### <a name="example-of-numerictypestylerule"></a>Exemplo de NumericTypeStyleRule

O JSON a seguir ilustra um `NumericTypeStyleRule` *estado* chamado `temperature` . Neste exemplo, o [`NumberRuleObject`](#numberruleobject) contém dois intervalos de temperatura definidos e seus estilos de cor associados. Se o intervalo de temperatura for 50-69, a exibição deverá usar a cor `#343deb` .  Se o intervalo de temperatura for 31-70, a exibição deverá usar a cor `#eba834` .

```json
{
    "keyname": "temperature",
    "type": "number",
    "rules":[
        {
            "range": {
            "minimum": 50,
            "exclusiveMaximum": 70
            },
            "color": "#343deb"
        },
        {
            "range": {
            "maximum": 70,
            "exclusiveMinimum": 30
            },
            "color": "#eba834"
        }
    ]
}
```

## <a name="booleantypestylerule"></a>BooleanTypeStyleRule

Um `BooleanTypeStyleRule` é um [`StyleObject`](#styleobject) e consiste nas seguintes propriedades:

| Propriedade | Tipo | Descrição | Obrigatório |
|-----------|----------|-------------|-------------|
| `keyName` | string |  O *estado* ou o nome da propriedade dinâmica.  Um `keyName` deve ser exclusivo na matriz de estilos.| Sim |
| `type` | string |O valor é "booliano". | Sim |
| `rules` | [`BooleanRuleObject`](#booleanruleobject)uma| Um par booliano com cores `true` para `false` valores de *estado* e.| Sim |

### <a name="booleanruleobject"></a>BooleanRuleObject

Um `BooleanRuleObject` define as cores `true` para `false` valores e.

| Propriedade | Tipo | Descrição | Obrigatório |
|-----------|----------|-------------|-------------|
| `true` | string | A cor a ser usada quando o valor do *estado* é `true` . A `color` propriedade é uma cadeia de caracteres JSON em qualquer um dos seguintes formatos: <ul><li> Valores hexadecimais de estilo HTML </li><li> RGB ("#ff0", "#ffff00", "RGB (255, 255, 0)")</li><li> RGBA ("RGBA (255, 255, 0, 1)")</li><li> HSL ("HSL (100, 50%, 50%)")</li><li> HSLA ("HSLA (100, 50%, 50%, 1)")</li><li> Nomes predefinidos de cores HTML, como amarelo e azul.</li></ul>| Sim |
| `false` | string | A cor a ser usada quando o valor do *estado* é `false` . | Sim |

### <a name="example-of-booleantypestylerule"></a>Exemplo de BooleanTypeStyleRule

O JSON a seguir ilustra um `BooleanTypeStyleRule` *estado* chamado `occupied` . O [`BooleanRuleObject`](#booleanruleobject) define cores para `true` `false` valores e.

```json
{
    "keyname": "occupied",
    "type": "boolean",
    "rules": [
    {
        "true": "#FF0000",
        "false": "#00FF00"
    }
    ]
}
```