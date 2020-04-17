---
title: Gerenciar e monitorar DBs do SQL Server em uma VM Azure
description: Este artigo descreve como gerenciar e monitorar bancos de dados SQL Server que estão sendo executados em uma VM Azure.
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 14e3a4797fe60a3d1857f1e6d947fa0c669bdcfe
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537297"
---
# <a name="manage-and-monitor-backed-up-sql-server-databases"></a>Gerenciar e monitorar backup de bancos de dados do SQL Server

Este artigo descreve tarefas comuns para gerenciar e monitorar bancos de dados do SQL Server que estão sendo executados em uma máquina virtual (VM) do Azure e que são copiados para um cofre do Azure Backup Recovery Services pelo serviço de backup do [Azure.](backup-overview.md) Você aprenderá como monitorar empregos e alertas, interromper e retomar a proteção do banco de dados, executar trabalhos de backup e descadastrar uma VM de backups.

Se você ainda não configurou backups para seus bancos de dados SQL Server, consulte [Backup de bancos de dados SQL Server em VMs do Azure](backup-azure-sql-database.md)

## <a name="monitor-manual-backup-jobs-in-the-portal"></a>Monitore trabalhos de backup manual no portal

O Azure Backup mostra todos os trabalhos acionados manualmente no portal **de empregos de backup.** Os trabalhos que você vê neste portal incluem a descoberta e o registro de banco de dados e operações de backup e restauração.

![O portal de empregos backup](./media/backup-azure-sql-database/jobs-list.png)

> [!NOTE]
> O portal **de empregos de backup** não mostra trabalhos de backup programados. Use o SQL Server Management Studio para monitorar trabalhos de backup agendados, conforme descrito na próxima seção.
>

Para obter detalhes sobre os cenários de monitoramento, acesse [Monitoring in the Azure portal](backup-azure-monitoring-built-in-monitor.md) and Monitoring usando o [Azure Monitor](backup-azure-monitoring-use-azuremonitor.md).  

## <a name="view-backup-alerts"></a>Exibir alertas de backup

Uma vez que os backups de log ocorrem a cada 15 minutos, monitorar os trabalhos de backup pode ser entediante. O Azure Backup facilita o monitoramento enviando alertas de e-mail. Os alertas de e-mail são:

- Acionado para todas as falhas de backup.
- Consolidado no nível do banco de dados por código de erro.
- Enviado apenas para a primeira falha de backup de um banco de dados.

Para monitorar os alertas de backup do banco de dados:

