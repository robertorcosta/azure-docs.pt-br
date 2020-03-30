---
title: Carregar dados no SQL Data Warehouse do Azure
description: Usar o Azure Data Factory para copiar dados para o Azure SQL Data Warehouse
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 06/22/2018
ms.openlocfilehash: dc6d8c9da749e7ee60713f4357dc6d46051d89f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131282"
---
# <a name="load-data-into-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Carregar dados no SQL Data Warehouse usando o Azure Data Factory

O [Azure SQL Data Warehouse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) é um banco de dados baseado em nuvem e expansível com capacidade de processar volumes imensos de dados, relacionais e não relacionais. O SQL Data Warehouse é criado em arquitetura de processamento paralelo maciço (MPP) que é otimizado para cargas de trabalho do data warehouse. Ele oferece a elasticidade da nuvem com a flexibilidade de dimensionar o armazenamento e a computação de modo independente.

A introdução ao Azure SQL Data Warehouse agora é mais fácil do que nunca quando você usar o Azure Data Factory. O Azure Data Factory é um serviço de integração de dados baseado em nuvem completamente gerenciado. Você pode usar o serviço para preencher o SQL Data Warehouse com dados do seu sistema existente e economizar tempo ao construir suas soluções analíticas.

O Azure Data Factory oferece os seguintes benefícios para carregar dados no Azure Data SQL Data Warehouse:

