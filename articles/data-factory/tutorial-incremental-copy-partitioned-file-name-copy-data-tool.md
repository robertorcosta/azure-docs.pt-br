---
title: Copiar incrementalmente novos arquivos com base no nome do arquivo particionado por tempo
description: Crie uma fábrica de dados do Azure e, em seguida, use a ferramenta Copiar dados para carregar gradualmente novos arquivos apenas com base no nome do arquivo particionado por tempo.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 3/17/2020
ms.openlocfilehash: 50e82362f39da2d3f3ecfad2de1ed07b68f8a6a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501598"
---
# <a name="incrementally-copy-new-files-based-on-time-partitioned-file-name-by-using-the-copy-data-tool"></a>Copie gradualmente novos arquivos com base no nome do arquivo particionado por tempo usando a ferramenta Copiar dados

Neste tutorial, você pode usar o portal do Azure para criar um Data Factory. Em seguida, você usa a ferramenta Copiar dados para criar um pipeline que copia gradualmente novos arquivos com base no nome do arquivo particionado de tempo do armazenamento Azure Blob para o armazenamento Do Azure Blob.

> [!NOTE]
> Se estiver se familiarizando com o Azure Data Factory, confira [Introdução ao Azure Data Factory](introduction.md).

Neste tutorial, você executa as seguintes etapas:

> [!div class="checklist"]
> * Criar um data factory.
> * Usar a ferramenta Copy Data para criar um pipeline.
> * Monitore as execuções de pipeline e de atividade.

## <a name="prerequisites"></a>Pré-requisitos

* **Assinatura do Azure**: Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* **Conta de armazenamento Do Zure**: Use o armazenamento Blob como _fonte_ e _sink_ data store. Se você não tiver uma conta de armazenamento do Azure, confira as instruções em [Criar uma conta de armazenamento](../storage/common/storage-account-create.md).

### <a name="create-two-containers-in-blob-storage"></a>Crie dois contêineres no armazenamento Blob

Prepare seu armazenamento Blob para o tutorial realizando essas etapas.

