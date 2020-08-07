---
title: Configurar Sincronização de Dados SQL
description: Este tutorial mostra como configurar o Sincronização de Dados SQL para o Azure
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 01/14/2019
ms.openlocfilehash: bd1362db2e70d4f9f46d80b00805856e08aedac4
ms.sourcegitcommit: 25bb515efe62bfb8a8377293b56c3163f46122bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87987334"
---
# <a name="tutorial-set-up-sql-data-sync-between-databases-in-azure-sql-database-and-sql-server"></a>Tutorial: Configurar Sincronização de Dados SQL entre bancos de dados no banco de dados SQL do Azure e SQL Server
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Neste tutorial, você aprende a configurar o Sincronização de Dados SQL criando um grupo de sincronização que contém o banco de dados SQL do Azure e instâncias de SQL Server. O grupo de sincronização é configurado de forma personalizada e sincronizado no agendamento que você definir.

Neste tutorial, presume-se que você tenha pelo menos alguma experiência anterior com o Banco de Dados SQL e o SQL Server.

Para obter uma visão geral do Sincronização de Dados SQL, consulte [sincronizar dados entre bancos de dados locais e de nuvem com sincronização de dados SQL](sql-data-sync-data-sql-server-sql-database.md).

Para obter exemplos do PowerShell sobre como configurar Sincronização de Dados SQL, consulte [como sincronizar entre bancos de dados no banco de dados SQL](scripts/sql-data-sync-sync-data-between-sql-databases.md) ou [entre bancos de dados no banco de dados SQL do Azure e SQL Server](scripts/sql-data-sync-sync-data-between-azure-onprem.md)

> [!IMPORTANT]
> O Sincronização de Dados SQL **não dá suporte ao** Azure SQL instância gerenciada no momento.

## <a name="create-sync-group"></a>Criar grupo de sincronização

