---
title: Funções de modelo - comparação
description: Descreve as funções a serem usadas em um modelo do Resource Manager para comparar valores.
ms.topic: conceptual
ms.date: 09/05/2017
ms.openlocfilehash: 42009e8543e307f2d3e4643ddaa79f492f9bdfee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156354"
---
# <a name="comparison-functions-for-arm-templates"></a>Funções de comparação para modelos ARM

O Resource Manager fornece várias funções para fazer comparações em seus modelos ARM (Azure Resource Manager).

* [equals](#equals)
* [Maior](#greater)
* [greaterOrEquals](#greaterorequals)
* [Menos](#less)
* [lessOrEquals](#lessorequals)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="equals"></a>equals
`equals(arg1, arg2)`

Verifica se dois valores são iguais entre si.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Type | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |int, string, array ou object |O primeiro valor para verificar a igualdade. |
| arg2 |Sim |int, string, array ou object |O segundo valor para verificar a igualdade. |

### <a name="return-value"></a>Valor retornado

Retorna **True** se os valores são iguais; caso contrário, **False**.

### <a name="remarks"></a>Comentários

A função equals é frequentemente usada com o elemento `condition` para testar se um recurso está implantado.

```json
{
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2017-06-01",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "[variables('storageAccountType')]"
    },
    "kind": "Storage",
    "properties": {}
}
```

### <a name="example"></a>Exemplo

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/equals.json) a seguir verifica os diferentes tipos de valores para igualdade. Todos os valores padrão retornam True.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 1
        },
        "firstString": {
            "type": "string",
            "defaultValue": "a"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        },
        "firstArray": {
            "type": "array",
            "defaultValue": ["a", "b"]
        },
        "secondArray": {
            "type": "array",
            "defaultValue": ["a", "b"]
        },
        "firstObject": {
            "type": "object",
            "defaultValue": {"a": "b"}
        },
        "secondObject": {
            "type": "object",
            "defaultValue": {"a": "b"}
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[equals(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[equals(parameters('firstString'), parameters('secondString'))]"
        },
        "checkArrays": {
            "type": "bool",
            "value": "[equals(parameters('firstArray'), parameters('secondArray'))]"
        },
        "checkObjects": {
            "type": "bool",
            "value": "[equals(parameters('firstObject'), parameters('secondObject'))]"
        }
    }
}
```

A saída do exemplo anterior com os valores padrão é:

| Nome | Type | Valor |
| ---- | ---- | ----- |
| checkInts | Bool | True |
| checkStrings | Bool | True |
| checkArrays | Bool | True |
| checkObjects | Bool | True |

Para implantar este modelo de exemplo com a CLI do Azure, use:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/equals.json
```

Para implantar este modelo de exemplo com o PowerShell, use:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/equals.json
```

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json) a seguir usa [not](template-functions-logical.md#not) com **equals**.

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
}
```

O resultado do exemplo anterior é:

| Nome | Type | Valor |
| ---- | ---- | ----- |
| checkNotEquals | Bool | True |

Para implantar este modelo de exemplo com a CLI do Azure, use:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/not-equals.json
```

Para implantar este modelo de exemplo com o PowerShell, use:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/not-equals.json
```

## <a name="greater"></a>greater
`greater(arg1, arg2)`

Verifica se o primeiro valor é maior que o segundo valor.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Type | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |int ou string |O primeiro valor da comparação de maior que. |
| arg2 |Sim |int ou string |O segundo valor da comparação de maior que. |

### <a name="return-value"></a>Valor retornado

Retorna **True** se o primeiro valor é maior que o segundo valor; caso contrário, **False**.

### <a name="example"></a>Exemplo

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/greater.json) a seguir verifica se um valor é maior que o outro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 2
        },
        "firstString": {
            "type": "string",
            "defaultValue": "A"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[greater(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[greater(parameters('firstString'), parameters('secondString'))]"
        }
    }
}
```

A saída do exemplo anterior com os valores padrão é:

| Nome | Type | Valor |
| ---- | ---- | ----- |
| checkInts | Bool | Falso |
| checkStrings | Bool | True |

Para implantar este modelo de exemplo com a CLI do Azure, use:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/greater.json
```

Para implantar este modelo de exemplo com o PowerShell, use:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/greater.json
```

## <a name="greaterorequals"></a>greaterOrEquals
`greaterOrEquals(arg1, arg2)`

Verifica se o primeiro valor é maior que ou igual ao segundo valor.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Type | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |int ou string |O primeiro valor da comparação de maior que ou igual a. |
| arg2 |Sim |int ou string |O segundo valor da comparação de maior que ou igual a. |

### <a name="return-value"></a>Valor retornado

Retorna **True** se o primeiro valor é maior que ou igual ao segundo valor; caso contrário, **False**.

### <a name="example"></a>Exemplo

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/greaterorequals.json) a seguir verifica se um valor é maior ou igual ao outro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 2
        },
        "firstString": {
            "type": "string",
            "defaultValue": "A"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[greaterOrEquals(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[greaterOrEquals(parameters('firstString'), parameters('secondString'))]"
        }
    }
}
```

A saída do exemplo anterior com os valores padrão é:

| Nome | Type | Valor |
| ---- | ---- | ----- |
| checkInts | Bool | Falso |
| checkStrings | Bool | True |

Para implantar este modelo de exemplo com a CLI do Azure, use:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/greaterorequals.json
```

Para implantar este modelo de exemplo com o PowerShell, use:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/greaterorequals.json
```

## <a name="less"></a>less
`less(arg1, arg2)`

Verifica se o primeiro valor é menor que o segundo valor.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Type | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |int ou string |O primeiro valor da comparação de menor que. |
| arg2 |Sim |int ou string |O segundo valor da comparação de menor que. |

### <a name="return-value"></a>Valor retornado

Retorna **True** se o primeiro valor é menor que o segundo valor; caso contrário, **False**.

### <a name="example"></a>Exemplo

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/less.json) a seguir verifica se um valor é menor que o outro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 2
        },
        "firstString": {
            "type": "string",
            "defaultValue": "A"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[less(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[less(parameters('firstString'), parameters('secondString'))]"
        }
    }
}
```

A saída do exemplo anterior com os valores padrão é:

| Nome | Type | Valor |
| ---- | ---- | ----- |
| checkInts | Bool | True |
| checkStrings | Bool | Falso |

Para implantar este modelo de exemplo com a CLI do Azure, use:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/less.json
```

