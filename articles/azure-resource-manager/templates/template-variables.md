---
title: Variáveis em modelos
description: Descreve como definir variáveis em um modelo de Azure Resource Manager.
ms.topic: conceptual
ms.date: 09/05/2019
ms.openlocfilehash: cf135959d30702ea58b7a1d4fdd82625a39245d2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "75483811"
---
# <a name="variables-in-azure-resource-manager-template"></a>Variáveis no modelo Azure Resource Manager

Este artigo descreve como definir e usar variáveis em seu modelo de Azure Resource Manager. Você usa variáveis para simplificar seu modelo. Em vez de repetir expressões complicadas em todo o modelo, você define uma variável que contém a expressão complicada. Em seguida, você faz referência a essa variável conforme necessário em todo o modelo.

O Gerenciador de recursos resolve as variáveis antes de iniciar as operações de implantação. Sempre que a variável é usada no modelo, o Resource Manager a substitui pelo valor resolvido.

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

* Para saber mais sobre as propriedades disponíveis para variáveis, consulte [entender a estrutura e a sintaxe de modelos de Azure Resource Manager](template-syntax.md).
* Para obter recomendações sobre como criar variáveis, consulte [Best Practices-Variables](template-best-practices.md#variables).
