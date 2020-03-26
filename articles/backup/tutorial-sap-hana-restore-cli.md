---
title: 'Tutorial: Restauração do BD SAP HANA no Azure usando a CLI'
description: Neste tutorial, saiba como restaurar bancos de dados SAP HANA executados em uma VM do Azure por meio de um cofre dos Serviços de Recuperação do Backup do Azure usando a CLI do Azure.
ms.topic: tutorial
ms.date: 12/4/2019
ms.openlocfilehash: 6dbe0c4382b648506d853feb281c70a8e8401595
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "75470401"
---
# <a name="tutorial-restore-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>Tutorial: Restaurar bancos de dados SAP HANA em uma VM do Azure usando a CLI do Azure

A CLI do Azure é usada para criar e gerenciar recursos do Azure na linha de comando ou por meio de scripts. Esta documentação fornece detalhes sobre como restaurar um banco de dados SAP HANA copiado em backup em uma VM do Azure usando a CLI do Azure. Execute também estas etapas usando o [portal do Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-restore).

Use o [Azure Cloud Shell](tutorial-sap-hana-backup-cli.md) para executar comandos da CLI.

Ao final deste tutorial, você poderá:

> [!div class="checklist"]
>
> * Exibir pontos de restauração de um banco de dados copiado em backup
> * Restaurar um banco de dados

Este tutorial pressupõe que você tenha um banco de dados SAP HANA em execução em uma VM do Azure que foi copiado em backup com o Backup do Azure. Se você usou [Fazer backup de um banco de dados SAP HANA no Azure usando a CLI](tutorial-sap-hana-backup-cli.md) para fazer backup do banco de dados SAP HANA, você está usando os seguintes recursos:

* um grupo de recursos chamado *saphanaResourceGroup*
* um cofre chamado *saphanaVault*
* um contêiner protegido chamado *VMAppContainer;Compute;saphanaResourceGroup;saphanaVM*
* um banco de dados/um item submetido a backup chamado *saphanadatabase;hxe;hxe*
* recursos na região *westus2*

## <a name="view-restore-points-for-a-backed-up-database"></a>Exibir pontos de restauração de um banco de dados copiado em backup

Para ver a lista de todos os pontos de recuperação de um banco de dados, use o cmdlet [az backup recoverypoint list](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-show-log-chain) da seguinte maneira:

```azurecli-interactive
az backup recoverypoint list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
   --output table
```

A lista de pontos de recuperação terá a seguinte aparência:

```output
Name                      Time                               BackupManagementType   Item Name               RecoveryPointType
-------------------       ---------------------------------  ---------------------  ----------------------  ------------------
7660777527047692711       2019-12-10T04:00:32.346000+00:00   AzureWorkload          SAPHanaDtabase;hxe;hxe  Full
7896624824685666836       2019-12-15T10:33:32.346000+00:00   AzureWorkload          SAPHanaDtabase;hxe;hxe  Differential
DefaultRangeRecoveryPoint                                    AzureWorkload          SAPHanaDtabase;hxe;hxe  Log
```

Como você pode ver, a lista acima contém três pontos de recuperação: um para o backup completo, um para o diferencial e um para o de log.

>[!NOTE]
>Veja também os pontos inicial e final de cada cadeia de backup de log ininterrupta usando o cmdlet [az backup recoverypoint show-log-chain](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-show-log-chain).

## <a name="prerequisites-to-restore-a-database"></a>Pré-requisitos para restaurar um banco de dados

Garanta que os seguintes pré-requisitos sejam atendidos antes de restaurar um banco de dados:

* Você pode restaurar o banco de dados somente para uma instância do SAP HANA que esteja na mesma região
* A instância de destino precisa ser registrada no mesmo cofre da origem
* O Backup do Azure não pode identificar duas instâncias diferentes do SAP HANA na mesma VM. Portanto, a restauração de dados de uma instância para outra na mesma VM não é possível.

## <a name="restore-a-database"></a>Restaurar um banco de dados

O Backup do Azure pode restaurar bancos de dados SAP HANA que estão em execução em VMs do Azure da seguinte maneira:

* Restauração em uma data ou uma hora específica (com precisão de segundos) usando backups de log. O Backup do Azure determina automaticamente os backups diferenciais completos apropriados e a cadeia de backups de log necessários para a restauração com base na hora selecionada.
* Restauração em um backup completo ou diferencial específico para restauração em um ponto de recuperação específico.

