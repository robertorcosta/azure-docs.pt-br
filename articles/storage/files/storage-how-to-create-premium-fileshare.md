---
title: Crie um compartilhamento de arquivos Premium Azure
description: Neste artigo, você aprende como criar um compartilhamento de arquivos Premium Azure.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 05/05/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 7680a28b165dc252159cf95311439508d3c867e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79529100"
---
# <a name="how-to-create-an-premium-azure-file-share"></a>Como criar um compartilhamento de arquivos Premium Azure
Os compartilhamentos de arquivos premium são oferecidos em mídia de armazenamento em disco de estado sólido (SSD) e são úteis para cargas de trabalho intensivas em IO, incluindo bancos de dados de hospedagem e computação de alto desempenho (HPC). Os compartilhamentos de arquivos premium são hospedados em um tipo de conta de armazenamento de propósito especial, chamada conta FileStorage. As ações de arquivos premium são projetadas para aplicações de alto desempenho e escala corporativa, fornecendo baixa latência consistente, IOPS alto e ações de alto rendimento.

Este artigo mostra como criar esse novo tipo de conta usando [o portal Azure,](https://portal.azure.com/)o Azure PowerShell e o Azure CLI.

## <a name="prerequisites"></a>Pré-requisitos

Para acessar os recursos do Azure, incluindo ações premium de arquivos Do Azure, você precisará de uma assinatura do Azure. Se você ainda não tiver uma assinatura, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-a-premium-file-share-using-the-azure-portal"></a>Crie um compartilhamento de arquivos premium usando o portal Azure

### <a name="sign-in-to-azure"></a>Entrar no Azure

Faça login no [portal Azure](https://portal.azure.com/).

### <a name="create-a-filestorage-storage-account"></a>Criar uma conta de armazenamento de armazenamento de arquivos

Agora você está pronto para criar sua conta de armazenamento.

Cada conta de armazenamento deve pertencer a um grupo de recursos do Azure. Um grupo de recursos é um contêiner lógico para agrupar seus serviços do Azure. Quando você cria uma conta de armazenamento, tem a opção de criar um novo grupo de recursos ou usar um grupo de recursos existente. Este artigo mostra como criar um novo grupo de recursos.

1. No portal Azure, selecione **Contas de armazenamento** no menu à esquerda.

    ![Página principal do portal Azure seleciona conta de armazenamento](media/storage-how-to-create-premium-fileshare/azure-portal-storage-accounts.png)

1. Na janela **Contas de Armazenamento** que aparece, escolha **Adicionar**.
1. Selecione a assinatura na qual você deseja criar a conta de armazenamento.
1. No campo **Grupo de recursos**, selecione **Criar novo**. Insira um nome para seu novo grupo de recursos, conforme mostrado na imagem a seguir.

1. Em seguida, insira um nome para sua conta de armazenamento. O nome escolhido deve ser exclusivo no Azure. O nome também deve ter entre 3 e 24 caracteres e pode incluir apenas números e letras minúsculas.
1. Selecione um local para sua conta de armazenamento ou use o local padrão.
1. Para **desempenho** selecione **Premium**.
1. Selecione **o tipo de conta** e escolha **FileStorage**.
1. Deixar **a replicação** definida para o valor padrão de **LRS (Locally-redundante storage)**.

    ![Como criar uma conta de armazenamento para um compartilhamento de arquivos premium](media/storage-how-to-create-premium-fileshare/create-filestorage-account.png)

1. Selecione **Revisar + Criar** para examinar as configurações da conta de armazenamento e criar a conta.
1. Selecione **Criar**.

Uma vez que o recurso da sua conta de armazenamento tenha sido criado, navegue até ele.

### <a name="create-a-premium-file-share"></a>Criar um compartilhamento de arquivo premium

1. No menu à esquerda para a conta de armazenamento, role até a seção **Serviço de arquivo** e selecione **Arquivos**.
1. Selecione **'Arquivo'** para criar um compartilhamento de arquivos premium.
1. Digite um nome e uma cota desejada para o compartilhamento de arquivos e selecione **Criar**.

> [!NOTE]
> Os tamanhos das ações provisionadas são especificados pela cota de ações, as ações de arquivo são cobradas sobre o tamanho provisionado, consulte a [página de preços](https://azure.microsoft.com/pricing/details/storage/files/) para obter mais detalhes.

   ![Criar um compartilhamento de arquivo premium](media/storage-how-to-create-premium-fileshare/create-premium-file-share.png)

### <a name="clean-up-resources"></a>Limpar recursos

Se você quiser limpar os recursos criados neste artigo, você pode simplesmente excluir o grupo de recursos. A exclusão do grupo de recursos também exclui a conta de armazenamento associada, bem como quaisquer outros recursos associados ao grupo de recursos.

## <a name="create-a-premium-file-share-using-powershell"></a>Crie um compartilhamento de arquivos premium usando o PowerShell

### <a name="create-an-account-using-powershell"></a>Criar uma conta usando o PowerShell

Primeiro, instale a última versão do módulo [PowerShellGet](/powershell/scripting/gallery/installing-psget) do PowerShell.

Em seguida, atualize seu módulo PowerShell, faça login na assinatura do Azure, crie um grupo de recursos e crie uma conta de armazenamento.

### <a name="upgrade-your-powershell-module"></a>Atualize seu módulo PowerShell

Para interagir com um compartilhamento de arquivos premium com o PowerShell, você precisará instalar um módulo Az.Storage versão 1.4.0 ou o módulo Az.Storage mais recente.

Comece abrindo uma sessão do PowerShell com permissões elevadas.

Instale o módulo Az.Storage:

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

### <a name="create-a-filestorage-storage-account"></a>Criar uma conta de armazenamento de armazenamento de arquivos

Para criar uma conta de armazenamento de arquivos do PowerShell, use o comando [New-AzStorageAccount:](/powershell/module/az.storage/New-azStorageAccount)

```powershell
$storageAcct = New-AzStorageAccount -ResourceGroupName $resourceGroup -Name "fileshowto" -SkuName "Premium_LRS" -Location "westus2" -Kind "FileStorage"
```

### <a name="create-a-premium-file-share"></a>Criar um compartilhamento de arquivo premium

Agora que você tem uma conta de armazenamento de arquivos, você pode criar um compartilhamento de arquivos premium. Use o [cmdlet New-AzStorageShare](/powershell/module/az.storage/New-AzStorageShare) para criar um.

> [!NOTE]
> Os tamanhos das ações provisionadas são especificados pela cota de ações, as ações de arquivo são cobradas sobre o tamanho provisionado, consulte a [página de preços](https://azure.microsoft.com/pricing/details/storage/files/) para obter mais detalhes.

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

## <a name="create-a-premium-file-share-using-azure-cli"></a>Crie um compartilhamento de arquivos premium usando o Azure CLI

Para iniciar o Azure Cloud Shell, entre no [portal do Azure](https://portal.azure.com).

Se você quiser fazer login na instalação local da CLI, primeiro certifique-se de ter a versão mais recente e execute o comando de login:

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

### <a name="create-a-filestorage-storage-account"></a>Criar uma conta de armazenamento de armazenamento de arquivos

Para criar uma conta de armazenamento de arquivos a partir do Azure CLI, use o comando [az storage create.](/cli/azure/storage/account)

```azurecli-interactive
az storage account create `
    --name fileshowto `
    --resource-group files-howto-resource-group `
    --location westus `
    --sku Premium_LRS `
    --kind FileStorage
```

### <a name="get-the-storage-account-key"></a>Obter a chave da conta de armazenamento

As chaves da conta de armazenamento controlam o acesso aos recursos em uma conta de armazenamento, neste artigo, usamos a chave para criar um compartilhamento de arquivos premium. As chaves são criadas automaticamente quando você cria uma conta de armazenamento. Você pode obter as chaves da conta de armazenamento usando o comando [az storage account keys list](/cli/azure/storage/account/keys):

```azurecli-interactive
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

### <a name="create-a-premium-file-share"></a>Criar um compartilhamento de arquivo premium

Agora que você tem uma conta de armazenamento de arquivos, você pode criar um compartilhamento de arquivo premium. Use [o comando az storage share create](/cli/azure/storage/share) para criar um.

> [!NOTE]
> Os tamanhos das ações provisionadas são especificados pela cota de ações, as ações de arquivo são cobradas sobre o tamanho provisionado, consulte a [página de preços](https://azure.microsoft.com/pricing/details/storage/files/) para obter mais detalhes.

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

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você criou um compartilhamento de arquivos premium. Para saber mais sobre o desempenho que essa conta oferece, continue até a seção de nível de desempenho do guia de planejamento.

> [!div class="nextstepaction"]
> [Níveis de compartilhamento de arquivos](storage-files-planning.md#storage-tiers)