1. Entre no [portal do Azure](https://portal.azure.com).

2. No painel de cofres, selecione **Alertas e Eventos**.

   ![Selecionar Alertas e Eventos](./media/backup-azure-sql-database/vault-menu-alerts-events.png)

3. Em **Alertas e Eventos**, selecione **Alertas de Backup**.

   ![Selecionar Alertas de Backup](./media/backup-azure-sql-database/backup-alerts-dashboard.png)

## <a name="stop-protection-for-a-sql-server-database"></a>Interromper a proteção para um banco de dados do SQL Server

Você pode interromper o backup de um banco de dados do SQL Server de duas maneiras:

- Interromper todos os trabalhos de backup futuros e excluir todos os pontos de recuperação.
- Pare todos os futuros trabalhos de backup, e deixe os pontos de recuperação intactos.

Se você optar por deixar os pontos de recuperação, tenha em mente estes detalhes:

- Todos os pontos de recuperação permanecerão intactos para sempre, toda a remoção será interrompida ao parar a proteção com os dados de retenção.
- Você será cobrado pela instância protegida e pelo armazenamento consumido. Para obter mais informações, consulte [os preços do Azure Backup](https://azure.microsoft.com/pricing/details/backup/).
- Se você excluir uma fonte de dados sem interromper os backups, os novos backups falharão. Os pontos de recuperação antigos expirarão de acordo com a política, mas um último ponto de recuperação será sempre mantido até que você pare os backups e exclua os dados.

Para interromper a proteção para um banco de dados:

1. No painel do cofre, selecione **Itens de backup**.

2. Em **Tipo de Gerenciamento de Backup,** selecione **SQL no Azure VM**.

    ![Selecionar SQL na VM do Azure](./media/backup-azure-sql-database/sql-restore-backup-items.png)

3. Selecione o banco de dados para o qual você deseja interromper a proteção.

    ![Selecionar o banco de dados para interromper a proteção](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

4. No menu de banco de dados, selecione **Parar backup**.

    ![Selecionar Parar Backup](./media/backup-azure-sql-database/stop-db-button.png)

5. No **menu 'Stop Backup',** selecione se deve reter ou excluir dados. Se você quiser, forneça uma razão e comente.

    ![Reter ou excluir dados no menu Stop Backup](./media/backup-azure-sql-database/stop-backup-button.png)

6. Selecione **Stop backup**.

> [!NOTE]
>
>Para obter mais informações sobre a opção de exclusão de dados, consulte o FAQ abaixo:
>
>- [Se eu excluir um banco de dados de uma instância autoprotegida, o que acontecerá com os backups?](faq-backup-sql-server.md#if-i-delete-a-database-from-an-autoprotected-instance-what-will-happen-to-the-backups)
>- [Se eu parar a operação de backup de um banco de dados autoprotegido, qual será o seu comportamento?](faq-backup-sql-server.md#if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-be-the-behavior)
>
>

## <a name="resume-protection-for-a-sql-database"></a>Retomar a proteção para um banco de dados SQL

Quando você interromper a proteção para o banco de dados SQL, se você selecionar a opção **Reter dados de backup,** poderá retomar a proteção posteriormente. Se você não reter os dados de backup, você não poderá retomar a proteção.

Para retomar a proteção de um banco de dados SQL:

1. Abra o item de backup e selecione **Retomar backup**.

    ![Selecionar Retomar backup para retomar a proteção do banco de dados](./media/backup-azure-sql-database/resume-backup-button.png)

2. No menu **Política de backup**, selecione uma política e, em seguida, selecione **Salvar**.

## <a name="run-an-on-demand-backup"></a>Executar um backup sob demanda

Você pode executar diferentes tipos de backups sob demanda:

- Backup completo
- Backup completo somente de cópia
- Backup diferencial
- Backup de log

Embora você precise especificar a duração da retenção para backup completo somente do Copy, o intervalo de retenção para backup completo sob demanda será automaticamente definido para 45 dias a partir do momento atual.

Para obter mais informações, consulte [os tipos de backup do SQL Server](backup-architecture.md#sql-server-backup-types).

## <a name="unregister-a-sql-server-instance"></a>Cancelar o registro de uma instância do SQL Server

Desregistre uma instância do SQL Server depois de desativar a proteção, mas antes de excluir o cofre:

1. No painel do cofre, em **Manage,** selecione **Infra-estrutura de backup**.  

   ![Selecionar Infraestrutura de Backup](./media/backup-azure-sql-database/backup-infrastructure-button.png)

2. Em **Servidores de Gerenciamento**, selecione **Servidores Protegidos**.

   ![Selecionar Servidores Protegidos](./media/backup-azure-sql-database/protected-servers.png)

3. Em **Servidores Protegidos**, selecione o servidor para cancelar o registro. Para excluir o cofre, você deve cancelar o registro de todos os servidores.

4. Clique com o botão direito do mouse no servidor protegido e **selecione Descadastrar**.

   ![Selecione Excluir](./media/backup-azure-sql-database/delete-protected-server.jpg)

## <a name="modify-policy"></a>Modificar a política

Modificar a diretiva para alterar a freqüência de backup ou o intervalo de retenção.

> [!NOTE]
> Qualquer alteração no período de retenção será aplicada retrospectivamente a todos os pontos de recuperação mais antigos, além dos novos.

No painel do cofre, vá para **Gerenciar** > **políticas de backup** e escolha a diretiva que deseja editar.

  ![Gerenciar a política de backup](./media/backup-azure-sql-database/modify-backup-policy.png)

  ![Modificar a política de backup](./media/backup-azure-sql-database/modify-backup-policy-impact.png)

A modificação da diretiva afetará todos os itens de backup associados e acionará os trabalhos **de proteção correspondentes.**

### <a name="inconsistent-policy"></a>Política inconsistente

Às vezes, uma operação de diretiva de modificação pode levar a uma versão de política **inconsistente** para alguns itens de backup. Isso acontece quando o trabalho **de proteção configurado** correspondente falha no item de backup após uma operação de diretiva de modificação ser acionada. Ele aparece da seguinte forma na exibição do item de backup:

  ![Política inconsistente](./media/backup-azure-sql-database/inconsistent-policy.png)

Você pode corrigir a versão de diretiva para todos os itens impactados em um clique:

  ![Corrigir política inconsistente](./media/backup-azure-sql-database/fix-inconsistent-policy.png)

## <a name="re-register-extension-on-the-sql-server-vm"></a>Recadastramento extensão no VM do Servidor SQL

Às vezes, a extensão da carga de trabalho na VM pode ser impactada por um motivo ou outro. Nesses casos, todas as operações desencadeadas na VM começarão a falhar. Você pode então precisar reregistrar a extensão na VM. A operação **de reregistro** reinstala a extensão de backup da carga de trabalho na VM para que as operações continuem.

Use esta opção com cautela; quando acionada em uma VM com uma extensão já saudável, esta operação fará com que a extensão seja reiniciada. Isso pode resultar em todos os trabalhos em andamento para falhar. Verifique por favor um ou mais dos [sintomas](backup-sql-server-azure-troubleshoot.md#re-registration-failures) antes de desencadear a operação de re-registro.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte [Backups de solução de problemas em um banco de dados do SQL Server](backup-sql-server-azure-troubleshoot.md).