Para implantar este modelo de exemplo com o PowerShell, use:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/less.json
```

## <a name="lessorequals"></a>lessOrEquals
`lessOrEquals(arg1, arg2)`

Verifica se o primeiro valor é menor que ou igual ao segundo valor.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Type | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |int ou string |O primeiro valor da comparação de menor que ou igual a. |
| arg2 |Sim |int ou string |O segundo valor da comparação de menor que ou igual a. |

### <a name="return-value"></a>Valor retornado

Retorna **True** se o primeiro valor é menor que ou igual ao segundo valor; caso contrário, **False**.

### <a name="example"></a>Exemplo

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/lessorequals.json) a seguir verifica se um valor é menor ou igual ao outro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 2
        },
        "firstString": {
            "type": "string",
            "defaultValue": "A"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[lessOrEquals(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[lessOrEquals(parameters('firstString'), parameters('secondString'))]"
        }
    }
}
```

A saída do exemplo anterior com os valores padrão é:

| Nome | Type | Valor |
| ---- | ---- | ----- |
| checkInts | Bool | True |
| checkStrings | Bool | Falso |

Para implantar este modelo de exemplo com a CLI do Azure, use:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/lessorequals.json
```

Para implantar este modelo de exemplo com o PowerShell, use:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/lessorequals.json
```

## <a name="next-steps"></a>Próximas etapas
* Para obter uma descrição das seções em um modelo do Azure Resource Manager, consulte [Os modelos do Azure Resource Manager](template-syntax.md).
* Para mesclar vários modelos, consulte [Usando modelos vinculados com o Azure Resource Manager](linked-templates.md).
* Para iterar um número especificado de vezes ao criar um tipo de recurso, consulte [Criar várias instâncias de recursos no Azure Resource Manager](copy-resources.md).
* Para ver como implantar o modelo criado, consulte [Implantar um aplicativo com o modelo Azure Resource Manager](deploy-powershell.md).

