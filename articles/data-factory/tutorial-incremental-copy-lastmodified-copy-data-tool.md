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
ms.openlocfilehash: 3098ca0d3d5e41c298d3058ffa84fcf129648281
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399478"
---
# <a name="incrementally-copy-new-and-changed-files-based-on-lastmodifieddate-by-using-the-copy-data-tool"></a>Copie gradualmente arquivos novos e alterados com base no LastModifiedDate usando a ferramenta Copiar dados

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Neste tutorial, você usará o portal Azure para criar uma fábrica de dados. Em seguida, você usará a ferramenta Copiar dados para criar um pipeline que copia gradualmente arquivos novos e alterados apenas, do armazenamento Azure Blob para o armazenamento Do Azure Blob. Ele `LastModifiedDate` usa para determinar quais arquivos copiar.

Depois de concluir as etapas aqui, a Fábrica de Dados do Azure `LastModifiedDate`irá escanear todos os arquivos na loja de origem, aplicar o filtro de arquivos por , e copiar para a loja de destino apenas arquivos que são novos ou foram atualizados desde a última vez. Observe que se a Fábrica de Dados digitaliza um grande número de arquivos, você ainda deve esperar longas durações. A varredura de arquivos é demorada, mesmo quando a quantidade de dados copiados é reduzida.

> [!NOTE]
> Se estiver se familiarizando com o Data Factory, confira a [Introdução ao Azure Data Factory](introduction.md).

Neste tutorial, você concluirá estas tarefas:

> [!div class="checklist"]
> * Criar um data factory.
> * Usar a ferramenta Copy Data para criar um pipeline.
> * Monitore as execuções de pipeline e de atividade.

## <a name="prerequisites"></a>Pré-requisitos

* **Assinatura do Azure**: Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* **Conta de armazenamento do Azure**: Use o armazenamento Blob para os armazenamentos de dados de origem e afundamento. Se você não tiver uma conta de armazenamento Azure, siga as instruções em [Criar uma conta de armazenamento](../storage/common/storage-account-create.md).

## <a name="create-two-containers-in-blob-storage"></a>Crie dois contêineres no armazenamento Blob

Prepare seu armazenamento Blob para o tutorial completando estas etapas:

