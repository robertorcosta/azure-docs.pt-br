---
title: 'Tutorial: Criar uma definição de trabalho do Apache Spark no Synapse Studio'
description: Tutorial - Use o Azure Synapse Analytics para criar definições de trabalho do Spark e enviá-las a um Apache Spark para um pool do Azure Synapse Analytics.
author: hrasheed-msft
ms.author: jejiang
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.subservice: ''
ms.date: 04/15/2020
ms.openlocfilehash: ac3e163ffefcb7b164860b0c4fa42edc866227e3
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87065634"
---
# <a name="tutorial-create-apache-spark-job-definition-in-synapse-studio"></a>Tutorial: Criar uma definição de trabalho do Apache Spark no Synapse Studio

Este tutorial demonstra como usar o Azure Synapse Studio para criar definições de trabalho do Apache Spark e depois enviá-las a um pool do Apache Spark.

Este tutorial cobre as seguintes tarefas:

* Criar uma definição de trabalho do Apache Spark para PySpark (Python)
* Criar uma definição de trabalho do Apache Spark para Spark (Scala)
* Criar uma definição de trabalho do Apache Spark para .NET Spark (C#/F#)
* Enviar uma definição de trabalho do Apache Spark como um trabalho em lotes
* Adicionar uma definição de trabalho do Apache Spark no pipeline

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, verifique se você atende aos seguintes requisitos:

* Um workspace do Azure Synapse Analytics. Para obter instruções, confira [Criar um workspace do Azure Synapse Analytics](../../machine-learning/how-to-manage-workspace.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#create-a-workspace).
* Um pool do Apache Spark
* Uma conta de armazenamento do ADLS Gen2. Você precisa ser o proprietário de dados do blob de armazenamento do sistema de arquivos ADLS Gen2 com o qual deseja trabalhar. Se não for, você precisará adicionar a permissão manualmente.

## <a name="create-an-apache-spark-job-definition-for-pyspark-python"></a>Criar uma definição de trabalho do Apache Spark para PySpark (Python)

Nesta seção, você cria uma definição de trabalho do Apache Spark para PySpark (Python).

1. Abra o [Azure Synapse Studio](https://web.azuresynapse.net/).

2. Você pode acessar [Arquivos de exemplo para criar definições de trabalho do Apache Spark](https://github.com/Azure-Samples/Synapse/tree/master/Spark/Python) para baixar **wordcount.py** e **shakespear.txt**. Em seguida, carregue esses arquivos no Armazenamento do Azure: Clique em **Dados**, selecione **Contas de armazenamento** e carregue os arquivos relacionados para o sistema de arquivos do ADLS Gen2. Ignore esta etapa se os arquivos já estiverem no Armazenamento do Azure. 

     ![carregar arquivo do Python](./media/apache-spark-job-definitions/upload-python-file.png)

3. Clique no hub **Desenvolver**, selecione **Definições de trabalho do Spark** no painel esquerdo, clique no nó de ação '…' ao lado de **Definição de trabalho do Spark** e, em seguida, selecione **Nova definição de trabalho do Spark** no menu de contexto.

     ![criar uma definição para Python](./media/apache-spark-job-definitions/create-new-definition.png)

4. Selecione **PySpark (Python)** na lista suspensa Linguagem na janela principal de definição de trabalho do Apache Spark.

5. Preencha as informações para a definição de trabalho do Apache Spark. Você pode copiar as informações de exemplo.

     |  Propriedade   | Descrição   |  
     | ----- | ----- |  
     |Nome da definição do trabalho| Insira um nome para a definição de trabalho do Apache Spark. Esse nome pode ser atualizado a qualquer momento até que seja publicado. Exemplo: `job definition sample`|
     |Arquivo de definição principal| O arquivo principal usado para o trabalho. Selecione um arquivo PY do seu armazenamento. Você pode selecionar **Carregar arquivo** para carregar o arquivo em uma conta de armazenamento. Exemplo: `abfss://…/path/to/wordcount.py`|
     |Argumentos de linha de comando| Argumentos opcionais para o trabalho. Exemplo: `abfss://…/path/to/shakespeare.txt abfss://…/path/to/result`|
     |Arquivos de referência| Arquivos adicionais usados para referência no arquivo de definição principal. Você pode selecionar **Carregar arquivo** para carregar o arquivo em uma conta de armazenamento. |
     |Pool do Spark| O trabalho será enviado para o pool do Apache Spark selecionado.|
     |Versão do Spark| A versão do Apache Spark que o pool do Apache Spark está executando.|
     |Executores| Número de executores a serem fornecidos no pool do Apache Spark especificado para o trabalho.|
     |Tamanho do executor| Número de núcleos e memória a serem usados para os executores fornecidos no pool do Apache Spark especificado para o trabalho.|  
     |Tamanho do driver| Número de núcleos e memória a serem usados para o driver fornecido no pool do Apache Spark especificado para o trabalho.|

     ![Definir o valor da definição de trabalho do Spark para Python](./media/apache-spark-job-definitions/create-py-definition.png)

6. Selecione **Publicar** para salvar a definição de trabalho do Apache Spark.

     ![publicar definição de py](./media/apache-spark-job-definitions/publish-py-definition.png)

## <a name="create-an-apache-spark-job-definition-for-apache-sparkscala"></a>Criar uma definição de trabalho do Apache Spark para o Apache Spark (Scala)

Nesta seção, você cria uma definição de trabalho do Apache Spark para o Apache Spark (Scala).

 1. Abra o [Azure Synapse Studio](https://web.azuresynapse.net/).

 2. Você pode acessar [Arquivos de exemplo para criar definições de trabalho do Apache Spark](https://github.com/Azure-Samples/Synapse/tree/master/Spark/Scala) para baixar **wordcount.jar** e **shakespear.txt**. Em seguida, carregue esses arquivos no Armazenamento do Azure: Clique em **Dados**, selecione **Contas de armazenamento** e carregue os arquivos relacionados para o sistema de arquivos do ADLS Gen2. Ignore esta etapa se os arquivos já estiverem no Armazenamento do Azure. 
 
     ![preparar estrutura do Scala](./media/apache-spark-job-definitions/prepare-scala-structure.png)

 3. Clique no hub **Desenvolver**, selecione **Definições de trabalho do Spark** no painel esquerdo, clique no nó de ação '…' ao lado de **Definição de trabalho do Spark** e, em seguida, selecione **Nova definição de trabalho do Spark** no menu de contexto.
     ![criar uma definição para Scala](./media/apache-spark-job-definitions/create-new-definition.png)

 4. Selecione **Spark (Scala)** na lista suspensa Linguagem na janela principal de definição de trabalho do Apache Spark.

 5. Preencha as informações para a definição de trabalho do Apache Spark. Você pode copiar as informações de exemplo.

     |  Propriedade   | Descrição   |  
     | ----- | ----- |  
     |Nome da definição do trabalho| Insira um nome para a definição de trabalho do Apache Spark. Esse nome pode ser atualizado a qualquer momento até que seja publicado. Exemplo: `job definition sample`|
     |Arquivo de definição principal| O arquivo principal usado para o trabalho. Selecione um arquivo JAR do seu armazenamento. Você pode selecionar **Carregar arquivo** para carregar o arquivo em uma conta de armazenamento. Exemplo: `abfss://…/path/to/wordcount.jar`|
     |Nome da classe principal| O identificador totalmente qualificado ou a classe principal que está no arquivo de definição principal. Exemplo: `WordCount`|
     |Argumentos de linha de comando| Argumentos opcionais para o trabalho. Exemplo: `abfss://…/path/to/shakespeare.txt abfss://…/path/to/result`|
     |Arquivos de referência| Arquivos adicionais usados para referência no arquivo de definição principal. Você pode selecionar **Carregar arquivo** para carregar o arquivo em uma conta de armazenamento.|
     |Pool do Spark| O trabalho será enviado para o pool do Apache Spark selecionado.|
     |Versão do Spark| A versão do Apache Spark que o pool do Apache Spark está executando.|
     |Executores| Número de executores a serem fornecidos no pool do Apache Spark especificado para o trabalho.|  
     |Tamanho do executor| Número de núcleos e memória a serem usados para os executores fornecidos no pool do Apache Spark especificado para o trabalho.|
     |Tamanho do driver| Número de núcleos e memória a serem usados para o driver fornecido no pool do Apache Spark especificado para o trabalho.|

     ![Definir o valor da definição de trabalho do Spark para Scala](./media/apache-spark-job-definitions/create-scala-definition.png)

 6. Selecione **Publicar** para salvar a definição de trabalho do Apache Spark.

     ![publicar definição do Scala](./media/apache-spark-job-definitions/publish-scala-definition.png)


## <a name="create-an-apache-spark-job-definition-for-net-sparkcf"></a>Criar uma definição de trabalho do Apache Spark para .NET Spark (C#/F#)

Nesta seção, você criará uma definição de trabalho do Apache Spark para .NET Spark (C#/F#).
 1. Abra o [Azure Synapse Studio](https://web.azuresynapse.net/).

 2. Você pode acessar [Arquivos de exemplo para criar definições de trabalho do Apache Spark](https://github.com/Azure-Samples/Synapse/tree/master/Spark/DotNET) para baixar **wordcount.zip** e **shakespear.txt**. Em seguida, carregue esses arquivos no Armazenamento do Azure: Clique em **Dados**, selecione **Contas de armazenamento** e carregue os arquivos relacionados para o sistema de arquivos do ADLS Gen2. Ignore esta etapa se os arquivos já estiverem no Armazenamento do Azure. 

     ![preparar estrutura dotnet](./media/apache-spark-job-definitions/prepare-scala-structure.png)

 3. Clique no hub **Desenvolver**, selecione **Definições de trabalho do Spark** no painel esquerdo, clique no nó de ação '…' ao lado de **Definição de trabalho do Spark** e, em seguida, selecione **Nova definição de trabalho do Spark** no menu de contexto.

     ![criar uma definição para dotnet](./media/apache-spark-job-definitions/create-new-definition.png)

 4. Selecione **.NET Spark(C#/F#)** na lista suspensa Linguagem na janela principal de definição de trabalho do Apache Spark.

 5. Preencha as informações para a definição de trabalho do Apache Spark. Você pode copiar as informações de exemplo.
    
     |  Propriedade   | Descrição   |  
     | ----- | ----- |  
     |Nome da definição do trabalho| Insira um nome para a definição de trabalho do Apache Spark. Esse nome pode ser atualizado a qualquer momento até que seja publicado. Exemplo: `job definition sample`|
     |Arquivo de definição principal| O arquivo principal usado para o trabalho. Selecione um arquivo zip que contenha seu aplicativo .NET para Apache Spark (ou seja, o arquivo executável principal, DLLs que contenham funções definidas pelo usuário e outros arquivos necessários) do seu armazenamento. Você pode selecionar **Carregar arquivo** para carregar o arquivo em uma conta de armazenamento. Exemplo: `abfss://…/path/to/wordcount.zip`|
     |Arquivo executável principal| O arquivo executável principal no arquivo zip de definição principal. Exemplo: `WordCount`|
     |Argumentos de linha de comando| Argumentos opcionais para o trabalho. Exemplo: `abfss://…/path/to/shakespeare.txt abfss://…/path/to/result`|
     |Arquivos de referência| Arquivos adicionais requeridos pelos nós de trabalho para execução do aplicativo .NET para Apache Spark que não estão incluídos no arquivo ZIP de definição principal (ou seja, jars dependentes, DLLs de funções definidas pelo usuário adicionais e outros arquivos de configuração). Você pode selecionar **Carregar arquivo** para carregar o arquivo em uma conta de armazenamento.|
     |Pool do Spark| O trabalho será enviado para o pool do Apache Spark selecionado.|
     |Versão do Spark| A versão do Apache Spark que o pool do Apache Spark está executando.|
     |Executores| Número de executores a serem fornecidos no pool do Apache Spark especificado para o trabalho.|  
     |Tamanho do executor| Número de núcleos e memória a serem usados para os executores fornecidos no pool do Apache Spark especificado para o trabalho.|
     |Tamanho do driver| Número de núcleos e memória a serem usados para o driver fornecido no pool do Apache Spark especificado para o trabalho.|

     ![Definir o valor da definição de trabalho do Spark para dotnet](./media/apache-spark-job-definitions/create-net-definition.png)

 6. Selecione **Publicar** para salvar a definição de trabalho do Apache Spark.

      ![publicar definição de dotnet](./media/apache-spark-job-definitions/publish-net-definition.png)

## <a name="submit-an-apache-spark-job-definition-as-a-batch-job"></a>Enviar uma definição de trabalho do Apache Spark como um trabalho em lotes

Depois de criar uma definição de trabalho do Apache Spark, você pode enviá-la para um pool do Apache Spark. Verifique se você é o proprietário de dados do Storage Blob do sistema de arquivos do ADLS Gen2 com o qual deseja trabalhar. Se não for, você precisará adicionar a permissão manualmente.

### <a name="scenario-1-submit-apache-spark-job-definition"></a>Cenário 1: Enviar uma definição de trabalho do Apache Spark
 1. Abra uma janela de definição de trabalho do Apache Spark clicando nela.

      ![Abrir a definição de trabalho do Spark a ser enviada ](./media/apache-spark-job-definitions/open-spark-definition.png)

 2. Clique no ícone **enviar** para enviar seu projeto para o pool do Apache Spark selecionado. Você pode clicar na guia **URL de monitoramento do Spark** para ver o LogQuery do aplicativo Apache Spark.

    ![Clique no botão enviar para enviar a definição de trabalho do Spark](./media/apache-spark-job-definitions/submit-spark-definition.png)

    ![A caixa de diálogo Envio do Spark](./media/apache-spark-job-definitions/submit-definition-result.png)

### <a name="scenario-2-view-apache-spark-job-running-progress"></a>Cenário 2: Exibir o progresso da execução do trabalho do Apache Spark

 1. Clique em **Monitorar** e selecione a opção **Aplicativos Spark**. Você pode encontrar o aplicativo Apache Spark enviado.

     ![Exibir aplicativo Spark](./media/apache-spark-job-definitions/view-spark-application.png)

 2. Em seguida, clique no aplicativo Apache Spark. A janela do **LogQuery** será exibida. Você pode ver o andamento da execução do trabalho no **LogQuery**.
     
     ![Exibir LogQuery do aplicativo Spark](./media/apache-spark-job-definitions/view-job-log-query.png)

### <a name="scenario-3-check-output-file"></a>Cenário 3: verificar arquivo de saída

 1. Clique em **Dados** e, em seguida, selecione **Contas de armazenamento**. Após uma execução bem-sucedida, você pode ir para o armazenamento de ADLS Gen2 e verificar se as saídas são geradas.

     ![Exibir arquivo de saída](./media/apache-spark-job-definitions/view-output-file.png)

## <a name="add-an-apache-spark-job-definition-into-pipeline"></a>Adicionar uma definição de trabalho do Apache Spark no pipeline

Nesta seção, você adiciona uma definição de trabalho do Apache Spark no pipeline.

 1. Abra uma definição de trabalho existente do Apache Spark.

 2. Clique no ícone no canto superior direito da definição de trabalho do Apache Spark, escolha **Pipeline existente** ou **Novo pipeline**. Consulte a página do Pipeline para obter mais informações.

     ![adicionar ao pipeline](./media/apache-spark-job-definitions/add-to-pipeline01.png)

     ![adicionar ao pipeline](./media/apache-spark-job-definitions/add-to-pipeline02.png)

## <a name="next-steps"></a>Próximas etapas

Este tutorial demonstra como usar o Azure Synapse Studio para criar definições de trabalho do Apache Spark e depois enviá-las a um pool do Apache Spark. Em seguida, você pode usar o Azure Synapse Studio para criar conjuntos de dados do Power BI e gerenciar dados do Power BI.

