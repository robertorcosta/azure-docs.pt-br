---
title: Copiar os dados do Link do Synapse para Azure Cosmos DB para um pool de SQL usando o Apache Spark
description: Carregar os dados em um dataframe do Spark, coletá-los e carregá-los em uma tabela do pool de SQL
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: synapse-link
ms.date: 08/10/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 88962d63519cfeb78be694c4f702b05ed4e7d3df
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88658312"
---
# <a name="copy-data-from-azure-cosmos-db-into-a-sql-pool-using-apache-spark"></a>Copiar dados do Azure Cosmos DB para um pool de SQL usando o Apache Spark

O Link do Azure Synapse para Azure Cosmos DB permite que os usuários executem análises quase em tempo real nos dados operacionais no Azure Cosmos DB. No entanto, há ocasiões em que alguns dados precisam ser agregados e enriquecidos para atender aos usuários do data warehouse. A coleta e a exportação de dados do Link do Synapse podem ser feitas com apenas algumas células em um notebook.

## <a name="prerequisites"></a>Pré-requisitos
* [Provisionar um workspace do Synapse](../quickstart-create-workspace.md) com:
    * [Pool do Spark](../quickstart-create-apache-spark-pool-studio.md)
    * [Pool de SQL](../quickstart-create-sql-pool-studio.md)
* [Provisionar uma conta do Cosmos DB com um contêiner HTAP com alguns dados](../../cosmos-db/configure-synapse-link.md)
* [Conectar o contêiner HTAP do Azure Cosmos DB ao workspace](./how-to-connect-synapse-link-cosmos-db.md)
* [Ter a configuração correta para importar dados para um pool de SQL do Spark](../spark/synapse-spark-sql-pool-import-export.md)

## <a name="steps"></a>Etapas
Neste tutorial, você se conectará ao repositório analítico para que não haja nenhum impacto no repositório transacional (ele não consumirá nenhuma Unidade de Solicitação). Passaremos pelas seguintes etapas:
1. Ler o contêiner HTAP do Cosmos DB em um dataframe do Spark
2. Agregar os resultados em um novo dataframe
3. Ingerir os dados em um pool de SQL

[![Etapas do Spark para o SQL](../media/synapse-link-spark-to-sql/synapse-spark-to-sql.png)](../media/synapse-link-spark-to-sql/synapse-spark-to-sql.png#lightbox)

## <a name="data"></a>Dados
Nesse exemplo, usamos um contêiner HTAP chamado **RetailSales**. Ele faz parte de um serviço vinculado chamado **ConnectedData** e tem o seguinte esquema:
* _rid: string (nullable = true)
* _ts: long (nullable = true)
* logQuantity: double (nullable = true)
* productCode: string (nullable = true)
* quantity: long (nullable = true)
* price: long (nullable = true)
* id: string (nullable = true)
* advertising: long (nullable = true)
* storeId: long (nullable = true)
* weekStarting: long (nullable = true)
* _etag: string (nullable = true)

Agregaremos as vendas (*quantity*, *revenue* – preço x quantidade) por *productCode* e *weekStarting* para fins de relatório. Por fim, exportaremos esses dados para uma tabela do pool de SQL chamada **dbo.productsales**.

## <a name="configure-a-spark-notebook"></a>Configurar um notebook do Spark
Crie um notebook do Spark com o Scala tendo o Spark (Scala) como a linguagem principal. Usaremos a configuração padrão do notebook para a sessão.

## <a name="read-the-data-in-spark"></a>Ler os dados no Spark
Leia o contêiner HTAP do Cosmos DB com o Spark em um dataframe na primeira célula.

```java
val df_olap = spark.read.format("cosmos.olap").
    option("spark.synapse.linkedService", "ConnectedData").
    option("spark.cosmos.container", "RetailSales").
    load()
```

## <a name="aggregate-the-results-in-a-new-dataframe"></a>Agregar os resultados em um novo dataframe

Na segunda célula, executaremos a transformação e as agregações necessárias para o novo dataframe antes de carregá-lo em um banco de dados do pool de SQL.

```java
// Select relevant columns and create revenue
val df_olap_step1 = df_olap.select("productCode","weekStarting","quantity","price").withColumn("revenue",col("quantity")*col("price"))
//Aggregate revenue, quantity sold and avg. price by week and product ID
val df_olap_aggr = df_olap_step1.groupBy("productCode","weekStarting").agg(sum("quantity") as "Sum_quantity",sum("revenue") as "Sum_revenue").
    withColumn("AvgPrice",col("Sum_revenue")/col("Sum_quantity"))
```

## <a name="load-the-results-into-a-sql-pool"></a>Carregar os resultados em um pool de SQL

Na terceira célula, carregaremos os dados em um pool de SQL. Ele criará automaticamente uma tabela externa temporária, uma fonte de dados externa e um formato de arquivo externo que será excluído quando o trabalho for concluído.

```java
df_olap_aggr.write.sqlanalytics("arnaudpool.dbo.productsales", Constants.INTERNAL)
```

## <a name="query-the-results-with-sql"></a>Consultar os resultados com o SQL

Consulte o resultado usando uma consulta SQL simples, como o seguinte script SQL:
```sql
SELECT  [productCode]
,[weekStarting]
,[Sum_quantity]
,[Sum_revenue]
,[AvgPrice]
 FROM [dbo].[productsales]
```

Sua consulta apresentará os seguintes resultados em um modo de gráfico: [![Etapas do Spark para o SQL](../media/synapse-link-spark-to-sql/sql-script-spark-sql.png)](../media/synapse-link-spark-to-sql/sql-script-spark-sql.png#lightbox)

## <a name="next-steps"></a>Próximas etapas
* [Consultar o repositório analítico do Azure Cosmos DB com o Apache Spark](./how-to-query-analytical-store-spark.md)