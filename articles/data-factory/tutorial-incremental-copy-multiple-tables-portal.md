---
title: Copiar várias tabelas incrementalmente usando o Azure Data Factory | Microsoft Docs
description: Neste tutorial, você deve criar um pipeline do Azure Data Factory que copie incrementalmente os dados delta de várias tabelas em um banco de dados do SQL Server local para um banco de dados SQL do Azure.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.date: 01/20/2018
ms.author: yexu
ms.openlocfilehash: 44ae433040c2c9cab47567cb663d4e588311a4a1
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177411"
---
# <a name="incrementally-load-data-from-multiple-tables-in-sql-server-to-an-azure-sql-database"></a>Carregar incrementalmente os dados de várias tabelas no SQL Server para um banco de dados SQL do Azure
Neste tutorial, você pode criar um Azure Data Factory com um pipeline que carrega dados delta de várias tabelas do SQL Server local para um banco de dados SQL do Azure.    

Neste tutorial, você realizará os seguintes procedimentos:

> [!div class="checklist"]
> * Preparará os armazenamentos de dados de origem e destino.
> * Criar um data factory.
> * Criar um tempo de execução de integração auto-hospedado.
> * Instalar o Integration Runtime. 
> * Criar serviços vinculados. 
> * Criar os conjuntos de dados de origem, de coletor e de marca-d'água.
> * Crie, execute e monitore um pipeline.
> * Revise os resultados.
> * Adicionar ou atualizar dados nas tabelas de origem.
> * Executar novamente e monitorar o pipeline.
> * Examinar os resultados finais.

## <a name="overview"></a>Visão geral
Aqui estão as etapas importantes ao criar essa solução: 

1. **Selecione a coluna de marca-d'água**.
    
    Selecione uma coluna para cada tabela no armazenamento de dados de origem. Posteriormente, essa coluna pode ser usada para identificar os registros novos ou atualizados de cada execução. Normalmente, os dados nessa coluna selecionada (por exemplo, ID ou last_modify_time) seguem crescendo quando linhas são criadas ou atualizadas. O valor máximo dessa coluna é usado como uma marca-d'água.

1. **Prepare um armazenamento de dados para armazenar o valor de marca-d'água**.   
    
    Neste tutorial, você deve armazenar o valor de marca-d'água em um banco de dados SQL.

1. **Crie um pipeline com as seguintes atividades**: 
    
    a. Crie uma atividade ForEach que itere em uma lista de nomes de tabela de origem passada como um parâmetro para o pipeline. Para cada tabela de origem, são chamadas as próximas atividades necessárias para o carregamento delta.

    b. Crie duas atividades de pesquisa. Use a primeira atividade de Pesquisa para recuperar o último valor de marca-d'água. Use a segunda atividade de Pesquisa para recuperar o novo valor de marca-d'água. Esses valores de marca-d'água são passados para a atividade de Cópia.

    c. Crie uma atividade de cópia que copie linhas do armazenamento de dados de origem com o valor da coluna de marca-d'água maior do que o antigo valor de marca-d'água e menor que o novo valor de marca-d'água. Em seguida, ela copia os dados delta do armazenamento de dados de origem para um Armazenamento de Blobs do Azure como um novo arquivo.

    d. Crie uma atividade de StoredProcedure que atualize o valor de marca-d'água para o pipeline que for executado da próxima vez. 

    A seguir está diagrama da solução de alto nível: 

    ![Carregar dados incrementalmente](media/tutorial-incremental-copy-multiple-tables-portal/high-level-solution-diagram.png)


Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos
* **SQL Server**. Neste tutorial, você utiliza um banco de dados do SQL Server local como o armazenamento de dados de origem. 
* **Banco de dados SQL do Azure**. Use um banco de dados SQL do Azure como o armazenamento de dados do coletor. Se você não tiver um banco de dados SQL, consulte [Criar um banco de dados SQL do Azure](../sql-database/sql-database-get-started-portal.md) para saber as etapas para criar um. 

### <a name="create-source-tables-in-your-sql-server-database"></a>Criar tabelas de origem no banco de dados do SQL Server

1. Abra o SQL Server Management Studio e conecte-se ao banco de dados do SQL Server local.

1. No **Gerenciador de Servidores**, clique com o botão direito do mouse no banco de dados e escolha **Nova consulta**.

1. Execute o seguinte comando SQL no banco de dados para criar as tabelas `customer_table` e `project_table`:

    ```sql
    create table customer_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );
    
    create table project_table
    (
        Project varchar(255),
        Creationtime datetime
    );
        
    INSERT INTO customer_table
    (PersonID, Name, LastModifytime)
    VALUES
    (1, 'John','9/1/2017 12:56:00 AM'),
    (2, 'Mike','9/2/2017 5:23:00 AM'),
    (3, 'Alice','9/3/2017 2:36:00 AM'),
    (4, 'Andy','9/4/2017 3:21:00 AM'),
    (5, 'Anny','9/5/2017 8:06:00 AM');
    
    INSERT INTO project_table
    (Project, Creationtime)
    VALUES
    ('project1','1/1/2015 0:00:00 AM'),
    ('project2','2/2/2016 1:23:00 AM'),
    ('project3','3/4/2017 5:16:00 AM');
    
    ```