1. Crie um recipiente chamado **source**.  Crie um caminho de pasta como **2020/03/17/03** em seu contêiner. Crie um arquivo de texto vazio e nomeie-o como **file1.txt**. Carregue o file1.txt para a fonte do caminho da **pasta/2020/03/17/03** em sua conta de armazenamento.  É possível usar várias ferramentas para executar essas tarefas, como o [Azure Storage Explorer](https://storageexplorer.com/).

    ![upload de arquivos](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/upload-file.png)

    > [!NOTE]
    > Por favor, ajuste o nome da pasta com o tempo UTC.  Por exemplo, se a hora atual do UTC for 3:38 AM em 17 de março de 2020, você poderá criar o caminho da pasta como **fonte/2020/03/17/03/** pela regra de **origem/{Year}/{Month}/{Day}/{Hour}/**.

2. Crie um contêiner chamado **destino**. É possível usar várias ferramentas para executar essas tarefas, como o [Azure Storage Explorer](https://storageexplorer.com/).

## <a name="create-a-data-factory"></a>Criar uma data factory

1. No menu à esquerda, selecione **Criar um recurso** > **Data + Analytics** > **Data Factory**:

   ![Seleção de Data Factory no painel "Novo"](./media/doc-common-process/new-azure-data-factory-menu.png)

2. Na página **Novo data factory**, em **Nome**, insira **ADFTutorialDataFactory**.

    O nome do seu data factory deve ser _globalmente exclusivo_. Você deve ver a seguinte mensagem de erro:

   ![Mensagem de erro do novo data factory](./media/doc-common-process/name-not-available-error.png)

   Se você receber uma mensagem de erro sobre o valor do nome, insira um nome diferente para o data factory. Por exemplo, use o nome _**seunome**_**ADFTutorialDataFactory**. Para ver as regras de nomenclatura de artefatos do Data Factory, confira [Regras de nomenclatura do Data Factory](naming-rules.md).
3. Selecione a **assinatura** do Azure na qual deseja criar o novo data factory.
4. Em **Grupo de Recursos**, use uma das seguintes etapas:

    a. Selecione **Usar existente**e selecione um grupo de recursos existente na lista suspensa.

    b. Selecione **Criar novo**e insira o nome de um grupo de recursos. 
         
    Para saber mais sobre grupos de recursos, confira [Usar grupos de recursos para gerenciar recursos do Azure](../azure-resource-manager/management/overview.md).

5. Em **versão**, selecione **V2** para a versão.
6. Em **localização,** selecione o local para a fábrica de dados. Somente os locais com suporte são exibidos na lista suspensa. Os armazenamentos de dados (por exemplo, Armazenamento do Azure e Banco de Dados SQL) e os serviços de computação (por exemplo, Azure HDInsight) usados pelo seu data factory podem estar em outros locais e regiões.
7. Selecione **Criar**.
8. Depois de finalizada a criação, a home page do **Data Factory** é exibida.
9. Para iniciar a interface do usuário do Azure Data Factory em uma guia separada, selecione o bloco **Criar e Monitorar**.

    ![Página inicial do data factory](./media/doc-common-process/data-factory-home-page.png)


## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Use a ferramenta Copy Data para criar um pipeline

1. Na página **Vamos começar,** selecione o título **Copiar dados** para iniciar a ferramenta Copiar dados.

   ![Bloco Ferramenta Copy Data](./media/doc-common-process/get-started-page.png)

2. Na página **Propriedades,** tome as seguintes etapas:

    a. Em **nome da tarefa,** digite **DeltaCopyFromBlobPipeline**.

    b. Em **Cadência de Tarefas ou Cronograma de Tarefas,** **selecione Executar regularmente no horário**.

    c. No **tipo Gatilho,** selecione **Janela de queda**.

    d. Em **Recorrência,** digite **1 Hora(s)**.

    e. Selecione **Avançar**.

    A interface do usuário do Data Factory cria um pipeline com o nome especificado da tarefa.

    ![Página Propriedades](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/copy-data-tool-properties-page.png)
3. Na página **Configurações do armazenamento de dados**, conclua as seguintes etapas:

    a. Clique **em + Crie uma nova conexão** para adicionar uma conexão
    
    b. Selecione Armazenamento de Blobs do Azure na galeria e, em seguida, selecione Continuar.
    
    c. Na página **Novo Serviço Vinculado (Armazenamento Azure Blob),** digite um nome para o serviço vinculado. Selecione sua assinatura do Azure e selecione sua conta de armazenamento na lista de nomes da **conta armazenamento.** Teste a conexão e, em seguida, selecione **Criar**.

    ![Página Armazenamento de dados de origem](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-linkedservice.png)

    d. Selecione o serviço vinculado recém-criado na página do **armazenamento de dados Origem** e clique em **"A seguir".**

4. Na página **Escolher o arquivo ou a pasta de entrada**, execute as etapas a seguir:

    a. Navegue e selecione o recipiente **de origem** e selecione **Escolher**.

    ![Escolha o arquivo ou a pasta de entrada](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-input-file-folder.png)

    b. Em **Comportamento de carregamento de**arquivos, selecione Carga **incremental: nomes de pasta/arquivo com partição de tempo**.

    c. Escreva o caminho dinâmico da pasta como **fonte/{year}/{month}/{day}/{hour}/** e altere o formato conforme mostrado na captura de tela a seguir. Verifique **a cópia binária** e clique **em Next**.

    ![Escolha o arquivo ou a pasta de entrada](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/check-binary-copy.png)     

5. Na página do **armazenamento de dados Destino,** selecione o **AzureBlobStorage**, que é a mesma conta de armazenamento de armazenamento de dados e clique em **Next**.

    ![Página Armazenamento de dados de destino](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/destination-data-store-page-select-linkedservice.png)
6. Na **página Escolher o arquivo de saída ou pasta,** faça as seguintes etapas:

    a. Navegue e selecione a pasta **de destino** e clique **em Escolher**.

    b. Escreva o caminho dinâmico da pasta como **destino/{year}/{month}/{day}/{hour}/** e altere o formato da seguinte forma:

    ![Escolha o arquivo ou a pasta de saída](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/output-file-name.png)

    c. Clique em **Avançar**.

    ![Escolha o arquivo ou a pasta de saída](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/click-next-after-output-folder.png)
7. Na página **Configurações**, selecione **Avançar**.

8. Na página **Resumo**, analise as configurações e selecione **Avançar**.

    ![Página Resumo](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/summary-page.png)

9. Na **página Implantação**, selecione **Monitorar** para monitorar o pipeline (tarefa).
    ![Página Implantação](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/deployment-page.png)

10. Observe que a guia **Monitor** à esquerda é selecionada automaticamente.  Você precisa esperar pelo pipeline funcionar quando ele for acionado automaticamente (cerca de uma hora depois de uma hora). Quando for executado, clique no link do nome do pipeline **DeltaCopyFromBlobPipeline** para visualizar os detalhes da execução da atividade ou executar o pipeline. Selecione **Atualizar** para atualizar a lista.

    ![Monitorar execuções de pipeline](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs1.png)
11. Há apenas uma atividade (atividade de cópia) no pipeline. Assim, você vê apenas uma entrada. Ajuste a largura da coluna das colunas **de origem** e **destino** (se necessário) para exibir mais detalhes, você pode ver o arquivo de origem (file1.txt) foi copiado de *origem/2020/03/17/03/* para *destino/2020/03/17/03/* com o mesmo nome do arquivo. 

    ![Monitorar execuções de pipeline](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs2.png)

    Você também pode verificar o mesmo usandohttps://storageexplorer.com/) o Azure Storage Explorer (para escanear os arquivos.

    ![Monitorar execuções de pipeline](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs3.png)

12. Crie outro arquivo de texto vazio com o novo nome como **file2.txt**. Carregue o arquivo2.txt para a fonte do caminho da **pasta/2020/03/17/04** em sua conta de armazenamento. É possível usar várias ferramentas para executar essas tarefas, como o [Azure Storage Explorer](https://storageexplorer.com/).

    > [!NOTE]
    > Você pode estar ciente de que um novo caminho de pasta é necessário para ser criado. Por favor, ajuste o nome da pasta com o tempo UTC.  Por exemplo, se a hora atual da UTC for 4:20 AM em 17 de março de 2020, você pode criar o caminho da pasta como **fonte/2020/03/17/04/** pela regra de **{Year}/{Month}/{Day}/{Hour}/**.

13. Para voltar à exibição **Pipeline Runs,** selecione **Todas as corridas de Pipeline**e aguarde que o mesmo pipeline seja acionado automaticamente após mais uma hora.  

    ![Monitorar execuções de pipeline](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs5.png)

14. Selecione o novo link **DeltaCopyFromBlobPipeline** para a segunda execução do pipeline quando ele chegar e faça o mesmo para revisar detalhes. Você verá que o arquivo de origem (file2.txt) foi copiado da **fonte/2020/03/17/04/** para **o destino/2020/03/17/04/** com o mesmo nome de arquivo. Você também pode verificar o mesmo usandohttps://storageexplorer.com/) o Azure Storage Explorer (para escanear os arquivos no contêiner **de destino.**


## <a name="next-steps"></a>Próximas etapas
Avance para o tutorial a seguir para saber mais sobre como transformar dados usando um cluster Spark no Azure:

> [!div class="nextstepaction"]
>[Transformar dados usando o cluster Spark na nuvem](tutorial-transform-data-spark-portal.md)
