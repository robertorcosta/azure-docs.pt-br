---
title: Colmeia de & Colmeia - Conector do Armazém colmeia - Azure HDInsight
description: Aprenda a integrar Apache Spark e Apache Hive com o Conector do Armazém colmeia no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/02/2020
ms.openlocfilehash: f386530ffb3a074a5c1db1d9f28535d28c8b1284
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252419"
---
# <a name="integrate-apache-spark-and-apache-hive-with-the-hive-warehouse-connector"></a>Integrar o Apache Spark e Apache Hive ao Hive Warehouse Connector

O Apache Hive Warehouse Connector (HWC) é uma biblioteca que permite que você trabalhe mais facilmente com apache spark e Apache Hive, apoiando tarefas como mover dados entre spark DataFrames e tabelas Colmeia, e também direcionar dados de streaming spark para tabelas hive. O conector do Armazém Hive funciona como uma ponte entre A Faísca e a Colméia. Ele suporta Scala, Java e Python para desenvolvimento.

O Conector do Armazém Colmeia permite que você aproveite as características exclusivas da Colmeia e da Spark para criar poderosas aplicações de big data. A Colmeia Apache oferece suporte para transações de banco de dados que são atômicas, consistentes, isoladas e duráveis (ACID). Para obter mais informações sobre ACID e transações em Hive, consulte [Hive Transactions](https://cwiki.apache.org/confluence/display/Hive/Hive+Transactions). A Hive também oferece controles de segurança detalhados através do Apache Ranger e do Processamento Analítico de Baixa Latência não disponíveis no Apache Spark.

Apache Spark, tem uma API de streaming estruturada que oferece recursos de streaming não disponíveis na Colmeia Apache. Começando com HDInsight 4.0, Apache Spark 2.3.1 e Apache Hive 3.1.0 têm metalojas separadas, o que pode dificultar a interoperabilidade. O conector do Armazém Colmeia facilita o uso de Faísca e Colmeia juntos. A biblioteca HWC carrega dados de daemons LLAP para executores spark em paralelo, tornando-o mais eficiente e escalável do que usar uma conexão JDBC padrão de Spark para Hive.

![arquitetura conector armazém colmeia](./media/apache-hive-warehouse-connector/hive-warehouse-connector-architecture.png)

Algumas das operações suportadas pelo Conector do Armazém colmeia são:

* Descrevendo uma tabela
* Criando uma tabela para dados formatados por ORC
* Selecionando dados da Colmeia e recuperando um DataFrame
* Escrevendo um DataFrame para Colmeia em lote
* Executando uma declaração de atualização da Colmeia
* Lendo dados da tabela da Colmeia, transformando-os em Faísca, e escrevendo-os para uma nova tabela Colmeia
* Escrevendo um fluxo dataframe ou spark para hive usando hivestreaming

## <a name="hive-warehouse-connector-setup"></a>Configuração do conector do armazém colmeia

Siga estas etapas para configurar o Conector do Armazém colmeia entre um cluster Spark e Interactive Query no Azure HDInsight:

### <a name="create-clusters"></a>Criar clusters

1. Crie um cluster HDInsight Spark **4.0** com uma conta de armazenamento e uma rede virtual personalizada do Azure. Para obter informações sobre a criação de um cluster em uma rede virtual Azure, consulte [Adicionar HDInsight a uma rede virtual existente](../../hdinsight/hdinsight-plan-virtual-network-deployment.md#existingvnet).

1. Crie um cluster HDInsight Interactive Query (LLAP) **4.0** com a mesma conta de armazenamento e rede virtual Azure do cluster Spark.

### <a name="modify-hosts-file"></a>Modificar arquivo hosts

Copie as informações do `/etc/hosts` nó do arquivo no headnode0 do seu cluster `/etc/hosts` De consulta interativa e concatena as informações para o arquivo no headnode0 do seu cluster Spark. Esta etapa permitirá que o cluster Spark resolva endereços IP dos nós no cluster Consulta Interativa. Exibir o conteúdo do `cat /etc/hosts`arquivo atualizado com . A saída final deve se parecer com o que é mostrado na captura de tela abaixo.

![conector armazém colmeia hosts arquivo](./media/apache-hive-warehouse-connector/hive-warehouse-connector-hosts-file.png)

### <a name="gather-preliminary-information"></a>Coletar informações preliminares

#### <a name="from-your-interactive-query-cluster"></a>A partir do seu cluster de consulta interativa

1. Navegue até a página apache ambari `https://LLAPCLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/configs` `LLAPCLUSTERNAME` hive do cluster usando onde está o nome do seu cluster de consulta interativa.

1. Navegue até **Advanced** > **General** > **hive.metastore.uris** e observe o valor. O valor pode ser `thrift://iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083,thrift://hn1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083`semelhante a: .

1. Navegue até **Advanced** > Advanced hive.zookeeper.quorum**Advanced hive-site** > **hive.zookeeper.quorum** e observe o valor. O valor pode ser `zk0-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk4-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181`semelhante a: .

#### <a name="from-your-apache-spark-cluster"></a>Do seu aglomerado Apache Spark

1. Navegue até a página apache ambari `https://SPARKCLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/configs` `SPARKCLUSTERNAME` hive do cluster usando onde está o nome do seu aglomerado Apache Spark.

1. Navegue até **advanced** > **advanced hive-interactive-site** > **hive.llap.daemon.service.hosts** e observe o valor. O valor pode ser `@llap0`semelhante a: .

### <a name="configure-spark-cluster-settings"></a>Configurar configurações de cluster Spark

A partir da sua ui web Spark Ambari, navegue até **spark2** > **CONFIGS** > **Custom spark2-defaults**.

![Configuração apache Ambari Spark2](./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark2-ambari.png)

Selecione **Adicionar propriedade...** conforme necessário para adicionar/atualizar o seguinte:

| Chave | Valor |
|----|----|
|`spark.hadoop.hive.llap.daemon.service.hosts`|O valor que você obteve anteriormente de **hive.llap.daemon.service.hosts**.|
|`spark.sql.hive.hiveserver2.jdbc.url`|`jdbc:hive2://LLAPCLUSTERNAME.azurehdinsight.net:443/;user=admin;password=PWD;ssl=true;transportMode=http;httpPath=/hive2`. Definido para a seqüência de conexões JDBC, que se conecta ao Hiveserver2 no cluster Consulta Interativa. SUBSTITUA `LLAPCLUSTERNAME` com o nome do seu cluster Consulta Interativa. Substitua com `PWD` a senha real.|
|`spark.datasource.hive.warehouse.load.staging.dir`|`wasbs://STORAGE_CONTAINER_NAME@STORAGE_ACCOUNT_NAME.blob.core.windows.net/tmp`. Definir para um diretório de encenação compatível com HDFS adequado. Se você tiver dois clusters diferentes, o diretório de encenação deve ser uma pasta no diretório de preparação da conta de armazenamento do cluster LLAP para que o HiveServer2 tenha acesso a ele.  Substitua pelo `STORAGE_ACCOUNT_NAME` nome da conta de armazenamento `STORAGE_CONTAINER_NAME` que está sendo usada pelo cluster e pelo nome do recipiente de armazenamento.|
|`spark.datasource.hive.warehouse.metastoreUri`|O valor que você obteve anteriormente de **hive.metastore.uris**.|
|`spark.security.credentials.hiveserver2.enabled`|`false`para o modo de implantação do cliente YARN.|
|`spark.hadoop.hive.zookeeper.quorum`|O valor que você obteve anteriormente de **hive.zookeeper.quorum**.|

Salve as alterações e reinicie os componentes conforme necessário.

## <a name="using-the-hive-warehouse-connector"></a>Usando o conector do armazém colmeia

### <a name="connecting-and-running-queries"></a>Conectando e executando consultas

Você pode escolher entre alguns métodos diferentes para se conectar ao seu cluster de consulta interativa e executar consultas usando o Conector do Armazém colmeia. Os métodos suportados incluem as seguintes ferramentas:

* [faísca-shell](../spark/apache-spark-shell.md)
* PySpark
* spark-submit
* [Zeppelin](../spark/apache-spark-zeppelin-notebook.md)
* [Livy](../spark/apache-spark-livy-rest-interface.md)

Todos os exemplos fornecidos neste artigo serão executados através de spark-shell.

Para iniciar uma sessão de concha sinuosa, faça as seguintes etapas:

1. SSH no headnode para o seu aglomerado Apache Spark. Para obter mais informações sobre como se conectar ao seu cluster com o SSH, consulte [Conectar-se ao HDInsight (Apache Hadoop) usando o SSH](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. Digite o seguinte comando para iniciar a camada de ignição:

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<STACK_VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    ```

    Você verá uma mensagem `scala>` de boas-vindas e um aviso onde você pode inserir comandos.

1. Depois de iniciar a faísca, uma instância do Conector do Armazém colmeia pode ser iniciada usando os seguintes comandos:

    ```scala
    import com.hortonworks.hwc.HiveWarehouseSession
    val hive = HiveWarehouseSession.session(spark).build()
    ```

### <a name="connecting-and-running-queries-on-enterprise-security-package-esp-clusters"></a>Conectando e executando consultas em clusters Enterprise Security Package (ESP)

O Enterprise Security Package (ESP) fornece recursos de nível corporativo, como autenticação baseada no Active Directory, suporte a vários usuários e controle de acesso baseado em função para clusters Apache Hadoop no Azure HDInsight. Para obter mais informações sobre o ESP, consulte [Use Enterprise Security Package no HDInsight](../domain-joined/apache-domain-joined-architecture.md).

1. SSH no headnode para o seu aglomerado Apache Spark. Para obter mais informações sobre como se conectar ao seu cluster com o SSH, consulte [Conectar-se ao HDInsight (Apache Hadoop) usando o SSH](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. Digite `kinit` e faça login com um usuário de domínio.

1. Inicie a spark-shell com a lista completa de parâmetros de configuração conforme mostrado abaixo. Todos os valores em todas as letras maiúsculas entre os suportes angulares devem ser especificados com base no seu cluster. Se você precisar descobrir os valores a serem inseridos para qualquer um dos parâmetros abaixo, consulte a seção na [configuração do Conector do Armazém colmeia](#hive-warehouse-connector-setup).:

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<STACK_VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    --conf spark.hadoop.hive.llap.daemon.service.hosts='<LLAP_APP_NAME>'
    --conf spark.sql.hive.hiveserver2.jdbc.url='jdbc:hive2://<ZOOKEEPER_QUORUM>;serviceDiscoveryMode=zookeeper;zookeeperNamespace=hiveserver2-interactive'
    --conf spark.datasource.hive.warehouse.load.staging.dir='<STAGING_DIR>'
    --conf spark.datasource.hive.warehouse.metastoreUri='<METASTORE_URI>'
    --conf spark.hadoop.hive.zookeeper.quorum='<ZOOKEEPER_QUORUM>'
   ```

### <a name="creating-spark-dataframes-from-hive-queries"></a>Criando dataframes de faísca a partir de consultas da Colméia

Os resultados de todas as consultas usando a biblioteca HWC são retornados como um DataFrame. Os exemplos a seguir demonstram como criar uma consulta básica.

```scala
hive.setDatabase("default")
val df = hive.executeQuery("select * from hivesampletable")
df.filter("state = 'Colorado'").show()
```

Os resultados da consulta são Spark DataFrames, que podem ser usados com bibliotecas Spark como MLIB e SparkSQL.

### <a name="writing-out-spark-dataframes-to-hive-tables"></a>Escrevendo quadros de dados spark para tabelas de colmeia

Spark não suporta nativamente a escrita nas tabelas ACID gerenciadas pela Hive. No entanto, usando o HWC, você pode escrever qualquer DataFrame em uma tabela Colmeia. Você pode ver essa funcionalidade no trabalho no seguinte exemplo:

1. Crie uma `sampletable_colorado` tabela chamada e especifique suas colunas usando o seguinte comando:

    ```scala
    hive.createTable("sampletable_colorado").column("clientid","string").column("querytime","string").column("market","string").column("deviceplatform","string").column("devicemake","string").column("devicemodel","string").column("state","string").column("country","string").column("querydwelltime","double").column("sessionid","bigint").column("sessionpagevieworder","bigint").create()
    ```

1. Filtrar `hivesampletable` a tabela `state` onde `Colorado`a coluna é igual . Esta consulta da tabela Hive é devolvida como um Spark DataFrame. Em seguida, o DataFrame é `sampletable_colorado` salvo `write` na tabela Colmeia usando a função.

    ```scala
    hive.table("hivesampletable").filter("state = 'Colorado'").write.format(HiveWarehouseSession.HIVE_WAREHOUSE_CONNECTOR).option("table","sampletable_colorado").save()
    ```

1. Veja os resultados com o seguinte comando:

    ```scala
    hive.table("sampletable_colorado").show()
    ```

    ![conector armazém colmeia mostrar tabela colmeia](./media/apache-hive-warehouse-connector/hive-warehouse-connector-show-hive-table.png)

### <a name="structured-streaming-writes"></a>Gravações estruturadas de streaming

Usando o Conector do Armazém Hive, você pode usar o streaming Spark para gravar dados em tabelas da Colmeia.

Siga as etapas abaixo para criar um exemplo do Conector do Armazém colmeia que ingere dados de um fluxo Spark na porta 9999 local em uma tabela colmeia.

1. Siga as etapas em [Conectar e executar consultas](#connecting-and-running-queries).

1. Inicie o fluxo de faíscas com o seguinte comando:

    ```scala
    val lines = spark.readStream.format("socket").option("host", "localhost").option("port",9999).load()
    ```

1. Gerar dados para o fluxo Spark que você criou, fazendo as seguintes etapas:
    1. Abra uma segunda sessão de SSH no mesmo cluster Spark.
    1. No prompt de comando, digite `nc -lk 9999`. Este comando usa o utilitário netcat para enviar dados da linha de comando para a porta especificada.

1. Retorne à primeira sessão SSH e crie uma nova tabela Hive para conter os dados de streaming. Na spark-shell, digite o seguinte comando:

    ```scala
    hive.createTable("stream_table").column("value","string").create()
    ```

1. Em seguida, escreva os dados de streaming para a tabela recém-criada usando o seguinte comando:

    ```scala
    lines.filter("value = 'HiveSpark'").writeStream.format(HiveWarehouseSession.STREAM_TO_STREAM).option("database", "default").option("table","stream_table").option("metastoreUri",spark.conf.get("spark.datasource.hive.warehouse.metastoreUri")).option("checkpointLocation","/tmp/checkpoint1").start()
    ```

    >[!Important]
    > As `metastoreUri` `database` opções devem ser definidas manualmente devido a um problema conhecido no Apache Spark. Para obter mais informações sobre este problema, consulte [SPARK-25460](https://issues.apache.org/jira/browse/SPARK-25460).

1. Retorne à segunda sessão ssh e insira os seguintes valores:

    ```bash
    foo
    HiveSpark
    bar
    ```

1. Retorne à primeira sessão de SSH e observe a breve atividade. Use o seguinte comando para visualizar os dados:

    ```scala
    hive.table("stream_table").show()
    ```

Use **Ctrl + C** para parar o netcat na segunda sessão ssh. Use `:q` para sair da faísca na primeira sessão SSH.

### <a name="securing-data-on-spark-esp-clusters"></a>Proteção de dados em clusters Spark ESP

1. Crie uma `demo` tabela com alguns dados de amostra inserindo os seguintes comandos:

    ```scala
    create table demo (name string);
    INSERT INTO demo VALUES ('HDinsight');
    INSERT INTO demo VALUES ('Microsoft');
    INSERT INTO demo VALUES ('InteractiveQuery');
    ```

1. Veja o conteúdo da tabela com o seguinte comando. Antes de aplicar a `demo` política, a tabela mostra a coluna completa.

    ```scala
    hive.executeQuery("SELECT * FROM demo").show()
    ```

    ![tabela demo antes de aplicar política ranger](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-before-ranger-policy.png)

1. Aplique uma diretiva de mascaramento de coluna que só mostra os últimos quatro caracteres da coluna.  
    1. Vá para a Ranger Admin UI em `https://CLUSTERNAME.azurehdinsight.net/ranger/`.
    1. Clique no serviço Hive para o seu cluster em **Hive**.
        ![gerente de serviço sumido](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-service-manager.png)
    1. Clique na guia **Mascaramento** e, em seguida, **adicione nova política**

        ![lista de políticas de política de colmeia do armazém colméia](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-hive-policy-list.png)

    a. Forneça um nome de política desejado. Selecione banco de dados: **Padrão**, Tabela Colmeia: **demonstração**, Coluna Colmeia: **nome**, Usuário: **rsadmin2**, Tipos de acesso: **selecione**e **Máscara parcial: mostre os últimos 4** do menu Selecionar Opção de **Mascaramento.** Clique em **Adicionar**.
                ![criar política](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-create-policy.png)
1. Veja o conteúdo da tabela novamente. Depois de aplicar a política ranger, podemos ver apenas os últimos quatro caracteres da coluna.

    ![tabela demo depois de aplicar política ranger](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-after-ranger-policy.png)

## <a name="next-steps"></a>Próximas etapas

* [Usar a Consulta Interativa com o HDInsight](./apache-interactive-query-get-started.md)
* [Exemplos de interação com o Conector do Armazém colmeia usando Zeppelin, Livy, spark-submit e pyspark](https://community.hortonworks.com/articles/223626/integrating-apache-hive-with-apache-spark-hive-war.html)
