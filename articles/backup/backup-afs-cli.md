---
title: Faça backup das ações de arquivos do Azure com a Cli do Azure
description: Saiba como usar o Azure CLI para fazer backup das ações de arquivos do Azure no Cofre de Serviços de Recuperação
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: ff1d8c6245521d2d0262b0440177d65713058742
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844034"
---
# <a name="back-up-azure-file-shares-with-cli"></a>Faça backup das ações de arquivos do Azure com a CLI

A interface de linha de comando (CLI) do Azure oferece uma experiência de linha de comando para gerenciar os recursos do Azure. É uma ótima ferramenta para construir automação personalizada para usar os recursos do Azure. Este artigo detalha como fazer backup das partes de arquivos do Azure com o Azure CLI. Você também pode executar essas etapas usando o [Azure PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-afs-automation) ou o [portal do Azure](backup-afs.md).

Ao final deste tutorial, você aprenderá como realizar abaixo as operações com o Azure CLI:

* Criar um cofre dos Serviços de Recuperação
* Habilitar backup para compartilhamentos de arquivos Do Zure
* Acione um backup demanda para compartilhamentos de arquivos

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Para instalar e usar a CLI localmente, você deve executar a CLI do Azure versão 2.0.18 ou posterior. Para encontrar a `run az --version`versão CLI, . Caso precise instalar ou atualizar, confira [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-a-recovery-services-vault"></a>Criar um cofre de serviços de recuperação

Um cofre de serviço de recuperação é uma entidade que oferece uma visão consolidada e capacidade de gerenciamento em todos os itens de backup. Quando o trabalho de backup para um recurso protegido é executado, ele cria um ponto de recuperação no cofre dos Serviços de Recuperação. Você pode usar um desses pontos de recuperação para restaurar dados para um determinado ponto no tempo.

Siga estas etapas para criar um cofre de serviços de recuperação:

1. Um cofre é colocado em um grupo de recursos. Se você não tiver um grupo de recursos existente, crie um novo com [a criação do grupo AZ](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) . Neste tutorial, criamos o novo grupo de recursos *azurefiles* na região leste dos EUA.

    ```azurecli-interactive
    az group create --name AzureFiles --location eastus --output table
    ```

    ```output
    Location    Name
    ----------  ----------
    eastus      AzureFiles
    ```

2. Use o [cofre de backup az criar](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest#az-backup-vault-create) cmdlet para criar o cofre. Especifique o mesmo local para o cofre usado para o grupo de recursos.

    O exemplo a seguir cria um cofre de serviços de recuperação chamado *azurefilesvault* na região leste dos EUA.

    ```azurecli-interactive
    az backup vault create --resource-group azurefiles --name azurefilesvault --location eastus --output table
    ```

    ```output
    Location    Name                ResourceGroup
    ----------  ----------------    ---------------
    eastus      azurefilesvault     azurefiles
    ```

3. Especifique o tipo de redundância a ser usada para o armazenamento do cofre. Você pode usar [armazenamento com redundância local](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs) ou [armazenamento com redundância geográfica](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs).

    O exemplo a seguir define a opção de redundância de armazenamento para *o azurefilesvault* para **georedundant** usando o conjunto [de propriedades de backup do cofre de backup az.](https://docs.microsoft.com/cli/azure/backup/vault/backup-properties?view=azure-cli-latest#az-backup-vault-backup-properties-set)

    ```azurecli-interactive
    az backup vault backup-properties set --name azurefilesvault --resource-group azurefiles --backup-storage-redundancy Georedundant
    ```

    Para verificar se o cofre foi criado com sucesso, você pode usar o [az backup vault show](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest#az-backup-vault-show) cmdlet para obter detalhes do seu cofre. O exemplo a seguir exibe os detalhes do *azurefilesvault* que criamos nas etapas acima.

    ```azurecli-interactive
    az backup vault show --name azurefilesvault --resource-group azurefiles --output table
    ```

    A saída será semelhante à seguinte resposta:

    ```output
    Location     Name               ResourceGroup
    ----------   ---------------    ---------------
    eastus       azurefilesvault    azurefiles
    ```

## <a name="enable-backup-for-azure-file-shares"></a>Habilitar backup para compartilhamentos de arquivos Do Zure

Esta seção pressupõe que você já tenha um compartilhamento de arquivos Azure para o qual você deseja configurar backup. Se você não tiver um, crie um compartilhamento de arquivos Azure usando o comando [az storage share create.](https://docs.microsoft.com/cli/azure/storage/share?view=azure-cli-latest#az-storage-share-create)

Para habilitar o backup para compartilhamentos de arquivos, você precisa criar uma política de proteção que define quando um trabalho de backup é executado e por quanto tempo os pontos de recuperação são armazenados. Você pode criar uma política de backup usando a [diretiva de backup az criar](https://docs.microsoft.com/cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-create) cmdlet.

O exemplo a seguir usa o cmdlet [de proteção de backup az enable for-azurefileshare](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-enable-for-azurefileshare) para habilitar o backup para o compartilhamento de arquivos *azurefiles* na conta de armazenamento *afsaccount* usando a diretiva de backup *do cronograma 1:*

```azurecli-interactive
az backup protection enable-for-azurefileshare --vault-name azurefilesvault --resource-group  azurefiles --policy-name schedule1 --storage-account afsaccount --azure-file-share azurefiles  --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
0caa93f4-460b-4328-ac1d-8293521dd928  azurefiles
```

O atributo **Nome** na saída corresponde ao nome do trabalho criado pelo serviço de backup para sua operação **de backup habilitada.** Para acompanhar o status do trabalho, use o [az backup job](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

## <a name="trigger-an-on-demand-backup-for-file-share"></a>Acione um backup demanda para compartilhamento de arquivos

Se você quiser ativar um backup demanda para o compartilhamento de arquivos em vez de esperar que a política de backup execute o trabalho no horário programado, use o cmdlet de [proteção de backup az.](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-backup-now)

Você precisa definir os seguintes parâmetros para ativar um backup demanda:

* **--nome do contêiner** é o nome da conta de armazenamento que hospeda o compartilhamento de arquivos. Para recuperar o **nome** ou **o nome amigável** do seu contêiner, use o comando [az backup container list.](/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)
* **--item-nome** é o nome da parte de arquivo para a qual você deseja acionar um backup demanda. Para recuperar o **nome** ou **o nome amigável** do seu item de backup, use o comando [az backup item list.](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list)
* **--reter-até** especifica a data até quando você deseja manter o ponto de recuperação. O valor deve ser definido no formato de tempo UTC (dd-mm-yyyy).

O exemplo a seguir desencadeia um backup sob demanda para o compartilhamento de arquivos *azuresfiles* na conta de armazenamento *afsaccount* com retenção até *20-01-2020*.

```azurecli-interactive
az backup protection backup-now --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --retain-until 20-01-2020 --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
9f026b4f-295b-4fb8-aae0-4f058124cb12  azurefiles
```

O atributo **Nome** na saída corresponde ao nome do trabalho criado pelo serviço de backup para sua operação de backup demanda. Para acompanhar o status de um trabalho, use o [az backup job](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cdlet.

## <a name="next-steps"></a>Próximas etapas

* Saiba como [restaurar os compartilhamentos de arquivos do Azure com a CLI](restore-afs-cli.md)
* Saiba como [gerenciar ackups de compartilhamento de arquivos do Azure com a CLI](manage-afs-backup-cli.md)