1. Crie um recipiente chamado **source**. Você pode usar várias ferramentas para executar essa tarefa, como [o Azure Storage Explorer](https://storageexplorer.com/).

2. Crie um contêiner chamado **destino**.

## <a name="create-a-data-factory"></a>Criar uma data factory

1. No painel esquerdo, selecione **Criar um recurso**. Selecionar **fábrica de** > **dados**de análise :

   ![Selecionar fábrica de dados](./media/doc-common-process/new-azure-data-factory-menu.png)

2. Na página **Novo data factory**, em **Nome**, insira **ADFTutorialDataFactory**.

   O nome do seu data factory deve ser globalmente exclusivo. Você pode receber esta mensagem de erro:

   ![Mensagem de erro nome não disponível](./media/doc-common-process/name-not-available-error.png)

   Se você receber uma mensagem de erro sobre o valor do nome, insira um nome diferente para o data factory. Por exemplo, use o nome _**seunome**_**ADFTutorialDataFactory**. Para ver as regras de nomenclatura de artefatos do Data Factory, confira [Regras de nomenclatura do Data Factory](naming-rules.md).
3. Em **Assinatura,** selecione a assinatura do Azure na qual você criará a nova fábrica de dados.
4. Em **Resource Group,** tome uma dessas etapas:

    * Selecione **Usar existente** e, em seguida, selecione um grupo de recursos existente na lista.

    * Selecione **Criar novo** e, em seguida, digite um nome para o grupo de recursos.
         
    Para saber mais sobre grupos de recursos, confira [Usar grupos de recursos para gerenciar recursos do Azure](../azure-resource-manager/management/overview.md).

5. Em **Versão**, selecione **V2**.
6. Em **Local**, selecione o local para o data factory. Apenas locais suportados aparecem na lista. Os armazenamentos de dados (por exemplo, Azure Storage e Azure SQL Database) e computa (por exemplo, o Azure HDInsight) que sua fábrica de dados usa pode estar em outros locais e regiões.
8. Selecione **Criar**.
9. Depois que a fábrica de dados é criada, a página inicial da fábrica de dados é exibida.
10. Para abrir a interface de usuário (UI) da Fábrica de Dados do Azure em uma guia separada, selecione o **bloco de & Monitor do Autor:**

    ![Página inicial do data factory](./media/doc-common-process/data-factory-home-page.png)

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Use a ferramenta Copy Data para criar um pipeline

1. Na página **Vamos começar,** selecione o bloco **Copiar dados** para abrir a ferramenta Copiar dados:

   ![Copiar blocode dados](./media/doc-common-process/get-started-page.png)

2. Na página **Propriedades,** tome as seguintes etapas:

    a. Em **nome da tarefa,** digite **DeltaCopyFromBlobPipeline**.

    b. Em **Cadência de Tarefas ou Cronograma de Tarefas,** **selecione Executar regularmente no horário**.

    c. No **tipo Gatilho,** selecione **Janela de queda**.

    d. Em **Recorrência,** digite **15 minutos( s)**.

    e. Selecione **Avançar**.

    A Fábrica de Dados cria um pipeline com o nome da tarefa especificado.

    ![Copiar página de propriedades de dados](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/copy-data-tool-properties-page.png)

3. Na página do **armazenamento de dados Origem,** complete estas etapas:

    a. Selecione **Criar nova conexão** para adicionar uma conexão.

    b. Selecione **O Armazenamento do Azure Blob** na galeria e selecione **Continuar**:

    ![Selecione o armazenamento do blog do Azure](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-blob.png)

    c. Na página **Novo Serviço Vinculado (Armazenamento Azure Blob),** selecione sua conta de armazenamento na lista de nomes da **conta de armazenamento.** Teste a conexão e selecione **Criar**.

    d. Selecione o novo serviço vinculado e selecione **Next**:

   ![Selecione o novo serviço vinculado](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-linkedservice.png)

4. Na página **Escolha o arquivo de entrada ou a pasta**, execute as seguintes etapas:

    a. Procure e selecione a pasta **de origem** e, em seguida, selecione **Escolher**.

    ![Escolha o arquivo ou a pasta de entrada](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-input-file-folder.png)

    b. Em **Comportamento de carregamento de**arquivos, selecione Carga **incremental: LastModifiedDate**.

    c. Selecione **cópia binária** e selecione **Next**:

     ![Escolha o arquivo de entrada ou a página da pasta](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/check-binary-copy.png)

5. Na página do **armazenamento de dados Destino,** selecione o serviço **AzureBlobStorage** que você criou. Esta é a mesma conta de armazenamento que o armazenamento de dados de origem. Em seguida, selecione **Avançar**.

6. Na página **Escolha o arquivo de saída ou a pasta**, execute as seguintes etapas:

    a. Procure e selecione a pasta **de destino** e selecione **Escolher**:

    ![Escolha o arquivo de saída ou a página da pasta](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-output-file-folder.png)

    b. Selecione **Avançar**.

7. Na página **Configurações**, selecione **Avançar**.

8. Na página **Resumo,** revise as configurações e selecione **Next**.

    ![Página Resumo](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/summary-page.png)

9. Na **página Implantação**, selecione **Monitorar** para monitorar o pipeline (tarefa).

    ![Página Implantação](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/deployment-page.png)

10. Observe que a guia **Monitor** à esquerda é selecionada automaticamente. O aplicativo muda para a guia **Monitor.** Você vê o status do oleoduto. Selecione **Atualizar** para atualizar a lista. Selecione o link em **PIPELINE NAME** para exibir detalhes da execução da atividade ou para executar o pipeline novamente.

    ![Atualize a lista e visualize os detalhes da execução da atividade](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs1.png)

11. Há apenas uma atividade (a atividade de cópia) no pipeline, então você vê apenas uma entrada. Para obter detalhes sobre a operação da cópia, selecione o link **Detalhes** (o ícone dos óculos) na coluna NOME de **ATIVIDADE.** Para obter detalhes sobre as propriedades, consulte [Visão geral da atividade de copiar](copy-activity-overview.md).

    ![Copiar atividade no pipeline](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs2.png)

    Como não há arquivos no contêiner de origem em sua conta de armazenamento Blob, você não verá nenhum arquivo copiado para o contêiner de destino na conta:

    ![Sem arquivos no contêiner de origem ou no contêiner de destino](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3.png)

12. Crie um arquivo de texto vazio e nomeie-o **file1.txt**. Carregue este arquivo de texto para o contêiner de origem em sua conta de armazenamento. Você pode usar várias ferramentas para executar essas tarefas, como [o Azure Storage Explorer](https://storageexplorer.com/).

    ![Crie file1.txt e carregue-o no recipiente de origem](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3-1.png)

13. Para voltar à exibição **de executões** do Pipeline, selecione **Todas as executoras do pipeline**e aguarde que o mesmo pipeline seja acionado automaticamente novamente.  

    ![Selecione Todas as executões de pipeline](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs4.png)

14. Quando a segunda execução do pipeline for concluída, siga as mesmas etapas mencionadas anteriormente para revisar os detalhes da execução da atividade.  

    Você verá que um arquivo (file1.txt) foi copiado do contêiner de origem para o contêiner de destino da sua conta de armazenamento Blob:

    ![file1.txt foi copiado do contêiner de origem para o contêiner de destino](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs6.png)

15. Crie outro arquivo de texto vazio e nomeie-o **file2.txt**. Carregue este arquivo de texto para o contêiner de origem em sua conta de armazenamento Blob.

16. Repita as etapas 13 e 14 para o segundo arquivo de texto. Você verá que apenas o novo arquivo (file2.txt) foi copiado do contêiner de origem para o contêiner de destino da sua conta de armazenamento durante esta execução do pipeline.  

    Você também pode verificar se apenas um arquivo foi copiado usando [o Azure Storage Explorer](https://storageexplorer.com/) para escanear os arquivos:

    ![Escaneie arquivos usando o Azure Storage Explorer](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs8.png)


## <a name="next-steps"></a>Próximas etapas
Acesse o tutorial a seguir para saber como transformar dados usando um cluster Apache Spark no Azure:

> [!div class="nextstepaction"]
>[Transforme dados na nuvem usando um cluster Apache Spark](tutorial-transform-data-spark-portal.md)
