---
title: Guia do Azure-criar um cofre de chaves do Azure e uma política de acesso de cofre usando o modelo de Azure Resource Manager | Microsoft Docs
description: Mostra como criar cofres de chaves do Azure e políticas de acesso do cofre usando Azure Resource Manager modelo.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/5/2020
ms.author: mbaldwin
ms.openlocfilehash: cf19561005fe2e98b7b5cf6812ff9224fd9474dc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91804293"
---
# <a name="how-to-create-azure-key-vault-and-vault-access-policy-using-a-resource-manager-template"></a>Como criar Azure Key Vault e política de acesso do cofre usando um modelo do Resource Manager

[O Azure Key Vault](../general/overview.md) é um serviço de nuvem que fornece um armazenamento seguro de segredos, como chaves, senhas e certificados, entre outros. Este guia se concentra no processo de implantação de um modelo de Azure Resource Manager (modelo ARM) para criar um cofre de chaves.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, será preciso:

* Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.


## <a name="create-key-vault-resource-manager-template"></a>Criar Key Vault modelo do Resource Manager

O modelo a seguir mostra uma maneira básica de criar o cofre de chaves. Alguns valores são especificados no modelo.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the key vault."
      }
    },
    "skuName": {
      "type": "string",
      "defaultValue": "Standard",
      "allowedValues": [
        "Standard",
        "Premium"
      ],
      "metadata": {
        "description": "Specifies whether the key vault is a standard vault or a premium vault."
      }
    }
   },
  "resources": [
    {
      "type": "Microsoft.KeyVault/vaults",
      "apiVersion": "2019-09-01",
      "name": "[parameters('keyVaultName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "enabledForDeployment": "false",
        "enabledForDiskEncryption": "false",
        "enabledForTemplateDeployment": "false",
        "tenantId": "[subscription().tenantId]",
        "accessPolicies": [],
        "sku": {
          "name": "[parameters('skuName')]",
          "family": "A"
        },
        "networkAcls": {
          "defaultAction": "Allow",
          "bypass": "AzureServices"
        }
      }
    }
  ]
}

```

Para obter mais informações sobre Key Vault configurações de modelo, consulte [referência de modelo do ARM Key Vault](https://docs.microsoft.com/azure/templates/microsoft.keyvault/vaults).

> [!IMPORTANT]
> Se o modelo for reimplantado, ele substituirá as políticas de acesso existentes no cofre de chaves. É recomendável popular a `accessPolicies` propriedade com políticas de acesso existentes para evitar o acesso perder ao cofre de chaves. 

## <a name="add-access-policy-to-key-vault-resource-manager-template"></a>Adicionar política de acesso a Key Vault modelo do Resource Manager

Você pode implantar políticas de acesso ao cofre de chaves existente sem reimplantação de todo o modelo do cofre de chaves. O modelo a seguir mostra uma maneira básica de criar políticas de acesso.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the key vault."
      }
    },
    "objectId": {
      "type": "string",
      "metadata": {
        "description": "Specifies the object ID of a user, service principal or security group in the Azure Active Directory tenant for the vault. The object ID must be unique for the list of access policies. Get it by using Get-AzADUser or Get-AzADServicePrincipal cmdlets."
      }
    },
    "keysPermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to keys in the vault. Valid values are: all, encrypt, decrypt, wrapKey, unwrapKey, sign, verify, get, list, create, update, import, delete, backup, restore, recover, and purge."
      }
    },
    "secretsPermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to secrets in the vault. Valid values are: all, get, list, set, delete, backup, restore, recover, and purge."
      }
    },
    "certificatePermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to certificates in the vault. Valid values are: all,  create, delete, update, deleteissuers, get, getissuers, import, list, listissuers, managecontacts, manageissuers,  recover, backup, restore, setissuers, and purge."
      }
    },
  "resources": [
     {
      "type": "Microsoft.KeyVault/vaults/accessPolicies",
      "name": "[concat(parameters('keyVaultName'), '/add')]",
      "apiVersion": "2019-09-01",
      "properties": {
        "accessPolicies": [
          {
            "tenantId": "[subscription().tenantId]",
            "objectId": "[parameters('objectId')]",
            "permissions": {
              "keys": "[parameters('keysPermissions')]",
              "secrets": "[parameters('secretsPermissions')]",
              "certificates": [parameters('certificatesPermissions')]
            }
          }
        ]
      }
    }
  ]
}

```
Para obter mais informações sobre Key Vault configurações de modelo, consulte [referência de modelo do ARM Key Vault](https://docs.microsoft.com/azure/templates/microsoft.keyvault/vaults/accesspolicies).

## <a name="other-available-key-vault-resource-manager-templates"></a>Outros modelos do Key Vault Resource Manager disponíveis

Há outros modelos do Resource Manager disponíveis para objetos de Key Vault:

| Segredos | simétricas | Certificados |
|--|--|--|
|[Início rápido](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-template)<br>[Referência](https://docs.microsoft.com/azure/templates/microsoft.keyvault/vaults/secrets)|N/D|N/D|

Mais modelos de Key Vault que você pode encontrar aqui: [referência do Key Vault Resource Manager](https://docs.microsoft.com/azure/templates/microsoft.keyvault/allversions)

## <a name="deploy-the-templates"></a>Implantar os modelos

Você pode usar o portal do Azure para implantar modelos acima usando a opção "criar seu próprio modelo no editor" no guia abaixo: [implantar recursos do modelo personalizado](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-portal#deploy-resources-from-custom-template)

Você também pode salvar modelos acima em arquivos e usar os seguintes comandos:  [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) e [AZ Group Deployment Create](/cli/azure/group/deployment#az-group-deployment-create):

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile key-vault-template.json
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file key-vault-template.json
```

## <a name="clean-up-resources"></a>Limpar recursos

Se você planeja continuar e trabalhar nos guias de início rápido e tutoriais subsequentes, você pode deixar esses recursos em vigor. Quando os recursos não forem mais necessários, exclua o grupo de recursos, que exclui o cofre de chaves e os recursos relacionados. Para excluir o grupo de recursos usando o CLI do Azure ou Azure PowerShell, use as etapas a seguir.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="resources"></a>Recursos

- Leia uma [Visão geral do Azure Key Vault](../general/overview.md)
- Saiba mais sobre o [Azure Resource Manager](../../azure-resource-manager/management/overview.md)
- Examine as [Melhores práticas do Azure Key Vault](../general/best-practices.md)

## <a name="next-steps"></a>Próximas etapas

- [Proteger o acesso a um cofre de chaves](secure-your-key-vault.md)
- [Autenticar em um cofre de chaves](authentication.md)
- [Guia do desenvolvedor de Azure Key Vault](developers-guide.md)
