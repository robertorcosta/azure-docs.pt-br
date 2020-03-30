---
title: Ferramenta de dados para copiar arquivos novos e atualizados incrementalmente
description: Crie uma fábrica de dados do Azure e, em seguida, use a ferramenta Copiar dados para carregar gradualmente novos arquivos com base no LastModifiedDate.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 3/18/2020
ms.openlocfilehash: 743f9dd8f7998178a75d716f4da22efee2b3bc79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131239"
---
# <a name="incrementally-copy-new-and-changed-files-based-on-lastmodifieddate-by-using-the-copy-data-tool"></a>Copie gradualmente arquivos novos e alterados com base no LastModifiedDate usando a ferramenta Copiar dados

Neste tutorial, você usará o portal Azure para criar uma fábrica de dados. Em seguida, você usará a ferramenta Copiar dados para criar um pipeline que copia gradualmente arquivos novos e alterados apenas, com base no armazenamento **LastModifiedDate** do armazenamento Azure Blob para o armazenamento Azure Blob.

Ao fazer isso, o ADF irá escanear todos os arquivos da loja de origem, aplicar o filtro de arquivo por seu LastModifiedDate e copiar o arquivo novo e atualizado somente desde a última vez na loja de destino.  Por favor, note que se você deixar o ADF digitalizar grandes quantidades de arquivos, mas apenas copiar alguns arquivos para o destino, você ainda esperaria que a longa duração devido à digitalização de arquivos seja demorada também.   

> [!NOTE]
> Se estiver se familiarizando com o Azure Data Factory, confira [Introdução ao Azure Data Factory](introduction.md).

Neste tutorial, você aprenderá a executar as seguintes tarefas:

> [!div class="checklist"]
> * Criar um data factory.
> * Usar a ferramenta Copy Data para criar um pipeline.
> * Monitore as execuções de pipeline e de atividade.

## <a name="prerequisites"></a>Pré-requisitos

* **Assinatura do Azure**: Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* **Conta de armazenamento Do Zure**: Use o armazenamento Blob como _fonte_ e _sink_ data store. Se você não tiver uma conta de armazenamento do Azure, confira as instruções em [Criar uma conta de armazenamento](../storage/common/storage-account-create.md).

### <a name="create-two-containers-in-blob-storage"></a>Crie dois contêineres no armazenamento Blob

Prepare seu armazenamento Blob para o tutorial realizando essas etapas.

