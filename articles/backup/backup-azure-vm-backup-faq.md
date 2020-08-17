---
title: Perguntas frequentes-fazendo backup de VMs do Azure
description: Neste artigo, descubra respostas para perguntas comuns sobre como fazer backup de VMs do Azure com o serviço de backup do Azure.
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: 03e2f004fa54ee235eabc49afd6abd7532a6ee44
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/17/2020
ms.locfileid: "88262765"
---
# <a name="frequently-asked-questions-back-up-azure-vms"></a>Perguntas frequentes-fazer backup de VMs do Azure

Este artigo responde a perguntas comuns sobre o backup de VMs do Azure com o serviço de [backup do Azure](./backup-overview.md) .

## <a name="backup"></a>Backup

### <a name="which-vm-images-can-be-enabled-for-backup-when-i-create-them"></a>Quais imagens de VM podem ser habilitadas para backup quando eu as crio?

Ao criar uma VM, você pode habilitar o backup para VMs que executam [sistemas operacionais com suporte](backup-support-matrix-iaas.md#supported-backup-actions).

### <a name="is-the-backup-cost-included-in-the-vm-cost"></a>O custo de backup está incluído no custo da VM?

Não. Os custos de backup são separados dos custos de uma VM. Saiba mais sobre os [preços do backup do Azure](https://azure.microsoft.com/pricing/details/backup/).

### <a name="which-permissions-are-required-to-enable-backup-for-a-vm"></a>Quais permissões são necessárias para habilitar o backup de uma VM?

Se você for um colaborador de VM, poderá habilitar o backup na VM. Se você estiver usando uma função personalizada, precisará das seguintes permissões para habilitar o backup na VM:

- Microsoft.RecoveryServices/Vaults/write
- Microsoft.RecoveryServices/Vaults/read
- Microsoft.RecoveryServices/locations/*
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write
- Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write
- Microsoft.RecoveryServices/Vaults/backupPolicies/read
- Microsoft.RecoveryServices/Vaults/backupPolicies/write

Se o cofre e a VM dos serviços de recuperação tiverem grupos de recursos diferentes, verifique se você tem permissões de gravação no grupo de recursos para o cofre dos serviços de recuperação.  

### <a name="does-an-on-demand-backup-job-use-the-same-retention-schedule-as-scheduled-backups"></a>O trabalho de backup sob demanda usa o mesmo agendamento de retenção que os backups agendados?

Não. Especifique o período de retenção para um trabalho de backup sob demanda. Por padrão, ele é retido por 30 dias quando disparado de um portal.

### <a name="i-recently-enabled-azure-disk-encryption-on-some-vms-will-my-backups-continue-to-work"></a>Eu recentemente habilitei a Criptografia de Disco do Azure em algumas VMs. Meus backups continuarão a funcionar?

Forneça permissões para o backup do Azure acessar o Key Vault. Especifique as permissões no PowerShell, conforme descrito na seção **Habilitar backup** na documentação do [PowerShell do Backup do Azure](backup-azure-vms-automation.md).

### <a name="i-migrated-vm-disks-to-managed-disks-will-my-backups-continue-to-work"></a>Migrei os discos de uma VM para os discos gerenciados. Meus backups continuarão a funcionar?

Sim, os backups funcionam perfeitamente. Não é necessário reconfigurar nada.

### <a name="why-cant-i-see-my-vm-in-the-configure-backup-wizard"></a>Por que não consigo ver minha VM no assistente de Backup de Configuração?

O assistente lista apenas as VMs na mesma região do cofre e que ainda não estão sendo submetidas a backup.

### <a name="my-vm-is-shut-down-will-an-on-demand-or-a-scheduled-backup-work"></a>Minha VM está desligada. Será uma sob demanda ou um trabalho de backup agendado?

Sim. Os backups são executados quando um computador é desligado. O ponto de recuperação é marcado como consistente com a falha.

### <a name="can-i-cancel-an-in-progress-backup-job"></a>Posso cancelar um trabalho de backup em andamento?

Sim. Você pode cancelar o trabalho de backup em um estado de **captura de instantâneo** . Você não poderá cancelar um trabalho se a transferência de dados do instantâneo estiver em andamento.

### <a name="i-enabled-a-lock-on-the-resource-group-created-by-azure-backup-service-for-example-azurebackuprg_geo_number-will-my-backups-continue-to-work"></a>Habilitei um bloqueio no grupo de recursos criado pelo serviço de backup do Azure (por exemplo, `AzureBackupRG_<geo>_<number>` ). Meus backups continuarão a funcionar?

Se você bloquear o grupo de recursos criado pelo serviço de backup do Azure, os backups começarão a falhar, pois há um limite máximo de 18 pontos de restauração.

Remova o bloqueio e desmarque a coleção de pontos de restauração desse grupo de recursos para que os backups futuros sejam bem-sucedidos. [Siga estas etapas](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#clean-up-restore-point-collection-from-azure-portal) para remover a coleção de pontos de restauração.

### <a name="does-azure-backup-support-standard-ssd-managed-disks"></a>O backup do Azure dá suporte a discos gerenciados por SSD padrão?

Sim, o backup do Azure dá suporte a [discos gerenciados SSD padrão](https://azure.microsoft.com/blog/announcing-general-availability-of-standard-ssd-disks-for-azure-virtual-machine-workloads/).

### <a name="can-we-back-up-a-vm-with-a-write-accelerator-wa-enabled-disk"></a>Podemos fazer backup de uma VM com um disco ativado pelo Acelerador de Gravação?

Os instantâneos não podem ser criados no disco habilitado pelo Acelerador de Gravação. No entanto, o serviço de Backup do Azure pode excluir o disco habilitado pelo Acelerador de Gravação do backup.

### <a name="i-have-a-vm-with-write-accelerator-wa-disks-and-sap-hana-installed-how-do-i-back-up"></a>Tenho uma VM com discos do Acelerador de Gravação e do SAP HANA instalados. Como fazer backup?

O Backup do Azure não pode fazer backup do disco habilitado pelo Acelerador de Gravação, mas pode excluí-lo do backup. No entanto, o backup não fornecerá a consistência do banco de dados porque não são feitos backups de informações do disco habilitado pelo Acelerador de Gravação. Você pode fazer backup de discos com essa configuração se desejar o backup em disco do sistema operacional e o backup dos discos que não são habilitados pelo Acelerador de Gravação.

O backup do Azure fornece uma solução de backup de streaming para bancos de dados SAP HANA com um RPO de 15 minutos. BACKINT certificado pela SAP para fornecer um suporte de backup nativo, aproveitando as APIs nativas do SAP HANA. Saiba mais [sobre como fazer backup de bancos de dados SAP Hana em VMs do Azure](./sap-hana-db-about.md).

### <a name="what-is-the-maximum-delay-i-can-expect-in-backup-start-time-from-the-scheduled-backup-time-i-have-set-in-my-vm-backup-policy"></a>Qual é o atraso máximo que posso esperar na hora de início do backup a partir do horário agendado, defini em minha política de backup da VM?

O backup agendado será disparado dentro de 2 horas da hora do backup agendado. Por exemplo, se as VMs 100 tiverem a hora de início do backup agendada às 2:00 AM, por 4:00 A.M. às mais recentes, todas as 100 serão o trabalho de backup em andamento. Se os backups agendados tiverem sido pausados devido a uma interrupção e retomados ou tentarem novamente, o backup poderá ser iniciado fora dessa janela de duas horas agendada.

### <a name="what-is-the-minimum-allowed-retention-range-for-a-daily-backup-point"></a>Qual é o período de retenção mínimo permitido para um ponto de backup diário?

A política de backup de máquina virtual do Azure dá suporte a um período de retenção mínimo de sete dias até 9999 dias. Qualquer modificação em uma política de backup de VM existente com menos de sete dias exigirá uma atualização para atender ao período de retenção mínimo de sete dias.

### <a name="what-happens-if-i-change-the-case-of-the-name-of-my-vm-or-my-vm-resource-group"></a>O que acontece se eu alterar o caso do nome da minha VM ou meu grupo de recursos de VM?

Se você alterar o caso (para superior ou inferior) do seu grupo de recursos de VM ou VM, o caso do nome do item de backup não será alterado. No entanto, esse é o comportamento do backup do Azure esperado. A alteração de caso não aparecerá no item de backup, mas será atualizada no back-end.

### <a name="can-i-back-up-or-restore-selective-disks-attached-to-a-vm"></a>Posso fazer backup ou restaurar discos seletivos anexados a uma VM?

O Backup do Azure agora dá suporte a backup e restauração de disco seletivo usando a solução de backup de Máquina Virtual do Azure. Para obter mais informações, consulte [backup e restauração de disco seletivo para VMs do Azure](selective-disk-backup-restore.md).

### <a name="are-managed-identities-preserved-if-a-tenant-change-occurs-during-backup"></a>As identidades gerenciadas serão preservadas se uma alteração de locatário ocorrer durante o backup?

Se ocorrerem [alterações de locatário](https://docs.microsoft.com/azure/devops/organizations/accounts/change-azure-ad-connection) , será necessário desabilitar e reabilitar as [identidades gerenciadas](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) para fazer os backups funcionarem novamente.

## <a name="restore"></a>Restaurar

### <a name="how-do-i-decide-whether-to-restore-disks-only-or-a-full-vm"></a>Como faço para decidir se quero restaurar somente a discos ou uma VM completa?

Pense na restauração da VM como uma opção de criação rápida para uma VM do Azure. Essa opção altera os nomes de disco, contêineres usados pelos discos, endereços IP públicos e nomes de interface de rede. A alteração mantém recursos exclusivos quando uma VM é criada. A VM não é adicionada ao conjunto de disponibilidade.

Você pode usar a opção de disco de restauração se você quer:

- Personalizar a VM que é criada. Por exemplo, altere o tamanho.
- Adicione definições de configuração que não estavam lá no momento do backup.
- Controlar a convenção de nomenclatura para os recursos que são criados.
- Adicionar a VM a um grupo de disponibilidade.
- Adicione qualquer outra configuração que deva ser configurada usando o PowerShell ou um modelo.

### <a name="can-i-restore-backups-of-unmanaged-vm-disks-after-i-upgrade-to-managed-disks"></a>Posso restaurar backups de discos de VMs não gerenciadas após o upgrade dos meus discos para discos gerenciados?

Sim, você pode usar os backups feitos antes de migrar os discos de não gerenciados para gerenciados.

### <a name="how-do-i-restore-a-vm-to-a-restore-point-before-the-vm-was-migrated-to-managed-disks"></a>Como faço para restaurar uma VM em um ponto de restauração antes que a VM seja migrada para os discos gerenciados?

O processo de restauração permanece o mesmo. Se o ponto de recuperação for de um ponto no tempo em que a VM tinha discos não gerenciados, você poderá [restaurar discos como não gerenciados](tutorial-restore-disk.md#unmanaged-disks-restore). Se a VM tiver discos gerenciados, você poderá [restaurar discos como discos gerenciados](tutorial-restore-disk.md#managed-disk-restore). Em seguida, você pode [criar uma VM a partir desses discos](tutorial-restore-disk.md#create-a-vm-from-the-restored-disk).

[Saiba mais](backup-azure-vms-automation.md#restore-an-azure-vm) sobre como fazer isso no PowerShell.

### <a name="if-the-restore-fails-to-create-the-vm-what-happens-to-the-disks-included-in-the-restore"></a>Se a restauração não conseguir criar a VM, o que acontecerá com os discos incluídos na restauração?

No caso de uma restauração de VM gerenciada, mesmo que a criação da VM falhe, os discos ainda serão restaurados.

### <a name="can-i-restore-a-vm-thats-been-deleted"></a>Posso restaurar uma VM que foi excluída?

Sim. Mesmo se você excluir a VM, poderá ir para o item de backup correspondente no cofre e restaurar de um ponto de recuperação.

### <a name="how-do-i-restore-a-vm-to-the-same-availability-sets"></a>Como fazer restaurar uma VM para os mesmos conjuntos de disponibilidade?

Para as VMs do Azure de disco gerenciado, a restauração para os conjuntos de disponibilidade é habilitada fornecendo uma opção no modelo durante a restauração como discos gerenciados. Este modelo tem o parâmetro de entrada chamado **Conjuntos de disponibilidade**.

### <a name="how-do-we-get-faster-restore-performances"></a>Como conseguimos desempenhos de restauração mais rápidos?

A capacidade de [restauração instantânea](backup-instant-restore-capability.md) ajuda com backups e restaurações instantâneas mais rápidos a partir dos instantâneos.

### <a name="what-happens-when-we-change-the-key-vault-settings-for-the-encrypted-vm"></a>O que acontece quando alteramos as configurações do cofre de chaves para a VM criptografada?

Depois de alterar as configurações do cofre de chaves para a VM criptografada, os backups continuarão a funcionar com o novo conjunto de detalhes. No entanto, após a restauração de um ponto de recuperação antes da alteração, você precisará restaurar os segredos em um cofre de chaves antes de poder criar a VM a partir dele. Para obter mais informações, consulte este [artigo](./backup-azure-restore-key-secret.md).

Operações como segredo/chave de sobreposição não exigem essa etapa e o mesmo cofre de chaves pode ser usado após a restauração.

### <a name="can-i-access-the-vm-once-restored-due-to-a-vm-having-broken-relationship-with-domain-controller"></a>Posso acessar a VM uma vez restaurada devido a uma VM que está tendo uma relação quebrada com o controlador de domínio?

Sim, você acessa a VM uma vez restaurada devido a uma VM que tem uma relação quebrada com o controlador de domínio. Para obter mais informações, confira este [artigo](./backup-azure-arm-restore-vms.md#post-restore-steps)

## <a name="manage-vm-backups"></a>Gerenciar backups de VM

### <a name="what-happens-if-i-modify-a-backup-policy"></a>O que acontecerá se eu alterar minha política de backup?

O backup da VM é feito usando as configurações de retenção e agendamento na política nova ou modificada.

- Se a retenção for estendida, os pontos de recuperação existentes serão marcados e mantidos de acordo com a nova política.
- Se a retenção for reduzida, eles serão marcados para remoção no próximo trabalho de limpeza e subsequentemente excluídos.

### <a name="how-do-i-move-a-vm-backed-up-by-azure-backup-to-a-different-resource-group"></a>Como faço para mover uma VM com backup feito pelo Backup do Azure para um grupo de recursos diferente?

1. Interrompa temporariamente o backup e retenha os dados de backup.
2. Para mover as máquinas virtuais configuradas com o backup do Azure, execute as seguintes etapas:

   1. Localize o local da sua máquina virtual.
   2. Localize um grupo de recursos com o seguinte padrão de nomenclatura: `AzureBackupRG_<location of your VM>_1` . Por exemplo, *AzureBackupRG_westus2_1*
   3. Na portal do Azure, marque **Mostrar tipos ocultos**.
   4. Localize o recurso com o tipo **Microsoft. Compute/restorePointCollections** que tem o padrão de nomenclatura `AzureBackup_<name of your VM that you're trying to move>_###########` .
   5. Exclua este recurso. Esta operação exclui somente os pontos de recuperação instantânea, não os dados de backup no cofre.
   6. Depois que a operação de exclusão for concluída, você poderá mover sua máquina virtual.

3. Mova a VM para o grupo de recursos de destino.
4. Retome o backup.

Você pode restaurar a VM a partir dos pontos de restauração disponíveis que foram criados antes da operação de migração.

### <a name="what-happens-after-i-move-a-vm-to-a-different-resource-group"></a>O que acontece depois de mover uma VM para um grupo de recursos diferente?

Depois que uma VM é movida para um grupo de recursos diferente, ela é uma nova VM no que diz respeito ao backup do Azure.

Depois de mover a VM para um novo grupo de recursos, você pode proteger novamente a VM no mesmo cofre ou em um cofre diferente. Como essa é uma nova VM para o backup do Azure, você será cobrado separadamente.

Os pontos de restauração da VM antiga estarão disponíveis para restauração, se necessário. Se você não precisar desses dados de backup, poderá parar de proteger sua VM antiga com excluir dados.

### <a name="is-there-a-limit-on-number-of-vms-that-can-beassociated-with-the-same-backup-policy"></a>Há um limite para o número de VMs que podem ser associadas à mesma política de backup?

Sim, há um limite de 100 VMs que podem ser associadas à mesma política de backup do Portal. Recomendamos que, para mais de 100 VMs, crie várias políticas de backup com o mesmo agendamento ou agendamento diferente.

### <a name="how-can-i-view-the-retention-settings-for-my-backups"></a>Como exibir as configurações de retenção para meus backups?

No momento, você pode exibir as configurações de retenção em um nível de item de backup (VM) com base na política de backup atribuída à VM. 

Uma maneira de exibir as configurações de retenção para seus backups é navegar até o [painel](https://docs.microsoft.com/azure/backup/backup-azure-manage-vms#view-vms-on-the-dashboard) do item de backup para sua VM, na portal do Azure. Clicar no link para sua política de backup ajuda a exibir a duração da retenção de todos os pontos de retenção diários, semanais, mensais e anuais associados à VM.

Você também pode usar o [Gerenciador de backup](https://docs.microsoft.com/azure/backup/monitor-azure-backup-with-backup-explorer) para exibir as configurações de retenção de todas as suas VMs em um único painel de vidro. Navegue até o Gerenciador de backup em qualquer cofre dos serviços de recuperação, vá para a guia **itens de backup** e selecione o modo de exibição avançado para ver informações detalhadas de retenção para cada VM.  