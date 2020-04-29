---
title: Criar um compartilhamento de arquivos premium do Azure
description: Neste artigo, você aprenderá a criar um compartilhamento de arquivos premium do Azure.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 05/05/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 7680a28b165dc252159cf95311439508d3c867e1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79529100"
---
# <a name="how-to-create-an-premium-azure-file-share"></a>Como criar um compartilhamento de arquivos premium do Azure
Os compartilhamentos de arquivos Premium são oferecidos na mídia de armazenamento de SSD (disco de estado sólido) e são úteis para cargas de trabalho com uso intensivo de e/s, incluindo bancos de dados de hospedagem e HPC (computação de alto desempenho). Os compartilhamentos de arquivos Premium são hospedados em um tipo de conta de armazenamento de finalidade especial, chamado de conta de armazenamento de arquivo. Os compartilhamentos de arquivos Premium são projetados para aplicativos de alto desempenho e escala empresarial, fornecendo compartilhamentos de baixa latência, IOPS e alta taxa de transferência consistentes.

Este artigo mostra como criar esse novo tipo de conta usando [portal do Azure](https://portal.azure.com/), Azure PowerShell e CLI do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para acessar os recursos do Azure, incluindo compartilhamentos de arquivos premium do Azure, você precisará de uma assinatura do Azure. Se você ainda não tiver uma assinatura, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-a-premium-file-share-using-the-azure-portal"></a>Criar um compartilhamento de arquivos Premium usando o portal do Azure

### <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no [portal do Azure](https://portal.azure.com/).

### <a name="create-a-filestorage-storage-account"></a>Criar uma conta de armazenamento do FileStorage

Agora você está pronto para criar sua conta de armazenamento.

Cada conta de armazenamento deve pertencer a um grupo de recursos do Azure. Um grupo de recursos é um contêiner lógico para agrupar seus serviços do Azure. Quando você cria uma conta de armazenamento, tem a opção de criar um novo grupo de recursos ou usar um grupo de recursos existente. Este artigo mostra como criar um novo grupo de recursos.

1. No portal do Azure, selecione **contas de armazenamento** no menu à esquerda.

    ![portal do Azure página principal selecionar conta de armazenamento](media/storage-how-to-create-premium-fileshare/azure-portal-storage-accounts.png)

1. Na janela **Contas de Armazenamento** que aparece, escolha **Adicionar**.
1. Selecione a assinatura na qual você deseja criar a conta de armazenamento.
1. No campo **Grupo de recursos**, selecione **Criar novo**. Insira um nome para seu novo grupo de recursos, conforme mostrado na imagem a seguir.

1. Em seguida, insira um nome para sua conta de armazenamento. O nome escolhido deve ser exclusivo no Azure. O nome também deve ter entre 3 e 24 caracteres e pode incluir apenas números e letras minúsculas.
1. Selecione um local para sua conta de armazenamento ou use o local padrão.
1. Para **desempenho** , selecione **Premium**.
1. Selecione **tipo de conta** e escolha **FileStorage**.
1. Deixe a **replicação** definida com seu valor padrão de **armazenamento com REDUNDÂNCIA local (LRS)**.

    ![Como criar uma conta de armazenamento para um compartilhamento de arquivos Premium](media/storage-how-to-create-premium-fileshare/create-filestorage-account.png)

1. Selecione **Revisar + Criar** para examinar as configurações da conta de armazenamento e criar a conta.
1. Selecione **Criar**.

Depois que o recurso da conta de armazenamento tiver sido criado, navegue até ele.

### <a name="create-a-premium-file-share"></a>Criar um compartilhamento de arquivo premium

1. No menu à esquerda da conta de armazenamento, role até a seção **serviço de arquivo** e selecione **arquivos**.
1. Selecione **compartilhamento de arquivos** para criar um compartilhamento de arquivos premium.
1. Insira um nome e uma cota desejada para o compartilhamento de arquivos e, em seguida, selecione **criar**.

> [!NOTE]
> Os tamanhos de compartilhamento provisionados são especificados pela cota de compartilhamento, os compartilhamentos de arquivos são cobrados no tamanho provisionado, consulte a [página de preços](https://azure.microsoft.com/pricing/details/storage/files/) para obter mais detalhes.

   ![Criar um compartilhamento de arquivo premium](media/storage-how-to-create-premium-fileshare/create-premium-file-share.png)

### <a name="clean-up-resources"></a>Limpar os recursos

Se você quiser limpar os recursos criados neste artigo, bastará excluir o grupo de recursos. Excluir o grupo de recursos também exclui a conta de armazenamento associada, bem como quaisquer outros recursos associados ao grupo de recursos.

## <a name="create-a-premium-file-share-using-powershell"></a>Criar um compartilhamento de arquivos Premium usando o PowerShell

### <a name="create-an-account-using-powershell"></a>Criar uma conta usando o PowerShell

Primeiro, instale a última versão do módulo [PowerShellGet](/powershell/scripting/gallery/installing-psget) do PowerShell.

Em seguida, atualize seu módulo do PowerShell, entre na sua assinatura do Azure, crie um grupo de recursos e, em seguida, crie uma conta de armazenamento.

### <a name="upgrade-your-powershell-module"></a>Atualizar seu módulo do PowerShell

Para interagir com um compartilhamento de arquivos premium do com o PowerShell, você precisará instalar um módulo AZ. Storage versão 1.4.0 ou o módulo AZ. Storage mais recente.

Comece abrindo uma sessão do PowerShell com permissões elevadas.

Instale o módulo AZ. Storage:

```powershell
Install-Module Az.Storage -Repository PSGallery -AllowClobber -Force
```

### <a name="sign-in-to-your-azure-subscription"></a>Entre em sua Assinatura do Azure

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

### <a name="create-a-premium-file-share"></a>Criar um compartilhamento de arquivo premium

Agora que você tem uma conta de armazenamento de arquivo, é possível criar um compartilhamento de arquivos premium. Use o cmdlet [New-AzStorageShare](/powershell/module/az.storage/New-AzStorageShare) para criar um.

> [!NOTE]
> Os tamanhos de compartilhamento provisionados são especificados pela cota de compartilhamento, os compartilhamentos de arquivos são cobrados no tamanho provisionado, consulte a [página de preços](https://azure.microsoft.com/pricing/details/storage/files/) para obter mais detalhes.

```powershell
New-AzStorageShare `
   -Name myshare `
   -Context $storageAcct.Context
```

### <a name="clean-up-resources"></a>Limpar os recursos

Para remover o grupo de recursos e seus recursos associados, incluindo a nova conta de armazenamento, use o comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup): 

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="create-a-premium-file-share-using-azure-cli"></a>Criar um compartilhamento de arquivos Premium usando CLI do Azure

Para iniciar o Azure Cloud Shell, entre no [portal do Azure](https://portal.azure.com).

Se você quiser fazer logon em sua instalação local da CLI, primeiro verifique se você tem a versão mais recente e execute o comando de logon:

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

### <a name="create-a-premium-file-share"></a>Criar um compartilhamento de arquivo premium

Agora que você tem uma conta de armazenamento de arquivo, é possível criar um compartilhamento de arquivos premium. Use o comando [AZ Storage share Create](/cli/azure/storage/share) para criar um.

> [!NOTE]
> Os tamanhos de compartilhamento provisionados são especificados pela cota de compartilhamento, os compartilhamentos de arquivos são cobrados no tamanho provisionado, consulte a [página de preços](https://azure.microsoft.com/pricing/details/storage/files/) para obter mais detalhes.

```azurecli-interactive
az storage share create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" 
```

### <a name="clean-up-resources"></a>Limpar os recursos

Para remover o grupo de recursos e seus recursos associados, incluindo a nova conta de armazenamento, use o comando [az group delete](/cli/azure/group).

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você criou um compartilhamento de arquivos premium. Para saber mais sobre o desempenho oferecido por essa conta, continue na seção nível de desempenho do guia de planejamento.

> [!div class="nextstepaction"]
> [Camadas de compartilhamento de arquivos](storage-files-planning.md#storage-tiers)