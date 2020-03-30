---
title: Restaurar arquivos Azure com PowerShell
description: Neste artigo, saiba como restaurar arquivos Do Azure usando o serviço de backup do Azure e o PowerShell.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: 99aeaa6173bb5336e6e1719a9fc0df0c668374e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77086834"
---
# <a name="restore-azure-files-with-powershell"></a>Restaurar arquivos Azure com PowerShell

Este artigo explica como restaurar um compartilhamento inteiro de arquivos, ou arquivos específicos, a partir de um ponto de restauração criado pelo serviço de backup do [Azure](backup-overview.md) usando o Azure Powershell.

Você pode restaurar um compartilhamento inteiro de arquivos ou arquivos específicos sobre o compartilhamento. Você pode restaurar para o local original, ou para um local alternativo.

> [!WARNING]
> Certifique-se de que a versão ps seja atualizada para a versão mínima de 'Az.RecoveryServices 2.6.0' para backups AFS. Para obter mais detalhes, consulte [a seção](backup-azure-afs-automation.md#important-notice---backup-item-identification-for-afs-backups) que descreve o requisito para esta alteração.

## <a name="fetch-recovery-points"></a>Buscar pontos de recuperação

Use [Get-AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint?view=azps-1.4.0) para listar todos os pontos de recuperação para o item de backup.

No seguinte script:

* A variável **$rp** é uma matriz de pontos de recuperação para o item de backup selecionado dos últimos sete dias.
* A matriz é classificada em ordem inversa de tempo com o último ponto de recuperação no índice **0**.
* Use a indexação de matriz padrão do PowerShell para selecionar o ponto de recuperação.
* No exemplo, **$rp[0]** seleciona o ponto de recuperação mais recente.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $afsBkpItem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()

$rp[0] | fl
```

A saída é semelhante à seguinte.

```powershell
FileShareSnapshotUri : https://testStorageAcct.file.core.windows.net/testAzureFS?sharesnapshot=2018-11-20T00:31:04.00000
                       00Z
RecoveryPointType    : FileSystemConsistent
RecoveryPointTime    : 11/20/2018 12:31:05 AM
RecoveryPointId      : 86593702401459
ItemName             : testAzureFS
Id                   : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Micros                      oft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;teststorageRG;testStorageAcct/protectedItems/AzureFileShare;testAzureFS/recoveryPoints/86593702401462
WorkloadType         : AzureFiles
ContainerName        : storage;teststorageRG;testStorageAcct
ContainerType        : AzureStorage
BackupManagementType : AzureStorage
```

Depois que o ponto de recuperação relevante for selecionado, você restaurará o compartilhamento de arquivos ou o arquivo para o local original ou para um local alternativo.

## <a name="restore-an-azure-file-share-to-an-alternate-location"></a>Restaurar um compartilhamento de arquivos Azure para um local alternativo

Use o [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0) para restaurar o ponto de recuperação selecionado. Especifique esses parâmetros para identificar o local alternativo:

* **TargetStorageAccountName**: A conta de armazenamento para a qual o conteúdo de backup é restaurado. A conta de armazenamento de destino deve estar no mesmo local que o cofre.
* **TargetFileShareName**: O arquivo compartilha dentro da conta de armazenamento de destino para a qual o conteúdo de backup é restaurado.
* **TargetFolder**: A pasta o compartilhamento de arquivos para o qual os dados são restaurados. Se for para restaurar o conteúdo do backup em uma pasta raiz, forneça os valores da pasta de destino como uma cadeia de caracteres vazia.
* **ResolverConflito**: Instrução se houver um conflito com os dados restaurados. Aceita **Overwrite** ou **Skip**.

Execute o cmdlet com os parâmetros a seguir:

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -ResolveConflict Overwrite
```

O comando retorna um trabalho com uma ID a ser rastreada, como no exemplo a seguir.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS        Restore              InProgress           12/10/2018 9:56:38 AM                               9fd34525-6c46-496e-980a-3740ccb2ad75
```

## <a name="restore-an-azure-file-to-an-alternate-location"></a>Restaurar um arquivo Azure para um local alternativo

Use o [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0) para restaurar o ponto de recuperação selecionado. Especifique esses parâmetros para identificar o local alternativo e para identificar exclusivamente o arquivo que deseja restaurar.

* **TargetStorageAccountName**: A conta de armazenamento para a qual o conteúdo de backup é restaurado. A conta de armazenamento de destino deve estar no mesmo local que o cofre.
* **TargetFileShareName**: O arquivo compartilha dentro da conta de armazenamento de destino para a qual o conteúdo de backup é restaurado.
* **TargetFolder**: A pasta o compartilhamento de arquivos para o qual os dados são restaurados. Se for para restaurar o conteúdo do backup em uma pasta raiz, forneça os valores da pasta de destino como uma cadeia de caracteres vazia.
* **SourceFilePath**: O caminho absoluto do arquivo, a ser restaurado dentro do compartilhamento de arquivos, como uma seqüência. Esse caminho é o mesmo que foi usado no cmdlet **Get-AzStorageFile** do PowerShell.
* **SourceFileType**: Se um diretório ou um arquivo está selecionado. Aceita **diretório** ou **arquivo**.
* **ResolverConflito**: Instrução se houver um conflito com os dados restaurados. Aceita **Overwrite** ou **Skip**.

Os parâmetros adicionais (SourceFilePath e SourceFileType) estão relacionados apenas ao arquivo individual que você deseja restaurar.

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

Este comando retorna um trabalho com um ID a ser rastreado, como mostrado na seção anterior.

## <a name="restore-azure-file-shares-and-files-to-the-original-location"></a>Restaurar compartilhamentos de arquivos do Azure e arquivos para o local original

Quando você restaura um local original, você não precisa especificar parâmetros relacionados ao destino e ao destino. Somente **ResolveConflict** deve ser fornecido.

#### <a name="overwrite-an-azure-file-share"></a>Substituir um compartilhamento de arquivos do Azure

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -ResolveConflict Overwrite
```

#### <a name="overwrite-an-azure-file"></a>Substituir um arquivo do Azure

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

## <a name="next-steps"></a>Próximas etapas

[Saiba como](restore-afs.md) restaurar arquivos do Azure no portal Azure.
