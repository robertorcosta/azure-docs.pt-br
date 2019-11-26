---
title: Habilitar e criar compartilhamentos de arquivos grandes-arquivos do Azure
description: Neste artigo, você aprenderá a habilitar e a criar grandes compartilhamentos de arquivos.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 34b8af56a8f2f108b96ca07fa73f90bb9eb5bf13
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422735"
---
# <a name="how-to-enable-and-create-large-file-shares"></a>Como habilitar e criar compartilhamentos de arquivos grandes

Originalmente, os compartilhamentos de arquivos padrão só poderiam ser escalados para até 5 TiB, agora, com grandes compartilhamentos de arquivos, eles podem ser escalados verticalmente para 100 TiB. Os compartilhamentos de arquivos Premium são escalados verticalmente até 100 TiB por padrão. 

Para escalar verticalmente até 100 TiB usando compartilhamentos de arquivos padrão, você deve habilitar sua conta de armazenamento para usar grandes compartilhamentos de arquivos. Você pode habilitar uma conta existente ou criar uma nova conta para usar compartilhamentos de arquivos grandes.

## <a name="prerequisites"></a>pré-requisitos

- Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
- Se você pretende usar CLI do Azure, certifique [-se de instalar a versão mais recente](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
- Se você pretende usar Azure PowerShell, certifique [-se de instalar a versão mais recente](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0).

## <a name="restrictions"></a>Restrições

As contas habilitadas para compartilhamentos de arquivos grandes dão suporte a LRS ou ZRS. Por enquanto, as contas habilitadas para compartilhamentos de arquivos grandes não dão suporte a GZRS, GRS ou RA-GRS. Habilitar grandes compartilhamentos de arquivos em uma conta é um processo irreversível, uma vez habilitado, sua conta não pode ser convertida em GZRS, GRS ou RA-GRS.

## <a name="create-a-new-storage-account"></a>Criar uma nova conta de armazenamento

### <a name="portal"></a>Portal

Entre no [Portal do Azure](https://portal.azure.com).

1. No portal do Azure, clique em **Todos os serviços**. Na lista de recursos, digite **Contas de armazenamento**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Selecione **Contas de Armazenamento**.
1. Na janela **Contas de Armazenamento** que aparece, escolha **Adicionar**.
1. Selecione a assinatura na qual você deseja criar a conta de armazenamento.
1. No campo **Grupo de recursos**, selecione **Criar novo**. Insira um nome para seu novo grupo de recursos, conforme mostrado na imagem a seguir.

    ![Captura de tela mostrando como criar um grupo de recursos no portal](media/storage-files-how-to-create-large-file-share/create-large-file-share.png)

1. Em seguida, insira um nome para sua conta de armazenamento. O nome escolhido deve ser exclusivo no Azure. O nome também deve ter entre 3 e 24 caracteres e pode incluir apenas números e letras minúsculas.
1. Selecione um local para sua conta de armazenamento, verifique se ele é [uma das regiões com suporte para LFS](storage-files-planning.md#regional-availability).
1. Defina a replicação para o **armazenamento** com redundância local ou **armazenamento com redundância de zona**.
1. Deixe esses campos definidos como seus valores padrão:

   |Campo  |Valor  |
   |---------|---------|
   |Modelo de implantação     |Gerenciador de Recursos         |
   |Desempenho     |Standard         |
   |Tipo de conta     |StorageV2 (uso geral v2)         |
   |Camada de acesso     |Dinâmica         |

1. Selecione **avançado** e selecione **habilitado** para **compartilhamentos de arquivos grandes**.
1. Selecione **Revisar + Criar** para examinar as configurações da conta de armazenamento e criar a conta.

    ![Large-File-shares-Advanced-Enable. png](media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png)

1. Selecione **Criar**.


### <a name="cli"></a>CLI

Primeiro, certifique-se [de instalar a versão mais recente](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest), para que você possa habilitar esse recurso.

Para criar uma conta de armazenamento com grandes compartilhamentos de arquivos habilitados, substitua `<yourStorageAccountName>`, `<yourResourceGroup>`e `<yourDesiredRegion>` pelos valores e, em seguida, use o seguinte comando:

```azurecli-interactive
## This command creates a large file share enabled account, it will not support GZRS, GRS, or RA-GRS
az storage account create –name <yourStorageAccountName> -g <yourResourceGroup> -l <yourDesiredRegion> –sku Standard_LRS --kind StorageV2 –enable-large-file-share
```

### <a name="powershell"></a>PowerShell

Primeiro, certifique-se [de instalar a versão mais recente](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0), para que você possa habilitar esse recurso.

Para criar uma conta de armazenamento com grandes compartilhamentos de arquivos habilitados, substitua `<yourStorageAccountName>`, `<yourResourceGroup>`e `<yourDesiredRegion>` pelos valores e, em seguida, use o seguinte comando:

```PowerShell
## This command creates a large file share enabled account, it will not support GZRS, GRS, or RA-GRS
New-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -Location <yourDesiredRegion> -SkuName Standard_LRS -EnableLargeFileShare;
```

## <a name="enable-on-existing-account"></a>Habilitar na conta existente

### <a name="portal"></a>Portal

Você também pode habilitar compartilhamentos de arquivos grandes em contas existentes. Se você fizer isso, a conta não será mais capaz de converter em GZRS, GRS ou RA-GRS. Essa opção é irreversível nessa conta.

1. Abra o [portal do Azure](https://portal.azure.com) e navegue até a conta de armazenamento na qual você deseja habilitar compartilhamentos de arquivos grandes.
1. Abra a conta de armazenamento e selecione **configuração**.
1. Selecione **habilitado** em **compartilhamentos de arquivos grandes**e, em seguida, selecione salvar.
1. Selecione **visão geral** e selecione **Atualizar**.

![Enable-Large-File-shares-on-existing. png](media/storage-files-how-to-create-large-file-share/enable-large-file-shares-on-existing.png)

Agora você habilitou grandes compartilhamentos de arquivos em sua conta de armazenamento.

Se você receber o seguinte erro: "grandes compartilhamentos de arquivos não estão disponíveis para a conta ainda". Você pode esperar algum tempo, pois sua região provavelmente está no meio de concluir sua distribuição ou, se você tiver necessidades urgentes, entrar em contato com o suporte.

### <a name="cli"></a>CLI

Você pode habilitar compartilhamentos de arquivos grandes em suas contas existentes. Se você fizer isso, a conta não será mais capaz de converter em GZRS, GRS ou RA-GRS. Essa opção é irreversível nessa conta.

Substitua `<yourStorageAccountName>` e `<yourResourceGroup>` no comando a seguir e, em seguida, use-o para habilitar compartilhamentos de arquivos grandes em sua conta existente:

```azurecli-interactive
az storage account update –name <yourStorageAccountName> -g <yourResourceGroup> –enable-large-file-share
```

### <a name="powershell"></a>PowerShell

Você pode habilitar compartilhamentos de arquivos grandes em suas contas existentes. Se você fizer isso, a conta não será mais capaz de converter em GZRS, GRS ou RA-GRS. Essa opção é irreversível nessa conta.

Substitua `<yourStorageAccountName>` e `<yourResourceGroup>` no comando a seguir e, em seguida, use-o para habilitar compartilhamentos de arquivos grandes em sua conta existente:

```PowerShell
Set-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -EnableLargeFileShare
```

## <a name="create-a-large-file-share"></a>Criar um compartilhamento de arquivos grande

### <a name="portal"></a>Portal

A criação de um compartilhamento de arquivos grande é quase idêntica à criação de um compartilhamento de arquivos padrão. A principal diferença é que você pode definir uma cota de até 100 TiB.

1. Em sua conta de armazenamento, selecione **compartilhamentos de arquivos**.
1. Selecione **+ Compartilhamento de Arquivo**.
1. Insira um nome para o compartilhamento de arquivos e (opcionalmente) o tamanho da cota que você deseja, até 100 TiB e, em seguida, selecione **criar**. 

![Large-File-shares-Create-share. png](media/storage-files-how-to-create-large-file-share/large-file-shares-create-share.png)

### <a name="cli"></a>CLI

Depois de habilitar grandes compartilhamentos de arquivos em sua conta de armazenamento, você poderá criar compartilhamentos de arquivos nessa conta com cotas maiores. Substitua `<yourStorageAccountName>`, `<yourStorageAccountKey>`e `<yourFileShareName>` no comando a seguir pelos seus valores e, em seguida, você poderá usá-lo para criar um compartilhamento de arquivos grande:

```azurecli-interactive
az storage share create --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName>
```

### <a name="powershell"></a>PowerShell

Depois de habilitar grandes compartilhamentos de arquivos em sua conta de armazenamento, você poderá criar compartilhamentos de arquivos nessa conta com cotas maiores. Substitua `<YourStorageAccountName>`, `<YourStorageAccountKey>`e `<YourStorageAccountFileShareName>` no comando a seguir pelos seus valores e, em seguida, você poderá usá-lo para criar um compartilhamento de arquivos grande:

```PowerShell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
New-AzStorageShare -Name $shareName -Context $ctx
```

## <a name="expand-existing-file-shares"></a>Expandir compartilhamentos de arquivos existentes

### <a name="portal"></a>Portal

Depois de habilitar grandes compartilhamentos de arquivos em sua conta de armazenamento, você poderá expandir os compartilhamentos existentes para a cota mais alta.

1. Em sua conta de armazenamento, selecione **compartilhamentos de arquivos**.
1. Clique com o botão direito do mouse no compartilhamento de arquivos e selecione **cota**.
1. Digite o novo tamanho desejado e selecione **OK**.

![Update-Large-File-Share-quota. png](media/storage-files-how-to-create-large-file-share/update-large-file-share-quota.png)

### <a name="cli"></a>CLI

Depois de habilitar grandes compartilhamentos de arquivos em sua conta de armazenamento, você poderá expandir os compartilhamentos de arquivos existentes nessa conta para as cotas mais altas. Substitua `<yourStorageAccountName>`, `<yourStorageAccountKey>`e `<yourFileShareName>` no comando a seguir pelos seus valores e, em seguida, você poderá usá-lo para definir a cota para o tamanho máximo:

```azurecli-interactive
az storage share update --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName> --quota 102400
```

### <a name="powershell"></a>PowerShell

Depois de habilitar grandes compartilhamentos de arquivos em sua conta de armazenamento, você poderá expandir os compartilhamentos de arquivos existentes nessa conta para as cotas mais altas. Substitua `<YourStorageAccountName>`, `<YourStorageAccountKey>`e `<YourStorageAccountFileShareName>` no comando a seguir pelos seus valores e, em seguida, você poderá usá-lo para definir a cota para o tamanho máximo:

```PowerShell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
# update quota
Set-AzStorageShareQuota -ShareName $shareName -Context $ctx -Quota 102400
```

## <a name="next-steps"></a>Próximas etapas

* [Conecte e Monte o Compartilhamento de Arquivos - Windows](storage-how-to-use-files-windows.md)
* [Conecte e Monte o Compartilhamento de Arquivos - Linux](../storage-how-to-use-files-linux.md)
* [Conecte e Monte o Compartilhamento de Arquivos - macOS](storage-how-to-use-files-mac.md)