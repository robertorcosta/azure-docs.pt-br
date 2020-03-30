---
title: Defina várias instâncias de um valor de saída
description: Use a operação de cópia em um modelo do Azure Resource Manager para iterar várias vezes ao retornar um valor de uma implantação.
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: 3889260d02f438274c80e99e99136515499443e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153379"
---
# <a name="output-iteration-in-arm-templates"></a>Iteração de saída em modelos ARM

Este artigo mostra como criar mais de um valor para uma saída no modelo ARM (Azure Resource Manager). Ao adicionar o elemento **de cópia** à seção de saídas do seu modelo, você pode retornar dinamicamente uma série de itens durante a implantação.

Você também pode usar cópia com [recursos,](copy-resources.md) [propriedades em um recurso](copy-properties.md)e [variáveis.](copy-variables.md)

## <a name="outputs-iteration"></a>Iteração de saídas

O elemento de cópia tem o seguinte formato geral:

```json
"copy": [
  {
    "count": <number-of-iterations>,
    "input": <values-for-the-variable>
  }
]
```

A propriedade **count** especifica o número de iterações desejadas para o valor de saída.

A propriedade **de entrada** especifica as propriedades que você deseja repetir. Você cria uma matriz de elementos construídos a partir do valor na propriedade **de entrada.** Pode ser uma única propriedade (como uma string), ou um objeto com várias propriedades.

O exemplo a seguir cria um número variável de contas de armazenamento e retorna um ponto final para cada conta de armazenamento:

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
    "variables": {
        "baseName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[concat(copyIndex(), variables('baseName'))]",
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
    "outputs": {
        "storageEndpoints": {
            "type": "array",
            "copy": {
                "count": "[parameters('storageCount')]",
                "input": "[reference(concat(copyIndex(), variables('baseName'))).primaryEndpoints.blob]"
            }
        }
    }
}
```

O modelo anterior retorna uma matriz com os seguintes valores:

```json
[
    "https://0storagecfrbqnnmpeudi.blob.core.windows.net/",
    "https://1storagecfrbqnnmpeudi.blob.core.windows.net/"
]
```

O próximo exemplo retorna três propriedades das novas contas de armazenamento.

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
    "variables": {
        "baseName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[concat(copyIndex(), variables('baseName'))]",
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
    "outputs": {
        "storageInfo": {
            "type": "array",
            "copy": {
                "count": "[parameters('storageCount')]",
                "input": {
                    "id": "[reference(concat(copyIndex(), variables('baseName')), '2019-04-01', 'Full').resourceId]",
                    "blobEndpoint": "[reference(concat(copyIndex(), variables('baseName'))).primaryEndpoints.blob]",
                    "status": "[reference(concat(copyIndex(), variables('baseName'))).statusOfPrimary]"
                }
            }
        }
    }
}
```

O exemplo anterior retorna uma matriz com os seguintes valores:

```json
[
    {
        "id": "Microsoft.Storage/storageAccounts/0storagecfrbqnnmpeudi",
        "blobEndpoint": "https://0storagecfrbqnnmpeudi.blob.core.windows.net/",
        "status": "available"
    },
    {
        "id": "Microsoft.Storage/storageAccounts/1storagecfrbqnnmpeudi",
        "blobEndpoint": "https://1storagecfrbqnnmpeudi.blob.core.windows.net/",
        "status": "available"
    }
]
```

## <a name="next-steps"></a>Próximas etapas

* Para passar por um tutorial, consulte [Tutorial: crie várias instâncias de recursos usando modelos ARM](template-tutorial-create-multiple-instances.md).
* Para outros usos do elemento de cópia, consulte:
  * [Iteração de recursos em modelos ARM](copy-resources.md)
  * [Iteração de propriedade em modelos ARM](copy-properties.md)
  * [Iteração variável em modelos ARM](copy-variables.md)
* Se você quiser aprender sobre as seções de um modelo, consulte [Modelos DE AUTORIA DO ARM](template-syntax.md).
* Para saber como implantar seu modelo, consulte [Implantar um aplicativo com o modelo ARM](deploy-powershell.md).

