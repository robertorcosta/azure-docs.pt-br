---
title: Notebooks Zeppelin & grupo Apache Spark - Azure HDInsight
description: Instruções passo a passo sobre como usar notebooks Zeppelin com clusters Apache Spark no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/18/2020
ms.openlocfilehash: e313048986beca1991e38ce2e65ea12f954170d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77598265"
---
# <a name="use-apache-zeppelin-notebooks-with-apache-spark-cluster-on-azure-hdinsight"></a>Use os cadernos Apache Zeppelin com o cluster do Apache Spark no HDInsight do Azure

Os clusters do HDInsight Spark incluem os [blocos de anotações do Apache Zeppelin](https://zeppelin.apache.org/) que podem ser usados para executar os trabalhos [do Apache Spark](https://spark.apache.org/). Neste artigo, você aprenderá a usar o notebook Zeppelin em um cluster HDInsight.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do Apache Spark no HDInsight. Para obter instruções, consulte o artigo sobre como [Criar clusters do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* O esquema de URI do seu armazenamento primário de clusters. Isso seria `wasb://` para o Azure `abfs://` Blob Storage, para o `adl://` Azure Data Lake Storage Gen2 ou para o Azure Data Lake Storage Gen1. Se a transferência segura estiver ativada para `wasbs://`o Blob Storage, o URI será .  Para obter mais informações, consulte [Exigir transferência segura no Armazenamento Azure](../../storage/common/storage-require-secure-transfer.md) .

## <a name="launch-an-apache-zeppelin-notebook"></a>Inicie um notebook do Apache Zeppelin

1. Na visão **geral**do cluster Spark, selecione **o notebook Zeppelin** nos **painéis do Cluster**. Digite as credenciais de administrador para o cluster.  

   > [!NOTE]  
   > Você também pode acessar o Bloco de Notas Zeppelin de seu cluster abrindo a seguinte URL no navegador. Substitua **CLUSTERNAME** pelo nome do cluster:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/zeppelin`

2. Crie um novo bloco de anotações. Do painel de cabeçalho, navegue **até notebook** > **Criar uma nova nota**.

    ![Criar um novo bloco de anotações do Zeppelin](./media/apache-spark-zeppelin-notebook/hdinsight-create-zeppelin-notebook.png "Criar um novo bloco de anotações do Zeppelin")

    Digite um nome para o notebook e selecione **Criar nota**.

3. Certifique-se de que o cabeçalho do notebook mostra um status conectado. É denotado por um ponto verde no canto superior direito.

    ![Status do bloco de anotações do Zeppelin](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-connected.png "Status do bloco de anotações do Zeppelin")

4. Carregar dados de exemplo em uma tabela temporária. Quando você cria um cluster Spark no HDInsight, o arquivo de dados de amostra, `hvac.csv`é copiado para a conta de armazenamento associada em `\HdiSamples\SensorSampleData\hvac`.

    No parágrafo vazio criado por padrão no novo bloco de anotações, cole o snippet a seguir.

    ```scala
    %livy2.spark
    //The above magic instructs Zeppelin to use the Livy Scala interpreter

    // Create an RDD using the default Spark context, sc
    val hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

    // Define a schema
    case class Hvac(date: String, time: String, targettemp: Integer, actualtemp: Integer, buildingID: String)

    // Map the values in the .csv file to the schema
    val hvac = hvacText.map(s => s.split(",")).filter(s => s(0) != "Date").map(
        s => Hvac(s(0),
                s(1),
                s(2).toInt,
                s(3).toInt,
                s(6)
        )
    ).toDF()

    // Register as a temporary table called "hvac"
    hvac.registerTempTable("hvac")
    ```

    Pressione **SHIFT + ENTER** ou selecione o botão **Reproduzir** para que o parágrafo execute o trecho. O status no canto direito do parágrafo deve progredir de PRONTO, PENDENTE, EM EXCECUÇÃO para CONCLUÍDO. A saída é exibida na parte inferior do mesmo parágrafo. A captura de tela é semelhante ao seguinte:

    ![Criar uma tabela temporária por meio de dados brutos](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-load-data.png "Criar uma tabela temporária por meio de dados brutos")

    Você também pode fornecer um título para cada parágrafo. No canto direito do parágrafo, selecione o ícone **Configurações** (sprocket) e selecione **Mostrar título**.  

    > [!NOTE]  
    > O intérprete% spark2 não é suportado nos portáteis Zeppelin em todas as versões do HDInsight, e o intérprete% sh não será suportado pelo HDInsight 4.0 em diante.

5. Agora você pode executar as instruções Spark SQL na `hvac` mesa. Cole a seguinte consulta em um novo parágrafo. A consulta recupera a ID do prédio e a diferença entre as temperaturas almejada e real para cada prédio em uma determinada data. Pressione **SHIFT + ENTER**.

    ```sql
    %sql
    select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13"
    ```  

    A instrução **%sql** no início informa ao bloco de anotações para usar o interpretador Scala Livy.

6. Selecione o ícone **Gráfico de** barras para alterar o visor.  **as configurações**, que aparece após a escolha **do Gráfico de Barras,** permitem que você escolha **Chaves**e **Valores**.  A captura de tela a seguir mostra o resultado.

    ![Execute uma declaração Spark SQL usando o notebook1](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-1.png "Execute uma declaração Spark SQL usando o notebook1")

7. Você também pode executar instruções Spark SQL usando variáveis na consulta. O próximo trecho mostra como definir `Temp`uma variável, na consulta com os possíveis valores que você deseja consultar. Quando você executa a consulta pela primeira vez, uma lista suspensa é preenchida automaticamente com os valores especificados para a variável.

    ```sql
    %sql  
    select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff from hvac where targettemp > "${Temp = 65,65|75|85}"
    ```

    Cole esse snippet em um novo parágrafo e pressione **SHIFT+ENTER**. Em seguida, selecione **65** na lista de estiletes **temp.**

8. Selecione o ícone **Gráfico de** barras para alterar o visor.  Em seguida, selecione **as configurações** e faça as seguintes alterações:

   * **Grupos:**  Adicionar **targettemp**.  
   * **Valores:** 1. Remova **a data**.  2. Adicione **temp_diff**.  3.  Mude o agregador de **SUM** para **AVG**.  

     A captura de tela a seguir mostra o resultado.

     ![Execute uma declaração Spark SQL usando o notebook2](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-2.png "Execute uma declaração Spark SQL usando o notebook2")

## <a name="how-do-i-use-external-packages-with-the-notebook"></a>Como usar pacotes externos com o notebook?

Você pode configurar o notebook Zeppelin no cluster Apache Spark no HDInsight para usar pacotes externos que contribuíam com a comunidade que não estão incluídos fora da caixa no cluster. Você pode pesquisar o [Repositório do Maven](https://search.maven.org/) para obter uma lista de pacotes que estão disponíveis. Você também pode obter uma lista de pacotes disponíveis de outras fontes. Por exemplo, uma lista completa dos pacotes enviados pela comunidade está disponível em [Pacotes do Spark](https://spark-packages.org/).

Neste artigo, você verá como usar o pacote [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) com o notebook Jupyter.

1. Abra as configurações do interpretador. No canto superior direito, selecione o nome de usuário logado e selecione **Intérprete**.

    ![Iniciar o interpretador](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Saída do Hive")

2. Role para **livy2,** depois selecione **editar**.

    ![Alterar configurações do intérprete1](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-1.png "Alterar configurações do intérprete1")

3. Navegue `livy.spark.jars.packages`até a tecla e `group:id:version`defina seu valor no formato . Dessa forma, se você quiser usar o pacote [spark csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar), deverá definir o valor da chave como `com.databricks:spark-csv_2.10:1.4.0`.

    ![Alterar configurações do intérprete2](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-2.png "Alterar configurações do intérprete2")

    Selecione **Salvar** e, em seguida, **OK** para reiniciar o intérprete DeLivy.

4. se quiser saber como chegar ao valor da chave inserida acima, confira como.

    a. Localize o pacote no Repositório Maven. Para este artigo, usamos [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar).

    b. No repositório, colete os valores para **GroupId**, **ArtifactId** e **Version**.

    ![Usar pacotes externos com o bloco de notas Jupyter](./media/apache-spark-zeppelin-notebook/use-external-packages-with-jupyter.png "Usar pacotes externos com o bloco de notas Jupyter")

    c. Concatene os três valores, separados por dois pontos (**:**).

        com.databricks:spark-csv_2.10:1.4.0

## <a name="where-are-the-zeppelin-notebooks-saved"></a>Onde os notebooks Zeppelin são salvos?

Os notebooks Zeppelin são salvos nos nós de cabeçalho do cluster. Portanto, se você excluir o cluster, os notebooks também serão excluídos. Se você quiser preservar os notebooks para uso posterior em outros clusters, deverá exportá-los depois de concluir os trabalhos em execução. Para exportar um notebook, selecione o ícone **Exportar** como mostrado na imagem abaixo.

![Baixar o notebook](./media/apache-spark-zeppelin-notebook/zeppelin-download-notebook.png "Baixe o notebook")

Isso salva o notebook como um arquivo JSON em seu local de download.

## <a name="use-shiro-to-configure-access-to-zeppelin-interpreters-in-enterprise-security-package-esp-clusters"></a>Use o Shiro para configurar o acesso aos intérpretes Zeppelin em clusters esp (Enterprise Security Package, pacote de segurança corporativa)
Como observado acima, o `%sh` intérprete não é suportado a partir do HDInsight 4.0 em diante. Além disso, `%sh` uma vez que o intérprete introduz possíveis problemas de segurança, como teclas de acesso usando comandos shell, ele também foi removido dos clusters HDInsight 3.6 ESP. Isso `%sh` significa que o intérprete não está disponível ao clicar em **Criar nova nota** ou na UI intérprete por padrão. 

Usuários de domínio privilegiado `Shiro.ini` podem utilizar o arquivo para controlar o acesso à UI intérprete. Assim, somente esses usuários `%sh` podem criar novos intérpretes `%sh` e definir permissões em cada novo intérprete. Para controlar o `shiro.ini` acesso usando o arquivo, use as seguintes etapas:

1. Defina uma nova função usando um nome de grupo de domínio existente. No exemplo a `adminGroupName` seguir, está um grupo de usuários privilegiados no AAD. Não use caracteres especiais ou espaços brancos no nome do grupo. Os personagens depois `=` dão as permissões para este papel. `*`significa que o grupo tem permissões completas.

    ```
    [roles]
    adminGroupName = *
    ```

2. Adicione o novo papel para acesso aos intérpretes do Zeppelin. No exemplo a seguir, `adminGroupName` todos os usuários têm acesso aos intérpretes zeppelin e são capazes de criar novos intérpretes. Você pode colocar várias funções entre os suportes, `roles[]`separadas por commas. Em seguida, os usuários que tiverem as permissões necessárias, podem acessar os intérpretes do Zeppelin.

    ```
    [urls]
    /api/interpreter/** = authc, roles[adminGroupName]
    ```

## <a name="livy-session-management"></a>Gerenciamento de sessões do Livy

Quando você executa o primeiro parágrafo de código no notebook Zeppelin, uma nova sessão do Livy é criada em seu cluster HDInsight Spark. Essa sessão será compartilhada entre todos os notebooks Zeppelin que você criar posteriormente. Se por algum motivo a sessão livy for morta (reinicialização de cluster, e assim por diante), você não será capaz de executar trabalhos a partir do notebook Zeppelin.

Nesse caso, você deverá executar as etapas a seguir antes de iniciar a execução de trabalhos de um notebook do Zeppelin.  

1. Reinicie o interpretador Livy no notebook Zeppelin. Para isso, abra as configurações do intérprete selecionando o nome de usuário logado no canto superior direito e selecione **Intérprete**.

    ![Iniciar o interpretador](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Saída do Hive")

2. Role para **livy2,** em seguida, selecione **reiniciar**.

    ![Reinicie o intérprete de Livy](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "Reinicie o intérprete do Zeppelin")

3. Execute uma célula de código de um notebook Zeppelin existente. Isso cria uma nova sessão do Livy no cluster HDInsight.

## <a name="general-information"></a>Informações gerais

### <a name="validate-service"></a>Validar o serviço

Para validar o serviço do `https://CLUSTERNAME.azurehdinsight.net/#/main/services/ZEPPELIN/summary` Ambari, navegue até onde CLUSTERNAME é o nome do seu cluster.

Para validar o serviço de uma linha de comando, SSH para o nó principal. Mude o usuário `sudo su zeppelin`para o zeppelin usando o comando . Comandos de status:

|Comando |Descrição |
|---|---|
|`/usr/hdp/current/zeppelin-server/bin/zeppelin-daemon.sh status`|Status de serviço.|
|`/usr/hdp/current/zeppelin-server/bin/zeppelin-daemon.sh --version`|Versão de serviço.|
|`ps -aux | grep zeppelin`|Identifique o PID.|

### <a name="log-locations"></a>Localizações de log

|Serviço |Caminho |
|---|---|
|zeppelin-servidor|/usr/hdp/current/zeppelin-server/|
|Logs do servidor|/var/log/zeppelin|
|Intérprete de Configuração, Shiro, site.xml, log4j|/usr/hdp/current/zeppelin-server/conf ou /etc/zeppelin/conf|
|Diretório PID|/var/run/zeppelin|

### <a name="enable-debug-logging"></a>Habilitar o log de depuração

1. Navegue `https://CLUSTERNAME.azurehdinsight.net/#/main/services/ZEPPELIN/summary` até onde CLUSTERNAME é o nome do seu cluster.

1. Navegue até **CONFIGS** > **Avançado zeppelin-log4j-properties** > **log4j_properties_content**.

1. Modificar `log4j.appender.dailyfile.Threshold = INFO` `log4j.appender.dailyfile.Threshold = DEBUG`para .

1. Adicione `log4j.logger.org.apache.zeppelin.realm=DEBUG`.

1. Salvar alterações e reiniciar o serviço.

## <a name="next-steps"></a>Próximas etapas

[Visão geral: Apache Spark no Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Cenários

* [Apache Spark com BI: execute análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](apache-spark-use-bi-tools.md)
* [Apache Spark com Machine Learning: use o Spark no HDInsight para analisar a temperatura do edifício usando dados de HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark com Machine Learning: use o Spark no HDInsight para prever os resultados da inspeção de alimentos](apache-spark-machine-learning-mllib-ipython.md)
* [Análise de log do site usando o Apache Spark no HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicativos

* [Criar um aplicativo autônomo usando Scala](apache-spark-create-standalone-application.md)
* [Execute trabalhos remotamente em um cluster do Apache Spark usando o Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões

* [Use o Plugin do HDInsight Tools para o IntelliJ IDEA criar e enviar os aplicativos Scala do Apache Spark](apache-spark-intellij-tool-plugin.md)
* [Use o Plugin do HDInsight Tools para o IntelliJ IDEA para depurar os aplicativos do Apache Spark remotamente](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Kernels disponíveis para o notebook Jupyter no cluster do Apache Spark para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Usar pacotes externos com blocos de notas Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter em seu computador e conectar-se a um cluster Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerenciar recursos

* [Gerenciar os recursos de cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Rastrear e depurar trabalhos em execução em um cluster do Apache Spark no HDInsight](apache-spark-job-debugging.md)
