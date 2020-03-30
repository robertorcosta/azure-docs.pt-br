---
title: Use o conector Azure Data Explorer para Apache Spark para mover dados entre os clusters Azure Data Explorer e Spark.
description: Este tópico mostra como mover dados entre os clusters Azure Data Explorer e Apache Spark.
author: orspod
ms.author: orspodek
ms.reviewer: michazag
ms.service: data-explorer
ms.topic: conceptual
ms.date: 1/14/2020
ms.openlocfilehash: b358287664ac6d6a3b641e1ab63073810ceb4c40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78208569"
---
# <a name="azure-data-explorer-connector-for-apache-spark"></a>Conector azure Data Explorer para Apache Spark

[Apache Spark](https://spark.apache.org/) é um mecanismo de análise unificado para processamento de dados em larga escala. O Azure Data Explorer é um serviço de análise de dados rápido e totalmente gerenciado para análise em tempo real em grandes volumes de dados. 

O conector Azure Data Explorer for Spark é um [projeto de código aberto](https://github.com/Azure/azure-kusto-spark) que pode ser executado em qualquer cluster Spark. Ele implementa a fonte de dados e o dissipador de dados para movimentação de dados nos clusters Azure Data Explorer e Spark. Usando o Azure Data Explorer e o Apache Spark, você pode criar aplicativos rápidos e escaláveis visando cenários baseados em dados. Por exemplo, machine learning (ML), Extract-Transform-Load (ETL) e Log Analytics. Com o conector, o Azure Data Explorer se torna um armazenamento de dados válido para operações padrão de origem spark e sink, como gravação, leitura e writeStream.

Você pode escrever para o Azure Data Explorer em lote ou no modo de streaming. A leitura do Azure Data Explorer suporta poda de coluna e pressão predicada, que filtra os dados no Azure Data Explorer, reduzindo o volume de dados transferidos.

Este tópico descreve como instalar e configurar o conector Azure Data Explorer Spark e mover dados entre os clusters Azure Data Explorer e Apache Spark.

> [!NOTE]
> Embora alguns dos exemplos abaixo se refiem a um cluster [Azure Databricks](https://docs.azuredatabricks.net/) Spark, o conector Azure Data Explorer Spark não tem dependências diretas de Databricks ou qualquer outra distribuição do Spark.

## <a name="prerequisites"></a>Pré-requisitos

* [Crie um cluster e banco de dados do Azure Data Explorer](/azure/data-explorer/create-cluster-database-portal) 
* Criar um cluster Spark
* Instale a biblioteca de conectores do Azure Data Explorer:
    * Bibliotecas pré-construídas para [Spark 2.4, Scala 2.11](https://github.com/Azure/azure-kusto-spark/releases) 
    * [Maven repo](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/spark-kusto-connector)
* [Maven 3.x](https://maven.apache.org/download.cgi) instalado

> [!TIP]
> As versões 2.3.x também são suportadas, mas podem exigir algumas alterações nas dependências pom.xml.

## <a name="how-to-build-the-spark-connector"></a>Como construir o conector Spark

> [!NOTE]
> Esta etapa é opcional. Se você estiver usando bibliotecas pré-construídas, vá para a [configuração do cluster Spark](#spark-cluster-setup).

### <a name="build-prerequisites"></a>Construa pré-requisitos

1. Instale as bibliotecas listadas em [dependências,](https://github.com/Azure/azure-kusto-spark#dependencies) incluindo as [seguintes bibliotecas Kusto Java SDK:](/azure/kusto/api/java/kusto-java-client-library)
    * [Cliente de dados kusto](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-data)
    * [Cliente Kusto Ingest](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-ingest)

1. Consulte [esta fonte](https://github.com/Azure/azure-kusto-spark) para construir o Conector spark.

1. Para aplicações Scala/Java usando definições de projeto Maven, vincule seu aplicativo com o seguinte artefato (a versão mais recente pode diferir):
    
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

Para construir jar, execute todos os testes e instale o frasco no repositório maven local:

```
mvn clean install
```

Para obter mais informações, consulte [o uso do conector](https://github.com/Azure/azure-kusto-spark#usage).

## <a name="spark-cluster-setup"></a>Configuração do cluster spark

> [!NOTE]
> Recomenda-se usar a versão mais recente do conector Azure Data Explorer Spark ao executar as seguintes etapas.

1. Configure as seguintes configurações de cluster Spark, com base no cluster Azure Databricks usando o Spark 2.4.4 e o Scala 2.11:

    ![Configurações de cluster de databricks](media/spark-connector/databricks-cluster.png)
    
1. Instale a mais recente biblioteca de conectores spark-kusto da Maven:
    
    ![Bibliotecas](media/spark-connector/db-libraries-view.png) ![de importação Selecionam o Conector Spark-Kusto](media/spark-connector/db-dependencies.png)

1. Verifique se todas as bibliotecas necessárias estão instaladas:

    ![Verificar bibliotecas instaladas](media/spark-connector/db-libraries-view.png)

1. Para instalação usando um arquivo JAR, verifique se foram instaladas dependências adicionais:

    ![Adicionar dependências](media/spark-connector/db-not-maven.png)

## <a name="authentication"></a>Autenticação

O conector Azure Data Explorer Spark permite que você se autentique com o Azure Active Directory (Azure AD) usando um dos seguintes métodos:
* Um [aplicativo Azure AD](#azure-ad-application-authentication)
* Um [token de acesso a Azure AD](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#direct-authentication-with-access-token)
* [Autenticação de dispositivos](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#device-authentication) (para cenários não de produção)
* Um [cofre de chaves do Azure](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#key-vault) Para acessar o recurso Key Vault, instale o pacote azure-keyvault e forneça credenciais de aplicativo.

### <a name="azure-ad-application-authentication"></a>Autenticação do aplicativo Azure AD

A autenticação do aplicativo Azure AD é o método de autenticação mais simples e comum e é recomendada para o conector Azure Data Explorer Spark.

|Propriedades  |Descrição  |
|---------|---------|
|**KUSTO_AAD_CLIENT_ID**     |   Identificador de aplicativo Azure AD (cliente).      |
|**KUSTO_AAD_AUTHORITY_ID**     |  Azure AD autoridade de autenticação. ID do Azure AD Directory (inquilino).        |
|**KUSTO_AAD_CLIENT_PASSWORD**    |    Chave de aplicativo Azure AD para o cliente.     |

### <a name="azure-data-explorer-privileges"></a>Privilégios do Azure Data Explorer

Conceda os seguintes privilégios em um cluster do Azure Data Explorer:

* Para leitura (fonte de dados), a identidade Azure AD deve ter privilégios de *visualização* no banco de dados de destino ou privilégios *de administrador* na tabela de destino.
* Para escrever (dissipador de dados), a identidade Azure AD deve ter privilégios *ingestor* no banco de dados de destino. Ele também deve ter privilégios de *usuário* no banco de dados de destino para criar novas tabelas. Se a tabela de destino já existir, você deve configurar privilégios *de administrador* na tabela de destino.
 
Para obter mais informações sobre as funções principais do Azure Data Explorer, consulte [a autorização baseada em funções](/azure/kusto/management/access-control/role-based-authorization). Para gerenciar funções de segurança, consulte [gerenciamento de funções de segurança](/azure/kusto/management/security-roles).

## <a name="spark-sink-writing-to-azure-data-explorer"></a>Spark sink: escrevendo para o Azure Data Explorer

1. Configure parâmetros da pia:

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

1. Escreva o Spark DataFrame para o cluster do Azure Data Explorer como lote:

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
   
1. Escrever dados de streaming:

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

## <a name="spark-source-reading-from-azure-data-explorer"></a>Fonte de faísca: leitura do Azure Data Explorer

1. Ao ler [pequenas quantidades de dados,](/azure/kusto/concepts/querylimits)defina a consulta de dados:

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

1. Opcional: Se **você** fornecer o armazenamento transitório blob (e não o Azure Data Explorer) as bolhas são criadas estão responsabilidade do chamador. Isso inclui o provisionamento do armazenamento, a rotação de chaves de acesso e a exclusão de artefatos transitórios. 
    O módulo KustoBlobStorageUtils contém funções auxiliares para excluir blobs com base em coordenadas de conta e contêiner e credenciais de conta, ou uma URL SAS completa com permissões de gravação, leitura e lista. Quando o RDD correspondente não é mais necessário, cada transação armazena artefatos de bolha transitórias em um diretório separado. Este diretório é capturado como parte dos registros de informações de transação de leitura relatados no nó Spark Driver.

    ```scala
    // Use either container/account-key/account name, or container SaS
    val container = dbutils.secrets.get(scope = "KustoDemos", key = "blobContainer")
    val storageAccountKey = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountKey")
    val storageAccountName = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountName")
    // val storageSas = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageSasUrl")
    ```

    No exemplo acima, o Key Vault não é acessado usando a interface do conector; um método mais simples de usar os segredos Databricks é usado.

1. Leia no Azure Data Explorer.

    * Se **você** fornecer o armazenamento transitório de blob, leia do Azure Data Explorer da seguinte forma:

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

    * Se **o Azure Data Explorer** fornecer o armazenamento transitório de blob, leia do Azure Data Explorer da seguinte forma:
    
        ```scala
        val dfFiltered = df2
          .where(df2.col("ColA").startsWith("row-2"))
          .filter("ColB > 12")
          .filter("ColB <= 21")
          .select("ColA")
        
        display(dfFiltered)
        ```

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre o [Conector spark do Azure Data Explorer](https://github.com/Azure/azure-kusto-spark/tree/master/docs)
* [Código de exemplo para Java e Python](https://github.com/Azure/azure-kusto-spark/tree/master/samples/src/main)
