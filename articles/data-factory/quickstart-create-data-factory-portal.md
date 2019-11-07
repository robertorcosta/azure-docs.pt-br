---
title: Criar um Azure Data Factory usando a interface do usuário do Azure Data Factory | Microsoft Docs
description: Crie um data factory com um pipeline que copia dados de uma localização no Armazenamento de Blobs do Azure para outro local.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: quickstart
ms.date: 06/20/2018
ms.author: jingwang
ms.openlocfilehash: 3dee054f6c382a00a55e4acf07e2a188c5cc6a07
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490664"
---
# <a name="quickstart-create-a-data-factory-by-using-the-azure-data-factory-ui"></a>Início Rápido: Criar um data factory usando a interface do usuário do Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está sendo usada:"]
> * [Versão 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Versão atual](quickstart-create-data-factory-portal.md)

Este guia de início rápido descreve como usar a interface do usuário do Azure Data Factory para criar e monitorar um data factory. O pipeline que você criar nesse data factory *copia* dados de uma pasta para outra no Armazenamento de Blobs do Azure. Para ver um tutorial sobre como *transformar* dados usando o Azure Data Factory, confira [Tutorial: Transformar dados usando o Spark](tutorial-transform-data-spark-portal.md).

> [!NOTE]
> Se você for novo no Azure Data Factory, confira a [Introdução ao Azure Data Factory](data-factory-introduction.md) antes de fazer este guia de início rápido. 

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

### <a name="video"></a>Vídeo 
Assistir a este vídeo o ajuda a entender a interface do usuário do Data Factory: 
>[!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Visually-build-pipelines-for-Azure-Data-Factory-v2/Player]

## <a name="create-a-data-factory"></a>Criar uma data factory

