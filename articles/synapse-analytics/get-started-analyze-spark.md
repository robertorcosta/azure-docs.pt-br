---
title: 'Tutorial: Introdução à análise com o Spark'
description: Neste tutorial, você aprenderá a analisar dados com o Apache Spark
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: ebcec3907e40a8ba58aab841cd788c58ec7a94fe
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/11/2020
ms.locfileid: "90017907"
---
# <a name="analyze-with-apache-spark"></a>Análise com o Apache Spark

## <a name="analyze-nyc-taxi-data-in-blob-storage--using-spark"></a>Analisar os dados de Táxi de Nova York no armazenamento de blobs usando o Spark

Neste tutorial, você conhecerá as etapas básicas usadas para carregar e analisar dados com o Apache Spark para o Azure Synapse.

1. No hub **Dados** em **Vinculado**, clique com o botão direito do mouse em **Armazenamento de Blobs do Azure > Conjuntos de Dados de Exemplo > nyc_tlc_yellow** e selecione **Selecionar novo notebook**
1. Isso criará um Notebook com o seguinte código:
    ```
    from azureml.opendatasets import NycTlcYellow

    data = NycTlcYellow()
    data_df = data.to_spark_dataframe()
    display(data_df.limit(10))
    ```
1. No notebook, escolha um pool do Spark no menu **Anexar a**
1. Clique em **Executar** na célula

## <a name="load-the-nyc-taxi-data-into-the-spark-nyctaxi-database"></a>Carregar os dados de táxi de Nova York no banco de dados nyctaxi do Spark

Temos dados disponíveis em uma tabela em **SQLDB1**. Carregue-os em um banco de dados do Spark chamado **nyctaxi**.

1. No Synapse Studio, acesse o hub **Desenvolver**.
1. Selecione **+**  > **Notebook**.
1. Na parte superior do notebook, defina o valor **Anexar a** como **Spark1**.
1. Selecione **Adicionar código** para adicionar uma célula de código do notebook e depois cole o seguinte texto:

    ```scala
    %%spark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    val df = spark.read.sqlanalytics("SQLDB1.dbo.Trip") 
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```

1. Acesse o hub **Dados**, clique com o botão direito do mouse em **Bancos de dados** e selecione **Atualizar**. Agora você deve ver estes bancos de dados:

    - **SQLDB1** (pool de SQL)
    - **nyctaxi** (Spark)

## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>Analisar os dados de táxi de NYC usando Spark e notebooks

1. Retorne ao notebook.
1. Crie uma célula de código e insira o texto a seguir. Em seguida, execute a célula para mostrar os dados de táxi de Nova York que carregamos no banco de dados **nyctaxi** do Spark.

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. Execute o código a seguir para realizar a mesma análise que fizemos anteriormente com o pool de SQL **SQLDB1**. Esse código salva os resultados da análise em uma tabela chamada **nyctaxi.passengercountstats** e exibe os resultados.

   ```py
   %%pyspark
   df = spark.sql("""
      SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
      FROM nyctaxi.trip
      WHERE TripDistanceMiles > 0 AND PassengerCount > 0
      GROUP BY PassengerCount
      ORDER BY PassengerCount
   """) 
   display(df)
   df.write.saveAsTable("nyctaxi.passengercountstats")
   ```

1. Nos resultados da célula, selecione **Gráfico** para visualizar os dados exibidos.

## <a name="customize-data-visualization-with-spark-and-notebooks"></a>Personalizar visualização de dados com Spark e notebooks

Você pode controlar o modo como os gráficos são renderizados usando notebooks. O código a seguir mostra um exemplo simples. Ele usa as bibliotecas populares **matplotlib** e **manascido**. O código renderiza o mesmo tipo de gráfico de linhas que as consultas SQL que executamos anteriormente.

```py
%%pyspark
import matplotlib.pyplot
import seaborn

seaborn.set(style = "whitegrid")
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.toPandas()
seaborn.lineplot(x="PassengerCount", y="SumTripDistance" , data = df)
seaborn.lineplot(x="PassengerCount", y="AvgTripDistance" , data = df)
matplotlib.pyplot.show()
```



## <a name="load-data-from-a-spark-table-into-a-sql-pool-table"></a>Carregar dados de uma tabela do Spark em uma tabela do pool de SQL

Anteriormente, copiamos dados da tabela do pool de SQL **SQLDB1.dbo.Trip** na tabela do Spark **nyctaxi.trip**. Em seguida, usando o Spark, agregamos os dados na tabela do Spark **nyctaxi.passengercountstats**. Agora, copiaremos os dados de **nyctaxi.passengercountstats** em uma tabela de pools de SQL chamada **SQLDB1.dbo.PassengerCountStats**.

Execute a célula a seguir no seu notebook. Ela copia a tabela do Spark agregada de volta na tabela do pool de SQL.

```scala
%%spark
val df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df.write.sqlanalytics("SQLDB1.dbo.PassengerCountStats", Constants.INTERNAL )
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Analisar dados no Armazenamento](get-started-analyze-storage.md)


