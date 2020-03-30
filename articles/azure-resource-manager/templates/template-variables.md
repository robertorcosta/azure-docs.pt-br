---
title: Variáveis em modelos
description: Descreve como definir variáveis em um modelo do Azure Resource Manager.
ms.topic: conceptual
ms.date: 09/05/2019
ms.openlocfilehash: cf135959d30702ea58b7a1d4fdd82625a39245d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75483811"
---
# <a name="variables-in-azure-resource-manager-template"></a>Variáveis no modelo do Azure Resource Manager

Este artigo descreve como definir e usar variáveis no modelo do Azure Resource Manager. Você usa variáveis para simplificar seu modelo. Em vez de repetir expressões complicadas ao longo do seu modelo, você define uma variável que contém a expressão complicada. Em seguida, você referencia essa variável conforme necessário ao longo de seu modelo.

O Gerenciador de Recursos resolve variáveis antes de iniciar as operações de implantação. Onde quer que a variável seja usada no modelo, o Gerenciador de Recursos substitui-a pelo valor resolvido.

## <a name="define-variable"></a>Definir variável

O seguinte exemplo mostra uma definição de variável. Ele cria um valor de cadeia de caracteres para um nome de conta de armazenamento. Ele usa várias funções de modelo para obter um valor de parâmetro, e concatena-o para uma seqüência única.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

Você não pode usar a função de [referência](template-functions-resource.md#reference) ou qualquer uma das funções da [lista](template-functions-resource.md#list) na seção variáveis. Essas funções recebem o estado de tempo de execução de um recurso e não podem ser executadas antes da implantação quando as variáveis são resolvidas.

## <a name="use-variable"></a>Usar uma variável

No modelo, você faz referência ao valor do parâmetro usando a função [variáveis.](template-functions-deployment.md#variables) O exemplo a seguir mostra como usar a variável para uma propriedade de recursos.

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

Você pode definir variáveis que possuem valores relacionados para a configuração de um ambiente. Você define a variável como um objeto com os valores. O exemplo a seguir mostra um objeto que contém valores para dois ambientes - **teste** **e prod**.

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

Os exemplos a seguir demonstram cenários para o uso de variáveis.

|Modelo  |Descrição  |
|---------|---------|
| [definições disponíveis](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variables.json) | Demonstra os diferentes tipos de variáveis. O modelo não implanta todos os recursos. Ele cria valores de variável e retorna os valores. |
| [variável de configuração](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variablesconfigurations.json) | Demonstra o uso de uma variável que define os valores de configuração. O modelo não implanta todos os recursos. Ele cria valores de variável e retorna os valores. |
| [regras de segurança de rede](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) e [arquivo de parâmetro](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json) | Cria uma matriz no formato correto para a atribuição de regras de segurança a um grupo de segurança de rede. |

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre as propriedades disponíveis para variáveis, consulte [Entenda a estrutura e a sintaxe dos modelos do Azure Resource Manager](template-syntax.md).
* Para recomendações sobre a criação de variáveis, consulte [Melhores práticas - variáveis](template-best-practices.md#variables).
