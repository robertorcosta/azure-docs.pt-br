---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 03/25/2020
ms.author: orspodek
ms.openlocfilehash: 081ba777f6ab19be774f127383e359ff761e7f0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297916"
---
## <a name="create-a-new-key-vault"></a>Crie um novo cofre de chaves

Para criar um novo cofre de chaves usando o PowerShell, ligue para [o New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault). O cofre-chave que você usa para armazenar chaves gerenciadas pelo cliente para a criptografia do Azure Data Explorer deve ter duas configurações de proteção chave ativadas, **Soft Delete** e Não **Purga .** Substitua os valores do espaço reservado entre parênteses com seus próprios valores no exemplo abaixo.

```azurepowershell-interactive
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnableSoftDelete `
    -EnablePurgeProtection
```

## <a name="configure-the-key-vault-access-policy"></a>Configure a política de acesso ao cofre de chaves

Em seguida, configure a política de acesso para o cofre de chaves para que o cluster tenha permissões para acessá-lo. Nesta etapa, você usará a identidade gerenciada atribuída ao sistema que você atribuiu anteriormente ao cluster. Para definir a política de acesso para o cofre de chaves, ligue para [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy). Substitua os valores de espaço reservado entre parênteses por seus próprios valores e use as variáveis definidas nos exemplos anteriores.

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $cluster.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get,recover
```

## <a name="create-a-new-key"></a>Criar uma nova chave

Em seguida, crie uma nova chave no cofre da chave. Para criar uma nova chave, ligue para [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey). Substitua os valores de espaço reservado entre parênteses por seus próprios valores e use as variáveis definidas nos exemplos anteriores.

```azurepowershell-interactive
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```
