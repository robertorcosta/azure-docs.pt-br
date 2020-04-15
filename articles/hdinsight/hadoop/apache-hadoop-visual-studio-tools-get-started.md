---
title: Apache Hadoop & Ferramentas do Visual Studio Data Lake - Azure HDInsight
description: Saiba como instalar e usar o Data Lake Tools para o Visual Studio. Use a ferramenta para se conectar aos clusters Apache Hadoop no Azure HDInsight e, em seguida, execute consultas de Colmeia.
keywords: ferramentas do hadoop, consulta hive, visual studio, hadoop do visual studio
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: 7504826f267d717f30c5e88621578412c744e5f9
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383516"
---
# <a name="use-data-lake-tools-for-visual-studio-to-connect-to-azure-hdinsight-and-run-apache-hive-queries"></a>Use Data Lake Tools para Visual Studio para se conectar ao Azure HDInsight e executar consultas do Apache Hive

Aprenda a usar as ferramentas Microsoft Azure Data Lake e Stream Analytics para o Visual Studio (Data Lake Tools). Use a ferramenta para se conectar aos [clusters Apache Hadoop no Azure HDInsight](apache-hadoop-introduction.md) e envie consultas de Colmeia.  

Para obter mais informações sobre como usar o HDInsight, consulte [Get started with HDInsight](apache-hadoop-linux-tutorial-get-started.md).  