### <a name="create-destination-tables-in-your-azure-sql-database"></a>Criar tabelas de destino no banco de dados SQL do Azure
1. Abra o SQL Server Management Studio e conecte-se ao seu banco de dados SQL do Azure.

1. No **Gerenciador de Servidores**, clique com o botão direito do mouse no banco de dados e escolha **Nova consulta**.

1. Execute o seguinte comando SQL no banco de dados SQL para criar as tabelas `customer_table` e `project_table`:  
    
    ```sql
    create table customer_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );
    
    create table project_table
    (
        Project varchar(255),
        Creationtime datetime
    );

    ```

### <a name="create-another-table-in-the-azure-sql-database-to-store-the-high-watermark-value"></a>Criar outra tabela no banco de dados SQL do Azure para armazenar o valor de marca d'água alta
1. Execute o comando SQL a seguir no banco de dados SQL para criar uma tabela chamada `watermarktable` para armazenar o valor de marca-d'água: 
    
    ```sql
    create table watermarktable
    (
    
        TableName varchar(255),
        WatermarkValue datetime,
    );
    ```
1. Insira valores de marca d'água iniciais para ambas as tabelas de origem na tabela de marca d'água.

    ```sql

    INSERT INTO watermarktable
    VALUES 
    ('customer_table','1/1/2010 12:00:00 AM'),
    ('project_table','1/1/2010 12:00:00 AM');
    
    ```

### <a name="create-a-stored-procedure-in-the-azure-sql-database"></a>Criar um procedimento armazenado no banco de dados SQL do Azure 

Execute o comando a seguir para criar um procedimento armazenado no banco de dados SQL. Esse procedimento armazenado atualiza o valor de marca d'água após cada execução de pipeline. 

```sql
CREATE PROCEDURE usp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN

    UPDATE watermarktable
    SET [WatermarkValue] = @LastModifiedtime 
WHERE [TableName] = @TableName

END

```

### <a name="create-data-types-and-additional-stored-procedures-in-azure-sql-database"></a>Criar tipos de dados e procedimentos armazenados adicionais no banco de dados SQL do Azure
Execute a consulta a seguir para criar dois tipos de dados e dois procedimentos armazenados no banco de dados SQL. Eles são usados para mesclar os dados das tabelas de origem nas tabelas de destino.

Para facilitar a jornada de introdução, usamos diretamente esses procedimentos armazenados passando os dados delta por meio de uma variável de tabela e, em seguida, mesclamos esses dados no repositório de destino. Tenha cuidado, pois ele não espera um "grande" número de linhas delta (mais de 100) a ser armazenado na variável de tabela.  

Caso precise mesclar um grande número de linhas delta no repositório de destino, sugerimos que você use a atividade de cópia para copiar todos os dados delta para uma tabela temporária de "preparo" no repositório de destino primeiro e, em seguida, criar seu próprio procedimento armazenado sem o uso da variável de tabela para mesclá-los da tabela de “preparo” para a tabela “final”. 


```sql
CREATE TYPE DataTypeforCustomerTable AS TABLE(
    PersonID int,
    Name varchar(255),
    LastModifytime datetime
);

GO

CREATE PROCEDURE usp_upsert_customer_table @customer_table DataTypeforCustomerTable READONLY
AS

BEGIN
  MERGE customer_table AS target
  USING @customer_table AS source
  ON (target.PersonID = source.PersonID)
  WHEN MATCHED THEN
      UPDATE SET Name = source.Name,LastModifytime = source.LastModifytime
  WHEN NOT MATCHED THEN
      INSERT (PersonID, Name, LastModifytime)
      VALUES (source.PersonID, source.Name, source.LastModifytime);
END

GO

CREATE TYPE DataTypeforProjectTable AS TABLE(
    Project varchar(255),
    Creationtime datetime
);

GO

CREATE PROCEDURE usp_upsert_project_table @project_table DataTypeforProjectTable READONLY
AS

BEGIN
  MERGE project_table AS target
  USING @project_table AS source
  ON (target.Project = source.Project)
  WHEN MATCHED THEN
      UPDATE SET Creationtime = source.Creationtime
  WHEN NOT MATCHED THEN
      INSERT (Project, Creationtime)
      VALUES (source.Project, source.Creationtime);
END

```

## <a name="create-a-data-factory"></a>Criar uma data factory

1. Iniciar o navegador da Web **Microsoft Edge** ou **Google Chrome**. Atualmente, a interface do usuário do Data Factory tem suporte apenas nos navegadores da Web Microsoft Edge e Google Chrome.
1. Clique em **Novo** no menu à esquerda, clique em **Dados + Análise** e clique em **Data Factory**. 
   
   ![Novo -> DataFactory](./media/tutorial-incremental-copy-multiple-tables-portal/new-azure-data-factory-menu.png)
1. Na página **Novo data factory**, insira **ADFMultiIncCopyTutorialDF** como o **nome**. 
      
     ![Página de novo data factory](./media/tutorial-incremental-copy-multiple-tables-portal/new-azure-data-factory.png)
 
   O nome do Azure Data Factory deve ser **globalmente exclusivo**. Se você receber o seguinte erro, altere o nome de data factory (por exemplo, yournameADFMultiIncCopyTutorialDF) e tente criar novamente. Confira o artigo [Data Factory - regras de nomenclatura](naming-rules.md) para ver as regras de nomenclatura para artefatos do Data Factory.
  
       `Data factory name ADFMultiIncCopyTutorialDF is not available`
