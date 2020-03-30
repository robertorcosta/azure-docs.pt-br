---
title: Migrar dados relacionais de um a poucos para a API AQS Do Azure Cosmos DB SQL
description: Saiba como lidar com a migração de dados complexos para relacionamentos únicos na API SQL
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: thvankra
ms.openlocfilehash: 467e9627a2623779bd808ca5aebdf76d8a5eda42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75896630"
---
# <a name="migrate-one-to-few-relational-data-into-azure-cosmos-db-sql-api-account"></a>Migrar dados relacionais de um a poucos para a conta API Do Azure Cosmos DB SQL

Para migrar de um banco de dados relacional para a API Aql AQS do Azure Cosmos DB, pode ser necessário fazer alterações no modelo de dados para otimização.

Uma transformação comum é a desnormalização de dados incorporando subitens relacionados em um documento JSON. Aqui vamos ver algumas opções para isso usando a Fábrica de Dados Azure ou a Azure Databricks. Para obter orientações gerais sobre modelagem de dados para Cosmos DB, por favor, [revise a modelagem de dados no Azure Cosmos DB](modeling-data.md).  

## <a name="example-scenario"></a>Cenário de Exemplo

Suponha que temos as duas tabelas a seguir em nosso banco de dados SQL, Pedidos e Detalhes de Pedidos.


![Order Details](./media/migrate-relational-to-cosmos-sql-api/orders.png)

Queremos combinar essa relação de um a poucos em um documento JSON durante a migração. Para fazer isso, podemos criar uma consulta T-SQL usando "FOR JSON" como abaixo:

```sql
SELECT
  o.OrderID,
  o.OrderDate,
  o.FirstName,
  o.LastName,
  o.Address,
  o.City,
  o.State,
  o.PostalCode,
  o.Country,
  o.Phone,
  o.Total,
  (select OrderDetailId, ProductId, UnitPrice, Quantity from OrderDetails od where od.OrderId = o.OrderId for json auto) as OrderDetails
FROM Orders o;
```

Os resultados desta consulta ficariam abaixo: 

