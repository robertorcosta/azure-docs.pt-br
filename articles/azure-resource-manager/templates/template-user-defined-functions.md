---
title: Funções definidas pelo usuário em modelos
description: Descreve como definir e usar funções definidas pelo usuário em um modelo do Azure Resource Manager.
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 2c09572a460aa028b23987033d2b77e2aad8a0cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78943212"
---
# <a name="user-defined-functions-in-azure-resource-manager-template"></a>Funções definidas pelo usuário no modelo azure Resource Manager

Dentro de seu modelo, você pode criar suas próprias funções. Essas funções estão disponíveis para uso em seu modelo. As funções definidas pelo usuário são separadas das [funções de modelo padrão](template-functions.md) que estão automaticamente disponíveis dentro do seu modelo. Crie suas próprias funções quando tiver expressões complicadas que são usadas repetidamente em seu modelo.

Este artigo descreve como adicionar funções definidas pelo usuário no modelo do Azure Resource Manager.

## <a name="define-the-function"></a>Defina a função

As suas funções exigem um valor de namespace para evitar conflitos de nomenclatura com funções de modelo. O exemplo a seguir mostra uma função que retorna um nome único:

```json
"functions": [
  {
    "namespace": "contoso",
    "members": {
      "uniqueName": {
        "parameters": [
          {
            "name": "namePrefix",
            "type": "string"
          }
        ],
        "output": {
          "type": "string",
          "value": "[concat(toLower(parameters('namePrefix')), uniqueString(resourceGroup().id))]"
        }
      }
    }
  }
],
```

## <a name="use-the-function"></a>Use a função

O exemplo a seguir mostra um modelo que inclui uma função definida pelo usuário. Ele usa essa função para obter um nome único para uma conta de armazenamento. O modelo tem um parâmetro chamado **storageNamePrefix** que ele passa como um parâmetro para a função.

```json
{
 "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
 "contentVersion": "1.0.0.0",
 "parameters": {
   "storageNamePrefix": {
     "type": "string",
     "maxLength": 11
   }
 },
 "functions": [
  {
    "namespace": "contoso",
    "members": {
      "uniqueName": {
        "parameters": [
          {
            "name": "namePrefix",
            "type": "string"
          }
        ],
        "output": {
          "type": "string",
          "value": "[concat(toLower(parameters('namePrefix')), uniqueString(resourceGroup().id))]"
        }
      }
    }
  }
],
 "resources": [
   {
     "type": "Microsoft.Storage/storageAccounts",
     "apiVersion": "2019-04-01",
     "name": "[contoso.uniqueName(parameters('storageNamePrefix'))]",
     "location": "South Central US",
     "sku": {
       "name": "Standard_LRS"
     },
     "kind": "StorageV2",
     "properties": {
       "supportsHttpsTrafficOnly": true
     }
   }
 ]
}
```

## <a name="limitations"></a>Limitações

Ao definir uma função de usuário, há algumas restrições:

* A função não pode acessar variáveis.
* A função só pode usar os parâmetros que são definidos na função. Quando você usa a função [parâmetros](template-functions-deployment.md#parameters) dentro de uma função definida pelo usuário, você está restrito aos parâmetros para essa função.
* A função não pode chamar outras funções definidas pelo usuário.
* A função não pode usar a função de [referência](template-functions-resource.md#reference) ou qualquer uma das funções da [lista.](template-functions-resource.md#list)
* Os parâmetros para a função não podem ter valores padrão.


## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre as propriedades disponíveis para funções definidas pelo usuário, consulte [Entenda a estrutura e a sintaxe dos modelos do Azure Resource Manager](template-syntax.md).
* Para obter uma lista das funções de modelo disponíveis, consulte as [funções do modelo Do Azure Resource Manager](template-functions.md).
