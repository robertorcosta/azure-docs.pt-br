---
title: Criar um compartilhamento de arquivos premium do Azure
description: Saiba como criar um compartilhamento de arquivos do Azure Premium usando o portal do Azure, Azure PowerShell module ou o CLI do Azure.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 08/26/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 1ec8e4a945f8b8277d05c11bf3673d2e4ab15f9a
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2020
ms.locfileid: "94626785"
---
# <a name="how-to-create-an-azure-premium-file-share"></a>Como criar um compartilhamento de arquivos premium do Azure

Os compartilhamentos de arquivos Premium são oferecidos na mídia de armazenamento de SSD (disco de estado sólido) e são úteis para cargas de trabalho com uso intensivo de e/s, incluindo bancos de dados de hospedagem e HPC (computação de alto desempenho). Os compartilhamentos de arquivos Premium são hospedados em um tipo de conta de armazenamento de finalidade especial, chamado de conta de armazenamento de arquivo. Os compartilhamentos de arquivos Premium são projetados para aplicativos de alto desempenho e escala empresarial, fornecendo compartilhamentos de baixa latência, IOPS e alta taxa de transferência consistentes.

Este artigo mostra como criar esse novo tipo de conta usando o [portal do Azure](https://portal.azure.com/), o módulo Azure PowerShell e o CLI do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
- Se pretende usar a CLI do Azure, [instale a versão mais recente](/cli/azure/install-azure-cli?view=azure-cli-latest).
- Se você pretende usar o módulo Azure PowerShell, [Instale a versão mais recente](/powershell/azure/install-az-ps?view=azps-4.6.0).

## <a name="create-a-filestorage-storage-account"></a>Criar uma conta de armazenamento do FileStorage

Cada conta de armazenamento deve pertencer a um grupo de recursos do Azure. Um grupo de recursos é um contêiner lógico para agrupar seus serviços do Azure. Quando você cria uma conta de armazenamento, tem a opção de criar um novo grupo de recursos ou usar um grupo de recursos existente. Os compartilhamentos de arquivos Premium exigem uma conta de armazenamento de arquivo.

# <a name="portal"></a>[Portal](#tab/azure-portal)

### <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no [portal do Azure](https://portal.azure.com/).

Agora você está pronto para criar sua conta de armazenamento.

1. No portal do Azure, selecione **contas de armazenamento** no menu à esquerda.

    ![portal do Azure página principal selecionar conta de armazenamento](media/storage-how-to-create-premium-fileshare/azure-portal-storage-accounts.png)

1. Na janela **Contas de Armazenamento** que aparece, escolha **Adicionar**.
1. Selecione a assinatura na qual você deseja criar a conta de armazenamento.
1. No campo **Grupo de recursos** , selecione **Criar novo**. Insira um nome para seu novo grupo de recursos, conforme mostrado na imagem a seguir.

1. Em seguida, insira um nome para sua conta de armazenamento. O nome escolhido deve ser exclusivo no Azure. O nome também deve ter entre 3 e 24 caracteres e pode incluir apenas números e letras minúsculas.
1. Selecione um local para sua conta de armazenamento ou use o local padrão.
1. Para **desempenho** , selecione **Premium**.

    Você deve selecionar **Premium** para **armazenamento** de filepara ser uma opção disponível na lista suspensa **tipo de conta** .

1. Selecione **tipo de conta** e escolha **FileStorage**.
1. Deixe a **replicação** definida com seu valor padrão de **armazenamento com REDUNDÂNCIA local (LRS)**.

    ![Como criar uma conta de armazenamento para um compartilhamento de arquivos Premium](media/storage-how-to-create-premium-fileshare/create-filestorage-account.png)

1. Selecione **Revisar + Criar** para examinar as configurações da conta de armazenamento e criar a conta.
1. Selecione **Criar**.

Depois que o recurso da conta de armazenamento tiver sido criado, navegue até ele.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

### <a name="sign-in-to-azure"></a>Entrar no Azure

Use o comando `Connect-AzAccount` e siga as instruções na tela para fazer a autenticação.

```powershell
Connect-AzAccount
```

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Para criar um novo grupo de recursos com o PowerShell, use o comando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup):

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hardcoding it repeatedly
$resourceGroup = "storage-how-to-resource-group"
$location = "westus2"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

### <a name="create-a-filestorage-storage-account"></a>Criar uma conta de armazenamento do FileStorage

Para criar uma conta de armazenamento do FileStorage do PowerShell, use o comando [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount) :

