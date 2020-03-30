---
title: Perguntas frequentes – Fazer backup dos bancos de dados do SAP HANA em VMs do Azure
description: Neste artigo, descubra respostas a perguntas comuns sobre o backup de bancos de dados SAP HANA usando o serviço de backup do Azure.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: a46c4d6cccc00452a56567880400ef5779e6aed4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80155385"
---
# <a name="frequently-asked-questions--back-up-sap-hana-databases-on-azure-vms"></a>Perguntas frequentes – Backup de bancos de dados SAP HANA em VMs Azure

Este artigo responde a perguntas comuns sobre o backup de bancos de dados SAP HANA usando o serviço de backup do Azure.

## <a name="backup"></a>Backup

### <a name="how-many-full-backups-are-supported-per-day"></a>Quantos backups completos são suportados por dia?

Nós suportamos apenas um backup completo por dia. Você não pode ter backup diferencial e backup completo acionados no mesmo dia.

### <a name="do-successful-backup-jobs-create-alerts"></a>Trabalhos de backup bem-sucedidos criam alertas?

Não. Trabalhos de backup bem-sucedidos não geram alertas. Os alertas são enviados somente para trabalhos de backup com falha. O comportamento detalhado para alertas de portais é documentado [aqui.](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor) No entanto, se você estiver interessado em ter alertas mesmo para trabalhos bem-sucedidos, você pode usar [o Azure Monitor](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor).

### <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>Posso ver trabalhos de backup programados no menu Backup Jobs?

O menu Backup Job só mostrará trabalhos de backup ad-hoc. Para trabalhos programados, use [o Monitor Azure](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor).

### <a name="are-future-databases-automatically-added-for-backup"></a>Bancos de dados futuros são adicionados automaticamente ao backup?

Não, isso não é suportado no momento.

### <a name="if-i-delete-a-database-from-an-instance-what-will-happen-to-the-backups"></a>Se eu excluir um banco de dados de uma instância, o que acontecerá com os backups?

Se um banco de dados for retirado de uma instância sap HANA, os backups do banco de dados ainda serão tentados. Isso implica que o banco de dados excluído começa a aparecer como insalubre em **Itens de Backup** e ainda está protegido.
A maneira correta de parar de proteger esse banco de dados é realizar **o Stop Backup com dados de exclusão** neste banco de dados.

### <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-the-behavior-be"></a>Se eu mudar o nome do banco de dados depois de protegido, qual será o comportamento?

Um banco de dados renomeado é tratado como um novo banco de dados. Portanto, o serviço tratará essa situação como se o banco de dados não fosse encontrado e com falha nos backups. O banco de dados renomeado aparecerá como um novo banco de dados e deve ser configurado para proteção.

### <a name="what-are-the-prerequisites-to-back-up-sap-hana-databases-on-an-azure-vm"></a>Quais são os pré-requisitos para fazer backup dos bancos de dados SAP HANA em uma VM Azure?

Consulte os [pré-requisitos](tutorial-backup-sap-hana-db.md#prerequisites) e [o que o script de pré-registro faz](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) seções.

### <a name="what-permissions-should-be-set-for-azure-to-be-able-to-back-up-sap-hana-databases"></a>Quais permissões devem ser definidas para que o Azure possa fazer backup dos bancos de dados SAP HANA?

A execução do script de pré-registro define as permissões necessárias para permitir que o Azure faz backup dos bancos de dados SAP HANA. Você pode encontrar mais o que o script de pré-registro faz [aqui.](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)

### <a name="will-backups-work-after-migrating-sap-hana-from-10-to-20"></a>Os backups funcionarão depois de migrar o SAP HANA de 1,0 para 2,0?

Consulte [esta seção](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot#upgrading-from-sap-hana-10-to-20) do guia de solução de problemas.

### <a name="can-azure-hana-backup-be-set-up-against-a-virtual-ip-load-balancer-and-not-a-virtual-machine"></a>O Backup do Azure HANA pode ser configurado contra um IP virtual (balanceador de carga) e não contra uma máquina virtual?

Atualmente, não temos a capacidade de configurar a solução contra um IP virtual sozinho. Precisamos de uma máquina virtual para executar a solução.

### <a name="i-have-a-sap-hana-system-replication-hsr-how-should-i-configure-backup-for-this-setup"></a>Tenho um SAP HANA System Replication (HSR), como devo configurar o backup para esta configuração?

Os nódulos primários e secundários do HSR serão tratados como duas VMs individuais, não relacionadas. Você precisa configurar o backup no nó principal e quando a falha acontecer, você precisa configurar o backup no nó secundário (que agora se torna o nó principal). Não há 'fail-over' automático de backup para o outro nó.

## <a name="restore"></a>Restaurar

### <a name="why-cant-i-see-the-hana-system-i-want-my-database-to-be-restored-to"></a>Por que não posso ver o sistema HANA para o qual quero que meu banco de dados seja restaurado?

Verifique se todos os pré-requisitos para a restauração para atingir a instância SAP HANA são cumpridos. Para obter mais informações, consulte [Pré-requisitos - Restaurar bancos de dados SAP HANA no Azure VM](https://docs.microsoft.com/azure/backup/sap-hana-db-restore#prerequisites).

### <a name="why-is-the-overwrite-db-restore-failing-for-my-database"></a>Por que a restauração do DB de sobregravação está falhando no meu banco de dados?

Certifique-se de que a opção **'Sobressapor** a força' esteja selecionada durante a restauração.

### <a name="why-do-i-see-the-source-and-target-systems-for-restore-are-incompatible-error"></a>Por que vejo que o erro "Os sistemas de origem e destino para restauração são incompatíveis"?

Consulte o SAP HANA Note [1642148](https://launchpad.support.sap.com/#/notes/1642148) para ver quais tipos de restauração são suportados atualmente.

## <a name="next-steps"></a>Próximas etapas

Saiba como [fazer backup dos bancos de dados SAP HANA](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database) em execução em VMs do Azure.