1. Iniciar o navegador da Web **Microsoft Edge** ou **Google Chrome**. Atualmente, a interface do usuário do Data Factory tem suporte apenas nos navegadores da Web Microsoft Edge e Google Chrome.
1. Vá para o [Portal do Azure](https://portal.azure.com). 
1. No menu do portal do Azure, selecione **Criar um recurso**.
   
   ![Selecione Criar um recurso no menu do portal do Azure](./media/doc-common-process/create-a-resource.png)
1. Selecione **Análise** e, em seguida, **Data Factory**. 
   
   ![Seleção de Data Factory no painel "Novo"](./media/doc-common-process/new-azure-data-factory-menu.png)
1. Na página **Novo data factory**, insira **ADFTutorialDataFactory** no campo **Nome**. 
 
   O nome do Azure Data Factory deve ser *globalmente exclusivo*. Se você visualizar o seguinte erro, altere o nome de data factory (por exemplo, **&lt;yourname&gt;ADFTutorialDataFactory**) e tente criar novamente. Para ver as regras de nomenclatura para artefatos do Data Factory consulte o artigo [Data Factory - regras de nomenclatura](naming-rules.md).
  
   ![Erro quando um nome não está disponível](./media/doc-common-process/name-not-available-error.png)
1. Para **Assinatura**, selecione a assinatura do Azure na qual você deseja criar o data factory. 
1. Para o **Grupo de Recursos**, use uma das seguintes etapas:
     
   - Selecione **Usar existente** e selecione um grupo de recursos existente na lista. 
   - Selecione **Criar novo**e insira o nome de um grupo de recursos.   
         
   Para saber mais sobre grupos de recursos, consulte [Usando grupos de recursos para gerenciar recursos do Azure](../azure-resource-manager/resource-group-overview.md).  
1. Para **Versão**, selecione **V2**.
1. Em **Local**, selecione uma localização para o data factory.

   A lista mostra somente os locais aos quais o Data Factory dá suporte e em que os metadados do Azure Data Factory serão armazenados. Os armazenamentos de dados associados (como o Armazenamento do Azure e o Banco de Dados SQL do Azure) e os serviços de computação (como o Azure HDInsight) usados pelo Data Factory podem ser executados em outras regiões.

1. Selecione **Criar**.

1. Após a criação, a página do **Data Factory** será exibida. Clique no bloco **Criar e Monitorar** para iniciar o aplicativo de interface do usuário (IU) do Azure Data Factory em uma guia separada.
   
   ![Página inicial do Data Factory, com o bloco “Criar e Monitorar"](./media/doc-common-process/data-factory-home-page.png)
1. Na página **Introdução**, acesse a guia **Autor** no painel esquerdo. 

    ![Página “Introdução”](./media/quickstart-create-data-factory-portal/get-started-page.png)

## <a name="create-a-linked-service"></a>Criar um serviço vinculado
Nesta etapa, você criará um serviço vinculado para vincular sua Conta de Armazenamento do Azure ao Data Factory. O serviço vinculado tem as informações de conexão que o serviço do Data Factory usa no runtime para se conectar a ele.

1. Selecione **Conexões** e, em seguida, selecione o botão **Novo** na barra de ferramentas (o botão **Conexões** fica localizado na parte inferior da coluna esquerda, sob **Recursos de Fábrica**). 

1. Na página **Novo Serviço Vinculado**, selecione **Armazenamento de Blobs do Azure** e selecione **Continuar**. 

   ![Selecionar o bloco "Armazenamento de Blobs do Azure"](./media/quickstart-create-data-factory-portal/select-azure-blob-linked-service.png)
1. Na página Novo Serviço Vinculado (Armazenamento de Blobs do Azure), conclua as seguintes etapas: 

   a. Para o campo **Nome**, insira **AzureStorageLinkedService**.

   b. Insira o nome da sua Conta de Armazenamento do Azure em **Nome da conta de armazenamento**.

   c. Selecione **Testar conectividade** para confirmar se o serviço do Data Factory pode se conectar à conta de armazenamento. 

   d. Para salvar o serviço vinculado, selecione **Concluir**. 

## <a name="create-datasets"></a>Criar conjuntos de dados
Neste procedimento, você criará dois conjuntos de dados: **InputDataset** e **OutputDataset**. Esses conjuntos de dados são do tipo **AzureBlob**. Eles se referem ao Serviço vinculado do Armazenamento do Azure que você criou na seção anterior. 

O conjunto de dados de entrada representa os dados de origem na pasta de entrada. Na definição de conjunto de dados de entrada, especifique o contêiner de blob (**adftutorial**), a pasta (**entrada**) e o arquivo (**emp.txt**) que contém os dados de origem. 

Esse conjunto de dados de saída representa os dados que são copiados para o destino. Na definição de conjunto de dados de saída, especifique o contêiner de blob (**adftutorial**), a pasta (**saída**) e o arquivo para o qual os dados são copiados. Cada execução de um pipeline tem uma ID exclusiva associada a ele. Você pode acessar essa ID, usando a variável do sistema **RunId**. O nome do arquivo de saída é avaliado dinamicamente com base na ID de execução do pipeline.   

Nas configurações de serviço vinculado, você especificou a conta de armazenamento do Azure que contém os dados de origem. Nas configurações do conjunto de dados de origem, especifique onde exatamente os dados de origem residem (contêiner de blob, pasta e arquivo). Nas configurações de conjunto de dados do coletor, especifique para onde os dados são copiados (contêiner de blob, pasta e arquivo). 
 
1. Selecione o botão **+** (mais) adição e, em seguida, selecione **Conjunto de Dados**.

   ![Menu para criar um conjunto de dados](./media/quickstart-create-data-factory-portal/new-dataset-menu.png)
1. Na página **Novo Conjunto de Dados**, selecione **Armazenamento de Blobs do Azure** e, em seguida, selecione **Concluir**. 

   ![Selecionando “Armazenamento de Blobs do Azure”](./media/quickstart-create-data-factory-portal/select-azure-blob-dataset.png)
1. Na página **Selecionar Formato**, escolha o tipo de formato dos dados e, em seguida, selecione **Continuar**. Nesse caso, selecione **Binário** ao copiar arquivos no estado em que se encontram sem analisar o conteúdo.

    ![Tipo de formato de dado](./media/doc-common-process/select-binary.png)

1. Na página **Definir Propriedades**, conclua as seguintes etapas:

    a. Em **Nome**, insira **InputDataset**. 

    b. Selecione **AzureStorageLinkedService** para **Serviço vinculado**.

    c. Para **Caminho do arquivo**, selecione o botão **Procurar**.

    d. Na janela **Escolher um arquivo ou uma pasta**, navegue até a pasta **entrada** no contêiner **adftutorial**, selecione o arquivo **emp.txt** e clique em **Concluir**.
    
    e. Selecione **Continuar**.   

    ![Definir propriedades para InputDataset](./media/quickstart-create-data-factory-portal/set-properties-for-inputdataset.png)
1. Repita as etapas para criar o conjunto de dados de saída:  

    a. Selecione o botão **+** (mais) adição e, em seguida, selecione **Conjunto de Dados**.

    b. Na página **Novo Conjunto de Dados**, selecione **Armazenamento de Blobs do Azure** e, em seguida, selecione **Concluir**.

    c. Na página **Selecionar Formato**, escolha o tipo de formato dos dados e, em seguida, selecione **Continuar**.

    d. Na página **Definir Propriedades**, especifique **OutputDataset** para o nome. Selecione **AzureStorageLinkedService** como serviço vinculado.

    e. Em **Caminho do arquivo**, insira **adftutorial/output**. Se a pasta **output** não existir, a atividade de cópia a criará em runtime.

    f. Selecione **Continuar**.   

## <a name="create-a-pipeline"></a>Criar um pipeline 
Neste procedimento, você criará e validará um pipeline com uma atividade Copiar que usa o conjunto de dados de entrada e saída. A Atividade de cópia copia os dados do arquivo especificado por você nas configurações do conjunto de dados de entrada para o arquivo especificado por você nas configurações do conjunto de dados de saída. Se o conjunto de dados de entrada especifica apenas uma pasta (não o nome de arquivo), a Atividade de cópia copia todos os arquivos da pasta de origem para o destino. 

1. Selecione o botão **+** (mais) adição e, em seguida, selecione **Pipeline**. 

1. Na guia **Geral**, especifique **CopyPipeline** para o **nome**. 

1. Na caixa de ferramentas **Atividades**, expanda **Mover e transformar**. Arraste a atividade **Copiar Dados** da caixa de ferramentas **Atividades** para a superfície do Designer de Pipeline. Você também pode pesquisar atividades na caixa de ferramentas **Atividades**. Especifique **CopyFromBlobToBlob** para o **Nome**.

1. Alterne para a guia **Fonte** nas configurações da atividade de cópia e selecione **InputDataset** para o **Conjunto de dados de origem**.

1. Alterne para a guia **Coletor** nas configurações da atividade de cópia e selecione **OutputDataset** para o **Conjunto de dados do coletor**.

1. Clique em **Validar** na barra de ferramentas do pipeline sobre a tela para validar as configurações de pipeline. Confirme se esse pipeline foi validado com êxito. Para fechar a saída de validação, selecione o botão **>>** (seta para a direita). 

## <a name="debug-the-pipeline"></a>Depurar o pipeline
Nesta etapa, você depura o pipeline antes de implantá-lo no Data Factory. 

1. Na barra de ferramentas do pipeline acima da tela, clique em **Depurar** para disparar uma execução de teste. 
    
1. Confirme se você vê o status da execução do pipeline na guia **Saída** das configurações do pipeline na parte inferior. 

1. Confirme que você vê um arquivo de saída na pasta **saída** do contêiner **adftutorial**. O serviço Data Factory cria a pasta de saída automaticamente caso ela não exista. 

## <a name="trigger-the-pipeline-manually"></a>Disparar o pipeline manualmente
Nesta procedimento, você implanta entidades (serviços vinculados, conjuntos de dados, pipelines) ao Azure Data Factory. Depois, dispare manualmente a execução do pipeline. 

1. Antes de disparar um pipeline, você deve publicar as entidades no Data Factory. Para publicar, selecione **Publicar Tudo** na parte superior. 

   ![Botão Publicar](./media/quickstart-create-data-factory-portal/publish-button.png)
1. Para disparar o pipeline manualmente, selecione **Adicionar Gatilho** na barra de ferramentas do pipeline e, em seguida, selecione **Disparar Agora**. Na página **Execução de Pipeline**, selecione **Concluir**.

## <a name="monitor-the-pipeline"></a>Monitorar o Pipeline

1. Alterne para a guia **Monitorar** à esquerda. Use o botão **Atualizar** para atualizar a lista.

   ![Guia para monitoramento de execuções de pipeline](./media/quickstart-create-data-factory-portal/monitor-trigger-now-pipeline.png)
1. Selecione **Exibir Execuções de Atividade** em **Ações**. Você vê o status de execução da atividade de cópia nesta página. 

1. Para exibir detalhes sobre a operação de cópia, selecione **Detalhes** (imagem de óculos) na coluna **Ações**. Para obter detalhes sobre as propriedades, confira [Visão geral da atividade de cópia](copy-activity-overview.md). 

   ![Detalhes da operação de cópia](./media/quickstart-create-data-factory-portal/copy-operation-details.png)
1. Confirme que você vê um arquivo novo na pasta de **saída**. 
1. Alterne novamente para a exibição **Execuções do Pipeline** da exibição **Execuções de Atividade** selecionando o link **Execuções de Pipeline**. 

## <a name="trigger-the-pipeline-on-a-schedule"></a>Disparar o pipeline em um cronograma
Este procedimento é opcional neste tutorial. Você pode criar um *agendador de gatilho* para agendar a execução periódica do pipeline (por hora, diariamente, e assim por diante). Nesta procedimento, você cria um gatilho para ser executado a cada minuto até a data e hora de término especificadas. 

1. Alterne para a guia **Autor**. 

1. Acesse o pipeline, selecione **Adicionar Gatilho** na barra de ferramentas do pipeline e, em seguida, selecione **Novo/Editar**. 

1. Na página **Adicionar gatilhos**, selecione **Escolher gatilho** e, em seguida, selecione **Novo**. 

1. Na página **Novo gatilho**, no campo **Final**, selecione **Na Data**, especifique como hora de término alguns minutos após a hora atual e selecione **Aplicar**. 

   Um custo associado a cada execução de pipeline, então, especifique o a hora de término como apenas alguns minutos após a hora de início. Verifique se está como o mesmo dia. No entanto, verifique se há tempo suficiente para a execução do pipeline entre a hora da publicação e a hora de término. O gatilho só entra em vigor depois de você publicar a solução no Data Factory, e não ao salvar o gatilho na interface do usuário. 

1. Na página **Novo Gatilho**, marque a caixa de seleção **Ativado** e, em seguida, selecione **Salvar**. 

   ![Nova configuração de gatilho](./media/quickstart-create-data-factory-portal/trigger-settings-next.png)
1. Examine a mensagem de aviso e selecione **Concluir**.

1. Clique em **Publicar Tudo** para publicar as alterações no Data Factory. 

1. Alterne para a guia **Monitorar** à esquerda. Selecione **Atualizar** para atualizar a lista. Você verá que o pipeline é executado uma vez por minuto desde o momento da publicação até hora de término. 

   Observe os valores na coluna **Disparado Por**. A execução do gatilho manual foi feita em uma etapa anterior (**Disparar agora**). 

1. Alterne para o modo de exibição **Execuções de gatilho**. 

1. Confirme que um arquivo de saída é criado para cada execução de pipeline até a data e hora de término especificadas na pasta **saída**. 

## <a name="next-steps"></a>Próximas etapas
O pipeline neste exemplo copia dados de uma localização para outra em um Armazenamento de Blobs do Azure. Percorra os [tutoriais](tutorial-copy-data-portal.md) para saber mais sobre o uso do Data Factory em mais cenários. 
