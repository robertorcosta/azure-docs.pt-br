---
title: Consultar Repositório Analítico do Azure Cosmos DB (versão prévia) com o Apache Spark para Azure Synapse Analytics
description: Como consultar análise do Azure Cosmos DB com Apache Spark para Azure Synapse Analytics
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 05/06/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 5c19a3f808b85ba9e34d3304251fe8acb21204ce
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83700193"
---
# <a name="query-azure-cosmos-db-analytical-store-preview-with-apache-spark-for-azure-synapse-analytics"></a>Consultar Repositório Analítico do Azure Cosmos DB (versão prévia) com o Apache Spark para Azure Synapse Analytics

Este artigo fornece alguns exemplos de como você pode interagir com o repositório analítico usando gestos do Synapse. Os gestos são vistos quando você clica com o botão direito do mouse em um contêiner. Com gestos, você pode gerar código rapidamente e ajustá-lo às suas necessidades. Os gestos também são perfeitos para descobrir dados com um único clique.

## <a name="load-to-dataframe"></a>Carregar em DataFrame

Nesta etapa, você vai ler dados do repositório analítico do Azure Cosmos DB em um DataFrame do Spark. Ele exibirá 10 linhas do DataFrame chamado ***df***. Depois que os dados estiverem no dataframe, você poderá executar análises adicionais.

Esta operação não afeta o repositório transacional.

```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

df = spark.read.format("cosmos.olap")\
    .option("spark.synapse.linkedService", "INFERRED")\
    .option("spark.cosmos.container", "INFERRED")\
    .load()

df.show(10)
```

O gesto na **Escala** seria equivalente ao seguinte código:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val df_olap = spark.read.format("cosmos.olap").
    option("spark.synapse.linkedService", "pySparkSamplesDb").
    option("spark.cosmos.container", "trafficSourceColl").
    load()
```

## <a name="create-spark-table"></a>Criar tabela do Spark

Nesse gesto, você criará uma tabela do Spark apontando para o contêiner selecionado. Nessa operação, não há movimentação de dados. Se você decidir excluir essa tabela, o contêiner subjacente (e o repositório analítico correspondente) não será afetado. 

Esse cenário é conveniente para reutilizar tabelas por meio de ferramentas de terceiros e fornecer acessibilidade aos dados para o tempo de execução.

```sql
%%sql
-- To select a preferred list of regions in a multi-region Azure Cosmos DB account, add spark.cosmos.preferredRegions '<Region1>,<Region2>' in the config options

create table call_center using cosmos.olap options (
    spark.synapse.linkedService 'INFERRED',
    spark.cosmos.container 'INFERRED'
)
```

## <a name="write-dataframe-to-container"></a>Gravar DataFrame no contêiner

Nesse gesto, você gravará um dataframe em um contêiner. Esta operação afetará o desempenho transacional e consumirá Unidades de Solicitação. Usar o desempenho transacional do Azure Cosmos DB é ideal nas transações de gravação. Certifique-se de substituir **YOURDATAFRAME** pelo dataframe para o qual você deseja fazer write-back.

```python
# Write a Spark DataFrame into an Azure Cosmos DB container
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")


YOURDATAFRAME.write.format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "INFERRED")\
    .option("spark.cosmos.container", "INFERRED")\
    .option("spark.cosmos.write.upsertEnabled", "true")\
    .mode('append')\
    .save()
```

O gesto na **Escala** seria equivalente ao seguinte código:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

import org.apache.spark.sql.SaveMode

df.write.format("cosmos.oltp").
    option("spark.synapse.linkedService", "pySparkSamplesDb").
    option("spark.cosmos.container", "trafficSourceColl"). 
    option("spark.cosmos.write.upsertEnabled", "true").
    mode(SaveMode.Overwrite).
    save()
```

## <a name="load-streaming-dataframe-from-container"></a>Carregar DataFrame de streaming do contêiner
Nesse gesto, você usará o recurso de streaming do Spark para carregar dados de um contêiner para um dataframe. Os dados serão armazenados na conta principal do data lake (e sistema de arquivos) que você conectou ao workspace. 

Se a pasta */localReadCheckpointFolder* não foi criada, ela será criada automaticamente. Esta operação afetará o desempenho transacional do Azure Cosmos DB.

```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

dfStream = spark.readStream\
    .format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "INFERRED")\
    .option("spark.cosmos.container", "INFERRED")\
    .option("spark.cosmos.changeFeed.readEnabled", "true")\
    .option("spark.cosmos.changeFeed.startFromTheBeginning", "true")\
    .option("spark.cosmos.changeFeed.checkpointLocation", "/localReadCheckpointFolder")\
    .option("spark.cosmos.changeFeed.queryName", "streamQuery")\
    .load()
```

O gesto na **Escala** seria equivalente ao seguinte código:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val dfStream = spark.readStream.
    format("cosmos.oltp").
    option("spark.synapse.linkedService", "pySparkSamplesDb").
    option("spark.cosmos.container", "trafficSourceColl").
    option("spark.cosmos.changeFeed.readEnabled", "true").
    option("spark.cosmos.changeFeed.startFromTheBeginning", "true").
    option("spark.cosmos.changeFeed.checkpointLocation", "/localReadCheckpointFolder").
    option("spark.cosmos.changeFeed.queryName", "streamTestRevin2").
    load()
```

## <a name="write-streaming-dataframe-to-container"></a>Gravar DataFrame de streaming no contêiner
Nesse gesto, você gravará um dataframe de streaming no contêiner selecionado do Azure Cosmos DB. Se a pasta */localReadCheckpointFolder* não foi criada, ela será criada automaticamente. Esta operação afetará o desempenho transacional do Azure Cosmos DB.

```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

streamQuery = dfStream\
        .writeStream\
        .format("cosmos.oltp")\
        .outputMode("append")\
        .option("checkpointLocation", "/localWriteCheckpointFolder")\
        .option("spark.synapse.linkedService", "INFERRED")\
        .option("spark.cosmos.container", "trafficSourceColl_sink")\
        .option("spark.cosmos.connection.mode", "gateway")\
        .start()

streamQuery.awaitTermination()
```

O gesto na **Escala** seria equivalente ao seguinte código:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val query = dfStream.
            writeStream.
            format("cosmos.oltp").
            outputMode("append").
            option("checkpointLocation", "/localWriteCheckpointFolder").
            option("spark.synapse.linkedService", "pySparkSamplesDb").
            option("spark.cosmos.container", "test2").
            option("spark.cosmos.connection.mode", "gateway").
            start()

query.awaitTermination()
```
## <a name="next-steps"></a>Próximas etapas

* [Saiba o que é compatível entre o Synapse e o Azure Cosmos DB](./concept-synapse-link-cosmos-db-support.md)
* [Conectar-se ao Link do Azure Synapse para Azure Cosmos DB](../quickstart-connect-synapse-link-cosmos-db.md)
