---
title: Data tool to copy new and updated files incrementally
description: Create an Azure data factory and then use the Copy Data tool to incrementally load new files based on LastModifiedDate.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 1/24/2019
ms.openlocfilehash: 5c20196bd243d025d58f7cc08e015e1e0038e178
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74217796"
---
# <a name="incrementally-copy-new-and-changed-files-based-on-lastmodifieddate-by-using-the-copy-data-tool"></a>Incrementally copy new and changed files based on LastModifiedDate by using the Copy Data tool

In this tutorial, you'll use the Azure portal to create a data factory. Then, you'll use the Copy Data tool to create a pipeline that incrementally copies new and changed files only, based on their **LastModifiedDate** from Azure Blob storage to Azure Blob storage.

By doing so, ADF will scan all the files from the source store, apply the file filter by their LastModifiedDate, and copy the new and updated file only since last time to the destination store.  Please note that if you let ADF scan huge amounts of files but only copy a few files to destination, you would still expect the long duration due to file scanning is time consuming as well.   

> [!NOTE]
> Se estiver se familiarizando com o Azure Data Factory, confira [Introdução ao Azure Data Factory](introduction.md).

In this tutorial, you will perform the following tasks:

> [!div class="checklist"]
> * Criar um data factory.
> * Usar a ferramenta Copy Data para criar um pipeline.
> * Monitore as execuções de pipeline e de atividade.

## <a name="prerequisites"></a>Pré-requisitos

* **Assinatura do Azure**: caso não tenha uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* **Azure storage account**: Use Blob storage as the _source_ and _sink_ data store. Se você não tiver uma conta de armazenamento do Azure, confira as instruções em [Criar uma conta de armazenamento](../storage/common/storage-quickstart-create-account.md).

### <a name="create-two-containers-in-blob-storage"></a>Create two containers in Blob storage

Prepare your Blob storage for the tutorial by performing these steps.

