---
title: Usar Apache Beeline com Apache Hive - Azure HDInsight
description: Aprenda a usar o cliente Beeline para executar consultas Hive com Hadoop no HDInsight. Beeline é um utilitário para trabalhar com HiveServer2 sobre JDBC.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 4f2b192765aab4c7cf18c62988ae2f6080b4b17c
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80436910"
---
# <a name="use-the-apache-beeline-client-with-apache-hive"></a>Usar o cliente Apache Beeline com Apache Hive

Saiba como usar o [Apache Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) para executar consultas do Apache Hive no HDInsight.

O Beeline é um cliente Hive que está incluído em nós principais do cluster HDInsight. Para instalar a Beeline localmente, consulte [Instalar o cliente beeline](#install-beeline-client), abaixo. O Beeline usa o JDBC para se conectar ao HiveServer2, um serviço hospedado em seu cluster HDInsight. Você também pode usar o Beeline para acessar remotamente o Hive no HDInsight pela internet. Os exemplos a seguir fornecem as strings de conexão mais comuns usadas para se conectar ao HDInsight da Beeline.

## <a name="types-of-connections"></a>Tipos de conexões

### <a name="from-an-ssh-session"></a>De uma sessão ssh

Ao conectar de uma sessão SSH a um cabeçada de `headnodehost` cluster, `10001`você pode então conectar-se ao endereço na porta:

```bash
beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
```

---

### <a name="over-an-azure-virtual-network"></a>Sobre uma rede virtual do Azure

Ao conectar de um cliente ao HDInsight em uma rede virtual do Azure, você deve fornecer o nome de domínio totalmente qualificado (FQDN) de um nó de cabeça de cluster. Desde que essa conexão seja feita diretamente para os nós de cluster, a conexão usa a porta `10001`:

```bash
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

Substitua pelo `<headnode-FQDN>` nome de domínio totalmente qualificado de um cabeçada de cluster. Para localizar o nome de domínio totalmente qualificado de um nó principal, use as informações do documento [Gerenciar HDInsight usando a API de REST do Apache Ambari](../hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes).

---

### <a name="to-hdinsight-enterprise-security-package-esp-cluster-using-kerberos"></a>Para o cluster HDInsight Enterprise Security Package (ESP) usando kerberos

Ao conectar de um cliente a um cluster Enterprise Security Package (ESP) junto ao Azure Active Directory (AAD)-DS em `<AAD-Domain>` uma máquina no mesmo reino do cluster, `<username>`você também deve especificar o nome de domínio e o nome de uma conta de usuário de domínio com permissões para acessar o cluster:

```bash
kinit <username>
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/default;principal=hive/_HOST@<AAD-Domain>;auth-kerberos;transportMode=http' -n <username>
```

Substitua `<username>` pelo nome de uma conta no domínio com permissões para acessar o cluster. Substitua pelo `<AAD-DOMAIN>` nome do Azure Active Directory (AAD) ao que o cluster está ligado. Use uma seqüência `<AAD-DOMAIN>` maiúscula para o valor, caso contrário, a credencial não será encontrada. Verifique `/etc/krb5.conf` os nomes do reino, se necessário.

Para encontrar a URL JDBC da Ambari:

1. A partir de um `https://CLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/summary`navegador `CLUSTERNAME` da Web, navegue até , onde está o nome do seu cluster. Certifique-se de que o HiveServer2 está sendo executado.

1. Use a área de transferência para copiar a URL do HiveServer2 JDBC.

---

### <a name="over-public-or-private-endpoints"></a>Sobre pontos finais públicos ou privados

Ao se conectar a um cluster usando os pontos finais públicos ou `admin`privados, você deve fornecer o nome da conta de login do cluster (padrão ) e senha. Por exemplo, usando Beeline de um sistema de cliente para conectar-se para o `clustername.azurehdinsight.net` endereço. Essa conexão é `443`feita por porta e é criptografada usando TLS/SSL.

Substitua `clustername` pelo nome do cluster HDInsight. Substitua `admin` pela conta de logon do cluster de seu cluster. Para clusters ESP, use o UPN user@domain.comcompleto (por exemplo, ). Substitua `password` pela senha da conta de logon do cluster.

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p 'password'
```

ou para o ponto final privado:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p 'password'
```

Os pontos finais privados apontam para um balanceador de carga básico, que só pode ser acessado a partir dos VNETs pesquisados na mesma região. Consulte [as restrições em peering vnet global e balanceadores de carga](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) para obter mais informações. Você pode `curl` usar `-v` o comando com opção para solucionar problemas de conectividade com pontos finais públicos ou privados antes de usar a linha beeline.

---

### <a name="use-beeline-with-apache-spark"></a>Usar Beeline com Apache Spark

O Apache Spark fornece sua própria implementação de HiveServer2, que, às vezes, é referenciado como o servidor Spark Thrift. Esse serviço usa Spark SQL para resolver consultas em vez de Hive e pode fornecer um desempenho melhor, dependendo da consulta.

#### <a name="through-public-or-private-endpoints"></a>Através de pontos finais públicos ou privados

A seqüência de conexão usada é ligeiramente diferente. Em vez `httpPath=/hive2` de contê-lo, é. `httpPath/sparkhive2` Substitua `clustername` pelo nome do cluster HDInsight. Substitua `admin` pela conta de logon do cluster de seu cluster. Para clusters ESP, use o UPN user@domain.comcompleto (por exemplo, ). Substitua `password` pela senha da conta de logon do cluster.

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p 'password'
```

ou para o ponto final privado:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p 'password'
```

Os pontos finais privados apontam para um balanceador de carga básico, que só pode ser acessado a partir dos VNETs pesquisados na mesma região. Consulte [as restrições em peering vnet global e balanceadores de carga](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) para obter mais informações. Você pode `curl` usar `-v` o comando com opção para solucionar problemas de conectividade com pontos finais públicos ou privados antes de usar a linha beeline.

---

#### <a name="from-cluster-head-or-inside-azure-virtual-network-with-apache-spark"></a>Da cabeça de cluster ou dentro da Rede Virtual Azure com o Apache Spark

Ao conectar diretamente do nó principal do cluster ou de um recurso dentro da mesma Rede Virtual do Azure que o cluster HDInsight, a porta `10002` deve ser usada para o servidor do Spark Thrift em vez de `10001`. O exemplo a seguir mostra como se conectar diretamente ao nó da cabeça:

```bash
/usr/hdp/current/spark2-client/bin/beeline -u 'jdbc:hive2://headnodehost:10002/;transportMode=http'
```

---

## <a name="prerequisites-for-examples"></a>Pré-requisitos para exemplos

* Um cluster Hadoop no HDInsight. Veja [Get Started com hdinsight no Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Observe o [esquema URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) para o armazenamento principal do seu cluster. Por exemplo, `wasb://` para armazenamento `abfs://` Azure, para Azure `adl://` Data Lake Storage Gen2 ou para Azure Data Lake Storage Gen1. Se a transferência segura estiver ativada para `wasbs://`o Armazenamento Azure, o URI será . Para obter mais informações, consulte [transferência segura](../../storage/common/storage-require-secure-transfer.md).

* Opção 1: um cliente SSH. Para saber mais, confira [Conectar-se ao HDInsight (Apache Hadoop) usando SSH](../hdinsight-hadoop-linux-use-ssh-unix.md). A maioria das etapas deste documento presume que você está usando Beeline de uma sessão SSH para o cluster.

* Opção 2: Um cliente beeline local.

## <a name="run-a-hive-query"></a>Executar um trabalho do Hive

Este exemplo é baseado no uso do cliente Beeline a partir de uma conexão SSH.

1. Abra uma conexão SSH para o cluster com o código abaixo. Substitua `sshuser` pelo usuário do SSH do cluster e substitua `CLUSTERNAME` pelo nome do cluster. Quando solicitado, digite a senha da conta de usuário SSH.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Conecte-se ao HiveServer2 com seu cliente Beeline a partir da sessão ssh aberta, inserindo o seguinte comando:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

3. Os comandos Beeline normalmente começam com um caractere `!`, por exemplo, `!help` exibe a ajuda. No entanto, o `!` pode ser omitido para alguns comandos. Por exemplo, `help` também funciona.

    Há `!sql`, que é usado para executar declarações HiveQL. No entanto, o HiveQL é tão usado que é possível omitir o `!sql`anterior. As duas instruções a seguir são equivalentes:

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

5. Digite as seguintes instruções para criar uma tabela chamada **log4jLogs** usando dados de amostra fornecidos com o cluster HDInsight: (Revise conforme necessário com base no seu [esquema URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme).)

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

    Essas declarações fazem as seguintes ações:

    |de |Descrição |
    |---|---|
    |DROP TABLE|Se a tabela existe, ela é excluída.|
    |CRIAR TABELA EXTERNA|Cria uma mesa **externa** em Hive. Tabelas externas só armazenam a definição da tabela no Hive. Os dados são mantidos no local original.|
    |FORMATO DA LINHA|Como os dados são formatados. Nesse caso, os campos em cada log são separados por um espaço.|
    |ARMAZENADO COMO LOCAL DE ARQUIVO DE TEXTO|Onde os dados são armazenados e em que formato de arquivo.|
    |SELECT|Seleciona uma contagem de todas as linhas onde a coluna **t4** contém o valor **[ERROR]**. Essa consulta deve retornar um valor de **3**, já que existem três linhas que contêm esse valor.|
    |INPUT__FILE__NAME COMO '%.log'|Hive tenta aplicar o esquema a todos os arquivos do diretório. Neste caso, o diretório contém arquivos que não correspondem ao esquema. Para evitar dados incorretos nos resultados, essa instrução informa ao Hive que deve retornar apenas dados de arquivos que terminam em .log.|

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

6. Saída beeline:

    ```bash
    !exit
    ```

## <a name="run-a-hiveql-file"></a>Executar um arquivo HiveQL

Esta é uma continuação do exemplo anterior. Use as etapas a seguir para criar um arquivo e  executá-lo usando o Beeline.

1. Use o comando a seguir para criar um novo arquivo chamado **query.hql**:

    ```bash
    nano query.hql
    ```

1. Use o texto a seguir como conteúdo do arquivo. Essa consulta cria uma nova tabela 'interna' chamada **errorLogs**:

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Essas declarações fazem as seguintes ações:

    |de |Descrição |
    |---|---|
    |CRIAR TABELA SE NÃO EXISTIR|Se a tabela ainda não existe, ela é criada. Como a **palavra-chave EXTERNAL** não é usada, esta declaração cria uma tabela interna. As tabelas internas são armazenadas no data warehouse do Hive e totalmente gerenciadas por ele.|
    |ARMAZENADO COMO ORC|Armazena os dados no formato OCR (Optimized Row Columnar). O formato ORC é altamente otimizado e eficiente para o armazenamento de dados do Hive.|
    |INSERIR SOBREGRAVAÇÃO ... Selecione|Seleciona linhas da tabela **log4jLogs** que contêm **[ERROR]** e insere os dados na tabela **errorLogs**.|

    > [!NOTE]  
    > Diferentemente de tabelas externas, o descarte de uma tabela interna excluirá também os dados subjacentes.

1. Para salvar o arquivo, use **Ctrl**+**X,** depois digite **Y**e, finalmente, **Enter**.

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

## <a name="install-beeline-client"></a>Instale o cliente beeline

Embora o Beeline esteja incluído nos nós de cabeça do seu cluster HDInsight, você pode querer instalá-lo em uma máquina local.  As etapas abaixo para instalar o Beeline em uma máquina local são baseadas em um [Subsistema Windows para Linux](https://docs.microsoft.com/windows/wsl/install-win10).

1. Atualizar listas de pacotes. Digite o seguinte comando em sua concha de bash:

    ```bash
    sudo apt-get update
    ```

1. Instale Java se não estiver instalado. Você pode verificar `which java` com o comando.

    1. Se nenhum pacote java estiver instalado, digite o seguinte comando:

        ```bash
        sudo apt install openjdk-11-jre-headless
        ```

    1. Abra o arquivo bashrc (geralmente encontrado em `nano ~/.bashrc`~/.bashrc): .

    1. Altere o arquivo bashrc. Adicione a seguinte linha ao final do arquivo:

        ```bash
        export JAVA_HOME=/usr/lib/jvm/java-1.11.0-openjdk-amd64
        ```

        Em seguida, **pressione Ctrl+X,** depois **Y,** em seguida, entre.

1. Baixe arquivos Hadoop e Beeline, digite os seguintes comandos:

    ```bash
    wget https://archive.apache.org/dist/hadoop/core/hadoop-2.7.3/hadoop-2.7.3.tar.gz
    wget https://archive.apache.org/dist/hive/hive-1.2.1/apache-hive-1.2.1-bin.tar.gz
    ```

1. Desempacotar os arquivos, digitar os seguintes comandos:

    ```bash
    tar -xvzf hadoop-2.7.3.tar.gz
    tar -xvzf apache-hive-1.2.1-bin.tar.gz
    ```

1. Alterar ainda mais o arquivo bashrc. Você precisa identificar o caminho para onde os arquivos foram desembalados. Se estiver usando o [Subsistema Windows para Linux](https://docs.microsoft.com/windows/wsl/install-win10), e `/mnt/c/Users/user/`você `user` seguiu os passos exatamente, seu caminho seria , onde está o seu nome de usuário.

    1. Abra o arquivo:`nano ~/.bashrc`

    1. Modifique os comandos abaixo com o caminho apropriado e digite-os no final do arquivo bashrc:

        ```bash
        export HADOOP_HOME=/path_where_the_archives_were_unpacked/hadoop-2.7.3
        export HIVE_HOME=/path_where_the_archives_were_unpacked/apache-hive-1.2.1-bin
        PATH=$PATH:$HIVE_HOME/bin
        ```

    1. Em seguida, **pressione Ctrl+X,** depois **Y,** em seguida, entre.

1. Feche e depois reabra sua sessão de bash.

1. Teste sua conexão. Use o formato de conexão de [Pontos finais públicos ou privados](#over-public-or-private-endpoints)acima.

## <a name="next-steps"></a>Próximas etapas

* Para obter informações mais gerais sobre hive no HDInsight, consulte [Use Apache Hive com Apache Hadoop no HDInsight](hdinsight-use-hive.md)

* Para obter mais informações sobre outras maneiras de trabalhar com Hadoop no HDInsight, consulte [Use MapReduce com Apache Hadoop no HDInsight](hdinsight-use-mapreduce.md)
