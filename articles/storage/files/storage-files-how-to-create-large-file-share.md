---
title: Habilitar e criar grandes compartilhamentos de arquivos - Arquivos Azure
description: Neste artigo, você aprende como habilitar e criar grandes compartilhamentos de arquivos.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: bd7726d2bbf2830d18d78b5f0b0d7202b734124d
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537671"
---
# <a name="enable-and-create-large-file-shares"></a>Habilite e crie grandes compartilhamentos de arquivos

Quando você habilita grandes compartilhamentos de arquivos em sua conta de armazenamento, seus compartilhamentos de arquivos podem aumentar até 100 TiB. Você pode habilitar esse dimensionamento em suas contas de armazenamento existentes para os compartilhamentos de arquivos existentes.

## <a name="prerequisites"></a>Pré-requisitos

- Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
- Se você pretende usar o Azure CLI, [instale a versão mais recente](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
- Se você pretende usar o Azure PowerShell, [instale a versão mais recente](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0).

## <a name="restrictions"></a>Restrições

Por enquanto, você só pode usar o LRS (Locally-redundante) ou o ZRS (Zone-Redundante Storage, armazenamento redundante de zona) em contas com grande compartilhamento de arquivos — ativadas. Você não pode usar o armazenamento redundante em geo-zona (GZRS), o armazenamento geo-redundante (GRS) ou o armazenamento geo-redundante de acesso de leitura (RA-GRS).
Habilitar grandes compartilhamentos de arquivos em uma conta é um processo irreversível. Depois de habilitá-la, você não poderá converter sua conta em GZRS, GRS ou RA-GRS.

## <a name="create-a-new-storage-account"></a>Criar uma nova conta de armazenamento

### <a name="portal"></a>Portal

1. Entre no [portal do Azure](https://portal.azure.com).
1. No portal Azure, selecione **Todos os serviços**. 
1. Na lista de recursos, insira Contas de **armazenamento**. A lista filtra com base em sua entrada, à medida que você digita. Selecione **contas de armazenamento**.
1. Na janela **Contas de armazenamento** que aparece, selecione **Adicionar**.
1. Selecione a assinatura que você usará para criar a conta de armazenamento.
1. No campo **Grupo de recursos**, selecione **Criar novo**. Digite um nome para o seu novo grupo de recursos.

    ![Captura de tela mostrando como criar um grupo de recursos no portal](media/storage-files-how-to-create-large-file-share/create-large-file-share.png)

1. Em seguida, insira um nome para sua conta de armazenamento. O nome deve ser exclusivo em todo o Azure. O nome também deve ter de 3 a 24 caracteres de comprimento, e só pode ter números e letras minúsculas.
1. Selecione um local para sua conta de armazenamento.
1. Defina a replicação para **armazenamento localmente redundante** ou **armazenamento redundante de zona.**
1. Deixe esses campos em seus valores padrão:

   |Campo  |Valor  |
   |---------|---------|
   |Modelo de implantação     |Gerenciador de Recursos         |
   |Desempenho     |Standard         |
   |Tipo de conta     |StorageV2 (uso geral v2)         |
   |Camada de acesso     |Dinâmica         |

1. Selecione **Avançado**e selecione o botão **opção Ativado** à direita de **compartilhamentos**de arquivos Grandes .
1. Selecione **Revisar + Criar** para examinar as configurações da conta de armazenamento e criar a conta.

    ![Captura de tela com o botão de opção "ativado" em uma nova conta de armazenamento no portal Azure](media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png)

1. Selecione **Criar**.

### <a name="cli"></a>CLI

Primeiro, [instale a versão mais recente do Azure CLI para](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) que você possa habilitar grandes compartilhamentos de arquivos.

Para criar uma conta de armazenamento com grandes compartilhamentos de arquivos ativados, use o seguinte comando. `<yourStorageAccountName>`Substitua `<yourResourceGroup>`, `<yourDesiredRegion>` e com suas informações.

```azurecli-interactive
## This command creates a large file share–enabled account. It will not support GZRS, GRS, or RA-GRS.
az storage account create --name <yourStorageAccountName> -g <yourResourceGroup> -l <yourDesiredRegion> --sku Standard_LRS --kind StorageV2 --enable-large-file-share
```

### <a name="powershell"></a>PowerShell

Primeiro, [instale a versão mais recente do PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0) para que você possa habilitar grandes compartilhamentos de arquivos.

Para criar uma conta de armazenamento com grandes compartilhamentos de arquivos ativados, use o seguinte comando. `<yourStorageAccountName>`Substitua `<yourResourceGroup>`, `<yourDesiredRegion>` e com suas informações.

```powershell
## This command creates a large file share–enabled account. It will not support GZRS, GRS, or RA-GRS.
New-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -Location <yourDesiredRegion> -SkuName Standard_LRS -EnableLargeFileShare;
```

## <a name="enable-large-files-shares-on-an-existing-account"></a>Habilite grandes compartilhamentos de arquivos em uma conta existente

Você também pode habilitar grandes compartilhamentos de arquivos em suas contas existentes. Se você habilitar grandes compartilhamentos de arquivos, você não poderá converter-se em GZRS, GRS ou RA-GRS. Habilitar grandes compartilhamentos de arquivos é irreversível nesta conta de armazenamento.

### <a name="portal"></a>Portal

1. Abra o [portal Dozure](https://portal.azure.com)e vá para a conta de armazenamento onde deseja habilitar grandes compartilhamentos de arquivos.
1. Abra a conta de armazenamento e selecione **Configuração**.
1. Selecione **Ativado** em **grandes compartilhamentos de arquivos**e, em seguida, **selecione Salvar**.
1. Selecione **Visão geral** e selecione **Atualizar**.

![Selecionando o botão opção Ativado em uma conta de armazenamento existente no portal Azure](media/storage-files-how-to-create-large-file-share/enable-large-file-shares-on-existing.png)

Agora você habilitou grandes compartilhamentos de arquivos em sua conta de armazenamento. Em seguida, você deve atualizar a cota de ações existente para aproveitar o aumento da capacidade e escala.

Se você receber a mensagem de erro "Grandes compartilhamentos de arquivos ainda não estão disponíveis para a conta", sua região pode estar no meio de completar sua implantação. Suporte de contato se você tiver uma necessidade urgente de grandes compartilhamentos de arquivos.

### <a name="cli"></a>CLI

Para habilitar grandes compartilhamentos de arquivos em sua conta existente, use o seguinte comando. `<yourStorageAccountName>` Substitua `<yourResourceGroup>` e com suas informações.

```azurecli-interactive
az storage account update --name <yourStorageAccountName> -g <yourResourceGroup> --enable-large-file-share
```

### <a name="powershell"></a>PowerShell

Para habilitar grandes compartilhamentos de arquivos em sua conta existente, use o seguinte comando. `<yourStorageAccountName>` Substitua `<yourResourceGroup>` e com suas informações.

```powershell
Set-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -EnableLargeFileShare
```

## <a name="create-a-large-file-share"></a>Criar um grande compartilhamento de arquivos

Depois de habilitar grandes compartilhamentos de arquivos em sua conta de armazenamento, você pode criar compartilhamentos de arquivos nessa conta com cotas mais altas. 

### <a name="portal"></a>Portal

Criar um grande compartilhamento de arquivos é quase idêntico à criação de um compartilhamento padrão de arquivos. A principal diferença é que você pode definir uma cota até 100 TiB.

1. Na sua conta de armazenamento, selecione **'Arquivo'**
1. Selecione **+ Compartilhamento de arquivos**.
1. Digite um nome para o seu compartilhamento de arquivos. Você também pode definir o tamanho da cota que você gostaria, até 100 TiB. Em seguida, selecione **Criar**. 

![A UI do portal Azure mostrando as caixas de Nome e Cota](media/storage-files-how-to-create-large-file-share/large-file-shares-create-share.png)

### <a name="cli"></a>CLI

Para criar um grande compartilhamento de arquivos, use o seguinte comando. `<yourStorageAccountName>`Substitua `<yourStorageAccountKey>`, `<yourFileShareName>` e com suas informações.

```azurecli-interactive
az storage share create --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName>
```

### <a name="powershell"></a>PowerShell

Para criar um grande compartilhamento de arquivos, use o seguinte comando. `<YourStorageAccountName>`Substitua `<YourStorageAccountKey>`, `<YourStorageAccountFileShareName>` e com suas informações.

```powershell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
New-AzStorageShare -Name $shareName -Context $ctx
```

## <a name="expand-existing-file-shares"></a>Expandir os compartilhamentos de arquivos existentes

Depois de habilitar grandes compartilhamentos de arquivos em sua conta de armazenamento, você também pode expandir as ações de arquivos existentes nessa conta para a cota mais alta. 

### <a name="portal"></a>Portal

1. Na sua conta de armazenamento, selecione **'Arquivo'**
1. Clique com o botão direito do mouse no compartilhamento de arquivos e, em seguida, selecione **Cota**.
1. Digite o novo tamanho que você deseja e, em seguida, selecione **OK**.

![A UI do portal Azure com Cota de compartilhamentos de arquivos existentes](media/storage-files-how-to-create-large-file-share/update-large-file-share-quota.png)

### <a name="cli"></a>CLI

Para definir a cota para o tamanho máximo, use o seguinte comando. `<yourStorageAccountName>`Substitua `<yourStorageAccountKey>`, `<yourFileShareName>` e com suas informações.

```azurecli-interactive
az storage share update --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName> --quota 102400
```

### <a name="powershell"></a>PowerShell

Para definir a cota para o tamanho máximo, use o seguinte comando. `<YourStorageAccountName>`Substitua `<YourStorageAccountKey>`, `<YourStorageAccountFileShareName>` e com suas informações.

```powershell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
# update quota
Set-AzStorageShareQuota -ShareName $shareName -Context $ctx -Quota 102400
```

## <a name="next-steps"></a>Próximas etapas

* [Conecte e monte um compartilhamento de arquivos no Windows](storage-how-to-use-files-windows.md)
* [Conecte e monte um compartilhamento de arquivos no Linux](storage-how-to-use-files-linux.md)
* [Conecte e monte um compartilhamento de arquivos no macOS](storage-how-to-use-files-mac.md)
