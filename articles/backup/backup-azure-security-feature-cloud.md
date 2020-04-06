---
title: Recursos de segurança para ajudar a proteger as cargas de trabalho na nuvem
description: Saiba como usar os recursos de segurança no Azure Backup para tornar os backups mais seguros.
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: bd7c86e18114513a264a0f9252589533fb7ff2d3
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668733"
---
# <a name="security-features-to-help-protect-cloud-workloads-that-use-azure-backup"></a>Recursos de segurança para ajudar a proteger as cargas de trabalho na nuvem que usam o Azure Backup

Preocupações sobre problemas de segurança, como malware, ransomware e invasão, estão aumentando. Esses problemas de segurança podem ser dispendiosos em termos de dinheiro e dados. Para se proteger contra esses ataques, o Azure Backup agora fornece recursos de segurança para ajudar a proteger os dados de backup mesmo após a exclusão.

Um desses recursos é o soft delete. Com a exclusão suave, mesmo que um ator mal-intencionado exclua o backup de uma VM (ou os dados de backup são acidentalmente excluídos), os dados de backup são retidos por 14 dias adicionais, permitindo a recuperação desse item de backup sem perda de dados. A retenção adicional de 14 dias de dados de backup no estado de "soft delete" não incorre em nenhum custo para o cliente. O Azure também criptografa todos os dados de backup em repouso usando a [Criptografia do Serviço de Armazenamento](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) para proteger ainda mais seus dados.

A proteção de exclusão suave para máquinas virtuais Azure geralmente está disponível.

>[!NOTE]
>Exclusão suave para servidor SQL no Azure VM e soft delete for SAP HANA in Azure VM workloads now available in preview.<br>
>Para se inscrever para a pré-visualização, escreva para nós emAskAzureBackupTeam@microsoft.com

## <a name="soft-delete"></a>Exclusão suave

### <a name="soft-delete-for-vms"></a>Exclusão suave para VMs

A exclusão suave para VMs protege os backups de suas VMs contra exclusão não intencional. Mesmo depois que os backups são excluídos, eles são preservados em estado de exclusão suave por 14 dias adicionais.

> [!NOTE]
> O soft delete só protege dados de backup excluídos. Se uma VM for excluída sem um backup, o recurso soft-delete não preservará os dados. Todos os recursos devem ser protegidos com o Backup do Azure para garantir total resiliência.
>

### <a name="supported-regions"></a>Regiões com suporte

Atualmente, o Soft Delete é suportado no Centro-Oeste dos EUA, Leste da Ásia, Canadá Central, Canadá Leste, França Central, França Do Sul, Coréia Central, Coréia do Sul, Reino Unido, Reino Unido Oeste, Austrália Leste, Austrália Sudeste, Europa Norte, EUA Ocidental, US$ 42, Central dos EUA, Sudeste Asiático, Norte Central dos EUA, Japão Leste, Japão Oeste, Índia, Índia, Índia Central, Índia Ocidental, Leste dos EUA , Suíça Norte, Suíça Oeste, Noruega Oeste, Noruega Leste e todas as regiões Nacionais.

### <a name="soft-delete-for-vms-using-azure-portal"></a>Exclusão suave para VMs usando portal Azure

1. Para excluir os dados de backup de uma VM, o backup deve ser interrompido. No portal Azure, vá para o cofre dos serviços de recuperação, clique com o botão direito do mouse no item de backup e escolha **Parar de fazer backup**.

   ![Captura de tela dos itens de backup do portal Azure](./media/backup-azure-security-feature-cloud/backup-stopped.png)

2. Na janela seguinte, você terá a opção de excluir ou reter os dados de backup. Se você escolher **Excluir dados de backup** e, em seguida, interromper o **backup,** o backup da VM não será excluído permanentemente. Em vez disso, os dados de backup serão retidos por 14 dias no estado de exclusão suave. Se **os dados de backup excluirforem,** um alerta de exclusão de e-mail será enviado para o ID de e-mail configurado informando ao usuário que restam 14 dias de retenção estendida para dados de backup. Além disso, um alerta de e-mail é enviado no 12º dia informando que faltam mais dois dias para ressuscitar os dados excluídos. A exclusão é adiada até o dia 15, quando ocorrerá a exclusão permanente e um alerta final de e-mail é enviado informando sobre a exclusão permanente dos dados.

   ![Captura de tela do portal Azure, tela Stop Backup](./media/backup-azure-security-feature-cloud/delete-backup-data.png)