1. Selecione a **assinatura** do Azure na qual você deseja criar o data factory. 
1. Para o **Grupo de Recursos**, execute uma das seguintes etapas:
     
      - Selecione **Usar existente**e selecione um grupo de recursos existente na lista suspensa. 
      - Selecione **Criar novo**e insira o nome de um grupo de recursos.   
         
        Para saber mais sobre grupos de recursos, consulte [Usando grupos de recursos para gerenciar recursos do Azure](../azure-resource-manager/resource-group-overview.md).  
1. Selecione **V2 (Versão Prévia)** para a **versão**.
1. Selecione o **local** do data factory. Apenas os locais com suporte são exibidos na lista suspensa. Os armazenamentos de dados (Armazenamento do Azure, Banco de Dados SQL do Azure, etc.) e serviços de computação (HDInsight, etc.) usados pelo data factory podem estar em outras regiões.
1. Selecione **Fixar no painel**.     
1. Clique em **Criar**.      
1. No painel, você deve ver o seguinte bloco com status: **Implantando data factory**. 

    ![implantando bloco data factory](media/tutorial-incremental-copy-multiple-tables-portal/deploying-data-factory.png)
1. Após a criação, a página do **Data Factory** será exibida conforme mostrado na imagem.
   
   ![Página inicial do data factory](./media/tutorial-incremental-copy-multiple-tables-portal/data-factory-home-page.png)
1. Clique no bloco **Criar e Monitorar** para iniciar a interface do usuário do Azure Data Factory em uma guia separada.
1. Na página de introdução da interface do usuário do Azure Data Factory, clique em **Criar pipeline** (ou) alterne para a guia **Editar**. 

   ![Página Introdução](./media/tutorial-incremental-copy-multiple-tables-portal/get-started-page.png)

## <a name="create-self-hosted-integration-runtime"></a>Criar um Integration Runtime auto-hospedado
Conforme você move dados de um armazenamento de dados em uma rede privada (local) para um armazenamento de dados do Azure, instale um RI (tempo de execução de integração) auto-hospedado em seu ambiente local. O IR auto-hospedado move dados entre sua rede privada e o Azure. 

1. Clique em **Conexões** na parte inferior do painel esquerdo e alterne para **Integration Runtimes** na janela **Conexões**. 

   ![Guia Conexões](./media/tutorial-incremental-copy-multiple-tables-portal/connections-tab.png)
1. Em **Integration Runtimes** , clique em **+ Novo**. 

   ![Novo tempo de execução de integração - botão](./media/tutorial-incremental-copy-multiple-tables-portal/new-integration-runtime-button.png)
1. Na janela **Instalação do Integration Runtime**, selecione **Executar atividades de movimentação e distribuição de dados para cálculos externos** e clique em **Avançar**. 

   ![Selecionar tipo de tempo de execução de integração](./media/tutorial-incremental-copy-multiple-tables-portal/select-integration-runtime-type.png)
1. Selecione ** Rede Privada** e clique em **Avançar**. 

   ![Selecionar rede privada](./media/tutorial-incremental-copy-multiple-tables-portal/select-private-network.png)
1. Insira **MySelfHostedIR** como **Nome** e clique em **Avançar**. 

   ![Nome do IR auto-hospedado](./media/tutorial-incremental-copy-multiple-tables-portal/self-hosted-ir-name.png)
1. Clique em **Clique aqui para inicializar a instalação expressa para este computador** na seção **Opção 1: instalação expressa**. 

   ![Clique no link de instalação Expressa](./media/tutorial-incremental-copy-multiple-tables-portal/click-express-setup.png)
1. Na janela **Instalação Expressa do Microsoft Integration Runtime (auto-hospedado)** , clique em **Fechar**. 

   ![Instalação do tempo de execução de integração - êxito](./media/tutorial-incremental-copy-multiple-tables-portal/integration-runtime-setup-successful.png)
1. No navegador da Web, na janela **Configuração de Tempo de Execução de Integração**, clique em **Concluir**. 

   ![Configuração do tempo de execução de integração - conclusão](./media/tutorial-incremental-copy-multiple-tables-portal/click-finish-integration-runtime-setup.png)
1. Confirme que você vê **MySelfHostedIR** na lista de tempos de execução de integração.

    ![Tempos de execução de integração - lista](./media/tutorial-incremental-copy-multiple-tables-portal/integration-runtimes-list.png)

## <a name="create-linked-services"></a>Criar serviços vinculados
Os serviços vinculados são criados em um data factory para vincular seus armazenamentos de dados e serviços de computação ao data factory. Nesta seção, você cria serviços vinculados para seu banco de dados do SQL Server local e banco de dados SQL. 

### <a name="create-the-sql-server-linked-service"></a>Criar um serviço vinculado do SQL Server
Nesta etapa, você vincula seu banco de dados do SQL Server local ao data factory.

