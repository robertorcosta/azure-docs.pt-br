---
title: Configurar Azure Key Vault para VMs Linux
description: Como configurar o Key Vault para uso com uma máquina virtual do Azure Resource Manager com a CLI do Azure.
author: mimckitt
manager: vashan
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 02/24/2017
ms.author: mimckitt
ms.openlocfilehash: 9ae486ee522982b116af58cfb7cbfbca66a7ef4a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81458736"
---
# <a name="how-to-set-up-key-vault-for-virtual-machines-with-the-azure-cli"></a>Como configurar o Key Vault para máquinas virtuais com a CLI do Azure

Na pilha do Azure Resource Manager, os segredos/certificados são modelados como recursos que são fornecidos pelo Key Vault. Para saber mais sobre o Cofre de Chaves do Azure, consulte [O que é o Cofre de Chaves do Azure?](../../key-vault/general/overview.md) Para que o Key Vault seja usado com VMs do Azure Resource Manager, a propriedade *EnabledForDeployment* no Key Vault deverá ser definida como true. Este artigo mostra como configurar o Key Vault para uso com VMs (máquinas virtuais) do Azure usando a CLI do Azure. 

Para realizar essas etapas, é preciso ter a [CLI do Azure](/cli/azure/install-az-cli2) mais recente instalada e conectada a uma conta do Azure usando [az login](/cli/azure/reference-index).

## <a name="create-a-key-vault"></a>Criar um cofre de chaves
Crie um Key Vault e atribua a política de implantação com [az keyvault create](/cli/azure/keyvault). O exemplo a seguir cria um Key Vault chamado `myKeyVault` no grupo de recursos `myResourceGroup`:

```azurecli
az keyvault create -l westus -n myKeyVault -g myResourceGroup --enabled-for-deployment true
```

## <a name="update-a-key-vault-for-use-with-vms"></a>Atualizar um Key Vault para uso com VMs
Defina a política de implantação em um Key Vault existente com [az keyvault update](/cli/azure/keyvault). O exemplo a seguir atualiza o cofre de chaves chamado `myKeyVault` no grupo de recursos `myResourceGroup`:

```azurecli
az keyvault update -n myKeyVault -g myResourceGroup --set properties.enabledForDeployment=true
```

## <a name="use-templates-to-set-up-key-vault"></a>Usar modelos para configurar o Cofre de Chaves
Ao usar um modelo, você precisa definir a propriedade `enabledForDeployment` como `true` para o recurso do Key Vault da seguinte forma:

```json
{
    "type": "Microsoft.KeyVault/vaults",
    "name": "ContosoKeyVault",
    "apiVersion": "2015-06-01",
    "location": "<location-of-key-vault>",
    "properties": {
    "enabledForDeployment": "true",
    ....
    ....
    }
}
```

## <a name="next-steps"></a>Próximas etapas
Para obter outras opções que você pode definir ao criar um Key Vault usando modelos, consulte [Create a key vault](https://azure.microsoft.com/documentation/templates/101-key-vault-create/) (Criar um Key Vault).