1. Crie um recipiente chamado **source**. Você pode usar várias ferramentas para executar essa tarefa, como [o Azure Storage Explorer](https://storageexplorer.com/).

2. Crie um contêiner chamado **destino**.

## <a name="create-a-data-factory"></a>Criar uma data factory

1. No menu à esquerda, selecione **Criar um recurso** > **Data + Analytics** > **Data Factory**:

   ![Seleção de Data Factory no painel "Novo"](./media/doc-common-process/new-azure-data-factory-menu.png)

2. Na página **Novo data factory**, em **Nome**, insira **ADFTutorialDataFactory**.

   O nome do seu data factory deve ser _globalmente exclusivo_. Você deve ver a seguinte mensagem de erro:

   ![Mensagem de erro do novo data factory](./media/doc-common-process/name-not-available-error.png)

   Se você receber uma mensagem de erro sobre o valor do nome, insira um nome diferente para o data factory. Por exemplo, use o nome _**seunome**_**ADFTutorialDataFactory**. Para ver as regras de nomenclatura de artefatos do Data Factory, confira [Regras de nomenclatura do Data Factory](naming-rules.md).
3. Selecione a **assinatura** do Azure na qual você criará a nova fábrica de dados.
4. Em **Grupo de Recursos**, use uma das seguintes etapas:

    * Selecione **Usar o existente** e selecionar um grupo de recursos existente na lista de paradas.

    * Selecione **Criar novo** e digite o nome de um grupo de recursos. 
         
    Para saber mais sobre grupos de recursos, confira [Usar grupos de recursos para gerenciar recursos do Azure](../azure-resource-manager/management/overview.md).

5. Em **versão,** selecione **V2**.
6. Em **localização,** selecione o local para a fábrica de dados. Somente os locais com suporte são exibidos na lista suspensa. Os armazenamentos de dados (por exemplo, Azure Storage e SQL Database) e computas (por exemplo, Azure HDInsight) que sua fábrica de dados usa podem estar em outros locais e regiões.
8. Selecione **Criar**.
9. Depois de finalizada a criação, a home page do **Data Factory** é exibida.
10. Para abrir a interface de usuário (UI) da Fábrica de Dados do Azure em uma guia separada, selecione o **bloco De monitor de &** autor.

    ![Página inicial do data factory](./media/doc-common-process/data-factory-home-page.png)

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Use a ferramenta Copy Data para criar um pipeline

1. Na página **Vamos começar,** selecione o título **Copiar dados** para abrir a ferramenta Copiar dados.

   ![Bloco Ferramenta Copy Data](./media/doc-common-process/get-started-page.png)

2. Na página **Propriedades,** tome as seguintes etapas:

    a. Em **nome da tarefa,** digite **DeltaCopyFromBlobPipeline**.

    b. Em **Cadência de Tarefas** ou **Cronograma de Tarefas,** **selecione Executar regularmente no horário**.

    c. Em **Tipo de gatilho,** selecione **Janela de queda**.

    d. Em **Recorrência,** digite **15 minutos( s)**.

    e. Selecione **Avançar**.

    A interface do usuário do Data Factory cria um pipeline com o nome especificado da tarefa.

    ![Página Propriedades](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/copy-data-tool-properties-page.png)

3. Na página **Configurações do armazenamento de dados**, conclua as seguintes etapas:

    a. Selecione **+ Crie uma nova conexão,** para adicionar uma conexão.

    b. Selecione **Armazenamento de Blobs do Azure** na galeria e, em seguida, selecione **Continuar**.

    ![Página Armazenamento de dados de origem](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-blob.png)

    c. Na página **Novo Serviço Vinculado (Armazenamento Azure Blob),** selecione sua conta de armazenamento na lista de nomes da **conta de armazenamento.** Teste a conexão e, em seguida, selecione **Criar**.

    d. Selecione o serviço vinculado recém-criado e selecione **Next**.

   ![Página Armazenamento de dados de origem](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-linkedservice.png)

4. Na página **Escolha o arquivo de entrada ou a pasta**, execute as seguintes etapas:

    a. Navegue e selecione a pasta **de origem** e, em seguida, **selecione Escolher**.

    ![Escolha o arquivo ou a pasta de entrada](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-input-file-folder.png)

    b. Em **Comportamento de carregamento de**arquivos, selecione Carga **incremental: LastModifiedDate**.

    c. Verifique **a cópia binária** e selecione **Next**.

     ![Escolha o arquivo ou a pasta de entrada](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/check-binary-copy.png)

5. Na página do **armazenamento de dados Destino,** selecione o **AzureBlobStorage** que você criou. Esta é a mesma conta de armazenamento que o armazenamento de dados de origem. Em seguida, selecione **Next**.

6. Na página **Escolha o arquivo de saída ou a pasta**, execute as seguintes etapas:

    a. Navegue e selecione a pasta **de destino** e, em seguida, selecione **Escolher**.

    ![Escolha o arquivo ou a pasta de saída](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-output-file-folder.png)

    b. Selecione **Avançar**.

7. Na página **Configurações**, selecione **Avançar**.

8. Na página **Resumo,** revise as configurações e selecione **Next**.

    ![Página Resumo](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/summary-page.png)

9. Na **página Implantação**, selecione **Monitorar** para monitorar o pipeline (tarefa).

    ![Página Implantação](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/deployment-page.png)

10. Observe que a guia **Monitor** à esquerda é selecionada automaticamente. O aplicativo muda para a guia **Monitor.** Você vê o status do oleoduto. Selecione **Atualizar** para atualizar a lista. Clique no link em **PIPELINE NAME** para exibir detalhes da execução da atividade ou reexecutar o pipeline. 

    ![Atualizar lista e selecionar Exibir executa atividades](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs1.png)

11. Há apenas uma atividade (a atividade de cópia) no pipeline, então você vê apenas uma entrada. Para obter detalhes sobre a operação da cópia, selecione o link **Detalhes** (ícone de óculos) na coluna NOME de **ATIVIDADE.** Para obter detalhes sobre as propriedades, confira [Visão geral da atividade de cópia](copy-activity-overview.md). 

    ![A atividade de cópia está em pipeline](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs2.png)

    Como não há nenhum arquivo no contêiner **de origem** em sua conta de armazenamento Blob, você não verá nenhum arquivo copiado para o contêiner **de destino** em sua conta de armazenamento Blob.

    ![Nenhum arquivo no contêiner de origem ou contêiner de destino](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3.png)

12. Crie um arquivo de texto vazio e nomeie-o **file1.txt**. Carregue este arquivo de texto para o contêiner **de origem** em sua conta de armazenamento. É possível usar várias ferramentas para executar essas tarefas, como o [Azure Storage Explorer](https://storageexplorer.com/).

    ![Criar file1.txt e carregar para o contêiner de origem](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3-1.png)

13. Para voltar à exibição **Pipeline Runs,** selecione **Todas as executoras de pipeline**e aguarde que o mesmo pipeline seja acionado automaticamente.  

    ![Selecione Todas as corridas de pipeline](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs4.png)

14. Quando a segunda execução do pipeline for concluída, siga as mesmas etapas mencionadas acima para revisar os detalhes da execução da atividade.  

    Você verá que um arquivo (file1.txt) foi copiado do contêiner de **origem** para o contêiner de **destino** da sua conta de armazenamento Blob.

    ![File1.txt foi copiado do contêiner de origem para o contêiner de destino](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs6.png)

15. Crie outro arquivo de texto vazio e nomeie-o **file2.txt**. Carregue este arquivo de texto para o contêiner **de origem** em sua conta de armazenamento Blob.

16. Repita as etapas 13 e 14 para este segundo arquivo de texto. Você verá que apenas o novo arquivo (file2.txt) foi copiado do contêiner de **origem** para o contêiner de **destino** da sua conta de armazenamento na próxima execução do pipeline.  

    Você também pode verificar isso usando [o Azure Storage Explorer](https://storageexplorer.com/) para escanear os arquivos.

    ![Scaneie arquivos usando o Azure Storage Explorer](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs8.png)


## <a name="next-steps"></a>Próximas etapas
Avance para o tutorial a seguir para aprender sobre a transformação de dados usando um cluster Apache Spark no Azure:

> [!div class="nextstepaction"]
>[Transforme dados na nuvem usando um cluster Apache Spark](tutorial-transform-data-spark-portal.md)
