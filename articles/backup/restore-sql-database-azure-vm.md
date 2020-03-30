---
title: Restaurar bancos de dados SQL Server em uma VM Azure
description: Este artigo descreve como restaurar os bancos de dados do SQL Server que estão sendo executados em uma VM Azure e que são copiados com o Azure Backup.
ms.topic: conceptual
ms.date: 05/22/2019
ms.openlocfilehash: 642476c98ca223da01bda5c6eb79ee9b53732468
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252448"
---
# <a name="restore-sql-server-databases-on-azure-vms"></a>Restaurar bancos de dados do SQL Server em VMs do Azure

Este artigo descreve como restaurar um banco de dados do SQL Server que está sendo executado em uma máquina virtual (VM) do Azure que o serviço [de backup do Azure](backup-overview.md) fez backup em um cofre do Azure Backup Recovery Services.

Este artigo descreve como restaurar bancos de dados do SQL Server. Para obter mais informações, consulte [backup de bancos de dados sql server em VMs do Azure](backup-azure-sql-database.md).

## <a name="restore-to-a-time-or-a-recovery-point"></a>Restaurar para um tempo ou um ponto de recuperação

O Azure Backup pode restaurar os bancos de dados do SQL Server que estão sendo executados em VMs do Azure da seguinte forma:

- Restaure-a em uma data ou hora específica (para a segunda) usando backups de log de transações. O Azure Backup determina automaticamente o backup diferencial completo apropriado e a cadeia de backups de log que são necessários para restaurar com base no tempo selecionado.
- Restaure um backup completo ou diferencial específico para restaurar um ponto de recuperação específico.

## <a name="prerequisites"></a>Pré-requisitos

Antes de restaurar um banco de dados, observe o seguinte:

- Você pode restaurar o banco de dados para uma instância de um SQL Server na mesma região do Azure.
- O servidor de destino precisa ser registrado no mesmo cofre que a fonte.
- Para restaurar um banco de dados criptografado por TDE a outro Servidor SQL, você precisa primeiro [restaurar o certificado para o servidor de destino](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?view=sql-server-2017).
- Antes de restaurar o banco de dados "mestre", inicie a instância do SQL Server no modo de usuário único usando a opção inicial **-m AzureWorkloadBackup**.
  - O valor para **-m** é o nome do cliente.
  - Apenas o nome do cliente especificado pode abrir a conexão.
- Para todos os bancos de dados do sistema (modelo, mestre, msdb), pare o serviço do Agente do Servidor SQL antes de acionar a restauração.
- Feche quaisquer aplicativos que possam tentar obter uma conexão com qualquer um desses bancos de dados.
- Se você tiver várias instâncias em execução em um servidor, todas as instâncias devem estar em funcionamento, caso contrário, o servidor não apareceria na lista de servidores de destino para você restaurar o banco de dados.

## <a name="restore-a-database"></a>Restaurar um banco de dados

Para restaurar, você precisa das seguintes permissões:

- **Permissões do operador** de backup no cofre onde você está fazendo a restauração.
- Acesso de **Colaborador (gravação)** à VM de origem da qual será feito backup.
- Acesso de **Colaborador (gravação)** à VM de destino:
  - Se você está restaurando para a mesma VM, esta é a VM de origem.
  - Se você está restaurando para um local alternativo, este é o novo alvo VM.

Restaure da seguinte maneira:

1. Abra o cofre no qual a VM do SQL Server está registrada.
2. No painel do cofre, em **Uso**, selecione **Itens de Backup**.
3. Em **Itens de Backup**, em **Tipo de Gerenciamento de Backup**, selecione **SQL na VM do Azure**.

    ![Selecionar SQL na VM do Azure](./media/backup-azure-sql-database/sql-restore-backup-items.png)

