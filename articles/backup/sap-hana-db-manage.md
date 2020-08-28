---
title: Gerenciar bancos de dados do SAP HANA com backup em VMs do Azure
description: Neste artigo, aprenda tarefas comuns para gerenciar e monitorar bancos de dados SAP HANA que estão em execução em máquinas virtuais do Azure.
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: 6de1144237366806bb06c28a0777f62d69004e8b
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89006558"
---
# <a name="manage-and-monitor-backed-up-sap-hana-databases"></a>Gerenciar e monitorar backup de bancos de dados do SAP HANA

Este artigo descreve as tarefas comuns para gerenciamento e monitoramento de bancos de dados do SAP HANA em execução em uma máquina virtual (VM) do Azure e dos quais foi feito backup no cofre dos Serviços de Recuperação do Backup do Azure pelo serviço [Backup do Azure](./backup-overview.md). Você aprenderá a monitorar trabalhos e alertas, disparar um backup sob demanda, editar políticas, parar e retomar a proteção de banco de dados e cancelar o registro de uma VM para backups.

Se você ainda não configurou backups para seus bancos de dados do SAP HANA, confira [Fazer backup de bancos de dados do SAP HANA em VMs do Azure](./backup-azure-sap-hana-database.md).

## <a name="monitor-manual-backup-jobs-in-the-portal"></a>Monitorar trabalhos de backup manuais no portal

O Backup do Azure mostra todos os trabalhos disparados manualmente na seção **Trabalhos de backup** no portal do Azure.

![Seção Trabalhos de backup](./media/sap-hana-db-manage/backup-jobs.png)

Os trabalhos que você vê nesse portal incluem descoberta de banco de dados e registro e operações de backup e restauração. Os trabalhos agendados, incluindo backups de log, não são mostrados nesta seção. Os backups disparados manualmente dos clientes nativos do SAP HANA (Studio/ Cockpit/ DBA Cockpit) também não aparecem aqui.

![Lista de trabalhos de backup](./media/sap-hana-db-manage/backup-jobs-list.png)

Para saber mais sobre monitoramento, acesse [Monitoramento no portal do Azure](./backup-azure-monitoring-built-in-monitor.md) e [Monitoramento usando o Azure Monitor](./backup-azure-monitoring-use-azuremonitor.md).

## <a name="view-backup-alerts"></a>Exibir alertas de backup

Os alertas são uma maneira fácil de monitorar backups de bancos de dados do SAP HANA. Os alertas ajudam você a se concentrar nos eventos mais importantes sem se perder na infinidade de eventos que um backup gera. O backup do Azure permite definir alertas e eles podem ser monitorados da seguinte maneira:

