---
title: Copiar dados usando a ferramenta de Copiar Dados do Azure
description: Crie um Azure Data Factory e use a ferramenta Copy Data para copiar dados de Armazenamento de blobs do Azure para o banco de dados SQL.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.date: 09/11/2018
ms.author: jingwang
ms.openlocfilehash: 4646d7429dc4b3286f6af8861eaf7f1e6e27a760
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683592"
---
# <a name="copy-data-from-azure-blob-storage-to-a-sql-database-by-using-the-copy-data-tool"></a>Copie dados do Armazenamento de blobs do Azure para um banco de dados SQL usando a ferramenta Copy Data
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Versão atual](tutorial-copy-data-tool.md)

Neste tutorial, você pode usar o portal do Azure para criar um Data Factory. Em seguida, use a ferramenta Copiar Dados para criar um pipeline que copia dados de um Armazenamento de blobs do Azure para um banco de dados SQL.

> [!NOTE]
> Se estiver se familiarizando com o Azure Data Factory, confira [Introdução ao Azure Data Factory](introduction.md).

Neste tutorial, você executa as seguintes etapas:
> [!div class="checklist"]
> * Criar um data factory.
> * Usar a ferramenta Copy Data para criar um pipeline.
> * Monitore as execuções de pipeline e de atividade.

## <a name="prerequisites"></a>Pré-requisitos

* **Assinatura do Azure**: Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* **Conta de Armazenamento do Azure**: Use o armazenamento de Blobs como um armazenamento de dados de _origem_. Se você não tiver uma conta de armazenamento do Azure, confira as instruções em [Criar uma conta de armazenamento](../storage/common/storage-quickstart-create-account.md).
* **Banco de Dados SQL do Azure**: Use um banco de dados SQL como o armazenamento de dados do _coletor_. Se você não tiver um banco de dados SQL, confira as instruções em [Criar um banco de dados SQL](../sql-database/sql-database-get-started-portal.md).

### <a name="create-a-blob-and-a-sql-table"></a>Criar um blob e uma tabela SQL

Prepare o Armazenamento de blobs e o banco de dados SQL para o tutorial, executando as etapas a seguir.

#### <a name="create-a-source-blob"></a>Criar um blob de origem

1. Inicie o **Bloco de notas**. Copie o texto a seguir e salve-o em um arquivo chamado **inputEmp.txt** no disco:

    ```
    John|Doe
    Jane|Doe
    ```

