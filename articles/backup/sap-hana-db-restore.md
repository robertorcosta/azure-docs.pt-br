---
title: Restaurar bancos de dados SAP HANA em VMs do Azure
description: Neste artigo, descubra como restaurar os bancos de dados SAP HANA que estão sendo executados em Máquinas Virtuais Azure.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: 999edba61177758ad9039e81e789efcef99ca1de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74287912"
---
# <a name="restore-sap-hana-databases-on-azure-vms"></a>Restaurar bancos de dados SAP HANA em VMs do Azure

Este artigo descreve como restaurar os bancos de dados SAP HANA que estão sendo executados no Azure Virtual Machine (VM), que o serviço de backup do Azure fez backup em um cofre do Azure Backup Recovery Services. As restaurações podem ser usadas para criar cópias dos dados para cenários de dev / teste ou para retornar a um estado anterior.

Para obter mais informações sobre como fazer backup de bancos de dados SAP HANA, consulte [bancos de dados SAP HANA em VMs Azure](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database).

## <a name="restore-to-a-point-in-time-or-to-a-recovery-point"></a>Restaurar a um ponto no tempo ou a um ponto de recuperação

O Backup do Azure pode restaurar bancos de dados SAP HANA que estão em execução em VMs do Azure da seguinte maneira:

* Restauração em uma data ou uma hora específica (com precisão de segundos) usando backups de log. O Backup do Azure determina automaticamente os backups diferenciais completos apropriados e a cadeia de backups de log necessários para a restauração com base na hora selecionada.

* Restauração em um backup completo ou diferencial específico para restauração em um ponto de recuperação específico.

## <a name="prerequisites"></a>Pré-requisitos

Antes de restaurar um banco de dados, observe o seguinte:

* Você pode restaurar o banco de dados somente para uma instância do SAP HANA que esteja na mesma região

* A instância de destino precisa ser registrada no mesmo cofre da origem

* O Azure Backup não pode identificar duas instâncias diferentes do SAP HANA na mesma VM. Portanto, restaurar dados de uma instância para outra na mesma VM não é possível

* Para garantir que a instância SAP HANA de destino esteja pronta para restauração, verifique seu status **de prontidão de backup:**

  * Abra o cofre no qual a instância sap hana alvo é registrada

  * No painel do cofre, em **Getting started**, escolha **Backup**

![Backup no painel do cofre](media/sap-hana-db-restore/getting-started-backup.png)

* Em **Backup,** em O que você **SAP HANA in Azure VM** deseja **fazer backup?**

![Escolha sap HANA em Azure VM](media/sap-hana-db-restore/sap-hana-backup.png)

* Em **Discover DBs em VMs** clique em **Ver detalhes**

![Exibir detalhes](media/sap-hana-db-restore/view-details.png)

* Revise a **prontidão** de backup da VM de destino

![Servidores protegidos](media/sap-hana-db-restore/protected-servers.png)

