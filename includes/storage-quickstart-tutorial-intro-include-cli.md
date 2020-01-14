---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/06/2019
ms.author: tamram
ms.openlocfilehash: e479f2376668a2fc3824e733996c94cfab04c9ec
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75466518"
---
## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos do Azure com o comando [az group create](/cli/azure/group). Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

Lembre-se de substituir os valores dos espaços reservados entre colchetes angulares pelos seus próprios valores:

```azurecli-interactive
az group create \
    --name <resource-group-name> \
    --location <location>
```

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Crie uma conta de armazenamento de uso geral com o comando [az storage account create](/cli/azure/storage/account). A conta de armazenamento de uso geral pode ser usada para todos os quatro serviços: blobs, arquivos, tabelas e filas.

Lembre-se de substituir os valores dos espaços reservados entre colchetes angulares pelos seus próprios valores:

```azurecli-interactive
az storage account create \
    --name <account-name> \
    --resource-group <resource-group-name> \
    --location <location> \
    --sku Standard_ZRS \
    --encryption blob
```

## <a name="specify-storage-account-credentials"></a>Especificar as credenciais da conta de armazenamento

A CLI do Azure precisa das suas credenciais da conta de armazenamento para a maioria dos comandos neste tutorial. Embora haja várias opções para fazer isso, uma das maneiras mais fáceis de fornecê-las é definir as variáveis de ambiente `AZURE_STORAGE_ACCOUNT` e `AZURE_STORAGE_KEY`.

> [!NOTE]
> Este artigo mostra como definir variáveis de ambiente usando o Bash. Outros ambientes podem exigir modificações de sintaxe.

Primeiro, exiba as chaves da conta de armazenamento usando o comando [az storage account keys list](/cli/azure/storage/account/keys). Lembre-se de substituir os valores dos espaços reservados entre colchetes angulares pelos seus próprios valores:

```azurecli-interactive
az storage account keys list \
    --account-name <account-name> \
    --resource-group <resource-group-name> \
    --output table
```

Agora, defina as variáveis de ambiente `AZURE_STORAGE_ACCOUNT` e `AZURE_STORAGE_KEY`. Você pode fazer isso no shell do Bash usando o comando `export`. Lembre-se de substituir os valores dos espaços reservados entre colchetes angulares pelos seus próprios valores:

```bash
export AZURE_STORAGE_ACCOUNT="<account-name>"
export AZURE_STORAGE_KEY="<account-key>"
```

Para obter mais informações sobre como recuperar as chaves de acesso da conta usando o portal do Azure, confira [Gerenciar chaves de acesso da conta de armazenamento](../articles/storage/common/storage-account-keys-manage.md).