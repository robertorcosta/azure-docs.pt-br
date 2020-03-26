---
title: Usar o portal do Azure para criar um pipeline do data factory
description: Este tutorial fornece instruções passo a passo para usar o portal do Azure a fim de criar um data factory com um pipeline. O pipeline usa a atividade de cópia para copiar dados do Armazenamento de blobs do Azure para um banco de dados SQL do Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 06/21/2018
ms.author: jingwang
ms.openlocfilehash: 135a18f275137e72b5ff4d79f6a32bd39bd9c00c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "75977397"
---
# <a name="copy-data-from-azure-blob-storage-to-a-sql-database-by-using-azure-data-factory"></a>Copiar dados do Armazenamento de Blobs do Azure para um banco de dados SQL usando o Azure Data Factory
Neste tutorial, você criará um data factory ao usar a interface do usuário do Azure Data Factory. O pipeline neste data factory copia dados do Armazenamento de Blobs do Azure para um banco de dados SQL. O padrão de configuração neste tutorial aplica-se a cópia de um armazenamento de dados baseado em arquivo para um armazenamento de dados relacional. Para obter uma lista de armazenamentos de dados com suporte como origens e coletores, confira a tabela [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

> [!NOTE]
> - Se estiver se familiarizando com o Data Factory, confira a [Introdução ao Azure Data Factory](introduction.md).

Neste tutorial, você executa as seguintes etapas:

> [!div class="checklist"]
> * Criar um data factory.
> * Criar um pipeline com uma atividade de cópia.
> * Executar teste do pipeline.
> * Disparar o pipeline manualmente.
> * Disparar o pipeline em um cronograma.
> * Monitore as execuções de pipeline e de atividade.

## <a name="prerequisites"></a>Pré-requisitos
* **Assinatura do Azure**. Caso você não tenha uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.
* **Conta de Armazenamento do Azure**. Você usa o armazenamento de blobs como um armazenamento de dados de *origem*. Se você não tiver uma conta de armazenamento, confira [Criar uma conta de armazenamento do Azure](../storage/common/storage-account-create.md) a fim de conhecer as etapas para criar uma.
* **Banco de dados SQL do Azure**. Você usa o banco de dados como um armazenamento de dados de *coletor*. Se você não tiver um banco de dados SQL, confira [Criar um banco de dados SQL](../sql-database/sql-database-get-started-portal.md) a fim de conhecer as etapas para criar um.

### <a name="create-a-blob-and-a-sql-table"></a>Criar um blob e uma tabela SQL

Agora, prepare seu armazenamento de blobs e o banco de dados SQL para o tutorial executando as etapas a seguir.

#### <a name="create-a-source-blob"></a>Criar um blob de origem

1. Inicie o Bloco de notas. Copie o texto a seguir e salve-o como um arquivo **emp.txt** no disco:

    ```
    John,Doe
    Jane,Doe
    ```

1. Crie um contêiner chamado **adftutorial** no seu Armazenamento de blobs. Crie uma pasta chamada **input** nesse contêiner. Em seguida, carregue o arquivo **emp.txt** na pasta **input**. Use as ferramentas ou o portal do Azure, como [Gerenciador de Armazenamento do Azure](https://storageexplorer.com/) para realizar essas tarefas.

#### <a name="create-a-sink-sql-table"></a>Criar uma tabela do SQL de coletor

1. Use o script SQL a seguir para criar a tabela **dbo.emp** em seu banco de dados SQL:

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

1. Permita que os serviços do Azure acessem o SQL Server. Verifique se a opção **Permitir acesso aos serviços do Azure** está **ATIVADA** para o SQL Server, para que o Data Factory possa gravar dados no SQL Server. Para verificar e ativar essa configuração, vá até seu servidor do Azure SQL > Visão geral > Configurar o firewall do servidor > defina a opção **Permitir acesso aos serviços do Azure** para **ATIVADA**.

## <a name="create-a-data-factory"></a>Criar uma data factory
Nesta etapa, você cria um data factory e inicia a interface do usuário do Data Factory para criar um pipeline no data factory.

1. Abra o **Microsoft Edge** ou **Google Chrome**. Atualmente, a interface do usuário do Data Factory tem suporte apenas nos navegadores da Web Microsoft Edge e Google Chrome.
2. No menu à esquerda, selecione **Criar um recurso** > **Analytics** > **Data Factory**:

   ![Seleção de Data Factory no painel "Novo"](./media/doc-common-process/new-azure-data-factory-menu.png)

3. Na página **Novo data factory**, em **Nome**, insira **ADFTutorialDataFactory**.

   O nome do Azure Data Factory deve ser *globalmente exclusivo*. Se você receber uma mensagem de erro sobre o valor do nome, insira um nome diferente para o data factory. (por exemplo, yournameADFTutorialDataFactory). Para ver as regras de nomenclatura para artefatos do Data Factory, confira [Data Factory – Regras de nomenclatura](naming-rules.md).

     ![Novo data factory](./media/doc-common-process/name-not-available-error.png)
4. Selecione a **assinatura** do Azure na qual deseja criar o data factory.
5. Em **Grupo de Recursos**, use uma das seguintes etapas:

    a. Selecione **Usar existente**e selecione um grupo de recursos existente na lista suspensa.

    b. Selecione **Criar novo**e insira o nome de um grupo de recursos. 
         
    Para saber mais sobre grupos de recursos, confira [Usar grupos de recursos para gerenciar recursos do Azure](../azure-resource-manager/management/overview.md). 
6. Em **Versão**, selecione **V2**.
7. Em **Local**, informe uma localização para o data factory. Apenas os locais com suporte são exibidos na lista suspensa. Os armazenamentos de dados (por exemplo, Armazenamento do Azure e Banco de Dados SQL) e os serviços de computação (por exemplo, Azure HDInsight) usados pelo data factory podem estar em outras regiões.
8. Selecione **Criar**.
9. Depois que a criação for concluída, você verá o aviso no centro de notificações. Selecione **Ir para o recurso** para navegar até a página do Data Factory.
10. Clique em **Criar e Monitorar** para iniciar a IU do Azure Data Factory em uma guia separada.


## <a name="create-a-pipeline"></a>Criar um pipeline
Nesta etapa, você criará um pipeline com a atividade de cópia no data factory. A Atividade de cópia copia dados do Armazenamento de blobs para o Banco de Dados SQL. No [Tutorial de início rápido](quickstart-create-data-factory-portal.md), você criou um pipeline seguindo estas etapas:

1. Criar o serviço vinculado.
1. Criar conjuntos de dados de entrada e saída.
1. Crie um pipeline.

Neste tutorial, inicie com a criação do pipeline. Em seguida, crie conjuntos de dados e serviços vinculados quando forem necessários para configurar o pipeline.

1. Na página **Introdução**, selecione **Criar pipeline**.

   ![Criar um pipeline](./media/doc-common-process/get-started-page.png)
1. Na guia **Geral** do pipeline, insira **CopyPipeline** para o **Nome** do pipeline.

1. Na caixa de ferramentas de **Atividades**, expanda a categoria **Mover e transformar** e arraste e solte a atividade **Copiar dados** da caixa de ferramentas para a superfície do designer do pipeline. Especifique **CopyFromBlobToSql** para o **Nome**.

    ![Atividade de cópia](./media/tutorial-copy-data-portal/drag-drop-copy-activity.png)

### <a name="configure-source"></a>Configurar fonte

1. Vá para a guia **Origem**. Selecione **+ Novo** para criar um conjunto de dados de origem.

1. Na caixa de diálogo **Novo conjunto de dados**, selecione **Armazenamento de Blobs do Azure** e, em seguida, selecione **Continuar**. Os dados de origem estão em um Armazenamento de blobs e, portanto, você deve selecionar o **Armazenamento de Blobs do Azure** para o conjunto de dados de origem.

1. Na caixa de diálogo **Selecionar formato**, escolha o tipo de formato dos seus dados e, em seguida, selecione **Continuar**.

    ![Tipo de formato de dado](./media/doc-common-process/select-data-format.png)

1. Na caixa de diálogo **Definir propriedades**, insira **SourceBlobDataset** como o nome. Selecione **+ Novo** ao lado da caixa de texto **Serviço vinculado**.

1. Na caixa de diálogo **Novo serviço vinculado (Armazenamento de Blobs do Azure)** , insira **AzureStorageLinkedService** como o nome e selecione sua conta de armazenamento na lista **Nome da conta de armazenamento**. Teste a conexão e, em seguida, selecione **Concluir** para implantar o serviço vinculado.

1. Depois que o serviço vinculado for criado, ele será redirecionado de volta para a página **Definir propriedades**. Ao lado de **Caminho do arquivo**, selecione **Procurar**.

1. Navegue até a pasta **adftutorial/entrada**, selecione o arquivo **emp.txt** e selecione **Concluir**.

1. Ele navega automaticamente para a página do pipeline. Na guia **Origem**, confirme se **SourceBlobDataset** está selecionado. Para visualizar os dados da página, selecione **Visualizar dados**.

    ![Conjunto de dados de origem](./media/tutorial-copy-data-portal/source-dataset-selected.png)

### <a name="configure-sink"></a>Configurar coletor

1. Alterne para a guia **Coletor** e selecione **+ Novo** para criar um conjunto de dados do coletor.

1. Na caixa de diálogo **Novo conjunto de dados**, insira "SQL" na caixa de pesquisa para filtrar os conectores, selecione **Banco de Dados SQL do Azure** e, em seguida, selecione **Continuar**. Neste tutorial, você copia dados para um banco de dados SQL.

1. Na caixa de diálogo **Definir propriedades**, insira **OutputSqlDataset** como o nome. Selecione **+ Novo** ao lado da caixa de texto **Serviço vinculado**. Um conjunto de dados deve ser associado um serviço vinculado. O serviço vinculado tem a cadeia de conexão que o Data Factory usa para se conectar ao banco de dados SQL no runtime. O conjunto de dados especifica o contêiner, pasta e arquivo (opcional) para o qual os dados são copiados.

1. Na caixa de diálogo **Novo serviço vinculado (Banco de Dados SQL do Azure)** , execute as etapas a seguir:

    a. Em **Nome**, insira **AzureSqlDatabaseLinkedService**.

    b. Em **Nome do servidor**, selecione sua instância do SQL Server.

    c. Em **Nome do banco de dados**, selecione seu banco de dados SQL.

    d. Em **Nome de usuário**, insira o nome do usuário.

    e. Em **Senha**, insira a senha do usuário.

    f. Selecione **Testar conectividade** para testar a conexão.

    g. Para implantar o serviço vinculado, selecione **Concluir**.

    ![Salvar o novo serviço vinculado](./media/tutorial-copy-data-portal/new-azure-sql-linked-service-window.png)

1. Ele navega automaticamente para a caixa de diálogo **Definir propriedades**. Em **Tabela**, selecione **[dbo].[emp]** . Em seguida, selecione **Concluir**.

1. Alterne para a guia com o pipeline e, em **Conjunto de Dados do Coletor**, confirme se **OutputSqlDataset** está selecionado.

    ![Guia Pipeline](./media/tutorial-copy-data-portal/pipeline-tab-2.png)       

Opcionalmente, é possível mapear o esquema da origem para o esquema de destino correspondente seguindo [Mapeamento de esquema na atividade de cópia ](copy-activity-schema-and-type-mapping.md)

## <a name="validate-the-pipeline"></a>Validar o pipeline
Selecione **Validar** na barra de ferramentas para validar as configurações de pipeline.

Você pode ver o código JSON associado ao pipeline clicando em **Código**, no canto superior direito.

## <a name="debug-and-publish-the-pipeline"></a>Depurar e publicar o pipeline
Você pode depurar um pipeline antes de publicar os artefatos (serviços vinculados, conjuntos de dados e pipeline) no Data Factory ou em seu próprio repositório Git do Azure Repos.

1. Para depurar o pipeline, selecione **Depurar** na barra de ferramentas. Você verá o status da execução do pipeline na guia **Saída** na parte inferior da janela.

1. Depois que o pipeline for executado corretamente, na barra de ferramentas superior, selecione **Publicar Tudo**. Esta ação publica as entidades (conjuntos de dados e pipelines) criadas por você anteriormente no Data Factory.

1. Aguarde até que você veja a mensagem **Publicado com êxito**. Para ver as mensagens de notificação, clique na guia **Mostrar Notificações** no canto superior direito (botão de sino).

## <a name="trigger-the-pipeline-manually"></a>Disparar o pipeline manualmente
Nesta etapa, você aciona manualmente o pipeline publicado na etapa anterior.

1. Selecione **Adicionar gatilho** na barra de ferramentas e selecione **Disparar Agora**. Na página **Execução de Pipeline**, selecione **Concluir**.  

1. Vá para a guia **Monitorar** à esquerda. Você verá uma execução do pipeline que é disparada por um gatilho manual. Você pode usar os links na coluna **Ações** para exibir detalhes da atividade e executar o pipeline novamente.

    ![Monitorar execuções de pipeline](./media/tutorial-copy-data-portal/monitor-pipeline.png)

1. Selecione o link **Exibir atividades em execução** na coluna **Ações** para ver a atividade em execução associada à execução do pipeline. Neste exemplo, há apenas uma atividade, então você vê apenas uma entrada na lista. Para obter detalhes sobre a operação de cópia, selecione o link **Detalhes** (ícone de óculos) na coluna **Ações**. Para voltar ao modo de exibição Execuções de Pipeline, selecione **Execuções de Pipeline** na parte superior. Para atualizar a exibição, selecione **Atualizar**.

    ![Monitorar execuções de atividade](./media/tutorial-copy-data-portal/view-activity-runs.png)

1. Verifique se mais duas linhas são adicionadas à tabela **emp** no banco de dados SQL.

## <a name="trigger-the-pipeline-on-a-schedule"></a>Disparar o pipeline em um cronograma
Nesse cronograma, você criará um gatilho de agenda para o pipeline. O gatilho dispara o pipeline conforme a agenda especificada, como por hora ou por dia. Aqui você definirá o gatilho para executar a cada minuto até o datetime de término especificado.

1. Vá até a guia **Autor** à esquerda acima da guia Monitorar.

1. Vá até o pipeline, clique em **Adicionar gatilho** na barra de ferramentas e selecione **Novo/Editar**.

1. Na caixa de diálogo **Adicionar gatilhos**, selecione **+ novo** na área **Escolher gatilho**.

1. Na janela **Novo Gatilho**, siga estas etapas:

    a. Em **Nome**, digite **RunEveryMinute**.

    b. Em **Término**, selecione **Na Data**.

    c. Em **Terminar Em**, selecione a lista suspensa.

    d. Selecione a opção do **dia atual**. Por padrão, o dia final é definido para o próximo dia.

    e. Atualize a parte **Hora de término** para alguns minutos após o datetime atual. O gatilho só é ativado depois de você publicar as alterações. Se você a definir com apenas alguns minutos de diferença e não publicar até a hora especificada, nenhum gatilho será executado.

    f. Escolha **Aplicar**.

    g. Para a opção **Ativado**, selecione **Sim**.

    h. Selecione **Avançar**.

    ![Botão Ativado](./media/tutorial-copy-data-portal/trigger-activiated-next.png)

    > [!IMPORTANT]
    > Há um custo associado a cada execução de pipeline e, portanto, defina a data de término adequadamente.
1. Na página **Parâmetros da Execução de Gatilho**, revise o aviso e selecione **Concluir**. O pipeline neste exemplo não usa parâmetros.

1. Clique em **Publicar tudo** para publicar a alteração.

1. Vá para a guia **Monitor** à esquerda para ver as execuções de pipeline disparadas.

    ![Execuções de pipeline disparadas](./media/tutorial-copy-data-portal/triggered-pipeline-runs.png)   

1. Para alternar do modo de exibição **Execuções de Pipeline** para o modo de exibição **Execuções de Gatilho**, selecione **Execuções de Gatilho** na parte superior da janela.

1. As execuções de gatilho serão exibidas em uma lista.

1. Verifique se duas linhas por minuto (para cada execução de pipeline) são inseridas na tabela **emp** até a hora de término especificada.

## <a name="next-steps"></a>Próximas etapas
O pipeline neste exemplo copia dados de uma localização para outra em um Armazenamento de Blobs. Você aprendeu a:

> [!div class="checklist"]
> * Criar um data factory.
> * Criar um pipeline com uma atividade de cópia.
> * Executar teste do pipeline.
> * Disparar o pipeline manualmente.
> * Disparar o pipeline em um cronograma.
> * Monitore as execuções de pipeline e de atividade.


Avance para o tutorial a seguir para saber mais sobre como copiar dados do local para a nuvem:

> [!div class="nextstepaction"]
>[Copiar dados do local para a nuvem](tutorial-hybrid-copy-portal.md)
