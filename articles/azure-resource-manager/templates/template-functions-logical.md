---
title: Funções de modelo - lógica
description: Descreve as funções a serem usadas em um modelo do Resource Manager para determinar valores lógicos.
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: f058baa32e5f93a4177913287a5e9873fa7a9acb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156303"
---
# <a name="logical-functions-for-arm-templates"></a>Funções lógicas para modelos ARM

O Resource Manager fornece várias funções para fazer comparações em seus modelos ARM (Azure Resource Manager).

* [E](#and)
* [bool](#bool)
* [if](#if)
* [Não](#not)
* [Ou](#or)

## <a name="and"></a>e

`and(arg1, arg2, ...)`

Verifica se todos os valores de parâmetros são verdadeiros.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Type | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |booleano |O primeiro valor para verificar se é verdadeiro. |
| arg2 |Sim |booleano |O segundo valor para verificar se é verdadeiro. |
| argumentos adicionais |Não |booleano |Argumentos adicionais para verificar se são verdadeiros. |

### <a name="return-value"></a>Valor retornado

Retorna **True** se todos os valores forem verdadeiros; caso contrário, **False**.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) a seguir mostra como usar funções lógicas.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [ ],
    "outputs": {
        "andExampleOutput": {
            "value": "[and(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "orExampleOutput": {
            "value": "[or(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "notExampleOutput": {
            "value": "[not(bool('true'))]",
            "type": "bool"
        }
    }
}
```

O resultado do exemplo anterior é:

| Nome | Type | Valor |
| ---- | ---- | ----- |
| andExampleOutput | Bool | Falso |
| orExampleOutput | Bool | True |
| notExampleOutput | Bool | Falso |

## <a name="bool"></a>bool

`bool(arg1)`

Converte o parâmetro em um booliano.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Type | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |cadeia de caracteres ou inteiro |O valor a ser convertido em um booliano. |

### <a name="return-value"></a>Valor retornado
Um booliano do valor convertido.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/bool.json) a seguir mostra como usar um booliano com uma cadeia de caracteres ou um inteiro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "trueString": {
            "value": "[bool('true')]",
            "type" : "bool"
        },
        "falseString": {
            "value": "[bool('false')]",
            "type" : "bool"
        },
        "trueInt": {
            "value": "[bool(1)]",
            "type" : "bool"
        },
        "falseInt": {
            "value": "[bool(0)]",
            "type" : "bool"
        }
    }
}
```

A saída do exemplo anterior com os valores padrão é:

| Nome | Type | Valor |
| ---- | ---- | ----- |
| trueString | Bool | True |
| falseString | Bool | Falso |
| trueInt | Bool | True |
| falseInt | Bool | Falso |

## <a name="if"></a>if

`if(condition, trueValue, falseValue)`

Retorna um valor com base em se uma condição é verdadeira ou falsa.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Type | Descrição |
|:--- |:--- |:--- |:--- |
| condition |Sim |booleano |O valor para verificar se é verdadeiro ou falso. |
| trueValue |Sim | cadeia de caracteres, inteiro, objeto ou matriz |O valor a ser retornado quando a condição é verdadeira. |
| falseValue |Sim | cadeia de caracteres, inteiro, objeto ou matriz |O valor a ser retornado quando a condição é falsa. |

### <a name="return-value"></a>Valor retornado

Retorna o segundo parâmetro quando o primeiro parâmetro é **True**; caso contrário, retorna o terceiro parâmetro.

### <a name="remarks"></a>Comentários

Quando a condição é **Verdadeira,** apenas o valor verdadeiro é avaliado. Quando a condição é **falsa,** apenas o valor falso é avaliado. Com a função **if,** você pode incluir expressões que só são condicionadas. Por exemplo, você pode referenciar um recurso que existe uma condição, mas não a outra condição. Um exemplo de avaliação condicional das expressões é mostrado na seção a seguir.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/if.json) a seguir mostra como usar a função `if`.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    ],
    "outputs": {
        "yesOutput": {
            "type": "string",
            "value": "[if(equals('a', 'a'), 'yes', 'no')]"
        },
        "noOutput": {
            "type": "string",
            "value": "[if(equals('a', 'b'), 'yes', 'no')]"
        },
        "objectOutput": {
            "type": "object",
            "value": "[if(equals('a', 'a'), json('{\"test\": \"value1\"}'), json('null'))]"
        }
    }
}
```

O resultado do exemplo anterior é:

| Nome | Type | Valor |
| ---- | ---- | ----- |
| yesOutput | String | sim |
| noOutput | String | não |
| objectOutput | Objeto | { "test": "value1" } |

O [modelo a seguir](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/conditionWithReference.json) mostra como usar essa função com expressões que são apenas condicionadas.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "logAnalytics": {
            "type": "string",
            "defaultValue": ""
        }
    },
    "resources": [
        {
            "condition": "[not(empty(parameters('logAnalytics')))]",
            "name": "[concat(parameters('vmName'),'/omsOnboarding')]",
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2017-03-30",
            "properties": {
                "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                "type": "MicrosoftMonitoringAgent",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "workspaceId": "[if(not(empty(parameters('logAnalytics'))), reference(parameters('logAnalytics'), '2015-11-01-preview').customerId, json('null'))]"
                },
                "protectedSettings": {
                    "workspaceKey": "[if(not(empty(parameters('logAnalytics'))), listKeys(parameters('logAnalytics'), '2015-11-01-preview').primarySharedKey, json('null'))]"
                }
            }
        }
    ],
    "outputs": {
        "mgmtStatus": {
            "type": "string",
            "value": "[if(not(empty(parameters('logAnalytics'))), 'Enabled monitoring for VM!', 'Nothing to enable')]"
        }
    }
}
```

