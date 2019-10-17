---
title: Alterar a ID de locatário do cofre de chaves de depois de mover uma assinatura - Azure Key Vault| Microsoft Docs
description: Saiba como mudar a ID de locatário para um cofre de chaves depois que uma assinatura é movida para um locatário diferente
services: key-vault
author: amitbapat
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: ambapat
ms.openlocfilehash: bc2e73d037b05c012002d7a07e2a2af2431423fa
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72428913"
---
# <a name="change-a-key-vault-tenant-id-after-a-subscription-move"></a>Alterar a ID de locatário do cofre de chaves depois de mover uma assinatura

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


Quando você cria um novo cofre de chaves em uma assinatura, ele é vinculado automaticamente à ID de locatário do Azure Active Directory padrão para essa assinatura. Todas as entradas de política de acesso também são vinculadas a essa ID de locatário. 

Se você mover a assinatura do Azure do locatário A para o locatário B, os cofres de chaves existentes ficarão inacessíveis para as entidades (usuários e aplicativos) no locatário B. Para corrigir esse problema, você precisa:

* Alterar a ID do locatário associada a todos os cofres de chaves existentes na assinatura para o locatário B.
* Remover todas as entradas de política de acesso existentes.
* Adicionar novas entradas de política de acesso associadas ao locatário B.

Por exemplo, se você tiver o cofre de chaves 'myvault' em uma assinatura que foi movida do locatário A para o B, poderá usar o Azure PowerShell para alterar a ID de locatário e remover políticas de acesso antigas.

```azurepowershell
Select-AzSubscription -SubscriptionId <your-subscriptionId>                # Select your Azure Subscription
$vaultResourceId = (Get-AzKeyVault -VaultName myvault).ResourceId          # Get your key vault's Resource ID 
$vault = Get-AzResource –ResourceId $vaultResourceId -ExpandProperties     # Get the properties for your key vault
$vault.Properties.TenantId = (Get-AzContext).Tenant.TenantId               # Change the Tenant that your key vault resides in
$vault.Properties.AccessPolicies = @()                                     # Access policies can be updated with real
                                                                           # applications/users/rights so that it does not need to be                             # done after this whole activity. Here we are not setting 
                                                                           # any access policies. 
Set-AzResource -ResourceId $vaultResourceId -Properties $vault.Properties  # Modifies the key vault's properties.
````

Ou você pode usar a CLI do Azure.

```azurecli
az account set <your-subscriptionId>                                       # Select your Azure Subscription
tenantId=$(az account show --query tenantId)                               # Get your tenantId
az keyvault update -n myvault --remove Properties.accessPolicies           # Remove the access policies
az keyvault update -n myvault --set Properties.tenantId=$tenantId          # Update the key vault tenantId
```

Agora que o cofre está associado à ID de locatário correta e as antigas entradas da política de acesso foram removidas, defina novas entradas da política de acesso com o cmdlet [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy) do Azure PowerShell ou o comando [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) da CLI do Azure.

Se você estiver usando uma identidade gerenciada para recursos do Azure, será necessário atualizá-la também para o novo locatário do Azure AD também. Para obter mais informações sobre identidades gerenciadas, confira [Fornecer autenticação do Key Vault com uma identidade gerenciada](managed-identity.md).


Se estiver usando o MSI, você também precisará atualizar a identidade do MSI, já que a identidade antiga não estará mais no locatário correto do AAD.

## <a name="next-steps"></a>Próximas etapas

Se você tiver dúvidas sobre o Cofre de Chaves do Azure, visite os [Fóruns do Cofre de Chaves do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault).
