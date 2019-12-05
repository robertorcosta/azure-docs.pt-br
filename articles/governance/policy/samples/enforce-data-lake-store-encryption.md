---
title: Exemplo – Exigir criptografia para o Data Lake Storage
description: Esta definição de política de amostra exige que o estado de criptografia em contas do Data Lake Storage seja habilitado.
ms.date: 01/23/2019
ms.topic: sample
ms.openlocfilehash: 5b29caa7fa8b9169b8c2164975f9cdb2663c5cef
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74463635"
---
# <a name="sample---require-data-lake-store-encryption"></a>Amostra – Exigir a criptografia do Data Lake Storage

Essa política interna nega as contas do Data Lake Store que não tem a criptografia habilitada.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Modelo de exemplo

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.DataLakeStore/accounts"
      },
      {
        "field": "Microsoft.DataLakeStore/accounts/encryptionState",
        "equals": "Disabled"
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

Você pode implantar este modelo usando o [portal do Azure](#deploy-with-the-portal), com [PowerShell](#deploy-with-powershell) ou com o [CLI do Azure](#deploy-with-azure-cli). Para obter a política interna, use a ID `a7ff3161-0087-490a-9ad9-ad6217f4f43a`.

## <a name="deploy-with-the-portal"></a>Implantar com o portal

Ao atribuir uma política, selecione **Impor a criptografia em contas do DataLakeStore** nas definições internas disponíveis.

## <a name="deploy-with-powershell"></a>Implantação com o PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

```azurepowershell-interactive
$definition = Get-AzPolicyDefinition -Id /providers/Microsoft.Authorization/policyDefinitions/a7ff3161-0087-490a-9ad9-ad6217f4f43a

New-AzPolicyAssignment -name "Data Lake Store encryption" -PolicyDefinition $definition -Scope <scope>
```

### <a name="clean-up-powershell-deployment"></a>Limpar a implantação do PowerShell

Execute o seguinte comando para remover a atribuição de política.

```azurepowershell-interactive
Remove-AzPolicyAssignment -Name "Data Lake Store encryption" -Scope <scope>
```

## <a name="deploy-with-azure-cli"></a>Implantar com a CLI do Azure

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

```azurecli-interactive
az policy assignment create --scope <scope> --name "Data Lake Store encryption" --policy a7ff3161-0087-490a-9ad9-ad6217f4f43a
```

### <a name="clean-up-azure-cli-deployment"></a>Limpar a implantação da CLI do Azure

Execute o seguinte comando para remover a atribuição de política.

```azurecli-interactive
az policy assignment delete --name "Data Lake Store encryption" --resource-group myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

- Examine mais exemplos nos [exemplos do Azure Policy](index.md)