* **Fácil de configurar**: assistente intuitivo de 5 etapas sem nenhum script necessário.
* **Suporte a armazenamento de dados rico**: Suporte incorporado para um rico conjunto de armazenamentos de dados on-premises e baseados em nuvem. Para obter uma lista detalhada, consulte a tabela de [Suporte para repositórios de dados](copy-activity-overview.md#supported-data-stores-and-formats).
* **Seguro e compatível**: os dados são transferidos por HTTPS, ou ExpressRoute. A presença do serviço global garante que os dados nunca saiam do limite geográfico.
* **Desempenho incomparável usando PolyBase**: usar o Polybase é a maneira mais eficiente para mover dados ao Azure SQL Data Warehouse. Use o recurso de objeto binário em etapas para obter velocidades de alta carga de todos os tipos de armazenamentos de dados, incluindo armazenamento Azure Blob e Data Lake Store. (A Polybase suporta o armazenamento Azure Blob e o Azure Data Lake Store por padrão.) Para obter detalhes, consulte [O desempenho da atividade de copiar](copy-activity-performance.md).

Este artigo mostra como usar a ferramenta de cópia de dados do Data Factory para _carregar dados do Banco de Dados SQL do Azure no Azure SQL Data Warehouse_. Você pode seguir as etapas semelhantes para copiar dados de outros tipos de armazenamentos de dados.

> [!NOTE]
> Para obter mais informações, consulte [Copiar dados para ou da Azure SQL Data Warehouse usando o Azure Data Factory](connector-azure-sql-data-warehouse.md).

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure: Caso você não tenha uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Azure SQL Data Warehouse: o data warehouse contém os dados copiados do Banco de Dados SQL. Se você não tiver um Azure SQL Data Warehouse, veja as instruções em [Criar um SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md).
* Azure SQL Database: este tutorial copia os dados do banco de dados SQL do Azure com os dados modelo do Adventure Works LT. Você pode criar um banco de dados seguindo as instruções em [Criar um banco de dados SQL do Azure](../sql-database/sql-database-get-started-portal.md).
* Conta de armazenamento do Azure: armazenamento do Azure é usado como _blob_ de processo de reparo na operação de cópia em massa. Se você não tiver uma conta de armazenamento do Azure, confira as instruções em [Criar uma conta de armazenamento](../storage/common/storage-account-create.md).

## <a name="create-a-data-factory"></a>Criar uma data factory

1. No menu à esquerda, selecione **Criar um recurso** > **Data + Analytics** > **Data Factory**:

   ![Seleção de Data Factory no painel "Novo"](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Na página **Novo data factory**, forneça os valores para os campos que estão mostrados na imagem a seguir:

   ![Página de novo data factory](./media/load-azure-sql-data-warehouse/new-azure-data-factory.png)

    * **Nome**: insira um nome exclusivo para o seu Azure data factory. Se você receber o erro "Nome do Data factory \"LoadSQLDWDemo\" não está disponível," digite um nome diferente para o data factory. Por exemplo, use o nome _**seunome**_**ADFTutorialDataFactory**. Tente criar o data factory novamente. Para ver as regras de nomenclatura de artefatos do Data Factory, confira [Regras de nomenclatura do Data Factory](naming-rules.md).
    * **Assinatura**: selecione a assinatura do Azure na qual você deseja criar o data factory. 
    * **Grupo de Recursos**: Selecione um grupo de recursos existente na lista suspensa ou selecione a opção **Criar novo** e digite o nome de um grupo de recursos. Para saber mais sobre grupos de recursos, consulte [Usando grupos de recursos para gerenciar recursos do Azure](../azure-resource-manager/management/overview.md).  
    * **Versão**: selecione **V2**.
    * **Local**: selecione um local para o data factory. Somente os locais com suporte são exibidos na lista suspensa. Os armazenamentos de dados que são usados pela data factory podem estar em outros locais e regiões. Esses armazenamentos de dados incluem Azure Data Lake Store, Azure Storage, Banco de Dados SQL do Azure e assim por diante.

3. Selecione **Criar**.
4. Após a conclusão da criação, vá para o seu data factory. Você verá a home page **Data Factory** conforme mostrado na imagem a seguir:

   ![Página inicial do data factory](./media/load-azure-sql-data-warehouse/data-factory-home-page.png)

   Selecione o bloco **Autor & Monitor** para iniciar o aplicativo de integração de dados em uma guia separada.

## <a name="load-data-into-azure-sql-data-warehouse"></a>Carregar dados no SQL Data Warehouse do Azure

1. Na página **Introdução**, selecione o bloco **Copy Data** para iniciar a ferramenta Copy Data:

   ![Bloco Ferramenta Copy Data](./media/load-azure-sql-data-warehouse/copy-data-tool-tile.png)
1. Na página **Propriedades**, especifique **CopyFromSQLToSQLDW** para o campo **Nome da tarefa** e clique em **Avançar**:

    ![Página Propriedades](./media/load-azure-sql-data-warehouse/copy-data-tool-properties-page.png)

1. Na página **Armazenamento de dados de origem**, conclua as etapas a seguir:

    a. clique em **+ Cria nova conexão**:

    ![Página Armazenamento de dados de origem](./media/load-azure-sql-data-warehouse/new-source-linked-service.png)

    b. Selecione **Banco de Dados SQL do Azure** da galeria e selecione **Continuar**. Você pode digitar "SQL" na caixa de pesquisa para filtrar os conectores.

    ![Selecione o BD SQL do Azure](./media/load-azure-sql-data-warehouse/select-azure-sql-db-source.png)

    c. Na página **Novo Serviço Vinculado,** selecione o nome do servidor e o nome DB na lista de saque e especifique o nome de usuário e a senha. Clique em **Testar conectividade** para validar as configurações e selecione **Concluir**.

    ![Configure o Banco de Dados SQL do Azure](./media/load-azure-sql-data-warehouse/configure-azure-sql-db.png)

    d. Selecione o serviço vinculado recém-criado como fonte, depois clique **Avançar**.

    ![Selecione o serviço de origem vinculado](./media/load-azure-sql-data-warehouse/select-source-linked-service.png)

1. Em **Selecione as tabelas das quais copiar os dados ou use uma página de consulta personalizada**, insira **SalesLT** para filtrar as tabelas. Escolha a caixa **(Selecionar tudo)** para usar todas as tabelas para a cópia e, em seguida, selecione **Avançar**:

    ![Selecionar tabelas de origem](./media/load-azure-sql-data-warehouse/select-source-tables.png)

1. Na página **Armazenamento de dados de destino**, conclua as etapas a seguir:

    a. Clique **em + Crie uma nova conexão** para adicionar uma conexão

    ![Página de armazenamento de dados do coletor](./media/load-azure-sql-data-warehouse/new-sink-linked-service.png)

    b. Selecione **SQL Data Warehouse do Azure** da galeria e selecione **Avançar**.

    ![Selecione o SQL DW do Azure](./media/load-azure-sql-data-warehouse/select-azure-sql-dw-sink.png)

    c. Na página **Novo Serviço Vinculado,** selecione o nome do servidor e o nome DB na lista de saque e especifique o nome de usuário e a senha. Clique em **Testar conectividade** para validar as configurações e selecione **Concluir**.

    ![Configure o SQL DW do Azure](./media/load-azure-sql-data-warehouse/configure-azure-sql-dw.png)

    d. Selecione o serviço vinculado criado recentemente como coletor e clique em **Avançar**.

    ![Selecione o serviço vinculado do coletor](./media/load-azure-sql-data-warehouse/select-sink-linked-service.png)

1. Na página **Mapeamento de tabela**, examine o conteúdo e selecione **Avançar**. Um mapeamento de tabela inteligente é exibido. As tabelas de origem são mapeadas para as tabelas de destino com base nos nomes de tabela. Se a tabela de origem não existir no destino, por padrão o Azure Data Factory cria uma tabela de destino com o mesmo nome por padrão. Você também pode mapear uma tabela de origem para uma tabela de destino existente.

   > [!NOTE]
   > A criação de tabela automática para o coletor do SQL Data Warehouse aplica-se quando o SQL Server ou banco de dados SQL do Azure for a origem. Se você copiar seus dados de outro armazenamento de dados de origem, você precisa criar previamente o esquema no coletor SQL Data Warehouse do Azure antes de executar a cópia de dados.

   ![Página Mapeamento de tabela](./media/load-azure-sql-data-warehouse/table-mapping.png)

1. Na página **Mapeamento de esquema**, examine o conteúdo e selecione **Avançar**. O mapeamento de tabela inteligente é baseado no nome da coluna. Se você deixar o Data Factory criar automaticamente as tabelas, a conversão do tipo de dados pode ocorrer quando houver incompatibilidades entre a origem e os armazenamentos de destino. Se houver uma conversão de tipo de dados sem suporte entre a coluna de origem e de destino, você verá uma mensagem de erro próximo à tabela correspondente.

    ![Página Mapeamento de esquema](./media/load-azure-sql-data-warehouse/schema-mapping.png)

1. Na página **Configurações**, execute as seguintes etapas:

    a. Na seção **Configurações de preparo**, clique em **+ Novo** para criar um novo armazenamento de preparo. O armazenamento é usado para preparar os dados antes de serem carregados no SQL Data Warehouse usando o PolyBase. Depois que a cópia for concluída, os dados provisórios no Azure Storage serão limpos automaticamente.

    ![Configure o preparo](./media/load-azure-sql-data-warehouse/configure-staging.png)

    b. Na página **Novo Serviço Vinculado**, selecione a conta de armazenamento e, em seguida, selecione **Concluir**.

    ![Configure o Armazenamento do Microsoft Azure](./media/load-azure-sql-data-warehouse/configure-blob-storage.png)

    c. Na seção **Configurações avançadas**, desmarque a opção **Usar tipo padrão** e, em seguida, selecione **Avançar**.

    ![Configure o PolyBase](./media/load-azure-sql-data-warehouse/configure-polybase.png)

1. Na página **Resumo**, examine as configurações e selecione **Avançar**:

    ![Página Resumo](./media/load-azure-sql-data-warehouse/summary-page.png)
1. Na **Página Implantação**, selecione **Monitor** para monitorar o pipeline (tarefa):

    ![Página Implantação](./media/load-azure-sql-data-warehouse/deployment-page.png)
1. Observe que a guia **Monitor** à esquerda é selecionada automaticamente. A coluna **Ações** inclui links para exibir detalhes da execução da atividade e para reexecutar o pipeline:

    ![Monitorar execuções de pipeline](./media/load-azure-sql-data-warehouse/pipeline-monitoring.png)
1. Para exibir as execuções de atividade associadas com a execução do pipeline, selecione o link **Exibir as Execuções de Atividade** na coluna **Ações**. Para voltar à exibição de execuções de pipeline, selecione o link **Pipelines** na parte superior. Selecione **Atualizar** para atualizar a lista.

    ![Monitorar execuções de atividade](./media/load-azure-sql-data-warehouse/activity-monitoring.png)

1. Para monitorar os detalhes de execução de cada atividade de cópia, selecione o link **Detalhes** em **Ações** no modo de exibição de monitoramento de atividades. Você pode monitorar detalhes como o volume de dados copiados da fonte para o coletor, taxa de transferência de dados, etapas de execução com duração correspondente e configurações usadas:

    ![Monitorar detalhes da execução da atividade](./media/load-azure-sql-data-warehouse/monitor-activity-run-details.png)

## <a name="next-steps"></a>Próximas etapas

Ir para o seguinte artigo para saber mais sobre o suporte do SQL Data Warehouse do Azure:

> [!div class="nextstepaction"]
>[Conector do armazém de dados Azure SQL](connector-azure-sql-data-warehouse.md)