![Order Details](./media/migrate-relational-to-cosmos-sql-api/for-json-query-result.png#lightbox)


Idealmente, você deseja usar uma única atividade de cópia do Azure Data Factory (ADF) para consultar dados SQL como fonte e escrever a saída diretamente para o Azure Cosmos DB afundar como objetos JSON apropriados. Atualmente, não é possível realizar a transformação JSON necessária em uma atividade de cópia. Se tentarmos copiar os resultados da consulta acima em um contêiner Azure Cosmos DB SQL API, veremos o campo OrderDetails como uma propriedade de string do nosso documento, em vez da matriz JSON esperada.

Podemos contornar essa limitação atual de uma das seguintes maneiras:

* **Use a Fábrica de Dados Do Azure com duas atividades de cópia:** 
  1. Obtenha dados formatados por JSON do SQL para um arquivo de texto em um local intermediário de armazenamento blob e 
  2. Carregar dados do arquivo de texto JSON para um contêiner no Azure Cosmos DB.

* **Use a Azure Databricks para ler do SQL e escrever para o Azure Cosmos DB** - apresentaremos duas opções aqui.


Vamos olhar essas abordagens com mais detalhes:

## <a name="azure-data-factory"></a>Fábrica de dados do Azure

Embora não possamos incorporar orderDetails como uma matriz JSON no documento Design DB do Destino, podemos contornar o problema usando duas atividades de cópia separadas.

### <a name="copy-activity-1-sqljsontoblobtext"></a>#1 de atividade de cópia: SqlJsonToBlobText

Para os dados de origem, usamos uma consulta SQL para obter o resultado definido como uma única coluna com um objeto JSON (representando a Ordem) por linha usando os recursos SQL Server OPENJSON e FOR JSON PATH:

```sql
SELECT [value] FROM OPENJSON(
  (SELECT
    id = o.OrderID,
    o.OrderDate,
    o.FirstName,
    o.LastName,
    o.Address,
    o.City,
    o.State,
    o.PostalCode,
    o.Country,
    o.Phone,
    o.Total,
    (select OrderDetailId, ProductId, UnitPrice, Quantity from OrderDetails od where od.OrderId = o.OrderId for json auto) as OrderDetails
   FROM Orders o FOR JSON PATH)
)
```

![Cópia do ADF](./media/migrate-relational-to-cosmos-sql-api/adf1.png)


Para o dissipador da atividade de cópia sqlJsonToBlobText, escolhemos "Texto delimitado" e o apontamos para uma pasta específica@concatno Azure Blob Storage com um nome de arquivo exclusivo gerado dinamicamente (por exemplo, ' (pipeline(). RunId,'.json').
Como nosso arquivo de texto não é realmente "delimitado" e não queremos que ele seja analisado em colunas separadas usando vírgulas e queremos preservar as aspas duplas ("), definimos "Delimitador de coluna" para uma Guia ("\t") - ou outro caractere que não ocorra nos dados - e "Citar caractere" para "Sem caráter de citação".

![Cópia do ADF](./media/migrate-relational-to-cosmos-sql-api/adf2.png)

### <a name="copy-activity-2-blobjsontocosmos"></a>Atividade de cópia #2: BlobJsonToCosmos

Em seguida, modificamos nosso pipeline ADF adicionando a segunda Atividade de Cópia que parece no Azure Blob Storage para o arquivo de texto que foi criado pela primeira atividade. Ele processa-o como fonte "JSON" para inserir no dissipador Cosmos DB como um documento por linha JSON encontrado no arquivo de texto.

![Cópia do ADF](./media/migrate-relational-to-cosmos-sql-api/adf3.png)

Opcionalmente, também adicionamos uma atividade "Excluir" ao pipeline para que ele exclua todos os arquivos anteriores restantes na pasta /Orders/antes de cada execução. Nosso oleoduto ADF agora se parece com algo assim:

![Cópia do ADF](./media/migrate-relational-to-cosmos-sql-api/adf4.png)

Depois de acionar o pipeline acima, vemos um arquivo criado em nosso local intermediário de armazenamento Azure Blob contendo um objeto JSON por linha:

![Cópia do ADF](./media/migrate-relational-to-cosmos-sql-api/adf5.png)

Também vemos documentos de pedidos com detalhes de pedidos devidamente incorporados inseridos em nossa coleção Cosmos DB:

![Cópia do ADF](./media/migrate-relational-to-cosmos-sql-api/adf6.png)


## <a name="azure-databricks"></a>Azure Databricks

Também podemos usar o Spark in [Azure Databricks](https://azure.microsoft.com/services/databricks/) para copiar os dados da nossa fonte de banco de dados SQL para o destino Azure Cosmos DB sem criar os arquivos intermediários de texto/JSON no Azure Blob Storage. 

> [!NOTE]
> Para clareza e simplicidade, os trechos de código abaixo incluem senhas de banco de dados de texto explicitamente inline, mas você deve sempre usar segredos do Azure Databricks.
>

Primeiro, criamos e anexamos o [conector SQL](https://docs.databricks.com/data/data-sources/sql-databases-azure.html) necessário e as [bibliotecas de conectores Azure Cosmos DB](https://docs.databricks.com/data/data-sources/azure/cosmosdb-connector.html) ao nosso cluster Azure Databricks. Reinicie o cluster para garantir que as bibliotecas estejam carregadas.

![Databricks](./media/migrate-relational-to-cosmos-sql-api/databricks1.png)

Em seguida, apresentamos duas amostras, para Scala e Python. 

### <a name="scala"></a>Scala
Aqui, obtemos os resultados da consulta SQL com a saída "FOR JSON" em um DataFrame:

```scala
// Connect to Azure SQL https://docs.databricks.com/data/data-sources/sql-databases-azure.html
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._
val configSql = Config(Map(
  "url"          -> "xxxx.database.windows.net",
  "databaseName" -> "xxxx",
  "queryCustom"  -> "SELECT o.OrderID, o.OrderDate, o.FirstName, o.LastName, o.Address, o.City, o.State, o.PostalCode, o.Country, o.Phone, o.Total, (SELECT OrderDetailId, ProductId, UnitPrice, Quantity FROM OrderDetails od WHERE od.OrderId = o.OrderId FOR JSON AUTO) as OrderDetails FROM Orders o",
  "user"         -> "xxxx", 
  "password"     -> "xxxx" // NOTE: For clarity and simplicity, this example includes secrets explicitely as a string, but you should always use Databricks secrets
))
// Create DataFrame from Azure SQL query
val orders = sqlContext.read.sqlDB(configSql)
display(orders)
```

![Databricks](./media/migrate-relational-to-cosmos-sql-api/databricks2.png)

Em seguida, nos conectamos ao nosso banco de dados e coleção Cosmos DB:

```scala
// Connect to Cosmos DB https://docs.databricks.com/data/data-sources/azure/cosmosdb-connector.html
import org.joda.time._
import org.joda.time.format._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.CosmosDBSpark
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.apache.spark.sql.functions._
import org.joda.time._
import org.joda.time.format._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.CosmosDBSpark
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.apache.spark.sql.functions._
val configMap = Map(
  "Endpoint" -> "https://xxxx.documents.azure.com:443/",
  // NOTE: For clarity and simplicity, this example includes secrets explicitely as a string, but you should always use Databricks secrets
  "Masterkey" -> "xxxx",
  "Database" -> "StoreDatabase",
  "Collection" -> "Orders")
val configCosmos = Config(configMap)
```

Finalmente, definimos nosso esquema e usamos from_json para aplicar o DataFrame antes de salvá-lo na coleção CosmosDB.

```scala
// Convert DataFrame to proper nested schema
import org.apache.spark.sql.types._
val orderDetailsSchema = ArrayType(StructType(Array(
    StructField("OrderDetailId",IntegerType,true),
    StructField("ProductId",IntegerType,true),
    StructField("UnitPrice",DoubleType,true),
    StructField("Quantity",IntegerType,true)
  )))
val ordersWithSchema = orders.select(
  col("OrderId").cast("string").as("id"),
  col("OrderDate").cast("string"),
  col("FirstName").cast("string"),
  col("LastName").cast("string"),
  col("Address").cast("string"),
  col("City").cast("string"),
  col("State").cast("string"),
  col("PostalCode").cast("string"),
  col("Country").cast("string"),
  col("Phone").cast("string"),
  col("Total").cast("double"),
  from_json(col("OrderDetails"), orderDetailsSchema).as("OrderDetails")
)
display(ordersWithSchema)
// Save nested data to Cosmos DB
CosmosDBSpark.save(ordersWithSchema, configCosmos)
```

![Databricks](./media/migrate-relational-to-cosmos-sql-api/databricks3.png)


### <a name="python"></a>Python

Como uma abordagem alternativa, você pode precisar executar transformações JSON no Spark (se o banco de dados de origem não suportar "FOR JSON" ou uma operação semelhante), ou você pode querer usar operações paralelas para um conjunto de dados muito grande. Aqui apresentamos uma amostra de PySpark. Comece configurando as conexões de banco de dados de origem e destino na primeira célula:

```python
import uuid
import pyspark.sql.functions as F
from pyspark.sql.functions import col
from pyspark.sql.types import StringType,DateType,LongType,IntegerType,TimestampType
 
#JDBC connect details for SQL Server database
jdbcHostname = "jdbcHostname"
jdbcDatabase = "OrdersDB"
jdbcUsername = "jdbcUsername"
jdbcPassword = "jdbcPassword"
jdbcPort = "1433"
 
connectionProperties = {
  "user" : jdbcUsername,
  "password" : jdbcPassword,
  "driver" : "com.microsoft.sqlserver.jdbc.SQLServerDriver"
}
jdbcUrl = "jdbc:sqlserver://{0}:{1};database={2};user={3};password={4}".format(jdbcHostname, jdbcPort, jdbcDatabase, jdbcUsername, jdbcPassword)
 
#Connect details for Target Azure Cosmos DB SQL API account
writeConfig = {
    "Endpoint": "Endpoint",
    "Masterkey": "Masterkey",
    "Database": "OrdersDB",
    "Collection": "Orders",
    "Upsert": "true"
}
```

Em seguida, consultaremos o banco de dados de origem (neste caso o SQL Server) para os registros de detalhes de ordem e pedido, colocando os resultados em Spark Dataframes. Também criaremos uma lista contendo todos os IDs de pedidos e um pool de threads para operações paralelas:

```python
import json
import ast
import pyspark.sql.functions as F
import uuid
import numpy as np
import pandas as pd
from functools import reduce
from pyspark.sql import SQLContext
from pyspark.sql.types import *
from pyspark.sql import *
from pyspark.sql.functions import exp
from pyspark.sql.functions import col
from pyspark.sql.functions import lit
from pyspark.sql.functions import array
from pyspark.sql.types import *
from multiprocessing.pool import ThreadPool
 
#get all orders
orders = sqlContext.read.jdbc(url=jdbcUrl, table="orders", properties=connectionProperties)
 
#get all order details
orderdetails = sqlContext.read.jdbc(url=jdbcUrl, table="orderdetails", properties=connectionProperties)
 
#get all OrderId values to pass to map function 
orderids = orders.select('OrderId').collect()
 
#create thread pool big enough to process merge of details to orders in parallel
pool = ThreadPool(10)
```

Em seguida, crie uma função para escrever Ordens na coleção de API SQL de destino. Esta função filtrará todos os detalhes do pedido para o ID da ordem dada, converte-os em uma matriz JSON e inserirá o array em um documento JSON que escreveremos na Coleção de API SQL de destino para essa ordem:

```python
def writeOrder(orderid):
  #filter the order on current value passed from map function
  order = orders.filter(orders['OrderId'] == orderid[0])
  
  #set id to be a uuid
  order = order.withColumn("id", lit(str(uuid.uuid1())))
  
  #add details field to order dataframe
  order = order.withColumn("details", lit(''))
  
  #filter order details dataframe to get details we want to merge into the order document
  orderdetailsgroup = orderdetails.filter(orderdetails['OrderId'] == orderid[0])
  
  #convert dataframe to pandas
  orderpandas = order.toPandas()
  
  #convert the order dataframe to json and remove enclosing brackets
  orderjson = orderpandas.to_json(orient='records', force_ascii=False)
  orderjson = orderjson[1:-1] 
  
  #convert orderjson to a dictionaory so we can set the details element with order details later
  orderjsondata = json.loads(orderjson)
  
  
  #convert orderdetailsgroup dataframe to json, but only if details were returned from the earlier filter
  if (orderdetailsgroup.count() !=0):
    #convert orderdetailsgroup to pandas dataframe to work better with json
    orderdetailsgroup = orderdetailsgroup.toPandas()
    
    #convert orderdetailsgroup to json string
    jsonstring = orderdetailsgroup.to_json(orient='records', force_ascii=False)
    
    #convert jsonstring to dictionary to ensure correct encoding and no corrupt records
    jsonstring = json.loads(jsonstring)
    
    #set details json element in orderjsondata to jsonstring which contains orderdetailsgroup - this merges order details into the order 
    orderjsondata['details'] = jsonstring
 
  #convert dictionary to json
  orderjsondata = json.dumps(orderjsondata)
 
  #read the json into spark dataframe
  df = spark.read.json(sc.parallelize([orderjsondata]))
  
  #write the dataframe (this will be a single order record with merged many-to-one order details) to cosmos db using spark the connector
  #https://docs.microsoft.com/azure/cosmos-db/spark-connector
  df.write.format("com.microsoft.azure.cosmosdb.spark").mode("append").options(**writeConfig).save()
```

Finalmente, chamaremos o acima usando uma função de mapa no pool de segmentos, para executar em paralelo, passando na lista de IDs de ordem que criamos anteriormente:

```python
#map order details to orders in parallel using the above function
pool.map(writeOrder, orderids)
```
Em ambas as aproximações, no final, devemos obter detalhes de ordem incorporados devidamente salvos dentro de cada documento da Ordem na coleção Cosmos DB:

![Databricks](./media/migrate-relational-to-cosmos-sql-api/databricks4.png)

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre [modelagem de dados no Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/modeling-data)
* Saiba [como modelar e particionar dados no Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/how-to-model-partition-example)
