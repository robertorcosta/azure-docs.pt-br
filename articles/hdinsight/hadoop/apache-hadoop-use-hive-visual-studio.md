---
title: Ferramentas do Apache Hive & Data Lake para o Visual Studio - Azure HDInsight
description: Saiba como usar as ferramentas de Data Lake para Visual Studio para executar consultas do Apache Hive com Apache Hadoop no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/27/2019
ms.openlocfilehash: 27ab13481525819eb1435f4c9ac256a21acd21fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74687798"
---
# <a name="run-apache-hive-queries-using-the-data-lake-tools-for-visual-studio"></a>Executar consultas do Apache Hive usando as ferramentas do Data Lake para Visual Studio

Saiba como usar as ferramentas do Data Lake para Visual Studio para consultar o Apache Hive. As ferramentas de Data Lake permitem que você facilmente crie, envie e monitore consultas de Hive para Apache Hadoop no Azure HDInsight.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do Apache Hadoop no HDInsight. Para obter informações sobre a criação deste item, consulte [Criar o cluster Apache Hadoop no Azure HDInsight usando o modelo Resource Manager](./apache-hadoop-linux-tutorial-get-started.md).

* [Estúdio Visual](https://visualstudio.microsoft.com/vs/). Os passos deste artigo utilizam o Visual Studio 2019.

* Ferramentas do HDInsight para Visual Studio ou ferramentas do Azure Data Lake para Visual Studio. Para obter informações sobre a instalação e configuração das ferramentas, consulte [Instalar ferramentas do Lago de Dados para o Visual Studio](apache-hadoop-visual-studio-tools-get-started.md#install-data-lake-tools-for-visual-studio).

## <a name="run-apache-hive-queries-using-the-visual-studio"></a> Executar consultas Apache Hive usando o Visual Studio

Você tem duas opções para criar e executar consultas do Hive:

* Crie consultas ad-hoc.
* Crie um aplicativo de colmeia.

### <a name="create-an-ad-hoc-hive-query"></a>Crie uma consulta de Colmeia ad-hoc

Consultas ad hoc podem ser executadas no modo **Batch** ou **Interactive.**

1. Inicie **o Visual Studio** e **selecione Continuar sem código**.

2. No **Server Explorer**, clique com o botão direito do mouse **Azure,** selecione **Conecte-se à Assinatura Do Microsoft Azure...** e complete o processo de login.

3. ExpandA **o HDInsight**, clique com o botão direito do mouse no cluster onde deseja executar a consulta e, em seguida, **selecione Escrever uma consulta de colmeia**.

4. Digite a seguinte consulta de colmeia:

    ```hql
    SELECT * FROM hivesampletable;
    ```

5. Selecione **Executar**. O modo de execução é padrão para **Interativo**.

    ![Execute consulta interativa da Colmeia, Visual Studio](./media/apache-hadoop-use-hive-visual-studio/vs-execute-hive-query.png)

6. Para executar a mesma consulta no modo **Lote,** alterne a lista de parada de **Interativa** para **Lote**. O botão de execução muda de **Execute** para **Submit**.

    ![Enviar consulta de colmeia em lote, Visual Studio](./media/apache-hadoop-use-hive-visual-studio/visual-studio-batch-query.png)

    O editor do Hive é compatível com o IntelliSense. Agora as Ferramentas do Data Lake para Visual Studio dão suporte à obtenção de metadados remotos quando você edita o script do Hive. Por exemplo, se `SELECT * FROM`você digitar, o IntelliSense lista todos os nomes de tabela sugeridos. Quando um nome de tabela for especificado, o IntelliSense listará os nomes de coluna. As ferramentas dão suporte a quase todas as instruções DML Hive, subconsultas e UDFs internos. O IntelliSense sugere apenas os metadados dos clusters selecionados na Barra de Ferramentas do HDInsight.

7. Na barra de ferramentas de consulta (a área abaixo da guia consulta e acima do texto de consulta), selecione **'Enviar'** ou selecione a seta pulldown ao lado de **Enviar** e escolha **Avançado** na lista de retirada. Se você selecionar a última opção,

8. Se você selecionou a opção de envio avançado, configure **Nome do trabalho,** **Argumentos,** **Configurações adicionais**e **Diretório de status** na caixa de diálogo Enviar **script.** Em seguida, selecione **Enviar**.

    ![Enviar caixa de diálogo script, consulta HDInsight Hadoop Hive](./media/apache-hadoop-use-hive-visual-studio/vs-tools-submit-jobs-advanced.png)

### <a name="create-a-hive-application"></a>Criar um aplicativo Hive

Para executar uma consulta da Colmeia criando um aplicativo hive, siga estas etapas:

1. Abra **o Visual Studio**.

2. Na janela **Iniciar**, selecione **Criar projeto**.

3. Na **Janela Criar uma nova** janela de projeto, na caixa Procurar **modelos,** digite *Colmeia*. Em seguida, escolha **Aplicativo Colmeia** e selecione **Next**.

4. Na **configuração da nova** janela de projeto, digite um **nome de projeto,** selecione ou crie um **Local** para o novo projeto e, em seguida, selecione **Criar**.

5. Abra o arquivo **Script.hql** criado com esse projeto e cole as seguintes instruções HiveQL:

    ```hql
    set hive.execution.engine=tez;
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND  INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
    ```

    Essas declarações fazem as seguintes ações:

    * `DROP TABLE`: Exclui a tabela se ela existir.

    * `CREATE EXTERNAL TABLE`: cria uma nova tabela 'externa' no Hive. Tabelas externas só armazenam a definição da tabela no Hive. (Os dados são deixados no local original.)

        > [!NOTE]  
        > As tabelas externas devem ser usadas quando você espera que os dados subjacentes sejam atualizados por uma fonte externa, como um trabalho MapReduce ou um serviço Azure.
        >
        > Remover uma tabela externa **não** exclui os dados, somente a definição de tabela.

    * `ROW FORMAT`: informa ao Hive como os dados são formatados. Nesse caso, os campos em cada log são separados por um espaço.

    * `STORED AS TEXTFILE LOCATION`: Diz à Colmeia que os dados são armazenados no *diretório de exemplo/dados* e que são armazenados como texto.

    * `SELECT`: Seleciona uma contagem de `t4` todas `[ERROR]`as linhas onde a coluna contém o valor . Esta declaração retorna `3`um valor de , porque três linhas contêm esse valor.

    * `INPUT__FILE__NAME LIKE '%.log'`: Diz a Hive para retornar apenas dados de arquivos que terminam em .log. Esta cláusula restringe a pesquisa ao arquivo *sample.log* que contém os dados.

6. Na barra de ferramentas do arquivo de consulta (que tem uma aparência semelhante à barra de ferramentas de consulta ad-hoc), selecione o cluster HDInsight que você deseja usar para esta consulta. Em seguida, altere **Interativo** para **Lote** (se necessário) e **selecione Enviar** para executar as instruções como um trabalho de Colmeia.

   O **Resumo do Trabalho do Hive** aparecerá e exibirá informações sobre o trabalho em execução. Use o link **Atualizar** para atualizar as informações do trabalho, até o **Status do Trabalho** ser alterado para **Concluído**.

   ![Resumo completo do trabalho colmeia, aplicação colmeia, Estúdio Visual](./media/apache-hadoop-use-hive-visual-studio/hdinsight-job-summary.png)

7. Selecione **Saída de trabalho** para visualizar a saída deste trabalho. Ele exibe `[ERROR] 3`, que é o valor retornado por essa consulta.

### <a name="additional-example"></a>Exemplo adicional

O exemplo a `log4jLogs` seguir se baseia na tabela criada no procedimento anterior, [Criar uma aplicação Colmeia](#create-a-hive-application).

1. No **Server Explorer,** clique com o botão direito do mouse no cluster e selecione **Write a Hive Query**.

2. Digite a seguinte consulta de colmeia:

    ```hql
    set hive.execution.engine=tez;
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Essas declarações fazem as seguintes ações:

    * `CREATE TABLE IF NOT EXISTS`: Cria uma tabela se ela ainda não existe. Como `EXTERNAL` a palavra-chave não é usada, esta afirmação cria uma tabela interna. As tabelas internas são armazenadas no data warehouse do Hive e gerenciadas por ele.

        > [!NOTE]  
        > Ao contrário das tabelas `EXTERNAL`, o descarte de uma tabela interna excluirá também os dados subjacentes.

    * `STORED AS ORC`: Armazena os dados em formato de colunar de *linha otimizada* (ORC). Esse é um formato altamente otimizado e eficiente para o armazenamento de dados do Hive.

    * `INSERT OVERWRITE ... SELECT`: seleciona linhas da tabela `log4jLogs` que contêm `[ERROR]` e, então, insere os dados na tabela `errorLogs`.

3. Alterar **interativo** para **lote,** se necessário, em seguida, **selecione Enviar**.

4. Para verificar se o trabalho criou a tabela, vá para o **Server Explorer** e **expanda o Azure** > **HDInsight**. Expanda seu cluster HDInsight e, em seguida, expanda > **o padrão**de **bancos de dados hive**. As tabelas **errorLogs** e **log4jLogs** são listadas.

## <a name="next-steps"></a>Próximas etapas

Como você pode ver, as ferramentas do HDInsight para o Visual Studio fornecem uma maneira fácil de trabalhar com as consultas do Hive no HDInsight.

* Para obter informações gerais sobre hive no HDInsight, veja [o que é Colmeia Apache e HiveQL no Azure HDInsight?](hdinsight-use-hive.md)

* Para obter informações sobre outras maneiras de trabalhar com Hadoop no HDInsight, consulte [Use MapReduce in Apache Hadoop no HDInsight](hdinsight-use-mapreduce.md)

* Para obter mais informações sobre as ferramentas HDInsight para o Visual Studio, consulte[Use Data Lake Tools for Visual Studio para se conectar ao Azure HDInsight e executar consultas apache hive](apache-hadoop-visual-studio-tools-get-started.md)