3. Durante esses 14 dias, no Cofre de Serviços de Recuperação, a VM soft excluída aparecerá com um ícone vermelho "soft-delete" ao lado.

   ![Captura de tela do portal Azure, VM em estado de exclusão suave](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

   > [!NOTE]
   > Se algum item de backup excluído estiver presente no cofre, o cofre não poderá ser excluído nesse momento. Por favor, tente a exclusão do cofre depois que os itens de backup forem excluídos permanentemente e não houver nenhum item no estado deletado suave deixado no cofre.

4. Para restaurar a VM soft-excluída, ela deve primeiro ser desexcluída. Para desexcluir, escolha a VM excluída e selecione a opção **Desexcluir**.

   ![Captura de tela do portal Azure, Undelete VM](./media/backup-azure-security-feature-cloud/choose-undelete.png)

   Uma janela aparecerá avisando que, se não excluir for escolhido, todos os pontos de restauração da VM não serão excluídos e estarão disponíveis para realizar uma operação de restauração. A VM será retida em um estado de "stop protection with retain data" com backups pausados e dados de backup retidos para sempre sem nenhuma política de backup efetiva.

   ![Captura de tela do portal Azure, confirmar a exclusão da VM](./media/backup-azure-security-feature-cloud/undelete-vm.png)

   Neste ponto, você também pode restaurar a VM selecionando **Restaurar VM** a partir do ponto de restauração escolhido.  

   ![Captura de tela do portal Azure, opção Restaurar VM](./media/backup-azure-security-feature-cloud/restore-vm.png)

   > [!NOTE]
   > O coletor de lixo executará e limpará os pontos de recuperação expirados somente após o usuário executar a operação **de backup Do Currículo.**

5. Depois que o processo de desexclusão for concluído, o status retornará a "Parar backup com dados retidos" e, em seguida, você poderá escolher **Retomar backup**. A operação **de backup Do Currículo** traz de volta o item de backup no estado ativo, associado a uma política de backup selecionada pelo usuário que define os cronogramas de backup e retenção.

   ![Captura de tela do portal Azure, opção de backup do Currículo](./media/backup-azure-security-feature-cloud/resume-backup.png)

Este fluxograma mostra as diferentes etapas e estados de um item de backup quando o Soft Delete está ativado:

![Ciclo de vida do item de backup excluído suavemente](./media/backup-azure-security-feature-cloud/lifecycle.png)

Para obter mais informações, consulte a seção [Perguntas Frequentes](backup-azure-security-feature-cloud.md#frequently-asked-questions) abaixo.

### <a name="soft-delete-for-vms-using-azure-powershell"></a>Exclusão suave para VMs usando o Azure PowerShell

> [!IMPORTANT]
> A versão Az.RecoveryServices necessária para usar soft-delete usando o Azure PS é min 2.2.0. Use ```Install-Module -Name Az.RecoveryServices -Force``` para obter a versão mais recente.

Como descrito acima para o portal Azure, a seqüência de passos é a mesma ao usar o Azure PowerShell também.

#### <a name="delete-the-backup-item-using-azure-powershell"></a>Exclua o item de backup usando o Azure PowerShell

Exclua o item de backup usando o [cmdlet PS Disable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) PS.

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           DeleteBackupData     Completed            12/5/2019 12:44:15 PM     12/5/2019 12:44:50 PM     0488c3c2-accc-4a91-a1e0-fba09a67d2fb
```

O 'DeleteState' do item de backup mudará de 'NotDeleted' para 'ToBeDeleted'. Os dados de backup serão retidos por 14 dias. Se desejar reverter a operação de exclusão, então desfazer-excluir deve ser realizado.

#### <a name="undoing-the-deletion-operation-using-azure-powershell"></a>Desfazer a operação de exclusão usando o Azure PowerShell

Primeiro, busque o item de backup relevante que está em estado de exclusão suave (ou seja, prestes a ser excluído).

```powershell

Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

Name                                     ContainerType        ContainerUniqueName                      WorkloadType         ProtectionStatus     HealthStatus         DeleteState
----                                     -------------        -------------------                      ------------         ----------------     ------------         -----------
VM;iaasvmcontainerv2;selfhostrg;AppVM1    AzureVM             iaasvmcontainerv2;selfhostrg;AppVM1       AzureVM              Healthy              Passed               ToBeDeleted

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID -Name AppVM1
```

Em seguida, execute a operação de desmarcação usando o cmdlet PS PS do [Undo-AzRecoveryServices.](https://docs.microsoft.com/powershell/module/az.recoveryservices/undo-azrecoveryservicesbackupitemdeletion?view=azps-3.1.0)

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           Undelete             Completed            12/5/2019 12:47:28 PM     12/5/2019 12:47:40 PM     65311982-3755-46b5-8e53-c82ea4f0d2a2
```

O 'DeleteState' do item de backup será revertido para 'NotDeleted'. Mas a proteção ainda está parada. [Retome o backup](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#change-policy-for-backup-items) para reativar a proteção.

### <a name="soft-delete-for-vms-using-rest-api"></a>Exclusão suave para VMs usando API REST

- Exclua os backups usando a API REST como mencionado [aqui](backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data).
- Se o usuário desejar desfazer essas operações de exclusão, consulte as etapas mencionadas [aqui](backup-azure-arm-userestapi-backupazurevms.md#undo-the-stop-protection-and-delete-data).

## <a name="disabling-soft-delete"></a>Desabilitando a exclusão suave

A exclusão suave é habilitada por padrão em cofres recém-criados para proteger os dados de backup de exclusões acidentais ou maliciosas.  Desativar este recurso não é recomendado. A única circunstância em que você deve considerar desativar a exclusão suave é se você está planejando mover seus itens protegidos para um novo cofre, e não pode esperar os 14 dias necessários antes de excluir e reproteger (como em um ambiente de teste.) Apenas o proprietário do cofre pode desativar esse recurso. Se você desativar esse recurso, todas as exclusões futuras de itens protegidos resultarão em remoção imediata, sem a capacidade de restaurar. Os dados de backup que existem em estado soft excluído antes de desativar esse recurso permanecerão em estado de exclusão suave pelo período de 14 dias. Se você deseja excluí-los permanentemente imediatamente, então você precisa desaletá-los e excluí-los novamente para ser excluído permanentemente.

### <a name="disabling-soft-delete-using-azure-portal"></a>Desabilitando o soft delete usando o portal Azure

Para desativar a exclusão suave, siga estas etapas:

1. No portal Azure, vá para o seu cofre e, em seguida, vá para **Propriedades de Configurações** -> **Properties**.
2. No painel propriedades, selecione **Atualização de configurações de** -> **segurança**.  
3. No painel de configurações de segurança, em **Soft Delete,** **selecione Desativar**.

![Desativar a exclusão suave](./media/backup-azure-security-feature-cloud/disable-soft-delete.png)

### <a name="disabling-soft-delete-using-azure-powershell"></a>Desabilitando a exclusão suave usando o Azure PowerShell

> [!IMPORTANT]
> A versão Az.RecoveryServices necessária para usar soft-delete usando o Azure PS é min 2.2.0. Use ```Install-Module -Name Az.RecoveryServices -Force``` para obter a versão mais recente.

Para desativar, use o [cmdlet SET-AzRecoveryServicesVaultBackupProperty](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty?view=azps-3.1.0) PS.

```powershell
Set-AzRecoveryServicesVaultProperty -VaultId $myVaultID -SoftDeleteFeatureState Disable


StorageModelType       :
StorageType            :
StorageTypeState       :
EnhancedSecurityState  : Enabled
SoftDeleteFeatureState : Disabled
```

### <a name="disabling-soft-delete-using-rest-api"></a>Desabilitando a exclusão suave usando a API REST

Para desativar a funcionalidade de exclusão suave usando a API REST, consulte as etapas mencionadas [aqui](use-restapi-update-vault-properties.md#update-soft-delete-state-using-rest-api).

## <a name="permanently-deleting-soft-deleted-backup-items"></a>Excluindo permanentemente itens de backup excluídos suavemente

Os dados de backup em estado deexclusão suave antes de desativar esse recurso permanecerão em estado de exclusão suave. Se você deseja excluí-los permanentemente, então desapague e exclua-os novamente para ser excluído permanentemente.

### <a name="using-azure-portal"></a>Usando o Portal do Azure

Siga estas etapas:

1. Siga as etapas para desativar a [exclusão suave](#disabling-soft-delete).
2. No portal Azure, vá ao seu cofre, vá para **Itens de backup**e escolha a VM soft excluída.

   ![Escolha vm soft excluído](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

3. Selecione a opção **Não excluir**.

   ![Escolha 'Desexcluir'](./media/backup-azure-security-feature-cloud/choose-undelete.png)

4. Uma janela aparecerá. Selecione **'Não excluir'.**

   ![Selecionar ''''''''''](./media/backup-azure-security-feature-cloud/undelete-vm.png)

5. Escolha **Excluir dados de backup** para excluir permanentemente os dados de backup.

   ![Escolha Excluir dados de backup](https://docs.microsoft.com/azure/backup/media/backup-azure-manage-vms/delete-backup-buttom.png)

6. Digite o nome do item de backup para confirmar se deseja excluir os pontos de recuperação.

   ![Digite o nome do item de backup](https://docs.microsoft.com/azure/backup/media/backup-azure-manage-vms/delete-backup-data1.png)

7. Para excluir os dados de backup do item, **selecione Excluir**. Uma mensagem de notificação permite que você saiba que os dados de backup foram excluídos.

### <a name="using-azure-powershell"></a>Usando o PowerShell do Azure

Se os itens foram excluídos antes de o soft-delete ser desativado, então eles estarão em um estado de exclusão suave. Para excluí-los imediatamente, a operação de exclusão precisa ser revertida e executada novamente.

Identifique os itens que estão em estado de exclusão suave.

```powershell

Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

Name                                     ContainerType        ContainerUniqueName                      WorkloadType         ProtectionStatus     HealthStatus         DeleteState
----                                     -------------        -------------------                      ------------         ----------------     ------------         -----------
VM;iaasvmcontainerv2;selfhostrg;AppVM1    AzureVM             iaasvmcontainerv2;selfhostrg;AppVM1       AzureVM              Healthy              Passed               ToBeDeleted

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID -Name AppVM1
```

Em seguida, inverta a operação de exclusão que foi realizada quando o soft-delete foi ativado.

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           Undelete             Completed            12/5/2019 12:47:28 PM     12/5/2019 12:47:40 PM     65311982-3755-46b5-8e53-c82ea4f0d2a2
```

Uma vez que a exclusão suave está agora desativada, a operação de exclusão resultará na remoção imediata dos dados de backup.

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           DeleteBackupData     Completed            12/5/2019 12:44:15 PM     12/5/2019 12:44:50 PM     0488c3c2-accc-4a91-a1e0-fba09a67d2fb
```

### <a name="using-rest-api"></a>Usando a API REST

Se os itens foram excluídos antes de o soft-delete ser desativado, então eles estarão em um estado de exclusão suave. Para excluí-los imediatamente, a operação de exclusão precisa ser revertida e executada novamente.

1. Primeiro, desfaça as operações de exclusão com as etapas [aqui](backup-azure-arm-userestapi-backupazurevms.md#undo-the-stop-protection-and-delete-data)mencionadas .
2. Em seguida, desative a funcionalidade soft-delete usando a API REST usando as etapas mencionadas [aqui](use-restapi-update-vault-properties.md#update-soft-delete-state-using-rest-api).
3. Em seguida, exclua os backups usando a API REST como mencionado [aqui](backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data).

## <a name="encryption"></a>Criptografia

Todos os seus dados de backup são criptografados automaticamente quando armazenados na nuvem usando a criptografia do Azure Storage, o que ajuda você a cumprir seus compromissos de segurança e conformidade. Esses dados em repouso são criptografados usando criptografia AES de 256 bits, uma das cifras de bloco mais fortes disponíveis, e é compatível com FIPS 140-2.

Além da criptografia em repouso, todos os seus dados de backup em trânsito são transferidos por HTTPS. Ele sempre permanece na rede backbone do Azure.

Para obter mais informações, consulte [a criptografia de armazenamento do Azure para obter dados em repouso](https://docs.microsoft.com/azure/storage/common/storage-service-encryption). Consulte o [Azure Backup FAQ](https://docs.microsoft.com/azure/backup/backup-azure-backup-faq#encryption) para responder a quaisquer perguntas que você possa ter sobre criptografia.

### <a name="encryption-of-backup-data-using-platform-managed-keys"></a>Criptografia de dados de backup usando chaves gerenciadas pela plataforma

Por padrão, todos os seus dados são criptografados usando chaves gerenciadas pela plataforma. Você não precisa tomar nenhuma ação explícita do seu lado para habilitar essa criptografia e ela se aplica a todas as cargas de trabalho que estão sendo apoiadas no cofre dos Serviços de Recuperação.

### <a name="encryption-of-backup-data-using-customer-managed-keys"></a>Criptografia de dados de backup usando chaves gerenciadas pelo cliente

Ao fazer backup de suas Máquinas Virtuais do Azure, agora você pode criptografar seus dados usando chaves de propriedade e gerenciadas por você. O Azure Backup permite que você use as chaves RSA armazenadas no Azure Key Vault para criptografar seus backups. A chave de criptografia usada para criptografar backups pode ser diferente da usada para a fonte. Os dados são protegidos usando uma chave de criptografia de dados baseada em AES 256 (DEK), que, por sua vez, é protegida usando suas chaves. Isso lhe dá controle total sobre os dados e as chaves. Para permitir a criptografia, é necessário que o cofre dos Serviços de Recuperação tenha acesso à chave de criptografia no Cofre de Chaves do Azure. Você pode desativar a chave ou revogar o acesso sempre que necessário. No entanto, você deve habilitar a criptografia usando suas chaves antes de tentar proteger quaisquer itens para o cofre.

>[!NOTE]
>Este recurso está atualmente em disponibilidade limitada. Preencha [esta pesquisa](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapURE9TTDRIUEUyNFhNT1lZS1BNVDdZVllHWi4u) e envie AskAzureBackupTeam@microsoft.com um e-mail para nós se desejar criptografar seus dados de backup usando chaves gerenciadas pelo cliente. Observe que a capacidade de usar esse recurso está sujeita à aprovação do serviço de backup do Azure.

### <a name="backup-of-managed-disk-vms-encrypted-using-customer-managed-keys"></a>Backup de VMs de disco gerenciado criptografados usando chaves gerenciadas pelo cliente

O Azure Backup também permite fazer backup das Suas VMs do Azure que usam sua chave para criptografia do lado do servidor. A chave usada para criptografar os discos é armazenada no Cofre de Chaves do Azure e gerenciada por você. A criptografia do lado do servidor usando chaves gerenciadas pelo cliente difere da Criptografia de Disco do Azure, uma vez que o ADE aproveita o BitLocker (para Windows) e o DM-Crypt (para Linux) para executar a criptografia no hóspede, o SSE criptografa dados no serviço de armazenamento, permitindo que você use qualquer Sistema Operacional ou imagens para suas VMs. Consulte a [Criptografia de discos gerenciados com chaves gerenciadas pelo cliente](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption#customer-managed-keys) para obter mais detalhes.

### <a name="backup-of-vms-encrypted-using-ade"></a>Backup de VMs criptografados usando ADE

Com o Azure Backup, você também pode fazer backup de suas máquinas Virtuais do Azure que têm seus discos de sO ou dados criptografados usando o Azure Disk Encryption. O ADE usa o BitLocker para VMs windows e DM-Crypt para VMs Linux para executar criptografia in-guest. Para obter detalhes, consulte [Fazer backup e restaurar máquinas virtuais criptografadas com o Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption).

## <a name="private-endpoints"></a>Pontos finais privados

[!INCLUDE [Private Endpoints](../../includes/backup-private-endpoints.md)]

## <a name="other-security-features"></a>Outros recursos de segurança

### <a name="protection-of-azure-backup-recovery-points"></a>Proteção dos pontos de recuperação do Azure Backup

As contas de armazenamento usadas pelos cofres dos serviços de recuperação são isoladas e não podem ser acessadas pelos usuários para quaisquer fins maliciosos. O acesso só é permitido através de operações de gerenciamento de backup do Azure, como restauração. Essas operações de gerenciamento são controladas através do RBAC (Role-Based Access Control).

Para obter mais informações, consulte [Usar controle de acesso baseado em função para gerenciar pontos de recuperação do Azure Backup](https://docs.microsoft.com/azure/backup/backup-rbac-rs-vault).

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="for-soft-delete"></a>Para excluir suavemente

#### <a name="do-i-need-to-enable-the-soft-delete-feature-on-every-vault"></a>Preciso ativar o recurso de exclusão suave em cada cofre?

Não, é construído e habilitado por padrão para todos os cofres de serviços de recuperação.

#### <a name="can-i-configure-the-number-of-days-for-which-my-data-will-be-retained-in-soft-deleted-state-after-delete-operation-is-complete"></a>Posso configurar o número de dias para os quais meus dados serão retidos em estado de exclusão suave após a operação de exclusão estiver concluída?

Não, é fixado para 14 dias de retenção adicional após a operação de exclusão.

#### <a name="do-i-need-to-pay-the-cost-for-this-additional-14-day-retention"></a>Preciso pagar o custo dessa retenção adicional de 14 dias?

Não, essa retenção adicional de 14 dias vem sem custos como parte da funcionalidade soft-delete.

#### <a name="can-i-perform-a-restore-operation-when-my-data-is-in-soft-delete-state"></a>Posso realizar uma operação de restauração quando meus dados estão em estado de exclusão suave?

Não, você precisa desexcluir o recurso suave excluído para restaurar. A operação undelete trará o recurso de volta à **proteção Stop com estado de retenção de dados** onde você pode restaurar a qualquer momento. O coletor de lixo permanece em pausa neste estado.

#### <a name="will-my-snapshots-follow-the-same-lifecycle-as-my-recovery-points-in-the-vault"></a>Meus instantâneos seguirão o mesmo ciclo de vida dos meus pontos de recuperação no cofre?

Sim.

#### <a name="how-can-i-trigger-the-scheduled-backups-again-for-a-soft-deleted-resource"></a>Como posso acionar os backups programados novamente para um recurso deletado?

A exclusão seguida de operação de currículo protegerá o recurso novamente. A operação de currículo associa uma política de backup para ativar os backups programados com o período de retenção selecionado. Além disso, o coletor de lixo funciona assim que a operação de currículo for concluída. Se você deseja realizar uma restauração a partir de um ponto de recuperação que já passou da data de validade, é aconselhável fazê-lo antes de desencadear a operação de currículo.

#### <a name="can-i-delete-my-vault-if-there-are-soft-deleted-items-in-the-vault"></a>Posso excluir meu cofre se houver itens macios excluídos no cofre?

O cofre dos Serviços de Recuperação não pode ser excluído se houver itens de backup em estado deletado no cofre. Os itens excluídos são excluídos permanentemente 14 dias após a operação de exclusão. Se você não puder esperar por 14 dias, [desabilite a exclusão suave,](#disabling-soft-delete)desexclua os itens excluídos suavemente e exclua-os novamente para serem excluídos permanentemente. Depois de garantir que não há itens protegidos e nenhum item deletado, o cofre pode ser excluído.  

#### <a name="can-i-delete-the-data-earlier-than-the-14-days-soft-delete-period-after-deletion"></a>Posso excluir os dados antes do período de exclusão suave de 14 dias após a exclusão?

Não. Você não pode forçar a exclusão dos itens excluídos suavemente, eles são automaticamente excluídos após 14 dias. Esse recurso de segurança está habilitado para proteger os dados de backup de exclusões acidentais ou maliciosas.  Você deve esperar 14 dias antes de realizar qualquer outra ação na VM.  Os itens suprimidos serão cobrados.  Se você precisar reproteger as VMs marcadas para exclusão suave dentro de 14 dias para um novo cofre, entre em contato com o suporte da Microsoft.

#### <a name="can-soft-delete-operations-be-performed-in-powershell-or-cli"></a>As operações de exclusão suave podem ser realizadas no PowerShell ou na CLI?

As operações de exclusão suave podem ser realizadas usando [o PowerShell](#soft-delete-for-vms-using-azure-powershell). Atualmente, a CLI não é suportada.

#### <a name="is-soft-delete-supported-for-other-cloud-workloads-like-sql-server-in-azure-vms-and-sap-hana-in-azure-vms"></a>O soft delete é suportado para outras cargas de trabalho na nuvem, como o SQL Server em VMs Azure e o SAP HANA em VMs azure?

Não. Atualmente, o soft delete só é suportado para máquinas virtuais Azure.

## <a name="next-steps"></a>Próximas etapas

- Leia sobre [controles de segurança para backup do Azure](backup-security-controls.md).
