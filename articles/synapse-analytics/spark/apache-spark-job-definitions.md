---
title: 'Tutorial – Azure Synapse Analytics: Definição de trabalho do Spark para o Synapse'
description: Tutorial – use o Azure Synapse Analytics para criar definições de trabalho do Spark e enviá-las a um Pool do Spark do Synapse.
author: hrasheed-msft
ms.author: jejiang
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 04/15/2020
ms.openlocfilehash: d5443a2db6f4fecbd84ef51166f44c3a6e920aee
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81425905"
---
# <a name="tutorial-use-azure-synapse-analytics-to-create-apache-spark-job-definitions-for-synapse-spark-pools"></a>Tutorial: usar o Azure Synapse Analytics para criar definições de trabalho do Spark para pools do Spark do Synapse

Este tutorial demonstra como usar o Azure Synapse Analytics para criar definições de trabalho do Spark e depois enviá-las a um Pool do Spark do Synapse. Você pode usar o plug-in destas maneiras:

* Desenvolver e enviar uma definição de trabalho do Spark em um Pool do Spark do Synapse.
* Exibir detalhes do trabalho após o envio.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
>
> * Desenvolver e enviar uma definição de trabalho do Spark em um Pool do Spark do Synapse.
> * Exibir detalhes do trabalho após o envio.

## <a name="prerequisites"></a>Pré-requisitos