## <a name="not"></a>não

`not(arg1)`

Converte o valor booliano em seu valor oposto.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Type | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |booleano |O valor a ser convertido. |

### <a name="return-value"></a>Valor retornado

Retorna **True** quando o parâmetro é **False**. Retorna **False** quando o parâmetro é **True**.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) a seguir mostra como usar funções lógicas.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [ ],
    "outputs": {
        "andExampleOutput": {
            "value": "[and(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "orExampleOutput": {
            "value": "[or(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "notExampleOutput": {
            "value": "[not(bool('true'))]",
            "type": "bool"
        }
    }
}
```

O resultado do exemplo anterior é:

| Nome | Type | Valor |
| ---- | ---- | ----- |
| andExampleOutput | Bool | Falso |
| orExampleOutput | Bool | True |
| notExampleOutput | Bool | Falso |

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json) a seguir usa **not** com [equals](template-functions-comparison.md#equals).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    ],
    "outputs": {
        "checkNotEquals": {
            "type": "bool",
            "value": "[not(equals(1, 2))]"
        }
    }
```

O resultado do exemplo anterior é:

| Nome | Type | Valor |
| ---- | ---- | ----- |
| checkNotEquals | Bool | True |

## <a name="or"></a>ou

`or(arg1, arg2, ...)`

Verifica se qualquer valor do parâmetro é verdadeiro.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Type | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |booleano |O primeiro valor para verificar se é verdadeiro. |
| arg2 |Sim |booleano |O segundo valor para verificar se é verdadeiro. |
| argumentos adicionais |Não |booleano |Argumentos adicionais para verificar se são verdadeiros. |

### <a name="return-value"></a>Valor retornado

Retorna **True** se qualquer valor for verdadeiro; caso contrário, **False**.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) a seguir mostra como usar funções lógicas.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [ ],
    "outputs": {
        "andExampleOutput": {
            "value": "[and(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "orExampleOutput": {
            "value": "[or(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "notExampleOutput": {
            "value": "[not(bool('true'))]",
            "type": "bool"
        }
    }
}
```

O resultado do exemplo anterior é:

| Nome | Type | Valor |
| ---- | ---- | ----- |
| andExampleOutput | Bool | Falso |
| orExampleOutput | Bool | True |
| notExampleOutput | Bool | Falso |

## <a name="next-steps"></a>Próximas etapas

* Para obter uma descrição das seções em um modelo do Azure Resource Manager, consulte [Os modelos do Azure Resource Manager](template-syntax.md).
* Para mesclar vários modelos, consulte [Usando modelos vinculados com o Azure Resource Manager](linked-templates.md).
* Para iterar um número especificado de vezes ao criar um tipo de recurso, consulte [Criar várias instâncias de recursos no Azure Resource Manager](copy-resources.md).
* Para ver como implantar o modelo que você criou, consulte [Implantar um aplicativo com o Modelo do Azure Resource Manager](deploy-powershell.md).