1. Create a container named **source**. You can use various tools to perform this task, such as [Azure Storage Explorer](https://storageexplorer.com/).

2. Create a container named **destination**. 

## <a name="create-a-data-factory"></a>Criar uma data factory

1. No menu à esquerda, selecione **Criar um recurso** > **Dados + Análise** > **Data Factory**: 
   
   ![Seleção de Data Factory no painel "Novo"](./media/doc-common-process/new-azure-data-factory-menu.png)

2. Na página **Novo data factory**, em **Nome**, insira **ADFTutorialDataFactory**. 
 
   O nome do seu data factory deve ser _globalmente exclusivo_. Você deve ver a seguinte mensagem de erro:
   
   ![Mensagem de erro do novo data factory](./media/doc-common-process/name-not-available-error.png)

   Se você receber uma mensagem de erro sobre o valor do nome, insira um nome diferente para o data factory. Por exemplo, use o nome _**seunome**_ **ADFTutorialDataFactory**. Para ver as regras de nomenclatura de artefatos do Data Factory, confira [Regras de nomenclatura do Data Factory](naming-rules.md).
3. Select the Azure **subscription** in which you'll create the new data factory. 
4. Em **Grupo de Recursos**, use uma das seguintes etapas:
     
    * Selecione **Usar existente**e selecione um grupo de recursos existente na lista suspensa.

    * Selecione **Criar novo**e insira o nome de um grupo de recursos. 
         
    Para saber mais sobre grupos de recursos, confira [Usar grupos de recursos para gerenciar recursos do Azure](../azure-resource-manager/resource-group-overview.md).

5. Under **version**, select **V2**.
6. Em **local**, selecione o local para o data factory. Somente os locais com suporte são exibidos na lista suspensa. The data stores (for example, Azure Storage and SQL Database) and computes (for example, Azure HDInsight) that your data factory uses can be in other locations and regions.
7. Selecione **Fixar no painel**. 
8. Clique em **Criar**.
9. On the dashboard, refer to the **Deploying Data Factory** tile to see the process status.

    ![Deploying Data Factory Tile](media/tutorial-copy-data-tool/deploying-data-factory.png)
10. Depois de finalizada a criação, a home page do **Data Factory** é exibida.
   
    ![Página inicial do data factory](./media/doc-common-process/data-factory-home-page.png)
11. To open the Azure Data Factory user interface (UI) on a separate tab, select the **Author & Monitor** tile. 

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Use a ferramenta Copy Data para criar um pipeline

1. On the **Let's get started** page, select the **Copy Data** title to open the Copy Data tool. 

   ![Bloco Ferramenta Copy Data](./media/doc-common-process/get-started-page.png)
   
2. On the **Properties** page, take the following steps:

    a. Under **Task name**, enter **DeltaCopyFromBlobPipeline**.

    b. Under **Task cadence** or **Task schedule**, select **Run regularly on schedule**.

    c. Under **Trigger Type**, select **Tumbling Window**.
    
    d. Under **Recurrence**, enter **15 Minute(s)** . 
    
    e. Selecione **Avançar**. 
    
    A interface do usuário do Data Factory cria um pipeline com o nome especificado da tarefa. 

    ![Página Propriedades](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/copy-data-tool-properties-page.png)
    
3. Na página **Configurações do armazenamento de dados**, conclua as seguintes etapas:

    a. Select  **+ Create new connection**, to add a connection.
    
    ![Página Armazenamento de dados de origem](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page.png)

    b. Selecione **Armazenamento de Blobs do Azure** na galeria e, em seguida, selecione **Continuar**.
    
    ![Página Armazenamento de dados de origem](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-blob.png)

    c. On the **New Linked Service** page, select your storage account from the **Storage account name** list and then select **Finish**.
    
    ![Página Armazenamento de dados de origem](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-linkedservice.png)
    
    d. Select the newly created linked service and then select **Next**. 
    
   ![Página Armazenamento de dados de origem](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-linkedservice.png)

4. Na página **Escolha o arquivo de entrada ou a pasta**, execute as seguintes etapas:
    
    a. Browse and select the **source** folder, and then select **Choose**.
    
    ![Escolha o arquivo ou a pasta de entrada](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-input-file-folder.png)
    
    b. Under **File loading behavior**, select **Incremental load: LastModifiedDate**.
    
    ![Escolha o arquivo ou a pasta de entrada](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-loading-behavior.png)
    
    c. Check **Binary copy** and select **Next**.
    
     ![Escolha o arquivo ou a pasta de entrada](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/check-binary-copy.png)
     
5. On the **Destination data store** page, select **AzureBlobStorage**. This is the same storage account as the source data store. Em seguida, selecione **Avançar**.

    ![Página Armazenamento de dados de destino](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/destination-data-store-page-select-linkedservice.png)
    
6. Na página **Escolha o arquivo de saída ou a pasta**, execute as seguintes etapas:
    
    a. Browse and select the **destination** folder, and then select **Choose**.
    
    ![Escolha o arquivo ou a pasta de saída](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-output-file-folder.png)
    
    b. Selecione **Avançar**.
    
     ![Escolha o arquivo ou a pasta de saída](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/click-next-after-output-folder.png)
    
7. Na página **Configurações**, selecione **Avançar**. 

    ![Página Configurações](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/settings-page.png)
    
8. On the **Summary** page, review the settings and then select **Next**.

    ![Página Resumo](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/summary-page.png)
    
9. Na **página Implantação**, selecione **Monitorar** para monitorar o pipeline (tarefa).

    ![Página Implantação](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/deployment-page.png)
    
10. Observe que a guia **Monitor** à esquerda é selecionada automaticamente. A coluna **Ações** inclui links para exibir detalhes da execução da atividade e executar o pipeline novamente. Select **Refresh** to refresh the list, and select the **View Activity Runs** link in the **Actions** column. 

    ![Refresh list and select View Activity Runs](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs1.png)

11. There's only one activity (the copy activity) in the pipeline, so you see only one entry. Para obter detalhes sobre a operação de cópia, selecione o link **Detalhes** (ícone de óculos) na coluna **Ações**. 

    ![Copy activity is in pipeline](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs2.png)
    
    Because there is no file in the **source** container in your Blob storage account, you will not see any file copied to the **destination** container in your Blob storage account.
    
    ![No file in source container or destination container](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3.png)
    
12. Create an empty text file and name it **file1.txt**. Upload this text file to the **source** container in your storage account. É possível usar várias ferramentas para executar essas tarefas, como o [Azure Storage Explorer](https://storageexplorer.com/).   

    ![Create file1.txt and upload to source container](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3-1.png)
    
13. To go back to the **Pipeline Runs** view, select **All Pipeline Runs**, and wait for the same pipeline to be triggered again automatically.  

    ![Select All Pipeline Runs](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs4.png)

14. Select **View Activity Run** for the second pipeline run when you see it. Then review the details in the same way you did for the first pipeline run.  

    ![Select View Activity Run and review details](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs5.png)

    You will that see one file (file1.txt) has been copied from the **source** container to the **destination** container of your Blob storage account.
    
    ![File1.txt has been copied from source container to destination container](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs6.png)
    
15. Create another empty text file and name it **file2.txt**. Upload this text file to the **source** container in your Blob storage account.   
    
16. Repeat steps 13 and 14 for this second text file. You will see that only the new file (file2.txt) has been copied from the **source** container to the **destination** container of your storage account in the next pipeline run.  
    
    ![File2.txt has been copied from source container to destination container](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs7.png)

    You can also verify this by using [Azure Storage Explorer](https://storageexplorer.com/) to scan the files.
    
    ![Scan files using Azure Storage Explorer](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs8.png)

    
## <a name="next-steps"></a>Próximos passos
Advance to the following tutorial to learn about transforming data by using an Apache Spark cluster on Azure:

> [!div class="nextstepaction"]
>[Transform data in the cloud by using an Apache Spark cluster](tutorial-transform-data-spark-portal.md)