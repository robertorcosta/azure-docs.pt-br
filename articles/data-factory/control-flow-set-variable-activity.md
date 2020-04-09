---
title: Definir Atividade Variável no Azure Data Factory
description: Saiba como usar a atividade Definir Variável para definir o valor de uma variável existente definida em um pipeline do Data Factory
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/07/2020
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.openlocfilehash: e736cc95628bd0e15bdb7ffd425608278788c353
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879251"
---
# <a name="set-variable-activity-in-azure-data-factory"></a>Definir atividade variável na fábrica de dados do Azure

Use a atividade Definir Variável para definir o valor de uma variável existente do tipo Cadeia de caracteres, Booliano ou Matriz definido em um pipeline do Data Factory.

## <a name="type-properties"></a>Propriedades de tipo

Propriedade | Descrição | Obrigatório
-------- | ----------- | --------
name | Nome da atividade no pipeline | sim
descrição | Texto descrevendo o que a atividade realiza | não
type | Deve ser definido como **SetVariable** | sim
value | Valor do objeto de seqüência literal ou de expressão ao que a variável será atribuída | sim
variableName | Nome da variável que será definida por essa atividade | sim

## <a name="incrementing-a-variable"></a>Incrementando uma variável

Um cenário comum envolvendo variáveis na Fábrica de Dados Do Azure é usar uma variável como um iterador dentro de uma atividade de até ou para cada atividade. Em uma atividade variável definida, você não `value` pode referenciar a variável que está sendo definida no campo. Para contornar essa limitação, defina uma variável temporária e crie uma atividade variável de segundo conjunto. A atividade variável do segundo conjunto define o valor do iterator para a variável temporária. 

Abaixo está um exemplo deste padrão:

![Incrementar variável](media/control-flow-set-variable-activity/increment-variable.png "Incrementar variável")

``` json
{
    "name": "pipeline3",
    "properties": {
        "activities": [
            {
                "name": "Set I",
                "type": "SetVariable",
                "dependsOn": [
                    {
                        "activity": "Increment J",
                        "dependencyConditions": [
                            "Succeeded"
                        ]
                    }
                ],
                "userProperties": [],
                "typeProperties": {
                    "variableName": "i",
                    "value": {
                        "value": "@variables('j')",
                        "type": "Expression"
                    }
                }
            },
            {
                "name": "Increment J",
                "type": "SetVariable",
                "dependsOn": [],
                "userProperties": [],
                "typeProperties": {
                    "variableName": "j",
                    "value": {
                        "value": "@string(add(int(variables('i')), 1))",
                        "type": "Expression"
                    }
                }
            }
        ],
        "variables": {
            "i": {
                "type": "String",
                "defaultValue": "0"
            },
            "j": {
                "type": "String",
                "defaultValue": "0"
            }
        },
        "annotations": []
    }
}
```


## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre uma atividade de fluxo de controle relacionada com suporte pelo Data Factory: 

- [Acrescentar Atividade Variável](control-flow-append-variable-activity.md)
