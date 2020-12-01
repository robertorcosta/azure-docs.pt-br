---
title: Variáveis em modelos
description: Descreve como definir variáveis em um modelo de Azure Resource Manager (modelo ARM).
ms.topic: conceptual
ms.date: 11/24/2020
ms.openlocfilehash: 5d9b58d63e96656c45d3494d24099bbeadc46b11
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353452"
---
# <a name="variables-in-arm-template"></a>Variáveis no modelo ARM

Este artigo descreve como definir e usar variáveis em seu modelo de Azure Resource Manager (modelo ARM). Você usa variáveis para simplificar seu modelo. Em vez de repetir expressões complicadas em todo o modelo, você define uma variável que contém a expressão complicada. Em seguida, você faz referência a essa variável conforme necessário em todo o modelo.

O Gerenciador de recursos resolve as variáveis antes de iniciar as operações de implantação. Sempre que a variável é usada no modelo, o Resource Manager a substitui pelo valor resolvido.

O formato de cada variável deve corresponder a um dos [tipos de dados](template-syntax.md#data-types).

## <a name="define-variable"></a>Definir variável

O seguinte exemplo mostra uma definição de variável. Ele cria um valor de cadeia de caracteres para um nome de conta de armazenamento. Ele usa várias funções de modelo para obter um valor de parâmetro e concatena-o a uma cadeia de caracteres exclusiva.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

Você não pode usar a função de [referência](template-functions-resource.md#reference) ou qualquer uma das funções de [lista](template-functions-resource.md#list) na seção de variáveis. Essas funções obtêm o estado de tempo de execução de um recurso e não podem ser executadas antes da implantação quando as variáveis são resolvidas.

## <a name="use-variable"></a>Usar uma variável

No modelo, você faz referência ao valor do parâmetro usando a função [Variables](template-functions-deployment.md#variables) . O exemplo a seguir mostra como usar a variável para uma propriedade de recurso.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageName')]",
    ...
  }
]
```

## <a name="configuration-variables"></a>Variáveis de configuração

Você pode definir variáveis que mantêm valores relacionados para configurar um ambiente. Você define a variável como um objeto com os valores. O exemplo a seguir mostra um objeto que contém valores para dois ambientes – **Test** e **prod**.

```json
"variables": {
  "environmentSettings": {
    "test": {
      "instanceSize": "Small",
      "instanceCount": 1
    },
    "prod": {
      "instanceSize": "Large",
      "instanceCount": 4
    }
  }
},
```

Em parâmetros, você cria um valor que indica quais valores de configuração devem ser usados.

```json
"parameters": {
  "environmentName": {
    "type": "string",
    "allowedValues": [
      "test",
      "prod"
    ]
  }
},
```

Para recuperar as configurações do ambiente especificado, use a variável e o parâmetro juntos.

```json
"[variables('environmentSettings')[parameters('environmentName')].instanceSize]"
```

## <a name="example-templates"></a>Modelos de exemplo

Os exemplos a seguir demonstram cenários para usar variáveis.

|Modelo  |Descrição  |
|---------|---------|
| [definições disponíveis](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variables.json) | Demonstra os diferentes tipos de variáveis. O modelo não implanta todos os recursos. Ele cria valores de variável e retorna os valores. |
| [variável de configuração](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variablesconfigurations.json) | Demonstra o uso de uma variável que define os valores de configuração. O modelo não implanta todos os recursos. Ele cria valores de variável e retorna os valores. |
| [regras de segurança de rede](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) e [arquivo de parâmetro](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json) | Cria uma matriz no formato correto para a atribuição de regras de segurança a um grupo de segurança de rede. |

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre as propriedades disponíveis para variáveis, consulte [entender a estrutura e a sintaxe de modelos ARM](template-syntax.md).
* Para obter recomendações sobre como criar variáveis, consulte [Best Practices-Variables](template-best-practices.md#variables).
