---
title: Restaurar bancos de dados SQL Server em uma VM do Azure
description: Este artigo descreve como restaurar SQL Server bancos de dados que estão em execução em uma VM do Azure e cujo backup é feito com o backup do Azure.
ms.topic: conceptual
ms.date: 05/22/2019
ms.openlocfilehash: 5d7fc52aaaca0bf99955919c954cc22ab0d9d3d8
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86538388"
---
# <a name="restore-sql-server-databases-on-azure-vms"></a>Restaurar bancos de dados do SQL Server em VMs do Azure

Este artigo descreve como restaurar um banco de dados SQL Server que está em execução em uma VM (máquina virtual) do Azure em que o backup do serviço [do Azure](backup-overview.md) foi feito em um cofre dos serviços de recuperação de backup do Azure.

Este artigo descreve como restaurar bancos de dados do SQL Server. Para obter mais informações, consulte [fazer backup de bancos de dados SQL Server em VMs do Azure](backup-azure-sql-database.md).

## <a name="restore-to-a-time-or-a-recovery-point"></a>Restaurar para um momento ou um ponto de recuperação

O backup do Azure pode restaurar SQL Server bancos de dados que estão em execução em VMs do Azure da seguinte maneira:

- Restaurar para uma data ou hora específica (para o segundo) usando backups de log de transações. O backup do Azure determina automaticamente o backup diferencial completo apropriado e a cadeia de backups de log que são necessários para restaurar com base na hora selecionada.
- Restaure um backup completo ou diferencial específico para restaurar para um ponto de recuperação específico.

## <a name="prerequisites"></a>Pré-requisitos

Antes de restaurar um banco de dados, observe o seguinte:

- Você pode restaurar o banco de dados para uma instância de um SQL Server na mesma região do Azure.
- O servidor de destino precisa ser registrado no mesmo cofre que a fonte.
- Para restaurar um banco de dados criptografado com TDE para outro SQL Server, primeiro você precisa [restaurar o certificado para o servidor de destino](/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server).
- Antes de restaurar o banco de dados "mestre", inicie a instância de SQL Server no modo de usuário único usando a opção de inicialização **-m AzureWorkloadBackup**.
  - O valor de **-m** é o nome do cliente.
  - Somente o nome do cliente especificado pode abrir a conexão.
- Para todos os bancos de dados do sistema (modelo, mestre, msdb), interrompa o serviço de SQL Server Agent antes de disparar a restauração.
- Feche todos os aplicativos que possam tentar fazer uma conexão com qualquer um desses bancos de dados.
- Se você tiver várias instâncias em execução em um servidor, todas as instâncias deverão estar ativas e em execução, caso contrário, o servidor não aparecerá na lista de servidores de destino para os quais você deseja restaurar o banco de dados.

## <a name="restore-a-database"></a>Restaurar um banco de dados

Para restaurar, você precisa das seguintes permissões:

- Permissões de **operador de backup** no cofre em que você está fazendo a restauração.
- Acesso de **Colaborador (gravação)** à VM de origem da qual será feito backup.
- Acesso de **Colaborador (gravação)** à VM de destino:
  - Se você estiver restaurando para a mesma VM, esta é a VM de origem.
  - Se você estiver restaurando para um local alternativo, essa será a nova VM de destino.

Restaure da seguinte maneira:

1. Abra o cofre no qual a VM do SQL Server está registrada.
2. No painel do cofre, em **Uso**, selecione **Itens de Backup**.
3. Em **Itens de Backup**, em **Tipo de Gerenciamento de Backup**, selecione **SQL na VM do Azure**.

    ![Selecionar SQL na VM do Azure](./media/backup-azure-sql-database/sql-restore-backup-items.png)