* Para saber mais sobre os tipos de restauração que o SAP HANA suporta, consulte o SAP HANA Note [1642148](https://launchpad.support.sap.com/#/notes/1642148)

## <a name="restore-a-database"></a>Restaurar um banco de dados

* Abra o cofre no qual o banco de dados SAP HANA a ser restaurado é registrado

* No painel do cofre, em **Itens protegidos,** escolha **Itens de backup**

![Itens de Backup](media/sap-hana-db-restore/backup-items.png)

* Em **Itens de backup,** em **Tipo de gerenciamento de backup** selecione **SAP HANA no Azure VM**

![Tipo de gerenciamento de backup](media/sap-hana-db-restore/backup-management-type.png)

* Selecione o banco de dados a ser restaurado

 ![Banco de dados para restaurar](media/sap-hana-db-restore/database-to-restore.png)

* Examine o menu do banco de dados. Ele fornece informações sobre backup de banco de dados, incluindo:

  * Os pontos de restauração mais antigos e mais recentes

  * O status de backup de log para as últimas 24 e 72 horas para o banco de dados

![Menu de banco de dados](media/sap-hana-db-restore/database-menu.png)

* Selecione **Restaurar DB**

* Em **Configuração de restauração,** especifique onde (ou como) restaurar dados:

  * **Localização alternativa**: Restaure o banco de dados para um local alternativo e mantenha o banco de dados de origem original.

  * **Sobregravação DB**: Restaurar os dados na mesma instância SAP HANA da fonte original. Essa opção substitui o banco de dados original.

![Configuração de restauração](media/sap-hana-db-restore/restore-configuration.png)

### <a name="restore-to-alternate-location"></a>Restauração em uma localização alternativa

* No menu **Configuração de restauração,** em **Where to Restore,** selecione **Local Alternativo**.

![Restauração em uma localização alternativa](media/sap-hana-db-restore/restore-alternate-location.png)

* Selecione o nome de host sap HANA e o nome de instância para o qual você deseja restaurar o banco de dados.
* Verifique se a instância SAP HANA de destino está pronta para ser restaurada, garantindo sua **prontidão de backup.** Consulte a [seção de pré-requisitos](#prerequisites) para obter mais detalhes.
* Na caixa **Nome do Banco de Dados Restaurado**, digite o nome do banco de dados de destino.

> [!NOTE]
> As restaurações do Single Database Container (SDC) devem seguir essas [verificações](backup-azure-sap-hana-database-troubleshoot.md#single-container-database-sdc-restore).

* Se aplicável, selecione **Substituir se o DB com o mesmo nome já existir na instância HANA selecionada**.
* Selecione **OK**.

![Configuração de restauração - tela final](media/sap-hana-db-restore/restore-configuration-last.png)

* Em **Selecionar ponto de restauração,** **selecione Logs (Point in Time)** para [restaurar em um ponto específico no tempo](#restore-to-a-specific-point-in-time). Ou selecione **Diferencial de & Completo** para restaurar um ponto de recuperação [específico](#restore-to-a-specific-recovery-point).

### <a name="restore-and-overwrite"></a>Restaurar e substituir

* No menu **Configuração de restauração,** em **Where to Restore,** selecione **Substituir DB** > **OK**.

![Substituir BD](media/sap-hana-db-restore/overwrite-db.png)

* Em **Selecionar ponto de restauração,** **selecione Logs (Point in Time)** para [restaurar em um ponto específico no tempo](#restore-to-a-specific-point-in-time). Ou selecione **Diferencial de & Completo** para restaurar um ponto de recuperação [específico](#restore-to-a-specific-recovery-point).

### <a name="restore-to-a-specific-point-in-time"></a>Restaurar a um ponto específico no tempo

Se você tiver selecionado **Logs (Pontual)** como o tipo de restauração, faça o seguinte:

* Selecione um ponto de recuperação no gráfico de log e selecione **OK** para escolher o ponto de restauração.

![Ponto de restauração](media/sap-hana-db-restore/restore-point.png)

* No menu **Restaurar**, selecione **Restaurar** para iniciar o trabalho de restauração.

![Selecione restaurar](media/sap-hana-db-restore/restore-restore.png)

* Acompanhe o progresso da restauração na área **Notificações** ou rastreie-a selecionando **Restaurar trabalhos** no menu do banco de dados.

![Restauração acionada com sucesso](media/sap-hana-db-restore/restore-triggered.png)

### <a name="restore-to-a-specific-recovery-point"></a>Restaurar para um ponto de recuperação específico

Se você tiver selecionado **Completo e Diferencial** como o tipo de restauração, faça o seguinte:

* Selecione um ponto de recuperação da lista e selecione **OK** para escolher o ponto de restauração.

![Restaurar ponto de recuperação específico](media/sap-hana-db-restore/specific-recovery-point.png)

* No menu **Restaurar**, selecione **Restaurar** para iniciar o trabalho de restauração.

![Comece a restaurar o trabalho](media/sap-hana-db-restore/restore-specific.png)

* Acompanhe o progresso da restauração na área **Notificações** ou rastreie-a selecionando **Restaurar trabalhos** no menu do banco de dados.

![Progresso da restauração](media/sap-hana-db-restore/restore-progress.png)

> [!NOTE]
> Em Restaurações de MDC (Multiple Database Database Container, contêiner de banco de dados) depois que o DB do sistema é restaurado para uma instância de destino, é necessário executar o script de pré-registro novamente. Só então as restaurações subseqüentes do INQUILINO DB terão sucesso. Para saber mais, consulte [Solução de problemas – Restauração do MDC](backup-azure-sap-hana-database-troubleshoot.md#multiple-container-database-mdc-restore).

## <a name="next-steps"></a>Próximas etapas

* [Saiba como](sap-hana-db-manage.md) gerenciar bancos de dados SAP HANA com backup usando o Azure Backup
