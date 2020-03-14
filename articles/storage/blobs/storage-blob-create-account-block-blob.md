---
title: Criar uma conta de armazenamento de blobs de blocos-armazenamento do Azure | Microsoft Docs
description: Mostra como criar uma conta do Azure BlockBlobStorage com características de desempenho premium.
author: tamram
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 6dd5d98b559d49656c44b75e86398a017d923203
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/14/2020
ms.locfileid: "79371079"
---
# <a name="create-a-blockblobstorage-account"></a>Criar uma conta do BlockBlobStorage

O tipo de conta BlockBlobStorage permite criar blobs de blocos com características de desempenho premium. Esse tipo de conta de armazenamento é otimizado para cargas de trabalho com altas taxas de transações ou que exigem tempos de acesso muito rápidos. Este artigo mostra como criar uma conta do BlockBlobStorage usando o portal do Azure, o CLI do Azure ou o Azure PowerShell.

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

Para obter mais informações sobre contas do BlockBlobStorage, consulte [visão geral da conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-account-overview).

## <a name="prerequisites"></a>Prerequisites

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Nenhum.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Este artigo de instruções requer o módulo Azure PowerShell AZ Version 1.2.0 ou posterior. Execute `Get-Module -ListAvailable Az` para localizar a versão atual. Se você precisar instalá-lo ou atualizá-lo, confira [Instalar o módulo do Azure PowerShell](/powershell/azure/install-Az-ps).

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Você pode entrar no Azure e executar comandos da CLI do Azure de uma das duas formas a seguir:

- Você pode executar comandos da CLI de dentro do portal do Azure, em Azure Cloud Shell.
- Você pode instalar a CLI e executar comandos da CLI localmente.

### <a name="use-azure-cloud-shell"></a>Usar o Azure Cloud Shell

O Azure Cloud Shell é um shell Bash gratuito que pode ser executado diretamente no portal do Azure. O CLI do Azure é pré-instalado e configurado para ser usado com sua conta. Clique no botão **Cloud Shell** no menu na seção superior direita da portal do Azure:

[![Cloud Shell](../common/media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

O botão inicia um shell interativo que você pode usar para executar as etapas descritas neste artigo de instruções:

[![Captura de tela mostrando a janela do Cloud Shell no portal](../common/media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>Instalar a CLI localmente

Você também pode instalar e usar a CLI do Azure localmente. Este artigo de instruções requer que você esteja executando o CLI do Azure versão 2.0.46 ou posterior. Execute `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli). 

---

## <a name="sign-in-to-azure"></a>Entrar no Azure

# <a name="portal"></a>[Portal](#tab/azure-portal)

Entre no [portal do Azure](https://portal.azure.com).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Entre em sua assinatura do Azure com o comando `Connect-AzAccount` e siga as instruções na tela para fazer a autenticação.

```powershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para iniciar o Azure Cloud Shell, entre no [portal do Azure](https://portal.azure.com).

Para fazer logon em sua instalação local da CLI, execute o comando [AZ login](/cli/azure/reference-index#az-login) :

```cli
az login
```

---

## <a name="create-a-blockblobstorage-account"></a>Criar uma conta do BlockBlobStorage

## <a name="portal"></a>[Portal](#tab/azure-portal)
Para criar uma conta do BlockBlobStorage no portal do Azure, siga estas etapas:

1. Na portal do Azure, selecione **todos os serviços** > Categoria de **armazenamento** > **contas de armazenamento**.

1. Em **contas de armazenamento**, selecione **Adicionar**.

1. No campo **assinatura** , selecione a assinatura na qual criar a conta de armazenamento.

1. No campo **grupo de recursos** , selecione um grupo de recursos existente ou selecione **criar novo**e insira um nome para o novo grupo de recursos.

1. No campo **nome da conta de armazenamento** , insira um nome para a conta. Observe as seguintes diretrizes:

   - O nome deve ser exclusivo em todo o Azure.
   - O nome deve ter entre três e 24 caracteres.
   - O nome pode incluir apenas números e letras minúsculas.

1. No campo **local** , selecione um local para a conta de armazenamento ou use o local padrão.

1. Para o restante das configurações, configure o seguinte:

   |Campo     |Valor  |
   |---------|---------|
   |**Desempenho**    |  Selecione **Premium**.   |
   |**Tipo de conta**    | Selecione **BlockBlobStorage**.      |
   |**Replicação**    |  Deixe a configuração padrão de **armazenamento com redundância local (LRS)** .      |

   ![Mostra a interface do usuário do portal para criar uma conta de armazenamento de blob de blocos](media/storage-blob-create-account-block-blob/create-block-blob-storage-account.png)

1. Selecione **revisão + criar** para revisar as configurações da conta de armazenamento.

1. Selecione **Criar**.

## <a name="azure-powershell"></a>[Powershell do Azure](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

1. Abra uma sessão do Windows PowerShell com privilégios elevados (executar como administrador).

1. Execute o comando a seguir para certificar-se de que a versão mais recente do módulo `Az` PowerShell esteja instalada.

   ```powershell
   Install-Module -Name Az -AllowClobber
   ```

1. Abra um novo console do PowerShell e entre com sua conta do Azure.

   ```powershell
   Connect-AzAccount -SubscriptionId <SubscriptionID>
   ```

1. Se necessário, crie um novo grupo de recursos. Substitua os valores entre aspas e execute o comando a seguir.

   ```powershell
   $resourcegroup = "new_resource_group_name"
   $location = "region_name"
   New-AzResourceGroup -Name $resourceGroup -Location $location
   ```

1. Crie a conta BlockBlobStorage. Substitua os valores entre aspas e execute o comando a seguir.

   ```powershell
   $resourcegroup = "resource_group_name"
   $storageaccount = "new_storage_account_name"
   $location = "region_name"

   New-AzStorageAccount -ResourceGroupName $resourcegroup -Name $storageaccount -Location $location -Kind "BlockBlobStorage" -SkuName "Premium_LRS"
   ```

## <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para criar uma conta de blob de blocos usando o CLI do Azure, você deve primeiro instalar o CLI do Azure v. 2.0.46 ou uma versão posterior. Execute `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

1. Entre em sua assinatura do Azure.

   ```azurecli
   az login
   ```

1. Se necessário, crie um novo grupo de recursos. Substitua os valores entre colchetes (incluindo os colchetes) e execute o comando a seguir.

   ```azurecli
   az group create \
    --name "<new_resource_group_name>" \
    --location "<location>"
   ```

1. Crie a conta BlockBlobStorage. Substitua os valores entre colchetes (incluindo os colchetes) e execute o comando a seguir.

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

- Para saber mais sobre os grupos de recursos, confira [Visão geral do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