1. Na janela **Conexões**, alterne da guia **Integration Runtime** para a guia **Serviços Associados** e clique em **+ Novo**.

    ![Botão Novo Serviço Vinculado](./media/tutorial-incremental-copy-multiple-tables-portal/new-sql-server-linked-service-button.png)
1. Na janela **Novo Serviço Vinculado**, selecione **SQL Server** e clique em **Continuar**. 

    ![Selecionar SQL Server](./media/tutorial-incremental-copy-multiple-tables-portal/select-sql-server.png)
1. Na janela **Novo Serviço Vinculado**, execute estas etapas:

    1. Insira **SqlServerLinkedService** como **Nome**. 
    1. Selecione **MySelfHostedIR** para **Conectar por meio do tempo de execução de integração**. Esta é uma etapa **importante**. O tempo de execução de integração padrão não pode se conectar a um armazenamento de dados local. Use o tempo de execução de integração auto-hospedado criado anteriormente. 
    1. Como **Nome do servidor**, digite o nome do computador que tem o banco de dados do SQL Server.
    1. Como **Nome do banco de dados**, digite o nome do banco de dados no SQL Server que contém os dados de origem. Você criou uma tabela e dados inseridos neste banco de dados como parte dos pré-requisitos. 
    1. Como **Tipo de autenticação**, selecione o **tipo de autenticação** que você deseja usar para se conectar ao banco de dados. 
    1. Como **Nome de usuário**, digite o nome do usuário que tem acesso ao banco de dados do SQL Server. Se precisar usar um caractere de barra (`\`) em sua conta de usuário e nome de servidor, use o caractere de escape (`\`). Um exemplo é `mydomain\\myuser`.
    1. Para **Senha**, insira a **senha** do usuário. 
    1. Para testar se o data factory pode se conectar ao banco de dados do SQL Server, clique em **Testar conexão**. Corrija os erros até que a conexão seja bem-sucedida. 
    1. Para salvar um serviço vinculado, clique em **Salvar**.

        ![Serviço vinculado do SQL Server - configurações](./media/tutorial-incremental-copy-multiple-tables-portal/sql-server-linked-service-settings.png)

### <a name="create-the-azure-sql-database-linked-service"></a>Criar o serviço vinculado do Banco de Dados SQL do Azure
Na última etapa, você deve criar um serviço vinculado para vincular o banco de dados do SQL Server de origem ao data factory. Nesta etapa, você vincula o banco de dados SQL do Azure de destino/coletor ao data factory. 

1. Na janela **Conexões**, alterne da guia **Integration Runtime** para a guia **Serviços Associados** e clique em **+ Novo**.

    ![Botão Novo Serviço Vinculado](./media/tutorial-incremental-copy-multiple-tables-portal/new-sql-server-linked-service-button.png)
1. Na janela **Novo Serviço Vinculado**, selecione **Banco de Dados SQL do Azure** e clique em **Continuar**. 
1. Na janela **Novo Serviço Vinculado**, execute estas etapas:

    1. Insira **AzureSqlDatabaseLinkedService** para o **Nome**. 
    1. Como **Nome do servidor**, selecione o nome do servidor SQL do Azure na lista suspensa. 
    1. Como **Nome do banco de dados**, selecione o banco de dados SQL do Azure em que você criou customer_table e project_table como parte dos pré-requisitos. 
    1. Como **Nome de usuário**, digite o nome do usuário que tem acesso ao banco de dados SQL do Azure. 
    1. Para **Senha**, insira a **senha** do usuário. 
    1. Para testar se o data factory pode se conectar ao banco de dados do SQL Server, clique em **Testar conexão**. Corrija os erros até que a conexão seja bem-sucedida. 
    1. Para salvar um serviço vinculado, clique em **Salvar**.

        ![Serviço vinculado do SQL do Azur - configurações](./media/tutorial-incremental-copy-multiple-tables-portal/azure-sql-linked-service-settings.png)
1. Confirme que você vê os dois serviços vinculados na lista. 
   
    ![Dois serviços vinculados](./media/tutorial-incremental-copy-multiple-tables-portal/two-linked-services.png) 

## <a name="create-datasets"></a>Criar conjuntos de dados
Nesta etapa, você cria conjuntos de dados para representar a fonte de dados, o destino de dados e o local para armazenar a marca d'água.

### <a name="create-a-source-dataset"></a>Criar um conjunto de dados de origem

1. No painel esquerdo, clique em **+ (adição)** e clique em **Conjunto de Dados**.

   ![Menu Novo conjunto de dados](./media/tutorial-incremental-copy-multiple-tables-portal/new-dataset-menu.png)
1. Na janela **Novo Conjunto de Dados**, selecione **SQL Server** e clique em **Concluir**. 

   ![Selecionar SQL Server](./media/tutorial-incremental-copy-multiple-tables-portal/select-sql-server-for-dataset.png)
1. Você vê uma nova guia aberta no navegador da Web para configurar o conjunto de dados. Você também vê um conjunto de dados no modo de exibição de árvore. Na guia **Geral** da janela Propriedades, na parte inferior, insira **SourceDataset** como **Nome**. 

   ![Conjunto de dados de origem - nome](./media/tutorial-incremental-copy-multiple-tables-portal/source-dataset-general.png)
1. Alterne para a guia **Conexão** na janela Propriedades e selecione **SqlServerLinkedService** como **Serviço vinculado**. Você não seleciona uma tabela aqui. A atividade de Cópia no pipeline usa uma consulta SQL para carregar os dados em vez de carregar a tabela inteira.

   ![Conjunto de Dados de Origem - conexão](./media/tutorial-incremental-copy-multiple-tables-portal/source-dataset-connection.png)


### <a name="create-a-sink-dataset"></a>Criar um conjunto de dados de coletor
1. No painel esquerdo, clique em **+ (adição)** e clique em **Conjunto de Dados**.

   ![Menu Novo conjunto de dados](./media/tutorial-incremental-copy-multiple-tables-portal/new-dataset-menu.png)
1. Na janela **Novo Conjunto de dados**, selecione **Banco de Dados SQL do Azure** e clique em **Concluir**. 

   ![Selecione um Banco de Dados SQL do Azure](./media/tutorial-incremental-copy-multiple-tables-portal/select-azure-sql-database.png)
1. Você vê uma nova guia aberta no navegador da Web para configurar o conjunto de dados. Você também vê um conjunto de dados no modo de exibição de árvore. Na guia **Geral** da janela Propriedades para o conjunto de dados, na parte inferior, insira **SinkDataset** como **Nome**.

   ![Conjunto de Dados do Coletor - geral](./media/tutorial-incremental-copy-multiple-tables-portal/sink-dataset-general.png)
1. Alterne para a guia **Parâmetros** na janela Propriedades e execute as seguintes etapas: 

    1. Clique em **Novo** na seção **Criar/atualizar parâmetros**. 
    1. Digite **SinkTableName** como **nome** e **Cadeia de Caracteres** como **tipo**. Este conjunto de dados usa **SinkTableName** como um parâmetro. O parâmetro SinkTableName é definido pelo pipeline dinamicamente no tempo de execução. A atividade ForEach no pipeline itera por meio de uma lista de nomes de tabela e passa o nome da tabela para esse conjunto de dados em cada iteração.
   
       ![Conjunto de Dados do Coletor - propriedades](./media/tutorial-incremental-copy-multiple-tables-portal/sink-dataset-parameters.png)
1. Alterne para a guia **Conexão** na janela Propriedades e selecione **AzureSqlLinkedService** como **Serviço vinculado**. Para a propriedade **Tabela**, clique em **Adicionar conteúdo dinâmico**. 

   ![Conjunto de Dados do Coletor - conexão](./media/tutorial-incremental-copy-multiple-tables-portal/sink-dataset-connection.png)
    
    
1. Selecione **SinkTableName** na seção **Parâmetros**
   
   ![Conjunto de Dados do Coletor - conexão](./media/tutorial-incremental-copy-multiple-tables-portal/sink-dataset-connection-dynamicContent.png)

   
 1. Depois de clicar em **Concluir**, você verá **\@dataset().SinkTableName** como o nome da tabela.
   
   ![Conjunto de Dados do Coletor - conexão](./media/tutorial-incremental-copy-multiple-tables-portal/sink-dataset-connection-completion.png)

### <a name="create-a-dataset-for-a-watermark"></a>Criar um conjunto de dados para uma marca-d'água
Nesta etapa, você deve criar um conjunto de dados para armazenar um valor de marca d'água alta. 

1. No painel esquerdo, clique em **+ (adição)** e clique em **Conjunto de Dados**.

   ![Menu Novo conjunto de dados](./media/tutorial-incremental-copy-multiple-tables-portal/new-dataset-menu.png)
1. Na janela **Novo Conjunto de dados**, selecione **Banco de Dados SQL do Azure** e clique em **Concluir**. 

   ![Selecione um Banco de Dados SQL do Azure](./media/tutorial-incremental-copy-multiple-tables-portal/select-azure-sql-database.png)
1. Na guia **Geral** da janela Propriedades para o conjunto de dados, na parte inferior, insira **WatermarkDataset** como **Nome**.
1. Alterne para a guia **Conexão** e siga estas etapas: 

    1. Selecione **AzureSqlDatabaseLinkedService** para **Serviço vinculado**.
    1. Selecione **[dbo].[watermarktable]** para **Tabela**.

       ![Conjunto de Dados de Marca d'Água - conexão](./media/tutorial-incremental-copy-multiple-tables-portal/watermark-dataset-connection.png)

## <a name="create-a-pipeline"></a>Criar um pipeline
O pipeline usa uma lista de nomes de tabela como um parâmetro. A atividade ForEach itera na lista de nomes de tabela e executa as seguintes operações: 

1. Use a atividade de Pesquisa para recuperar o valor de marca d'água antigo (o valor inicial ou o que foi usado na última iteração).

1. Use a atividade de Pesquisa para recuperar o novo valor de marca d'água (o valor máximo da coluna da marca d'água na tabela de origem).

1. Use a atividade de Cópia para copiar os dados entre esses dois valores de marca d'água do banco de dados de origem para o banco de dados de destino.

1. Use a atividade StoredProcedure para atualizar o valor da marca d'água antiga a ser usado na primeira etapa da próxima iteração. 

### <a name="create-the-pipeline"></a>Criar o pipeline

1. No painel esquerdo, clique em **+ (adição)** e clique em **Pipeline**.

    ![Novo Pipeline - menu](./media/tutorial-incremental-copy-multiple-tables-portal/new-pipeline-menu.png)
1. Na guia **Geral** da janela **Propriedades**, digite **IncrementalCopyPipeline** como **Nome**. 

    ![Nome do pipeline](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-name.png)
1. Na janela **Propriedades**, siga estas etapas: 

    1. Clique em **+ Novo**. 
    1. Digite **tableList** para o parâmetro **nome**. 
    1. Selecione **Objeto** como o parâmetro **Tipo**.

    ![Parâmetros de pipeline](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-parameters.png) 
1. Na caixa de ferramentas **Atividades**, expanda **Iteração e condicionais** e arraste e solte a atividade **ForEach** para a superfície de designer do pipeline. Na guia **Geral** da janela **Propriedades**, insira **IterateSQLTables**. 

    ![Atividade ForEach - nome](./media/tutorial-incremental-copy-multiple-tables-portal/foreach-name.png)
1. Alterne para a guia **Configurações** na janela **Propriedades** e digite `@pipeline().parameters.tableList` como **Itens**. A atividade ForEach itera por meio de uma lista de tabelas e executa a operação de cópia incremental. 

    ![Atividade ForEach - configurações](./media/tutorial-incremental-copy-multiple-tables-portal/foreach-settings.png)
1. Selecione a atividade **ForEach** no pipeline, se já não estiver selecionada. Clique no botão **Editar (ícone de lápis)** .

    ![Atividade ForEach - edição](./media/tutorial-incremental-copy-multiple-tables-portal/edit-foreach.png)
1. Na caixa de ferramentas **Atividades**, expanda **Geral** e arraste e solte a atividade de **Pesquisa** para a superfície do designer de pipeline, e insira **LookupOldWaterMarkActivity** para o **Nome**.

    ![Primeira Atividade de Pesquisa - nome](./media/tutorial-incremental-copy-multiple-tables-portal/first-lookup-name.png)
1. Alterne para a guia **Configurações** na janela **Propriedades** e execute as seguintes etapas: 

    1. Selecione **WatermarkDataset** como **Conjunto de Dados de Origem**.
    1. Selecione **Consulta** para **Usar consulta**. 
    1. Insira a seguinte consulta SQL para **Consulta**. 

        ```sql
        select * from watermarktable where TableName  =  '@{item().TABLE_NAME}'
        ```

        ![Primeira Atividade de Pesquisa - configurações](./media/tutorial-incremental-copy-multiple-tables-portal/first-lookup-settings.png)
1. Arraste e solte a atividade **Pesquisar** na caixa de ferramentas **Atividades** e digite **LookupNewWaterMarkActivity** como **Nome**.
        
    ![Segunda Atividade de Pesquisa - nome](./media/tutorial-incremental-copy-multiple-tables-portal/second-lookup-name.png)
1. Alterne para a guia **Configurações** .

    1. Selecione **SourceDataset** para o **Conjunto de dados de origem**. 
    1. Selecione **Consulta** para **Usar consulta**.
    1. Insira a seguinte consulta SQL para **Consulta**.

        ```sql    
        select MAX(@{item().WaterMark_Column}) as NewWatermarkvalue from @{item().TABLE_NAME}
        ```
    
        ![Segunda Atividade de Pesquisa - configurações](./media/tutorial-incremental-copy-multiple-tables-portal/second-lookup-settings.png)
1. Arraste e solte a atividade de **Cópia** da caixa de ferramentas **Atividades** e digite **IncrementalCopyActivity** como **Nome**. 

    ![Atividade de cópia - nome](./media/tutorial-incremental-copy-multiple-tables-portal/copy-activity-name.png)
1. Conecte as atividades de **Pesquisa** à atividade de **Cópia**, uma por uma. Para se conectar, comece arrastando a caixa **verde** anexada à atividade de **Pesquisa** e solte-a na atividade de **Cópia**. Solte o botão do mouse quando visualizar a cor da borda da atividade de Cópia ficar **azul**.

    ![Conetar atividades de Pesquisa à atividade de Cópia](./media/tutorial-incremental-copy-multiple-tables-portal/connect-lookup-to-copy.png)
1. Selecione a atividade de **Cópia** no pipeline. Alterne para a guia **Origem** na janela **Propriedades**. 

    1. Selecione **SourceDataset** para o **Conjunto de dados de origem**. 
    1. Selecione **Consulta** para **Usar consulta**. 
    1. Insira a seguinte consulta SQL para **Consulta**.

        ```sql
        select * from @{item().TABLE_NAME} where @{item().WaterMark_Column} > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and @{item().WaterMark_Column} <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'        
        ```

        ![Atividade de cópia - configurações da fonte](./media/tutorial-incremental-copy-multiple-tables-portal/copy-source-settings.png)
1. Alterne para a guia **Coletor** e selecione **SinkDataset** para o campo **Conjunto de Dados do Coletor**. 
        
    ![Atividade de cópia - configurações do coletor](./media/tutorial-incremental-copy-multiple-tables-portal/copy-sink-settings.png)
1. Execute as seguintes etapas:

    1. Na propriedade **Dataset**, para o parâmetro **SinkTableName**, insira `@{item().TABLE_NAME}`.
    1. Na propriedade **Nome do Procedimento Armazenado**, insira `@{item().StoredProcedureNameForMergeOperation}`.
    1. Na propriedade **Tipo de Tabela**, insira `@{item().TableType}`.


        ![Atividade de cópia - parâmetros](./media/tutorial-incremental-copy-multiple-tables-portal/copy-activity-parameters.png)
1. Arraste e solte a atividade de **Procedimento armazenado** da caixa de ferramentas **Atividades** para a superfície do designer de pipeline. Conecte a atividade de **Cópia** à atividade de **Procedimento Armazenado**. 

    ![Atividade de cópia - parâmetros](./media/tutorial-incremental-copy-multiple-tables-portal/connect-copy-to-sproc.png)
1. Selecione a atividade **Procedimento Armazenado** no pipeline e digite **StoredProceduretoWriteWatermarkActivity** como **Nome** na guia **Geral** da janela **Propriedades**. 

    ![Atividade de procedimento armazenado - nome](./media/tutorial-incremental-copy-multiple-tables-portal/sproc-activity-name.png)
1. Alterne para a guia **Conta SQL** e selecione **AzureSqlDatabaseLinkedService** para o **Serviço Vinculado**.

    ![Atividade de procedimento armazenado - Conta SQL](./media/tutorial-incremental-copy-multiple-tables-portal/sproc-activity-sql-account.png)
1. Alterne para a guia **Procedimento armazenado** e execute as etapas a seguir:

    1. Para o **Nome do procedimento armazenado**, selecione `usp_write_watermark`. 
    1. Selecione **Parâmetro de importação**. 
    1. Especifique os seguintes valores para os parâmetros: 

        | NOME | Tipo | Valor | 
        | ---- | ---- | ----- |
        | LastModifiedtime | Datetime | `@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}` |
        | TableName | Cadeia de caracteres | `@{activity('LookupOldWaterMarkActivity').output.firstRow.TableName}` |
    
        ![Atividade de Procedimento armazenado - configurações de procedimento armazenado](./media/tutorial-incremental-copy-multiple-tables-portal/sproc-activity-sproc-settings.png)
1. No painel esquerdo, clique em **Publicar**. Essa ação publica as entidades que você criou para o serviço Data Factory. 

    ![Botão Publicar](./media/tutorial-incremental-copy-multiple-tables-portal/publish-button.png)
1. Aguarde até que você veja a mensagem **Publicado com êxito**. Para ver as notificações, clique no link **Mostrar Notificações**. Feche a janela de notificações clicando em **X**.

    ![Mostrar notificações](./media/tutorial-incremental-copy-multiple-tables-portal/notifications.png)

 
## <a name="run-the-pipeline"></a>Executar o pipeline

1. Na barra de ferramentas do pipeline, clique em **Gatilho** e clique em **Disparar Agora**.     

    ![Disparar agora](./media/tutorial-incremental-copy-multiple-tables-portal/trigger-now.png)
1. Na janela **Execução de Pipeline**, digite o valor a seguir para o parâmetro **tableList** e clique em **Concluir**. 

    ```
    [
        {
            "TABLE_NAME": "customer_table",
            "WaterMark_Column": "LastModifytime",
            "TableType": "DataTypeforCustomerTable",
            "StoredProcedureNameForMergeOperation": "usp_upsert_customer_table"
        },
        {
            "TABLE_NAME": "project_table",
            "WaterMark_Column": "Creationtime",
            "TableType": "DataTypeforProjectTable",
            "StoredProcedureNameForMergeOperation": "usp_upsert_project_table"
        }
    ]
    ```

    ![Argumentos de Execução do Pipeline](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-run-arguments.png)

## <a name="monitor-the-pipeline"></a>Monitorar o Pipeline

1. Alterne para a guia **Monitorar** à esquerda. Você pode ver a execução do pipeline disparado pelo **gatilho manual**. Clique no botão **Atualizar** para atualizar a lista. Os links na coluna **Ação** permitem a exibição de atividades em execução associadas ao pipeline de execução e executar novamente o pipeline. 

    ![Execuções de pipeline](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-runs.png)
1. Clique no link **Exibir Execuções da atividade** na coluna **Ações**. Você vê todas as execuções de atividade associadas à execução do pipeline selecionada. 

    ![Execuções de atividade](./media/tutorial-incremental-copy-multiple-tables-portal/activity-runs.png)

## <a name="review-the-results"></a>Revise os resultados
No SQL Server Management Studio, execute as seguintes consultas no banco de dados SQL de destino para verificar se os dados foram copiados das tabelas de origem para as tabelas de destino: 

**Consulta** 
```sql
select * from customer_table
```

**Saída**
```
===========================================
PersonID    Name    LastModifytime
===========================================
1           John    2017-09-01 00:56:00.000
2           Mike    2017-09-02 05:23:00.000
3           Alice   2017-09-03 02:36:00.000
4           Andy    2017-09-04 03:21:00.000
5           Anny    2017-09-05 08:06:00.000
```

**Consulta**

```sql
select * from project_table
```

**Saída**

```
===================================
Project     Creationtime
===================================
project1    2015-01-01 00:00:00.000
project2    2016-02-02 01:23:00.000
project3    2017-03-04 05:16:00.000
```

**Consulta**

```sql
select * from watermarktable
```

**Saída**

```
======================================
TableName       WatermarkValue
======================================
customer_table  2017-09-05 08:06:00.000
project_table   2017-03-04 05:16:00.000
```

Observe que os valores de marca d'água para ambas as tabelas foram atualizados. 

## <a name="add-more-data-to-the-source-tables"></a>Adicionar mais dados às tabelas de origem

Execute a consulta a seguir feita no banco de dados do SQL Server de origem para atualizar uma linha existente na customer_table. Insera uma nova linha em project_table. 

```sql
UPDATE customer_table
SET [LastModifytime] = '2017-09-08T00:00:00Z', [name]='NewName' where [PersonID] = 3

