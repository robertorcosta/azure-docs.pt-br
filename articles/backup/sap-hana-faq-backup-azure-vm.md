---
title: Perguntas frequentes – Fazer backup dos bancos de dados do SAP HANA em VMs do Azure
description: Neste artigo, descubra respostas a perguntas comuns sobre como fazer backup dos bancos de dados SAP HANA usando o serviço de Backup do Azure.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: 56f98dddb00eb3ffc87eb27da73066de807a1ee1
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83701017"
---
# <a name="frequently-asked-questions--back-up-sap-hana-databases-on-azure-vms"></a>Perguntas frequentes – Fazer backup de bancos de dados SAP HANA em VMs do Azure

Este artigo responde perguntas comuns sobre como fazer backup dos bancos de dados SAP HANA usando o serviço de Backup do Azure.

## <a name="backup"></a>Backup

### <a name="how-many-full-backups-are-supported-per-day"></a>Há suporte para quantos backups completos por dia?

Damos suporte a apenas um backup completo por dia. Você não pode disparar o backup diferencial e o backup completo no mesmo dia.

### <a name="do-successful-backup-jobs-create-alerts"></a>Trabalhos de backup bem-sucedidos criam alertas?

Não. Trabalhos de backup bem-sucedidos não geram alertas. Os alertas são enviados somente para trabalhos de backup com falha. O comportamento detalhado para alertas do portal é documentado [aqui](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor). No entanto, se você tiver interesse em ter alertas mesmo para trabalhos bem-sucedidos, poderá usar o [Azure Monitor](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor).

### <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>Posso ver trabalhos de backup agendados no menu Trabalhos de Backup?

O menu Trabalho de Backup só mostrará trabalhos de backup ad-hoc. Para trabalhos agendados, use o [Azure Monitor](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor).

### <a name="are-future-databases-automatically-added-for-backup"></a>Bancos de dados futuros são adicionados automaticamente ao backup?

Não, no momento não há suporte para isso.

### <a name="if-i-delete-a-database-from-an-instance-what-will-happen-to-the-backups"></a>Se eu excluir um banco de dados de uma instância, o que acontecerá com os backups?

Se um backup for removido de uma instância SAP HANA, os backups do banco de dados ainda serão tentados. Isso implica que o banco de dados excluído começa a ser exibido como não íntegro em **Itens de Backup** e ainda está protegido.
A maneira correta de parar a proteção desse banco de dados é executar **Parar o Backup com os dados de exclusão** nesse banco de dados.

### <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-the-behavior-be"></a>Se eu alterar o nome do banco de dados depois que ele tiver sido protegido, qual será o comportamento?

Um banco de dados renomeado é tratado como um novo. Portanto, o serviço tratará essa situação como se o banco de dados não tivesse sido encontrado e se estivesse com backups com falha. O banco de dados renomeado será exibido como um novo e deverá ser configurado para ter proteção.

### <a name="what-are-the-prerequisites-to-back-up-sap-hana-databases-on-an-azure-vm"></a>Quais são os pré-requisitos para fazer backup de bancos de dados SAP HANA em uma VM do Azure?

Consulte as seções [pré-requisitos](tutorial-backup-sap-hana-db.md#prerequisites) e [O que o script de pré-registro faz](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does).

### <a name="what-permissions-should-be-set-for-azure-to-be-able-to-back-up-sap-hana-databases"></a>Quais permissões devem ser definidas para o Azure poder fazer backup de bancos de dados SAP HANA?

A execução do script de pré-registro define as permissões necessárias para permitir que o Azure faça backup dos bancos de dados SAP HANA. Você pode descobrir o que mais o script pré-registro faz [aqui](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does).

### <a name="will-backups-work-after-migrating-sap-hana-from-sdc-to-mdc"></a>Os backups funcionarão após a migração do SAP HANA de SDC para MDC?

Consulte [esta seção](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot#sdc-to-mdc-upgrade-with-a-change-in-sid) do guia de solução de problemas.

### <a name="can-azure-hana-backup-be-set-up-against-a-virtual-ip-load-balancer-and-not-a-virtual-machine"></a>O Backup HANA do Azure pode ser configurado em um IP virtual (balanceador de carga) e não em uma máquina virtual?

No momento, não temos a capacidade de configurar a solução em um IP virtual sozinho. Precisamos de uma máquina virtual para executar a solução.

### <a name="i-have-a-sap-hana-system-replication-hsr-how-should-i-configure-backup-for-this-setup"></a>Tenho um HSR (Replicação de Sistema do SAP HANA). Como eu devo configurar o backup para essa configuração?

Os nós primário e secundário do HSR serão tratados como duas VMs individuais não relacionadas. Você precisa configurar o backup no nó primário, e quando o failover acontece, você precisa configurar backup no nó secundário (que agora se torna o nó primário). Não há nenhum ' failover ' automático de backup para o outro nó.

### <a name="how-can-i-move-an-on-demand-backup-to-the-local-file-system-instead-of-the-azure-vault"></a>Como posso migrar um backup sob demanda para o sistema de arquivos local em vez do cofre do Azure?

1. Aguarde até que o backup em execução no momento seja concluído no banco de dados desejado (verifique o estúdio para conclusão)
1. Desabilite os backups de log e defina o backup do catálogo como **Filesystem** para o BD desejado usando as seguintes etapas:
1. Clique duas vezes em **SYSTEMDB** -> **configuração** -> **Selecionar Banco de Dados** -> **Filtrar (log)**
    1. Defina enable_auto_log_backup como **não**
    1. Defina log_backup_using_backint como **falso**
1. Faça um backup sob demanda no banco de dados desejado e aguarde a conclusão do backup e do backup do catálogo.
1. Reverta para as configurações anteriores para permitir que os backups fluam para o cofre do Azure:
    1. Defina enable_auto_log_backup como **sim**
    1. Defina log_backup_using_backint como **verdadeiro**

## <a name="restore"></a>Restaurar

### <a name="why-cant-i-see-the-hana-system-i-want-my-database-to-be-restored-to"></a>Por que não consigo ver o sistema HANA para o qual eu quero que meu banco de dados seja restaurado?

Verifique se todos os pré-requisitos para a restauração direcionar a instância SAP HANA são atendidos. Para obter mais informações, confira [Pré-requisitos – Restaurar bancos de dados SAP HANA na VM do Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-restore#prerequisites).

### <a name="why-is-the-overwrite-db-restore-failing-for-my-database"></a>Por que a restauração Substituir BD está falhando para o meu banco de dados?

Verifique se a opção **Forçar Substituição** está selecionada durante a restauração.

### <a name="why-do-i-see-the-source-and-target-systems-for-restore-are-incompatible-error"></a>Por que eu vejo o erro “Os sistemas de origem e destino para restauração são incompatíveis”?

Consulte a Nota [1642148](https://launchpad.support.sap.com/#/notes/1642148) do SAP HANA para ver quais tipos de restauração têm suporte no momento.

## <a name="next-steps"></a>Próximas etapas

Saiba como [fazer backup de bancos de dados do SAP HANA](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database) em execução em VMs do Azure.
