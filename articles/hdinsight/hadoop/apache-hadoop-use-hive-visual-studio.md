---
title: Apache Hive & Data Lake Tools for Visual Studio – Azure HDInsight
description: Saiba como usar as ferramentas de Data Lake para Visual Studio para executar consultas do Apache Hive com Apache Hadoop no Azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: hrasheed
ms.openlocfilehash: 37652a8ca9750e6b33bd2744bda386eaba92b025
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044523"
---
# <a name="run-apache-hive-queries-using-the-data-lake-tools-for-visual-studio"></a>Executar consultas do Apache Hive usando as ferramentas do Data Lake para Visual Studio

Saiba como usar as ferramentas do Data Lake para Visual Studio para consultar o Apache Hive. As ferramentas de Data Lake permitem que você facilmente crie, envie e monitore consultas de Hive para Apache Hadoop no Azure HDInsight.

## <a id="prereq"></a>Pré-requisitos

* Um cluster do Apache Hadoop no HDInsight. Consulte [Introdução ao HDInsight no Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Visual Studio (uma das versões a seguir):

    * Visual Studio 2015, 2017 (qualquer edição)
    * Visual Studio 2013 Community/Professional/Premium/Ultimate com Atualização 4

* Ferramentas do HDInsight para Visual Studio ou ferramentas do Azure Data Lake para Visual Studio. Confira [Começar a usar as ferramentas Hadoop para HDInsight do Visual Studio](apache-hadoop-visual-studio-tools-get-started.md) para obter informações sobre como instalar e configurar as ferramentas.

## <a id="run"></a> Executar consultas Apache Hive usando o Visual Studio

Você tem duas opções para criar e executar consultas do Hive:

* Criar consultas ad hoc
* Criar um aplicativo Hive

### <a name="ad-hoc"></a>Ad hoc

Consultas ad hoc podem ser executadas no modo de **lote** ou **interativo** .

1. Abra o **Visual Studio**.

2. Em **Gerenciador de servidores**, navegue até **Azure** > **HDInsight**.

3. Expanda o **HDInsight**e clique com o botão direito do mouse no cluster em que você deseja executar a consulta e, em seguida, selecione **gravar uma consulta do hive**.

4. Insira a seguinte consulta de Hive:

    ```hql
    SELECT * FROM hivesampletable;
    ```

5. Selecione **Executar**. Observe que o modo de execução é padronizado como **interativo**.

    ![Captura de tela de Executar consultas interativas do Hive](./media/apache-hadoop-use-hive-visual-studio/vs-execute-hive-query.png)

6. Para executar a mesma consulta no modo de **lote** , alterne a lista suspensa de **interativo** para **lote**. Observe que o botão de execução muda de **executar** para **Enviar**.

    ![Captura de tela do envio de uma consulta do Hive](./media/apache-hadoop-use-hive-visual-studio/visual-studio-batch-query.png)

    O editor do Hive é compatível com o IntelliSense. Agora as Ferramentas do Data Lake para Visual Studio dão suporte à obtenção de metadados remotos quando você edita o script do Hive. Por exemplo, se você digitar `SELECT * FROM`, o IntelliSense listará todos os nomes de tabela sugeridos. Quando um nome de tabela for especificado, o IntelliSense listará os nomes de coluna. As ferramentas dão suporte a quase todas as instruções DML Hive, subconsultas e UDFs internos. O IntelliSense sugere apenas os metadados dos clusters selecionados na Barra de Ferramentas do HDInsight.

    ![Captura de tela de um exemplo do HDInsight Ferramentas do Visual Studio IntelliSense 1](./media/apache-hadoop-use-hive-visual-studio/vs-intellisense-table-name.png "IntelliSense U-SQL")
   
    ![Captura de tela de um exemplo do HDInsight Ferramentas do Visual Studio IntelliSense 2](./media/apache-hadoop-use-hive-visual-studio/vs-intellisense-column-name.png "IntelliSense U-SQL")

7. Selecione **Enviar** ou **Enviar (Avançado)** .

   Se você selecionar a opção de envio avançado, configure **Nome do Trabalho**, **Argumentos**, **Configurações Adicionais** e **Diretório de Status** para o script:

    ![Captura de tela de uma consulta de Hive Hadoop do HDInsight](./media/apache-hadoop-use-hive-visual-studio/vs-tools-submit-jobs-advanced.png "Enviar consultas")

### <a name="hive-application"></a>Aplicativo do hive

1. Abra o **Visual Studio**.

2. Na barra de menus, navegue até **arquivo** > **novo** **projeto**de > .

3. Na janela **novo projeto** , navegue até **modelos** > **Azure data Lake** > **Hive (HDInsight)**  > **aplicativo Hive**. 

4. Forneça um nome para este projeto e, em seguida, selecione **OK**.

5. Abra o arquivo **Script.hql** criado com esse projeto e cole as seguintes instruções HiveQL:

    ```hiveql
    set hive.execution.engine=tez;
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND  INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
    ```

    As instruções executam as seguintes ações:

   * `DROP TABLE`: se a tabela existir, esta instrução a excluirá.

   * `CREATE EXTERNAL TABLE`: cria uma nova tabela 'externa' no Hive. As tabelas externas armazenam apenas a definição da tabela no Hive (os dados são mantidos no local original).

     > [!NOTE]  
     > As tabelas externas devem ser usadas quando você espera que os dados subjacentes sejam atualizados por uma fonte externa. Por exemplo, um trabalho de MapReduce ou serviço do Azure.
     >
     > Remover uma tabela externa **não** exclui os dados, somente a definição de tabela.

   * `ROW FORMAT`: informa ao Hive como os dados são formatados. Nesse caso, os campos em cada log são separados por um espaço.

   * `STORED AS TEXTFILE LOCATION`: informa ao Hive o local em que os dados são armazenados no diretório de exemplo/de dados e que eles estão armazenados como texto.

   * `SELECT`: seleciona uma contagem de todas as linhas, nas quais a coluna `t4` contém o valor `[ERROR]`. Essa instrução retorna um valor de `3`, já que há três linhas que contêm esse valor.

   * `INPUT__FILE__NAME LIKE '%.log'`: informa ao Hive que só devemos retornar dados de arquivos que terminam em .log. Essa cláusula restringe a pesquisa para o arquivo sample.log que contém os dados.

6. Na barra de ferramentas, selecione o **Cluster HDInsight** que você deseja usar nessa consulta. Selecione **Enviar** para executar as instruções como um trabalho do Hive.

   ![Envio da barra de ferramentas do Azure HDInsight](./media/apache-hadoop-use-hive-visual-studio/hdinsight-toolbar-submit.png)

7. O **Resumo do Trabalho do Hive** aparecerá e exibirá informações sobre o trabalho em execução. Use o link **Atualizar** para atualizar as informações do trabalho, até o **Status do Trabalho** ser alterado para **Concluído**.

   ![resumo do trabalho exibindo um trabalho concluído](./media/apache-hadoop-use-hive-visual-studio/hdinsight-job-summary.png)

8. Use o link **Saída de Trabalho** para exibir a saída desse trabalho. Ele exibe `[ERROR] 3`, que é o valor retornado por essa consulta.

### <a name="additional-example"></a>Exemplo adicional

Este exemplo se baseia na tabela de `log4jLogs` criada na etapa anterior.

1. Em **Gerenciador de servidores**, clique com o botão direito do mouse no cluster e selecione **gravar uma consulta do hive**.

2. Insira a seguinte consulta de Hive:

    ```hql
    set hive.execution.engine=tez;
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    As instruções executam as seguintes ações:

    * `CREATE TABLE IF NOT EXISTS`: cria uma tabela, se ela ainda não existir. Uma vez que a palavra-chave `EXTERNAL` não é usada, essa instrução cria uma tabela interna. As tabelas internas são armazenadas no data warehouse do Hive e gerenciadas por ele.
    
    > [!NOTE]  
    > Ao contrário das tabelas `EXTERNAL`, o descarte de uma tabela interna excluirá também os dados subjacentes.

    * `STORED AS ORC`: armazena os dados no formato ORC (Optimized Row Columnar). Esse é um formato altamente otimizado e eficiente para o armazenamento de dados do Hive.
    
    * `INSERT OVERWRITE ... SELECT`: seleciona linhas da tabela `log4jLogs` que contêm `[ERROR]` e, então, insere os dados na tabela `errorLogs`.

3. Execute a consulta no modo de **lote** .

4. Para verificar se o trabalho criou a tabela, use o **Gerenciador de Servidores** e expanda **Azure** > **HDInsight** > seu cluster HDInsight > **Bancos de Dados do Hive** > **padrão**. As tabelas **errorLogs** e **log4jLogs** são listadas.

## <a id="nextsteps"></a>Próximas etapas

Como você pode ver, as ferramentas do HDInsight para o Visual Studio fornecem uma maneira fácil de trabalhar com as consultas do Hive no HDInsight.

Para obter informações gerais sobre o Hive no HDInsight:

* [Use o Apache Hive com o Apache Hadoop no HDInsight](hdinsight-use-hive.md)

Para obter informações sobre outros modos possíveis de trabalhar com Hadoop no HDInsight:

* [Use o Apache Pig com o Apache Hadoop no HDInsight](hdinsight-use-pig.md)

* [Usar MapReduce com Apache Hadoop no HDInsight](hdinsight-use-mapreduce.md)

Para obter mais informações sobre as ferramentas do HDInsight para o Visual Studio:

* [Introdução às ferramentas do HDInsight para Visual Studio](apache-hadoop-visual-studio-tools-get-started.md)