INSERT INTO project_table
(Project, Creationtime)
VALUES
('NewProject','10/1/2017 0:00:00 AM');
``` 

## <a name="rerun-the-pipeline"></a>Executar novamente o pipeline
1. Na janela do navegador da Web, alterne para a guia **Editar** à esquerda. 
1. Na barra de ferramentas do pipeline, clique em **Gatilho** e clique em **Disparar Agora**.   

    ![Disparar agora](./media/tutorial-incremental-copy-multiple-tables-portal/trigger-now.png)
1. Na janela **Execução de Pipeline**, digite o valor a seguir para o parâmetro **tableList** e clique em **Concluir**. 

    ```
    [
        {
            "TABLE_NAME": "customer_table",
            "WaterMark_Column": "LastModifytime",
            "TableType": "DataTypeforCustomerTable",
            "StoredProcedureNameForMergeOperation": "usp_upsert_customer_table"
        },
        {
            "TABLE_NAME": "project_table",
            "WaterMark_Column": "Creationtime",
            "TableType": "DataTypeforProjectTable",
            "StoredProcedureNameForMergeOperation": "usp_upsert_project_table"
        }
    ]
    ```

## <a name="monitor-the-pipeline-again"></a>Monitorar o pipeline novamente

1. Alterne para a guia **Monitorar** à esquerda. Você pode ver a execução do pipeline disparado pelo **gatilho manual**. Clique no botão **Atualizar** para atualizar a lista. Os links na coluna **Ação** permitem a exibição de atividades em execução associadas ao pipeline de execução e executar novamente o pipeline. 

    ![Execuções de pipeline](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-runs.png)
1. Clique no link **Exibir Execuções da atividade** na coluna **Ações**. Você vê todas as execuções de atividade associadas à execução do pipeline selecionada. 

    ![Execuções de atividade](./media/tutorial-incremental-copy-multiple-tables-portal/activity-runs.png) 

## <a name="review-the-final-results"></a>Examine os resultados finais
No SQL Server Management Studio, execute as seguintes consultas no banco de dados de destino para verificar se os dados atualizados/novos foram copiados das tabelas de origem para as tabelas de destino. 

**Consulta** 
```sql
select * from customer_table
```

**Saída**
```
===========================================
PersonID    Name    LastModifytime
===========================================
1           John    2017-09-01 00:56:00.000
2           Mike    2017-09-02 05:23:00.000
3           NewName 2017-09-08 00:00:00.000
4           Andy    2017-09-04 03:21:00.000
5           Anny    2017-09-05 08:06:00.000
```

Observe os novos valores de **Name** e **LastModifytime** para **PersonID** para o número 3. 

**Consulta**

```sql
select * from project_table
```

**Saída**

```
===================================
Project     Creationtime
===================================
project1    2015-01-01 00:00:00.000
project2    2016-02-02 01:23:00.000
project3    2017-03-04 05:16:00.000
NewProject  2017-10-01 00:00:00.000
```

Observe que a entrada **NewProject** foi adicionada a project_table. 

**Consulta**

```sql
select * from watermarktable
```

**Saída**

```
======================================
TableName       WatermarkValue
======================================
customer_table  2017-09-08 00:00:00.000
project_table   2017-10-01 00:00:00.000
```

Observe que os valores de marca d'água para ambas as tabelas foram atualizados.
     
## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você realizará os seguintes procedimentos: 

> [!div class="checklist"]
> * Preparará os armazenamentos de dados de origem e destino.
> * Criar um data factory.
> * Criar um Integration Runtime (IR) auto-hospedado.
> * Instalar o Integration Runtime.
> * Criar serviços vinculados. 
> * Criar os conjuntos de dados de origem, de coletor e de marca-d'água.
> * Crie, execute e monitore um pipeline.
> * Revise os resultados.
> * Adicionar ou atualizar dados nas tabelas de origem.
> * Executar novamente e monitorar o pipeline.
> * Examinar os resultados finais.

Avance para o tutorial a seguir para saber mais sobre como transformar dados usando um cluster Spark no Azure:

> [!div class="nextstepaction"]
>[Carregar incrementalmente os dados do Banco de Dados SQL do Azure para o Armazenamento de Blobs do Azure usando a tecnologia Controle de Alterações](tutorial-incremental-copy-change-tracking-feature-portal.md)


