---
title: Local do recurso de modelo
description: Descreve como definir o local do recurso em um modelo de Azure Resource Manager.
ms.topic: conceptual
ms.date: 09/04/2019
ms.custom: ''
ms.openlocfilehash: e1310c81d7b726e9be2fe9f38a0bb8d701035ba4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91613689"
---
# <a name="set-resource-location-in-arm-template"></a>Definir local do recurso no modelo ARM

Ao implantar um modelo de Azure Resource Manager (ARM), você deve fornecer um local para cada recurso. O local não precisa ser o mesmo local que o local do grupo de recursos.

## <a name="get-available-locations"></a>Obter locais disponíveis

Tipos de recursos diferentes têm suporte em locais diferentes. Para obter os locais com suporte para um tipo de recurso, use Azure PowerShell ou CLI do Azure.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes `
  | Where-Object ResourceTypeName -eq batchAccounts).Locations
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az provider show \
  --namespace Microsoft.Batch \
  --query "resourceTypes[?resourceType=='batchAccounts'].locations | [0]" \
  --out table
```

---

## <a name="use-location-parameter"></a>Usar parâmetro de local

Para permitir a flexibilidade ao implantar seu modelo, use um parâmetro para especificar o local dos recursos. Defina o valor padrão do parâmetro como `resourceGroup().location` .

O exemplo a seguir mostra uma conta de armazenamento implantada a um local especificado como um parâmetro:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat('storage', uniquestring(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2018-07-01",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "StorageV2",
      "properties": {}
    }
  ],
  "outputs": {
    "storageAccountName": {
      "type": "string",
      "value": "[variables('storageAccountName')]"
    }
  }
}
```

## <a name="next-steps"></a>Próximas etapas

* Para obter a lista completa das funções de modelo, veja [Funções de modelo do Gerenciador de Recursos do Azure](template-functions.md).
* Para obter mais informações sobre arquivos de modelo, consulte [entender a estrutura e a sintaxe de modelos ARM](template-syntax.md).
