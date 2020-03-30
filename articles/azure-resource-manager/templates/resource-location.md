---
title: Localização do recurso de modelo
description: Descreve como definir a localização dos recursos em um modelo do Azure Resource Manager.
ms.topic: conceptual
ms.date: 09/04/2019
ms.openlocfilehash: a8324dac1232eecd5624e5f1dc0e6656295c0a10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156422"
---
# <a name="set-resource-location-in-arm-template"></a>Definir a localização do recurso no modelo ARM

Ao implantar um modelo ARM (Azure Resource Manager, gerenciador de recursos do Azure), você deve fornecer um local para cada recurso. O local não precisa ser o mesmo local que a localização do grupo de recursos.

## <a name="get-available-locations"></a>Obtenha locais disponíveis

Tipos de recursos diferentes têm suporte em locais diferentes. Para obter os locais suportados para um tipo de recurso, use o Azure PowerShell ou o Azure CLI.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes `
  | Where-Object ResourceTypeName -eq batchAccounts).Locations
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az provider show \
  --namespace Microsoft.Batch \
  --query "resourceTypes[?resourceType=='batchAccounts'].locations | [0]" \
  --out table
```

---

## <a name="use-location-parameter"></a>Usar parâmetro de localização

Para permitir flexibilidade ao implantar seu modelo, use um parâmetro para especificar o local para recursos. Defina o valor padrão `resourceGroup().location`do parâmetro como .

O exemplo a seguir mostra uma conta de armazenamento implantada a um local especificado como um parâmetro:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
* Para obter mais informações sobre arquivos de modelo, consulte [Entenda a estrutura e a sintaxe dos modelos ARM](template-syntax.md).
