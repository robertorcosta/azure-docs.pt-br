---
title: Funções de modelo - data
description: Descreve as funções a serem utilizadas em um modelo do Azure Resource Manager para trabalhar com datas.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 75234a142b9f2fbe61c337bfeb378b47534bac79
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986272"
---
# <a name="date-functions-for-arm-templates"></a>Funções de data para modelos ARM

O Resource Manager fornece as seguintes funções para trabalhar com datas em seus modelos ARM (Azure Resource Manager, gerenciador de recursos do Azure):

* [dateTimeAdd](#datetimeadd)
* [utcNow](#utcnow)

## <a name="datetimeadd"></a>dateTimeAdd

`dateTimeAdd(base, duration, [format])`

Adiciona uma duração de tempo a um valor de data-hora base.

### <a name="parameters"></a>parâmetros

| Parâmetro | Obrigatório | Type | Descrição |
|:--- |:--- |:--- |:--- |
| base | Sim | string | O valor da data de início para a adição. Use [o formato iso 8601 de carimbo de data e hora](https://en.wikipedia.org/wiki/ISO_8601). |
| duration | Sim | string | O valor de tempo a ser adicionado à base. Pode ser um valor negativo. Use [o formato de duração ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Durations). |
| format | Não | string | O formato de saída para o resultado da data. Se não for fornecido, o formato do valor base é utilizado. Use [strings de formato padrão](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou [strings de formato personalizados](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). |

### <a name="return-value"></a>Valor retornado

O valor de data que resulta da adição do valor de duração ao valor base.

### <a name="examples"></a>Exemplos

O modelo de exemplo a seguir mostra diferentes maneiras de adicionar valores de tempo.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters":{
        "baseTime":{
            "type":"string",
            "defaultValue": "[utcNow('u')]"
        }
    },
    "variables": {
        "add3Years": "[dateTimeAdd(parameters('baseTime'), 'P3Y')]",
        "subtract9Days": "[dateTimeAdd(parameters('baseTime'), '-P9D')]",
        "add1Hour": "[dateTimeAdd(parameters('baseTime'), 'PT1H')]"
    },
    "resources": [],
    "outputs": {
        "add3Years": {
            "value": "[variables('add3Years')]",
            "type": "string"
        },
        "subtract9Days": {
            "value": "[variables('subtract9Days')]",
            "type": "string"
        },
        "add1Hour": {
            "value": "[variables('add1Hour')]",
            "type": "string"
        },
    }
}
```

Quando o modelo anterior é implantado `2020-04-07 14:53:14Z`com um tempo base de , a saída é:

| Nome | Type | Valor |
| ---- | ---- | ----- |
| add3Anos | String | 07/04/2023 2:53:14 PM |
| subtrair9Dias | String | 29/03/2020 14:53:14 |
| add1Hour | String | 07/04/2020 3:53:14 PM |

O modelo seguinte mostra como definir a hora de início de um cronograma de Automação.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "omsAutomationAccountName": {
            "type": "string",
            "defaultValue": "demoAutomation",
            "metadata": {
                "description": "Use an existing Automation account."
            }
        },
        "scheduleName": {
            "type": "string",
            "defaultValue": "demoSchedule1",
            "metadata": {
                "description": "Name of the new schedule."
            }
        },
        "baseTime":{
            "type":"string",
            "defaultValue": "[utcNow('u')]",
            "metadata": {
                "description": "Schedule will start one hour from this time."
            }
        }
    },
    "variables": {
        "startTime": "[dateTimeAdd(parameters('baseTime'), 'PT1H')]"
    },
    "resources": [
        {
            "name": "[concat(parameters('omsAutomationAccountName'), '/', parameters('scheduleName'))]",
            "type": "microsoft.automation/automationAccounts/schedules",
            "apiVersion": "2015-10-31",
            "location": "eastus2",
            "tags": {
            },
            "properties": {
                "description": "Demo Scheduler",
                "startTime": "[variables('startTime')]",
                "isEnabled": "true",
                "interval": 1,
                "frequency": "hour"
            }
        }
    ],
    "outputs": {
    }
}
```

## <a name="utcnow"></a>utcNow

`utcNow(format)`

Retorna o valor de data-hora atual (UTC) no formato especificado. Se nenhum formato for fornecido, o formato ISO 8601 (yyyyMddTHHmmssZ) é usado. **Esta função só pode ser usada no valor padrão de um parâmetro.**

### <a name="parameters"></a>parâmetros

| Parâmetro | Obrigatório | Type | Descrição |
|:--- |:--- |:--- |:--- |
| format |Não |string |O valor codificado em URI a ser convertido em uma cadeia de caracteres. Use [strings de formato padrão](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou [strings de formato personalizados](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). |

### <a name="remarks"></a>Comentários

Você só pode usar esta função dentro de uma expressão para o valor padrão de um parâmetro. Usar esta função em qualquer outro lugar em um modelo retorna um erro. A função não é permitida em outras partes do modelo porque retorna um valor diferente cada vez que é chamada. Implantar o mesmo modelo com os mesmos parâmetros não produziria os mesmos resultados de forma confiável.

Se você usar a [opção para reimplantar uma implantação bem-sucedida anteriormente,](rollback-on-error.md)e a implantação anterior incluir um parâmetro que usa utcNow, o parâmetro não será reavaliado. Em vez disso, o valor do parâmetro da implantação anterior é automaticamente reutilizado na implantação de reversão.

Tenha cuidado ao reimplantar um modelo que dependa da função utcNow por um valor padrão. Quando você reimplanta e não fornece um valor para o parâmetro, a função é reavaliada. Se você quiser atualizar um recurso existente em vez de criar um novo, passe o valor do parâmetro da implantação anterior.

### <a name="return-value"></a>Valor retornado

O valor atual de data-hora da UTC.

### <a name="examples"></a>Exemplos

O modelo de exemplo a seguir mostra diferentes formatos para o valor da data.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "utcValue": {
            "type": "string",
            "defaultValue": "[utcNow()]"
        },
        "utcShortValue": {
            "type": "string",
            "defaultValue": "[utcNow('d')]"
        },
        "utcCustomValue": {
            "type": "string",
            "defaultValue": "[utcNow('M d')]"
        }
    },
    "resources": [
    ],
    "outputs": {
        "utcOutput": {
            "type": "string",
            "value": "[parameters('utcValue')]"
        },
        "utcShortOutput": {
            "type": "string",
            "value": "[parameters('utcShortValue')]"
        },
        "utcCustomOutput": {
            "type": "string",
            "value": "[parameters('utcCustomValue')]"
        }
    }
}
```

A saída do exemplo anterior varia para cada implantação, mas será semelhante a:

| Nome | Type | Valor |
| ---- | ---- | ----- |
| utcSaída | string | 20190305T175318Z |
| utcShortOutput | string | 05/03/2019 |
| utcCustomOutput | string | 3 5 |

O próximo exemplo mostra como usar um valor da função ao definir um valor de marcação.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "utcShort": {
            "type": "string",
            "defaultValue": "[utcNow('d')]"
        },
        "rgName": {
            "type": "string"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/resourceGroups",
            "apiVersion": "2018-05-01",
            "name": "[parameters('rgName')]",
            "location": "westeurope",
            "tags":{
                "createdDate": "[parameters('utcShort')]"
            },
            "properties":{}
        }
    ],
    "outputs": {
        "utcShort": {
            "type": "string",
            "value": "[parameters('utcShort')]"
        }
    }
}
```