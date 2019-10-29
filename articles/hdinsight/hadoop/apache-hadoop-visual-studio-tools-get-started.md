---
title: Apache Hadoop & ferramentas de Data Lake do Visual Studio – Azure HDInsight
description: Saiba como instalar e usar o Data Lake Tools para Visual Studio para se conectar a clusters do Apache Hadoop no HDInsight do Azure e, em seguida, executar consultas do Hive.
keywords: ferramentas do hadoop, consulta hive, visual studio, hadoop do visual studio
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 7386250cf09d1cfbce8c34f85eaa57d07e67ed35
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044542"
---
# <a name="use-data-lake-tools-for-visual-studio-to-connect-to-azure-hdinsight-and-run-apache-hive-queries"></a>Use Data Lake Tools para Visual Studio para se conectar ao Azure HDInsight e executar consultas do Apache Hive

Saiba como usar [Microsoft Azure data Lake e ferramentas de Stream Analytics para Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504) (também chamado de ferramentas data Lake) para se conectar a clusters Apache Hadoop no [Azure HDInsight](../hdinsight-hadoop-introduction.md) e enviar consultas de Hive.  

Para obter mais informações sobre como usar o HDInsight, consulte [Introdução ao HDInsight](../hdinsight-hadoop-introduction.md) e [Começar a usar o HDInsight](apache-hadoop-linux-tutorial-get-started.md).  

