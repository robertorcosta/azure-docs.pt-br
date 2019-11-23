---
title: Usar Apache Beeline com Apache Hive - Azure HDInsight
description: Aprenda a usar o cliente Beeline para executar consultas Hive com Hadoop no HDInsight. Beeline é um utilitário para trabalhar com HiveServer2 sobre JDBC.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: 26a166e61086af8cf10f761b608fcf66eb8734fd
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406259"
---
# <a name="use-the-apache-beeline-client-with-apache-hive"></a>Usar o cliente Apache Beeline com Apache Hive

Saiba como usar o [Apache Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) para executar consultas do Apache Hive no HDInsight.

O Beeline é um cliente Hive que está incluído em nós principais do cluster HDInsight. O Beeline usa o JDBC para se conectar ao HiveServer2, um serviço hospedado em seu cluster HDInsight. Você também pode usar o Beeline para acessar remotamente o Hive no HDInsight pela internet. Os exemplos a seguir fornecem as cadeias de caracteres de conexão mais comuns usadas para se conectar ao HDInsight do Beeline:

## <a name="types-of-connections"></a>Types of connections

### <a name="from-an-ssh-session"></a>From an SSH session

When connecting from an SSH session to a cluster headnode, you can then connect to the `headnodehost` address on port `10001`:

```bash
beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
```

---

### <a name="over-an-azure-virtual-network"></a>Over an Azure Virtual Network

When connecting from a client to HDInsight over an Azure Virtual Network, you must provide the fully qualified domain name (FQDN) of a cluster head node. Desde que essa conexão seja feita diretamente para os nós de cluster, a conexão usa a porta `10001`:

```bash
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

Replace `<headnode-FQDN>` with the fully qualified domain name of a cluster headnode. Para localizar o nome de domínio totalmente qualificado de um nó principal, use as informações do documento [Gerenciar HDInsight usando a API de REST do Apache Ambari](../hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes).

---

### <a name="to-hdinsight-enterprise-security-package-esp-cluster-using-kerberos"></a>To HDInsight Enterprise Security Package (ESP) cluster using Kerberos

When connecting from a client to an Enterprise Security Package (ESP) cluster joined to Azure Active Directory (AAD)-DS on a machine in same realm of the cluster, you must also specify the domain name `<AAD-Domain>` and the name of a domain user account with permissions to access the cluster `<username>`:

```bash
kinit <username>
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/default;principal=hive/_HOST@<AAD-Domain>;auth-kerberos;transportMode=http' -n <username>
```

Substitua `<username>` pelo nome de uma conta no domínio com permissões para acessar o cluster. Replace `<AAD-DOMAIN>` with the name of the Azure Active Directory (AAD) that the cluster is joined to. Use an uppercase string for the `<AAD-DOMAIN>` value, otherwise the credential won't be found. Check `/etc/krb5.conf` for the realm names if needed.

---

### <a name="over-public-or-private-endpoints"></a>Over public or private endpoints

When connecting to a cluster using the public or private endpoints, you must provide the cluster login account name (default `admin`) and password. Por exemplo, usando Beeline de um sistema de cliente para conectar-se para o `<clustername>.azurehdinsight.net` endereço. Essa conexão é feita pela porta `443`e é criptografada com SSL:

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n <username> -p password
```

or for private endpoint:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n <username> -p password
```

Substitua `clustername` pelo nome do cluster HDInsight. Substitua `<username>` pela conta de logon do cluster de seu cluster. For ESP clusters, use the full UPN (e.g. user@domain.com). Substitua `password` pela senha da conta de logon do cluster.

Private endpoints point to a basic load balancer, which can only be accessed from the VNETs peered in the same region. See [constraints on global VNet peering and load balancers](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) for more info. You can use the `curl` command with `-v` option to troubleshoot any connectivity problems with public or private endpoints before using beeline.

---

### <a id="sparksql"></a>Usar Beeline com Apache Spark

O Apache Spark fornece sua própria implementação de HiveServer2, que, às vezes, é referenciado como o servidor Spark Thrift. Esse serviço usa Spark SQL para resolver consultas em vez de Hive e pode fornecer um desempenho melhor, dependendo da consulta.

#### <a name="through-public-or-private-endpoints"></a>Through public or private endpoints

The connection string used  is slightly different. Instead of containing `httpPath=/hive2` it's `httpPath/sparkhive2`:

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n <username> -p password
```

or for private endpoint:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n <username> -p password
```

Substitua `clustername` pelo nome do cluster HDInsight. Substitua `<username>` pela conta de logon do cluster de seu cluster. For ESP clusters, use the full UPN (e.g. user@domain.com). Substitua `password` pela senha da conta de logon do cluster.

Private endpoints point to a basic load balancer, which can only be accessed from the VNETs peered in the same region. See [constraints on global VNet peering and load balancers](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) for more info. You can use the `curl` command with `-v` option to troubleshoot any connectivity problems with public or private endpoints before using beeline.

---

#### <a name="from-cluster-head-or-inside-azure-virtual-network-with-apache-spark"></a>From cluster head or inside Azure Virtual Network with Apache Spark

Ao conectar diretamente do nó principal do cluster ou de um recurso dentro da mesma Rede Virtual do Azure que o cluster HDInsight, a porta `10002` deve ser usada para o servidor do Spark Thrift em vez de `10001`. The following example shows how to connect directly to the head node:

```bash
/usr/hdp/current/spark2-client/bin/beeline -u 'jdbc:hive2://headnodehost:10002/;transportMode=http'
```

---

## <a id="prereq"></a>Pré-requisitos

* A Hadoop cluster on HDInsight. Consulte [Introdução ao HDInsight no Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Notice the [URI scheme](../hdinsight-hadoop-linux-information.md#URI-and-scheme) for your cluster's primary storage. For example,  `wasb://` for Azure Storage, `abfs://` for Azure Data Lake Storage Gen2, or `adl://` for Azure Data Lake Storage Gen1. If secure transfer is enabled for Azure Storage, the URI is `wasbs://`. For more information, see [secure transfer](../../storage/common/storage-require-secure-transfer.md).

