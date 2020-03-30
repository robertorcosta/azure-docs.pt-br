---
title: Criar uma conta de armazenamento blob de blocos - Azure Storage | Microsoft Docs
description: Mostra como criar uma conta Azure BlockBlobStorage com características de desempenho premium.
author: tamram
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 6303644ada5c6f093611dba94daf8006f8cc5819
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536897"
---
# <a name="create-a-blockblobstorage-account"></a>Criar uma conta BlockBlobStorage

O tipo de conta BlockBlobStorage permite criar blobs de bloco com características de desempenho premium. Esse tipo de conta de armazenamento é otimizada para cargas de trabalho com altas taxas de transações ou que requerem tempos de acesso muito rápidos. Este artigo mostra como criar uma conta BlockBlobStorage usando o portal Azure, o Azure CLI ou o Azure PowerShell.

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

Para obter mais informações sobre contas blockblobArmazenamento, consulte [visão geral da conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-account-overview).

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Nenhum.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Este artigo de como fazer requer o módulo Azure PowerShell versão 1.2.0 ou posterior. Execute `Get-Module -ListAvailable Az` para localizar a versão atual. Se você precisar instalá-lo ou atualizá-lo, confira [Instalar o módulo do Azure PowerShell](/powershell/azure/install-Az-ps).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Você pode entrar no Azure e executar comandos da CLI do Azure de uma das duas formas a seguir:

- Você pode executar comandos CLI de dentro do portal Azure, no Azure Cloud Shell.
- Você pode instalar o CLI e executar comandos CLI localmente.

### <a name="use-azure-cloud-shell"></a>Usar o Azure Cloud Shell

O Azure Cloud Shell é um shell Bash gratuito que pode ser executado diretamente no portal do Azure. O Azure CLI está pré-instalado e configurado para uso com sua conta. Clique no botão **Cloud Shell** no menu na seção superior direita do portal Azure:

[![Cloud Shell](../common/media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

O botão lança uma concha interativa que você pode usar para executar as etapas descritas neste artigo de como fazer:

[![Captura de tela mostrando a janela Cloud Shell no portal](../common/media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>Instalar a CLI localmente

Você também pode instalar e usar a CLI do Azure localmente. Este artigo de como fazer requer que você esteja executando a versão 2.0.46 do Azure CLI ou posterior. Execute `az --version` para encontrar a versão. Caso precise instalar ou atualizar, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli). 

---

## <a name="sign-in-to-azure"></a>Entrar no Azure

# <a name="portal"></a>[Portal](#tab/azure-portal)

Faça login no [portal Azure](https://portal.azure.com).

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Entre em sua assinatura do Azure com o comando `Connect-AzAccount` e siga as instruções na tela para fazer a autenticação.

```powershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Para lançar o Azure Cloud Shell, entre no [portal Azure](https://portal.azure.com).

Para fazer login na instalação local da CLI, execute o comando [az login:](/cli/azure/reference-index#az-login)

```azurecli
az login
```

---

## <a name="create-a-blockblobstorage-account"></a>Criar uma conta BlockBlobStorage

## <a name="portal"></a>[Portal](#tab/azure-portal)
Para criar uma conta blockblobStorage no portal Azure, siga estas etapas:

1. No portal Azure, selecione **Todos os serviços** > categoria **Armazenamento** > **armazenamento**.

1. Em **Contas de armazenamento,** selecione **Adicionar**.

1. No campo **Assinatura,** selecione a assinatura na qual criar a conta de armazenamento.

1. No **campo grupo Recurso,** selecione um grupo de recursos existente ou selecione **Criar novo**e digite um nome para o novo grupo de recursos.

1. No campo Nome da **conta de armazenamento,** digite um nome para a conta. Observe as seguintes diretrizes:

   - O nome deve ser exclusivo em todo o Azure.
   - O nome deve ter entre três e 24 caracteres.
   - O nome pode incluir apenas números e letras minúsculas.

1. No campo **Local,** selecione um local para a conta de armazenamento ou use o local padrão.

1. Para o resto das configurações, configure o seguinte:

   |Campo     |Valor  |
   |---------|---------|
   |**Desempenho**    |  Selecione **Premium**.   |
   |**Tipo de conta**    | Selecione **BlockBlobStorage**.      |
   |**Replicação**    |  Deixe a configuração padrão de **LRS (Locally-redundante) de armazenamento (LRS).**      |

   ![Mostra a im do portal para criar uma conta de armazenamento blob de bloco](media/storage-blob-create-account-block-blob/create-block-blob-storage-account.png)

1. Selecione **'Revisar + criar para** revisar as configurações da conta de armazenamento'.

1. Selecione **Criar**.

## <a name="azure-powershell"></a>[Azure Powershell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

1. Abra uma sessão elevada do Windows PowerShell (Execute como administrador).

1. Execute o seguinte comando para garantir `Az` que a versão mais recente do módulo PowerShell esteja instalada.

   ```powershell
   Install-Module -Name Az -AllowClobber
   ```

1. Abra um novo console PowerShell e faça login com sua conta do Azure.

   ```powershell
   Connect-AzAccount -SubscriptionId <SubscriptionID>
   ```

1. Se necessário, crie um novo grupo de recursos. Substitua os valores nas cotações e execute o seguinte comando.

   ```powershell
   $resourcegroup = "new_resource_group_name"
   $location = "region_name"
   New-AzResourceGroup -Name $resourceGroup -Location $location
   ```

1. Crie a conta BlockBlobStorage. Substitua os valores nas cotações e execute o seguinte comando.

   ```powershell
   $resourcegroup = "resource_group_name"
   $storageaccount = "new_storage_account_name"
   $location = "region_name"

   New-AzStorageAccount -ResourceGroupName $resourcegroup -Name $storageaccount -Location $location -Kind "BlockBlobStorage" -SkuName "Premium_LRS"
   ```

## <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Para criar uma conta blob de bloco usando o Azure CLI, você deve primeiro instalar o Azure CLI v. 2.0.46 ou uma versão posterior. Execute `az --version` para encontrar a versão. Caso precise instalar ou atualizar, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

1. Entre em sua assinatura do Azure.

   ```azurecli
   az login
   ```

1. Se necessário, crie um novo grupo de recursos. Substitua os valores entre parênteses (incluindo os suportes) e execute o seguinte comando.

   ```azurecli
   az group create \
    --name "<new_resource_group_name>" \
    --location "<location>"
   ```

1. Crie a conta BlockBlobStorage. Substitua os valores entre parênteses (incluindo os suportes) e execute o seguinte comando.

   ```azurecli
   az storage account create \
    --location "<location>" \
    --name "<new_storage_account_name>" \
    --resource-group "<resource_group_name>" \
    --kind "BlockBlobStorage" \
    --sku "Premium_LRS"
   ```

---

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre as contas de armazenamento, confira [Visão geral da conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-account-overview).

- Para obter mais informações sobre grupos de recursos, consulte [a visão geral do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