Para obter mais informações sobre como se conectar à Tempestade Apache, consulte [Desenvolver topologias C# para apache storm usando as ferramentas data lake](../storm/apache-storm-develop-csharp-visual-studio-topology.md).

As Ferramentas do Data Lake para Visual Studio podem ser usadas para acessar o Data Lake Analytics e o HDInsight. Para obter informações sobre as Ferramentas do Data Lake, confira [Desenvolver scripts U-SQL usando as Ferramentas do Data Lake para Visual Studio](../../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo e usar o Data Lake Tools para o Visual Studio, você precisa dos seguintes itens:

* Um cluster Azure HDInsight. Para criar um cluster Azure HDInsight, consulte [Introdução ao uso do Apache Hadoop no Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md). Para executar consultas do Hive interativas, é necessário ter um cluster de [Consulta interativa do HDInsight](../interactive-query/apache-interactive-query-get-started.md).  

* [Estúdio Visual](https://visualstudio.microsoft.com/downloads/). A [edição do Visual Studio Community](https://visualstudio.microsoft.com/vs/community/) é gratuita. As instruções aqui mostradas são para [o Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).

## <a name="install-data-lake-tools-for-visual-studio"></a>Instalar Ferramentas do Data Lake para Visual Studio  

Siga as instruções apropriadas para instalar o Data Lake Tools para a sua versão do Visual Studio:

* Para visual studio 2017 ou Visual Studio 2019:

    Durante a instalação do Visual Studio, certifique-se de incluir a carga de trabalho de desenvolvimento do **Azure** ou a carga de trabalho de **armazenamento e processamento de dados.**  

    Para as instalações existentes do Visual Studio, vá até a barra de menus do IDE e selecione **Ferramentas** > **Obter Ferramentas e Recursos** para abrir o Visual Studio Installer. Na guia **Cargas de trabalho,** selecione pelo menos a carga de trabalho **de desenvolvimento do Azure** (em **Web & Cloud**). Ou selecione a carga de trabalho de **armazenamento e processamento de dados** (em **Outras ferramentas ).**

  ![Seleção de carga de trabalho, Instalador do Estúdio Visual](./media/apache-hadoop-visual-studio-tools-get-started/vs-installation.png)

* Para visual studio 2015:

    [Baixar Data Lake Tools](https://www.microsoft.com/download/details.aspx?id=49504). Escolha a versão das Ferramentas do Data Lake que correspondem à versão do Visual Studio.

## <a name="update-data-lake-tools-for-visual-studio"></a>Atualize as ferramentas do Lago de Dados para o Visual Studio  

Em seguida, certifique-se de atualizar Data Lake Tools para a versão mais recente.

1. Abra o Visual Studio.

2. Na janela **Iniciar,** **selecione Continuar sem código**.

3. Na barra de menus do Visual Studio IDE, escolha **Extensões** > **Gerenciar extensões**.

4. Na caixa de diálogo **Gerenciar extensões,** expanda o nó **Atualizações.**

5. Se a lista de atualizações disponíveis incluir **a Azure Data Lake e as Ferramentas de Análise de Fluxo,** selecione-a. Em seguida, selecione o botão **Atualizar.** Depois **que** a caixa de diálogo Download and Install aparece e desaparece, o Visual Studio adiciona a extensão **Azure Data Lake e Stream Analytic Tools** ao cronograma de atualização.

6. Feche todas as janelas do Visual Studio. A caixa de diálogo **VSIX Installer** é exibida.

7. Selecione **Licença** para ler os termos da licença e selecione **Fechar** para retornar à caixa de diálogo **Do instalador VSIX.**

8. Selecione **Modificar**. A instalação da atualização de extensão começa. Depois de um tempo, a caixa de diálogo muda para mostrar que terminou de fazer modificações. Selecione **Fechar**e, em seguida, reinicie o Visual Studio para concluir a instalação.

> [!NOTE]  
> Você pode usar somente as Ferramentas do Data Lake versão 2.3.0.0 ou posterior para se conectar aos clusters de Consulta Interativa e executar consultas interativas Hive.

## <a name="connect-to-azure-subscriptions"></a>Conectar-se a assinaturas do Azure

Você pode usar o Data Lake Tools for Visual Studio para se conectar aos seus clusters HDInsight, fazer algumas operações básicas de gerenciamento e executar consultas de Colméia.

> [!NOTE]  
> Para obter informações sobre como se conectar a um cluster hadoop genérico, consulte [Como escrever e enviar consultas de Colmeia usando o Visual Studio](https://blogs.msdn.microsoft.com/xiaoyong/2015/05/04/how-to-write-and-submit-hive-queries-using-visual-studio/).

### <a name="connect-to-an-azure-subscription"></a>Conectar-se a uma assinatura do Azure

Para conectar-se à sua assinatura do Azure:

1. Abra o Visual Studio.

2. Na janela **Iniciar,** **selecione Continuar sem código**.

3. Na barra de menu si, escolha **Exibir** > **o Explorador do Servidor**.

4. No **Server Explorer,** clique com o botão direito do mouse **no Azure,** **selecione Conectar à assinatura do Microsoft Azure**e complete o processo de autenticação. Do **Server Explorer,** **expanda o Azure** > **HDInsight** para visualizar uma lista de clusters HDInsight existentes.

5. Se você não tiver nenhum cluster, crie um usando o portal Azure, o Azure PowerShell ou o HDInsight SDK. Para obter mais informações, consulte [Configurar clusters no HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

   ![Lista de clusters HDInsight, Server Explorer, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-server-explorer.png)

6. Expanda um cluster do HDInsight. O cluster contém nós para **bancos de dados de colmeias**. Além disso, uma conta de armazenamento padrão, quaisquer contas de armazenamento vinculadas adicionais e **o Hadoop Service Log**. Você pode expandir mais as entidades.

Depois de se conectar à assinatura do Azure, você poderá fazer as tarefas a seguir.

### <a name="connect-to-azure-from-visual-studio"></a>Conecte-se ao Azure do Visual Studio

Conectar-se ao portal do Azure pelo Visual Studio:

1. No **Server Explorer,** **expanda o Azure** > **HDInsight** e selecione seu cluster.

2. Clique com o botão direito do mouse em um cluster HDInsight e selecione **Gerenciar cluster no portal Azure**.

### <a name="offer-questions-and-feedback-from-visual-studio"></a>Ofereça perguntas e feedback do Visual Studio

Para fazer perguntas e, ou fornecer feedback do Visual Studio:

1. No Server Explorer, escolha **O Azure** > **HDInsight**.

2. Clique com o botão direito do **mouse no HDInsight** e selecione o **Fórum MSDN** para fazer perguntas ou **Dê feedback** para dar feedback.

## <a name="link-to-or-edit-a-cluster"></a>Link para ou editar um cluster

> [!NOTE]
> Atualmente, o único tipo de cluster HDInsight que você pode vincular é um tipo de Colmeia.

Para vincular um cluster HDInsight:

1. Clique com o botão direito do mouse **no HDInsight**e selecione **Link a HDInsight Cluster** para exibir a caixa de diálogo Link a **HDInsight Cluster.**

2. Digite uma URL `https://CLUSTERNAME.azurehdinsight.net`de **conexão** no formulário . O **Nome do Cluster** preenche automaticamente com a parte de nome do cluster da sua URL quando você vai para outro campo. Em seguida, digite um **nome de usuário** e **senha**e selecione **Next**.

    ![Link a cluster, HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-dialog.png)

3. Selecione **Concluir**. Se o link de cluster for bem-sucedido, o cluster será listado no nó **HDInsight.**

Para atualizar um cluster vinculado, clique com o botão direito do mouse no cluster e selecione **Editar**. Em seguida, você pode atualizar as informações do cluster.

![Edite um cluster vinculado, HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-update.png)

## <a name="explore-linked-resources"></a>Explorar recursos vinculados

No Gerenciador de Servidores, pode-se visualizar a conta de armazenamento padrão e quaisquer contas de armazenamento vinculadas. Se você expandir a conta de armazenamento padrão, visualizará os contêineres na conta de armazenamento. Tanto a conta de armazenamento padrão como o contêiner padrão estão marcados.

![Data Lake Tools for Visual Studio recursos vinculados no Server Explorer](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-linked-resources.png)

Clique com o botão direito do mouse em um contêiner e **selecione Exibir contêiner** para visualizar o conteúdo do contêiner. Depois de abrir um contêiner, você pode usar os botões da barra de ferramentas para **atualizar** a lista de conteúdo, **Carregar Blob,** **Excluir blobs selecionados,** **Abrir Blob**e baixar **(Save As)** blobs selecionados.

![Lista de contêineres e operações blob, cluster HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-blob-operations.png)

## <a name="run-interactive-apache-hive-queries"></a>Executar consultas interativas do Apache Hive

O [Apache Hive](https://hive.apache.org) é uma infraestrutura do data warehouse criada no Hadoop. O Hive é usado para análise, consultas e resumo de dados. Você pode usar as Ferramentas do Data Lake para Visual Studio a fim de executar consultas Hive pelo Visual Studio. Para obter mais informações sobre a Colmeia, consulte [O que é colmeia apache e hiveQL no Azure HDInsight?](hdinsight-use-hive.md).

[Consulta Interativa no Azure HDInsight](../interactive-query/apache-interactive-query-get-started.md) usa [colmeia no LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) na Colmeia Apache 2.1. A Consulta Interativa traz interatividade para consultas complexas no estilo data warehouse em conjuntos de dados grandes e armazenados. A execução de consultas de Colmeia em Consulta Interativa é muito mais rápida do que os trabalhos tradicionais em lote da Colmeia. 

> [!NOTE]  
> Você só pode executar consultas interativas do Hive quando se conecta a um cluster de [Consulta Interativa HDInsight](../interactive-query/apache-interactive-query-get-started.md).

Você também pode usar data lake tools para visual studio para ver o que está dentro de um trabalho colmeia. As Ferramentas do Data Lake para Visual Studio coletam e revelam logs Yarn de determinados trabalhos do Hive.

No **Server Explorer,** escolha **O Zure** > **HDInsight** e selecione seu cluster.  Este nó é o ponto de partida no **Server Explorer** para as seções a seguir.

### <a name="view-hivesampletable"></a>Exibir hivesampletable

Todos os clusters HDInsight têm uma `hivesampletable`tabela colmeia de amostra padrão chamada .  

A partir do seu cluster, escolha **os bancos de dados colmeias** > **padrão** > **.**

* Para ver `hivesampletable` o esquema:

    Expandir **a colmeia .** Os nomes e tipos `hivesampletable` de dados das colunas são mostrados.

* Para visualizar `hivesampletable` os dados:

    Clique com o botão direito do mouse e **selecione** **Exibir as 100 linhas principais**. A lista de 100 resultados aparece na **tabela Hive: hivesampletable** window. Esta ação equivale a executar a seguinte consulta da Colmeia usando o driver Hive ODBC:

    `SELECT * FROM hivesampletable LIMIT 100`

    Você pode personalizar a contagem de linhas alterando **número de linhas;** você pode escolher 50, 100, 200 ou 1000 linhas da lista de paradas.

### <a name="create-hive-tables"></a>Criar tabelas Hive

Você pode usar a GUI ou usar consultas Hive para criar uma tabela Hive. Para obter informações sobre o uso de consultas hive, consulte [Criar e executar consultas de Colmeia](#create-and-run-hive-queries).

1. A partir do seu cluster, escolha o > **padrão**Dos **bancos de dados hive**.

2. Clique com o botão direito do mouse **padrão**e selecione **Criar tabela**.

3. Configure a tabela.

4. Selecione o botão **Criar tabela** para enviar o trabalho, que cria a nova tabela Colmeia.

    ![Criar janela de mesa, hive, cluster HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-create-hive-table.png)

### <a name="create-and-run-hive-queries"></a>Criar e executar consultas de Colmeia

Você tem duas opções para criar e executar consultas do Hive:

* Criar consultas locais
* Criar um aplicativo Hive

#### <a name="create-an-ad-hoc-query"></a>Crie uma consulta ad-hoc

Para criar e executar uma consulta ad-hoc:

1. Clique com o botão direito do mouse no cluster onde deseja executar a consulta e selecione **Write a Hive Query**.  

2. Digite uma consulta de Colméia.

    O editor do Hive é compatível com o IntelliSense. Agora as Ferramentas do Data Lake para Visual Studio dão suporte à obtenção de metadados remotos quando você edita o script do Hive. Por exemplo, se `SELECT * FROM`você digitar, o IntelliSense lista todos os nomes de tabela sugeridos. Quando um nome de tabela for especificado, o IntelliSense listará os nomes de coluna. As ferramentas dão suporte a quase todas as instruções DML Hive, subconsultas e UDFs internos.

    ![Exemplo do IntelliSense 1, consulta ad-hoc da Hive, cluster HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-table-names.png)

    ![Exemplo intelliSense 2, consulta ad-hoc hive, cluster HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-column-names.png)

    > [!NOTE]  
    > O IntelliSense sugere apenas os metadados dos clusters selecionados na Barra de Ferramentas do HDInsight.

    Aqui está uma consulta de exemplo que você pode usar:

    ```sql
    SELECT devicemodel, COUNT(devicemodel) AS deviceCount
    FROM hivesampletable
    GROUP BY devicemodel
    ORDER BY devicemodel
    ```

3. Escolha o modo de execução:

    * **Interativo**  

        Na primeira lista de paradas, escolha **'Interativo'** e selecione **Executar**.

        ![Modo interativo, consulta ad-hoc hive, cluster HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-query-execute.png)  

    * **Lote**  

        Na primeira lista de paradas, escolha **'Lote'** e, em seguida, **selecione Enviar**. Ou selecione o ícone de saque ao lado **de Enviar** e escolher **Avançado**.

        ![Modo em lote, consulta ad-hoc hive, cluster HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-query-batch.png)

        Se você selecionar a opção de envio avançado, a caixa de diálogo **Enviar script** será exibida. Configure o **nome do trabalho,** **argumentos,** **configurações adicionais**e **diretório de status** para o script.

        ![Enviar caixa de diálogo script, consulta ad-hoc hive, cluster HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-submit-jobs-advanced.png)

      > [!NOTE]  
      > Você não pode enviar lotes para clusters de consulta interativa.  Você deve usar o modo interativo.

#### <a name="create-a-hive-application"></a>Criar um aplicativo Hive

Para criar e executar uma solução de Hive:

1. Na barra de menu, escolha **Arquivo** > **Novo** > **Projeto**.

2. Na **Criação de uma nova** janela de projeto, selecione a caixa de pesquisa e **digite Hive**. Em seguida, escolha **Aplicativo Colmeia** e selecione **Next**.

3. Na **configuração** da nova janela do projeto, digite um **nome de projeto,** selecione ou crie o local **do**projeto e selecione **Criar**.

    ![Novo aplicativo hive, configure sua nova janela de projeto, HDInsight Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-new-hive-project.png)

4. No **Gerenciador de Soluções**, clique duas vezes em **Script.hql** para abri-lo.

### <a name="view-job-summary-and-output"></a>Ver resumo do trabalho e saída

O resumo do trabalho varia ligeiramente entre o modo **Batch** e **Interactive.**

![Janelas de resumo de trabalho colmeias, lote e modo interativo, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-job-summary.png)

Use o ícone **Atualizar** para atualizar o status até que o status do trabalho seja alterado para **Concluído**.  

* Para obter os detalhes do trabalho no modo **Lote,** selecione os links na parte inferior para ver a **consulta de emprego,** **saída de trabalho**ou registro de **trabalho**ou para **exibir logs de fio**.

* Para obter os detalhes do trabalho do modo **Interativo,** consulte os painéis **de saída** e **saída hiveServer2.**

    ![Saída de trabalho interativa hive, cluster HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-job-details.png)

### <a name="view-job-graph"></a>Ver gráfico de emprego

Atualmente, os gráficos de trabalho são mostrados apenas para trabalhos de Colmeia que usam Tez como motor de execução.  Para obter informações sobre como ativar o Tez, consulte [O que é colmeia apache e hiveQL no Azure HDInsight?](hdinsight-use-hive.md).  Veja também, [Use Apache Tez em vez de Map Reduce](../hdinsight-hadoop-optimize-hive-query.md#use-apache-tez-instead-of-map-reduce).  

Para ver todos os operadores dentro do vértice, clique duas vezes nos vértices do gráfico de trabalho. Você também pode apontar para um operador específico e ver mais detalhes sobre o operador.

Mesmo que Tez seja especificado como o motor de execução, o gráfico de trabalho pode não aparecer se nenhum aplicativo Tez for lançado.  Essa situação pode ocorrer porque o trabalho não contém declarações de DML. Ou porque as instruções do DML podem retornar sem lançar um aplicativo Tez. Por exemplo, `SELECT * FROM table1` não será lançado o aplicativo Tez.

![Gráfico de trabalho apache hive, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-fast-path-hive-execution.png)

### <a name="view-task-execution-detail"></a>Ver detalhes da execução da tarefa

A partir do gráfico de trabalho, você pode selecionar **Detalhes de Execução de Tarefas** para obter informações estruturadas e visualizadas para trabalhos de Colmeia. Você também pode obter mais detalhes do trabalho. Se ocorrerem problemas de desempenho, você poderá usar o modo de exibição para obter mais detalhes sobre o problema. Por exemplo, você pode recuperar informações sobre como cada tarefa opera e informações detalhadas sobre cada tarefa (leitura/gravação de dados, cronograma/início/tempo de término e muito mais). Use as informações para ajustar as configurações de trabalho ou a arquitetura do sistema com base nas informações visualizadas.

![Janela de exibição de execução de tarefas, Ferramentas do Estúdio Visual do Lago de Dados](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-task-execution-view.png)

### <a name="view-hive-jobs"></a>Exibir trabalhos Hive

Você pode exibir consultas de trabalho, saída do trabalho, logs de trabalho e logs do Yarn para trabalhos do Hive.

No lançamento mais recente das ferramentas, você pode ver o que está dentro de seus trabalhos de Colmeia coletando e surgindo logs de fio. Um log do Yarn pode ajudar você a investigar problemas de desempenho. Para obter mais informações sobre como o HDInsight coleta logs de fios, consulte [os registros do aplicativo Access Apache Hadoop YARN](../hdinsight-hadoop-access-yarn-app-logs-linux.md).

Para exibir trabalhos do Hive:

1. Clique com o botão direito do mouse em um cluster HDInsight e selecione **Exibir trabalhos**.

    ![Ver Empregos, Colmeia Apache, cluster HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-view-hive-jobs.png)

    Uma lista dos trabalhos do Hive executados no cluster aparecerá.  

2. Selecione um trabalho. Na janela Resumo do **Trabalho de Colmeia,** selecione um dos seguintes links:
    - **Consulta do Trabalho**
    - **Saída de Emprego**
    - **Registro de empregos**  
    - **Log de fios**

## <a name="run-apache-pig-scripts"></a>Executar scripts do Pig do Apache

1. Na barra de menu, escolha **Arquivo** > **Novo** > **Projeto**.

2. Na janela **Iniciar,** selecione a caixa de pesquisa e insira **Porco**. Em seguida, selecione **Aplicativo de porco** e selecione **Next**.

3. Na **Configure sua nova** janela de projeto, digite um **nome do Projeto**e selecione ou crie um **Local** para o projeto. Em seguida, selecione **Criar**.

4. No painel **IDE Solution Explorer,** clique duas vezes em **Script.pig** para abrir o script.

## <a name="feedback-and-known-issues"></a>Comentários e problemas conhecidos

* Foi corrigido um problema no qual os resultados que são iniciados com valores nulos não são mostrados. Se você estiver bloqueado em relação a esse problema, contate a equipe de suporte.

* O script HQL que o Visual Studio cria é codificado, dependendo da configuração da região local do usuário. O script não executa corretamente se você carrega o script em um cluster como um arquivo binário.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a usar o pacote Ferramentas do Data Lake para Visual Studio para conectar-se aos clusters HDInsight pelo Visual Studio. Você também aprendeu a executar uma consulta do Hive. 

* [Executar consultas do Apache Hive usando as ferramentas do Data Lake para Visual Studio](apache-hadoop-use-hive-visual-studio.md)
* [Saiba mais sobre o Apache Hive e o HiveQL no Azure HDInsight?](hdinsight-use-hive.md)
* [Criar o cluster do Apache Hadoop – Modelo](apache-hadoop-linux-tutorial-get-started.md)
* [Enviar empregos Apache Hadoop no HDInsight](submit-apache-hadoop-jobs-programmatically.md)
* [Analisar dados do Twitter usando o Apache Hive e Apache Hadoop no HDInsight](../hdinsight-analyze-twitter-data-linux.md)