Para obter mais informações sobre como conectar um cluster do Apache Storm, consulte [Desenvolver topologias C# para Apache Storm no HDInsight usando Visual Studio](../storm/apache-storm-develop-csharp-visual-studio-topology.md).

As Ferramentas do Data Lake para Visual Studio podem ser usadas para acessar o Data Lake Analytics e o HDInsight. Para obter informações sobre as Ferramentas do Data Lake, confira [Desenvolver scripts U-SQL usando as Ferramentas do Data Lake para Visual Studio](../../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo e usar as ferramentas de Data Lake para o Visual Studio, você precisará dos seguintes itens:

* Um cluster Azure HDInsight. Para criar um cluster Azure HDInsight, consulte [Introdução ao uso do Apache Hadoop no Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md). Para executar consultas do Hive interativas, é necessário ter um cluster de [Consulta interativa do HDInsight](../interactive-query/apache-interactive-query-get-started.md).  

* [Visual Studio](https://visualstudio.microsoft.com/downloads/) (2013 ou mais recente).  O [Visual Studio Community Edition](https://visualstudio.microsoft.com/vs/community/) é gratuito.  Consulte também [instalar o visual studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio) e o [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/). Há pequenas variações de interface com o Visual Studio 2019.

  > [!IMPORTANT]  
  > Não há mais suporte para ferramentas de Data Lake para Visual Studio 2013.

## <a name="install-data-lake-tools-for-visual-studio"></a>Instalar Ferramentas do Data Lake para Visual Studio  
<a name="install-or-update-data-lake-tools-for-visual-studio"></a>

* Visual Studio 2017 ou Visual Studio 2019  
  Durante a instalação, certifique-se de incluir pelo menos cargas de trabalho **desenvolvimento do Azure** ou **armazenamento e processamento de dados**.  

  Para instalações existentes, na barra de menus, navegue até **ferramentas** > **obter ferramentas e recursos...** para abrir o instalador do Visual Studio.  Em seguida, selecione pelo menos cargas de trabalho **desenvolvimento do Azure** ou **armazenamento e processamento de dados**.

  ![Captura de tela de Instalador do Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/vs-2017-installation.png)

* Visual Studio 2013 e 2015  
  [Baixe as ferramentas de data Lake](https://www.microsoft.com/download/details.aspx?id=49504). Escolha a versão das Ferramentas do Data Lake que correspondem à versão do Visual Studio.  

> [!NOTE]  
> Atualmente, somente a versão em inglês das Ferramentas do Data Lake para Visual Studio está disponível.

## <a name="update-data-lake-tools-for-visual-studio"></a>Atualizar as ferramentas de Data Lake para o Visual Studio  

1. Abra o Visual Studio.

2. Na barra de menus, navegue até **ferramentas** > **extensões e atualizações...** .

3. Na janela **extensões e atualizações** , expanda **atualizações** à esquerda.

4. Se uma atualização estiver disponível, as **ferramentas analíticas Azure data Lake e Stream** serão exibidas na janela principal.  Selecione **Atualização**.

> [!NOTE]  
> Você pode usar somente as Ferramentas do Data Lake versão 2.3.0.0 ou posterior para se conectar aos clusters de Consulta Interativa e executar consultas interativas Hive.

## <a name="connect-to-azure-subscriptions"></a>Conectar-se a assinaturas do Azure
Você pode usar as ferramentas do Data Lake para Visual Studio para se conectar a clusters HDInsight, executar algumas operações básicas de gerenciamento e executar consultas Hive.

> [!NOTE]  
> Para obter informações sobre como se conectar a um cluster Hadoop genérico, confira [Escrever e enviar consultas Hive usando o Visual Studio](https://blogs.msdn.com/b/xiaoyong/archive/2015/05/04/how-to-write-and-submit-hive-queries-using-visual-studio.aspx).

Para conectar-se à sua assinatura do Azure:

1. Abra o Visual Studio.

2. Na barra de menus, navegue até **exibir** > **Gerenciador de servidores**.

3. Em Gerenciador de Servidores, clique com o botão direito do mouse em **Azure**, selecione **conectar a Microsoft Azure assinatura...** e conclua o processo de entrada.

4. Em Gerenciador de Servidores, uma lista de clusters HDInsight existentes é exibida. Se não tiver nenhum cluster, você poderá criar um usando o portal do Azure, o Azure PowerShell ou o SDK do HDInsight. Para saber mais, veja [Criar clusters HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

   ![Data Lake as ferramentas para a lista de clusters do Visual Studio no Gerenciador de Servidores](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-server-explorer.png "Data Lake as ferramentas para a lista de clusters do Visual Studio no Gerenciador de Servidores")

5. Expanda um cluster do HDInsight. **Os bancos de dados do hive**, uma conta de armazenamento padrão, as contas de armazenamento vinculadas e o **log de serviço do Hadoop** são exibidos. Você pode expandir mais as entidades.

Depois de se conectar à sua assinatura do Azure, você poderá executar as seguintes tarefas.

Conectar-se ao portal do Azure pelo Visual Studio:

1. Em Gerenciador de Servidores, navegue até **Azure** > **HDInsight** e selecione o cluster.

2. Clique com o botão direito do mouse em um cluster HDInsight e selecione **gerenciar cluster no portal do Azure [sic]** .

Para fazer perguntas e/ou fornecer comentários do Visual Studio:

1. Em Gerenciador de Servidores, navegue até **Azure** > **HDInsight**.

2. Clique com o botão direito do mouse em **HDInsight** e selecione um **Fórum do MSDN** para fazer perguntas ou **forneça comentários** para fornecer comentários.

## <a name="link-a-cluster"></a>Vincular um cluster
Você pode vincular um cluster clicando com o botão direito do mouse no **HDInsight** e, em seguida, selecionando **vincular um cluster HDInsight**. Insira **a URL de conexão**, o nome de **usuário** e a **senha**, clique em **Avançar** e em **concluir**. o cluster deve estar listado no nó HDInsight com êxito.

![Captura de tela da caixa de diálogo ferramentas de Data Lake para o cluster de link do Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-dialog.png)

Clique com o botão direito do mouse no cluster vinculado, selecione **Editar**, o usuário pode atualizar as informações do cluster. A adição de um cluster HDInsight só dá suporte ao hive por enquanto.

![Captura de tela das ferramentas de Data Lake para a atualização do cluster de link do Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-update.png)

## <a name="explore-linked-resources"></a>Explorar recursos vinculados
No Gerenciador de Servidores, pode-se visualizar a conta de armazenamento padrão e quaisquer contas de armazenamento vinculadas. Se você expandir a conta de armazenamento padrão, visualizará os contêineres na conta de armazenamento. Tanto a conta de armazenamento padrão como o contêiner padrão estão marcados. Clique com o botão direito do mouse em qualquer um dos contêineres para ver o conteúdo do contêiner.

![Ferramentas de Data Lake para recursos vinculados do Visual Studio no Gerenciador de Servidores](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-linked-resources.png "Listar recursos vinculados")

Depois de abrir um contêiner, você poderá usar os botões abaixo para carregar, excluir e baixar blobs:

![Ferramentas de Data Lake para operações de blob do Visual Studio no Gerenciador de Servidores](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-blob-operations.png "Carregar, excluir e baixar BLOBs no Gerenciador de Servidores")

## <a name="run-interactive-apache-hive-queries"></a>Executar consultas interativas do Apache Hive
O [Apache Hive](https://hive.apache.org) é uma infraestrutura do data warehouse criada no Hadoop. O Hive é usado para análise, consultas e resumo de dados. Você pode usar as Ferramentas do Data Lake para Visual Studio a fim de executar consultas Hive pelo Visual Studio. Para obter mais informações sobre o Hive, consulte [Usar Apache Hive com HDInsight](hdinsight-use-hive.md).

A [Consulta Interativa](../interactive-query/apache-interactive-query-get-started.md) usa [Hive em LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) no Apache Hive 2.1. A Consulta Interativa traz interatividade a consultas de estilo data warehouse complexas em conjuntos de dados grandes e armazenados. A execução de consultas Hive na Consulta Interativa é muito mais rápida em comparação com os trabalhos em lotes tradicionais do Hive. 

> [!NOTE]  
> Você só pode executar consultas interativas do Hive quando se conecta a um cluster de [Consulta Interativa HDInsight](../interactive-query/apache-interactive-query-get-started.md).

Use também as Ferramentas do Data Lake para Visual Studio a fim de ver o que está em um trabalho do Hive. As Ferramentas do Data Lake para Visual Studio coletam e revelam logs Yarn de determinados trabalhos do Hive.

Em Gerenciador de Servidores, navegue até **Azure** > **HDInsight** e selecione o cluster.  Esse será o ponto de partida em Gerenciador de Servidores para as seções seguirem.

### <a name="view-hivesampletable"></a>Exibir hivesampletable
Todos os clusters HDInsight têm uma tabela de hive de exemplo padrão chamada `hivesampletable`.  

No cluster, navegue até **bancos de dados do Hive** > **padrão** > **hivesampletable**.

* Para exibir `hivesampletable` esquema:  
Expanda **hivesampletable**.

* Para exibir dados de `hivesampletable`:  
Clique com o botão direito do mouse em **hivesampletable**e selecione **exibir as primeiras 100 linhas**.  Isso é o equivalente a executar a seguinte consulta Hive usando o driver ODBC do Hive:

   `SELECT * FROM hivesampletable LIMIT 100`

  É possível personalizar a contagem de linhas.

  ![Captura de tela de uma consulta de esquema do hive do Visual Studio do HDInsight](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-hive-schema.png "Resultados da consulta de Hive")

### <a name="create-hive-tables"></a>Criar tabelas Hive
Você pode usar a GUI ou usar consultas Hive para criar uma tabela Hive. Para obter informações sobre o uso de consultas do Hive, consulte [Executar consultas do Apache Hive](#run.queries).

1. No cluster, navegue até **bancos de dados do Hive** > **padrão**.

2. Clique com o botão direito do mouse em **padrão**e selecione **criar tabela**.

3. Configure a tabela conforme desejado.  

4. Selecione **Criar Tabela** para enviar o trabalho e criar uma nova tabela Hive.

    ![Captura de tela da janela criar tabela do Ferramentas do Visual Studio do HDInsight](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-create-hive-table.png "Criar tabela do hive")

### <a name="run.queries"></a>Criar e executar consultas do hive
Você tem duas opções para criar e executar consultas do Hive:

* Criar consultas locais
* Criar um aplicativo Hive

Para criar e executar consultas ad hoc:

1. Clique com o botão direito do mouse no cluster em que você deseja executar a consulta e selecione **gravar uma consulta do hive**.  

2. Insira a seguinte consulta de Hive:

    ```sql
    SELECT devicemodel, COUNT(devicemodel) AS deviceCount
    FROM hivesampletable
    GROUP BY devicemodel
    ORDER BY devicemodel
    ```

    O editor do Hive é compatível com o IntelliSense. Agora as Ferramentas do Data Lake para Visual Studio dão suporte à obtenção de metadados remotos quando você edita o script do Hive. Por exemplo, se você digitar `SELECT * FROM`, o IntelliSense listará todos os nomes de tabela sugeridos. Quando um nome de tabela for especificado, o IntelliSense listará os nomes de coluna. As ferramentas dão suporte a quase todas as instruções DML Hive, subconsultas e UDFs internos.

    ![Captura de tela de um exemplo do HDInsight Ferramentas do Visual Studio IntelliSense 1](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-table-names.png "IntelliSense U-SQL")

    ![Captura de tela de um exemplo do HDInsight Ferramentas do Visual Studio IntelliSense 2](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-column-names.png "IntelliSense U-SQL")

   > [!NOTE]  
   > O IntelliSense sugere apenas os metadados dos clusters selecionados na Barra de Ferramentas do HDInsight.

3. Escolha o modo de execução:

    * **Interativo**  

      Verifique se **interativo** está selecionado e, em seguida, selecione **executar**.

      ![Captura de tela de consulta e execução](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-query-execute.png)  

    * **Batch**  

      Verifique se o **lote** está selecionado e, em seguida, selecione **Enviar**.  Se você selecionar a opção de envio avançado, configure o **nome do trabalho**, os **argumentos**, **as configurações adicionais**e o **diretório de status** para o script.

      ![Opções de consulta e lote do Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-query-batch.png)  

      ![Captura de tela de uma consulta de Hive Hadoop do HDInsight](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-submit-jobs-advanced.png "Enviar consultas")

      > [!NOTE]  
      > Você não pode enviar lotes para os clusters de consulta interativa.  Você deve usar o modo interativo.

Para criar e executar uma solução de Hive:

1. Na barra de menus, navegue até **arquivo** > **novo** > **projeto...** .

2. No painel esquerdo, navegue até **instalado** > **Azure Data Lake** **hive do > (HDInsight)** .  

3. No painel central, selecione **Aplicativo Hive**. Insira as propriedades e selecione **OK**.

    ![Captura de tela de um HDInsight Ferramentas do Visual Studio novo projeto do hive](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-new-hive-project.png "Criar aplicativos do hive por meio do Visual Studio")

4. No **Gerenciador de Soluções**, clique duas vezes em **Script.hql** para abri-lo.

### <a name="view-job-summary-and-output"></a>Exibir Resumo e saída do trabalho

O resumo do trabalho varia ligeiramente entre o modo de **lote** e **interativo** .

![Exibição da guia Resumo do trabalho Apache Hive](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-job-summary.png "Resumo do trabalho do hive")

Use o botão **Atualizar** para atualizar o status até que o status do trabalho seja alterado para **concluído**.  

* Para obter os detalhes do trabalho no modo de **lote** , selecione os links na parte inferior para ver a **consulta de trabalho**, **saída de trabalho**, log de **trabalho**ou **log de yarn**.

* Para obter os detalhes do trabalho do modo **interativo** , consulte **saída** de guias e **saída de HiveServer2**.

  ![Detalhes do trabalho do Visual Studio Apache Hive](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-job-details.png "Detalhes do trabalho do hive")

### <a name="view-job-graph"></a>Exibir grafo de trabalho

Atualmente, os grafos de trabalho são mostrados apenas para trabalhos do hive que usam tez como o mecanismo de execução.  Para saber mais sobre como habilitar o Tez, consulte [Usar o Apache Hive no HDInsight](hdinsight-use-hive.md).  Consulte também, [Use Apache tez em vez da redução de mapa](../hdinsight-hadoop-optimize-hive-query.md#use-apache-tez-instead-of-map-reduce).  

Para ver todos os operadores dentro do vértice, clique duas vezes nos vértices do gráfico do trabalho. Você também pode apontar para um operador específico e ver mais detalhes sobre o operador.

O grafo do trabalho pode não aparecer mesmo se tez for especificado como o mecanismo de execução se nenhum aplicativo tez for iniciado.  Isso pode acontecer porque o trabalho não contém instruções DML ou as instruções DML podem retornar sem iniciar um aplicativo tez. Por exemplo, `SELECT * FROM table1` não iniciará o aplicativo tez.

![Grafo de trabalho do Visual Studio Apache Hive](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-fast-path-hive-execution.png "Resumo do trabalho do hive")

### <a name="task-execution-detail"></a>Detalhes de execução da tarefa

No grafo do trabalho, você pode selecionar **detalhes de execução da tarefa** para obter informações estruturadas e visualizadas para trabalhos do hive. Você também pode obter mais detalhes sobre o trabalho. Se ocorrerem problemas de desempenho, você poderá usar o modo de exibição para obter mais detalhes sobre o problema. Por exemplo, você pode obter informações sobre como funciona cada tarefa e informações detalhadas sobre cada tarefa (leitura/gravação de dados, agendamento/início/término e assim por diante). Use as informações para ajustar as configurações de trabalho ou a arquitetura do sistema com base nas informações visualizadas.

![Janela de exibição de execução de tarefa do Data Lake Ferramentas do Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-task-execution-view.png "Exibição de execução de tarefa")


### <a name="view-hive-jobs"></a>Exibir trabalhos Hive
Você pode exibir consultas de trabalho, saída do trabalho, logs de trabalho e logs do Yarn para trabalhos do Hive.

Na versão mais recente das ferramentas, você pode ver o que está dentro de trabalhos do Hive coletando e identificando logs do Yarn. Um log do Yarn pode ajudar você a investigar problemas de desempenho. Para saber mais sobre como o HDInsight coleta logs do Yarn, confira [Acessar logs de aplicativo do HDInsight programaticamente](../hdinsight-hadoop-access-yarn-app-logs.md).

Para exibir trabalhos do Hive:

1. Clique com o botão direito do mouse em um cluster HDInsight e selecione **exibir trabalhos**. Uma lista dos trabalhos do Hive executados no cluster aparecerá.  

2. Selecione um trabalho. Na janela **Resumo do Trabalho de Hive**, selecione uma das seguintes opções:
    - **Consulta do Trabalho**
    - **Saída do trabalho**
    - **Log de trabalhos**  
    - **Log do Yarn**

    ![Captura de tela da janela exibir trabalhos do hive Ferramentas do Visual Studio do HDInsight](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-view-hive-jobs.png "Exibir trabalhos Hive")


## <a name="run-apache-pig-scripts"></a>Executar scripts do Pig do Apache

1. Na barra de menus, navegue até **arquivo** > **novo** > **projeto...** .

2. No painel esquerdo, navegue até **instalado** > **Azure data Lake** > **Pig (HDInsight)** .  

3. No painel central, selecione **aplicativo Pig**. Insira as propriedades e selecione **OK**.

4. Em **Gerenciador de soluções**, clique duas vezes em **script. Pig** para abrir o script.

## <a name="feedback-and-known-issues"></a>Comentários e problemas conhecidos
* Foi corrigido um problema no qual os resultados que são iniciados com valores nulos não são mostrados. Se você estiver bloqueado em relação a esse problema, contate a equipe de suporte.
* O script HQL criado pelo Visual Studio é codificado, dependendo da configuração de região local do usuário. O script não executa corretamente se você carrega o script em um cluster como um arquivo binário.

## <a name="next-steps"></a>Próximos passos
Neste artigo, você aprendeu a usar o pacote Ferramentas do Data Lake para Visual Studio para conectar-se aos clusters HDInsight pelo Visual Studio. Você também aprendeu a executar uma consulta do Hive. Para obter mais informações, consulte estes artigos:

* [Executar consultas do Apache Hive usando as ferramentas do Data Lake para Visual Studio](apache-hadoop-use-hive-visual-studio.md)
* [Usar o Hive do Hadoop no HDInsight](hdinsight-use-hive.md)
* [Introdução ao uso do Apache Hadoop no HDInsight](apache-hadoop-linux-tutorial-get-started.md)
* [Enviar trabalhos do Apache Hadoop no HDInsight](submit-apache-hadoop-jobs-programmatically.md)
* [Analisar dados do Twitter com Apache Hadoop no HDInsight](../hdinsight-analyze-twitter-data.md)

