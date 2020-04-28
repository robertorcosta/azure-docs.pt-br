---
title: Funções de modelo – numéricas
description: Descreve as funções a serem usadas em um modelo do Resource Manager para trabalhar com números.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: dc15ade453fc5ea4dc031ced0377892f4f8cf27d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82192341"
---
# <a name="numeric-functions-for-arm-templates"></a>Funções numéricas para modelos de ARM

O Gerenciador de recursos fornece as seguintes funções para trabalhar com inteiros em seu modelo de Azure Resource Manager (ARM):

* [add](#add)
* [copyIndex](#copyindex)
* [div](#div)
* [float](#float)
* [int](#int)
* [max](#max)
* [min](#min)
* [resto](#mod)
* [Mul](#mul)
* [projeto](#sub)

## <a name="add"></a>add

`add(operand1, operand2)`

Retorna a soma dos dois inteiros fornecidos.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
|operand1 |Sim |INT |Primeiro número a ser adicionado. |
|operand2 |Sim |INT |Segundo número a ser adicionado. |

### <a name="return-value"></a>Valor retornado

Um inteiro que contém a soma dos parâmetros.

### <a name="example"></a>Exemplo

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/add.json) a seguir adiciona dois parâmetros.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "defaultValue": 5,
            "metadata": {
                "description": "First integer to add"
            }
        },
        "second": {
            "type": "int",
            "defaultValue": 3,
            "metadata": {
                "description": "Second integer to add"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "addResult": {
            "type": "int",
            "value": "[add(parameters('first'), parameters('second'))]"
        }
    }
}
```

A saída do exemplo anterior com os valores padrão é:

| Nome | Type | Valor |
| ---- | ---- | ----- |
| addResult | Int | 8 |

## <a name="copyindex"></a>copyIndex

`copyIndex(loopName, offset)`

Retorna o índice de um loop de iteração.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| loopName | Não | cadeia de caracteres | O nome do loop para obter a iteração. |
| deslocamento |Não |INT |O número a ser adicionado ao valor de iteração com base em zero. |

### <a name="remarks"></a>Comentários

Essa função é sempre usada com um objeto **copy** . Se nenhum valor for fornecido para **offset**, o valor de iteração atual retornará. O valor de iteração começa em zero.

A propriedade **loopName** permite que você especifique se copyIndex se refere a uma iteração de recursos ou a uma iteração de propriedade. Se nenhum valor for fornecido para **loopName**, a iteração de tipo de recurso atual será usada. Forneça um valor para **loopName** durante a iteração em uma propriedade.

Para obter mais informações sobre como usar a cópia, consulte:

* [Iteração de recurso em modelos ARM](copy-resources.md)
* [Iteração de propriedade em modelos ARM](copy-properties.md)
* [Iteração variável em modelos ARM](copy-variables.md)
* [Iteração de saída em modelos ARM](copy-outputs.md)

### <a name="example"></a>Exemplo

O exemplo a seguir mostra um loop de cópia e o valor de índice incluído no nome.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageCount": {
            "type": "int",
            "defaultValue": 2
        }
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": "[parameters('storageCount')]"
            }
        }
    ],
    "outputs": {}
}
```

### <a name="return-value"></a>Valor retornado

Um inteiro que representa o índice atual da iteração.

## <a name="div"></a>div

`div(operand1, operand2)`

Retorna a divisão de inteiros dos dois inteiros fornecidos.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| operand1 |Sim |INT |O número que está sendo dividido. |
| operand2 |Sim |INT |O número usado para dividir. Não pode ser 0. |

### <a name="return-value"></a>Valor retornado

Um inteiro que representa a divisão.

### <a name="example"></a>Exemplo

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/div.json) a seguir divide um parâmetro por outro parâmetro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "defaultValue": 8,
            "metadata": {
                "description": "Integer being divided"
            }
        },
        "second": {
            "type": "int",
            "defaultValue": 3,
            "metadata": {
                "description": "Integer used to divide"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "divResult": {
            "type": "int",
            "value": "[div(parameters('first'), parameters('second'))]"
        }
    }
}
```

A saída do exemplo anterior com os valores padrão é:

| Nome | Type | Valor |
| ---- | ---- | ----- |
| divResult | Int | 2 |

## <a name="float"></a>FLOAT

`float(arg1)`

Converte o valor em um número de ponto flutuante. Você só usa essa função ao passar parâmetros personalizados para um aplicativo, como um aplicativo lógico.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |cadeia de caracteres ou inteiro |O valor a ser convertido em um número de ponto flutuante. |

### <a name="return-value"></a>Valor retornado

Um número de ponto flutuante.

### <a name="example"></a>Exemplo

O exemplo a seguir mostra como usar float para passar parâmetros para um aplicativo lógico:

```json
{
    "type": "Microsoft.Logic/workflows",
    "properties": {
        ...
        "parameters": {
            "custom1": {
                "value": "[float('3.0')]"
            },
            "custom2": {
                "value": "[float(3)]"
            },
```

## <a name="int"></a>INT

`int(valueToConvert)`

Converte o valor especificado em um inteiro.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| valueToConvert |Sim |cadeia de caracteres ou inteiro |O valor a ser convertido em um inteiro. |

### <a name="return-value"></a>Valor retornado

Um inteiro do valor convertido.

### <a name="example"></a>Exemplo

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/int.json) a seguir converte o valor do parâmetro fornecido pelo usuário em inteiro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringToConvert": {
            "type": "string",
            "defaultValue": "4"
        }
    },
    "resources": [
    ],
    "outputs": {
        "intResult": {
            "type": "int",
            "value": "[int(parameters('stringToConvert'))]"
        }
    }
}
```

A saída do exemplo anterior com os valores padrão é:

| Nome | Type | Valor |
| ---- | ---- | ----- |
| intResult | Int | 4 |

## <a name="max"></a>max

`max (arg1)`

Retorna o valor máximo de uma matriz de inteiros ou uma lista de inteiros separados por vírgulas.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |matriz de inteiros ou lista de inteiros separados por vírgulas |A coleção para obtenção do valor máximo. |

### <a name="return-value"></a>Valor retornado

Um inteiro que representa o valor máximo da coleção.

### <a name="example"></a>Exemplo

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/max.json) a seguir mostra como usar max com uma matriz e uma lista de inteiros:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": [0,3,2,5,4]
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "int",
            "value": "[max(parameters('arrayToTest'))]"
        },
        "intOutput": {
            "type": "int",
            "value": "[max(0,3,2,5,4)]"
        }
    }
}
```

A saída do exemplo anterior com os valores padrão é:

| Nome | Type | Valor |
| ---- | ---- | ----- |
| arrayOutput | Int | 5 |
| intOutput | Int | 5 |

## <a name="min"></a>Min

`min (arg1)`

Retorna o valor mínimo de uma matriz de inteiros ou uma lista de inteiros separados por vírgulas.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |matriz de inteiros ou lista de inteiros separados por vírgulas |A coleção para obtenção do valor mínimo. |

### <a name="return-value"></a>Valor retornado

Um inteiro que representa o valor mínimo da coleção.

### <a name="example"></a>Exemplo

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/min.json) a seguir mostra como usar min com uma matriz e uma lista de inteiros:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": [0,3,2,5,4]
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "int",
            "value": "[min(parameters('arrayToTest'))]"
        },
        "intOutput": {
            "type": "int",
            "value": "[min(0,3,2,5,4)]"
        }
    }
}
```

A saída do exemplo anterior com os valores padrão é:

| Nome | Type | Valor |
| ---- | ---- | ----- |
| arrayOutput | Int | 0 |
| intOutput | Int | 0 |

## <a name="mod"></a>mod

`mod(operand1, operand2)`

Retorna o restante da divisão de inteiros usando os dois inteiros fornecidos.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| operand1 |Sim |INT |O número que está sendo dividido. |
| operand2 |Sim |INT |O número que é usado para dividir, não pode ser 0. |

### <a name="return-value"></a>Valor retornado

Um inteiro que representa o resto.

### <a name="example"></a>Exemplo

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/mod.json) a seguir retorna o resto da divisão de um parâmetro por outro parâmetro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "defaultValue": 7,
            "metadata": {
                "description": "Integer being divided"
            }
        },
        "second": {
            "type": "int",
            "defaultValue": 3,
            "metadata": {
                "description": "Integer used to divide"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "modResult": {
            "type": "int",
            "value": "[mod(parameters('first'), parameters('second'))]"
        }
    }
}
```

A saída do exemplo anterior com os valores padrão é:

| Nome | Type | Valor |
| ---- | ---- | ----- |
| modResult | Int | 1 |

## <a name="mul"></a>mul

`mul(operand1, operand2)`

Retorna a multiplicação de dois inteiros fornecidos.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| operand1 |Sim |INT |Primeiro número a ser multiplicado. |
| operand2 |Sim |INT |Segundo número a ser multiplicado. |

### <a name="return-value"></a>Valor retornado

Um inteiro que representa a multiplicação.

### <a name="example"></a>Exemplo

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/mul.json) a seguir multiplica um parâmetro por outro parâmetro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "defaultValue": 5,
            "metadata": {
                "description": "First integer to multiply"
            }
        },
        "second": {
            "type": "int",
            "defaultValue": 3,
            "metadata": {
                "description": "Second integer to multiply"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "mulResult": {
            "type": "int",
            "value": "[mul(parameters('first'), parameters('second'))]"
        }
    }
}
```

A saída do exemplo anterior com os valores padrão é:

| Nome | Type | Valor |
| ---- | ---- | ----- |
| mulResult | Int | 15 |

## <a name="sub"></a>sub

`sub(operand1, operand2)`

Retorna a subtração dos dois inteiros fornecidos.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| operand1 |Sim |INT |O número do qual é subtraído. |
| operand2 |Sim |INT |O número subtraído. |

### <a name="return-value"></a>Valor retornado

Um inteiro que representa a subtração.

### <a name="example"></a>Exemplo

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/sub.json) a seguir subtrai um parâmetro por outro parâmetro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "defaultValue": 7,
            "metadata": {
                "description": "Integer subtracted from"
            }
        },
        "second": {
            "type": "int",
            "defaultValue": 3,
            "metadata": {
                "description": "Integer to subtract"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "subResult": {
            "type": "int",
            "value": "[sub(parameters('first'), parameters('second'))]"
        }
    }
}
```

A saída do exemplo anterior com os valores padrão é:

| Nome | Type | Valor |
| ---- | ---- | ----- |
| subResult | Int | 4 |

## <a name="next-steps"></a>Próximas etapas

* Para obter uma descrição das seções em um modelo de Azure Resource Manager, consulte [entender a estrutura e a sintaxe de modelos ARM](template-syntax.md).
* Para iterar um número especificado de vezes ao criar um tipo de recurso, consulte [criar várias instâncias de recursos no Azure Resource Manager](copy-resources.md).
