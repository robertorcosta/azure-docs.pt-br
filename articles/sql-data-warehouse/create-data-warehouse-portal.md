---
title: Criar e consultar um data warehouse (portal do Azure)
description: Criar e consultar um pool do SQL do Azure Synapse Analytics usando o portal do Azure
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: development
ms.date: 05/28/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 7a3dbe5d74dc1e88d0615937b8c6e6d2a77b64a7
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78381084"
---
# <a name="quickstart-create-and-query-an-azure-synapse-analytics-sql-pool-using-the-azure-portal"></a>Início rápido: criar e consultar um pool do SQL do Azure Synapse Analytics usando o portal do Azure

Crie e consulte rapidamente um data warehouse Provisionando o pool SQL no Azure Synapse Analytics (anteriormente conhecido como SQL DW) usando o portal do Azure.

## <a name="prerequisites"></a>{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}

1. Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

   > [!NOTE]
   > A criação de um pool SQL no Azure Synapse pode resultar em um novo serviço faturável. Para obter mais informações, consulte [preços do Azure Synapse Analytics](https://azure.microsoft.com/pricing/details/synapse-analytics/).

2. Baixe e instale a versão mais recente do [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [portal do Azure](https://portal.azure.com/).

## <a name="create-a-sql-pool"></a>Criar um pool do SQL

Os data warehouses são criados usando o pool do SQL no Azure Synapse Analytics. Um pool do SQL é criado com um conjunto definido de [recursos de computação](memory-concurrency-limits.md). O banco de dados é criado dentro de um [grupo de recursos do Azure](../azure-resource-manager/management/overview.md) e em um [servidor lógico SQL do Azure](../sql-database/sql-database-servers.md).

Siga estas etapas para criar um data warehouse que contém os dados de exemplo **AdventureWorksDW** .

1. Selecione **criar um recurso** no canto superior esquerdo do portal do Azure.

   ![criar um recurso no portal do Azure](media/create-data-warehouse-portal/create-a-resource.png)

2. Selecione **bancos de dados** na **nova** página e selecione **Azure Synapse Analytics (anteriormente conhecido como SQL DW)** na lista em **destaque** .

   ![criar data warehouse vazio](media/create-data-warehouse-portal/create-a-data-warehouse.png)

3. Em **noções básicas**, forneça sua assinatura, grupo de recursos, nome do pool SQL e nome do servidor:

   | Configuração | Valor sugerido | Descrição |
   | :------ | :-------------- | :---------- |
   | **Assinatura** | Sua assinatura | Para obter detalhes sobre suas assinaturas, consulte [Assinaturas](https://account.windowsazure.com/Subscriptions). |
   | **Grupo de recursos** | myResourceGroup | Para ver os nomes do grupo de recursos válidos, consulte [Regras e restrições de nomenclatura](/azure/architecture/best-practices/resource-naming). |
   | **Nome do data warehouse** | Qualquer nome globalmente exclusivo (um exemplo é *mySampleDataWarehouse*) | Para ver os nomes do banco de dados válidos, consulte [Identificadores do Banco de Dados](/sql/relational-databases/databases/database-identifiers). Observe que um data warehouse é um tipo de banco de dados. |
   | **Servidor** | Qualquer nome exclusivo globalmente | Selecione servidor existente ou crie um novo nome de servidor, selecione **criar novo**. Para ver os nomes do servidor válidos, consulte [Regras e restrições de nomenclatura](/azure/architecture/best-practices/resource-naming). |

   ![criar um data warehouse detalhes básicos](media/create-data-warehouse-portal/create-sql-pool-basics.png)

4. Em **nível de desempenho**, selecione **selecionar nível de desempenho** para, opcionalmente, alterar sua configuração com um controle deslizante.

   ![alterar o nível de desempenho data warehouse](media/create-data-warehouse-portal/create-sql-pool-performance-level.png)  

   Para obter mais informações sobre os níveis de desempenho, consulte [gerenciar computação no Azure SQL data warehouse](sql-data-warehouse-manage-compute-overview.md).

5. Agora que você concluiu a guia noções básicas do formulário do Azure Synapse Analytics, selecione **revisar + criar** e **criar** para criar o data warehouse no pool do SQL. O provisionamento demora alguns minutos.

   ![Selecione revisão + criar](media/create-data-warehouse-portal/create-sql-pool-review-create.png)

   ![Selecione Criar](media/create-data-warehouse-portal/create-sql-pool-create.png)

6. Na barra de ferramentas, selecione **notificações** para monitorar o processo de implantação.

   ![(notificação)](media/create-data-warehouse-portal/notification.png)

## <a name="create-a-server-level-firewall-rule"></a>Criar uma regra de firewall no nível de servidor

O serviço Synapse do Azure cria um firewall no nível do servidor. Esse firewall impede que aplicativos e ferramentas externas se conectem ao servidor ou a quaisquer bancos de dados no servidor. Para habilitar a conectividade, é possível adicionar regras de firewall que habilitem a conectividade para endereços IP específicos. Siga estas etapas para criar uma [regra de firewall de nível de servidor](../sql-database/sql-database-firewall-configure.md) para o endereço IP do seu cliente.

> [!NOTE]
> O Azure Synapse se comunica pela porta 1433. Se você estiver tentando conectar-se de dentro de uma rede corporativa, o tráfego de saída pela porta 1433 talvez não seja permitido pelo firewall de sua rede. Se isto acontecer, você não poderá conectar o servidor do Banco de Dados SQL do Azure, a menos que o departamento de TI abra a porta 1433.

1. Após a conclusão da implantação, selecione **Todos os serviços** no menu à esquerda. Selecione **bancos de dados**, selecione a estrela ao lado de **Azure Synapse Analytics** para adicionar o Azure Synapse Analytics aos seus favoritos.

2. Selecione **Azure Synapse Analytics** no menu à esquerda e, em seguida, selecione **mySampleDataWarehouse** na página de **análise do Azure Synapse** . A página Visão geral do seu banco de dados é aberta, mostrando o nome totalmente qualificado do servidor (como **sqlpoolservername.Database.Windows.net**) e fornece opções para configuração adicional.

3. Copie esse nome do servidor totalmente qualificado para se conectar ao servidor e a seus bancos de dados neste e nos próximos inícios rápidos. Para abrir as configurações do servidor, selecione o nome do servidor.

   ![localizar o nome do servidor](media/create-data-warehouse-portal/find-server-name.png)

4. Selecione **Mostrar configurações de firewall**.

   ![configurações do servidor](media/create-data-warehouse-portal/server-settings.png)

5. A página **Configurações do firewall** do servidor do Banco de Dados SQL é aberta.

   ![regra de firewall do servidor](media/create-data-warehouse-portal/server-firewall-rule.png)

6. Para adicionar o endereço IP atual a uma nova regra de firewall, selecione **Adicionar IP do cliente** na barra de ferramentas. Uma regra de firewall pode abrir a porta 1433 para um único endereço IP ou um intervalo de endereços IP.

7. Selecione **Salvar**. Uma regra de firewall no nível do servidor é criada para a porta de abertura 1433 de seu endereço IP atual no servidor lógico.

8. Selecione **OK** e feche a página **configurações de firewall** .

Agora é possível conectar-se ao SQL Server e a seus data warehouses usando este endereço IP. A conexão funciona no SQL Server Management Studio ou em outra ferramenta de sua escolha. Quando você se conectar, use a conta ServerAdmin criada anteriormente.

> [!IMPORTANT]
> Por padrão, o acesso através do firewall do Banco de Dados SQL está habilitado para todos os serviços do Azure. Selecione **desativado** nesta página e, em seguida, selecione **salvar** para desabilitar o firewall para todos os serviços do Azure.

## <a name="get-the-fully-qualified-server-name"></a>Obter o nome do servidor totalmente qualificado

Obtenha o nome do servidor totalmente qualificado para seu SQL Server no Portal do Azure. Posteriormente, você usará o nome totalmente qualificado ao se conectar ao servidor.

1. Entre no [portal do Azure](https://portal.azure.com/).

2. Selecione **Azure Synapse Analytics** no menu à esquerda e selecione seu data warehouse na página de **análise Synapse do Azure** .

3. No painel **Essentials**, na página do Portal do Azure de seu banco de dados, localize e copie o **Nome do servidor**. Neste exemplo, o nome totalmente qualificado é sqlpoolservername.database.windows.net.

    ![informações da conexão](media/create-data-warehouse-portal/find-server-name-copy.png)

## <a name="connect-to-the-server-as-server-admin"></a>Conectar-se ao servidor como administrador do servidor

Esta seção usa o [SSMS](/sql/ssms/download-sql-server-management-studio-ssms) (SQL Server Management Studio) para estabelecer uma conexão com o SQL Server do Azure.

1. Abra o SQL Server Management Studio.

2. Na caixa de diálogo **Conectar ao Servidor**, insira as informações a seguir:

   | Configuração | Valor sugerido | Descrição |
   | :------ | :-------------- | :---------- |
   | Tipo de servidor | Mecanismo de banco de dados | Esse valor é obrigatório |
   | Nome do servidor | O nome do servidor totalmente qualificado | Aqui está um exemplo: **sqlpoolservername.Database.Windows.net**. |
   | Autenticação | Autenticação do SQL Server | A Autenticação do SQL é o único tipo de autenticação configurado neste tutorial. |
   | Login | A conta do administrador do servidor | A conta que você especificou quando criou o servidor. |
   | Senha | A senha para sua conta do administrador do servidor | A senha que você especificou quando criou o servidor. |
   ||||

   ![conectar-se ao servidor](media/create-data-warehouse-portal/connect-to-server-ssms.png)

3. Selecione **Conectar**. A janela Pesquisador de Objetos será aberta no SSMS. 

4. No Pesquisador de Objetos, expanda **Bancos de Dados**. Em seguida, expanda **meuBancoDeDadosDeExemplo** para exibir os objetos no novo banco de dados.

   ![objetos de banco de dados](media/create-data-warehouse-portal/connected-ssms.png) 

## <a name="run-some-queries"></a>Executar algumas consultas

O SQL Data Warehouse usa T-SQL como a linguagem de consulta. Para abrir uma janela de consulta e executar algumas consultas T-SQL, use as seguintes etapas:

1. Clique com o botão direito do mouse em **mySampleDataWarehouse** e selecione **nova consulta**. Uma janela de nova consulta é aberta.

2. Na janela de consulta, digite o seguinte comando para ver uma lista de bancos de dados.

    ```sql
    SELECT * FROM sys.databases
    ```

3. Selecione **executar**. Os resultados da consulta mostram dois bancos de dados: **mestre** e **meuDataWarehouseDeExemplo**.

   ![Bancos de dados de consulta](media/create-data-warehouse-portal/query-databases.png)

4. Para examinar alguns dados, use o comando a seguir para ver o número de clientes com o sobrenome Alves com três filhos em casa. Os resultados listam seis clientes. 

    ```sql
    SELECT LastName, FirstName FROM dbo.dimCustomer
    WHERE LastName = 'Adams' AND NumberChildrenAtHome = 3;
    ```

   ![Consulta dbo.dimCustomer](media/create-data-warehouse-portal/query-customer.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Você está sendo cobrado por unidades de data warehouse e pelos dados armazenados em seu data warehouse. Esses recursos de computação e armazenamento são cobrados separadamente.

- Se desejar manter os dados no armazenamento, será possível pausar a computação quando você não estiver usando o data warehouse. Ao pausar a computação, você será cobrado apenas pelo armazenamento de dados. Você poderá retomar a computação sempre que estiver pronto para trabalhar com os dados.

- Se desejar remover encargos futuros, será possível excluir o data warehouse.

Execute estas etapas para limpar os recursos desnecessários.

1. Entre no [portal do Azure](https://portal.azure.com) e selecione seu data warehouse.

   ![Limpar os recursos](media/create-data-warehouse-portal/clean-up-resources.png)

2. Para pausar a computação, selecione o botão **Pausar**. Quando o data warehouse for pausado, você verá um botão **Retomar**. Para retomar a computação, selecione **retomar**.

3. Para remover o data warehouse para que você não seja cobrado pela computação ou pelo armazenamento, selecione **excluir**.

4. Para remover o SQL Server que você criou, selecione **sqlpoolservername.Database.Windows.net** na imagem anterior e, em seguida, selecione **excluir**. Tenha cuidado com essa exclusão, uma vez que a exclusão do servidor também exclui todos os bancos de dados atribuídos ao servidor.

5. Para remover o grupo de recursos, selecione **myResourceGroup** e, em seguida, **Excluir grupo de recursos**.

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

Agora você criou um data warehouse, criou uma regra de firewall, conectou-se ao data warehouse e executou algumas consultas. Para saber mais sobre o SQL Data Warehouse do Azure, prossiga para o tutorial de carregamento de dados.

> [!div class="nextstepaction"]
> [Carregar dados em um SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md)