4. Selecione o banco de dados a ser restaurado.

    ![Selecionar o banco de dados a ser restaurado](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

5. Examine o menu do banco de dados. Ele fornece informações sobre o backup do banco de dados, incluindo:

    - Os pontos de restauração mais antigo e mais recentes.
    - O status de backup de log das últimas 24 horas para bancos de dados que estão no modo de recuperação total e em massa e que estão configurados para backups de log transacional.

6. Selecione **Restaurar**.

    ![Selecione Restaurar](./media/backup-azure-sql-database/restore-db.png)

7. Na **configuração de restauração,** especifique onde (ou como) restaurar os dados:
   - **Localização alternativa**: Restaure o banco de dados para um local alternativo e mantenha o banco de dados de origem original.
   - **Substituir Banco de Dados**: restaure os dados para a mesma instância do SQL Server que a fonte original. Essa opção substitui o banco de dados original.

    > [!IMPORTANT]
    > Se o banco de dados selecionado pertencer a um grupo de disponibilidade Always On, o SQL Server não permitirá que o banco de dados seja substituído. Somente **Localização Alternativa** está disponível.
    >
   - **Restaurar como arquivos**: Em vez de restaurar como um banco de dados, restaure os arquivos de backup que podem ser recuperados como um banco de dados mais tarde em qualquer máquina onde os arquivos estão presentes usando o SQL Server Management Studio.
     ![Menu Restaurar Configuração](./media/backup-azure-sql-database/restore-configuration.png)

### <a name="restore-to-an-alternate-location"></a>Restaurar para um local alternativo

1. No menu **Configuração de restauração,** em **Where to Restore,** selecione **Local Alternativo**.
2. Selecione o nome do SQL Server e a instância na qual você deseja restaurar o banco de dados.
3. Na caixa **Nome do Banco de Dados Restaurado**, digite o nome do banco de dados de destino.
4. Se aplicável, selecione **Substituir se o BD com o mesmo nome já existir na instância do SQL selecionada**.
5. Selecione **OK**.

    ![Forneça valores para o menu de Configuração da Restauração](./media/backup-azure-sql-database/restore-configuration.png)

6. Em **Selecionar ponto de restauração,** selecione se deve restaurar em um ponto específico no [tempo](#restore-to-a-specific-point-in-time) ou restaurar um ponto de [recuperação específico](#restore-to-a-specific-restore-point).

    > [!NOTE]
    > A restauração point-in-time está disponível apenas para backups de log para bancos de dados que estão no modo de recuperação completo e registrado em massa.

### <a name="restore-and-overwrite"></a>Restaurar e substituir

1. No menu **Configuração de restauração,** em **Where to Restore,** selecione **Substituir DB** > **OK**.

    ![Selecione Substituir Banco de Dados](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

2. Em **Selecionar ponto de restauração,** **selecione Logs (Point in Time)** para [restaurar em um ponto específico no tempo](#restore-to-a-specific-point-in-time). Ou selecione **Diferencial de & Completo** para restaurar um ponto de recuperação [específico](#restore-to-a-specific-restore-point).

    > [!NOTE]
    > A restauração point-in-time está disponível apenas para backups de log para bancos de dados que estão no modo de recuperação completo e registrado em massa.

### <a name="restore-as-files"></a>Restaurar como arquivos

Para restaurar os dados de backup como arquivos .bak em vez de um banco de dados, escolha **Restaurar como Arquivos**. Uma vez que os arquivos são despejados em um caminho especificado, você pode levar esses arquivos para qualquer máquina onde você deseja restaurá-los como um banco de dados. Com a virtude de ser capaz de mover esses arquivos para qualquer máquina, agora você pode restaurar os dados através de assinaturas e regiões.

1. No menu **Configuração de restauração,** em **Where to Restore,** selecione **Restaurar como arquivos**.
2. Selecione o nome do SQL Server para o qual deseja restaurar os arquivos de backup.
3. No **caminho Destino no servidor, insira** o caminho da pasta no servidor selecionado na etapa 2. Este é o local onde o serviço despejará todos os arquivos de backup necessários. Normalmente, um caminho de compartilhamento de rede ou o caminho de um compartilhamento de arquivos Azure montado quando especificado como o caminho de destino, permite um acesso mais fácil a esses arquivos por outras máquinas na mesma rede ou com o mesmo compartilhamento de arquivos Azure montado neles.<BR>

    >Para restaurar os arquivos de backup do banco de dados em um Compartilhamento de Arquivos Azure montado na VM registrada no destino, certifique-se de que o NT AUTHORITY\SYSTEM tenha acesso ao compartilhamento de arquivos. Você pode executar as etapas abaixo para conceder as permissões de leitura/gravação ao AFS montado na VM:
    >
    >- Executar `PsExec -s cmd` para entrar no shell NT AUTHORITY\SYSTEM
    >   - Execute `cmdkey /add:<storageacct>.file.core.windows.net /user:AZURE\<storageacct> /pass:<storagekey>`
    >   - Verificar o acesso com`dir \\<storageacct>.file.core.windows.net\<filesharename>`
    >- Inicie uma restauração como arquivos `\\<storageacct>.file.core.windows.net\<filesharename>` do Cofre de Backup para como o caminho<BR>
    Você pode baixar Psexec via<https://docs.microsoft.com/sysinternals/downloads/psexec>

4. Selecione **OK**.

    ![Selecione Restaurar como arquivos](./media/backup-azure-sql-database/restore-as-files.png)

5. Selecione o **Ponto de Restauração** correspondente ao qual todos os arquivos .bak disponíveis serão restaurados.

    ![Selecione um ponto de restauração](./media/backup-azure-sql-database/restore-point.png)

6. Todos os arquivos de backup associados ao ponto de recuperação selecionado são despejados no caminho de destino. Você pode restaurar os arquivos como um banco de dados em qualquer máquina que eles estejam presentes usando o SQL Server Management Studio.

    ![Arquivos de backup restaurados no caminho de destino](./media/backup-azure-sql-database/sql-backup-files.png)

### <a name="restore-to-a-specific-point-in-time"></a>Restaurar a um ponto específico no tempo

Se você tiver selecionado **Logs (Pontual)** como o tipo de restauração, faça o seguinte:

1. Em **Restaurar data/hora,** abra o calendário. No calendário, as datas que possuem pontos de recuperação são exibidas no tipo negrito, e a data atual é destacada.
1. Selecione uma data que tenha pontos de recuperação. Você não pode selecionar datas que não tenham pontos de recuperação.

    ![Abra o calendário](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

1. Depois de você selecionar uma data, o gráfico de linha do tempo exibirá os pontos de recuperação disponíveis em um intervalo contínuo.
1. Especifique um tempo para a recuperação no gráfico de linha do tempo ou selecione uma hora. Em seguida, selecione **OK**.

    ![Selecione um tempo de restauração](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

1. No menu **Configuração Avançada,** se você quiser manter o banco de dados não operacional após a restauração, habilite **restaurar com NORECOVERY**.
1. Se quiser alterar a localização de restauração no servidor de destino, insira um novo caminho de destino.
1. Selecione **OK**.

    ![Menu de configuração avançada](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

1. No menu **Restaurar**, selecione **Restaurar** para iniciar o trabalho de restauração.
1. Acompanhe o progresso da restauração na área **Notificações** ou rastreie-o selecionando **Restaurar trabalhos** no menu do banco de dados.

    ![Progresso do trabalho de restauração](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-to-a-specific-restore-point"></a>Restaurar a um ponto de restauração específico

Se você tiver selecionado **Completo e Diferencial** como o tipo de restauração, faça o seguinte:

1. Selecione um ponto de recuperação da lista e selecione **OK** para concluir o procedimento de ponto de restauração.

    ![Escolha um ponto de recuperação completo](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

    >[!NOTE]
    > Por padrão, os pontos de recuperação dos últimos 30 dias são exibidos. Você pode exibir pontos de recuperação com mais de 30 dias clicando em **Filtro** e selecionando um intervalo personalizado.

1. No menu **Configuração Avançada,** se você quiser manter o banco de dados não operacional após a restauração, habilite **restaurar com NORECOVERY**.
1. Se quiser alterar a localização de restauração no servidor de destino, insira um novo caminho de destino.
1. Selecione **OK**.

    ![Menu de Configuração Avançada](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

1. No menu **Restaurar**, selecione **Restaurar** para iniciar o trabalho de restauração.
1. Acompanhe o progresso da restauração na área **Notificações** ou rastreie-o selecionando **Restaurar trabalhos** no menu do banco de dados.

    ![Progresso do trabalho de restauração](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-databases-with-large-number-of-files"></a>Restaurar bancos de dados com grande número de arquivos

Se o tamanho total da seqüência de arquivos em um banco de dados for maior que um [limite específico,](backup-sql-server-azure-troubleshoot.md#size-limit-for-files)o Azure Backup armazena a lista de arquivos de banco de dados em um componente de pit diferente, de forma que você não será capaz de definir o caminho de restauração de destino durante a operação de restauração. Os arquivos serão restaurados para o caminho padrão SQL em vez disso.

  ![Restaurar banco de dados com arquivo grande](./media/backup-azure-sql-database/restore-large-files.jpg)

## <a name="next-steps"></a>Próximas etapas

[Gerenciar e monitorar](manage-monitor-sql-database-backup.md) Bancos de dados SQL Server que são apoiados pelo Azure Backup.
