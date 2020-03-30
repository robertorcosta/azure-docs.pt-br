---
title: Fazer backup de arquivos Azure com PowerShell
description: Neste artigo, saiba como fazer backup dos arquivos do Azure usando o serviço de backup do Azure e do PowerShell.
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: f85451e0da6458de34aea936836b46781f4c4a21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273534"
---
# <a name="back-up-azure-files-with-powershell"></a>Fazer backup de arquivos Azure com PowerShell

Este artigo descreve como usar o Azure PowerShell para fazer backup de um compartilhamento de arquivos do Azure usando um cofre [do Azure Backup](backup-overview.md) Recovery Services.

Este artigo explica como:

> [!div class="checklist"]
>
> * Configure o PowerShell e registre o provedor de serviços de recuperação do Azure.
> * Crie um cofre dos Serviços de Recuperação.
> * Configure backup para um compartilhamento de arquivos Azure.
> * Faça um trabalho de backup.

## <a name="before-you-start"></a>Antes de começar

* [Saiba mais](backup-azure-recovery-services-vault-overview.md) sobre os cofres dos Serviços de Recuperação.
* Leia sobre os recursos de visualização para [fazer backup das partes de arquivos do Azure](backup-afs.md).
* Revise a hierarquia de objetos PowerShell para serviços de recuperação.

## <a name="recovery-services-object-hierarchy"></a>Hierarquia de objetos dos Serviços de Recuperação

A hierarquia do objeto é resumida no diagrama a seguir.

