---
title: SQL DB no backup do Azure VM & restauração via PowerShell
description: Faça backup e restaure bancos de dados SQL em VMs Azure usando o Azure Backup e o PowerShell.
ms.topic: conceptual
ms.date: 03/15/2019
ms.assetid: 57854626-91f9-4677-b6a2-5d12b6a866e1
ms.openlocfilehash: 9608b02869b1d41d901ec77a42cfaa6d882040e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131824"
---
# <a name="back-up-and-restore-sql-databases-in-azure-vms-with-powershell"></a>Fazer backup e restaurar bancos de dados SQL em VMs do Azure com o PowerShell

Este artigo descreve como usar o Azure PowerShell para fazer backup e recuperar um Db SQL dentro de uma VM Azure usando o cofre [azure Backup](backup-overview.md) Recovery Services.

Este artigo explica como:

> [!div class="checklist"]
>
> * Configure o PowerShell e registre o provedor de serviços de recuperação do Azure.
> * Crie um cofre dos Serviços de Recuperação.
> * Configure backup para SQL DB dentro de uma VM Azure.
> * Faça um trabalho de backup.
> * Restaurar um SQL DB de backup.
> * Monitore trabalhos de backup e restauração.

## <a name="before-you-start"></a>Antes de começar

* [Saiba mais](backup-azure-recovery-services-vault-overview.md) sobre os cofres dos Serviços de Recuperação.
* Leia sobre os recursos de recursos para [fazer backup de DBs SQL dentro de VMs azure](backup-azure-sql-database.md#before-you-start).
* Revise a hierarquia de objetos PowerShell para serviços de recuperação.

### <a name="recovery-services-object-hierarchy"></a>Hierarquia de objetos dos Serviços de Recuperação

A hierarquia do objeto é resumida no diagrama a seguir.

![Hierarquia de objetos dos Serviços de Recuperação](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Revise a referência de [cmdlet do](/powershell/module/az.recoveryservices) **Az.RecoveryServices** na biblioteca Azure.

### <a name="set-up-and-install"></a>Configurar e instalar

Configure o PowerShell da seguinte forma:

1. [Baixe a versão mais recente do Azure PowerShell](/powershell/azure/install-az-ps). A versão mínima necessária é 1.5.0.

2. Encontre os cmdlets powershell de backup do Azure com este comando:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. Revise os aliases e cmdlets para o Azure Backup e o cofre dos Serviços de Recuperação. Aqui está um exemplo do que você pode ver. Não é uma lista completa de cmdlets.

    ![Listar cmdlets dos Serviços de Recuperação](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. Faça login na sua conta do Azure com **o Connect-AzAccount**.
5. Na página da Web que aparece, você é solicitado a inserir suas credenciais de conta.

    * Alternativamente, você pode incluir suas credenciais de conta como parâmetro no cmdlet **Connect-AzAccount** com **-Credencial**.
    * Se você é um parceiro CSP trabalhando para um inquilino, especifique o cliente como inquilino, usando seu inquilinoID ou nome de domínio principal do inquilino. Um exemplo é **Connect-AzAccount -Tenant** fabrikam.com.

6. Associe a assinatura que deseja usar com a conta, pois uma conta pode ter várias assinaturas.

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

7. Se você estiver usando um Backup do Azure pela primeira vez, use o cmdlet **Register-AzResourceProvider** para registrar o provedor dos Serviços de Recuperação do Azure com sua assinatura.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

8. Verifique se os provedores se registraram com sucesso:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

9. Na saída de comando, verifique se **o RegistroÉ** altera-se em **Registrado**. Se isso não acontecer, execute novamente o **cmdlet Register-AzResourceProvider.**

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação

Siga estas etapas para criar um cofre dos Serviços de Recuperação:

O cofre dos Serviços de Recuperação é um recurso do Resource Manager e, portanto, você precisará colocá-lo em um grupo de recursos. Você pode usar um grupo de recursos existente ou criar um grupo de recursos com o cmdlet **New-AzResourceGroup**. Ao criar um grupo de recursos, especifique o nome e o local.

1. Um cofre é colocado em um grupo de recursos. Se você não tiver um grupo de recursos existente, crie um novo com o [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-1.4.0). Neste exemplo, criamos um novo grupo de recursos na região oeste dos EUA.

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```

2. Use o [cmdlet New-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/New-AzRecoveryServicesVault?view=azps-1.4.0) para criar o cofre. Especifique o mesmo local para o cofre usado para o grupo de recursos.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. Especifique o tipo de redundância a ser usada para o armazenamento do cofre.

    * Você pode usar [armazenamento com redundância local](../storage/common/storage-redundancy-lrs.md) ou [armazenamento com redundância geográfica](../storage/common/storage-redundancy-grs.md).
    * O exemplo a seguir define a opção **-BackupStorageRedundânciay** para[o Set-AzRecoveryServicesBackupProperty](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) cmd for **testvault** set to **GeoRedundant**.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

### <a name="view-the-vaults-in-a-subscription"></a>Exibir os cofres em uma assinatura

Para exibir todos os cofres da assinatura, use [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0).

```powershell
Get-AzRecoveryServicesVault
```

A saída é semelhante à seguinte. O grupo de recursos e a localização associados são fornecidos.

```output
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

### <a name="set-the-vault-context"></a>Definir o contexto do cofre

Armazene o objeto do cofre em uma variável e defina o contexto do cofre.

* Muitos cmdlets de backup do Azure requerem o objeto cofre do Recovery Services como uma entrada, por isso é conveniente armazenar o objeto do cofre em uma variável.
* O contexto do cofre é o tipo de dados protegido no cofre. Defina-o com [Set-AzRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0). Depois que o contexto é definido, aplica-se a todos os cmdlets subseqüentes.

O exemplo a seguir define o contexto de cofre de **testvault**.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>Busque a id do cofre.

Planejamos depreciar a configuração do contexto do cofre de acordo com as diretrizes do Azure PowerShell. Em vez disso, você pode armazenar ou buscar o ID do cofre, e passá-lo para comandos relevantes, da seguinte forma:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

## <a name="configure-a-backup-policy"></a>Configurar uma política de backup

Uma política de backup especifica o cronograma para backups e por quanto tempo os pontos de recuperação de backup devem ser mantidos:

* Uma política de backup está associada a pelo menos uma política de retenção. Uma política de retenção define por quanto tempo um ponto de recuperação é mantido até ser excluído.
* Exibir a retenção de diretiva de backup padrão usando [Get-AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0).
* Exibir o cronograma de política de backup padrão usando [Get-AzRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0).
* Você usa o [cmdlet New-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0) para criar uma nova política de backup. Você insere os objetos da política de agendamento e retenção.

Por padrão, um tempo de início é definido no Objeto de diretiva de agendamento. Use o exemplo a seguir para alterar o tempo de início para o tempo de início desejado. O horário de início desejado deve ser na UTC também. O exemplo abaixo pressupõe que a hora de início desejada seja 01:00 AM UTC para backups diários.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "MSSQL"
$UtcTime = Get-Date -Date "2019-03-20 01:30:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> Você precisa fornecer o tempo de partida apenas em múltiplos de 30 minutos. No exemplo acima, pode ser apenas "01:00:00" ou "02:30:00". O horário de início não pode ser "01:15:00"

O exemplo a seguir armazena a política de agendamento e a política de retenção em variáveis. Em seguida, usa essas variáveis como parâmetros para uma nova política (**NewSQLPolicy**). **NewSQLPolicy** faz um backup diário "Completo", mantém-o por 180 dias e faz um backup de log a cada 2 horas

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "MSSQL"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "MSSQL"
$NewSQLPolicy = New-AzRecoveryServicesBackupProtectionPolicy -Name "NewSQLPolicy" -WorkloadType "MSSQL" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

A saída é semelhante à seguinte.

```output
Name                 WorkloadType       BackupManagementType BackupTime                Frequency                                IsDifferentialBackup IsLogBackupEnabled
                                                                                                                                Enabled
----                 ------------       -------------------- ----------                ---------                                -------------------- ------------------
NewSQLPolicy         MSSQL              AzureWorkload        3/15/2019 01:30:00 AM      Daily                                    False                True
```

## <a name="enable-backup"></a>Habilite o backup

### <a name="registering-the-sql-vm"></a>Registrando o SQL VM

Para backups do Azure VM e compartilhamentos de arquivos Do Azure, o serviço de backup pode se conectar a esses recursos do Azure Resource Manager e buscar os detalhes relevantes. Como o SQL é um aplicativo dentro de uma VM do Azure, o serviço de backup precisa de permissão para acessar o aplicativo e buscar os detalhes necessários. Para fazer isso, você precisa *'registrar'* a VM do Azure que contém o aplicativo SQL com um cofre de serviços de recuperação. Uma vez que você registra um VM SQL com um cofre, você pode proteger os DBs SQL apenas para esse cofre. Use [Register-AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/Register-AzRecoveryServicesBackupContainer?view=azps-1.5.0) PS cmdlet para registrar o VM.

```powershell
 $myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
```

O comando retornará um 'contêiner de backup' deste recurso e o status será 'registrado'

> [!NOTE]
> Se o parâmetro de força não for dado, o usuário deverá confirmar com um texto 'Deseja desativar a proteção deste contêiner'. Por favor, ignore este texto e diga "Y" para confirmar. Este é um problema conhecido e estamos trabalhando para remover o texto e a exigência do parâmetro de força.

### <a name="fetching-sql-dbs"></a>Buscar DBs SQL

Uma vez feito o registro, o serviço de backup poderá listar todos os componentes SQL disponíveis dentro da VM. Para visualizar todos os componentes SQL ainda a serem copiados para este cofre, use [get-AzRecoveryServicesBackupProtectableItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) PS cmdlet

```powershell
Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -VaultId $targetVault.ID
```

A saída mostrará todos os componentes SQL desprotegidos em todas as VMs SQL registradas neste cofre com Tipo de Item e Nome do Servidor. Você pode filtrar ainda mais para um VM SQL específico passando o parâmetro '-Container' ou usar a combinação de 'Nome' e 'ServerName' juntamente com o sinalizador ItemType para chegar a um item SQL exclusivo.

```powershell
$SQLDB = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID -Name "<Item Name>" -ServerName "<Server Name>"
```

### <a name="configuring-backup"></a>Configurar o backup

Agora que temos o SQL DB necessário e a política com a qual ele precisa ser feito backup, podemos usar o [cmdlet Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) para configurar o backup para este SQL DB.

```output
Enable-AzRecoveryServicesBackupProtection -ProtectableItem $SQLDB -Policy $NewSQLPolicy
```

O comando aguarda até que o backup configurado seja concluído e retorne a seguinte saída.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 6:00:21 PM      3/18/2019 6:01:35 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="fetching-new-sql-dbs"></a>Buscando novos DBs SQL

Uma vez que a máquina esteja registrada, o serviço de backup buscará os detalhes dos DBs disponíveis em seguida. Se o usuário adicionar Instâncias SQL DBs/SQL à máquina registrada mais tarde, será necessário acionar manualmente o serviço de backup para executar uma nova 'consulta' para obter TODOS os DBs desprotegidos (incluindo os recém-adicionados) novamente. Use o [cmdlet PS Initialize-AzRecoveryServicesNo](https://docs.microsoft.com/powershell/module/az.recoveryservices/Initialize-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) enops no SQL VM para realizar uma nova consulta. O comando espera até que a operação seja concluída. Mais tarde, use o [get-AzRecoveryServicesBackupProtectableitem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) PS cmdlet para obter a lista dos componentes SQL desprotegidos mais recentes

```powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Initialize-AzRecoveryServicesBackupProtectableItem -Container $SQLContainer -WorkloadType MSSQL -VaultId $targetvault.ID
Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID
```

Uma vez que os itens protegidos relevantes sejam buscados, habilite os backups conforme instruído na [seção acima](#configuring-backup).
Se não quiser detectar manualmente novos DBs, ele pode optar pela autoproteção, conforme explicado [abaixo](#enable-autoprotection).

## <a name="enable-autoprotection"></a>Habilite a autoproteção

Um usuário pode configurar backup de modo que todos os DBs adicionados no futuro sejam automaticamente protegidos com uma determinada política. Para habilitar a proteção automática, use [o cmdlet PS do Enable-AzRecoveryServicesBackupAutoProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupAutoProtection?view=azps-1.5.0) PS.

Uma vez que a instrução é fazer backup de todos os Futuros DBs, a operação é feita em um nível SQLInstance.

```powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Enable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -Policy $NewSQLPolicy -VaultId $targetvault.ID
```

Uma vez dada a intenção de autoproteção, o inquérito sobre a máquina para buscar DBs recém-adicionados ocorre como uma tarefa de fundo programada a cada 8 horas.

## <a name="restore-sql-dbs"></a>Restaurar DBs SQL

O Azure Backup pode restaurar os bancos de dados do SQL Server que estão sendo executados em VMs do Azure da seguinte forma:

* Restaure-a em uma data ou hora específica (para a segunda) usando backups de log de transações. O Azure Backup determina automaticamente o backup diferencial completo apropriado e a cadeia de backups de log que são necessários para restaurar com base no tempo selecionado.
* Restaure um backup completo ou diferencial específico para restaurar um ponto de recuperação específico.

Verifique os pré-requisitos [aqui](restore-sql-database-azure-vm.md#prerequisites) mencionados antes de restaurar Os DBs SQL.

Primeiro busque o SQL DB de backup relevante usando o [cmdlet GET-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem?view=azps-1.5.0) PS.

```powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
```

### <a name="fetch-the-relevant-restore-time"></a>Busque o tempo de restauração relevante

Como descrito acima, o usuário pode restaurar o SQL DB de backup para uma cópia completa/diferencial **OU** para um ponto de log no tempo.

#### <a name="fetch-distinct-recovery-points"></a>Busque pontos de recuperação distintos

Use [Get-AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupRecoveryPoint?view=azps-1.5.0) para buscar pontos de recuperação distintos (completos/diferenciais) para um SQL DB de backup.

```powershell
$startDate = (Get-Date).AddDays(-7).ToUniversalTime()
$endDate = (Get-Date).ToUniversalTime()
Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -StartDate $startdate -EndDate $endDate
```

A saída é semelhante ao exemplo a seguir

```output
RecoveryPointId    RecoveryPointType  RecoveryPointTime      ItemName                             BackupManagemen
                                                                                                  tType
---------------    -----------------  -----------------      --------                             ---------------
6660368097802      Full               3/18/2019 8:09:35 PM   MSSQLSERVER;model             AzureWorkload
```

Use o filtro 'RecoveryPointId' ou um filtro de matriz para buscar o ponto de recuperação relevante.

```powershell
$FullRP = Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -RecoveryPointId "6660368097802"
```

#### <a name="fetch-point-in-time-recovery-point"></a>Ponto de recuperação point-in-time buscar

Se o usuário quiser restaurar o DB a um determinado ponto no tempo, use [get-AzRecoveryServicesBackupRecoveryLogChain](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupRecoveryLogChain?view=azps-1.5.0) PS cmdlet. O cmdlet retorna uma lista de datas que representam os tempos de início e término de uma cadeia de log contínua e ininterrupta para esse item de backup SQL. O ponto-no-tempo desejado deve estar dentro deste intervalo.

```powershell
Get-AzRecoveryServicesBackupRecoveryLogChain -Item $bkpItem -Item -VaultId $targetVault.ID
```

A saída será semelhante ao exemplo a seguir.

```output
ItemName                       StartTime                      EndTime
--------                       ---------                      -------
SQLDataBase;MSSQLSERVER;azu... 3/18/2019 8:09:35 PM           3/19/2019 12:08:32 PM
```

A saída acima significa que o usuário pode restaurar a qualquer ponto no tempo entre a hora de início exibida e o tempo de término. Os tempos estão na UTC. Construa qualquer ponto-no-tempo em PS que esteja dentro do intervalo mostrado acima.

> [!NOTE]
> Quando um ponto de log-in-time selecionado para restauração, o usuário não precisa especificar o ponto de partida, ou seja, backup completo do qual o DB é restaurado. O serviço de backup do Azure cuidará de todo o plano de recuperação, ou seja, qual backup completo para escolher, quais backups de log para aplicar etc.

### <a name="determine-recovery-configuration"></a>Determinar a configuração de recuperação

No caso da restauração sql DB, os seguintes cenários de restauração são suportados.

* Substituindo o SQL DB de backup com dados de outro ponto de recuperação - OriginalWorkloadRestore
* Restaurando o SQL DB como um novo DB na mesma instância SQL - AlternateWorkloadRestore
* Restaurando o SQL DB como um novo DB em outra instância SQL em outro VM SQL - AlternateWorkloadRestore
* Restaurando o SQL DB como arquivos .bak -RestoreAsFiles

Depois de buscar o ponto de recuperação relevante (distinto ou ponto de registro no tempo), use [Get-AzRecoveryServicesBackupWorkloadRecoveryRecovery](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupWorkloadRecoveryConfig?view=azps-1.5.0) PS cmdlet para buscar o objeto de configuração de recuperação conforme o plano de recuperação desejado.

#### <a name="original-workload-restore"></a>Restauração original da carga de trabalho

Para substituir o DB de backup com dados do ponto de recuperação, basta especificar o sinalizador certo e o ponto de recuperação relevante, conforme mostrado no exemplo a seguir.

##### <a name="original-restore-with-distinct-recovery-point"></a>Restauração original com ponto de recuperação distinto

```powershell
$OverwriteWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -OriginalWorkloadRestore -VaultId $targetVault.ID
```

##### <a name="original-restore-with-log-point-in-time"></a>Restauração original com ponto de log-in-time

```powershell
$OverwriteWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem  -OriginalWorkloadRestore -VaultId $targetVault.ID
```

#### <a name="alternate-workload-restore"></a>Restauração alternativa da carga de trabalho

> [!IMPORTANT]
> Um SQL DB de backup pode ser restaurado como um novo DB para outro SQLInstance apenas, em uma VM Azure 'registrada' neste cofre.

Como descrito acima, se o SQLInstance de destino estiver dentro de outra VM do Azure, certifique-se de que ele está [registrado neste cofre](#registering-the-sql-vm) e o SQLInstance relevante aparecerá como um item protegido.

```powershell
$TargetInstance = Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -ItemType SQLInstance -Name "<SQLInstance Name>" -ServerName "<SQL VM name>" -VaultId $targetVault.ID
```

Em seguida, basta passar o ponto de recuperação relevante, direcionar a instância SQL com a bandeira direita como mostrado abaixo.

##### <a name="alternate-restore-with-distinct-recovery-point"></a>Restauração alternativa com ponto de recuperação distinto

```powershell
$AnotherInstanceWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -TargetItem $TargetInstance -AlternateWorkloadRestore -VaultId $targetVault.ID
```

##### <a name="alternate-restore-with-log-point-in-time"></a>Restauração alternativa com ponto de log no tempo

```powershell
$AnotherInstanceWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem -AlternateWorkloadRestore -VaultId $targetVault.ID
```

##### <a name="restore-as-files"></a>Restaurar como Arquivos

Para restaurar os dados de backup como arquivos .bak em vez de um banco de dados, escolha a opção **Restaurar como Arquivos.** O SQL DB de backup pode ser restaurado para qualquer VM de destino que esteja registrada neste cofre.

```powershell
$TargetContainer= Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName "VM name" -VaultId $vaultID
```

##### <a name="restore-as-files-with-distinct-recovery-point"></a>Restaurar como arquivos com ponto de recuperação distinto

```powershell
$FileRestoreWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -TargetContainer $TargetContainer -RestoreAsFiles -FilePath "<>" -VaultId $targetVault.ID
```

##### <a name="restore-as-files-with-log-point-in-time-from-latest-full"></a>Restaurar como arquivos com log point-in-time a partir do mais recente completo

```powershell
$FileRestoreWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -TargetContainer $TargetContainer -RestoreAsFiles -FilePath "<>" -VaultId $targetVault.ID
```

##### <a name="restore-as-files-with-log-point-in-time-from-a-specified-full"></a>Restaurar como arquivos com log point-in-time a partir de um completo especificado

Se você quiser dar um máximo específico que deve ser usado para restaurar, use o seguinte comando:

```powershell
$FileRestoreWithLogAndSpecificFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -FromFull $FullRP -TargetContainer $TargetContainer -RestoreAsFiles -FilePath "<>" -VaultId $targetVault.ID
```

O objeto final de configuração do ponto de recuperação obtido do [Get-AzRecoveryServicesBackupWorkloadWorkloadRecoveryConfig](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupWorkloadRecoveryConfig?view=azps-1.5.0) PS cmdlet tem todas as informações relevantes para restauração e é como mostrado abaixo.

```output
TargetServer         : <SQL server name>
TargetInstance       : <Target Instance name>
RestoredDBName       : <Target Instance name>/azurebackup1_restored_3_19_2019_1850
OverwriteWLIfpresent : No
NoRecoveryMode       : Disabled
targetPhysicalPath   : {azurebackup1, azurebackup1_log}
ContainerId          : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/vmappcontainer;compute;computeRG;SQLVMName
SourceResourceId     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/computeRG/VMAppContainer/SQLVMName
RestoreRequestType   : Alternate WL Restore
RecoveryPoint        : Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureWorkloadRecoveryPoint
PointInTime          : 1/1/0001 12:00:00 AM
```

Você pode editar o nome DB restaurado, OverwriteWLIfpresent, NoRecoveryMode e targetPhysicalPath. Obtenha mais detalhes para os caminhos de arquivo de destino como mostrado abaixo.

```powershell
$AnotherInstanceWithFullConfig.targetPhysicalPath
```

```output
MappingType SourceLogicalName SourcePath                  TargetPath
----------- ----------------- ----------                  ----------
Data        azurebackup1      F:\Data\azurebackup1.mdf    F:\Data\azurebackup1_1553001753.mdf
Log         azurebackup1_log  F:\Log\azurebackup1_log.ldf F:\Log\azurebackup1_log_1553001753.ldf
```

Defina as propriedades PS relevantes como valores de seqüência como mostrado abaixo.

```powershell
$AnotherInstanceWithFullConfig.OverwriteWLIfpresent = "Yes"
$AnotherInstanceWithFullConfig | fl
```

```output
TargetServer         : <SQL server name>
TargetInstance       : <Target Instance name>
RestoredDBName       : <Target Instance name>/azurebackup1_restored_3_19_2019_1850
OverwriteWLIfpresent : Yes
NoRecoveryMode       : Disabled
targetPhysicalPath   : {azurebackup1, azurebackup1_log}
ContainerId          : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/vmappcontainer;compute;computeRG;SQLVMName
SourceResourceId     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/computeRG/VMAppContainer/SQLVMName
RestoreRequestType   : Alternate WL Restore
RecoveryPoint        : Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureWorkloadRecoveryPoint
PointInTime          : 1/1/0001 12:00:00 AM
```

> [!IMPORTANT]
> Certifique-se de que o objeto de configuração de recuperação final tenha todos os valores necessários e adequados, uma vez que a operação de restauração será baseada no objeto de configuração.

### <a name="restore-with-relevant-configuration"></a>Restaurar com configuração relevante

Uma vez que o objeto de configuração de recuperação relevante seja obtido e verificado, use o [cmdlet RESTORE-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Restore-AzRecoveryServicesBackupItem?view=azps-1.5.0) PS para iniciar o processo de restauração.

```powershell
Restore-AzRecoveryServicesBackupItem -WLRecoveryConfig $AnotherInstanceWithLogConfig -VaultId $targetVault.ID
```

A operação de restauração retorna um trabalho a ser rastreado.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Restore              InProgress           3/17/2019 10:02:45 AM                                3274xg2b-e4fg-5952-89b4-8cb566gc1748
```

## <a name="manage-sql-backups"></a>Gerenciar backups SQL

### <a name="on-demand-backup"></a>Backup demanda

Uma vez que o backup tenha sido ativado para um DB, o usuário também pode acionar um backup demanda para o DB usando [backup-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Backup-AzRecoveryServicesBackupItem?view=azps-1.5.0) PS cmdlet. O exemplo a seguir aciona um backup completo em um DB SQL com compressão ativada e o backup completo deve ser mantido por 60 dias.

```powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
$endDate = (Get-Date).AddDays(60).ToUniversalTime()
Backup-AzRecoveryServicesBackupItem -Item $bkpItem -BackupType Full -EnableCompression -VaultId $targetVault.ID -ExpiryDateTimeUTC $endDate
```

O comando de backup demanda retorna um trabalho a ser rastreado.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Backup               InProgress           3/18/2019 8:41:27 PM                                2516bb1a-d3ef-4841-97a3-9ba455fb0637
```

Se a saída for perdida ou se você quiser obter o ID de trabalho relevante, [obtenha a lista de empregos](#track-azure-backup-jobs) do serviço de backup do Azure e, em seguida, rastreie-a e seus detalhes.

### <a name="change-policy-for-backup-items"></a>Alterar política para itens de backup

O usuário pode modificar a política existente ou alterar a política do item de backup de Policy1 para Policy2. Para alternar políticas para um item de backup, busque a política relevante e faça backup do item e use o comando [Enable-AzRecoveryServices](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) com item de backup como parâmetro.

```powershell
$TargetPol1 = Get-AzRecoveryServicesBackupProtectionPolicy -Name <PolicyName>
$anotherBkpItem = Get-AzRecoveryServicesBackupItem -WorkloadType MSSQL -BackupManagementType AzureWorkload -Name "<BackupItemName>"
Enable-AzRecoveryServicesBackupProtection -Item $anotherBkpItem -Policy $TargetPol1
```

O comando aguarda até que o backup configurado seja concluído e retorne a seguinte saída.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 8:00:21 PM      3/18/2019 8:02:16 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="re-register-sql-vms"></a>Reregistre VMs SQL

> [!WARNING]
> Certifique-se de ler este [documento](backup-sql-server-azure-troubleshoot.md#re-registration-failures) para entender os sintomas de falha e causas antes de tentar o recadastramento

Para ativar o recadastramento do SQL VM, busque o recipiente de backup relevante e passe-o para o cmdlet de registro.

```powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Register-AzRecoveryServicesBackupContainer -Container $SQLContainer -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID
```

### <a name="stop-protection"></a>Parar a proteção

#### <a name="retain-data"></a>Reter dados

Se o usuário desejar interromper a proteção, ele pode usar o [cmdlet DEDisabl-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) PS. Isso impedirá os backups programados, mas os dados copiados até agora são retidos para sempre.

```powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID
```

#### <a name="delete-backup-data"></a>Excluir dados de backup

Para remover completamente os dados de backup armazenados no cofre, basta adicionar o sinalizador/switch 'RemoveRecoveryPoints' ao [comando de proteção 'desativar'.](#retain-data)

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID -RemoveRecoveryPoints
```

#### <a name="disable-auto-protection"></a>Desativar proteção automática

Se a proteção automática foi configurada em um SQLInstance, o usuário poderá desativá-la usando o [cmdlet Disable-AzRecoveryServicesBackupAutoProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupAutoProtection?view=azps-1.5.0) PS.

```powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Disable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetvault.ID
```

#### <a name="unregister-sql-vm"></a>Sql VM sql desregistrado

Se todos os DBs de um servidor SQL [não estiverem mais protegidos e não existirem dados de backup,](#delete-backup-data)o usuário poderá cancelar o registro do VM SQL deste cofre. Só então o usuário pode proteger DBs para outro cofre. Use [Unregister-AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/Unregister-AzRecoveryServicesBackupContainer?view=azps-1.5.0) PS cmdlet para cancelar o registro do VM SQL.

```powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
 Unregister-AzRecoveryServicesBackupContainer -Container $SQLContainer -VaultId $targetvault.ID
```

### <a name="track-azure-backup-jobs"></a>Acompanhe os trabalhos de backup do Azure

É importante notar que o Azure Backup só rastreia trabalhos acionados pelo usuário no backup SQL. Os backups programados (incluindo backups de log) não são visíveis no portal/powershell. No entanto, se algum trabalho programado falhar, um [alerta de backup](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault) é gerado e mostrado no portal. [Use o Monitor Azure](backup-azure-monitoring-use-azuremonitor.md) para rastrear todos os trabalhos programados e outras informações relevantes.

Os usuários podem acompanhar as operações acionadas demanda/usuário com o JobID que é devolvido na [saída](#on-demand-backup) de trabalhos assíncronos, como backup. Use [get-AzRecoveryServicesBackupJobDetail](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupJobDetail) PS cmdlet para acompanhar o trabalho e seus detalhes.

```powershell
 Get-AzRecoveryServicesBackupJobDetails -JobId 2516bb1a-d3ef-4841-97a3-9ba455fb0637 -VaultId $targetVault.ID
```

Para obter a lista de empregos demanda e seus status do serviço de backup do Azure, use [o cmdlet GET-AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupJob?view=azps-1.5.0) PS. O exemplo a seguir retorna todos os trabalhos SQL em andamento.

```powershell
Get-AzRecoveryServicesBackupJob -Status InProgress -BackupManagementType AzureWorkload
```

Para cancelar um trabalho em andamento, use o [cmdlet STOP-AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/Stop-AzRecoveryServicesBackupJob?view=azps-1.5.0) PS.

## <a name="managing-sql-always-on-availability-groups"></a>Gerenciando grupos sql sempre em disponibilidade

Para SQL Always On Availability Groups, certifique-se de [registrar todos os nós](#registering-the-sql-vm) do grupo Disponibilidade (AG). Uma vez que o registro é feito para todos os nós, um objeto de grupo de disponibilidade SQL é criado logicamente itens protegidos. As bases de dados o SQL AG serão listadas como 'SQLDatabase'. Os nós aparecerão como instâncias autônomas e os bancos de dados SQL padrão eles também serão listados como bancos de dados SQL.

Por exemplo, vamos supor que um SQL AG tenha dois nós: 'sql-server-0' e 'sql-server-1' e 1 SQL AG DB. Uma vez que ambos os nós estejam registrados, se o usuário [listar os itens protegidos,](#fetching-sql-dbs)ele lista os seguintes componentes

* Um objeto SQL AG - tipo de item protegido como SQLAvailabilityGroup
* Um SQL AG DB - tipo de item protegido como SQLDatabase
* sql-server-0 - tipo de item protegido como SQLInstance
* sql-server-1 - tipo de item protegido como SQLInstance
* Quaisquer DBs SQL padrão (mestre, modelo, msdb) sql-server-0 - tipo de item protectável como SQLDatabase
* Quaisquer DBs SQL padrão (mestre, modelo, msdb) sql-server-1 - tipo de item protectável como SQLDatabase

sql-server-0, sql-server-1 também será listado como "AzureVMAppContainer" quando [os contêineres de backup forem listados](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupContainer?view=azps-1.5.0).

Basta buscar o banco de dados SQL relevante para [permitir](#configuring-backup) backup e os [cmdlets ps](#restore-sql-dbs) de backup demanda e de [backup](#on-demand-backup) são idênticos.