* Entre no [portal do Azure](https://portal.azure.com/).
* No painel do cofre, selecione **Alertas de Backup**.

  ![Alertas de backup no painel do cofre](./media/sap-hana-db-manage/backup-alerts-dashboard.png)

* Você poderá ver os alertas:

  ![Lista de alertas de backup](./media/sap-hana-db-manage/backup-alerts-list.png)

* Clique nos alertas para ver mais detalhes:

  ![Detalhes do Alerta](./media/sap-hana-db-manage/alert-details.png)

Hoje o Backup do Azure permite o envio de alertas por email. Esses alertas são:

* Disparados para todas as falhas de backup.
* Consolidados no nível do banco de dados por código de erro.
* Enviado somente para a primeira falha de backup de um banco de dados.

Para saber mais sobre monitoramento, acesse [Monitoramento no portal do Azure](./backup-azure-monitoring-built-in-monitor.md) e [Monitoramento usando o Azure Monitor](./backup-azure-monitoring-use-azuremonitor.md).

## <a name="management-operations"></a>Operações de Gerenciamento

O Backup do Azure facilita o gerenciamento do backup de um banco de dados do SAP HANA com uma abundância de operações de gerenciamento que ele suporta. Essas operações são discutidas mais detalhadamente nas seções seguintes.

### <a name="run-an-on-demand-backup"></a>Executar um backup sob demanda

Os backups são executados de acordo com o agendamento da política. Você pode executar um backup sob demanda da seguinte maneira:

1. No menu do cofre, clique em **Itens de backup**.
2. Em **Itens de Backup**, selecione a VM que executa o banco de dados do SAP HANA e clique em **Fazer Backup Agora**.
3. Em **fazer backup agora**, escolha o tipo de backup que você deseja executar. Em seguida, clique em **OK**. Esse backup será mantido de acordo com a política associada a este item de backup.
4. Monitorar as notificações do portal. Você pode monitorar o andamento do trabalho no painel do cofre > **Trabalhos de Backup** > **Em Andamento**. Dependendo do tamanho do banco dados, a criação do backup inicial pode demorar um pouco.

### <a name="hana-native-client-integration"></a>Integração do cliente nativo do HANA

#### <a name="backup"></a>Backup

Os backups sob demanda disparados de qualquer um dos clientes nativos do HANA (para **Backint**) aparecerão na lista de backup na página **Itens de Backup**.

![Últimos backups executados](./media/sap-hana-db-manage/last-backups.png)

Você também pode [monitorar esses backups](#monitor-manual-backup-jobs-in-the-portal) na página **Trabalhos de backup**.

Esses backups sob demanda também aparecerão na lista de pontos de restauração.

![Lista de pontos de restauração](./media/sap-hana-db-manage/list-restore-points.png)

#### <a name="restore"></a>Restaurar

As restaurações disparadas de clientes nativos do HANA (usando **Backint**) para restaurar para o mesmo computador podem ser [monitoradas](#monitor-manual-backup-jobs-in-the-portal) na página **Trabalhos de backup**.

### <a name="run-sap-hana-native-client-backup-on-a-database-with-azure-backup-enabled"></a>Executar SAP HANA backup de cliente nativo em um banco de dados com o backup do Azure habilitado

Se você quiser fazer um backup local (usando o HANA Studio/Cockpit) de um banco de dados cujo backup está sendo feito com o Backup do Azure, faça o seguinte:

1. Aguarde até que os backups completos ou de log do banco de dados sejam concluídos. Verifique o status no SAP HANA Studio/Cockpit.
2. Desabilite os backups de log e defina o catálogo de backup para o sistema de arquivos para o banco de dados relevante.
3. Para fazer isso, clique duas vezes em **systemdb** > **Configuração** > **Selecionar Banco de Dados** > **Filtrar (Log)** .
4. Defina **enable_auto_log_backup** como **Não**.
5. Defina **log_backup_using_backint** como **Falso**.
6. Faça um backup completo sob demanda do banco de dados.
7. Aguarde até que o backup completo e o backup do catálogo sejam concluídos.
8. Reverter as configurações anteriores para as do Azure:
   * Defina **enable_auto_log_backup** como **Sim**.
   * Defina **log_backup_using_backint** como **Verdadeiro**.

### <a name="change-policy"></a>Alterar política

Você pode alterar a política subjacente para um item de backup do SAP HANA.

* No painel do cofre, vá até **Itens de Backup**:

  ![Selecione os itens de backup](./media/sap-hana-db-manage/backup-items.png)

* Escolha **SAP HANA na VM do Azure**

  ![Escolha SAP HANA na VM do Azure](./media/sap-hana-db-manage/sap-hana-in-azure-vm.png)

* Escolha o item de backup cuja política subjacente você deseja alterar
* Clique na política de backup existente

  ![Selecione a política de backup existente](./media/sap-hana-db-manage/existing-backup-policy.png)

* Altere a política, escolhendo na lista. [Criar uma nova política de backup](./backup-azure-sap-hana-database.md#create-a-backup-policy) se for necessário.

  ![Escolha a política na lista suspensa](./media/sap-hana-db-manage/choose-backup-policy.png)

* Salve as alterações

  ![Salve as alterações](./media/sap-hana-db-manage/save-changes.png)

* A modificação da política afetará todos os itens de backup associados e disparará os trabalhos de **configuração de proteção** correspondentes.

>[!NOTE]
> Qualquer alteração no período de retenção será aplicada de forma retrospectiva a todos os pontos de recuperação mais antigos além dos novos.
>
> As políticas de backup incremental não podem ser usadas para bancos de dados SAP HANA. Atualmente, o backup incremental não tem suporte para esses bancos de dados.

### <a name="modify-policy"></a>Modificar a política

Modifique a política para alterar tipos de backup, frequências e período de retenção.

>[!NOTE]
>Qualquer alteração no período de retenção será aplicada de forma retroativa a todos os pontos de recuperação mais antigos além dos novos.

1. No painel do cofre, vá até **Gerenciar > Políticas de Backup** e escolha a política que você deseja editar.

   ![Escolher a política a ser editada](./media/sap-hana-db-manage/manage-backup-policies.png)

1. Selecione **Modificar**.

   ![Selecione Modificar](./media/sap-hana-db-manage/modify-policy.png)

1. Escolha a frequência dos tipos de backup.

   ![Escolha a frequência do backup](./media/sap-hana-db-manage/choose-frequency.png)

A modificação da política afetará todos os itens de backup associados e disparará os trabalhos de **configuração de proteção** correspondentes.

### <a name="inconsistent-policy"></a>Política inconsistente

Ocasionalmente, uma operação de modificação de política pode levar a uma versão de política **inconsistente**  para alguns itens de backup. Isso ocorre quando ocorre falha no trabalho de **configuração de proteção** correspondente para o item de backup depois que uma operação de modificação de política é disparada. Ele aparece da seguinte maneira na exibição do item de backup:

![Política inconsistente](./media/sap-hana-db-manage/inconsistent-policy.png)

Você pode corrigir a versão da política para todos os itens impactados em um único clique:

![Corrigir versão da política](./media/sap-hana-db-manage/fix-policy-version.png)

### <a name="stop-protection-for-an-sap-hana-database"></a>Interromper a proteção de um banco de dados SAP HANA

Você pode interromper a proteção de um banco de dados SAP HANA de algumas maneiras:

* Interromper todos os trabalhos de backup futuros e excluir todos os pontos de recuperação.
* Interromper todos os trabalhos de backup futuros e deixar os pontos de recuperação intactos.

Se você optar por deixar os pontos de recuperação, tenha em mente estes detalhes:

* Todos os pontos de recuperação permanecerão intactos para sempre, e toda a remoção será interrompida em parar proteção com reter dados.
* Você será cobrado pela instância protegida e pelo armazenamento consumido. Para saber mais, confira [Preços do Backup do Azure](https://azure.microsoft.com/pricing/details/backup/).
* Se você excluir uma fonte de dados sem interromper os backups, os novos backups falharão.

Para interromper a proteção para um banco de dados:

* No painel do cofre, selecione **Itens de Backup**.
* Em **Tipo de Gerenciamento de Backup**, selecione **SAP HANA na VM do Azure**

  ![Selecionar SAP HANA na VM do Azure](./media/sap-hana-db-manage/sap-hana-azure-vm.png)

* Selecione o banco de dados para o qual você deseja interromper a proteção em:

  ![Selecionar o banco de dados para interromper a proteção](./media/sap-hana-db-manage/select-database.png)

* No menu de banco de dados, selecione **Parar backup**.

  ![Selecionar parar backup](./media/sap-hana-db-manage/stop-backup.png)

* No menu **Parar Backup**, selecione se deseja reter ou excluir dados. Se quiser, insira um motivo e um comentário.

  ![Selecionar reter ou excluir dados](./media/sap-hana-db-manage/retain-backup-data.png)

* Selecionar **Parar Backup**.

### <a name="resume-protection-for-an-sap-hana-database"></a>Retomar a proteção de um banco de dados SAP HANA

Quando você interrompe a proteção do banco de dados SAP HANA, se selecionar a opção **Reter Dados de Backup**, posteriormente pode continuar a proteção. Se você não mantiver os dados de backup, não poderá retomar a proteção.

Para retomar a proteção de um banco de dados SAP HANA:

* Abra o item de backup e selecione **Retomar o backup**.

   ![Selecionar Retomar o backup](./media/sap-hana-db-manage/resume-backup.png)

* No menu **Política de backup**, selecione uma política e, em seguida, selecione **Salvar**.

### <a name="upgrading-from-sdc-to-mdc"></a>Atualização de SDC para MDC

Saiba como continuar o backup de um banco de dados do SAP HANA [após a atualização de SDC para MDC](backup-azure-sap-hana-database-troubleshoot.md#sdc-to-mdc-upgrade-with-a-change-in-sid).

### <a name="upgrading-from-sdc-to-mdc-without-a-sid-change"></a>Atualização de SDC para MDC sem uma alteração de SID

Saiba como continuar o backup de um banco de dados SAP HANA cujo [Sid não foi alterado após a atualização de SDC para MDC](backup-azure-sap-hana-database-troubleshoot.md#sdc-to-mdc-upgrade-with-no-change-in-sid).

### <a name="unregister-an-sap-hana-instance"></a>Cancelar o registro de uma instância do SAP HANA

Cancele o registro de uma instância do SAP HANA depois de desabilitar a proteção, mas antes você deve excluir o cofre:

* No painel de cofres, em **Gerenciar**, selecione **Infraestrutura de Backup**.

   ![Selecionar Infraestrutura de Backup](./media/sap-hana-db-manage/backup-infrastructure.png)

* Selecione o **tipo de Gerenciamento de Backup** como **Carga de trabalho na VM do Azure**

   ![Selecione o tipo de Gerenciamento de Backup como Carga de trabalho na VM do Azure](./media/sap-hana-db-manage/backup-management-type.png)

* Em **Servidores Protegidos**, selecione a instância para cancelar o registro. Para excluir o cofre, você deve cancelar o registro de todos os servidores/instâncias.

* Clique com o botão direito do mouse na instância protegida e selecione **Cancelar o registro**.

   ![Selecionar cancelar o registro](./media/sap-hana-db-manage/unregister.png)

### <a name="re-register-extension-on-the-sap-hana-server-vm"></a>Registrar novamente a extensão na VM do servidor SAP HANA

Às vezes, a extensão da carga de trabalho na VM pode ser afetada por um motivo ou outro. Nesses casos, todas as operações disparadas na VM começarão a falhar. Você pode precisar registrar novamente a extensão na VM. A operação de novo registro reinstala a extensão de backup da carga de trabalho na VM para que as operações continuem.

Use essa opção com cuidado: quando disparado em uma VM com uma extensão já íntegra, essa operação fará a extensão ser reiniciada. Isso pode causar falha em todos os trabalhos em andamento. Verifique se há um ou mais dos [sintomas](backup-azure-sap-hana-database-troubleshoot.md#re-registration-failures) antes de acionar a operação de novo registro.

## <a name="next-steps"></a>Próximas etapas

* Saiba como [solucionar problemas comuns ao fazer backup de bancos de dados do SAP HANA.](./backup-azure-sap-hana-database-troubleshoot.md)