![Hierarquia de objetos dos Serviços de Recuperação](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Revise a referência de [cmdlet do](/powershell/module/az.recoveryservices) **Az.RecoveryServices** na biblioteca Azure.

## <a name="set-up-and-install"></a>Configurar e instalar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Configure o PowerShell da seguinte forma:

1. [Baixe a versão mais recente do Azure PowerShell](/powershell/azure/install-az-ps). A versão 1.0.0 é a mínima necessária.

> [!WARNING]
> A versão mínima do PS necessária para visualização foi 'Az 1.0.0'. Devido às próximas alterações para GA, a versão mínima do PS necessária será 'Az.RecoveryServices 2.6.0'. É muito importante atualizar todas as versões existentes de PS para esta versão. Caso contrário, os scripts existentes serão quebrados após ga. Instale a versão mínima com os seguintes comandos PS

```powershell
Install-module -Name Az.RecoveryServices -RequiredVersion 2.6.0
```

2. Encontre os cmdlets powershell de backup do Azure com este comando:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. Revise os aliases e cmdlets para backup do Azure, recuperação do site do Azure e o cofre dos Serviços de Recuperação aparecer. Aqui está um exemplo do que você pode ver. Não é uma lista completa de cmdlets.

    ![Listar cmdlets dos Serviços de Recuperação](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. Faça login na sua conta do Azure com **o Connect-AzAccount**.
5. Na página da Web que aparece, você é solicitado a inserir suas credenciais de conta.

    * Alternativamente, você pode incluir suas credenciais de conta como parâmetro no cmdlet **Connect-AzAccount** com **-Credencial**.
    * Se você é um parceiro CSP trabalhando em nome de um inquilino, especifique o cliente como inquilino, usando seu inquilinoID ou nome de domínio principal do inquilino. Um exemplo é **Connect-AzAccount -Tenant** fabrikam.com.

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

O cofre dos Serviços de Recuperação é um recurso do Resource Manager e, portanto, você precisará colocá-lo em um grupo de recursos. Você pode usar um grupo de recursos existente ou criar um grupo de recursos com o cmdlet **New-AzResourceGroup**. Ao criar um grupo de recursos, especifique o nome e o local.

Siga estas etapas para criar um cofre dos Serviços de Recuperação:

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
   * O exemplo a seguir define a opção **-BackupStorageRedundânciay** para o[Set-AzRecoveryServicesBackupProperties](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) cmd for **testvault** set to **GeoRedundant**.

     ```powershell
     $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
     Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
     ```

### <a name="view-the-vaults-in-a-subscription"></a>Exibir os cofres em uma assinatura

Para exibir todos os cofres da assinatura, use [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0).

```powershell
Get-AzRecoveryServicesVault
```

A saída é semelhante à seguinte. Observe que o grupo de recursos e a localização associados são fornecidos.

```powershell
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

Planejamos depreciar a configuração do contexto do cofre de acordo com as diretrizes do Azure PowerShell. Em vez disso, você pode armazenar ou buscar o ID do cofre e passá-lo para comandos relevantes. Então, se você não definiu o contexto do cofre ou deseja especificar o comando para executar para um determinado cofre, passe o ID do cofre como "-vaultID" para todos os comandos relevantes da seguinte forma:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol -VaultID $vaultID
```

## <a name="configure-a-backup-policy"></a>Configurar uma política de backup

Uma política de backup especifica o cronograma para backups e por quanto tempo os pontos de recuperação de backup devem ser mantidos:

* Uma política de backup está associada a pelo menos uma política de retenção. Uma política de retenção define por quanto tempo um ponto de recuperação é mantido até ser excluído.
* Exibir a retenção de diretiva de backup padrão usando [Get-AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0).
* Exibir o cronograma de política de backup padrão usando [Get-AzRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0).
* Você usa o [cmdlet New-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0) para criar uma nova política de backup. Você insere os objetos da política de agendamento e retenção.

Por padrão, um tempo de início é definido no Objeto de diretiva de agendamento. Use o exemplo a seguir para alterar o tempo de início para o tempo de início desejado. O horário de início desejado deve ser na UTC também. O exemplo abaixo pressupõe que a hora de início desejada seja 01:00 AM UTC para backups diários.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$UtcTime = Get-Date -Date "2019-03-20 01:30:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> Você precisa fornecer o tempo de partida apenas em múltiplos de 30 minutos. No exemplo acima, pode ser apenas "01:00:00" ou "02:30:00". O horário de início não pode ser "01:15:00"

O exemplo a seguir armazena a política de agendamento e a política de retenção em variáveis. Em seguida, usa essas variáveis como parâmetros para uma nova política (**NewAFSPolicy**). **NewAFSPolicy** faz um backup diário e os retém por 30 dias.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureFiles"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

A saída é semelhante à seguinte.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewAFSPolicy           AzureFiles            AzureStorage              10/24/2019 1:30:00 AM
```

## <a name="enable-backup"></a>Habilite o backup

Depois de definir a política de backup, você pode habilitar a proteção para o compartilhamento de arquivos do Azure usando a diretiva.

### <a name="retrieve-a-backup-policy"></a>Recupere uma política de backup

Você busca o objeto de diretiva relevante com [Get-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0). Use este cmdlet para obter uma política específica ou para visualizar as políticas associadas a um tipo de carga de trabalho.

#### <a name="retrieve-a-policy-for-a-workload-type"></a>Recupere uma diretiva para um tipo de carga de trabalho

O exemplo a seguir recupera políticas para o tipo de carga de trabalho **AzureFiles**.

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureFiles"
```

A saída é semelhante à seguinte.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
dailyafs             AzureFiles         AzureStorage         1/10/2018 12:30:00 AM
```

> [!NOTE]
> O fuso horário do campo **BackupTime** no PowerShell é o UTC (Tempo Universal Coordenado). Quando a hora de backup é mostrada no Portal do Azure, o horário é ajustado para seu fuso horário local.

### <a name="retrieve-a-specific-policy"></a>Recupere uma política específica

A política a seguir recupera a política de backup **dailyafs**.

```powershell
$afsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "dailyafs"
```

### <a name="enable-backup-and-apply-policy"></a>Habilite backup e aplique política

Habilite a proteção com [o Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0). Depois que a diretiva é associada ao cofre, os backups são acionados de acordo com o cronograma da diretiva.

O exemplo a seguir permite a proteção para o teste de compartilhamento de arquivos **AzureAzureFileShare** no teste da conta **de armazenamentoStorageAcct**, com a política **dailyafs**.

```powershell
Enable-AzRecoveryServicesBackupProtection -StorageAccountName "testStorageAcct" -Name "testAzureFS" -Policy $afsPol
```

O comando aguarda até que o trabalho de configurar a proteção seja concluído e fornece uma saída semelhante à mostrada abaixo.

```cmd
WorkloadName       Operation            Status                 StartTime                                                                                                         EndTime                   JobID
------------             ---------            ------               ---------                                  -------                   -----
testAzureFS       ConfigureBackup      Completed            11/12/2018 2:15:26 PM     11/12/2018 2:16:11 PM     ec7d4f1d-40bd-46a4-9edb-3193c41f6bf6
```

## <a name="important-notice---backup-item-identification-for-afs-backups"></a>Aviso importante - Identificação de itens de backup para backups AFS

Esta seção descreve uma mudança importante no backup AFS em preparação para o GA.

Ao ativar o backup para AFS, o usuário fornece o nome de compartilhamento de arquivo amigável ao cliente como o nome da entidade e um item de backup é criado. O 'nome' do item de backup é um identificador exclusivo criado pelo serviço de backup do Azure. Normalmente, o identificador envolve nome amigável. Mas para lidar com o importante cenário de soft-delete, onde um compartilhamento de arquivos pode ser excluído e outro compartilhamento de arquivo pode ser criado com o mesmo nome, a identidade exclusiva do compartilhamento de arquivos Azure agora será um ID em vez de um nome amigável ao cliente. Para saber a identidade/nome exclusivo de cada item, basta executar o comando com os ```Get-AzRecoveryServicesBackupItem``` filtros relevantes para backupManagementType e WorkloadType para obter todos os itens relevantes e, em seguida, observar o campo de nome no objeto/resposta PS retornado. É sempre recomendado listar itens e, em seguida, recuperar seu nome único do campo 'nome' em resposta. Use este valor para filtrar os itens com o parâmetro 'Nome'. Caso contrário, use o parâmetro FriendlyName para recuperar o item com seu nome/identificador amigável ao cliente.

> [!WARNING]
> Certifique-se de que a versão ps seja atualizada para a versão mínima de 'Az.RecoveryServices 2.6.0' para backups AFS. Com esta versão, o filtro 'friendlyName' está disponível para ```Get-AzRecoveryServicesBackupItem``` comando. Passe o nome de compartilhamento de arquivos do Azure para um parâmetro friendlyName. Se você passar o nome Azure File Share para o parâmetro 'Nome', esta versão lançará um aviso para passar este nome amigável para o parâmetro de nome amigável. A não instalação desta versão mínima pode resultar em falha dos scripts existentes. Instale a versão mínima do PS com o seguinte comando.

```powershell
Install-module -Name Az.RecoveryServices -RequiredVersion 2.6.0
```

## <a name="trigger-an-on-demand-backup"></a>Disparar um backup sob demanda

Use [backup-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem?view=azps-1.4.0) para executar um backup demanda para um compartilhamento de arquivos Azure protegido.

1. Recupere a conta de armazenamento do contêiner no cofre que contém seus dados de backup com [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer).
2. Para iniciar um trabalho de backup, você obtém informações sobre o compartilhamento de arquivos do Azure com [o Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem).
3. Execute um backup sob demanda com [Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem).

Execute o backup demanda da seguinte forma:

```powershell
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType "AzureFiles" -FriendlyName "testAzureFS"
$job =  Backup-AzRecoveryServicesBackupItem -Item $afsBkpItem
```

O comando retorna um trabalho com uma ID a ser rastreada, como no exemplo a seguir.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS       Backup               Completed            11/12/2018 2:42:07 PM     11/12/2018 2:42:11 PM     8bdfe3ab-9bf7-4be6-83d6-37ff1ca13ab6
```

Os instantâneos do compartilhamento de arquivos do Azure são usados enquanto os backups são feitos, portanto, o trabalho normalmente é concluído no momento em que o comando retorna essa saída.

### <a name="using-on-demand-backups-to-extend-retention"></a>Usando backups demanda para ampliar a retenção

Backups demanda podem ser usados para reter seus instantâneos por 10 anos. Os agendadores podem ser usados para executar scripts PowerShell demanda com retenção escolhida e, assim, tirar instantâneos em intervalos regulares a cada semana, mês ou ano. Ao tirar instantâneos regulares, consulte as [limitações dos backups demanda](https://docs.microsoft.com/azure/backup/backup-azure-files-faq#how-many-on-demand-backups-can-i-take-per-file-share) usando backup do Azure.

Se você estiver procurando scripts de exemplo, você pode consultar<https://github.com/Azure-Samples/Use-PowerShell-for-long-term-retention-of-Azure-Files-Backup>o script de exemplo no GitHub ( ) usando o runbook azure Automation que permite agendar backups em uma base periódica e retê-los até 10 anos.

> [!WARNING]
> Certifique-se de que a versão ps seja atualizada para a versão mínima de 'Az.RecoveryServices 2.6.0' para backups AFS em seus runbooks de automação. Você terá que substituir o antigo módulo 'AzureRM' pelo módulo 'Az'. Com esta versão, o filtro 'friendlyName' está disponível para ```Get-AzRecoveryServicesBackupItem``` comando. Passe o nome do compartilhamento de arquivo azure para o parâmetro friendlyName. Se você passar o nome de compartilhamento de arquivo azure para o parâmetro 'Nome', esta versão lançará um aviso para passar este nome amigável para o parâmetro de nome amigável.

## <a name="next-steps"></a>Próximas etapas

[Saiba mais sobre](backup-afs.md) o backup de arquivos do Azure no portal Azure.
