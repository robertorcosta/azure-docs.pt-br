---
title: Perguntas frequentes sobre fazer backup de Arquivos do Azure
description: Neste artigo, descubra respostas para perguntas comuns sobre como proteger seus compartilhamentos de arquivo do Azure com o serviço de Backup do Azure.
ms.date: 04/22/2020
ms.topic: conceptual
ms.openlocfilehash: d7b19fd11e6784a188a18f6a613eef5ff4f77764
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82105634"
---
# <a name="questions-about-backing-up-azure-files"></a>Perguntas sobre como fazer backup de Arquivos do Azure

Este artigo responde perguntas frequentes sobre como fazer backup de Arquivos do Azure. Em algumas das respostas, há links para artigos com informações abrangentes. Você também pode postar perguntas sobre o serviço de Backup do Azure no [fórum de discussão](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

Para verificar rapidamente as seções neste artigo, use os links à direita, em **Neste artigo**.

## <a name="configuring-the-backup-job-for-azure-files"></a>Configurando o trabalho de backup para Arquivos do Azure

### <a name="why-cant-i-see-some-of-my-storage-accounts-that-i-want-to-protect-which-contain-valid-azure-file-shares"></a>Por que não consigo ver algumas das minhas contas de armazenamento que desejo proteger, que contêm compartilhamentos de arquivos válidos do Azure?

Consulte a [matriz de suporte para backup de compartilhamentos de arquivos do Azure](azure-file-share-support-matrix.md) para garantir que a conta de armazenamento pertença a um dos tipos de conta de armazenamento com suporte. Também é possível que a conta de armazenamento que você está procurando já esteja protegida ou registrada em outro cofre. [Cancele o registro da conta de armazenamento](manage-afs-backup.md#unregister-a-storage-account) do cofre para descobrir a conta de armazenamento em outros cofres para proteção.

### <a name="why-cant-i-see-some-of-my-azure-file-shares-in-the-storage-account-when-im-trying-to-configure-backup"></a>Por que não vejo alguns dos meus compartilhamentos de arquivos do Azure na conta de armazenamento quando estou tentando configurar o backup?

Verifique se o compartilhamento de arquivos do Azure já está protegido no mesmo cofre dos Serviços de Recuperação ou se foi excluído recentemente.

### <a name="can-i-protect-file-shares-connected-to-a-sync-group-in-azure-files-sync"></a>Posso proteger compartilhamentos de arquivos conectados a um grupo de sincronização na Sincronização de arquivos do Azure?

Sim. A proteção dos compartilhamentos de arquivos do Azure conectados a grupos de sincronização está habilitada.

### <a name="when-trying-to-back-up-file-shares-i-clicked-on-a-storage-account-for-discovering-the-file-shares-in-it-however-i-didnt-protect-them-how-do-i-protect-these-file-shares-with-any-other-vault"></a>Ao tentar fazer backup de compartilhamentos de arquivos, cliquei em uma Conta de Armazenamento para ver os compartilhamentos de arquivos que estão nela. No entanto, não as protejai. Como fazer proteger esses compartilhamentos de arquivos com qualquer outro cofre?

Ao tentar fazer backup, selecionar uma conta de armazenamento para descobrir os compartilhamentos de arquivos dentro dele registra a conta de armazenamento no cofre do qual isso é feito. Se você optar por proteger os compartilhamentos de arquivos com um cofre diferente, [cancele o registro](manage-afs-backup.md#unregister-a-storage-account) da conta de armazenamento escolhida deste cofre.

### <a name="can-i-change-the-vault-to-which-i-back-up-my-file-shares"></a>É possível alterar o Cofre em que faço backup dos meus compartilhamentos de arquivos?

Sim. No entanto, você precisará [interromper a proteção no compartilhamento de arquivos](manage-afs-backup.md#stop-protection-on-a-file-share) do cofre conectado, [cancelar o registro](manage-afs-backup.md#unregister-a-storage-account) dessa conta de armazenamento e, em seguida, protegê-la de um cofre diferente.

### <a name="how-many-azure-file-shares-can-i-protect-in-a-vault"></a>Quantos compartilhamentos de arquivos do Azure posso proteger em um cofre?

Você pode proteger compartilhamentos de arquivos do Azure de até 50 contas de armazenamento por cofre. Também é possível proteger até 200 compartilhamentos de arquivos do Azure em um único cofre.

### <a name="can-i-protect-two-different-file-shares-from-the-same-storage-account-to-different-vaults"></a>Posso proteger dois compartilhamentos de arquivo diferentes da mesma conta de armazenamento em cofres diferentes?

Não. Os compartilhamentos de arquivos em uma mesma Conta de Armazenamento só podem ser protegidos pelo mesmo Cofre.

## <a name="backup"></a>Backup

### <a name="what-should-i-do-if-my-backups-start-failing-due-to-the-maximum-limit-reached-error"></a>O que devo fazer se meus backups começarem a falhar devido ao erro de limite máximo atingido?

Você pode ter até 200 instantâneos para um compartilhamento de arquivos a qualquer hora. O limite inclui instantâneos tirados pelo Backup do Azure conforme definido pela sua política. Se os backups começarem a falhar depois de atingir o limite, exclua os instantâneos sob demanda para obter backups futuros bem-sucedidos.

## <a name="restore"></a>Restaurar

### <a name="can-i-recover-from-a-deleted-azure-file-share"></a>Posso recuperar a partir de um compartilhamento de arquivos do Azure excluído?

Quando um compartilhamento de arquivos do Azure é excluído, você é mostrado na lista de backups que serão excluídos e uma confirmação é solicitada. No momento, um compartilhamento de arquivos do Azure excluído não pode ser restaurado.

### <a name="can-i-restore-from-backups-if-i-stopped-protection-on-an-azure-file-share"></a>Posso restaurar a partir de backups se parar a proteção em um compartilhamento de arquivos do Azure?

Sim. Caso tenha escolhido **Reter Dados de Backup**, ao parar a proteção, você poderá restaurar a partir de todos os pontos de restauração existentes.

### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>O que acontecerá se eu cancelar um trabalho de restauração em andamento?

Se um trabalho de restauração em andamento for cancelado, o processo de restauração será interrompido e todos os arquivos restaurados antes do cancelamento permanecerão no destino configurado (local original ou alternativo) sem nenhuma reversão.

## <a name="manage-backup"></a>Gerenciar backups

### <a name="can-i-use-powershell-to-configuremanagerestore-backups-of-azure-file-shares"></a>Posso usar o PowerShell para configurar/gerenciar/restaurar backups de compartilhamentos de Arquivos do Azure?

Sim. Consulte a documentação detalhada [aqui](backup-azure-afs-automation.md).

### <a name="can-i-access-the-snapshots-taken-by-azure-backups-and-mount-them"></a>Posso acessar os instantâneos tirados pelos backups do Azure e montá-los?

Todos os instantâneos feitos pelo backup do Azure podem ser acessados exibindo instantâneos no portal, no PowerShell ou na CLI. Para saber mais sobre os instantâneos de compartilhamento de arquivos do Azure, consulte [visão geral de instantâneos de compartilhamento para arquivos do Azure](../storage/files/storage-snapshots-files.md).

### <a name="what-is-the-maximum-retention-i-can-configure-for-backups"></a>Qual é a retenção máxima que posso configurar para backups?

Consulte a [matriz de suporte](azure-file-share-support-matrix.md) para obter detalhes sobre a retenção máxima. O backup do Azure faz um cálculo em tempo real do número de instantâneos quando você insere os valores de retenção ao configurar a política de backup. Assim que o número de instantâneos correspondentes aos valores de retenção definidos exceder 200, o portal mostrará um aviso solicitando que você ajuste os valores de retenção. Isso é para que você não exceda o limite de número máximo de instantâneos com suporte dos arquivos do Azure para qualquer compartilhamento de arquivos em qualquer ponto no tempo.

### <a name="what-happens-when-i-change-the-backup-policy-for-an-azure-file-share"></a>O que acontece quando altero a política de backup de um compartilhamento de arquivos do Azure?

Quando uma nova política for aplicada em compartilhamento de arquivos, a agenda e a retenção da nova política serão seguidas. Se a retenção for estendida, os pontos de recuperação existentes serão marcados para mantê-los de acordo com a nova política. Se a retenção for reduzida, eles são marcados para remoção no próximo trabalho de limpeza e excluídos.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre outras áreas do Backup do Azure, confira algumas destas outras perguntas frequentes sobre backups:

- [Perguntas Frequentes sobre o cofre dos Serviços de Recuperação](backup-azure-backup-faq.md)
- [Perguntas frequentes sobre o backup de VM do Azure](backup-azure-vm-backup-faq.md)
- [Perguntas frequentes sobre o agente de Backup do Azure](backup-azure-file-folder-backup-faq.md)