1. Vá para a [portal do Azure](https://portal.azure.com) para localizar o banco de dados no banco de dados SQL. Pesquise e selecione **bancos de dados SQL**.

    ![Pesquisar bancos de dados, portal do Microsoft Azure](./media/sql-data-sync-sql-server-configure/search-for-sql-databases.png)

1. Selecione o banco de dados que você deseja usar como o banco de dado de Hub para sincronização.

    ![Selecione na lista de bancos de dados portal do Microsoft Azure](./media/sql-data-sync-sql-server-configure/select-sql-database.png)

    > [!NOTE]
    > O banco de dados Hub é um ponto de extremidade central da topologia de sincronização, no qual um grupo de sincronização tem vários pontos de extremidades de banco de dados. Todos os outros membros do banco de dados com pontos de extremidade no grupo sincronizam com o banco de dados hub.

1. No menu do **banco de dados SQL** para o banco de dados selecionado, selecione **sincronizar com outros bancos**.

    ![Sincronizar com outros bancos de dados, portal do Microsoft Azure](./media/sql-data-sync-sql-server-configure/sync-to-other-databases.png)

1. Na página **Sincronizar com outros bancos de dados**, selecione **Novo Grupo de Sincronização**. A página **Novo grupo de sincronização** abre com o item **Criar grupo de sincronização (etapa 1)** realçado.

   ![Configurações da etapa 1](./media/sql-data-sync-sql-server-configure/stepone.png)

   Na página **Criar Grupo de Sincronização de Dados**, altere as seguintes configurações:

   | Setting                        | Descrição |
   | ------------------------------ | ------------------------------------------------- |
   | **Nome do grupo de sincronização** | Insira um nome para o novo grupo de sincronização. Esse nome é diferente do nome do banco de dados. |
   | **Sincronizar Banco de Dados de Metadados** | Escolha criar um banco de dados (recomendado) ou usar um banco de dados existente.<br/><br/>Se você escolher **Novo banco de dados**, selecione **Criar novo banco de dados**. Em seguida, na página **Banco de Dados SQL**, nomeie e configure o novo banco de dados e selecione **OK**.<br/><br/>Se você escolher **Usar banco de dados existente**, selecione o banco de dados na lista. |
   | **Sincronização automática** | Selecione **Ativar** ou **Desativar**.<br/><br/>Se você escolher **Ativar**, insira um número e selecione **Segundos**, **Minutos**, **Horas** ou **Dias** na seção **Frequência de Sincronização**.<br/> A primeira sincronização começa depois que o período de intervalo selecionado expira desde o momento em que a configuração é salva.|
   | **Resolução de conflitos** | Selecione **Hub win** ou **Membro win**.<br/><br/>**Hub win** significa que, quando ocorre um conflito, os dados no banco de dados hub substituem os dados conflitantes no banco de dados membro.<br/><br/>**Membro win** significa que, quando ocorre um conflito, os dados no banco de dados membro substituem os dados conflitantes no banco de dados hub. |

   > [!NOTE]
   > A Microsoft recomenda que você crie um novo banco de dados vazio para usar como o **banco de dados de metadados de sincronização**. A Sincronização de Dados cria tabelas nesse banco de dados e executa uma carga de trabalho frequente. Esse banco de dados é compartilhado como o **banco de dados de metadados de sincronização** para todos os grupos de sincronização em uma região e assinatura selecionada. Você não pode alterar o banco de dados ou seu nome sem remover todos os grupos de sincronização e agentes de sincronização na região.

   Selecione **OK** e aguarde até que o grupo de sincronização seja criado e implantado.

## <a name="add-sync-members"></a>Adicionar membros de sincronização

Depois que o novo grupo de sincronização for criado e implantado, o item **Adicionar membros de sincronização (etapa 2)**, ficará realçado na página **Novo grupo de sincronização**.

Na seção **banco de dados de Hub** , insira as credenciais existentes para o servidor no qual o banco de dados de Hub está localizado. Não insira *novas* credenciais nesta seção.

![Configurações da etapa 2](./media/sql-data-sync-sql-server-configure/steptwo.png)

### <a name="to-add-a-database-in-azure-sql-database"></a>Para adicionar um banco de dados no banco de dados SQL do Azure

Na seção **banco de dados membro** , opcionalmente, adicione um banco de dados no banco de dados SQL do Azure ao grupo de sincronização selecionando **Adicionar um banco de dados SQL do Azure**. A página **Configurar Banco de Dados SQL do Azure** é aberta.

  ![Etapa 2: Configurar banco de dados](./media/sql-data-sync-sql-server-configure/steptwo-configure.png)

  Na página **Configurar Banco de Dados SQL do Azure**, altere as seguintes configurações:

  | Setting                       | Descrição |
  | ----------------------------- | ------------------------------------------------- |
  | **Sincronizar Nome do Membro** | Forneça um nome para o novo membro de sincronização. Esse nome é diferente do nome do banco de dados. |
  | **Assinatura** | Selecione a assinatura associada do Azure para fins de cobrança. |
  | **Azure SQL Server** | Selecione o servidor existente. |
  | **Banco de Dados SQL do Azure** | Selecione o banco de dados existente no banco de dados SQL. |
  | **Sincronizar Trajeto** | Selecione **Sincronização Bidirecional**, **Para o Hub** ou **Do Hub**. |
  | **Nome de usuário** e **senha** | Insira as credenciais existentes para o servidor no qual o banco de dados membro está localizado. Não insira *novas* credenciais nesta seção. |

  Selecione **OK** e aguarde até que o novo membro de sincronização seja criado e implantado.

<a name="add-on-prem"></a>

### <a name="to-add-a-sql-server-database"></a>Para adicionar um banco de dados SQL Server

Na seção **banco de dados membro** , opcionalmente, adicione um banco de dados SQL Server ao grupo de sincronização selecionando **Adicionar um banco de dados local**. A página **Configurar Local** abre e nela você pode fazer o seguinte:

1. Selecione **Escolher o Gateway de Agente de Sincronização**. A página **Selecionar Agente de Sincronização** é aberta.

   ![Criar um agente de sincronização](./media/sql-data-sync-sql-server-configure/steptwo-agent.png)

1. Na página **Escolher Agente de Sincronização**, escolha se deseja usar um agente existente ou criar um agente.

   Se você escolher **Agentes existentes**, selecione o agente existente na lista.

   Se você escolher **Criar um novo agente**, faça o seguinte:

   1. Faça o download do agente de sincronização de dados pelo link fornecido e instale-o no computador em que o SQL Server está localizado. Você também pode baixar o agente diretamente do [agente de sincronização de dados SQL do Azure](https://www.microsoft.com/download/details.aspx?id=27693).

      > [!IMPORTANT]
      > Você precisa abrir a porta de saída TCP 1433 no firewall para permitir que o agente do cliente se comunique com o servidor.

   1. Insira um nome para o agente.

   1. Selecione **Criar e Gerar Chave** e copie a chave do agente para a área de transferência.

   1. Selecione **OK** para fechar a página **Selecionar o Agente de Sincronização**.

1. No computador do SQL Server, localize e execute o aplicativo Agente de Sincronização do Cliente.

   ![Os dados do aplicativo cliente do agente de sincronização de dados](./media/sql-data-sync-sql-server-configure/datasync-preview-clientagent.png)

    1. No aplicativo do agente de sincronização, selecione **Enviar Chave do Agente**. A caixa de diálogo **Configuração de Banco de Dados de Metadados de Sincronização** é aberta.

    1. Na caixa de diálogo **Configuração de Banco de Dados de Metadados de sincronização**, cole a chave do agente copiada do portal do Azure. Forneça também as credenciais existentes para o servidor no qual o banco de dados de metadados está localizado. (Se você criou um banco de dados de metadados, esse banco de dados está no mesmo servidor que o banco de dados de Hub.) Selecione **OK** e aguarde a conclusão da configuração.

        ![Insira as credenciais de chave e servidor do agente](./media/sql-data-sync-sql-server-configure/datasync-preview-agent-enterkey.png)

        > [!NOTE]
        > Se você receber um erro de firewall, crie uma regra de firewall no Azure para permitir o tráfego de entrada do computador do SQL Server. Você pode criar a regra manualmente no portal ou no SQL Server Management Studio (SSMS). No SSMS, conecte-se ao banco de dados hub no Azure inserindo o nome dele como <nome_banco_de_dados_hub>.database.windows.net.

    1. Selecione **Registrar** para registrar um banco de dados do SQL Server com o agente. A caixa de diálogo **Configuração do SQL Server** é aberta.

        ![Adicionar e configurar um banco de dados do SQL Server](./media/sql-data-sync-sql-server-configure/datasync-preview-agent-adddb.png)

    1. Na caixa de diálogo **Configuração do SQL Server**, escolha se conectar usando a autenticação do SQL Server ou a autenticação do Windows. Se você escolher a autenticação do SQL Server, insira as credenciais existentes. Forneça o nome do SQL Server e o nome do banco de dados que você deseja sincronizar e selecione **Testar conectividade** para testar as configurações. Em seguida, selecione **Salvar** e o banco de dados registrado aparece na lista.

        ![O banco de dados do SQL Server agora está registrado](./media/sql-data-sync-sql-server-configure/datasync-preview-agent-dbadded.png)

    1. Feche o aplicativo Agente de Sincronização do Cliente.

1. No portal, na página **Configurar Local**, selecione **Selecionar o Banco de Dados**.

1. Na página **Selecionar Banco de Dados**, no campo **Nome do Membro de Sincronização**, forneça um nome para o novo membro de sincronização. Esse nome é diferente do nome do banco de dados. Selecione o banco de dados na lista. No campo **Sincronizar Trajeto**, selecione **Sincronização Bidirecional**, **Para o Hub** ou **Do Hub**.

    ![Selecione o banco de dados local](./media/sql-data-sync-sql-server-configure/datasync-preview-selectdb.png)

1. Selecione **OK** para fechar a página **Selecionar Banco de Dados**. Em seguida, selecione **OK** para fechar a página **Configurar Local** e aguarde até o novo membro de sincronização ser criado e implantado. Por fim, selecione **OK** para fechar a página **Selecionar membros de sincronização**.

> [!NOTE]
> Para se conectar à Sincronização de Dados SQL e ao agente local, adicione seu nome de usuário à função *DataSync_Executor*. A Sincronização de Dados cria essa função na instância do SQL Server.

## <a name="configure-sync-group"></a>Configurar o grupo de sincronização

Depois que os novos membros do grupo de sincronização forem criados e implantados, o item **Configurar grupo de sincronização (etapa 3)**, fica realçado na página **Novo grupo de sincronização**.

![Configurações da etapa 3](./media/sql-data-sync-sql-server-configure/stepthree.png)

1. Na página **Tabelas**, selecione um banco de dados na lista de membros do grupo de sincronização e selecione **Atualizar esquema**.

1. Na lista, selecione as tabelas que você deseja sincronizar. Por padrão, todas as colunas são selecionadas, portanto, desabilite a caixa de seleção das colunas que você não deseja sincronizar. Certifique-se de deixar a coluna de chave primária selecionada.

1. Selecione **Salvar**.

1. Por padrão, os bancos de dados não são sincronizados até que ocorra uma execução agendada ou manual. Para executar uma sincronização manual, navegue até o banco de dados no banco de dados SQL no portal do Azure, selecione **sincronizar com outros bancos**de dados e selecione o grupo de sincronização. A página **Sincronização de Dados** abre. Selecione **Sincronizar**.

    ![Sincronização manual](./media/sql-data-sync-sql-server-configure/datasync-sync.png)

## <a name="faq"></a>Perguntas frequentes

**Com que frequência a Sincronização de Dados pode sincronizar meus dados?**

A duração mínima entre as sincronizações é de cinco minutos.

**A Sincronização de Dados SQL cria completamente as tabelas?**

Se estiverem faltando tabelas do esquema de sincronização no banco de dados de destino, a Sincronização de Dados SQL as cria com as colunas que você selecionou. No entanto, isso não resulta em um esquema totalmente fiel pelos seguintes motivos:

- Somente as colunas selecionadas são criadas na tabela de destino. As colunas não selecionadas serão ignoradas.
- Somente os índices de coluna selecionados são criados na tabela de destino. Com relação às colunas não selecionadas, esses índices são ignorados.
- Índices em colunas de tipo XML não são criados.
- Restrições CHECK não são criadas.
- Os gatilhos nas tabelas de origem não são criados.
- As exibições e os procedimentos armazenados não são criados.

Devido a essas limitações, recomendamos as seguintes ações:

- Para ambientes de produção, crie um esquema totalmente fiel por conta própria.
- Ao fazer experiências com o serviço, use o recurso de provisionamento automático.

**Por que vejo tabelas que não criei?**

A Sincronização de Dados cria tabelas adicionais no banco de dados para o controle de alterações. Não as exclua, pois a Sincronização de Dados deixará de funcionar.

**Meus dados são convergentes após uma sincronização?**

Não necessariamente. Considere um grupo de sincronização com um hub e três spokes (A, B e C), em que as sincronizações são: Hub para A, Hub para B e Hub para C. Se uma alteração for feita no banco de dados A *depois* da sincronização de Hub para A, essa alteração não será gravada no banco de dados B ou C até a próxima tarefa de sincronização.

**Como fazer para inserir alterações de esquema em um grupo de sincronização?**

Faça e propague todas as alterações de esquema manualmente.

1. Replique as alterações de esquema manualmente para o hub e para todos os membros de sincronização.
1. Atualize o esquema de sincronização.

Para adicionar novas tabelas e colunas:

Novas tabelas e colunas não causam impacto na sincronização atual, e a Sincronização de Dados as ignora até que sejam adicionadas ao esquema de sincronização. Ao adicionar novos objetos de banco de dados, siga esta sequência:

1. Adicione novas tabelas ou colunas ao hub e a todos os membros de sincronização.
1. Adicione novas tabelas ou colunas ao esquema de sincronização.
1. Comece a inserir os valores nas novas tabelas e colunas.

Para alterar o tipo de dados de uma coluna:

Ao alterar o tipo de dados de uma coluna existente, a Sincronização de Dados continuará funcionando, desde que os novos valores ajustem-se ao tipo de dados original definido no esquema de sincronização. Por exemplo, se você alterar o tipo no banco de dados de origem de **int** para **bigint**, a Sincronização de Dados continuará funcionando até que você insira um valor grande demais para o tipo de dados **int**. Para concluir a alteração, replique a alteração do esquema manualmente para o hub e para todos os membros de sincronização, em seguida, atualize o esquema de sincronização.

**Como posso exportar e importar um banco de dados com a Sincronização de Dados?**

Depois de exportar um banco de dados como um arquivo *.bacpac* e de importar o arquivo para criar um banco de dados, faça o seguinte para usar a Sincronização de Dados no novo banco de dados:

1. Limpe os objetos e as tabelas adicionais da Sincronização de Dados no novo banco de dados usando [este script](https://github.com/vitomaz-msft/DataSyncMetadataCleanup/blob/master/Data%20Sync%20complete%20cleanup.sql). O script exclui do banco de dados todos os objetos necessários da Sincronização de Dados.
1. Recrie o grupo de sincronização com o novo banco de dados. Se você não precisar mais do grupo de sincronização antigo, exclua-o.

**Onde posso saber mais sobre o agente cliente?**

Para perguntas frequentes sobre o agente cliente, confira [Perguntas frequentes sobre o agente](sql-data-sync-agent-overview.md#agent-faq).

## <a name="next-steps"></a>Próximas etapas

Parabéns. Você criou um grupo de sincronização que contém uma instância do Banco de Dados SQL e um banco de dados do SQL Server.

Para saber mais sobre a Sincronização de Dados SQL, veja:

- [Agente de Sincronização de Dados para a Sincronização de Dados SQL do Azure](sql-data-sync-agent-overview.md)
- [Práticas recomendadas](sql-data-sync-best-practices.md) e [Como solucionar problemas comuns com a Sincronização de Dados SQL do Azure](sql-data-sync-troubleshoot.md)
- [Monitorar a Sincronização de Dados SQL com logs do Azure Monitor](sql-data-sync-monitor-sync.md)
- [Atualizar o esquema de sincronização com o Transact-SQL](sql-data-sync-update-sync-schema.md) ou o [PowerShell](scripts/update-sync-schema-in-sync-group.md)

Para saber mais sobre o Banco de Dados SQL, veja:

- [Visão geral do Banco de Dados SQL](sql-database-paas-overview.md)
- [Gerenciamento de ciclo de vida do banco de dados](https://msdn.microsoft.com/library/jj907294.aspx)
