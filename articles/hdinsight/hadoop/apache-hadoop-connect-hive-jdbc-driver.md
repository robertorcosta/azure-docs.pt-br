---
title: Consultar Apache Hive por meio do driver JDBC - Azure HDInsight
description: Use o driver JDBC de um aplicativo Java para enviar consultas do Apache Hive ao Hadoop no HDInsight. Conecte-se de forma programática e do cliente SQL SQuirrel.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 8129239f152f6b359b930e56466052da12ef4d42
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437025"
---
# <a name="query-apache-hive-through-the-jdbc-driver-in-hdinsight"></a>Consultar o Apache Hive por meio do driver JDBC no HDInsight

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

Saiba como usar o driver JDBC de um aplicativo Java para enviar consultas do Apache Hive ao Apache Hadoop no Azure HDInsight. As informações neste documento demonstram como se conectar de forma programática e do cliente SQuirreL SQL.

Para obter mais informações sobre a Interface JDBC do Hive, consulte [HiveJDBCInterface](https://cwiki.apache.org/confluence/display/Hive/HiveJDBCInterface).

## <a name="prerequisites"></a>Pré-requisitos

* Cluster Hadoop do HDInsight. Para criar um, confira [Introdução ao Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md). Certifique-se de que o serviço HiveServer2 está sendo executado.
* O [Java Developer Kit (JDK) versão 11](https://www.oracle.com/technetwork/java/javase/downloads/jdk11-downloads-5066655.html) ou superior.
* [SQuirreL SQL](http://squirrel-sql.sourceforge.net/). O SQuirreL é um aplicativo cliente JDBC.

## <a name="jdbc-connection-string"></a>Cadeia de conexão JDBC

As conexões JDBC a um cluster HDInsight no Azure são feitas sobre a porta 443, e o tráfego é protegido usando TLS/SSL. O gateway público atrás dos quais ficam os clusters redireciona o tráfego para a porta que o HiveServer2 de fato está escutando. A cadeia de conexão a seguir mostra o formato a ser usado para o HDInsight:

    jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2

Substitua `CLUSTERNAME` pelo nome do cluster HDInsight.

Ou você pode obter a conexão através **da Ambari UI > Colmeia > Configs > Advanced**.

![Obtenha a seqüência de conexão JDBC através da Ambari](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-get-connection-string-through-ambari.png)

### <a name="host-name-in-connection-string"></a>Nome do host na seqüência de conexão

O nome do host 'CLUSTERNAME.azurehdinsight.net' na seqüência de conexões é o mesmo que a URL do cluster. Você pode obtê-lo através do portal Azure. 

### <a name="port-in-connection-string"></a>Porta na seqüência de conexão

Você só pode usar a **porta 443** para se conectar ao cluster a partir de alguns lugares fora da rede virtual Do Zure. HdInsight é um serviço gerenciado, o que significa que todas as conexões ao cluster são gerenciadas através de um Gateway seguro. Não é possível conectar-se ao HiveServer 2 diretamente nas portas 10001 ou 10000 porque essas portas não estão expostas ao exterior. 

## <a name="authentication"></a>Autenticação

Ao estabelecer a conexão, você deverá usar o nome e a senha do administrador do cluster HDInsight para se autenticar no gateway do cluster. Durante a conexão de clientes JDBC como o SQuirreL SQL, você deve inserir o nome e a senha do administrador nas configurações do cliente.

De um aplicativo Java, use o nome e a senha ao estabelecer uma conexão. Por exemplo, o código Java a seguir abre uma nova conexão usando a cadeia de conexão, o nome do administrador e a senha:

```java
DriverManager.getConnection(connectionString,clusterAdmin,clusterPassword);
```

## <a name="connect-with-squirrel-sql-client"></a>Conectar-se ao cliente do SQuirreL SQL

O SQuirreL SQL é um cliente JDBC que pode ser usado para executar remotamente as consultas do Hive com o cluster HDInsight. As etapas a seguir pressupõem que você já tenha instalado o SQuirreL SQL.

1. Crie um diretório para conter certos arquivos a serem copiados do seu cluster.

2. No script a `sshuser` seguir, substitua-o pelo nome da conta de usuário SSH para o cluster.  Substitua `CLUSTERNAME` pelo nome do cluster do HDInsight.  A partir de uma linha de comando, altere seu diretório de trabalho para o criado na etapa anterior e, em seguida, digite o seguinte comando para copiar arquivos de um cluster HDInsight:

    ```cmd
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hadoop-client/{hadoop-auth.jar,hadoop-common.jar,lib/log4j-*.jar,lib/slf4j-*.jar,lib/curator-*.jar} .

    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hive-client/lib/{commons-codec*.jar,commons-logging-*.jar,hive-*-*.jar,httpclient-*.jar,httpcore-*.jar,libfb*.jar,libthrift-*.jar} .
    ```

3. Inicie o aplicativo SQuirreL SQL. Na parte esquerda da janela, selecione **Drivers**.

    ![Guia Drivers à esquerda da janela](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-squirreldrivers.png)

4. Nos ícones na parte superior do diálogo **Drivers**, selecione o ícone **+** para criar um driver.

    ![Ícone dos drivers de aplicativos SQuirreL SQL](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-driversicons.png)

5. No diálogo Adicionar Driver, adicione as informações a seguir:

    |Propriedade | Valor |
    |---|---|
    |Nome|Hive|
    |Exemplo de URL|jdbc:hive2://localhost:443/default;transportMode=http;ssl=true;httpPath=/hive2|
    |Caminho de classe extra|Use o botão **Adicionar** para adicionar todos os arquivos de jar baixados anteriormente.|
    |Nome da Classe|org.apache.hive.jdbc.HiveDriver|

   ![adicionar diálogo de driver com parâmetros](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-add-driver.png)

   Selecione **OK** para salvar essas configurações.

6. À esquerda da janela do SQuirreL SQL, selecione **Aliases**. Em seguida, selecione o **+** ícone para criar um alias de conexão.

    ![SQuirreL SQL adicionar nova caixa de diálogo alias](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-new-aliases.png)

7. Use os seguintes valores para a caixa de diálogo **Adicionar alias:**

    |Propriedade |Valor |
    |---|---|
    |Nome|Hive no HDInsight|
    |Driver|Use o drop-down para selecionar o driver **Colmeia.**|
    |URL|jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2. Substitua **CLUSTERNAME** pelo nome do seu cluster HDInsight.|
    |Nome do Usuário|O nome da conta de logon do cluster para o cluster HDInsight. O padrão é **o admin**.|
    |Senha|A senha da conta de logon do cluster.|

    ![adicionar alias diálogo com parâmetros](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-addalias-dialog.png)

    > [!IMPORTANT]
    > Use o botão **Testar** para verificar se a conexão funciona. Quando o diálogo **Conectar a: Hive no HDInsight** for exibido, selecione **Conectar** para executar o teste. Se o teste tiver êxito, você verá um diálogo **Conexão bem-sucedida**. Se ocorrer um erro, consulte [Solução de problemas](#troubleshooting).

    Use o botão **Ok** na parte inferior do diálogo **Adicionar Alias** para salvar o alias de conexão.

8. Na lista suspensa **Conectar a**, na parte superior do SQuirreL SQL, selecione **Hive no HDInsight**. Quando solicitado, selecione **Conectar**.

    ![diálogo de conexão com parâmetros](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-connect-dialog.png)

9. Uma vez conectado, digite a seguinte consulta na caixa de diálogo consulta SQL e selecione o ícone **Executar** (uma pessoa em execução). A área de resultados deve mostrar os resultados da consulta.

    ```hql
    select * from hivesampletable limit 10;
    ```

    ![diálogo consulta sql, incluindo os resultados](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-sqlquery-dialog.png)

## <a name="connect-from-an-example-java-application"></a>Conectar-se de um aplicativo Java de exemplo

Um exemplo de usar um cliente Java para consultar [https://github.com/Azure-Samples/hdinsight-java-hive-jdbc](https://github.com/Azure-Samples/hdinsight-java-hive-jdbc)hive no HDInsight está disponível em . Siga as instruções no repositório para compilar e executar o exemplo.

## <a name="troubleshooting"></a>Solução de problemas

### <a name="unexpected-error-occurred-attempting-to-open-an-sql-connection"></a>Ocorreu um erro inesperado ao tentar abrir uma conexão SQL

**Sintomas**: ao se conectar a um cluster HDInsight versão 3.3 ou superior, você poderá receber uma mensagem indicando que ocorreu um erro inesperado. O rastreamento de pilha para esse erro começa com as seguintes linhas:

```java
java.util.concurrent.ExecutionException: java.lang.RuntimeException: java.lang.NoSuchMethodError: org.apache.commons.codec.binary.Base64.<init>(I)V
at java.util.concurrent.FutureTas...(FutureTask.java:122)
at java.util.concurrent.FutureTask.get(FutureTask.java:206)
```

**Causa**: este erro é causado por um arquivo commons-codec.jar de versão mais antiga incluído com o SQuirreL.

**Resolução:** Para corrigir este erro, use as seguintes etapas:

1. Saia do SQuirreL e, em seguida, vá para o diretório onde `C:\Program Files\squirrel-sql-4.0.0\lib`o SQuirreL está instalado em seu sistema, talvez . No diretório do SquirreL, sob o diretório `lib` , substitua o commons-codec.jar existente pelo baixado por meio do cluster HDInsight.

1. Reinicie o SQuirreL. O erro não deverá ocorrer mais nas próximas conexões ao Hive no HDInsight.

### <a name="connection-disconnected-by-hdinsight"></a>Conexão desconectada pelo HDInsight

**Sintomas**: Ao tentar baixar uma enorme quantidade de dados (digamos vários GBs) através do JDBC/ODBC, a conexão é desconectada pelo HDInsight inesperadamente durante o download. 

**Causa**: Este erro é causado pela limitação nos nós do Gateway. Ao obter dados do JDBC/ODBC, todos os dados precisam passar pelo nó Gateway. No entanto, um gateway não foi projetado para baixar uma enorme quantidade de dados, de modo que a conexão pode ser fechada pelo Gateway se ele não puder lidar com o tráfego.

**Resolução**: Evite usar o driver JDBC/ODBC para baixar grandes quantidades de dados. Em vez disso, copie os dados diretamente do armazenamento blob.


## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a usar o JDBC para trabalhar com a Hive, use os seguintes links para explorar outras maneiras de trabalhar com o Azure HDInsight.

* [Visualize os dados da Colmeia Apache com o Microsoft Power BI no Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Visualize dados da Colmeia de Consulta Interativa com power BI no Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Use o Apache Zeppelin para executar consultas da Colmeia Apache no Azure HDInsight](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Conectar o Excel ao HDInsight com o Driver ODBC do Microsoft Hive](apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Conecte o Excel ao Apache Hadoop usando o Power Query](apache-hadoop-connect-excel-power-query.md).
* [Conecte-se ao Azure HDInsight e execute consultas do Apache Hive usando o Data Lake Tools para Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Use a Ferramenta do Azure HDInsight para Visual Studio Code](../hdinsight-for-vscode.md).
* [Carregar dados para hdinsight](../hdinsight-upload-data.md)
* [Usar o Apache Hive com o HDInsight](hdinsight-use-hive.md)
* [Usar o Apache Pig com o HDInsight](hdinsight-use-pig.md)
* [Usar trabalhos do MapReduce com o HDInsight](hdinsight-use-mapreduce.md)
