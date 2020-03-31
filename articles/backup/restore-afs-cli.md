---
title: Restaurar as partes de arquivos do Azure com o Azure CLI
description: Saiba como usar o Azure CLI para restaurar as ações de arquivos do Azure de backup no cofre dos Serviços de Recuperação
ms.topic: conceptual
ms.date: 01/16/2020
ms.openlocfilehash: 63b2be2fe24c1274ed1581b7b849de578c978842
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76931043"
---
# <a name="restore-azure-file-shares-with-the-azure-cli"></a>Restaurar as partes de arquivos do Azure com o Azure CLI

O Azure CLI oferece uma experiência de linha de comando para gerenciar os recursos do Azure. É uma ótima ferramenta para construir automação personalizada para usar os recursos do Azure. Este artigo explica como restaurar um compartilhamento inteiro de arquivos ou arquivos específicos de um ponto de restauração criado pelo [Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview) usando o Cli do Azure. Você também pode executar essas etapas usando o [Azure PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-afs-automation) ou o [portal do Azure](backup-afs.md).

Ao final deste artigo, você aprenderá como realizar as seguintes operações com o Azure CLI:

* Exibir pontos de restauração para um compartilhamento de arquivos Azure com backup.
* Restaurar um compartilhamento completo de arquivos Azure.
* Restaurar arquivos ou pastas individuais.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Para instalar e usar a CLI localmente, você deve executar a CLI do Azure versão 2.0.18 ou posterior. Para encontrar a versão da CLI, execute `az --version`. Caso precise instalar ou atualizar, confira [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você já tem um compartilhamento de arquivos Azure que é apoiado pelo Azure Backup. Se você não tiver um, consulte Fazer backup das [ações do arquivo Azure com a CLI](backup-afs-cli.md) para configurar o backup para o compartilhamento de arquivos. Para este artigo, você usa os seguintes recursos:

| Compartilhamento de arquivo  | Conta de armazenamento | Região | Detalhes                                                      |
| ----------- | --------------- | ------ | ------------------------------------------------------------ |
| *azurefiles*  | *afsaccount*      | EastUS | Fonte original com backup usando o Azure Backup                 |
| *azurefiles1* | *afaccount1*      | EastUS | Fonte de destino usada para recuperação de localização alternativa |

Você pode usar uma estrutura semelhante para seus compartilhamentos de arquivos para experimentar os diferentes tipos de restaurações explicadas neste artigo.

## <a name="fetch-recovery-points-for-the-azure-file-share"></a>Buscar pontos de recuperação para o compartilhamento de arquivos Do Zure

Use o [cmdlet](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-list) da lista de pontos de recuperação de backup az para listar todos os pontos de recuperação para o compartilhamento de arquivos de backup.

O exemplo a seguir busca a lista de pontos de recuperação para o compartilhamento de arquivos *azurefiles* na conta de armazenamento *afsaccount.*

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --backup-management-type azurestorage --item-name “AzureFileShare;azurefiles” --workload-type azurefileshare --out table
```

Você também pode executar o cmdlet anterior usando o nome amigável para o contêiner e o item, fornecendo os seguintes dois parâmetros adicionais:

* **--tipo de gerenciamento de backup:** *azurestorage*
* **--tipo de carga de trabalho:** *azurefileshare*

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --item-name azurefiles --workload-type azurefileshare --out table
```

O conjunto de resultados é uma lista de pontos de recuperação com detalhes de tempo e consistência para cada ponto de restauração.

```output
Name                Time                        Consistency
------------------  -------------------------   --------------------
932887541532871865  2020-01-05T07:08:23+00:00   FileSystemConsistent
932885927361238054  2020-01-05T07:08:10+00:00   FileSystemConsistent
932879614553967772  2020-01-04T21:33:04+00:00   FileSystemConsistent
```

O atributo **Nome** na saída corresponde ao nome do ponto de recuperação que pode ser usado como um valor para o parâmetro **--rp-nome** em operações de recuperação.

## <a name="full-share-recovery-by-using-the-azure-cli"></a>Recuperação total de ações usando o Azure CLI

Você pode usar esta opção de restauração para restaurar o compartilhamento completo de arquivos no local original ou alternativo.

Defina os seguintes parâmetros para executar operações de restauração:

* **--nome do contêiner**: O nome da conta de armazenamento que hospeda o compartilhamento original de arquivos de backup. Para recuperar o nome ou o nome amigável do seu contêiner, use o comando [az backup container list.](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)
* **--item-nome**: O nome do compartilhamento de arquivo original de backup que você deseja usar para a operação de restauração. Para recuperar o nome ou o nome amigável do seu item de backup, use o comando [az backup item list.](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list)

### <a name="restore-a-full-share-to-the-original-location"></a>Restaurar uma parte completa do local original

Quando você restaura um local original, você não precisa especificar parâmetros relacionados ao destino. Apenas **resolver conflito** deve ser fornecido.

O exemplo a seguir usa o [cmdlet de restauração de restauração de backup az com](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefileshare) o modo de restauração definido como *originalpara* restaurar o compartilhamento de arquivos *azurefiles* no local original. Você usa o ponto de recuperação 932883129628959823, que você obteve em [Pontos de recuperação fetch para o compartilhamento de arquivos Azure](#fetch-recovery-points-for-the-azure-file-share):

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932887541532871865   --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --restore-mode originallocation --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
6a27cc23-9283-4310-9c27-dcfb81b7b4bb  azurefiles
```

O atributo **Nome** na saída corresponde ao nome do trabalho criado pelo serviço de backup para sua operação de restauração. Para acompanhar o status do trabalho, use o [az backup job](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

### <a name="restore-a-full-share-to-an-alternate-location"></a>Restaurar uma parte completa para um local alternativo

Você pode usar essa opção para restaurar um compartilhamento de arquivos em um local alternativo e manter o compartilhamento original de arquivos como está. Especifique os seguintes parâmetros para recuperação de localização alternativa:

* **--conta de armazenamento-alvo**: A conta de armazenamento para a qual o conteúdo de backup é restaurado. A conta de armazenamento de destino deve estar no mesmo local que o cofre.
* **--target-file-share**: O compartilhamento de arquivos dentro da conta de armazenamento de destino para a qual o conteúdo de backup é restaurado.
* **--target-folder**: A pasta o compartilhamento de arquivos para o qual os dados são restaurados. Se for para restaurar o conteúdo do backup em uma pasta raiz, forneça os valores da pasta de destino como uma cadeia de caracteres vazia.
* **--resolver-conflito:** Instrução se houver um conflito com os dados restaurados. Aceita **Overwrite** ou **Skip**.

O exemplo a seguir usa [az backup restore restore-azurefileshare](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefileshare) com o modo de restauração como *localização alternativa* para restaurar o compartilhamento de arquivos *azurefiles* na conta de armazenamento *afsaccount* para o compartilhamento de arquivos *azurefiles1"* na conta de armazenamento *afaccount1.*

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932883129628959823 --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
babeb61c-d73d-4b91-9830-b8bfa83c349a  azurefiles
```

O atributo **Nome** na saída corresponde ao nome do trabalho criado pelo serviço de backup para sua operação de restauração. Para acompanhar o status do trabalho, use o [az backup job](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

## <a name="item-level-recovery"></a>Recuperação em nível de item

Você pode usar esta opção de restauração para restaurar arquivos ou pastas individuais no local original ou alternativo.

Defina os seguintes parâmetros para executar operações de restauração:

* **--nome do contêiner**: O nome da conta de armazenamento que hospeda o compartilhamento original de arquivos de backup. Para recuperar o nome ou o nome amigável do seu contêiner, use o comando [az backup container list.](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)
* **--item-nome**: O nome do compartilhamento de arquivo original de backup que você deseja usar para a operação de restauração. Para recuperar o nome ou o nome amigável do seu item de backup, use o comando [az backup item list.](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list)

Especifique os seguintes parâmetros para os itens que deseja recuperar:

* **SourceFilePath**: O caminho absoluto do arquivo, a ser restaurado dentro do compartilhamento de arquivos, como uma seqüência. Este caminho é o mesmo caminho usado no [download de arquivo de armazenamento az](https://docs.microsoft.com/cli/azure/storage/file?view=azure-cli-latest#az-storage-file-download) ou no arquivo de armazenamento [az comandos](https://docs.microsoft.com/cli/azure/storage/file?view=azure-cli-latest#az-storage-file-show) CLI.
* **SourceFileType**: Escolha se um diretório ou um arquivo está selecionado. Aceita **diretório** ou **arquivo**.
* **ResolverConflito**: Instrução se houver um conflito com os dados restaurados. Aceita **Overwrite** ou **Skip**.

### <a name="restore-individual-files-or-folders-to-the-original-location"></a>Restaurar arquivos individuais ou pastas para o local original

Use o cmdlet de [restauração de restauração de backup az](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefiles) com o modo de restauração definido como *originalpara* restaurar arquivos ou pastas específicas para sua localização original.

O exemplo a seguir restaura o arquivo *RestoreTest.txt* em sua localização original: o compartilhamento de arquivos *azurefiles.*

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --restore-mode originallocation  --source-file-type file --source-file-path "Restore/RestoreTest.txt" --resolve-conflict overwrite  --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

O atributo **Nome** na saída corresponde ao nome do trabalho criado pelo serviço de backup para sua operação de restauração. Para acompanhar o status do trabalho, use o [az backup job](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

### <a name="restore-individual-files-or-folders-to-an-alternate-location"></a>Restaurar arquivos ou pastas individuais para um local alternativo

Para restaurar arquivos ou pastas específicas em um local alternativo, use o cmdlet de [restauração de restauração de backup az](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefiles) com o modo de restauração definido como *localização alternativa* e especifique os seguintes parâmetros relacionados ao destino:

* **--conta de armazenamento-alvo**: A conta de armazenamento para a qual o conteúdo de backup é restaurado. A conta de armazenamento de destino deve estar no mesmo local que o cofre.
* **--target-file-share**: O compartilhamento de arquivos dentro da conta de armazenamento de destino para a qual o conteúdo de backup é restaurado.
* **--target-folder**: A pasta o compartilhamento de arquivos para o qual os dados são restaurados. Se o conteúdo de backup for restaurado em uma pasta raiz, dê o valor da pasta de destino como uma seqüência de string vazia.

O exemplo a seguir restaura o arquivo *RestoreTest.txt* originalmente presente no compartilhamento de arquivos *azurefiles* para um local alternativo: a pasta *de restauração de dados* no compartilhamento de arquivos *azurefiles1* hospedado na conta de armazenamento *afaccount1.*

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --source-file-type file --source-file-path "Restore/RestoreTest.txt" --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

O atributo **Nome** na saída corresponde ao nome do trabalho criado pelo serviço de backup para sua operação de restauração. Para acompanhar o status do trabalho, use o [az backup job](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

## <a name="next-steps"></a>Próximas etapas

Saiba como [gerenciar backups de compartilhamento de arquivos do Azure com o Azure CLI](manage-afs-backup-cli.md).
