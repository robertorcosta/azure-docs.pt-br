---
title: Gerenciar bancos de dados SAP HANA com backup em VMs do Azure
description: Neste artigo, aprenda tarefas comuns para gerenciar e monitorar bancos de dados SAP HANA que estão sendo executados em máquinas virtuais Do Zure.
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: 89fd7f23163d301817e767771257d9bc6f4ed526
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480055"
---
# <a name="manage-and-monitor-backed-up-sap-hana-databases"></a>Gerenciar e monitorar backup de bancos de dados do SAP HANA

Este artigo descreve tarefas comuns para gerenciar e monitorar bancos de dados SAP HANA que estão sendo executados em uma máquina virtual Azure (VM) e que são copiados para um cofre do Azure Backup Recovery Services pelo serviço de backup do [Azure.](https://docs.microsoft.com/azure/backup/backup-overview) Você aprenderá como monitorar empregos e alertas, acionar um backup demanda, editar políticas, interromper e retomar a proteção do banco de dados e descadastrar uma VM de backups.

Se você ainda não configurou backups para seus bancos de dados SAP HANA, consulte [Backup de bancos de dados SAP HANA em VMs Azure](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database).

## <a name="monitor-manual-backup-jobs-in-the-portal"></a>Monitore trabalhos de backup manual no portal

O Azure Backup mostra todos os trabalhos acionados manualmente na seção **trabalhos de backup** no portal Azure.

![Seção de trabalhos de backup](./media/sap-hana-db-manage/backup-jobs.png)

Os trabalhos que você vê neste portal incluem a descoberta e o registro de banco de dados e operações de backup e restauração. Trabalhos agendados, incluindo backups de log, não são mostrados nesta seção. Backups acionados manualmente dos clientes nativos do SAP HANA (Studio/ Cockpit/DBA Cockpit) também não aparecem aqui.

![Lista de empregos de backup](./media/sap-hana-db-manage/backup-jobs-list.png)

Para saber mais sobre monitoramento, acesse [Monitoring in the Azure portal](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor) e Monitoring usando o [Azure Monitor](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor).

## <a name="view-backup-alerts"></a>Exibir alertas de backup

Os alertas são um meio fácil de monitorar backups de bancos de dados SAP HANA. Os alertas ajudam você a se concentrar nos eventos que você mais se importa sem se perder na infinidade de eventos que um backup gera. O Azure Backup permite definir alertas e eles podem ser monitorados da seguinte forma:

* Faça login no [portal Azure](https://portal.azure.com/).
* No painel do cofre, selecione **Alertas de backup**.

  ![Alertas de backup no painel do cofre](./media/sap-hana-db-manage/backup-alerts-dashboard.png)

* Você poderá ver os alertas:

  ![Lista de alertas de backup](./media/sap-hana-db-manage/backup-alerts-list.png)

* Clique nos alertas para ver mais detalhes:

  ![Detalhes do Alerta](./media/sap-hana-db-manage/alert-details.png)

Hoje, o Azure Backup permite o envio de alertas por e-mail. Estes alertas são:

* Acionado para todas as falhas de backup.
* Consolidado no nível do banco de dados por código de erro.
* Enviado apenas para a primeira falha de backup de um banco de dados.

Para saber mais sobre monitoramento, acesse [Monitoring in the Azure portal](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor) e Monitoring usando o [Azure Monitor](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor).

## <a name="management-operations"></a>Operações de Gerenciamento

O Azure Backup facilita o gerenciamento de um banco de dados SAP HANA com uma abundância de operações de gerenciamento que ele suporta. Essas operações são discutidas com mais detalhes nas seguintes seções.

### <a name="run-an-on-demand-backup"></a>Executar um backup sob demanda

Os backups são executados de acordo com o cronograma da apólice. Você pode executar um backup demanda da seguinte forma:

1. No menu do cofre, clique em **Itens de backup**.
2. Em **Itens de backup,** selecione a VM executando o banco de dados SAP HANA e clique **em Backup agora**.
3. Em **Backup Now,** use o controle de calendário para selecionar o último dia em que o ponto de recuperação deve ser mantido. Em seguida, clique em **OK**.
4. Monitorar as notificações do portal. Você pode monitorar o progresso do trabalho no painel do cofre > **backup jobs** > **em andamento**. Dependendo do tamanho do seu banco de dados, a criação do backup inicial pode demorar um pouco.

### <a name="hana-native-client-integration"></a>Integração de clientes nativos hana

Agora, backups completos demanda acionados de qualquer um dos clientes nativos do HANA aparecerão como um backup completo na página **Itens de backup.**

![Últimos backups executados](./media/sap-hana-db-manage/last-backups.png)

Esses backups completos ad-hoc também aparecerão na lista de pontos de restauração para restauração.

![Lista de pontos de restauração](./media/sap-hana-db-manage/list-restore-points.png)

### <a name="run-sap-hana-native-client-backup-on-a-database-with-azure-backup-enabled"></a>Execute o backup do cliente nativo do SAP HANA em um banco de dados com backup do Azure ativado

Se você quiser fazer um backup local (usando o HANA Studio / Cockpit) de um banco de dados que está sendo feito backup com o Azure Backup, faça o seguinte:

1. Aguarde os backups completos ou logpara o banco de dados terminar. Verifique o status no SAP HANA Studio/ Cockpit.
2. Desabilite backups de log e defina o catálogo de backup para o sistema de arquivos para banco de dados relevante.
3. Para fazer isso, clique duas vezes em **Systemdb** > **Configuration** > **Select Database** > **Filter (Log)**.
4. Definir **enable_auto_log_backup** para **No**.
5. Definir **log_backup_using_backint** para **Falso**.
6. Faça um backup completo demanda do banco de dados.
7. Aguarde o backup completo e o backup do catálogo para terminar.
8. Reverter as configurações anteriores de volta para aquelas para o Azure:
   * Definir **enable_auto_log_backup** para **Sim**.
   * Definir **log_backup_using_backint** para **True**.

### <a name="change-policy"></a>Alterar política

Você pode alterar a política subjacente para um item de backup SAP HANA.

* No painel do cofre, vá para **itens de backup:**

  ![Selecione itens de backup](./media/sap-hana-db-manage/backup-items.png)

* Escolha **sap HANA em Azure VM**

  ![Escolha sap HANA em Azure VM](./media/sap-hana-db-manage/sap-hana-in-azure-vm.png)

* Escolha o item de backup cuja política subjacente você deseja alterar
* Clique na diretiva backup existente

  ![Selecione a política de backup existente](./media/sap-hana-db-manage/existing-backup-policy.png)

* Mude a política, escolhendo entre a lista. [Crie uma nova política de backup,](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database#create-a-backup-policy) se necessário.

  ![Escolha a política na lista de baixa](./media/sap-hana-db-manage/choose-backup-policy.png)

* Salvar as alterações

  ![Salvar as alterações](./media/sap-hana-db-manage/save-changes.png)

* A modificação da diretiva afetará todos os itens de backup associados e acionará os trabalhos **de proteção correspondentes.**

>[!NOTE]
> Qualquer alteração no período de retenção será aplicada retrospectivamente a todos os pontos de recuperação mais antigos, além dos novos.
>
> As políticas de backup incrementais não podem ser usadas para bancos de dados SAP HANA. O backup incremental não é suportado no momento para esses bancos de dados.

### <a name="modify-policy"></a>Modificar a política

Modificar a diretiva para alterar tipos de backup, freqüências e faixa de retenção.

>[!NOTE]
>Qualquer alteração no período de retenção será aplicada retroativamente a todos os pontos de recuperação mais antigos, além dos novos.

1. No painel do cofre, vá para **Gerenciar > Políticas de backup** e escolha a diretiva que deseja editar.

   ![Escolha a política a ser editada](./media/sap-hana-db-manage/manage-backup-policies.png)

1. Selecione **Modificar**.

   ![Selecione Modificar](./media/sap-hana-db-manage/modify-policy.png)

1. Escolha a freqüência para os tipos de backup.

   ![Escolha a freqüência de backup](./media/sap-hana-db-manage/choose-frequency.png)

A modificação da diretiva afetará todos os itens de backup associados e acionará os trabalhos **de proteção correspondentes.**

### <a name="inconsistent-policy"></a>Política inconsistente

Ocasionalmente, uma operação de diretiva de modificação pode levar a uma versão de política **inconsistente** para alguns itens de backup. Isso acontece quando o trabalho **de proteção configurado** correspondente falha no item de backup após uma operação de diretiva de modificação ser acionada. Ele aparece da seguinte forma na exibição do item de backup:

![Política inconsistente](./media/sap-hana-db-manage/inconsistent-policy.png)

Você pode corrigir a versão de diretiva para todos os itens impactados em um clique:

![Corrigir versão da política](./media/sap-hana-db-manage/fix-policy-version.png)

### <a name="stop-protection-for-an-sap-hana-database"></a>Interromper a proteção de um banco de dados SAP HANA

Você pode interromper a proteção de um banco de dados SAP HANA de algumas maneiras:

* Interromper todos os trabalhos de backup futuros e excluir todos os pontos de recuperação.
* Interromper todos os trabalhos de backup futuros e deixar os pontos de recuperação intactos.

Se você optar por deixar os pontos de recuperação, tenha em mente estes detalhes:

* Todos os pontos de recuperação permanecerão intactos para sempre, toda a remoção será interrompida ao parar a proteção com os dados de retenção.
* Você será cobrado pela instância protegida e pelo armazenamento consumido. Para obter mais informações, consulte [os preços do Azure Backup](https://azure.microsoft.com/pricing/details/backup/).
* Se você excluir uma fonte de dados sem interromper os backups, os novos backups falharão.

Para interromper a proteção para um banco de dados:

* No painel do cofre, selecione **Itens de backup**.
* Em **Tipo de Gerenciamento de Backup,** selecione **SAP HANA na VM Azure**

  ![Selecione SAP HANA em Azure VM](./media/sap-hana-db-manage/sap-hana-azure-vm.png)

* Selecione o banco de dados para o qual deseja interromper a proteção:

  ![Selecione o banco de dados para interromper a proteção](./media/sap-hana-db-manage/select-database.png)

* No menu de banco de dados, selecione **Parar backup**.

  ![Selecione o backup stop](./media/sap-hana-db-manage/stop-backup.png)

* No **menu 'Stop Backup',** selecione se deve reter ou excluir dados. Se você quiser, forneça uma razão e comente.

  ![Selecione reter ou excluir dados](./media/sap-hana-db-manage/retain-backup-data.png)

* Selecione **Stop backup**.

### <a name="resume-protection-for-an-sap-hana-database"></a>Retomar a proteção para um banco de dados SAP HANA

Quando você parar a proteção para o banco de dados SAP HANA, se você selecionar a opção **Reter dados de backup,** poderá retomar a proteção posteriormente. Se você não reter os dados de backup, você não será capaz de retomar a proteção.

Para retomar a proteção de um banco de dados SAP HANA:

* Abra o item de backup e selecione **Retomar backup**.

   ![Selecione backup de currículo](./media/sap-hana-db-manage/resume-backup.png)

* No menu **Política de backup**, selecione uma política e, em seguida, selecione **Salvar**.

### <a name="upgrading-from-sap-hana-10-to-20"></a>Atualização do SAP HANA 1.0 para 2.0

Saiba como continuar o backup de um banco de dados SAP HANA [após a atualização do SAP HANA 1.0 para 2.0](backup-azure-sap-hana-database-troubleshoot.md#upgrading-from-sap-hana-10-to-20).

### <a name="upgrading-without-a-sid-change"></a>Atualização sem uma alteração SID

Saiba como continuar o backup de um banco de dados SAP HANA cujo [SID não foi alterado após a atualização](backup-azure-sap-hana-database-troubleshoot.md#upgrading-without-an-sid-change).

### <a name="unregister-an-sap-hana-instance"></a>Cancelar o registro de uma instância SAP HANA

Desregistre uma instância SAP HANA depois de desativar a proteção, mas antes de excluir o cofre:

* No painel do cofre, em **Manage,** selecione **Infra-estrutura de backup**.

   ![Selecionar Infraestrutura de Backup](./media/sap-hana-db-manage/backup-infrastructure.png)

* Selecione o **tipo de gerenciamento de backup** como Carga de Trabalho na **VM do Azure**

   ![Selecione o tipo de gerenciamento de backup como Carga de Trabalho na VM do Azure](./media/sap-hana-db-manage/backup-management-type.png)

* Em **Servidores Protegidos,** selecione a instância para não registrar. Para excluir o cofre, você deve cancelar o registro de todos os servidores/instâncias.

* Clique com o botão direito do mouse na instância protegida e **selecione Descadastrar**.

   ![Selecione não registrar](./media/sap-hana-db-manage/unregister.png)

### <a name="re-register-extension-on-the-sap-hana-server-vm"></a>Recadastramento extensão no VM do servidor SAP HANA

Às vezes, a extensão da carga de trabalho na VM pode ser impactada por uma razão ou outra. Nesses casos, todas as operações desencadeadas na VM começarão a falhar. Você pode então precisar reregistrar a extensão na VM. A operação de reregistro reinstala a extensão de backup da carga de trabalho na VM para que as operações continuem.

Use esta opção com cautela: quando acionado em uma VM com uma extensão já saudável, esta operação fará com que a extensão seja reiniciada. Isso pode fazer com que todos os trabalhos em andamento falhem. Verifique se há um ou mais dos [sintomas](backup-azure-sap-hana-database-troubleshoot.md#re-registration-failures) antes de desencadear a operação de re-registro.

## <a name="next-steps"></a>Próximas etapas

* Saiba como [solucionar problemas comuns ao fazer backup de bancos de dados SAP HANA.](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot)