1. Crie um contêiner chamado **adfv2tutorial** e carregue o arquivo inputEmp.txt no contêiner. É possível usar o portal do Azure ou várias ferramentas como o [Gerenciador de Armazenamento do Azure](https://storageexplorer.com/) para executar essas tarefas.

#### <a name="create-a-sink-sql-table"></a>Criar uma tabela do SQL de coletor

1. Use o script SQL a seguir para criar uma tabela chamada **dbo.emp** em seu banco de dados SQL:

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

2. Permita que os serviços do Azure acessem o SQL Server. Verifique se a configuração **Permitir acesso aos serviços do Azure** está habilitada para o servidor que está executando o Banco de Dados SQL. Essa configuração permite que o Data Factory grave dados em sua instância de banco de dados. Para verificar e ativar essa configuração, vá até seu servidor do Azure SQL > Visão geral > Configurar o firewall do servidor > defina a opção **Permitir acesso aos serviços do Azure** para **ATIVADA**.

## <a name="create-a-data-factory"></a>Criar uma data factory

1. No menu à esquerda, selecione **Criar um recurso** > **Analytics** > **Data Factory**:
    
    ![Criação do novo data factory](./media/doc-common-process/new-azure-data-factory-menu.png)
1. Na página **Novo data factory**, em **Nome**, insira **ADFTutorialDataFactory**.

    O nome do seu data factory deve ser _globalmente exclusivo_. Você deve ver a seguinte mensagem de erro:
    
    ![Mensagem de erro do novo data factory](./media/doc-common-process/name-not-available-error.png)

    Se você receber uma mensagem de erro sobre o valor do nome, insira um nome diferente para o data factory. Por exemplo, use o nome _**seunome**_ **ADFTutorialDataFactory**. Para ver as regras de nomenclatura de artefatos do Data Factory, confira [Regras de nomenclatura do Data Factory](naming-rules.md).
1. Selecione a **assinatura** do Azure na qual deseja criar o novo data factory.
1. Em **Grupo de Recursos**, use uma das seguintes etapas:
    
    a. Selecione **Usar existente**e selecione um grupo de recursos existente na lista suspensa.

    b. Selecione **Criar novo**e insira o nome de um grupo de recursos.
    
    Para saber mais sobre grupos de recursos, confira [Usar grupos de recursos para gerenciar recursos do Azure](../azure-resource-manager/resource-group-overview.md).

1. Em **versão**, selecione **V2** para a versão.
1. Em **local**, selecione o local para o data factory. Somente os locais com suporte são exibidos na lista suspensa. Os armazenamentos de dados (por exemplo, Armazenamento do Azure e Banco de Dados SQL) e os serviços de computação (por exemplo, Azure HDInsight) usados pelo seu data factory podem estar em outros locais e regiões.
1. Selecione **Criar**.

1. Depois de finalizada a criação, a home page do **Data Factory** é exibida.
    
    ![Página inicial do data factory](./media/doc-common-process/data-factory-home-page.png)
1. Para iniciar a interface do usuário do Azure Data Factory em uma guia separada, selecione o bloco **Criar e Monitorar**.

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Use a ferramenta Copy Data para criar um pipeline

1. Na página **Introdução**, selecione o bloco **Copy Data** para iniciar a ferramenta Copy Data.

    ![Bloco Ferramenta Copy Data](./media/doc-common-process/get-started-page.png)
1. Na página **Propriedades**, em **Nome da tarefa**, insira **CopyFromBlobToSqlPipeline**. Em seguida, selecione **Avançar**. A interface do usuário do Data Factory cria um pipeline com o nome especificado da tarefa.

1. Na página **Configurações do armazenamento de dados**, conclua as seguintes etapas:

    a. Clique em **+ Criar nova conexão** para adicionar uma conexão

    b. Selecione **Armazenamento de Blobs do Azure** na galeria e, em seguida, selecione **Continuar**.

    c. Na página **Novo Serviço Vinculado**, selecione a conta de armazenamento a partir da lista **nome da conta de armazenamento** e selecione **Concluir**.

    d. Selecione o serviço vinculado recém-criado como fonte, depois clique **Avançar**.

    ![Selecione o serviço de origem vinculado](./media/tutorial-copy-data-tool/select-source-linked-service.png)

1. Na página **Escolha o arquivo de entrada ou a pasta**, execute as seguintes etapas:
    
    a. Clique em **Navegar** para navegar até a pasta **adfv2tutorial/input**, selecione o arquivo **inputEmp.txt** e então clique em **Escolher**.

    b. Clique em **Avançar** para mover-se para a próxima etapa.

1. Na página **Configurações de formato de arquivo**, observe que a ferramenta detecta automaticamente os delimitadores de linha e coluna. Selecione **Avançar**. Também é possível visualizar os dados e exibir o esquema dos dados de entrada nesta página.

    ![Configurações de formato de arquivo](./media/tutorial-copy-data-tool/file-format-settings-page.png)
1. Na página **Armazenamento de dados de destino**, conclua as seguintes etapas:

    a. Clique em **+ Criar nova conexão** para adicionar uma conexão

    b. Selecione **Banco de Dados SQL do Azure** por meio da galeria e, em seguida, selecione **Continuar**.

    c. Na página **Novo Serviço Vinculado**, selecione o nome do servidor e o nome do banco de dados na lista suspensa e especifique o nome de usuário e senha, então selecione **Concluir**.

    ![Configure o Banco de Dados SQL do Azure](./media/tutorial-copy-data-tool/config-azure-sql-db.png)

    d. Selecione o serviço vinculado criado recentemente como coletor e clique em **Avançar**.

    ![Selecione o serviço vinculado do coletor](./media/tutorial-copy-data-tool/select-sink-linked-service.png)

1. Na página **Mapeamento de tabela**, selecione a tabela **[dbo].[emp]** e clique em **Avançar**.

1. Na página **Mapeamento de esquema**, observe que a primeira e segunda colunas no arquivo de entrada são mapeadas para as colunas **FirstName** e **LastName** da tabela **emp**. Selecione **Avançar**.

    ![Página Mapeamento de esquema](./media/tutorial-copy-data-tool/schema-mapping.png)
1. Na página **Configurações**, selecione **Avançar**.
1. Na página **Resumo**, analise as configurações e selecione **Avançar**.
1. Na **página Implantação**, selecione **Monitorar** para monitorar o pipeline (tarefa).
1. Observe que a guia **Monitor** à esquerda é selecionada automaticamente. A coluna **Ações** inclui links para exibir detalhes da execução da atividade e executar o pipeline novamente. Selecione **Atualizar** para atualizar a lista.

1. Para exibir as execuções de atividade associadas com a execução do pipeline, selecione o link **Exibir as Execuções de Atividade** na coluna **Ações**. Para obter detalhes sobre a operação de cópia, selecione o link **Detalhes** (ícone de óculos) na coluna **Ações**. Para voltar à exibição de Execuções de Pipelines, selecione o link **Execuções de Pipelines** na parte superior. Para atualizar a exibição, selecione **Atualizar**.

    ![Monitorar execuções de atividade](./media/tutorial-copy-data-tool/activity-monitoring.png)


1. Verifique se os dados são inseridos na tabela **emp** no seu banco de dados SQL.


1. Selecione a guia **Criar** à esquerda para alternar para o modo de edição. É possível atualizar os serviços vinculados, os conjuntos de dados e os pipelines criados com a ferramenta usando o editor. Para obter detalhes sobre essas entidades na IU do Data Factory, confira em [a versão do portal do Azure deste tutorial](tutorial-copy-data-portal.md).

## <a name="next-steps"></a>Próximas etapas
Neste exemplo, o pipeline copia os dados de um Armazenamento de blobs para um banco de dados SQL. Você aprendeu como:

> [!div class="checklist"]
> * Criar um data factory.
> * Usar a ferramenta Copy Data para criar um pipeline.
> * Monitore as execuções de pipeline e de atividade.

Avance para o tutorial a seguir para saber mais sobre como copiar dados do local para a nuvem:

>[!div class="nextstepaction"]
>[Copiar dados do local para a nuvem](tutorial-hybrid-copy-data-tool.md)
