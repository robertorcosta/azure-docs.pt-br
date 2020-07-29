---
title: Funções de modelo – comparação
description: Descreve as funções a serem usadas em um modelo do Resource Manager para comparar valores.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 01d66f43cf73dcc9228118db5a9b6149b19ee66d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84677824"
---
# <a name="comparison-functions-for-arm-templates"></a>Funções de comparação para modelos do ARM

O Gerenciador de recursos fornece várias funções para fazer comparações em seus modelos de Azure Resource Manager (ARM).

* [coalesce](#coalesce)
* [equals](#equals)
* [greater](#greater)
* [greaterOrEquals](#greaterorequals)
* [less](#less)
* [lessOrEquals](#lessorequals)

## <a name="coalesce"></a>coalesce

`coalesce(arg1, arg2, arg3, ...)`

Retorna o primeiro valor não nulo dos parâmetros. Cadeias de caracteres vazias, matrizes vazias e objetos vazios não são nulos.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Digite | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |int, string, array ou object |O primeiro valor para testar se é nulo. |
| argumentos adicionais |Não |int, string, array ou object |Valores adicionais para testar se são nulos. |

### <a name="return-value"></a>Retornar valor

O valor dos primeiros parâmetros não nulos, que pode ser uma cadeia de caracteres, inteiro, matriz ou objeto. Null se todos os parâmetros forem nulos.

### <a name="example"></a>Exemplo

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/coalesce.json) a seguir mostra a saída de diferentes usos de coalesce.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "objectToTest": {
            "type": "object",
            "defaultValue": {
                "null1": null,
                "null2": null,
                "string": "default",
                "int": 1,
                "object": {"first": "default"},
                "array": [1]
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "stringOutput": {
            "type": "string",
            "value": "[coalesce(parameters('objectToTest').null1, parameters('objectToTest').null2, parameters('objectToTest').string)]"
        },
        "intOutput": {
            "type": "int",
            "value": "[coalesce(parameters('objectToTest').null1, parameters('objectToTest').null2, parameters('objectToTest').int)]"
        },
        "objectOutput": {
            "type": "object",
            "value": "[coalesce(parameters('objectToTest').null1, parameters('objectToTest').null2, parameters('objectToTest').object)]"
        },
        "arrayOutput": {
            "type": "array",
            "value": "[coalesce(parameters('objectToTest').null1, parameters('objectToTest').null2, parameters('objectToTest').array)]"
        },
        "emptyOutput": {
            "type": "bool",
            "value": "[empty(coalesce(parameters('objectToTest').null1, parameters('objectToTest').null2))]"
        }
    }
}
```

A saída do exemplo anterior com os valores padrão é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| stringOutput | String | default |
| intOutput | Int | 1 |
| objectOutput | Objeto | {"first": "default"} |
| arrayOutput | Array |  [1] |
| emptyOutput | Bool | True |

## <a name="equals"></a>equals

`equals(arg1, arg2)`

Verifica se dois valores são iguais entre si.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Digite | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |int, string, array ou object |O primeiro valor para verificar a igualdade. |
| arg2 |Sim |int, string, array ou object |O segundo valor para verificar a igualdade. |

### <a name="return-value"></a>Retornar valor

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
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| checkInts | Bool | True |
| checkStrings | Bool | True |
| checkArrays | Bool | True |
| checkObjects | Bool | True |

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json) a seguir usa [not](template-functions-logical.md#not) com **equals**.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| checkNotEquals | Bool | True |

## <a name="greater"></a>greater

`greater(arg1, arg2)`

Verifica se o primeiro valor é maior que o segundo valor.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Digite | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |int ou string |O primeiro valor da comparação de maior que. |
| arg2 |Sim |int ou string |O segundo valor da comparação de maior que. |

### <a name="return-value"></a>Retornar valor

Retorna **True** se o primeiro valor é maior que o segundo valor; caso contrário, **False**.

### <a name="example"></a>Exemplo

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/greater.json) a seguir verifica se um valor é maior que o outro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| checkInts | Bool | Falso |
| checkStrings | Bool | True |

## <a name="greaterorequals"></a>greaterOrEquals

`greaterOrEquals(arg1, arg2)`

Verifica se o primeiro valor é maior que ou igual ao segundo valor.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Digite | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |int ou string |O primeiro valor da comparação de maior que ou igual a. |
| arg2 |Sim |int ou string |O segundo valor da comparação de maior que ou igual a. |

### <a name="return-value"></a>Retornar valor

Retorna **True** se o primeiro valor é maior que ou igual ao segundo valor; caso contrário, **False**.

### <a name="example"></a>Exemplo

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/greaterorequals.json) a seguir verifica se um valor é maior ou igual ao outro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| checkInts | Bool | Falso |
| checkStrings | Bool | True |

## <a name="less"></a>less

`less(arg1, arg2)`

Verifica se o primeiro valor é menor que o segundo valor.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Digite | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |int ou string |O primeiro valor da comparação de menor que. |
| arg2 |Sim |int ou string |O segundo valor da comparação de menor que. |

### <a name="return-value"></a>Retornar valor

Retorna **True** se o primeiro valor é menor que o segundo valor; caso contrário, **False**.

### <a name="example"></a>Exemplo

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/less.json) a seguir verifica se um valor é menor que o outro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| checkInts | Bool | True |
| checkStrings | Bool | Falso |

## <a name="lessorequals"></a>lessOrEquals

`lessOrEquals(arg1, arg2)`

Verifica se o primeiro valor é menor que ou igual ao segundo valor.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Digite | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |int ou string |O primeiro valor da comparação de menor que ou igual a. |
| arg2 |Sim |int ou string |O segundo valor da comparação de menor que ou igual a. |

### <a name="return-value"></a>Retornar valor

Retorna **True** se o primeiro valor é menor que ou igual ao segundo valor; caso contrário, **False**.

### <a name="example"></a>Exemplo

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/lessorequals.json) a seguir verifica se um valor é menor ou igual ao outro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| checkInts | Bool | True |
| checkStrings | Bool | Falso |

## <a name="next-steps"></a>Próximas etapas

* Para obter uma descrição das seções em um modelo de Azure Resource Manager, consulte [entender a estrutura e a sintaxe de modelos ARM](template-syntax.md).