4. Selecione o banco de dados a ser restaurado.

    ![Selecionar o banco de dados a ser restaurado](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

5. Examine o menu do banco de dados. Ele fornece informações sobre o backup do banco de dados, incluindo:

    - Os pontos de restauração mais antigo e mais recentes.
    - O status de backup de log das últimas 24 horas para bancos de dados que estão no modo de recuperação completa e bulk-logged e configurados para backups de log transacionais.

6. Selecione **Restaurar**.

    ![Selecione Restaurar](./media/backup-azure-sql-database/restore-db.png)

7. Em **Restaurar configuração**, especifique onde (ou como) restaurar os dados:
   - **Local alternativo**: restaure o banco de dados para um local alternativo e mantenha o banco de dados de origem original.
   - **Substituir Banco de Dados**: restaure os dados para a mesma instância do SQL Server que a fonte original. Essa opção substitui o banco de dados original.

        > [!IMPORTANT]
        > Se o banco de dados selecionado pertencer a um grupo de disponibilidade Always On, o SQL Server não permitirá que o banco de dados seja substituído. Somente **Localização Alternativa** está disponível.
        >
   - **Restaurar como arquivos**: em vez de restaurar como um banco de dados, restaure os arquivos de backup que podem ser recuperados como um banco de dados posteriormente em qualquer computador em que os arquivos estejam presentes usando SQL Server Management Studio.
     ![Menu Restaurar Configuração](./media/backup-azure-sql-database/restore-configuration.png)

### <a name="restore-to-an-alternate-location"></a>Restaurar para um local alternativo

1. No menu **configuração de restauração** , em **onde restaurar**, selecione **local alternativo**.
1. Selecione o nome do SQL Server e a instância na qual você deseja restaurar o banco de dados.
1. Na caixa **Nome do Banco de Dados Restaurado**, digite o nome do banco de dados de destino.
1. Se aplicável, selecione **Substituir se o BD com o mesmo nome já existir na instância do SQL selecionada**.
1. Selecione **ponto de restauração**e selecione se deseja [restaurar para um ponto específico no tempo](#restore-to-a-specific-point-in-time) ou para [restaurar para um ponto de recuperação específico](#restore-to-a-specific-restore-point).

    ![Selecionar ponto de restauração](./media/backup-azure-sql-database/select-restore-point.png)

    ![Restaurar para o ponto no tempo](./media/backup-azure-sql-database/restore-to-point-in-time.png)

1. No menu **Configuração Avançada**:

    - Se você quiser manter o banco de dados não operacional após a restauração, habilite **RESTORE WITH NORECOVERY**.
    - Se você quiser alterar o local de restauração no servidor de destino, insira novos caminhos de destino.

        ![Inserir caminhos de destino](./media/backup-azure-sql-database/target-paths.png)

1. Clique em **OK** para disparar a restauração. Acompanhe o progresso da restauração na área **notificações** ou acompanhe-a na exibição **trabalhos de backup** no cofre.

    > [!NOTE]
    > A restauração pontual está disponível apenas para backups de log de bancos de dados que estão no modo de recuperação completa e bulk-logged.

### <a name="restore-and-overwrite"></a>Restaurar e substituir

1. No menu **configuração de restauração** , em **onde restaurar**, selecione **substituir DB**  >  **OK**.

    ![Selecione Substituir Banco de Dados](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

2. Em **selecionar ponto de restauração**, selecione **logs (ponto no tempo)** para [restaurar para um ponto específico no tempo](#restore-to-a-specific-point-in-time). Ou selecione a **& total diferencial** para restaurar para um [ponto de recuperação específico](#restore-to-a-specific-restore-point).

    > [!NOTE]
    > A restauração pontual está disponível apenas para backups de log de bancos de dados que estão no modo de recuperação completa e bulk-logged.

### <a name="restore-as-files"></a>Restaurar como arquivos

Para restaurar os dados de backup como arquivos. bak, em vez de em um banco de dados, escolha **restaurar como arquivos**. Depois que os arquivos são despejados em um caminho especificado, você pode pegar esses arquivos em qualquer computador em que deseja restaurá-los como um banco de dados. Como você pode mover esses arquivos para qualquer computador, agora é possível restaurar os dados entre assinaturas e regiões.

1. Em **onde e como restaurar**, selecione **restaurar como arquivos**.
1. Selecione o nome de SQL Server para o qual você deseja restaurar os arquivos de backup.
1. No **caminho de destino no servidor** , insira o caminho da pasta no servidor selecionado na etapa 2. Esse é o local onde o serviço irá despejar todos os arquivos de backup necessários. Normalmente, um caminho de compartilhamento de rede (ou caminho de um compartilhamento de arquivo do Azure montado quando especificado como o caminho de destino) permite o acesso mais fácil a esses arquivos por outros computadores na mesma rede ou com o mesmo compartilhamento de arquivo do Azure montado neles.<BR>

    >Para restaurar os arquivos de backup de banco de dados em um compartilhamento de arquivos do Azure montado na VM registrada de destino, verifique se NT AUTHORITY\SYSTEM tem acesso ao compartilhamento de arquivos. Você pode executar as etapas fornecidas abaixo para conceder as permissões de leitura/gravação para o AFS montado na VM:
    >
    >- Executar `PsExec -s cmd` para entrar no Shell NT AUTHORITY\SYSTEM
    >   - Execute `cmdkey /add:<storageacct>.file.core.windows.net /user:AZURE\<storageacct> /pass:<storagekey>`
    >   - Verificar o acesso com`dir \\<storageacct>.file.core.windows.net\<filesharename>`
    >- Disparar uma restauração como arquivos do cofre de backup para `\\<storageacct>.file.core.windows.net\<filesharename>` como o caminho<BR>
    Você pode baixar o PsExec via<https://docs.microsoft.com/sysinternals/downloads/psexec>

1. Selecione **OK**.

    ![Selecione restaurar como arquivos](./media/backup-azure-sql-database/restore-as-files.png)

1. Selecione **ponto de restauração**e selecione se deseja [restaurar para um ponto específico no tempo](#restore-to-a-specific-point-in-time) ou para [restaurar para um ponto de recuperação específico](#restore-to-a-specific-restore-point).

1. Todos os arquivos de backup associados ao ponto de recuperação selecionado são despejados no caminho de destino. Você pode restaurar os arquivos como um banco de dados em qualquer computador em que estejam presentes usando SQL Server Management Studio.

    ![Arquivos de backup restaurados no caminho de destino](./media/backup-azure-sql-database/sql-backup-files.png)

### <a name="restore-to-a-specific-point-in-time"></a>Restaurar a um ponto específico no tempo

Se você tiver selecionado **Logs (Pontual)** como o tipo de restauração, faça o seguinte:

1. Em **restaurar data/hora**, abra o calendário. No calendário, as datas que têm pontos de recuperação são exibidas em negrito e a data atual é realçada.
1. Selecione uma data que tenha pontos de recuperação. Não é possível selecionar datas que não tenham pontos de recuperação.

    ![Abrir o calendário](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

1. Depois de você selecionar uma data, o gráfico de linha do tempo exibirá os pontos de recuperação disponíveis em um intervalo contínuo.
1. Especifique uma hora para a recuperação no grafo da linha do tempo ou selecione uma hora. Em seguida, selecione **OK**.

### <a name="restore-to-a-specific-restore-point"></a>Restaurar a um ponto de restauração específico

Se você tiver selecionado **Completo e Diferencial** como o tipo de restauração, faça o seguinte:

1. Selecione um ponto de recuperação da lista e selecione **OK** para concluir o procedimento de ponto de restauração.

    ![Escolha um ponto de recuperação completo](./media/backup-azure-sql-database/choose-full-recovery-point.png)

    >[!NOTE]
    > Por padrão, os pontos de recuperação dos últimos 30 dias são exibidos. Você pode exibir pontos de recuperação com mais de 30 dias clicando em **Filtrar** e selecionando um intervalo personalizado.

### <a name="restore-databases-with-large-number-of-files"></a>Restaurar bancos de dados com um grande número de arquivos

Se o tamanho total da cadeia de caracteres de arquivos em um banco de dados for maior que um [limite específico](backup-sql-server-azure-troubleshoot.md#size-limit-for-files), o backup do Azure armazenará a lista de arquivos de banco de dados em um componente Pit diferente, de modo que você não pode definir o caminho de restauração de destino durante a operação de restauração. Em vez disso, os arquivos serão restaurados no caminho padrão do SQL.

  ![Restaurar banco de dados com arquivo grande](./media/backup-azure-sql-database/restore-large-files.jpg)

## <a name="next-steps"></a>Próximas etapas

[Gerenciar e monitorar](manage-monitor-sql-database-backup.md) SQL Server bancos de dados que são submetidos a backup pelo backup do Azure.
