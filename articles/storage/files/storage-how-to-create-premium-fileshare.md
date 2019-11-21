---
title: Create a premium Azure file share
description: In this article, you learn how to create a premium Azure file share.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 05/05/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 4b3e51b5e8d0ee9b62a7e7bc39955396f327c7e7
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74209547"
---
# <a name="how-to-create-an-premium-azure-file-share"></a>How to create an premium Azure file share
Premium file shares are offered on solid-state disk (SSD) storage media and are useful for IO-intensive workloads, including hosting databases and high-performance computing (HPC). Premium file shares are hosted in a special purpose storage account kind, called a FileStorage account. Premium file shares are designed for high performance and enterprise scale applications, providing consistent low latency, high IOPS, and high throughput shares.

This article shows you how to create this new account type using [Azure portal](https://portal.azure.com/), Azure PowerShell, and Azure CLI.

## <a name="prerequisites"></a>Pré-requisitos

To access Azure resources including premium Azure file shares, you'll need an Azure subscription. Se você ainda não tiver uma assinatura, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-a-premium-file-share-using-the-azure-portal"></a>Create a premium file share using the Azure portal

### <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no [portal do Azure](https://portal.azure.com/).

### <a name="create-a-filestorage-storage-account"></a>Create a filestorage storage account

Now you're ready to create your storage account.

Cada conta de armazenamento deve pertencer a um grupo de recursos do Azure. Um grupo de recursos é um contêiner lógico para agrupar seus serviços do Azure. Quando você cria uma conta de armazenamento, tem a opção de criar um novo grupo de recursos ou usar um grupo de recursos existente. This article shows how to create a new resource group.

1. In the Azure portal, select **Storage Accounts** on the left menu.

    ![Azure portal main page select storage account](media/storage-how-to-create-premium-fileshare/azure-portal-storage-accounts.png)

1. Na janela **Contas de Armazenamento** que aparece, escolha **Adicionar**.
1. Selecione a assinatura na qual você deseja criar a conta de armazenamento.
1. No campo **Grupo de recursos**, selecione **Criar novo**. Insira um nome para seu novo grupo de recursos, conforme mostrado na imagem a seguir.

1. Em seguida, insira um nome para sua conta de armazenamento. O nome escolhido deve ser exclusivo no Azure. O nome também deve ter entre 3 e 24 caracteres e pode incluir apenas números e letras minúsculas.
1. Selecione um local para sua conta de armazenamento ou use o local padrão.
1. For **Performance** select **Premium**.
1. Select **Account kind** and choose **FileStorage**.
1. Leave **Replication** set to its default value of **Locally-redundant storage (LRS)** .

    ![How to create a storage account for a premium file share](media/storage-how-to-create-premium-fileshare/create-filestorage-account.png)

1. Selecione **Revisar + Criar** para examinar as configurações da conta de armazenamento e criar a conta.
1. Clique em **Criar**.

Once your storage account resource has been created, navigate to it.

### <a name="create-a-premium-file-share"></a>Criar um compartilhamento de arquivo premium

1. In the left menu for the storage account, scroll to the **File service** section, then select **Files**.
1. Select **File share** to create a premium file share.
1. Enter a name and a desired quota for your file share, then select **Create**.

> [!NOTE]
> Provisioned share sizes is specified by the share quota, file shares are billed on the provisioned size, refer to the [pricing page](https://azure.microsoft.com/pricing/details/storage/files/) for more details.

   ![Criar um compartilhamento de arquivo premium](media/storage-how-to-create-premium-fileshare/create-premium-file-share.png)

### <a name="clean-up-resources"></a>Limpar recursos

If you would like to clean up the resources created in this article, you can simply delete the resource group. Deleting the resource group also deletes the associated storage account as well as any other resources associated with the resource group.

## <a name="create-a-premium-file-share-using-powershell"></a>Create a premium file share using PowerShell

### <a name="create-an-account-using-powershell"></a>Criar uma conta usando o PowerShell

Primeiro, instale a última versão do módulo [PowerShellGet](/powershell/scripting/gallery/installing-psget) do PowerShell.

Em seguida, atualize seu módulo PowerShell, entre em sua assinatura do Azure, crie um grupo de recursos e, em seguida, crie uma conta de armazenamento.

### <a name="upgrade-your-powershell-module"></a>Upgrade your PowerShell module

To interact with a premium file share from with PowerShell, you'll need to install an Az.Storage module version 1.4.0, or the latest Az.Storage module.

Comece abrindo uma sessão do PowerShell com permissões elevadas.

Install the Az.Storage module:

```powershell
Install-Module Az.Storage -Repository PSGallery -AllowClobber -Force
```

### <a name="sign-in-to-your-azure-subscription"></a>Entre em sua Assinatura do Azure

Use o comando `Connect-AzAccount` e siga as instruções na tela para fazer a autenticação.

```powershell
Connect-AzAccount
```

### <a name="create-a-resource-group"></a>Criar um grupos de recursos

Para criar um novo grupo de recursos com o PowerShell, use o comando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup):

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hardcoding it repeatedly
$resourceGroup = "storage-how-to-resource-group"
$location = "westus2"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

### <a name="create-a-filestorage-storage-account"></a>Create a FileStorage storage account

To create a filestorage storage account from PowerShell, use the [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount) command:

```powershell
$storageAcct = New-AzStorageAccount -ResourceGroupName $resourceGroup -Name "fileshowto" -SkuName "Premium_LRS" -Location "westus2" -Kind "FileStorage"
```

### <a name="create-a-premium-file-share"></a>Criar um compartilhamento de arquivo premium

Now that you have a FileStorage account, you can create a premium file share. Use the [New-AzStorageShare](/powershell/module/az.storage/New-AzStorageShare) cmdlet to create one.

> [!NOTE]
> Provisioned share sizes is specified by the share quota, file shares are billed on the provisioned size, refer to the [pricing page](https://azure.microsoft.com/pricing/details/storage/files/) for more details.

```powershell
New-AzStorageShare `
   -Name myshare `
   -Context $storageAcct.Context
```

### <a name="clean-up-resources"></a>Limpar recursos

Para remover o grupo de recursos e seus recursos associados, incluindo a nova conta de armazenamento, use o comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup): 

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="create-a-premium-file-share-using-azure-cli"></a>Create a premium file share using Azure CLI

Para iniciar o Azure Cloud Shell, entre no [portal do Azure](https://portal.azure.com).

If you want to log into your local installation of the CLI, first make sure you have the latest version, then run the login command:

```cli
az login
```

### <a name="create-a-resource-group"></a>Criar um grupos de recursos

Para criar um novo grupo de recursos com a CLI do Azure, use o comando [az group create](/cli/azure/group).

```azurecli-interactive
az group create `
    --name files-howto-resource-group `
    --location westus2
```

### <a name="create-a-filestorage-storage-account"></a>Create a FileStorage storage account

To create a FileStorage storage account from the Azure CLI, use the [az storage account create](/cli/azure/storage/account) command.

```azurecli-interactive
az storage account create `
    --name fileshowto `
    --resource-group files-howto-resource-group `
    --location westus `
    --sku Premium_LRS `
    --kind FileStorage
```

### <a name="get-the-storage-account-key"></a>Obter a chave da conta de armazenamento

Storage account keys control access to resources in a storage account, in this article, we use the key in order to create a premium file share. As chaves são criadas automaticamente quando você cria uma conta de armazenamento. Você pode obter as chaves da conta de armazenamento usando o comando [az storage account keys list](/cli/azure/storage/account/keys):

```azurecli-interactive 
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

### <a name="create-a-premium-file-share"></a>Criar um compartilhamento de arquivo premium

Now that you have a filestorage account, you can create a premium file share. Use the [az storage share create](/cli/azure/storage/share) command to create one.

> [!NOTE]
> Provisioned share sizes is specified by the share quota, file shares are billed on the provisioned size, refer to the [pricing page](https://azure.microsoft.com/pricing/details/storage/files/) for more details.

```azurecli-interactive
az storage share create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" 
```

### <a name="clean-up-resources"></a>Limpar recursos

Para remover o grupo de recursos e seus recursos associados, incluindo a nova conta de armazenamento, use o comando [az group delete](/cli/azure/group).

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Próximos passos

In this article, you've created a premium file share. To learn about the performance this account offers, continue to the performance tier section of the planning guide.

> [!div class="nextstepaction"]
> [File share performance tiers](storage-files-planning.md#file-share-performance-tiers)