```powershell
$storageAcct = New-AzStorageAccount -ResourceGroupName $resourceGroup -Name "fileshowto" -SkuName "Premium_LRS" -Location "westus2" -Kind "FileStorage"
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para iniciar o Azure Cloud Shell, entre no [portal do Azure](https://portal.azure.com).

Se você quiser fazer logon em sua instalação local da CLI, verifique se você tem a versão mais recente e, em seguida, entre:

```azurecli
az login
```

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Para criar um novo grupo de recursos com a CLI do Azure, use o comando [az group create](/cli/azure/group).

```azurecli-interactive
az group create `
    --name files-howto-resource-group `
    --location westus2
```

### <a name="create-a-filestorage-storage-account"></a>Criar uma conta de armazenamento do FileStorage

Para criar uma conta de armazenamento do FileStorage a partir da CLI do Azure, use o comando [AZ Storage Account Create](/cli/azure/storage/account) .

```azurecli-interactive
az storage account create `
    --name fileshowto `
    --resource-group files-howto-resource-group `
    --location westus `
    --sku Premium_LRS `
    --kind FileStorage
```

### <a name="get-the-storage-account-key"></a>Obter a chave da conta de armazenamento

As chaves da conta de armazenamento controlam o acesso a recursos em uma conta de armazenamento, neste artigo, usamos a chave para criar um compartilhamento de arquivos premium. As chaves são criadas automaticamente quando você cria uma conta de armazenamento. Você pode obter as chaves da conta de armazenamento usando o comando [az storage account keys list](/cli/azure/storage/account/keys):

```azurecli-interactive
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```
---

## <a name="create-a-premium-file-share"></a>Criar um compartilhamento de arquivo premium

Agora que você criou uma conta de armazenamento de arquivo, é possível criar um compartilhamento de arquivos Premium dentro dessa conta de armazenamento.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. No menu à esquerda da conta de armazenamento, role até a seção **serviço de arquivo** e selecione **arquivos**.
1. Selecione **compartilhamento de arquivos** para criar um compartilhamento de arquivos premium.
1. Insira um nome e uma cota desejada para o compartilhamento de arquivos e, em seguida, selecione **criar**.

> [!NOTE]
> Os tamanhos de compartilhamento provisionados são especificados pela cota de compartilhamento, os compartilhamentos de arquivos são cobrados no tamanho provisionado. Para saber mais, confira a [página de preço](https://azure.microsoft.com/pricing/details/storage/files/).

   ![Criar um compartilhamento de arquivo premium](media/storage-how-to-create-premium-fileshare/create-premium-file-share.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para criar um compartilhamento de arquivos Premium com o módulo Azure PowerShell, use o cmdlet [New-AzStorageShare](/powershell/module/az.storage/New-AzStorageShare) .

> [!NOTE]
> Os tamanhos de compartilhamento provisionados são especificados pela cota de compartilhamento, os compartilhamentos de arquivos são cobrados no tamanho provisionado. Para saber mais, confira a [página de preço](https://azure.microsoft.com/pricing/details/storage/files/).

```powershell
New-AzStorageShare `
   -Name myshare `
   -EnabledProtocol SMB `
   -Context $storageAcct.Context
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para criar um compartilhamento de arquivos Premium com o CLI do Azure, use o comando [AZ Storage share Create](/cli/azure/storage/share) .

> [!NOTE]
> Os tamanhos de compartilhamento provisionados são especificados pela cota de compartilhamento, os compartilhamentos de arquivos são cobrados no tamanho provisionado. Para saber mais, confira a [página de preço](https://azure.microsoft.com/pricing/details/storage/files/).

```azurecli-interactive
az storage share create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --enabled-protocol SMB \
    --name "myshare" 
```
---

## <a name="clean-up-resources"></a>Limpar os recursos

# <a name="portal"></a>[Portal](#tab/azure-portal)

Se você quiser limpar os recursos criados neste artigo, exclua o grupo de recursos. Excluir o grupo de recursos também exclui a conta de armazenamento associada, bem como quaisquer outros recursos associados ao grupo de recursos.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Se você quiser limpar os recursos criados neste artigo, exclua o grupo de recursos. Excluir o grupo de recursos também exclui a conta de armazenamento associada, bem como quaisquer outros recursos associados ao grupo de recursos.

Para remover o grupo de recursos e seus recursos associados, incluindo a nova conta de armazenamento, use o comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup): 

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Se você quiser limpar os recursos criados neste artigo, exclua o grupo de recursos. Excluir o grupo de recursos também exclui a conta de armazenamento associada, bem como quaisquer outros recursos associados ao grupo de recursos.

Para remover o grupo de recursos e seus recursos associados, incluindo a nova conta de armazenamento, use o comando [az group delete](/cli/azure/group).

```azurecli-interactive
az group delete --name myResourceGroup
```
---

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você criou um compartilhamento de arquivos premium. Para saber mais sobre o desempenho oferecido por essa conta, continue na seção nível de desempenho do guia de planejamento.

> [!div class="nextstepaction"]
> [Camadas de compartilhamento de arquivos](storage-files-planning.md#storage-tiers)