---
title: 'Tutorial: carregar dados de dos táxis de Nova York'
description: O tutorial usa portal do Azure e SQL Server Management Studio para carregar dados de dos táxis de Nova York de um blob do Azure para Synapse SQL.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 05/31/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: c4dbc63e8829d8a9ca3a3820fbb6675da4fad357
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85207542"
---
# <a name="tutorial-load-the-new-york-taxicab-dataset"></a>Tutorial: carregar o conjunto de dos táxis de Nova York

Este tutorial usa a [instrução Copy](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) para carregar o conjunto de dos táxis New York de uma conta de armazenamento de BLOBs do Azure. Este tutorial usa o [Portal do Azure](https://portal.azure.com) e o [SSMS](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (SQL Server Management Studio) para:

> [!div class="checklist"]
>
> * Criar um pool SQL no portal do Azure
> * Configurar uma regra de firewall de nível de servidor no Portal do Azure
> * Conectar-se ao data warehouse com o SSMS
> * Criar um usuário designado para carregar dados
> * Criar as tabelas para o conjunto de exemplos 
> * Use a instrução COPY T-SQL para carregar dados em seu data warehouse
> * Exibir o andamento dos dados enquanto estão sendo carregados

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="before-you-begin"></a>Antes de começar

Antes de iniciar este tutorial, baixe e instale a versão mais recente do [SSMS](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (SQL Server Management Studio).

## <a name="log-in-to-the-azure-portal"></a>Faça logon no Portal do Azure

Faça logon no [Portal do Azure](https://portal.azure.com/).

## <a name="create-a-blank-database"></a>Criar um banco de dados vazio

Um pool de SQL é criado com um conjunto definido de [recursos de computação](memory-concurrency-limits.md). O banco de dados é criado dentro de um [grupo de recursos do Azure](../../azure-resource-manager/management/overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) e em um [servidor SQL lógico](../../azure-sql/database/logical-servers.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

Siga estas etapas para criar um banco de dados em branco.

1. Selecione **Criar um recurso** no canto superior esquerdo do portal do Azure.

2. Selecione **bancos de dados** na página **novo** e selecione análise de **Synapse do Azure** em **destaque** na página **novo** .

    ![criar data warehouse](./media/load-data-from-azure-blob-storage-using-polybase/create-empty-data-warehouse.png)

3. Preencha o formulário com as seguintes informações:

   | Configuração            | Valor sugerido       | Descrição                                                  |
   | ------------------ | --------------------- | ------------------------------------------------------------ |
   | *Nomes**            | meuDataWarehouseDeExemplo | Para ver os nomes do banco de dados válidos, consulte [Identificadores do Banco de Dados](/sql/relational-databases/databases/database-identifiers?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest). |
   | **Assinatura**   | Sua assinatura     | Para obter detalhes sobre suas assinaturas, consulte [Assinaturas](https://account.windowsazure.com/Subscriptions). |
   | **Grupo de recursos** | myResourceGroup       | Para ver os nomes do grupo de recursos válidos, consulte [Regras e restrições de nomenclatura](/azure/architecture/best-practices/resource-naming?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). |
   | **Selecionar fonte**  | Banco de dados em branco        | Especifique para criar um banco de dados em branco. Observe que um data warehouse é um tipo de banco de dados. |

    ![criar data warehouse](./media/load-data-from-azure-blob-storage-using-polybase/create-data-warehouse.png)

4. Selecione **Servidor** para criar e configurar um servidor para o novo banco de dados. Preencha o **formulário Novo servidor** com as seguintes informações:

    | Configuração                | Valor sugerido          | Descrição                                                  |
    | ---------------------- | ------------------------ | ------------------------------------------------------------ |
    | **Nome do servidor**        | Qualquer nome exclusivo globalmente | Para ver os nomes do servidor válidos, consulte [Regras e restrições de nomenclatura](/azure/architecture/best-practices/resource-naming?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). |
    | **Logon de administrador do servidor** | Qualquer nome válido           | Para ver os nomes de logon válidos, consulte [Identificadores do Banco de Dados](/sql/relational-databases/databases/database-identifiers?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest). |
    | **Senha**           | Qualquer senha válida       | Sua senha deve ter, pelo menos, oito caracteres e deve conter caracteres de três das seguintes categorias: caracteres com letras maiúsculas, letras minúsculas, números e caracteres não alfanuméricos. |
    | **Localidade**           | Qualquer local válido       | Para obter mais informações sobre as regiões, consulte [Regiões do Azure](https://azure.microsoft.com/regions/). |

    ![criar servidor](./media/load-data-from-azure-blob-storage-using-polybase/create-database-server.png)

5. Selecione **Selecionar**.

6. Selecione **nível de desempenho** para especificar se o data warehouse é Gen1 ou Gen2 e o número de unidades de data warehouse.

7. Para este tutorial, selecione pool do SQL **Gen2**. O controle deslizante é definido como **DW1000c** por padrão.  Experimente movê-lo para cima e para baixo para ver como ele funciona.

    ![configurar o desempenho](./media/load-data-from-azure-blob-storage-using-polybase/configure-performance.png)

8. Escolha **Aplicar**.
9. Na folha provisionamento, selecione um **agrupamento** para o banco de dados em branco. Neste tutorial, use o valor padrão. Para obter mais informações sobre ordenações, confira [Ordenações](/sql/t-sql/statements/collations?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

10. Agora que você concluiu o formulário, selecione **criar** para provisionar o banco de dados. O provisionamento demora alguns minutos.

11. Na barra de ferramentas, selecione **Notificações** para monitorar o processo de implantação.
  
     ![notificação](./media/load-data-from-azure-blob-storage-using-polybase/notification.png)

## <a name="create-a-server-level-firewall-rule"></a>Criar uma regra de firewall no nível de servidor

Um firewall no nível de servidor que impede que aplicativos e ferramentas externos se conectem ao servidor ou a qualquer banco de dados no servidor. Para habilitar a conectividade, é possível adicionar regras de firewall que habilitem a conectividade para endereços IP específicos.  Siga estas etapas para criar uma [regra de firewall de nível de servidor](../../azure-sql/database/firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) para o endereço IP do seu cliente.

> [!NOTE]
> O SQL Data Warehouse comunica-se pela porta 1433. Se você estiver tentando conectar-se de dentro de uma rede corporativa, o tráfego de saída pela porta 1433 talvez não seja permitido pelo firewall de sua rede. Se isso acontecer, você não conseguirá se conectar ao servidor, a menos que o departamento de TI abra a porta 1433.

1. Após a conclusão da implantação, selecione **bancos de dados SQL** no menu à esquerda e, em seguida, selecione **mySampleDatabase** na página **bancos de dados SQL** . A página de visão geral de seu banco de dados é aberta, mostrando o nome do servidor totalmente qualificado (como **meunovoservidor-20180430.database.windows.net**) e fornece opções para configurações adicionais.

2. Copie esse nome do servidor totalmente qualificado para se conectar ao servidor e a seus bancos de dados nos próximos guias de início rápido. Em seguida, selecione o nome do servidor para abrir as configurações do servidor.

    ![localizar o nome do servidor](././media/load-data-from-azure-blob-storage-using-polybase/find-server-name.png)

3. Selecione o nome do servidor para abrir as configurações do servidor.

    ![configurações do servidor](./media/load-data-from-azure-blob-storage-using-polybase/server-settings.png)

4. Selecione **Mostrar configurações de firewall**. A página **Configurações do firewall** do servidor será aberta.

    ![regra de firewall do servidor](./media/load-data-from-azure-blob-storage-using-polybase/server-firewall-rule.png)

5. Selecione **Adicionar IP do cliente** na barra de ferramentas para adicionar seu endereço IP atual a uma nova regra de firewall. Uma regra de firewall pode abrir a porta 1433 para um único endereço IP ou um intervalo de endereços IP.

6. Selecione **Salvar**. Uma regra de firewall no nível do servidor é criada para o endereço IP atual que abre a porta 1433 no servidor lógico.

7. Selecione **OK** e feche a página **configurações de firewall** .

Agora você pode se conectar ao servidor e a seus data warehouses usando esse endereço IP. A conexão funciona no SQL Server Management Studio ou em outra ferramenta de sua escolha. Quando você se conectar, use a conta ServerAdmin criada anteriormente.  

> [!IMPORTANT]
> Por padrão, o acesso através do firewall do Banco de Dados SQL está habilitado para todos os serviços do Azure. Selecione **desativado** nesta página e, em seguida, selecione **salvar** para desabilitar o firewall para todos os serviços do Azure.

## <a name="get-the-fully-qualified-server-name"></a>Obter o nome do servidor totalmente qualificado

Obtenha o nome do servidor totalmente qualificado para o seu servidor no portal do Azure. Posteriormente, você usará o nome totalmente qualificado ao se conectar ao servidor.

1. Faça logon no [Portal do Azure](https://portal.azure.com/).
2. Selecione **Azure Synapse Analytics** no menu à esquerda e selecione seu banco de dados na página de **análise de Synapse do Azure** .
3. No painel **Essentials**, na página do Portal do Azure de seu banco de dados, localize e copie o **Nome do servidor**. Neste exemplo, o nome totalmente qualificado é meunovoservidor-20180430.database.windows.net.

    ![informações da conexão](././media/load-data-from-azure-blob-storage-using-polybase/find-server-name.png)  

## <a name="connect-to-the-server-as-server-admin"></a>Conectar-se ao servidor como administrador do servidor

Esta seção usa o [SSMS](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (SQL Server Management Studio) para estabelecer uma conexão com o servidor.

1. Abra o SQL Server Management Studio.

2. Na caixa de diálogo **Conectar ao Servidor**, insira as informações a seguir:

    | Configuração        | Valor sugerido                            | Descrição                                                  |
    | -------------- | ------------------------------------------ | ------------------------------------------------------------ |
    | Tipo de servidor    | Mecanismo de banco de dados                            | Esse valor é obrigatório                                       |
    | Nome do servidor    | O nome do servidor totalmente qualificado            | O nome deve ser semelhante a este: **mynewserver-20180430.database.windows.net**. |
    | Autenticação | Autenticação do SQL Server                  | A Autenticação do SQL é o único tipo de autenticação que configuramos neste tutorial. |
    | Logon          | A conta do administrador do servidor                   | Esta é a conta que você especificou quando criou o servidor. |
    | Senha       | A senha para sua conta do administrador do servidor | Esta é a senha que você especificou quando criou o servidor. |

    ![conectar-se ao servidor](./media/load-data-from-azure-blob-storage-using-polybase/connect-to-server.png)

3. Selecione **Conectar**. A janela Pesquisador de Objetos será aberta no SSMS.

4. No Pesquisador de Objetos, expanda **Bancos de Dados**. Em seguida, expanda **Bancos de dados do sistema** e **mestre** para exibir os objetos no banco de dados mestre.  Expanda **mySampleDatabase** para exibir os objetos no novo banco de dados.

    ![objetos de banco de dados](./media/load-data-from-azure-blob-storage-using-polybase/connected.png)

## <a name="create-a-user-for-loading-data"></a>Criar um usuário para carregar dados

A conta do administrador do servidor é destinada a executar operações de gerenciamento, e não é adequada para executar consultas nos dados do usuário. O carregamento de dados é uma operação com uso intensivo de memória. Os máximos de memória são definidos de acordo com as [unidades de data warehouse](what-is-a-data-warehouse-unit-dwu-cdwu.md) e a [classe de recurso](resource-classes-for-workload-management.md) configurada.

É melhor criar um logon e um usuário dedicados para carregar dados. Em seguida, adicione o usuário carregado a uma [classe de recurso](resource-classes-for-workload-management.md) que permita uma alocação máxima de memória adequada.

Como você está, no momento, conectado como o administrador do servidor, é possível criar logons e usuários. Use estas etapas para criar um logon e um usuário chamado **LoaderRC20**. Em seguida, atribua o usuário à classe de recurso **staticrc20**.

1. No SSMS, selecione **mestre** ao lado do botão direito para mostrar um menu suspenso e escolha **nova consulta**. Uma janela de nova consulta é aberta.

    ![Nova consulta no mestre](./media/load-data-from-azure-blob-storage-using-polybase/create-loader-login.png)

2. Na janela de consulta, insira esses comandos T-SQL para criar um logon e um usuário chamado LoaderRC20, substituindo sua própria senha para 'a123STRONGpassword!'.

    ```sql
    CREATE LOGIN LoaderRC20 WITH PASSWORD = 'a123STRONGpassword!';
    CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
    ```

3. Selecione **Executar**.

4. Clique com o botão direito do mouse em **mySampleDataWarehouse** e escolha **Nova consulta**. Uma nova janela de consulta é aberta.  

    ![Nova consulta no data warehouse de exemplo](./media/load-data-from-azure-blob-storage-using-polybase/create-loading-user.png)

5. Insira os comandos T-SQL a seguir para criar um usuário de banco de dados chamado LoaderRC20 para o logon LoaderRC20. A segunda linha concede ao novo usuário permissões de CONTROLE sobre o novo data warehouse.  Essas permissões são semelhantes para tornar o usuário o proprietário do banco de dados. A terceira linha adiciona o novo usuário como um membro da [classe de recurso](resource-classes-for-workload-management.md) staticrc20.

    ```sql
    CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
    GRANT CONTROL ON DATABASE::[mySampleDataWarehouse] to LoaderRC20;
    EXEC sp_addrolemember 'staticrc20', 'LoaderRC20';
    ```

6. Selecione **Executar**.

## <a name="connect-to-the-server-as-the-loading-user"></a>Conectar-se ao servidor como o usuário carregado

A primeira etapa para carregar os dados é fazer logon como LoaderRC20.  

1. No Pesquisador de objetos, selecione o menu suspenso **conectar** e selecione **mecanismo de banco de dados**. A caixa de diálogo **Conectar-se ao Servidor** é exibida.

    ![Conectar-se com o novo logon](./media/load-data-from-azure-blob-storage-using-polybase/connect-as-loading-user.png)

2. Insira o nome do servidor totalmente qualificado, e vez insira **LoaderRC20** como o Logon.  Insira sua senha para LoaderRC20.

3. Selecione **Conectar**.

4. Quando sua conexão estiver pronta, você verá duas conexões de servidor no Pesquisador de Objetos. Uma conexão como ServerAdmin e outra como MedRCLogin.

    ![A conexão é bem-sucedida](./media/load-data-from-azure-blob-storage-using-polybase/connected-as-new-login.png)

## <a name="create-tables-for-the-sample-data"></a>Criar tabelas para os dados de exemplo

Você está pronto para iniciar o processo de carregamento de dados em seu novo data warehouse. Esta parte do tutorial mostra como usar a instrução de cópia para carregar o conjunto de uma Nova York City táxi de um blob de armazenamento do Azure. Para referência futura, para saber como obter seus dados para o armazenamento de BLOBs do Azure ou carregá-los diretamente da sua fonte, consulte a [visão geral de carregamento](design-elt-data-loading.md).

Execute os seguintes scripts SQL e especifique as informações sobre os dados que você deseja carregar. Essas informações incluem o local em que os dados estão localizados, o formato do conteúdo dos dados e a definição da tabela para os dados.

1. Na seção anterior, você fez logon em seu data warehouse como LoaderRC20. No SSMS, clique com o botão direito do mouse em sua conexão LoaderRC20 e selecione **Nova consulta**.  Uma nova janela de consulta é exibida.

    ![Nova janela de consulta de carregamento](./media/load-data-from-azure-blob-storage-using-polybase/new-loading-query.png)

2. Compare a janela de consulta com a imagem anterior.  Verifique se a nova janela de consulta está em execução como LoaderRC20 e está executando consultas no banco de dados MySampleDataWarehouse. Use essa janela de consulta para executar todas as etapas de carregamento.

7. Execute as seguintes instruções T-SQL para criar as tabelas:

    ```sql
    CREATE TABLE [dbo].[Date]
    (
        [DateID] int NOT NULL,
        [Date] datetime NULL,
        [DateBKey] char(10) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfMonth] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DaySuffix] varchar(4) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfWeek] char(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfWeekInMonth] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfWeekInYear] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfQuarter] varchar(3) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfYear] varchar(3) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [WeekOfMonth] varchar(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [WeekOfQuarter] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [WeekOfYear] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Month] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MonthName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MonthOfQuarter] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Quarter] char(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [QuarterName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Year] char(4) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [YearName] char(7) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MonthYear] char(10) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MMYYYY] char(6) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [FirstDayOfMonth] date NULL,
        [LastDayOfMonth] date NULL,
        [FirstDayOfQuarter] date NULL,
        [LastDayOfQuarter] date NULL,
        [FirstDayOfYear] date NULL,
        [LastDayOfYear] date NULL,
        [IsHolidayUSA] bit NULL,
        [IsWeekday] bit NULL,
        [HolidayUSA] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[Geography]
    (
        [GeographyID] int NOT NULL,
        [ZipCodeBKey] varchar(10) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [County] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [City] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [State] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Country] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [ZipCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[HackneyLicense]
    (
        [HackneyLicenseID] int NOT NULL,
        [HackneyLicenseBKey] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [HackneyLicenseCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[Medallion]
    (
        [MedallionID] int NOT NULL,
        [MedallionBKey] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [MedallionCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[Time]
    (
        [TimeID] int NOT NULL,
        [TimeBKey] varchar(8) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [HourNumber] tinyint NOT NULL,
        [MinuteNumber] tinyint NOT NULL,
        [SecondNumber] tinyint NOT NULL,
        [TimeInSecond] int NOT NULL,
        [HourlyBucket] varchar(15) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [DayTimeBucketGroupKey] int NOT NULL,
        [DayTimeBucket] varchar(100) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[Trip]
    (
        [DateID] int NOT NULL,
        [MedallionID] int NOT NULL,
        [HackneyLicenseID] int NOT NULL,
        [PickupTimeID] int NOT NULL,
        [DropoffTimeID] int NOT NULL,
        [PickupGeographyID] int NULL,
        [DropoffGeographyID] int NULL,
        [PickupLatitude] float NULL,
        [PickupLongitude] float NULL,
        [PickupLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DropoffLatitude] float NULL,
        [DropoffLongitude] float NULL,
        [DropoffLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [PassengerCount] int NULL,
        [TripDurationSeconds] int NULL,
        [TripDistanceMiles] float NULL,
        [PaymentType] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [FareAmount] money NULL,
        [SurchargeAmount] money NULL,
        [TaxAmount] money NULL,
        [TipAmount] money NULL,
        [TollsAmount] money NULL,
        [TotalAmount] money NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[Weather]
    (
        [DateID] int NOT NULL,
        [GeographyID] int NOT NULL,
        [PrecipitationInches] float NOT NULL,
        [AvgTemperatureFahrenheit] float NOT NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    ```
    

## <a name="load-the-data-into-your-data-warehouse"></a>Carregar os dados em seu data warehouse

Esta seção usa a [instrução de cópia para carregar](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) os dados de exemplo de Azure Storage BLOB.  

> [!NOTE]
> Este tutorial carrega os dados diretamente na tabela final. Normalmente, você carregaria em uma tabela de preparo para suas cargas de trabalho de produção. Enquanto os dados estão na tabela de preparo, você pode executar todas as transformações necessárias. 

1. Execute as seguintes instruções para carregar os dados:

    ```sql
    COPY INTO [dbo].[Date]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Date'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = ''
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Date] - Taxi dataset');
    
    
    COPY INTO [dbo].[Geography]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Geography'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = ''
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Geography] - Taxi dataset');
    
    COPY INTO [dbo].[HackneyLicense]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/HackneyLicense'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = ''
    )
    OPTION (LABEL = 'COPY : Load [dbo].[HackneyLicense] - Taxi dataset');
    
    COPY INTO [dbo].[Medallion]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Medallion'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = ''
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Medallion] - Taxi dataset');
    
    COPY INTO [dbo].[Time]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Time'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = ''
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Time] - Taxi dataset');
    
    COPY INTO [dbo].[Weather]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Weather'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = '',
        ROWTERMINATOR='0X0A'
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Weather] - Taxi dataset');
    
    COPY INTO [dbo].[Trip]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Trip2013'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = '|',
        FIELDQUOTE = '',
        ROWTERMINATOR='0X0A',
        COMPRESSION = 'GZIP'
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Trip] - Taxi dataset');
    ```

2. Exiba os dados enquanto eles são carregados. Você está carregando vários GBs de dados e compactando-os em índices columnstore clusterizados de alto desempenho. Execute a consulta a seguir que usa DMVs (exibições de gerenciamento dinâmico) para mostrar o status do carregamento.

    ```sql
    SELECT  r.[request_id]                           
    ,       r.[status]                               
    ,       r.resource_class                         
    ,       r.command
    ,       sum(bytes_processed) AS bytes_processed
    ,       sum(rows_processed) AS rows_processed
    FROM    sys.dm_pdw_exec_requests r
                  JOIN sys.dm_pdw_dms_workers w
                         ON r.[request_id] = w.request_id
    WHERE [label] = 'COPY : Load [dbo].[Date] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[Geography] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[HackneyLicense] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[Medallion] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[Time] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[Weather] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[Trip] - Taxi dataset' 
    and session_id <> session_id() and type = 'WRITER'
    GROUP BY r.[request_id]                           
    ,       r.[status]                               
    ,       r.resource_class                         
    ,       r.command;
    ```
    
3. Exiba todas as consultas do sistema.

    ```sql
    SELECT * FROM sys.dm_pdw_exec_requests;
    ```

4. Veja os dados carregados sem problemas no seu data warehouse.

    ![Exibir tabelas carregadas](./media/load-data-from-azure-blob-storage-using-polybase/view-loaded-tables.png)

## <a name="clean-up-resources"></a>Limpar recursos

Você está sendo cobrado por recursos de computação e por dados que você carregou em seu data warehouse. Eles são cobrados separadamente.

* Se desejar manter os dados no armazenamento, será possível pausar a computação quando você não estiver usando o data warehouse. Ao pausar a computação, você será cobrado apenas pelo armazenamento de dados e poderá retomar a computação quando estiver pronto para trabalhar com os dados.
* Se desejar remover encargos futuros, será possível excluir o data warehouse.

Siga estas etapas para limpar os recursos conforme desejado.

1. Faça logon no [portal do Azure](https://portal.azure.com), selecione seu data warehouse.

    ![Limpar os recursos](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Para pausar a computação, selecione o botão **Pausar**. Quando o data warehouse for pausado, você verá um botão **Iniciar**.  Para retomar a computação, selecione **Iniciar**.

3. Para remover o data warehouse para que você não seja cobrado pela computação ou pelo armazenamento, selecione **excluir**.

4. Para remover o servidor que você criou, selecione **mynewserver-20180430.Database.Windows.net** na imagem anterior e, em seguida, selecione **excluir**.  Tenha cuidado com isso, uma vez que a exclusão do servidor excluirá todos os bancos de dados atribuídos a ele.

5. Para remover o grupo de recursos, selecione **myResourceGroup** e, em seguida, **Excluir grupo de recursos**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como criar um data warehouse e um usuário para carregar dados. Você usou a [instrução de cópia](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest#examples) simples para carregar dados em seu data warehouse.

Você fez essas coisas:
> [!div class="checklist"]
>
> * Criou um data warehouse no Portal do Azure
> * Configurar uma regra de firewall de nível de servidor no Portal do Azure
> * Conectou-se ao data warehouse com o SSMS
> * Criou um usuário designado para carregar dados
> * Criou as tabelas para os dados de exemplo
> * Usou a instrução COPY T-SQL para carregar dados em seu data warehouse
> * Exibiu o andamento dos dados enquanto eles estão sendo carregados

Avance para a visão geral de desenvolvimento para saber como migrar um banco de dados existente para o Azure Synapse Analytics:

> [!div class="nextstepaction"]
> [Decisões de design para migrar um banco de dados existente para o Azure Synapse Analytics](sql-data-warehouse-overview-develop.md)

Para obter mais exemplos de carregamento e referências, veja a seguinte documentação:

- [Documentação de referência de instrução de cópia](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest#syntax)
- [COPIAR exemplos para cada método de autenticação](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-bulk-load-copy-tsql-examples)
- [COPIAR guia de início rápido para uma única tabela](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-bulk-load-copy-tsql)
