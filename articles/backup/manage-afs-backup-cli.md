---
title: Gerenciar backups de compartilhamento de arquivos do Azure com o Cli do Azure
description: Saiba como usar o Azure CLI para gerenciar e monitorar as ações de arquivos do Azure apoiadas pelo Azure Backup.
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: 44a49913abd99b285397b8b78ad9d4c0f9df52ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76934884"
---
# <a name="manage-azure-file-share-backups-with-the-azure-cli"></a>Gerenciar backups de compartilhamento de arquivos do Azure com o Cli do Azure

O Azure CLI oferece uma experiência de linha de comando para gerenciar os recursos do Azure. É uma ótima ferramenta para construir automação personalizada para usar os recursos do Azure. Este artigo explica como executar tarefas para gerenciar e monitorar os compartilhamentos de arquivos do Azure que são apoiados pelo [Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview). Você também pode executar essas etapas com o [portal Azure](https://portal.azure.com/). 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Para instalar e usar o CLI localmente, você deve executar a versão 2.0.18 do Azure CLI ou posterior. Para encontrar a versão da CLI, execute `az --version`. Caso precise instalar ou atualizar, confira [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você já tenha um compartilhamento de arquivos Azure apoiado pelo [Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview). Se você não tiver um, consulte Fazer backup das [partes de arquivo do Azure com a CLI](backup-afs-cli.md) para configurar o backup para os compartilhamentos de arquivos. Para este artigo, você usa os seguintes recursos:

* **Grupo de recursos**: *azurefiles*
* **RecoveryServicesVault**: *azurefilesvault*
* **Conta de armazenamento** *: afsaccount*
* **Compartilhamento de arquivos**: *azurefiles*

## <a name="monitor-jobs"></a>Monitorar trabalhos

Quando você aciona as operações de backup ou restauração, o serviço de backup cria um trabalho para rastreamento. Para monitorar trabalhos concluídos ou em execução, use o cmdlet da [lista de trabalho de backup az.](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list) Com a CLI, você também pode [suspender um trabalho em andamento](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-stop) ou esperar até que um trabalho [termine.](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-wait)

O exemplo a seguir exibe o status de trabalhos de backup para o cofre *azurefilesvault* Recovery Services:

```azurecli-interactive
az backup job list --resource-group azurefiles --vault-name azurefilesvault
```

```output
[
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/d477dfb6-b292-4f24-bb43-6b14e9d06ab5",
    "location": null,
    "name": "d477dfb6-b292-4f24-bb43-6b14e9d06ab5",
    "properties": {
      "actionsInfo": null,
      "activityId": "3cef43ed-0af4-43e2-b9cb-1322c496ccb4",
      "backupManagementType": "AzureStorage",
      "duration": "0:00:29.718011",
      "endTime": "2020-01-13T08:05:29.180606+00:00",
      "entityFriendlyName": "azurefiles",
      "errorDetails": null,
      "extendedInfo": null,
      "jobType": "AzureStorageJob",
      "operation": "Backup",
      "startTime": "2020-01-13T08:04:59.462595+00:00",
      "status": "Completed",
      "storageAccountName": "afsaccount",
      "storageAccountVersion": "MicrosoftStorage"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupJobs"
  },
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/1b9399bf-c23c-4caa-933a-5fc2bf884519",
    "location": null,
    "name": "1b9399bf-c23c-4caa-933a-5fc2bf884519",
    "properties": {
      "actionsInfo": null,
      "activityId": "2663449c-94f1-4735-aaf9-5bb991e7e00c",
      "backupManagementType": "AzureStorage",
      "duration": "0:00:28.145216",
      "endTime": "2020-01-13T08:05:27.519826+00:00",
      "entityFriendlyName": "azurefilesresource",
      "errorDetails": null,
      "extendedInfo": null,
      "jobType": "AzureStorageJob",
      "operation": "Backup",
      "startTime": "2020-01-13T08:04:59.374610+00:00",
      "status": "Completed",
      "storageAccountName": "afsaccount",
      "storageAccountVersion": "MicrosoftStorage"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupJobs"
  }
]
```

## <a name="modify-policy"></a>Modificar a política

Você pode modificar uma diretiva de backup para alterar a freqüência de backup ou o intervalo de retenção usando [a política de set-policy do item de backup az](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-set-policy).

Para alterar a diretiva, defina os seguintes parâmetros:

* **--nome do contêiner**: O nome da conta de armazenamento que hospeda o compartilhamento de arquivos. Para recuperar o **nome** ou **o nome amigável** do seu contêiner, use o comando [az backup container list.](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)
* **--nome**: O nome do compartilhamento de arquivos para o qual você deseja alterar a diretiva. Para recuperar o **nome** ou **o nome amigável** do seu item de backup, use o comando [az backup item list.](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list)
* **--nome da política**: O nome da política de backup que você deseja definir para o seu compartilhamento de arquivos. Você pode usar [a lista de políticas de backup az](https://docs.microsoft.com/cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-list) para exibir todas as políticas do seu cofre.

O exemplo a seguir define a diretiva de backup *schedule2* para o compartilhamento de arquivos *azurefiles* presente na conta de armazenamento *afsaccount.*

```azurecli-interactive
az backup item set-policy --policy-name schedule2 --name azurefiles --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --name "AzureFileShare;azurefiles" --backup-management-type azurestorage --out table
```

Você também pode executar o comando anterior usando os nomes amigáveis para o contêiner e o item, fornecendo os seguintes dois parâmetros adicionais:

* **--tipo de gerenciamento de backup:** *azurestorage*
* **--tipo de carga de trabalho:** *azurefileshare*

```azurecli-interactive
az backup item set-policy --policy-name schedule2 --name azurefiles --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --name azurefiles --backup-management-type azurestorage --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
fec6f004-0e35-407f-9928-10a163f123e5  azurefiles
```

O atributo **Nome** na saída corresponde ao nome do trabalho criado pelo serviço de backup para sua operação de política de alteração. Para acompanhar o status do trabalho, use o [az backup job](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

## <a name="stop-protection-on-a-file-share"></a>Interromper a proteção em um compartilhamento de arquivo

Há duas maneiras de interromper a proteção de compartilhamentos de arquivos do Azure:

* Interrompa todos os trabalhos de backup futuros e *exclua* todos os pontos de recuperação.
* Pare todos os futuros trabalhos de backup, mas *deixe* os pontos de recuperação.

Pode haver um custo associado à saída dos pontos de recuperação no armazenamento, porque os instantâneos subjacentes criados pelo Azure Backup serão mantidos. O benefício de deixar os pontos de recuperação é a opção de restaurar o compartilhamento de arquivos mais tarde, se você quiser. Para obter informações sobre o custo de deixar os pontos de recuperação, confira os [detalhes de preços](https://azure.microsoft.com/pricing/details/storage/files). Se você optar por excluir todos os pontos de recuperação, você não poderá restaurar o compartilhamento de arquivos.

Para interromper a proteção para o compartilhamento de arquivos, defina os seguintes parâmetros:

* **--nome do contêiner**: O nome da conta de armazenamento que hospeda o compartilhamento de arquivos. Para recuperar o **nome** ou **o nome amigável** do seu contêiner, use o comando [az backup container list.](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)
* **--item-nome**: O nome do compartilhamento de arquivos para o qual você deseja parar a proteção. Para recuperar o **nome** ou **o nome amigável** do seu item de backup, use o comando [az backup item list.](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list)

### <a name="stop-protection-and-retain-recovery-points"></a>Pare a proteção e retenha pontos de recuperação

Para interromper a proteção durante a retenção de dados, use o [cmdlet de proteção de backup az.](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-disable)

O exemplo a seguir interrompe a proteção para o compartilhamento de arquivos *azurefiles,* mas mantém todos os pontos de recuperação.

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name “AzureFileShare;azurefiles” --out table
```

Você também pode executar o comando anterior usando o nome amigável para o contêiner e o item, fornecendo os seguintes dois parâmetros adicionais:

* **--tipo de gerenciamento de backup:** *azurestorage*
* **--tipo de carga de trabalho:** *azurefileshare*

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
fec6f004-0e35-407f-9928-10a163f123e5  azurefiles
```

O atributo **Nome** na saída corresponde ao nome do trabalho criado pelo serviço de backup para sua operação de proteção contra paradas. Para acompanhar o status do trabalho, use o [az backup job](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

### <a name="stop-protection-without-retaining-recovery-points"></a>Parar a proteção sem reter pontos de recuperação

Para interromper a proteção sem reter pontos de recuperação, use o cmdlet [de proteção](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-disable) de backup az com a opção **de exclusão de dados de backup** definida como **true**.

O exemplo a seguir interrompe a proteção para o compartilhamento de arquivos *azurefiles* sem reter pontos de recuperação.

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name “AzureFileShare;azurefiles” --delete-backup-data true --out table
```

Você também pode executar o comando anterior usando o nome amigável para o contêiner e o item, fornecendo os seguintes dois parâmetros adicionais:

* **--tipo de gerenciamento de backup:** *azurestorage*
* **--tipo de carga de trabalho:** *azurefileshare*

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --delete-backup-data true --out table
```

## <a name="resume-protection-on-a-file-share"></a>Retomar a proteção em um compartilhamento de arquivo

Se você parou a proteção para um compartilhamento de arquivos Do Zure, mas manteve os pontos de recuperação, poderá retomar a proteção mais tarde. Se você não reter os pontos de recuperação, você não pode retomar a proteção.

Para retomar a proteção para o compartilhamento de arquivos, defina os seguintes parâmetros:

* **--nome do contêiner**: O nome da conta de armazenamento que hospeda o compartilhamento de arquivos. Para recuperar o **nome** ou **o nome amigável** do seu contêiner, use o comando [az backup container list.](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)
* **--item-nome**: O nome do compartilhamento de arquivos para o qual você deseja retomar a proteção. Para recuperar o **nome** ou **o nome amigável** do seu item de backup, use o comando [az backup item list.](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list)
* **--nome da política**: O nome da política de backup para a qual você deseja retomar a proteção para o compartilhamento de arquivos.

O exemplo a seguir usa o cmdlet de [proteção de backup az](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-resume) para retomar a proteção para o compartilhamento de arquivos *azurefiles* usando a diretiva de backup *agenda1.*

```azurecli-interactive
az backup protection resume --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --policy-name schedule2 --out table
```

Você também pode executar o comando anterior usando o nome amigável para o contêiner e o item, fornecendo os seguintes dois parâmetros adicionais:

* **--tipo de gerenciamento de backup:** *azurestorage*
* **--tipo de carga de trabalho:** *azurefileshare*

```azurecli-interactive
az backup protection resume --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --policy-name schedule2 --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
75115ab0-43b0-4065-8698-55022a234b7f  azurefiles
```

O atributo **Nome** na saída corresponde ao nome do trabalho criado pelo serviço de backup para sua operação de proteção de currículos. Para acompanhar o status do trabalho, use o [az backup job](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

## <a name="unregister-a-storage-account"></a>Cancelar o registro de uma conta de armazenamento

Se você quiser proteger seus compartilhamentos de arquivos em uma determinada conta de armazenamento usando um cofre diferente de Serviços de Recuperação, primeiro [pare a proteção para todos os compartilhamentos de arquivos](#stop-protection-on-a-file-share) nessa conta de armazenamento. Em seguida, não registre a conta do cofre de Serviços de Recuperação atualmente usado para proteção.

Você precisa fornecer um nome de contêiner para cancelar o registro da conta de armazenamento. Para recuperar o **nome** ou o **nome amigável** do seu contêiner, use o comando [az backup container list.](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)

O exemplo a seguir não registra a conta de armazenamento *afsaccount* do *azurefilesvault* usando o cmdlet de [não registro do contêiner de backup az.](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-unregister)

```azurecli-interactive
az backup container unregister --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --out table
```

Você também pode executar o cmdlet anterior usando o nome amigável para o contêiner, fornecendo o seguinte parâmetro adicional:

* **--tipo de gerenciamento de backup:** *azurestorage*

```azurecli-interactive
az backup container unregister --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --out table
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte Backup de [compartilhamentos de arquivos Azure](troubleshoot-azure-files.md).