* Um workspace do Azure Synapse Analytics. Para obter instruções, confira [Criar um workspace do Azure Synapse Analytics](../../machine-learning/how-to-manage-workspace.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#create-a-workspace).

## <a name="get-started"></a>Introdução

Antes de enviar uma definição de trabalho do Spark, você precisa ser o proprietário de dados do blob de armazenamento do sistema de arquivos ADLS Gen2 com o qual deseja trabalhar. Se não for, você precisará adicionar a permissão manualmente.

### <a name="scenario-1-add-permission"></a>Cenário 1: adicionar permissão

1. Abra o [Microsoft Azure](https://ms.portal.azure.com) e, em seguida, abra a conta de Armazenamento.

2. Clique em **Contêineres** e, em seguida, crie um **sistema de arquivos**. Este tutorial usa `sparkjob`.

    ![Clique no botão enviar para enviar a definição de trabalho do Spark](./media/apache-spark-job-definitions/open-azure-container.png)

    ![A caixa de diálogo Envio do Spark](./media/apache-spark-job-definitions/create-new-filesystem.png)

3. Abra `sparkjob`, clique em **Controle de Acesso (IAM)** e, em seguida, clique em **Adicionar** e selecione **Adicionar atribuição de função**.

    ![Clique no botão enviar para enviar a definição de trabalho do Spark](./media/apache-spark-job-definitions/add-role-assignment-01.png)

    ![Clique no botão enviar para enviar a definição de trabalho do Spark](./media/apache-spark-job-definitions/add-role-assignment-02.png)

4. Clique em **Atribuições de função**, insira o nome de usuário e verifique a função de usuário.

    ![Clique no botão enviar para enviar a definição de trabalho do Spark](./media/apache-spark-job-definitions/verify-user-role.png)

### <a name="scenario-2-prepare-folder-structure"></a>Cenário 2: preparar a estrutura de pastas

Antes de enviar uma definição de trabalho do Spark, você precisa carregar os arquivos para o ADLS Gen2 e preparar a estrutura de pastas ali. Usamos o nó do Armazenamento no Synapse Studio para armazenar arquivos.

1. Abra o [Azure Synapse Analytics](https://web.azuresynapse.net/).

2. Clique em **Dados**, selecione **Contas de armazenamento** e carregue os arquivos relevantes para o seu sistema de arquivos do ADLS Gen2. Damos suporte a escala, Java, .NET e Python. Este tutorial usa o exemplo na figura como uma demonstração, você poderá alterar a estrutura do projeto como desejar.

    ![Definir o valor da definição de trabalho do Spark](./media/apache-spark-job-definitions/prepare-project-structure.png)

## <a name="create-a-spark-job-definition"></a>Criar uma definição de trabalho do Spark

1. Abra o [Azure Synapse Analytics](https://web.azuresynapse.net/) e selecione **Desenvolver**.

2. Selecione **Definições de trabalho do Spark** no painel esquerdo.

3. Clique no nó **Ações** à direita de "Definições de trabalho do Spark".

     ![Criar uma definição de trabalho do Spark](./media/apache-spark-job-definitions/create-new-definition-01.png)

4. Na lista suspensa **Ações**, selecione **Nova definição de trabalho do Spark**

     ![Criar uma definição de trabalho do Spark](./media/apache-spark-job-definitions/create-new-definition-02.png)

5. Na janela nova definição de trabalho do Spark, selecione a linguagem de programação e forneça as seguintes informações:  

   * Selecione a **Linguagem** como **Spark (Scala)** .

    |  Propriedade   | Descrição   |  
    | ----- | ----- |  
    |Nome da definição do trabalho| Insira um nome para a sua definição de trabalho do Spark.  Este tutorial usa `job definition sample`. Esse nome pode ser atualizado a qualquer momento até que seja publicado.|  
    |Arquivo de definição principal| O arquivo principal usado para o trabalho. Selecione um arquivo JAR do seu armazenamento. Você pode selecionar **Carregar arquivo** para carregar o arquivo em uma conta de armazenamento. |
    |Nome da classe principal| O identificador totalmente qualificado ou a classe principal que está no arquivo de definição principal.|
    |Argumentos de linha de comando| Argumentos opcionais para o trabalho.|
    |Arquivos de referência| Arquivos adicionais usados para referência no arquivo de definição principal. Você pode selecionar **Carregar arquivo** para carregar o arquivo em uma conta de armazenamento.|
    |Pool do Spark| O trabalho será enviado para o Pool do Spark selecionado.|
    |Versão do Spark| A versão do Spark que o Pool do Spark está executando.|
    |Executores| Número de executores a serem fornecidos no Pool do Spark especificado para o trabalho.|
    |Tamanho do executor| Número de núcleos e memória a serem usados para os executores fornecidos no Pool do Spark especificado para o trabalho.|  
    |Tamanho do driver| Número de núcleos e memória a serem usados para o driver fornecido no Pool do Spark especificado para o trabalho.|

    ![Definir o valor da definição de trabalho do Spark](./media/apache-spark-job-definitions/create-scala-definition.png)

   * Selecione a **Linguagem** como **PySpark (Python)** .

    |  Propriedade   | Descrição   |  
    | ----- | ----- |  
    |Nome da definição do trabalho| Insira um nome para a sua definição de trabalho do Spark.  Este tutorial usa `job definition sample`. Esse nome pode ser atualizado a qualquer momento até que seja publicado.|  
    |Arquivo de definição principal| O arquivo principal usado para o trabalho. Selecione um arquivo PY do seu armazenamento. Você pode selecionar **Carregar arquivo** para carregar o arquivo em uma conta de armazenamento.|
    |Argumentos de linha de comando| Argumentos opcionais para o trabalho.|
    |Arquivos de referência| Arquivos adicionais usados para referência no arquivo de definição principal. Você pode selecionar **Carregar arquivo** para carregar o arquivo em uma conta de armazenamento.|
    |Pool do Spark| O trabalho será enviado para o Pool do Spark selecionado.|
    |Versão do Spark| A versão do Spark que o Pool do Spark está executando.|
    |Executores| Número de executores a serem fornecidos no Pool do Spark especificado para o trabalho.|
    |Tamanho do executor| Número de núcleos e memória a serem usados para os executores fornecidos no Pool do Spark especificado para o trabalho.|  
    |Tamanho do driver| Número de núcleos e memória a serem usados para o driver fornecido no Pool do Spark especificado para o trabalho.|

    ![Definir o valor da definição de trabalho do Spark](./media/apache-spark-job-definitions/create-py-definition.png)

   * Selecione a **Linguagem** como **.NET Spark (C#/F#)** .

    |  Propriedade   | Descrição   |  
    | ----- | ----- |  
    |Nome da definição do trabalho| Insira um nome para a sua definição de trabalho do Spark.  Este tutorial usa `job definition sample`. Esse nome pode ser atualizado a qualquer momento até que seja publicado.|  
    |Arquivo de definição principal| O arquivo principal usado para o trabalho. Selecione um arquivo zip que contenha seu aplicativo .NET para Spark (ou seja, o arquivo executável principal, DLLs que contenham funções definidas pelo usuário e outros arquivos necessários) do seu armazenamento. Você pode selecionar **Carregar arquivo** para carregar o arquivo em uma conta de armazenamento.|
    |Arquivo executável principal| O arquivo executável principal no arquivo zip de definição principal.|
    |Argumentos de linha de comando| Argumentos opcionais para o trabalho.|
    |Arquivos de referência| Arquivos adicionais requeridos pelos nós de trabalho para execução do aplicativo .NET para Spark que não estão incluídos no arquivo zip de definição principal (ou seja, jars dependentes, DLLs de funções definidas pelo usuário adicionais e outros arquivos de configuração). Você pode selecionar **Carregar arquivo** para carregar o arquivo em uma conta de armazenamento.|
    |Pool do Spark| O trabalho será enviado para o Pool do Spark selecionado.|
    |Versão do Spark| A versão do Spark que o Pool do Spark está executando.|
    |Executores| Número de executores a serem fornecidos no Pool do Spark especificado para o trabalho.|
    |Tamanho do executor| Número de núcleos e memória a serem usados para os executores fornecidos no Pool do Spark especificado para o trabalho.|  
    |Tamanho do driver| Número de núcleos e memória a serem usados para o driver fornecido no Pool do Spark especificado para o trabalho.|

    ![Definir o valor da definição de trabalho do Spark](./media/apache-spark-job-definitions/create-net-definition.png)

6. Selecione **Publicar** para salvar a definição de trabalho do Spark.

    ![Publicar definição de trabalho do Spark](./media/apache-spark-job-definitions/publish-net-definition.png)

## <a name="submit-a-spark-job-definition"></a>Enviar uma definição de trabalho do Spark

Depois de criar uma definição de trabalho do Spark, você pode enviá-la para um Pool do Spark do Synapse. Verifique se você passou pelas etapas em **Introdução** antes de experimentar amostras nesta parte.

### <a name="scenario-1-submit-spark-job-definition"></a>Cenário 1: enviar uma definição de trabalho do Spark

1. Abra uma janela de definição de trabalho do Spark clicando nela.

      ![Abrir a definição de trabalho do Spark a ser enviada ](./media/apache-spark-job-definitions/open-spark-definition.png)

2. Clique no ícone **enviar** para enviar seu projeto para o Pool do Spark selecionado. Você pode clicar na guia **URL de monitoramento do Spark** para ver o LogQuery do aplicativo Spark.

    ![Clique no botão enviar para enviar a definição de trabalho do Spark](./media/apache-spark-job-definitions/submit-spark-definition.png)

    ![A caixa de diálogo Envio do Spark](./media/apache-spark-job-definitions/submit-definition-result.png)

### <a name="scenario-2-view-spark-job-running-progress"></a>Cenário 2: ver o progresso da execução do trabalho do Spark

1. Clique em **Monitorar** e selecione a opção **Aplicativos Spark**. Você pode encontrar o aplicativo Spark enviado.

    ![Exibir aplicativo Spark](./media/apache-spark-job-definitions/view-spark-application.png)

2. Em seguida, clique no aplicativo Spark. A janela do **LogQuery** será exibida. Você pode ver o andamento da execução do trabalho no **LogQuery**.

    ![Exibir LogQuery do aplicativo Spark](./media/apache-spark-job-definitions/view-job-log-query.png)

### <a name="scenario-3-check-output-file"></a>Cenário 3: verificar arquivo de saída

 1. Clique em **Dados** e, em seguida, selecione **Contas de armazenamento**. Após uma execução bem-sucedida, você pode ir para o armazenamento de ADLS Gen2 e verificar se as saídas são geradas.

    ![Exibir arquivo de saída](./media/apache-spark-job-definitions/view-output-file.png)

## <a name="next-steps"></a>Próximas etapas

Este tutorial demonstrou como usar o Azure Synapse Analytics para criar definições de trabalho do Spark e depois enviá-las a um Pool do Spark do Synapse. Em seguida, você pode usar o Azure Synapse Analytics para criar conjuntos de dados do Power BI e gerenciar dados do Power BI. 

- [Conectar-se a dados no Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-quickstart-connect-to-data)
- [Visualizar com o Power BI](../sql-data-warehouse/sql-data-warehouse-get-started-visualize-with-power-bi.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
