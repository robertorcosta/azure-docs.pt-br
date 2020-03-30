---
title: Configure chaves gerenciadas pelo cliente no Azure Data Explorer usando o modelo do Azure Resource Manager
description: Este artigo descreve como configurar a criptografia de chaves gerenciadas pelo cliente em seus dados no Azure Data Explorer usando o modelo do Azure Resource Manager.
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 454a80089b5f74d4a70015ffcd03d0212e8c08a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297886"
---
# <a name="configure-customer-managed-keys-using-the-azure-resource-manager-template"></a>Configure as chaves gerenciadas pelo cliente usando o modelo do Azure Resource Manager

> [!div class="op_single_selector"]
> * [Portal](customer-managed-keys-portal.md)
> * [C #](customer-managed-keys-csharp.md)
> * [Modelo de Gerenciador de recursos do Azure](customer-managed-keys-resource-manager.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](../../includes/data-explorer-configure-customer-managed-keys.md)]

[!INCLUDE [data-explorer-configure-customer-managed-keys part 2](../../includes/data-explorer-configure-customer-managed-keys-b.md)]

## <a name="configure-encryption-with-customer-managed-keys"></a>Configurar criptografia com chaves gerenciadas pelo cliente

Nesta seção, você configura chaves gerenciadas pelo cliente usando modelos do Azure Resource Manager. Por padrão, a criptografia do Azure Data Explorer usa chaves gerenciadas pela Microsoft. Nesta etapa, configure o cluster Azure Data Explorer para usar chaves gerenciadas pelo cliente e especifique a chave para associar ao cluster.

Você pode implantar o modelo do Azure Resource Manager usando o portal Azure ou usando o PowerShell.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "clusterName": {
      "type": "string",
      "defaultValue": "[concat('kusto', uniqueString(resourceGroup().id))]",
      "metadata": {
        "description": "Name of the cluster to create"
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
  "variables": {},
  "resources": [
    {
      "name": "[parameters('clusterName')]",
      "type": "Microsoft.Kusto/clusters",
      "sku": {
        "name": "Standard_D13_v2",
        "tier": "Standard",
        "capacity": 2
      },
      "apiVersion": "2019-09-07",
      "location": "[parameters('location')]",
      "properties": {
        "keyVaultProperties": {
          "keyVaultUri": "<keyVaultUri>",
          "keyName": "<keyName>",
          "keyVersion": "<keyVersion"
        }
      }
    }
  ]
}
```

## <a name="update-the-key-version"></a>Atualize a versão-chave

Quando você criar uma nova versão de uma chave, você precisará atualizar o cluster para usar a nova versão. Primeiro, `Get-AzKeyVaultKey` ligue para obter a versão mais recente da chave. Em seguida, atualize as propriedades do cofre chave do cluster para usar a nova versão da chave, como mostrado na [criptografia Configurar com chaves gerenciadas pelo cliente](#configure-encryption-with-customer-managed-keys).

## <a name="next-steps"></a>Próximas etapas

* [Grupos secure Azure Data Explorer no Azure](security.md)
* [Configure identidades gerenciadas para o cluster Azure Data Explorer](managed-identities.md)
* [Proteja seu cluster no Azure Data Explorer - portal Azure,](manage-cluster-security.md) ativando a criptografia em repouso.
* [Configure as chaves gerenciadas pelo cliente usando C #](customer-managed-keys-csharp.md)