* Option 1: An SSH client. Para saber mais, confira [Conectar-se ao HDInsight (Apache Hadoop) usando SSH](../hdinsight-hadoop-linux-use-ssh-unix.md). Most of the steps in this document assume that you're using Beeline from an SSH session to the cluster.

* Option 2:  A local Beeline client.

## <a id="beeline"></a>Executar um trabalho do Hive

This example is based on using the Beeline client from an SSH connection.

1. Open an SSH connection to the cluster with the code below. Substitua `sshuser` pelo usuário do SSH do cluster e substitua `CLUSTERNAME` pelo nome do cluster. When prompted, enter the password for the SSH user account.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Connect to HiveServer2 with your Beeline client from your open SSH session by entering the following command:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

3. Os comandos Beeline normalmente começam com um caractere `!`, por exemplo, `!help` exibe a ajuda. No entanto, o `!` pode ser omitido para alguns comandos. Por exemplo, `help` também funciona.

    There's `!sql`, which is used to execute HiveQL statements. No entanto, o HiveQL é tão usado que é possível omitir o `!sql`anterior. As duas instruções a seguir são equivalentes:

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

5. Enter the following statements to create a table named **log4jLogs** by using sample data provided with the HDInsight cluster: (Revise as needed based on your [URI scheme](../hdinsight-hadoop-linux-information.md#URI-and-scheme).)

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

    These statements do the following actions:

    * `DROP TABLE` - If the table exists, it's deleted.

    * `CREATE EXTERNAL TABLE` – Cria uma tabela **externa** no Hive. Tabelas externas só armazenam a definição da tabela no Hive. Os dados são mantidos no local original.

    * `ROW FORMAT` – o modo como os dados são formatados. Nesse caso, os campos em cada log são separados por um espaço.

    * `STORED AS TEXTFILE LOCATION` – O local em que os dados são armazenados e em qual formato de arquivo.

    * `SELECT` – Seleciona uma contagem de todas as linhas em que a coluna **t4** contém o valor **[ERROR]** . Essa consulta deve retornar um valor de **3**, já que existem três linhas que contêm esse valor.

    * `INPUT__FILE__NAME LIKE '%.log'`- O Hive tenta aplicar o esquema a todos os arquivos no diretório. In this case, the directory contains files that don't match the schema. Para evitar dados incorretos nos resultados, essa instrução informa ao Hive que deve retornar apenas dados de arquivos que terminam em .log.

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

6. Para sair do Beeline, use `!exit`.

## <a id="file"></a>Executar um arquivo HiveQL

This is a continuation from the prior example. Use as etapas a seguir para criar um arquivo e  executá-lo usando o Beeline.

1. Use o comando a seguir para criar um novo arquivo chamado **query.hql**:

    ```bash
    nano query.hql
    ```

2. Use o texto a seguir como conteúdo do arquivo. Essa consulta cria uma nova tabela 'interna' chamada **errorLogs**:

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    These statements do the following actions:

   * **CREATE TABLE IF NOT EXISTS** - If the table doesn't already exist, it's created. Since the **EXTERNAL** keyword isn't used, this statement creates an internal table. As tabelas internas são armazenadas no data warehouse do Hive e totalmente gerenciadas por ele.
   * **STORES AS ORC** : armazena os dados no formato ORC (Optimized Row Columnar). O formato ORC é altamente otimizado e eficiente para o armazenamento de dados do Hive.
   * **INSERT OVERWRITE ... SELECT** - Selects rows from the **log4jLogs** table that contain **[ERROR]** , then inserts the data into the **errorLogs** table.

    > [!NOTE]  
    > Diferentemente de tabelas externas, o descarte de uma tabela interna excluirá também os dados subjacentes.

3. To save the file, use **Ctrl**+**X**, then enter **Y**, and finally **Enter**.

4. Use o seguinte para executar o arquivo usando Beeline:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i query.hql
    ```

    > [!NOTE]  
    > O parâmetro `-i` inicia o Beeline e executa as instruções no arquivo `query.hql`. Quando a consulta for concluída, você verá um prompt `jdbc:hive2://headnodehost:10001/>`. Você também pode executar um arquivo usando o parâmetro `-f`, que fechará o Beeline após a conclusão da consulta.

5. Para verificar se a tabela **errorLogs** foi criada, use a seguinte instrução para retornar todas as linhas de **errorLogs**:

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

## <a id="summary"></a><a id="nextsteps"></a>Próximas etapas

* For more general information on Hive in HDInsight, see [Use Apache Hive with Apache Hadoop on HDInsight](hdinsight-use-hive.md)

* For more information on other ways you can work with Hadoop on HDInsight, see [Use MapReduce with Apache Hadoop on HDInsight](hdinsight-use-mapreduce.md)