Para restaurar um banco de dados, use o cmdlet [az restore restore-azurewl](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurewl), que exige um objeto de configuração de recuperação como uma das entradas. Esse objeto pode ser gerado com o cmdlet [az backup recoveryconfig show](https://docs.microsoft.com/cli/azure/backup/recoveryconfig?view=azure-cli-latest#az-backup-recoveryconfig-show). O objeto de configuração de recuperação contém todos os detalhes para executar uma restauração. Um deles é o modo de restauração: **OriginalWorkloadRestore** ou **AlternateWorkloadRestore**.

>[!NOTE]
> **OriginalWorkloadRestore**: restaure os dados para a mesma instância do SAP HANA da fonte original. Essa opção substitui o banco de dados original. <br>
> **AlternateWorkloadRestore**: restaure o banco de dados em uma localização alternativa e mantenha o banco de dados de origem.

## <a name="restore-to-alternate-location"></a>Restauração em uma localização alternativa

Para restaurar um banco de dados em uma localização alternativa, use **AlternateWorkloadRestore** como o modo de restauração. Em seguida, você precisará escolher o ponto de restauração, que pode ser um ponto anterior no tempo ou um dos pontos de restauração anteriores.

Neste tutorial, você fará a restauração em um ponto de restauração anterior. [Veja a lista de pontos de restauração](#view-restore-points-for-a-backed-up-database) do banco de dados e escolha o ponto no qual deseja fazer a restauração. Este tutorial usará o ponto de restauração com o nome *7660777527047692711*.

Usando o nome do ponto de restauração acima e o modo de restauração, vamos criar o objeto de configuração de recuperação usando o cmdlet [az backup recoveryconfig show](https://docs.microsoft.com/cli/azure/backup/recoveryconfig?view=azure-cli-latest#az-backup-recoveryconfig-show). Vejamos o que significa cada um dos parâmetros restantes nesse cmdlet:

* **--target-item-name** Esse é o nome que será usado pelo banco de dados restaurado. Nesse caso, usamos o nome *restored_database*.
* **--target-server-name** Esse é o nome de um servidor do SAP HANA que foi registrado com êxito em um cofre dos Serviços de Recuperação e reside na mesma região do banco de dados a ser restaurado. Neste tutorial, restauraremos o banco de dados no mesmo servidor do SAP HANA que protegemos, chamado *hxehost*.
* **--target-server-type** Para a restauração de bancos de dados SAP HANA, **SapHanaDatabase** precisa ser usado.

```azurecli-interactive

az backup recoveryconfig show --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --restore-mode AlternateWorkloadRestore \
    --rp-name 7660777527047692711 \
    --target-item-name restored_database \
    --target-server-name hxehost \
    --target-server-type HANAInstance \
    --workload-type SAPHANA \
    --output json
```

A resposta à consulta acima será um objeto de configuração de recuperação semelhante a este:

```output
"{\"restore_mode\": \"OriginalLocation\", \"container_uri\": \" VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \", \"item_uri\": \"SAPHanaDatabase;hxe;hxe\", \"recovery_point_id\": \"DefaultRangeRecoveryPoint\", \"log_point_in_time\": \"28-11-2019-09:53:00\", \"item_type\": \"SAPHana\", \"source_resource_id\": \"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.Compute/virtualMachines/saphanavm\", \"database_name\": null, \"container_id\": null, \"alternate_directory_paths\": null}"
```

Agora, para restaurar o banco de dados, execute o cmdlet [az restore restore-azurewl](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurewl). Para usar esse comando, entraremos na saída JSON acima que é salva em um arquivo chamado *recoveryconfig.json*.

```azurecli-interactive
az backup restore restore-azurewl --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --restore-config recoveryconfig.json \
    --output table
```

A saída se parecerá com esta:

```output
Name                                  Resource
------------------------------------  -------
5b198508-9712-43df-844b-977e5dfc30ea  SAPHANA
```

A resposta fornecerá o nome do trabalho. Esse nome de trabalho pode ser usado para acompanhar o status do trabalho usando o cmdlet [az backup job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show).

## <a name="restore-and-overwrite"></a>Restaurar e substituir

Para fazer a restauração na localização original, usaremos **OriginalWorkloadRestore** como o modo de restauração. Em seguida, você precisará escolher o ponto de restauração, que pode ser um ponto anterior no tempo ou um dos pontos de restauração anteriores.

Neste tutorial, escolheremos o ponto no tempo anterior “28-11-2019-09:53:00” para o qual será feita a restauração. Você pode fornecer esse ponto de restauração nos seguintes formatos: dd-mm-aaaa, dd-mm-aaaa-hh:mm:ss. Para escolher um ponto no tempo válido para a restauração, use o cmdlet [az backup recoverypoint show-log-chain](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-show-log-chain), que lista os intervalos de backups da cadeia de logs ininterrupta.

```azurecli-interactive
az backup recoveryconfig show --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --restore-mode OriginalWorkloadRestore \
    --log-point-in-time 28-11-2019-09:53:00 \
    --output json
```

A resposta à consulta acima será um objeto de configuração de recuperação semelhante ao seguinte:

```output
"{\"restore_mode\": \"OriginalLocation\", \"container_uri\": \" VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \", \"item_uri\": \"SAPHanaDatabase;hxe;hxe\", \"recovery_point_id\": \"DefaultRangeRecoveryPoint\", \"log_point_in_time\": \"28-11-2019-09:53:00\", \"item_type\": \"SAPHana\", \"source_resource_id\": \"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.Compute/virtualMachines/saphanavm\", \"database_name\": null, \"container_id\": null, \"alternate_directory_paths\": null}"
```

Agora, para restaurar o banco de dados, execute o cmdlet [az restore restore-azurewl](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurewl). Para usar esse comando, entraremos na saída JSON acima que é salva em um arquivo chamado *recoveryconfig.json*.

```azurecli-interactive
az backup restore restore-azurewl --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --restore-config recoveryconfig.json \
    --output table
```

A saída se parecerá com esta:

```output
Name                                  Resource
------------------------------------  --------
5b198508-9712-43df-844b-977e5dfc30ea  SAPHANA
```

A resposta fornecerá o nome do trabalho. Esse nome de trabalho pode ser usado para acompanhar o status do trabalho usando o cmdlet [az backup job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show).

## <a name="next-steps"></a>Próximas etapas

* Para saber como gerenciar bancos de dados SAP HANA que são copiados em backup usando a CLI do Azure, prossiga para o tutorial [Gerenciar um banco de dados SAP HANA em uma VM do Azure usando a CLI](tutorial-sap-hana-backup-cli.md)

* Para saber como restaurar um banco de dados SAP HANA em execução na VM do Azure usando o portal do Azure, veja [Restaurar um banco de dados SAP HANA em VMs do Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
