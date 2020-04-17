---
title: Conectar Apache Spark ao Azure Cosmos DB
description: Saiba mais sobre o conector do Spark do Azure do Cosmos do Azure que permite conectar o Apache Spark ao Azure Cosmos DB.
author: tknandu
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: ramkris
ms.openlocfilehash: edfaf50b701f64b12f9cf5fcc9ab8d2c6d241d0a
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81482187"
---
# <a name="accelerate-big-data-analytics-by-using-the-apache-spark-to-azure-cosmos-db-connector"></a>Acelere a análise de big data usando o conector do Apache Spark para o Azure Cosmos DB

Você pode executar [trabalhos spark](https://spark.apache.org/) com dados armazenados no Azure Cosmos DB usando o conector Cosmos DB Spark. Cosmos pode ser usado para processamento de lotes e fluxos, e como uma camada de serviço para baixo acesso à latência.

Você pode usar o conector com [o Azure Databricks](https://azure.microsoft.com/services/databricks) ou [o Azure HDInsight](https://azure.microsoft.com/services/hdinsight/), que fornecem clusters Spark gerenciados no Azure. A tabela a seguir mostra versões suportadas do Spark.

| Componente | Versão |
|---------|-------|
| Apache Spark | 2.4.x, 2.3.x, 2.2.x e 2.1.x |
| Scala | 2.11 |
| Versão do Azure Databricks runtime | > 3.4 |

> [!WARNING]
> Este conector suporta a API núcleo (SQL) do Azure Cosmos DB.
> Para Cosmos DB para API MongoDB, use o [conector MongoDB Spark](https://docs.mongodb.com/spark-connector/master/).
> Para a Cosmos DB Cassandra API, use o [conector Cassandra Spark](https://github.com/datastax/spark-cassandra-connector).
>

## <a name="quickstart"></a>Guia de Início Rápido

* Siga as etapas em [Iniciar com o Java SDK](sql-api-async-java-get-started.md) para configurar uma conta Cosmos DB e preencher alguns dados.
* Siga as etapas do [Azure Databricks iniciando](/azure/azure-databricks/quickstart-create-databricks-workspace-portal) a configuração de um espaço de trabalho e cluster do Azure Databricks.
* Agora você pode criar novos Notebooks e importar a biblioteca de conectores Cosmos DB. Jump to [Working with the Cosmos DB conector](#bk_working_with_connector) para obter detalhes sobre como configurar seu espaço de trabalho.
* A seção a seguir tem trechos sobre como ler e escrever usando o conector.

### <a name="batch-reads-from-cosmos-db"></a>Leituras em lote do Cosmos DB

O trecho a seguir mostra como criar um Spark DataFrame para ler a partir do Cosmos DB no PySpark.

```python
# Read Configuration
readConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_pcoll",
    "query_custom": "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'" // Optional
}

# Connect via azure-cosmosdb-spark to create Spark DataFrame
flights = spark.read.format(
    "com.microsoft.azure.cosmosdb.spark").options(**readConfig).load()
flights.count()
```

E o mesmo trecho de código em Scala:

```scala
// Import Necessary Libraries
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Read Configuration
val readConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_pcoll",
  "query_custom" -> "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'" // Optional
))

// Connect via azure-cosmosdb-spark to create Spark DataFrame
val flights = spark.read.cosmosDB(readConfig)
flights.count()
```

### <a name="batch-writes-to-cosmos-db"></a>Batch escreve para Cosmos DB

O trecho a seguir mostra como escrever um quadro de dados para Cosmos DB em PySpark.

```python
# Write configuration
writeConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_fromsea",
    "Upsert": "true"
}

# Write to Cosmos DB from the flights DataFrame
flights.write.format("com.microsoft.azure.cosmosdb.spark").options(
    **writeConfig).save()
```

E o mesmo trecho de código em Scala:

```scala
// Write configuration

val writeConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_fromsea",
  "Upsert" -> "true"
))

// Write to Cosmos DB from the flights DataFrame
import org.apache.spark.sql.SaveMode
flights.write.mode(SaveMode.Overwrite).cosmosDB(writeConfig)
```

### <a name="streaming-reads-from-cosmos-db"></a>Leituras de streaming do Cosmos DB

O trecho a seguir mostra como se conectar e ler no Feed de Alterações do Azure Cosmos DB.

```python
# Read Configuration
readConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_pcoll",
    "ReadChangeFeed": "true",
    "ChangeFeedQueryName": "Departure-Delays",
    "ChangeFeedStartFromTheBeginning": "false",
    "InferStreamSchema": "true",
    "ChangeFeedCheckpointLocation": "dbfs:/Departure-Delays"
}


# Open a read stream to the Cosmos DB Change Feed via azure-cosmosdb-spark to create Spark DataFrame
changes = (spark
           .readStream
           .format("com.microsoft.azure.cosmosdb.spark.streaming.CosmosDBSourceProvider")
           .options(**readConfig)
           .load())
```
E o mesmo trecho de código em Scala:

```scala
// Import Necessary Libraries
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Read Configuration
val readConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_pcoll",
  "ReadChangeFeed" -> "true",
  "ChangeFeedQueryName" -> "Departure-Delays",
  "ChangeFeedStartFromTheBeginning" -> "false",
  "InferStreamSchema" -> "true",
  "ChangeFeedCheckpointLocation" -> "dbfs:/Departure-Delays"
))

// Open a read stream to the Cosmos DB Change Feed via azure-cosmosdb-spark to create Spark DataFrame
val df = spark.readStream.format(classOf[CosmosDBSourceProvider].getName).options(readConfig).load()
```

### <a name="streaming-writes-to-cosmos-db"></a>Streaming escreve para Cosmos DB

O trecho a seguir mostra como escrever um quadro de dados para Cosmos DB em PySpark.

```python
# Write configuration
writeConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_fromsea",
    "Upsert": "true",
    "WritingBatchSize": "500",
    "CheckpointLocation": "/checkpointlocation_write1"
}

# Write to Cosmos DB from the flights DataFrame
changeFeed = (changes
              .writeStream
              .format("com.microsoft.azure.cosmosdb.spark.streaming.CosmosDBSinkProvider")
              .outputMode("append")
              .options(**writeconfig)
              .start())
```

E o mesmo trecho de código em Scala:

```scala
// Write configuration

val writeConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_fromsea",
  "Upsert" -> "true",
  "WritingBatchSize" -> "500",
  "CheckpointLocation" -> "/checkpointlocation_write1"
))

// Write to Cosmos DB from the flights DataFrame
df
.writeStream
.format(classOf[CosmosDBSinkProvider].getName)
.options(writeConfig)
.start()
```
Mais trechos e amostras de ponta a ponta, veja [Jupyter](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/notebooks).

## <a name="working-with-the-connector"></a><a name="bk_working_with_connector"></a>Trabalhando com o conector

Você pode construir o conector a partir da fonte no GitHub, ou baixar os frascos de uber da Maven nos links abaixo.

| Spark | Scala | Última versão |
|---|---|---|
| 2.4.0 | 2.11 | [azure-cosmosdb-spark_2.4.0_2.11_1.4.0](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11/1.4.0/jar)
| 2.3.0 | 2.11 | [azure-cosmosdb-spark_2.3.0_2.11_1.3.3](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.3.0_2.11/1.3.3/jar)
| 2.2.0 | 2.11 | [azure-cosmosdb-spark_2.2.0_2.11_1.1.1.1](https://search.maven.org/#artifactdetails%7Ccom.microsoft.azure%7Cazure-cosmosdb-spark_2.2.0_2.11%7C1.1.1%7Cjar)
| 2.1.0 | 2.11 | [azure-cosmosdb-spark_2.1.0_2.11_1.2.2](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.1.0_2.11/1.2.2/jar)

### <a name="using-databricks-notebooks"></a>Usando notebooks Databricks

Crie uma biblioteca usando seu espaço de trabalho Databricks seguindo a orientação no Guia de Tijolos de Dados do Azure > [use o conector Azure Cosmos DB Spark](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/cosmosdb-connector.html)

> [!NOTE]
> Nota: a página Use a página **Azure Cosmos DB Spark Connector** não está atualizada. Em vez de baixar os seis frascos separados em seis bibliotecas diferentes, você pode baixar o pote de uber do maven em https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11/1.4.0/jar) e instalar este pote/biblioteca.
> 

### <a name="using-spark-cli"></a>Usando faísca-cli

Para trabalhar com o conector usando o `spark-shell`cli-faísca (isto é, `pyspark`), `spark-submit`você pode usar o `--packages` parâmetro com as [coordenadas maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11)do conector .

```sh
spark-shell --master yarn --packages "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.4.0"

```

### <a name="using-jupyter-notebooks"></a>Usando cadernos Jupyter

Se você estiver usando notebooks Jupyter dentro do HDInsight, você pode usar uma célula mágica `%%configure` de faísca para especificar as coordenadas maven do conector.

```python
{ "name":"Spark-to-Cosmos_DB_Connector",
  "conf": {
    "spark.jars.packages": "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.4.0",
    "spark.jars.excludes": "org.scala-lang:scala-reflect"
   }
   ...
}
```

> Note que a `spark.jars.excludes` inclusão do é específico para remover potenciais conflitos entre o conector, Apache Spark e Livy.

### <a name="build-the-connector"></a>Construa o conector

Atualmente, este projeto `maven` de conector usa para construir sem dependências, você pode executar:

```sh
mvn clean package
```

## <a name="working-with-our-samples"></a>Trabalhando com nossas amostras

O [repositório Cosmos DB Spark GitHub](https://github.com/Azure/azure-cosmosdb-spark) tem os seguintes cadernos de exemplo e scripts que você pode experimentar.

* **Desempenho de voo on-time com Spark e Cosmos DB (Seattle)** [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.html): Conecte Spark ao Cosmos DB usando o serviço de notebook HDInsight Jupyter para mostrar Spark SQL, GraphFrames e prever atrasos de voo usando pipelines ML.
* **Fonte do Twitter com Apache Spark e Azure Cosmos DB Change Feed**: [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.html)
* **Usando o Apache Spark para consultar o Cosmos DB Graphs**: [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.html)
* **[Conectando a Azure Databricks ao Azure Cosmos DB](https://docs.databricks.com/spark/latest/data-sources/azure/cosmosdb-connector.html)** usando `azure-cosmosdb-spark`.  O Linked também é uma versão do Azure Databricks do [notebook On-Time Flight Performance](https://github.com/dennyglee/databricks/tree/master/notebooks/Users/denny%40databricks.com/azure-databricks).
* **[Arquitetura Lambda com Azure Cosmos DB e HDInsight (Apache Spark)](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/readme.md)**: Você pode reduzir a sobrecarga operacional de manter pipelines de big data usando Cosmos DB e Spark.

## <a name="more-information"></a>Mais informações

Temos mais informações `azure-cosmosdb-spark` na [wiki,](https://github.com/Azure/azure-cosmosdb-spark/wiki) incluindo:

* [Guia do usuário do conector de faísca sinuoso do Azure Cosmos DB](https://github.com/Azure/azure-documentdb-spark/wiki/Azure-Cosmos-DB-Spark-Connector-User-Guide)
* [Exemplos de agregações](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples)

### <a name="configuration-and-setup"></a>Configuração e instalação

* [Configuração do conector de faíscas](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuration-references)
* [Configuração do conector Cosmos DB](https://github.com/Azure/azure-documentdb-spark/wiki/Spark-to-Cosmos-DB-Connector-Setup) (Em andamento)
* [Configurando a consulta direta do Power BI para o Azure Cosmos DB via Apache Spark (HDI)](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuring-Power-BI-Direct-Query-to-Azure-Cosmos-DB-via-Apache-Spark-(HDI))

### <a name="troubleshooting"></a>Solução de problemas

* [Usando agregados Cosmos DB](https://github.com/Azure/azure-documentdb-spark/wiki/Troubleshooting:-Using-Cosmos-DB-Aggregates)
* [Problemas Conhecidos](https://github.com/Azure/azure-cosmosdb-spark/wiki/Known-Issues)

### <a name="performance"></a>Desempenho

* [Dicas de desempenho](https://github.com/Azure/azure-cosmosdb-spark/wiki/Performance-tips)
* [Executa o teste de consulta](https://github.com/Azure/azure-documentdb-spark/wiki/Query-Test-Runs)
* [Executação de testes de escrita](https://github.com/Azure/azure-cosmosdb-spark/wiki/Writing-Test-Runs)

### <a name="change-feed"></a>Feed de alterações

* [Alterações de processamento de fluxo usando o Azure Cosmos DB Change Feed e Apache Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)
* [Alterar demonstrações de feed](https://github.com/Azure/azure-cosmosdb-spark/wiki/Change-Feed-demos)
* [Demonstrações estruturadas do córrego](https://github.com/Azure/azure-cosmosdb-spark/wiki/Structured-Stream-demos)

### <a name="monitoring"></a>Monitoramento

* [Monitorando trabalhos da Spark com insights de aplicativos](https://github.com/Azure/azure-cosmosdb-spark/tree/2.3/samples/monitoring)

## <a name="next-steps"></a>Próximas etapas

Se você ainda não tiver feito isso, faça o download do conector do Spark para o Azure Cosmos DB no repositório [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark) do GitHub. Explore os recursos adicionais no repositório a seguir:

* [Exemplos de agregações](https://github.com/Azure/azure-cosmosdb-spark/wiki/Aggregations-Examples)
* [Exemplos de scripts e cadernos](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)

Você também pode revisar o [Apache Spark SQL, o DataFrames e o Guia de Conjuntos de Dados](https://spark.apache.org/docs/latest/sql-programming-guide.html) e o artigo [Apache Spark no Azure HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md).
