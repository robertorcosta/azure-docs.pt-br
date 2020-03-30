---
title: Fazer backup do SQL Server no Azure como uma carga de trabalho do DPM
description: Uma introdução ao backup de bancos de dados SQL Server usando o serviço de backup do Azure
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 8cbb8c833bc2933afac300bcc848fd50861011d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77505925"
---
# <a name="back-up-sql-server-to-azure-as-a-dpm-workload"></a>Fazer backup do SQL Server no Azure como uma carga de trabalho do DPM

Este artigo leva você através das etapas de configuração para fazer backup dos bancos de dados do SQL Server usando o Backup do Azure.

Para fazer backup de bancos de dados do SQL Server no Azure é necessária uma conta do Azure. Se você não tiver uma, você pode criar uma conta gratuita em apenas alguns minutos. Para obter mais informações, consulte [Criar sua conta gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

Para fazer backup de um banco de dados do SQL Server no Azure e recuperá-lo do Azure:

1. Crie uma política de backup para proteger os bancos de dados do SQL Server no Azure.
1. Crie cópias de backup demanda no Azure.
1. Recuperar o banco de dados do Azure.

## <a name="before-you-start"></a>Antes de começar

Antes de começar, certifique-se de ter cumprido os [pré-requisitos](backup-azure-dpm-introduction.md#prerequisites-and-limitations) para usar o Azure Backup para proteger cargas de trabalho. Aqui estão algumas das tarefas pré-requisitos: 
* Crie um cofre de backup.
* Baixar credenciais do cofre. 
* Instale o agente de backup do Azure.
* Registre o servidor com o cofre.

## <a name="create-a-backup-policy"></a>Criar uma política de backup 

Para proteger os bancos de dados sql server no Azure, primeiro crie uma política de backup:

1. No servidor DPM (Data Protection Manager, gerente de proteção de dados), selecione o espaço de trabalho **Proteção.**
1. Selecione **Novo** para criar um grupo de proteção.

    ![Criar um grupo de proteção](./media/backup-azure-backup-sql/protection-group.png)
1. Na página inicial, revise as orientações sobre a criação de um grupo de proteção. Em seguida, selecione **Next**.
1. Selecione **Servidores**.

    ![Selecione o tipo de grupo de proteção servidores](./media/backup-azure-backup-sql/pg-servers.png)
1. Expanda a máquina SQL Server onde os bancos de dados que você deseja fazer backup estão localizados. Você vê as fontes de dados que podem ser copiadas desse servidor. Expanda **todos os compartilhamentos de SQL** e selecione os bancos de dados que deseja fazer backup. Neste exemplo, selecionamos ReportServer$MSDPM2012 e ReportServer$MSDPM2012TempDB. Em seguida, selecione **Next**.

    ![Selecione um banco de dados do SQL Server](./media/backup-azure-backup-sql/pg-databases.png)
1. Nomeie o grupo de proteção e selecione **I want on protection**.

    ![Escolha um método de proteção de dados - proteção de disco de curto prazo ou proteção on-line do Azure](./media/backup-azure-backup-sql/pg-name.png)
1. Na página **Especificar Metas de Curto Prazo,** inclua as entradas necessárias para criar pontos de backup no disco.

    Neste exemplo, **o intervalo de retenção** é definido como 5 *dias*. A **freqüência de sincronização** de backup é definida como uma vez a cada *15 minutos*. **O Backup Completo expresso** está programado para *8:00 PM*.

    ![Configure metas de curto prazo para proteção de backup](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > Neste exemplo, um ponto de backup é criado às 20:00 pm todos os dias. Os dados que foram modificados desde o ponto de backup das 20:00 horas do dia anterior são transferidos. Esse processo é chamado de **Backup Completo Expresso**. Embora os logs de transação sejam sincronizados a cada 15 minutos, se precisarmos recuperar o banco de dados às 21h, então o ponto é criado reproduzindo os logs do último ponto de backup completo expresso, que é 8:00 PM neste exemplo.
   >
   >

1. Selecione **Avançar**. O DPM mostra o espaço total de armazenamento disponível. Também mostra a potencial utilização do espaço em disco.

    ![Configurar a alocação de disco](./media/backup-azure-backup-sql/pg-storage.png)

    Por padrão, o DPM cria um volume por fonte de dados (banco de dados SQL Server). O volume é usado para a cópia inicial de backup. Nesta configuração, o Logical Disk Manager (LDM) limita a proteção do DPM a 300 fontes de dados (bancos de dados SQL Server). Para contornar essa limitação, selecione **Colocalizar dados no Pool de Armazenamento DPM**. Se você usar essa opção, o DPM usará um único volume para várias fontes de dados. Essa configuração permite que o DPM proteja até 2.000 bancos de dados SQL Server.

    Se você selecionar **Aumentar automaticamente os volumes, o**DPM poderá explicar o aumento do volume de backup à medida que os dados de produção crescem. Se você não selecionar **Aumentar automaticamente os volumes, o**DPM limitará o armazenamento de backup às fontes de dados do grupo de proteção.

1. Se você é um administrador, você pode optar por transferir esse backup inicial **automaticamente pela rede** e escolher a hora da transferência. Ou optar por transferir **manualmente** o backup. Em seguida, selecione **Next**.

    ![Escolha um método de criação de réplicas](./media/backup-azure-backup-sql/pg-manual.png)

    A cópia inicial de backup requer a transferência de toda a fonte de dados (banco de dados SQL Server). Os dados de backup são movedos do servidor de produção (máquina SQL Server) para o servidor DPM. Se esse backup for grande, a transferência dos dados pela rede pode causar congestionamento da largura de banda. Por essa razão, os administradores podem optar por usar mídia removível para transferir o backup inicial **manualmente**. Ou eles podem transferir os dados **automaticamente pela rede** em um horário especificado.

    Após o backup inicial, os backups continuam incrementalmente na cópia inicial de backup. Os backups incrementais tendem a ser pequenos e são facilmente transferidos pela rede.
    
1. Escolha quando executar uma verificação de consistência. Em seguida, selecione **Next**.

    ![Escolha quando executar uma verificação de consistência](./media/backup-azure-backup-sql/pg-consistent.png)

    O DPM pode executar uma verificação de consistência na integridade do ponto de backup. Ele calcula a soma de verificação do arquivo de backup no servidor de produção (a máquina SQL Server neste exemplo) e os dados de backup para esse arquivo no DPM. Se a verificação encontrar um conflito, então o arquivo de backup no DPM é considerado corrompido. O DPM corrige os dados de backup enviando os blocos que correspondem à incompatibilidade de soma de cheques. Como a verificação de consistência é uma operação de uso intensivo de desempenho, os administradores podem optar por agendar a verificação de consistência ou executá-la automaticamente.

1. Selecione as fontes de dados a serem proteger no Azure. Em seguida, selecione **Next**.

    ![Selecione fontes de dados para proteger no Azure](./media/backup-azure-backup-sql/pg-sqldatabases.png)
1. Se você é um administrador, você pode escolher horários de backup e políticas de retenção que se adequam às políticas da sua organização.

    ![Escolha horários e políticas de retenção](./media/backup-azure-backup-sql/pg-schedule.png)

    Neste exemplo, os backups são feitos diariamente às 12:00 e às 20:00.

    > [!TIP]
    > Para uma recuperação rápida, mantenha alguns pontos de recuperação de curto prazo em seu disco. Esses pontos de recuperação são usados para recuperação operacional. O Azure serve como uma boa localização fora do local, fornecendo SLAs mais altos e disponibilidade garantida.
    >
    > Use o DPM para agendar backups do Azure após o término dos backups de disco locais. Quando você segue essa prática, o backup de disco mais recente é copiado para o Azure.
    >

1. Escolha o agendamento de política de retenção. Para obter mais informações sobre como a política de retenção funciona, consulte [Use Azure Backup para substituir sua infra-estrutura de fita](backup-azure-backup-cloud-as-tape.md).

    ![Escolha uma política de retenção](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    Neste exemplo:

    * Os backups são feitos diariamente às 12:00 e 20:00. Eles são mantidos por 180 dias.
    * O reforço no sábado às 12:00 é mantido por 104 semanas.
    * O backup do último sábado do mês às 12:00 é mantido por 60 meses.
    * O reforço do último sábado de março às 12:00 é mantido por 10 anos.
    
    Depois de escolher uma política de retenção, selecione **Next**.

1. Escolha como transferir a cópia inicial de backup para o Azure.

    * A opção **Automaticamente sobre a rede** segue seu cronograma de backup para transferir os dados para o Azure.
    * Para obter mais informações sobre **backup off-line,** consulte [Visão geral do backup offline](offline-backup-overview.md).

    Depois de escolher um mecanismo de transferência, selecione **Next**.

1. Na página **Resumo,** revise os detalhes da política. Em seguida, selecione **Criar grupo**. Você pode selecionar **Fechar** e observar o progresso do trabalho no espaço de trabalho **de monitoramento.**

    ![O progresso da criação do grupo de proteção](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="create-on-demand-backup-copies-of-a-sql-server-database"></a>Crie cópias de backup demanda de um banco de dados SQL Server

Um ponto de recuperação é criado quando o primeiro backup ocorre. Em vez de esperar o cronograma ser executado, você pode acionar manualmente a criação de um ponto de recuperação:

1. No grupo de proteção, certifique-se de que o status do banco de dados está **ok**.

    ![Um grupo de proteção, mostrando o status do banco de dados](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
1. Clique com o botão direito do mouse no banco de dados e selecione **Criar ponto de recuperação**.

    ![Escolha criar um ponto de recuperação online](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
1. No menu suspenso, selecione **Proteção on-line**. Em seguida, selecione **OK** para iniciar a criação de um ponto de recuperação no Azure.

    ![Comece a criar um ponto de recuperação no Azure](./media/backup-azure-backup-sql/sqlbackup-azure.png)
1. Você pode ver o progresso do trabalho no espaço de trabalho **de monitoramento.**

    ![Veja o progresso do trabalho no console De monitoramento](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>Recuperar um banco de dados SQL Server no Azure

Para recuperar uma entidade protegida, como um banco de dados SQL Server, do Azure:

1. Abra o console de gerenciamento do servidor DPM. Vá ao espaço de trabalho **recuperação** para ver os servidores que o DPM faça backup. Selecione o banco de dados (neste exemplo, ReportServer$MSDPM2012). Selecione um **tempo de recuperação** que termine com **Online**.

    ![Selecionar um ponto de recuperação](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
1. Clique com o botão direito do mouse no nome do banco de dados e selecione **Recuperar**.

    ![Recuperar um banco de dados do Azure](./media/backup-azure-backup-sql/sqlbackup-recover.png)
1. O DPM mostra os detalhes do ponto de recuperação. Selecione **Avançar**. Para substituir o banco de dados, selecione o tipo de recuperação **Recuperar na instância original do SQL Server**. Em seguida, selecione **Next**.

    ![Recupere um banco de dados para sua localização original](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    Neste exemplo, o DPM permite que o banco de dados seja recuperado para outra instância do SQL Server ou para uma pasta de rede autônoma.
1. Na página **Especificar opções de recuperação,** você pode selecionar as opções de recuperação. Por exemplo, você pode escolher **o estrangulamento do uso da largura de banda da rede** para acelerar a largura de banda que a recuperação usa. Em seguida, selecione **Next**.
1. Na página **Resumo,** você verá a configuração de recuperação atual. Selecione **Recuperar**.

    O estado de recuperação mostra o banco de dados sendo recuperado. Você pode selecionar **Fechar** para fechar o assistente e visualizar o progresso no espaço de trabalho **de monitoramento.**

    ![Inicie o processo de recuperação](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    Quando a recuperação é concluída, o banco de dados restaurado é consistente com o aplicativo.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte [Azure Backup FAQ](backup-azure-backup-faq.md).
