---
title: Funções de modelo-data
description: Descreve as funções a serem usadas em um modelo de Azure Resource Manager para trabalhar com datas.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 0c31b26361a262a502b2a9e0fb068391846cab4b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82192290"
---
# <a name="date-functions-for-arm-templates"></a>Funções de data para modelos de ARM

O Resource Manager fornece as seguintes funções para trabalhar com datas em seus modelos de Azure Resource Manager (ARM):

* [dateTimeAdd](#datetimeadd)
* [utcNow](#utcnow)

## <a name="datetimeadd"></a>dateTimeAdd

`dateTimeAdd(base, duration, [format])`

Adiciona uma duração de tempo a um valor base. O formato ISO 8601 é esperado.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| base | Sim | cadeia de caracteres | O valor DateTime inicial para a adição. Use o [formato de carimbo de data/hora ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). |
| duration | Sim | cadeia de caracteres | O valor de tempo a ser adicionado à base. Pode ser um valor negativo. Use o [formato de duração ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Durations). |
| format | Não | cadeia de caracteres | O formato de saída para o resultado de data e hora. Se não for fornecido, o formato do valor de base será usado. Use cadeias de caracteres de [formato padrão](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou [cadeias de caracteres de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). |

### <a name="return-value"></a>Valor retornado

O valor de data e hora que resulta da adição do valor de duração ao valor base.

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

Quando o modelo anterior é implantado com uma hora `2020-04-07 14:53:14Z`base de, a saída é:

| Nome | Type | Valor |
| ---- | ---- | ----- |
| add3Years | String | 4/7/2023 2:53:14 PM |
| subtract9Days | String | 3/29/2020 2:53:14 PM |
| add1Hour | String | 4/7/2020 3:53:14 PM |

O modelo de exemplo a seguir mostra como definir a hora de início de uma agenda de automação.

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

Retorna o valor DateTime (UTC) atual no formato especificado. Se nenhum formato for fornecido, o formato ISO 8601 (yyyyMMddTHHmmssZ) será usado. **Essa função só pode ser usada no valor padrão para um parâmetro.**

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| format |Não |cadeia de caracteres |O valor codificado em URI a ser convertido em uma cadeia de caracteres. Use cadeias de caracteres de [formato padrão](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou [cadeias de caracteres de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). |

### <a name="remarks"></a>Comentários

Você só pode usar essa função dentro de uma expressão para o valor padrão de um parâmetro. O uso dessa função em qualquer outro lugar em um modelo retorna um erro. A função não é permitida em outras partes do modelo porque ela retorna um valor diferente cada vez que é chamada. Implantar o mesmo modelo com os mesmos parâmetros não produziria com confiança os mesmos resultados.

Se você usar a [opção para reimplantar uma implantação bem-sucedida anterior](rollback-on-error.md)e a implantação anterior incluir um parâmetro que usa UtcNow, o parâmetro não será reavaliado. Em vez disso, o valor do parâmetro da implantação anterior é automaticamente reutilizado na implantação de reversão.

Tenha cuidado ao reimplantar um modelo que dependa da função utcNow para um valor padrão. Quando você reimplanta e não fornece um valor para o parâmetro, a função é reavaliada. Se você quiser atualizar um recurso existente em vez de criar um novo, passe o valor do parâmetro da implantação anterior.

### <a name="return-value"></a>Valor retornado

O valor DateTime UTC atual.

### <a name="examples"></a>Exemplos

O modelo de exemplo a seguir mostra formatos diferentes para o valor DateTime.

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

A saída do exemplo anterior varia de acordo com cada implantação, mas será semelhante a:

| Nome | Type | Valor |
| ---- | ---- | ----- |
| utcOutput | cadeia de caracteres | 20190305T175318Z |
| utcShortOutput | cadeia de caracteres | 05/03/2019 |
| utcCustomOutput | cadeia de caracteres | 3 5 |

O exemplo a seguir mostra como usar um valor da função ao definir um valor de marca.

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

## <a name="next-steps"></a>Próximas etapas

* Para obter uma descrição das seções em um modelo de Azure Resource Manager, consulte [entender a estrutura e a sintaxe de modelos ARM](template-syntax.md).
