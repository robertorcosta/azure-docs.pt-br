---
title: Use o conector do Data Explorer do Azure para Apache Spark para mover dados entre os clusters do Azure Data Explorer e do Spark.
description: Este tópico mostra como mover dados entre clusters do Azure Data Explorer e Apache Spark.
author: orspod
ms.author: orspodek
ms.reviewer: michazag
ms.service: data-explorer
ms.topic: conceptual
ms.date: 1/14/2020
ms.openlocfilehash: b358287664ac6d6a3b641e1ab63073810ceb4c40
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2020
ms.locfileid: "78208569"
---
# <a name="azure-data-explorer-connector-for-apache-spark"></a>Conector do Data Explorer do Azure para Apache Spark

[Apache Spark](https://spark.apache.org/) é um mecanismo de análise unificado para processamento de dados em larga escala. O Azure Data Explorer é um serviço de análise de dados rápido e totalmente gerenciado para análise em tempo real em grandes volumes de dados. 

O conector do Azure Data Explorer para Spark é um [projeto](https://github.com/Azure/azure-kusto-spark) de software livre que pode ser executado em qualquer cluster do Spark. Ele implementa a fonte de dados e o coletor de dados para mover dados entre os clusters do Azure Data Explorer e Spark. Usando o Azure Data Explorer e o Apache Spark, você pode criar aplicativos rápidos e escalonáveis direcionados a cenários baseados em dados. Por exemplo, ML (Machine Learning), extração de transformação e carregamento (ETL) e Log Analytics. Com o conector, o Azure Data Explorer se torna um armazenamento de dados válido para operações de origem e coletor padrão do Spark, como Write, Read e writeStream.

Você pode gravar no Azure Data Explorer no modo de lote ou de streaming. A leitura do Azure Data Explorer dá suporte à remoção de colunas e à aplicação de predicado, que filtra os dados no Azure Data Explorer, reduzindo o volume de dados transferidos.

Este tópico descreve como instalar e configurar o conector do Azure Data Explorer Spark e mover dados entre os clusters do Azure Data Explorer e Apache Spark.

> [!NOTE]
> Embora alguns dos exemplos a seguir refiram-se a um cluster [Azure Databricks](https://docs.azuredatabricks.net/) Spark, o conector do Azure data Explorer Spark não assume dependências diretas no databricks ou em qualquer outra distribuição do Spark.

## <a name="prerequisites"></a>{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}

* [Criar um cluster do Azure Data Explorer e o banco de dados](/azure/data-explorer/create-cluster-database-portal) 
* Criar um cluster Spark
* Instalar a biblioteca do conector de Data Explorer do Azure:
    * Bibliotecas predefinidas para [Spark 2,4, escala 2,11](https://github.com/Azure/azure-kusto-spark/releases) 
    * [Repositório Maven](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/spark-kusto-connector)
* [Maven 3. x](https://maven.apache.org/download.cgi) instalado

> [!TIP]
> as versões 2.3. x também têm suporte, mas podem exigir algumas alterações nas dependências pom. xml.

## <a name="how-to-build-the-spark-connector"></a>Como criar o conector do Spark

> [!NOTE]
> Esta etapa é opcional. Se você estiver usando bibliotecas predefinidas, vá para a [configuração do cluster Spark](#spark-cluster-setup).

### <a name="build-prerequisites"></a>Pré-requisitos de compilação

1. Instale as bibliotecas listadas em [dependências](https://github.com/Azure/azure-kusto-spark#dependencies) , incluindo as seguintes bibliotecas do [SDK do Java Kusto](/azure/kusto/api/java/kusto-java-client-library) :
    * [Cliente de dados Kusto](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-data)
    * [Cliente de ingestão de Kusto](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-ingest)

1. Consulte [esta fonte](https://github.com/Azure/azure-kusto-spark) para criar o conector do Spark.

1. Para aplicativos escalares/Java usando definições de projeto Maven, vincule seu aplicativo com o seguinte artefato (a versão mais recente pode ser diferente):
    
    ```Maven
       <dependency>
         <groupId>com.microsoft.azure</groupId>
         <artifactId>spark-kusto-connector</artifactId>
         <version>1.1.0</version>
       </dependency>
    ```

### <a name="build-commands"></a>Criar comandos

Para criar o jar e executar todos os testes:

```
mvn clean package
```

Para criar o Jar, execute todos os testes e instale o jar em seu repositório Maven local:

```
mvn clean install
```

Para obter mais informações, consulte [uso do conector](https://github.com/Azure/azure-kusto-spark#usage).

## <a name="spark-cluster-setup"></a>Configuração do cluster Spark

> [!NOTE]
> É recomendável usar a versão mais recente do conector do Azure Data Explorer Spark ao executar as etapas a seguir.

1. Defina as seguintes configurações de cluster do Spark, com base no cluster Azure Databricks usando o Spark 2.4.4 e o escala 2,11:

    ![Configurações de cluster do databricks](media/spark-connector/databricks-cluster.png)
    
1. Instale a biblioteca mais recente do Spark-Kusto-Connector do Maven:
    
    ![importar bibliotecas](media/spark-connector/db-libraries-view.png) ![selecione Spark-Kusto-Connector](media/spark-connector/db-dependencies.png)

1. Verifique se todas as bibliotecas necessárias estão instaladas:

    ![Verificar bibliotecas instaladas](media/spark-connector/db-libraries-view.png)

1. Para a instalação usando um arquivo JAR, verifique se foram instaladas dependências adicionais:

    ![Adicionar dependências](media/spark-connector/db-not-maven.png)

## <a name="authentication"></a>Autenticação

O conector do Azure Data Explorer Spark permite que você autentique com o Azure Active Directory (AD do Azure) usando um dos seguintes métodos:
* Um [aplicativo do Azure ad](#azure-ad-application-authentication)
* Um [token de acesso do Azure ad](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#direct-authentication-with-access-token)
* [Autenticação de dispositivo](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#device-authentication) (para cenários de não produção)
* Um [Azure Key Vault](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#key-vault) acessar o recurso Key Vault, instalar o pacote Azure-keyvault e fornecer credenciais de aplicativo.

### <a name="azure-ad-application-authentication"></a>Autenticação de aplicativo do Azure AD

A autenticação de aplicativo do Azure AD é o método de autenticação mais simples e mais comum e é recomendada para o conector do Azure Data Explorer Spark.

|{1&gt;Propriedades&lt;1}  |Descrição  |
|---------|---------|
|**KUSTO_AAD_CLIENT_ID**     |   Identificador de aplicativo (cliente) do Azure AD.      |
|**KUSTO_AAD_AUTHORITY_ID**     |  Autoridade de autenticação do Azure AD. ID do diretório (locatário) do Azure AD.        |
|**KUSTO_AAD_CLIENT_PASSWORD**    |    Chave de aplicativo do Azure AD para o cliente.     |

### <a name="azure-data-explorer-privileges"></a>Privilégios de Data Explorer do Azure

Conceda os seguintes privilégios em um cluster de Data Explorer do Azure:

* Para leitura (fonte de dados), a identidade do Azure AD deve ter privilégios de *Visualizador* no banco de dado de destino ou privilégios de *administrador* na tabela de destino.
* Para gravação (coletor de dados), a identidade do Azure AD deve ter privilégios de *ingestão* no banco de dados de destino. Ele também deve ter privilégios de *usuário* no banco de dados de destino para criar novas tabelas. Se a tabela de destino já existir, você deverá configurar privilégios de *administrador* na tabela de destino.
 
Para obter mais informações sobre as funções de entidade de segurança Data Explorer do Azure, consulte [autorização baseada em função](/azure/kusto/management/access-control/role-based-authorization). Para gerenciar funções de segurança, consulte [Gerenciamento de funções de segurança](/azure/kusto/management/security-roles).

## <a name="spark-sink-writing-to-azure-data-explorer"></a>Coletor Spark: gravando no Azure Data Explorer

1. Configurar parâmetros do coletor:

     ```scala
    val KustoSparkTestAppId = dbutils.secrets.get(scope = "KustoDemos", key = "KustoSparkTestAppId")
    val KustoSparkTestAppKey = dbutils.secrets.get(scope = "KustoDemos", key = "KustoSparkTestAppKey")
 
    val appId = KustoSparkTestAppId
    val appKey = KustoSparkTestAppKey
    val authorityId = "72f988bf-86f1-41af-91ab-2d7cd011db47" // Optional - defaults to microsoft.com
    val cluster = "Sparktest.eastus2"
    val database = "TestDb"
    val table = "StringAndIntTable"
    ```

1. Grave o dataframe do Spark no cluster de Data Explorer do Azure como lote:

    ```scala
    import com.microsoft.kusto.spark.datasink.KustoSinkOptions
    import org.apache.spark.sql.{SaveMode, SparkSession}

    df.write
      .format("com.microsoft.kusto.spark.datasource")
      .option(KustoSinkOptions.KUSTO_CLUSTER, cluster)
      .option(KustoSinkOptions.KUSTO_DATABASE, database)
      .option(KustoSinkOptions.KUSTO_TABLE, "Demo3_spark")
      .option(KustoSinkOptions.KUSTO_AAD_CLIENT_ID, appId)
      .option(KustoSinkOptions.KUSTO_AAD_CLIENT_PASSWORD, appKey)
      .option(KustoSinkOptions.KUSTO_AAD_AUTHORITY_ID, authorityId)
      .option(KustoSinkOptions.KUSTO_TABLE_CREATE_OPTIONS, "CreateIfNotExist")
      .mode(SaveMode.Append)
      .save()  
    ```
    
   Ou use a sintaxe simplificada:
   
    ```scala
         import com.microsoft.kusto.spark.datasink.SparkIngestionProperties
         import com.microsoft.kusto.spark.sql.extension.SparkExtension._
         
         val sparkIngestionProperties = Some(new SparkIngestionProperties()) // Optional, use None if not needed
         df.write.kusto(cluster, database, table, conf, sparkIngestionProperties)
    ```
   
1. Gravar dados de streaming:

    ```scala    
    import org.apache.spark.sql.streaming.Trigger
    import java.util.concurrent.TimeUnit
    import java.util.concurrent.TimeUnit
    import org.apache.spark.sql.streaming.Trigger

    // Set up a checkpoint and disable codeGen. 
    spark.conf.set("spark.sql.streaming.checkpointLocation", "/FileStore/temp/checkpoint")
        
    // Write to a Kusto table from a streaming source
    val kustoQ = df
          .writeStream
          .format("com.microsoft.kusto.spark.datasink.KustoSinkProvider")
          .options(conf) 
          .option(KustoSinkOptions.KUSTO_WRITE_ENABLE_ASYNC, "true") // Optional, better for streaming, harder to handle errors
          .trigger(Trigger.ProcessingTime(TimeUnit.SECONDS.toMillis(10))) // Sync this with the ingestionBatching policy of the database
          .start()
    ```

## <a name="spark-source-reading-from-azure-data-explorer"></a>Origem do Spark: lendo do Azure Data Explorer

1. Ao ler [pequenas quantidades de dados](/azure/kusto/concepts/querylimits), defina a consulta de dados:

    ```scala
    import com.microsoft.kusto.spark.datasource.KustoSourceOptions
    import org.apache.spark.SparkConf
    import org.apache.spark.sql._
    import com.microsoft.azure.kusto.data.ClientRequestProperties

    val query = s"$table | where (ColB % 1000 == 0) | distinct ColA"
    val conf: Map[String, String] = Map(
          KustoSourceOptions.KUSTO_AAD_CLIENT_ID -> appId,
          KustoSourceOptions.KUSTO_AAD_CLIENT_PASSWORD -> appKey
        )

    val df = spark.read.format("com.microsoft.kusto.spark.datasource").
      options(conf).
      option(KustoSourceOptions.KUSTO_QUERY, query).
      option(KustoSourceOptions.KUSTO_DATABASE, database).
      option(KustoSourceOptions.KUSTO_CLUSTER, cluster).
      load()

    // Simplified syntax flavor
    import com.microsoft.kusto.spark.sql.extension.SparkExtension._
    
    val cpr: Option[ClientRequestProperties] = None // Optional
    val df2 = spark.read.kusto(cluster, database, query, conf, cpr)
    display(df2)
    ```

1. Opcional: se **você** fornecer o armazenamento de blob transitório (e não o Azure data Explorer), os BLOBs serão criados sob a responsabilidade do chamador. Isso inclui o provisionamento do armazenamento, a rotação de chaves de acesso e a exclusão de artefatos transitórios. 
    O módulo KustoBlobStorageUtils contém funções auxiliares para excluir BLOBs com base em coordenadas de conta e contêiner e credenciais de conta, ou uma URL SAS completa com permissões de gravação, leitura e lista. Quando o RDD correspondente não é mais necessário, cada transação armazena artefatos de blob transitórios em um diretório separado. Esse diretório é capturado como parte dos logs de informações da transação de leitura relatados no nó do driver Spark.

    ```scala
    // Use either container/account-key/account name, or container SaS
    val container = dbutils.secrets.get(scope = "KustoDemos", key = "blobContainer")
    val storageAccountKey = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountKey")
    val storageAccountName = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountName")
    // val storageSas = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageSasUrl")
    ```

    No exemplo acima, o Key Vault não é acessado usando a interface do conector; um método mais simples de usar os segredos do databricks é usado.

1. Leia o Data Explorer do Azure.

    * Se **você** fornecer o armazenamento de blob transitório, leia do Azure data Explorer da seguinte maneira:

        ```scala
         val conf3 = Map(
              KustoSourceOptions.KUSTO_AAD_CLIENT_ID -> appId,
              KustoSourceOptions.KUSTO_AAD_CLIENT_PASSWORD -> appKey
              KustoSourceOptions.KUSTO_BLOB_STORAGE_SAS_URL -> storageSas)
        val df2 = spark.read.kusto(cluster, database, "ReallyBigTable", conf3)
        
        val dfFiltered = df2
          .where(df2.col("ColA").startsWith("row-2"))
          .filter("ColB > 12")
          .filter("ColB <= 21")
          .select("ColA")
        
        display(dfFiltered)
        ```

    * Se o **azure data Explorer** fornecer o armazenamento de blob transitório, leia o data Explorer do Azure da seguinte maneira:
    
        ```scala
        val dfFiltered = df2
          .where(df2.col("ColA").startsWith("row-2"))
          .filter("ColB > 12")
          .filter("ColB <= 21")
          .select("ColA")
        
        display(dfFiltered)
        ```

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

* Saiba mais sobre o [conector do Azure data Explorer Spark](https://github.com/Azure/azure-kusto-spark/tree/master/docs)
* [Código de exemplo para Java e Python](https://github.com/Azure/azure-kusto-spark/tree/master/samples/src/main)
