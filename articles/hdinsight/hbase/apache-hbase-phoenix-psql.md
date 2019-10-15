---
title: Carregamento em massa no Apache Phoenix usando psql – HDInsight do Azure
description: Usar a ferramenta psql para carregar dados de carregamento em massa em tabelas Apache Phoenix no Azure HDInsight
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/10/2017
ms.author: ashishth
ms.openlocfilehash: f00f6bcf07cbdc5aeaeb04aeccf7e88cf4822dbf
ms.sourcegitcommit: 9dec0358e5da3ceb0d0e9e234615456c850550f6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/14/2019
ms.locfileid: "72311718"
---
# <a name="bulk-load-data-into-apache-phoenix-using-psql"></a>Carregar dados em massa para o Apache Phoenix usando psql

[Apache Phoenix](https://phoenix.apache.org/) é um banco de dados relacional livre e paralelo em massa criado em [Apache HBase](../hbase/apache-hbase-overview.md). O Phoenix fornece consultas como SQL em HBase. O Phoenix usa drivers JDBC para permitir aos usuários criar, excluir e alterar tabelas SQL, índices, exibições e as sequências e linhas upsert individualmente e em massa. O Phoenix usa compilação nativa noSQL, em vez de usar o MapReduce para compilar consultas, para criar aplicativos de baixa latência sobre HBase. O Phoenix adiciona coprocessadores para oferecer suporte à execução de código fornecido pelo cliente no espaço de endereço do servidor, executando o código colocalizado junto com os dados. Isso minimiza a transferência de dados do cliente/servidor.  Para trabalhar com dados usando Phoenix no HDInsight, primeiro crie tabelas e, em seguida, carregue dados nelas.

## <a name="bulk-loading-with-apache-phoenix"></a>Carregamento em massa com o Apache Phoenix

Há várias maneiras de obter dados em HBase incluindo o uso de APIs de clientes, um trabalho de MapReduce com TableOutputFormat ou inserindo os dados manualmente usando o shell do HBase. O Phoenix fornece dois métodos para carregar dados CSV em tabelas de Phoenix: uma ferramenta de carregamento de cliente chamada `psql` e uma ferramenta de carregamento em massa baseada em MapReduce.

A ferramenta `psql` é de thread único e é mais adequada para carregar megabytes ou gigabytes de dados. Para serem carregados, todos os arquivos CSV devem ter a extensão de arquivo '.csv'.  Você também pode especificar arquivos de script SQL na linha de comando do `psql` com a extensão de arquivo '.sql'.

O carregamento em massa com MapReduce é usado para volumes de dados muito maiores, normalmente em cenários de produção, pois o MapReduce usa vários threads.

Antes de iniciar o carregamento de dados, verifique se o Phoenix está habilitado e se as configurações de tempo limite de consulta estão como o esperado.  Acesse o painel de [Apache Ambari](https://ambari.apache.org/) do cluster HDInsight, selecione HBase e, em seguida, a guia Configuração.  Role para baixo para verificar se o Apache Phoenix está definido como `enabled` conforme mostrado:

![Configurações de Cluster de HDInsight do Apache Phoenix](./media/apache-hbase-phoenix-psql/apache-ambari-phoenix.png)

### <a name="use-psql-to-bulk-load-tables"></a>Use `psql` para tabelas de carregamento em massa

1. Criar uma nova tabela e salve a consulta com o nome do arquivo `createCustomersTable.sql`.

    ```sql
    CREATE TABLE Customers (
        ID varchar NOT NULL PRIMARY KEY,
        Name varchar,
        Income decimal,
        Age INTEGER,
        Country varchar);
    ```

2. Copie o arquivo CSV (conteúdos de exemplo mostrados) como `customers.csv` em um diretório `/tmp/` para carregar na sua tabela recém-criada.  Use o comando `hdfs` para copiar o arquivo CSV para o local de origem desejado.

    ```
    1,Samantha,260000.0,18,US
    2,Sam,10000.5,56,US
    3,Anton,550150.0,Norway
    ... 4997 more rows 
    ```

    ```bash
    hdfs dfs -copyToLocal /example/data/customers.csv /tmp/
    ```

3. Crie uma consulta SQL SELECT para verificar se os dados de entrada firam carregados corretamente e salve a consulta com o nome de arquivo `listCustomers.sql`. Você pode usar qualquer consulta SQL.
     ```sql
    SELECT Name, Income from Customers group by Country;
    ```

4. Carregue os dados em massa abrindo uma *nova* janela de comando do Hadoop. Primeiro altere para o local do diretório de execução com o comando `cd` e, em seguida, use a ferramenta `psql` (comando `psql.py` do Python). 

    O exemplo a seguir espera que você tenha copiado o arquivo `customers.csv` de uma conta de armazenamento para o diretório temporário local usando `hdfs` como na etapa 2 acima.

    ```bash
    cd /usr/hdp/current/phoenix-client/bin

    python psql.py ZookeeperQuorum createCustomersTable.sql /tmp/customers.csv listCustomers.sql
    ```

    > [!NOTE]   
    > Para determinar o nome `ZookeeperQuorum`, localize a cadeia de caracteres quorum [Apache ZooKeeper](https://zookeeper.apache.org/) no arquivo `/etc/hbase/conf/hbase-site.xml` com o nome de propriedade `hbase.zookeeper.quorum`.

5. Após a conclusão da operação `psql`, você verá uma mensagem na janela de comando:

    ```
    CSV Upsert complete. 5000 rows upserted
    Time: 4.548 sec(s)
    ```

## <a name="use-mapreduce-to-bulk-load-tables"></a>Use MapReduce para tabelas de carregamento em massa

Para carregamento de maior taxa de transferência distribuído pelo cluster, use a ferramenta de carregamento do MapReduce. Esse carregador primeiro converte todos os dados em HFiles e, em seguida, fornece o HFiles criado para o HBase.

1. Inicie o carregador de MapReduce CSV usando o `hadoop` com o jar do cliente Phoenix:

    ```bash
    hadoop jar phoenix-<version>-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table CUSTOMERS --input /data/customers.csv
    ```

2. Crie uma nova tabela com uma instrução SQL, assim como em `CreateCustomersTable.sql` anteriormente na etapa 1.

3. Para verificar o esquema da tabela, execute `!describe inputTable`.

4. Determine o caminho local para seus dados de entrada, como o arquivo de exemplo `customers.csv`. Os arquivos de entrada podem estar em sua conta de armazenamento WASB/ADLS. No cenário de exemplo, os arquivos de entrada estão no diretório `<storage account parent>/inputFolderBulkLoad`.

5. Altere para o diretório de execução do comando de carregamento em massa do MapReduce:

    ```bash
    cd /usr/hdp/current/phoenix-client/bin
    ```

6. Localize o valor do seu `ZookeeperQuorum` em `/etc/hbase/conf/hbase-site.xml`, com o nome de propriedade `hbase.zookeeper.quorum`.

7. Defina o caminho de classe e execute o comando da ferramenta `CsvBulkLoadTool`:

    ```bash
    /usr/hdp/current/phoenix-client$ HADOOP_CLASSPATH=/usr/hdp/current/hbase-client/lib/hbase-protocol.jar:/etc/hbase/conf hadoop jar /usr/hdp/2.4.2.0-258/phoenix/phoenix-4.4.0.2.4.2.0-258-client.jar

    org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input /inputFolderBulkLoad/customers.csv –zookeeper ZookeeperQuorum:2181:/hbase-unsecure
    ```

8. Para usar o MapReduce com o Azure Data Lake Storage, localize o diretório raiz do Data Lake Storage, que é o valor `hbase.rootdir` em `hbase-site.xml`. No comando a seguir, o diretório raiz do Data Lake Storage é `adl://hdinsightconf1.azuredatalakestore.net:443/hbase1`. Neste comando, especifique as pastas de entrada e saída do Data Lake Storage como parâmetros:

    ```bash
    cd /usr/hdp/current/phoenix-client

    $HADOOP_CLASSPATH=$(hbase mapredcp):/etc/hbase/conf hadoop jar /usr/hdp/2.4.2.0-258/phoenix/phoenix-4.4.0.2.4.2.0-258-client.jar

    org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/temp/input/customers.csv –zookeeper ZookeeperQuorum:2181:/hbase-unsecure --output  adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/output1
    ```

## <a name="recommendations"></a>Recomendações

* Use a mesma mídia de armazenamento para as pastas entradas e saídas, Azure Storage (WASB) ou Azure Data Lake Storage (ADL). Para transferir dados do Azure Storage para o Data Lake Storage, você pode usar o comando `distcp`:

    ```bash
    hadoop distcp wasb://@.blob.core.windows.net/example/data/gutenberg adl://.azuredatalakestore.net:443/myfolder
    ```

* Use nós de trabalho maiores. Os processos de mapeamento da cópia em massa do MapReduce geram grandes quantidades de saída temporária que preenchem o espaço não DFS disponível. Para uma grande quantidade de carregamento em massa, use mais e maiores nós de trabalho. O número de nós de trabalho que você aloca para o cluster afeta diretamente a velocidade de processamento.

* Divida os arquivos de entrada em partes de cerca de aproximadamente 10 GB. O carregamento em massa é uma operação de uso intensivo de armazenamento, portanto dividir seus arquivos de entrada em várias partes resulta em melhor desempenho.

* Evite pontos de acesso de servidor regional. Se a chave de linha está aumentando de forma monotônica, gravações sequenciais HBase podem induzir à regionalização de pontos de acesso do servidor. *Distribuir* a chave de linha reduz gravações sequenciais. O Phoenix fornece uma maneira de distribuir de forma transparente a chave de linha com um byte de distribuição para uma tabela específica, conforme mencionado abaixo.

## <a name="next-steps"></a>Próximas etapas

* [Carregamento de dados em massa com o Apache Phoenix](https://phoenix.apache.org/bulk_dataload.html)
* [Usar o Apache Phoenix com clusters do Apache HBase baseado em Linux no HDInsight](../hbase/apache-hbase-query-with-phoenix.md)
* [Tabelas distribuídas](https://phoenix.apache.org/salted.html)
* [Gramática do Apache Phoenix](https://phoenix.apache.org/language/index.html)
