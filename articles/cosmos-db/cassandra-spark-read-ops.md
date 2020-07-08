---
title: Leia os dados da tabela de API do Apache Cassandra usando Apache Spark
titleSufix: Azure Cosmos DB
description: Este artigo descreve como ler dados de tabelas de API do Cassandra no Azure Cosmos DB.
author: TheovanKraay
ms.author: thvankra
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 06/02/2020
ms.custom: seodec18
ms.openlocfilehash: 4ecb7758ee5f58345fccc2c490cee4d23043a20c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85257407"
---
# <a name="read-data-from-azure-cosmos-db-cassandra-api-tables-using-spark"></a>Ler dados das tabelas de API do Cassandra do Azure Cosmos DB usando Spark

 Este artigo descreve como ler dados armazenados na API do Azure Cosmos DB Cassandra do Spark.

## <a name="cassandra-api-configuration"></a>Configuração da API do Cassandra
```scala
import org.apache.spark.sql.cassandra._
//Spark connector
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql.CassandraConnector

//CosmosDB library for multiple retry
import com.microsoft.azure.cosmosdb.cassandra

//Connection-related
spark.conf.set("spark.cassandra.connection.host","YOUR_ACCOUNT_NAME.cassandra.cosmosdb.azure.com")
spark.conf.set("spark.cassandra.connection.port","10350")
spark.conf.set("spark.cassandra.connection.ssl.enabled","true")
spark.conf.set("spark.cassandra.auth.username","YOUR_ACCOUNT_NAME")
spark.conf.set("spark.cassandra.auth.password","YOUR_ACCOUNT_KEY")
spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")
//Throughput-related...adjust as needed
spark.conf.set("spark.cassandra.output.batch.size.rows", "1")
spark.conf.set("spark.cassandra.connection.connections_per_executor_max", "10")
spark.conf.set("spark.cassandra.output.concurrent.writes", "1000")
spark.conf.set("spark.cassandra.concurrent.reads", "512")
spark.conf.set("spark.cassandra.output.batch.grouping.buffer.size", "1000")
spark.conf.set("spark.cassandra.connection.keep_alive_ms", "600000000")
```
## <a name="dataframe-api"></a>API de Dataframe

### <a name="read-table-using-sessionreadformat-command"></a>Leia a tabela usando o comando session.read.format

```scala
val readBooksDF = sqlContext
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load

readBooksDF.explain
readBooksDF.show
```
### <a name="read-table-using-sparkreadcassandraformat"></a>Leia a tabela usando spark.read.cassandraForma 

```scala
val readBooksDF = spark.read.cassandraFormat("books", "books_ks", "").load()
```

### <a name="read-specific-columns-in-table"></a>Colunas específicas de leitura na tabela

```scala
val readBooksDF = spark
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load
  .select("book_name","book_author", "book_pub_year")

readBooksDF.printSchema
readBooksDF.explain
readBooksDF.show
```

### <a name="apply-filters"></a>Aplicar filtros

Você pode enviar predicados para o banco de dados para permitir melhores consultas do Spark otimizadas. Um predicado é uma condição em uma consulta que retorna true ou false, normalmente localizada na cláusula WHERE. Uma ação de predicado filtra os dados na consulta do banco de dados, reduzindo o número de entradas recuperadas do banco e melhorando o desempenho da consulta. Por padrão, a API do conjunto de dados do Spark enviará automaticamente cláusulas de WHERE válidas para o Database. 

```scala
val readBooksDF = spark
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load
  .select("book_name","book_author", "book_pub_year")
  .filter("book_pub_year > 1891")
//.filter("book_name IN ('A sign of four','A study in scarlet')")
//.filter("book_name='A sign of four' OR book_name='A study in scarlet'")
//.filter("book_author='Arthur Conan Doyle' AND book_pub_year=1890")
//.filter("book_pub_year=1903")  

readBooksDF.printSchema
readBooksDF.explain
readBooksDF.show
```

A seção PushedFilters do plano físico inclui o filtro GreaterThan Push Down. 

:::image type="content" source="./media/cassandra-spark-read-ops/pushdown-predicates.png" alt-text="Elas":::

## <a name="rdd-api"></a>API DE RDD

### <a name="read-table"></a>Tabela de leitura
```scala
val bookRDD = sc.cassandraTable("books_ks", "books")
bookRDD.take(5).foreach(println)
```

### <a name="read-specific-columns-in-table"></a>Colunas específicas de leitura na tabela

```scala
val booksRDD = sc.cassandraTable("books_ks", "books").select("book_id","book_name").cache
booksRDD.take(5).foreach(println)
```

## <a name="sql-views"></a>Exibições do SQL 

### <a name="create-a-temporary-view-from-a-dataframe"></a>Criar um modo de exibição temporário de um dataframe

```scala
spark
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load.createOrReplaceTempView("books_vw")
```

### <a name="run-queries-against-the-view"></a>Executar consultas no modo de exibição

```sql
select * from books_vw where book_pub_year > 1891
```

## <a name="next-steps"></a>Próximas etapas

A seguir estão os artigos adicionais sobre como trabalhar com a API Cassandra do Azure Cosmos DB do Spark:
 
 * [Operações de Upsert](cassandra-spark-upsert-ops.md)
 * [Excluir operações](cassandra-spark-delete-ops.md)
 * [Operações de agregação](cassandra-spark-aggregation-ops.md)
 * [Operações de cópia de tabela](cassandra-spark-table-copy-ops.md)

