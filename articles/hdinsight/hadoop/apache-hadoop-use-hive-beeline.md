---
title: Usar Apache Beeline com Apache Hive - Azure HDInsight
description: Aprenda a usar o cliente Beeline para executar consultas Hive com Hadoop no HDInsight. Beeline é um utilitário para trabalhar com HiveServer2 sobre JDBC.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/17/2020
ms.openlocfilehash: 2396207c88716420d299382006a270eb747ddc03
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82192656"
---
# <a name="use-the-apache-beeline-client-with-apache-hive"></a>Usar o cliente Apache Beeline com Apache Hive

Saiba como usar o [Apache Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) para executar consultas do Apache Hive no HDInsight.

O Beeline é um cliente Hive que está incluído em nós principais do cluster HDInsight. Para instalar o beeline localmente, consulte [instalar o cliente beeline](#install-beeline-client), abaixo. O Beeline usa o JDBC para se conectar ao HiveServer2, um serviço hospedado em seu cluster HDInsight. Você também pode usar o Beeline para acessar remotamente o Hive no HDInsight pela internet. Os exemplos a seguir fornecem as cadeias de conexão mais comuns usadas para se conectar ao HDInsight do beeline.

## <a name="types-of-connections"></a>Tipos de conexões

### <a name="from-an-ssh-session"></a>De uma sessão SSH

Ao conectar-se de uma sessão SSH a um cabeçalho de cluster, você pode conectar `headnodehost` -se ao `10001`endereço na porta:

```bash
beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
```

---

### <a name="over-an-azure-virtual-network"></a>Em uma rede virtual do Azure

Ao conectar-se de um cliente ao HDInsight em uma rede virtual do Azure, você deve fornecer o FQDN (nome de domínio totalmente qualificado) de um nó de cabeçalho do cluster. Desde que essa conexão seja feita diretamente para os nós de cluster, a conexão usa a porta `10001`:

```bash
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

Substitua `<headnode-FQDN>` pelo nome de domínio totalmente qualificado de um cabeçalho de cluster. Para localizar o nome de domínio totalmente qualificado de um nó principal, use as informações do documento [Gerenciar HDInsight usando a API de REST do Apache Ambari](../hdinsight-hadoop-manage-ambari-rest-api.md#get-the-fqdn-of-cluster-nodes).

---

### <a name="to-hdinsight-enterprise-security-package-esp-cluster-using-kerberos"></a>Para o cluster Enterprise Security Package do HDInsight (ESP) usando Kerberos

Ao conectar-se de um cliente a um cluster Enterprise Security Package (ESP) ingressado no Azure Active Directory (AAD) – DS em um computador no mesmo realm do cluster, você também deve especificar `<AAD-Domain>` o nome de domínio e o nome de uma conta de usuário de domínio `<username>`com permissões para acessar o cluster:

```bash
kinit <username>
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/default;principal=hive/_HOST@<AAD-Domain>;auth-kerberos;transportMode=http' -n <username>
```

Substitua `<username>` pelo nome de uma conta no domínio com permissões para acessar o cluster. Substituir `<AAD-DOMAIN>` pelo nome do Azure Active Directory (AAD) ao qual o cluster está associado. Use uma cadeia de caracteres em `<AAD-DOMAIN>` maiúsculas para o valor, caso contrário, a credencial não será encontrada. Verifique `/etc/krb5.conf` os nomes de territórios, se necessário.

Para localizar a URL JDBC de Ambari:

1. Em um navegador da Web, navegue `https://CLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/summary`até, `CLUSTERNAME` em que é o nome do cluster. Verifique se o HiveServer2 está em execução.

1. Use a área de transferência para copiar a URL JDBC do HiveServer2.

---

### <a name="over-public-or-private-endpoints"></a>Em pontos de extremidade públicos ou privados

Ao se conectar a um cluster usando os pontos de extremidade públicos ou privados, você deve fornecer o nome da conta de logon `admin`do cluster (padrão) e a senha. Por exemplo, usando Beeline de um sistema de cliente para conectar-se para o `clustername.azurehdinsight.net` endereço. Essa conexão é feita pela porta `443`e é criptografada usando TLS/SSL.

Substitua `clustername` pelo nome do cluster HDInsight. Substitua `admin` pela conta de logon do cluster de seu cluster. Para clusters ESP, use o UPN completo (por exemplo, user@domain.com). Substitua `password` pela senha da conta de logon do cluster.

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p 'password'
```

ou para o ponto de extremidade privado:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p 'password'
```

Pontos de extremidade privados apontam para um balanceador de carga básico, que só pode ser acessado por meio do VNETs emparelhado na mesma região. Consulte [restrições em emparelhamento VNet global e balanceadores de carga](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) para obter mais informações. Você pode usar o `curl` comando com `-v` a opção para solucionar problemas de conectividade com pontos de extremidade públicos ou privados antes de usar o beeline.

---

### <a name="use-beeline-with-apache-spark"></a>Usar Beeline com Apache Spark

O Apache Spark fornece sua própria implementação de HiveServer2, que, às vezes, é referenciado como o servidor Spark Thrift. Esse serviço usa o Spark SQL para resolver consultas em vez de Hive. E pode fornecer melhor desempenho dependendo da sua consulta.

#### <a name="through-public-or-private-endpoints"></a>Por meio de pontos de extremidade públicos ou privados

A cadeia de conexão usada é ligeiramente diferente. Em vez de `httpPath=/hive2` conter uso `httpPath/sparkhive2`. Substitua `clustername` pelo nome do cluster HDInsight. Substitua `admin` pela conta de logon do cluster de seu cluster. Para clusters ESP, use o UPN completo (por exemplo, user@domain.com). Substitua `password` pela senha da conta de logon do cluster.

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p 'password'
```

ou para o ponto de extremidade privado:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p 'password'
```

Pontos de extremidade privados apontam para um balanceador de carga básico, que só pode ser acessado por meio do VNETs emparelhado na mesma região. Consulte [restrições em emparelhamento VNet global e balanceadores de carga](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) para obter mais informações. Você pode usar o `curl` comando com `-v` a opção para solucionar problemas de conectividade com pontos de extremidade públicos ou privados antes de usar o beeline.

---

#### <a name="from-cluster-head-or-inside-azure-virtual-network-with-apache-spark"></a>Do cabeçalho do cluster ou dentro da rede virtual do Azure com Apache Spark

Ao conectar diretamente do nó principal do cluster ou de um recurso dentro da mesma Rede Virtual do Azure que o cluster HDInsight, a porta `10002` deve ser usada para o servidor do Spark Thrift em vez de `10001`. O exemplo a seguir mostra como se conectar diretamente ao nó principal:

```bash
/usr/hdp/current/spark2-client/bin/beeline -u 'jdbc:hive2://headnodehost:10002/;transportMode=http'
```

---

## <a name="prerequisites-for-examples"></a>Pré-requisitos para exemplos

* Um cluster Hadoop no HDInsight. Consulte [introdução ao HDInsight no Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Observe o esquema de URI para o armazenamento primário do seu cluster. Por exemplo, `wasb://` para o armazenamento do `abfs://` Azure, por Azure data Lake Storage Gen2 `adl://` ou para Azure data Lake Storage Gen1. Se a transferência segura estiver habilitada para o armazenamento do Azure `wasbs://`, o URI será. Para obter mais informações, consulte [transferência segura](../../storage/common/storage-require-secure-transfer.md).

* Opção 1: um cliente SSH. Para saber mais, confira [Conectar-se ao HDInsight (Apache Hadoop) usando SSH](../hdinsight-hadoop-linux-use-ssh-unix.md). A maioria das etapas neste documento pressupõe que você esteja usando o beeline de uma sessão SSH para o cluster.

* Opção 2: um cliente beeline local.

## <a name="run-a-hive-query"></a>Executar um trabalho do Hive

Este exemplo se baseia no uso do cliente beeline de uma conexão SSH.

1. Abra uma conexão SSH para o cluster com o código a seguir. Substitua `sshuser` pelo usuário do SSH do cluster e substitua `CLUSTERNAME` pelo nome do cluster. Quando solicitado, insira a senha para a conta de usuário SSH.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Conecte-se ao HiveServer2 com o cliente do beeline de sua sessão SSH aberta inserindo o seguinte comando:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

3. Os comandos Beeline normalmente começam com um caractere `!`, por exemplo, `!help` exibe a ajuda. No entanto, o `!` pode ser omitido para alguns comandos. Por exemplo, `help` também funciona.

    Há `!sql`, que é usado para executar instruções HiveQL. No entanto, o HiveQL é tão usado que é possível omitir o `!sql`anterior. As duas instruções a seguir são equivalentes:

    ```hiveql
    !sql show tables;
    show tables;
    ```

    Em um novo cluster, somente uma tabela é listada: **hivesampletable**.

4. Use o comando a seguir para exibir o esquema para a hivesampletable:

    ```hiveql
    describe hivesampletable;
    ```

    Esse comando retorna as informações a seguir:

        +-----------------------+------------+----------+--+
        |       col_name        | data_type  | comment  |
        +-----------------------+------------+----------+--+
        | clientid              | string     |          |
        | querytime             | string     |          |
        | market                | string     |          |
        | deviceplatform        | string     |          |
        | devicemake            | string     |          |
        | devicemodel           | string     |          |
        | state                 | string     |          |
        | country               | string     |          |
        | querydwelltime        | double     |          |
        | sessionid             | bigint     |          |
        | sessionpagevieworder  | bigint     |          |
        +-----------------------+------------+----------+--+

    Essas informações descrevem as colunas na tabela.

5. Insira as instruções a seguir para criar uma tabela chamada **log4jLogs** usando os dados de exemplo fornecidos com o cluster HDInsight: (revisar conforme necessário com base no seu esquema de URI).

    ```hiveql
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (
        t1 string,
        t2 string,
        t3 string,
        t4 string,
        t5 string,
        t6 string,
        t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs
        WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log'
        GROUP BY t4;
    ```

    Essas instruções realizam as seguintes ações:

    |de |Descrição |
    |---|---|
    |DROP TABLE|Se a tabela existir, ela será excluída.|
    |CREATE EXTERNAL TABLE|Cria uma tabela **externa** no hive. Tabelas externas só armazenam a definição da tabela no Hive. Os dados são mantidos no local original.|
    |FORMATO DE LINHA|Como os dados são formatados. Nesse caso, os campos em cada log são separados por um espaço.|
    |ARMAZENADO COMO LOCAL DO TEXTFILE|Onde os dados são armazenados e em qual formato de arquivo.|
    |SELECT|Seleciona uma contagem de todas as linhas em que a coluna **T4** contém o valor **[ERROR]**. Essa consulta deve retornar um valor de **3**, já que existem três linhas que contêm esse valor.|
    |INPUT__FILE__NAME como '%. log '|O hive tenta aplicar o esquema a todos os arquivos no diretório. Nesse caso, o diretório contém arquivos que não correspondem ao esquema. Para evitar dados incorretos nos resultados, essa instrução informa ao Hive que deve retornar apenas dados de arquivos que terminam em .log.|

   > [!NOTE]  
   > As tabelas externas devem ser usadas quando você espera que os dados subjacentes sejam atualizados por uma fonte externa. Por exemplo, um processo de upload de dados automatizado ou uma operação MapReduce.
   >
   > Remover uma tabela externa **não** exclui os dados, somente a definição de tabela.

    A saída desse comando é semelhante ao texto a seguir:

        INFO  : Tez session hasn't been created yet. Opening session
        INFO  :

        INFO  : Status: Running (Executing on YARN cluster with App id application_1443698635933_0001)

        INFO  : Map 1: -/-      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
        INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
        INFO  : Map 1: 1/1      Reducer 2: 0/1
        INFO  : Map 1: 1/1      Reducer 2: 0(+1)/1
        INFO  : Map 1: 1/1      Reducer 2: 1/1
        +----------+--------+--+
        |   sev    | count  |
        +----------+--------+--+
        | [ERROR]  | 3      |
        +----------+--------+--+
        1 row selected (47.351 seconds)

6. Sair do beeline:

    ```bash
    !exit
    ```

## <a name="run-a-hiveql-file"></a>Executar um arquivo HiveQL

Este exemplo é uma continuação do exemplo anterior. Use as etapas a seguir para criar um arquivo e  executá-lo usando o Beeline.

1. Use o comando a seguir para criar um novo arquivo chamado **query.hql**:

    ```bash
    nano query.hql
    ```

1. Use o texto a seguir como conteúdo do arquivo. Essa consulta cria uma nova tabela 'interna' chamada **errorLogs**:

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Essas instruções realizam as seguintes ações:

    |de |Descrição |
    |---|---|
    |CREATE TABLE SE NÃO EXISTIR|Se a tabela ainda não existir, ela será criada. Como a palavra-chave **external** não é usada, essa instrução cria uma tabela interna. As tabelas internas são armazenadas no data warehouse do Hive e totalmente gerenciadas por ele.|
    |ARMAZENADO COMO ORC|Armazena os dados no formato OCR (Optimized Row Columnar). O formato ORC é altamente otimizado e eficiente para o armazenamento de dados do Hive.|
    |INSERIR SUBSTITUIÇÃO... Não|Seleciona linhas da tabela **log4jLogs** que contêm **[ERROR]** e insere os dados na tabela **errorLogs**.|

    > [!NOTE]  
    > Diferentemente de tabelas externas, o descarte de uma tabela interna excluirá também os dados subjacentes.

1. Para salvar o arquivo, use **Ctrl**+**X**, em seguida, digite **Y**e, por fim, **Enter**.

1. Use o seguinte para executar o arquivo usando Beeline:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i query.hql
    ```

    > [!NOTE]  
    > O parâmetro `-i` inicia o Beeline e executa as instruções no arquivo `query.hql`. Quando a consulta for concluída, você verá um prompt `jdbc:hive2://headnodehost:10001/>`. Você também pode executar um arquivo usando o parâmetro `-f`, que fechará o Beeline após a conclusão da consulta.

1. Para verificar se a tabela **errorLogs** foi criada, use a seguinte instrução para retornar todas as linhas de **errorLogs**:

    ```hiveql
    SELECT * from errorLogs;
    ```

    Três linhas de dados devem ser devolvidas, todas contendo **[ERROR]** na coluna t4:

        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | errorlogs.t1  | errorlogs.t2  | errorlogs.t3  | errorlogs.t4  | errorlogs.t5  | errorlogs.t6  | errorlogs.t7  |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | 2012-02-03    | 18:35:34      | SampleClass0  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 18:55:54      | SampleClass1  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 19:25:27      | SampleClass4  | [ERROR]       | incorrect     | id            |               |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        3 rows selected (0.813 seconds)

## <a name="install-beeline-client"></a>Instalar o cliente beeline

Embora o beeline esteja incluído nos nós de cabeçalho, talvez você queira instalá-lo localmente.  As etapas de instalação para um computador local são baseadas em um [subsistema do Windows para Linux](https://docs.microsoft.com/windows/wsl/install-win10).

1. Atualizar listas de pacotes. Digite o seguinte comando em seu shell bash:

    ```bash
    sudo apt-get update
    ```

1. Instale o Java se não estiver instalado. Você pode verificar com o `which java` comando.

    1. Se nenhum pacote Java estiver instalado, digite o seguinte comando:

        ```bash
        sudo apt install openjdk-11-jre-headless
        ```

    1. Abra o arquivo bashrc (geralmente encontrado em ~/.bashrc): `nano ~/.bashrc`.

    1. Modifique o arquivo bashrc. Adicione a seguinte linha ao final do arquivo:

        ```bash
        export JAVA_HOME=/usr/lib/jvm/java-1.11.0-openjdk-amd64
        ```

        Em seguida, pressione **Ctrl + X**, em seguida, **Y**e, em seguida, Enter.

1. Baixe os arquivos mortos Hadoop e beeline, insira os seguintes comandos:

    ```bash
    wget https://archive.apache.org/dist/hadoop/core/hadoop-2.7.3/hadoop-2.7.3.tar.gz
    wget https://archive.apache.org/dist/hive/hive-1.2.1/apache-hive-1.2.1-bin.tar.gz
    ```

1. Descompacte os arquivos mortos, insira os seguintes comandos:

    ```bash
    tar -xvzf hadoop-2.7.3.tar.gz
    tar -xvzf apache-hive-1.2.1-bin.tar.gz
    ```

1. Modifique ainda mais o arquivo bashrc. Você precisará identificar o caminho para onde os arquivos mortos foram desempacotados. Se você estiver usando o [subsistema do Windows para Linux](https://docs.microsoft.com/windows/wsl/install-win10)e seguiu as etapas exatamente, o caminho será `/mnt/c/Users/user/`, onde `user` é seu nome de usuário.

    1. Abra o arquivo:`nano ~/.bashrc`

    1. Modifique os comandos abaixo com o caminho apropriado e insira-os no final do arquivo bashrc:

        ```bash
        export HADOOP_HOME=/path_where_the_archives_were_unpacked/hadoop-2.7.3
        export HIVE_HOME=/path_where_the_archives_were_unpacked/apache-hive-1.2.1-bin
        PATH=$PATH:$HIVE_HOME/bin
        ```

    1. Em seguida, pressione **Ctrl + X**, em seguida, **Y**e, em seguida, Enter.

1. Feche e reabra a sessão de bash.

1. Teste sua conexão. Use o formato de conexão de [pontos de extremidade públicos ou privados](#over-public-or-private-endpoints)acima.

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações gerais sobre o hive no HDInsight, consulte [usar Apache Hive com Apache Hadoop no HDInsight](hdinsight-use-hive.md)

* Para obter mais informações sobre outras maneiras que você pode trabalhar com o Hadoop no HDInsight, consulte [usar o MapReduce com o Apache Hadoop no hdinsight](hdinsight-use-mapreduce.md)
