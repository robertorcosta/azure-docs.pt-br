---
title: 'Início rápido: introdução à análise com o Spark'
description: Neste tutorial, você aprenderá a analisar dados com o Apache Spark.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: tutorial
ms.date: 12/31/2020
ms.openlocfilehash: 8559bd0a354a64872e58d014d1027ed971773b60
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104655335"
---
# <a name="analyze-with-apache-spark"></a>Análise com o Apache Spark

Neste tutorial, você conhecerá as etapas básicas usadas para carregar e analisar dados com o Apache Spark para o Azure Synapse.

## <a name="create-a-serverless-apache-spark-pool"></a>Criar um Pool do Apache Spark sem servidor

1. No Synapse Studio, no painel do lado esquerdo, selecione **Gerenciar** > **Pools do Apache Spark**.
1. Selecione **Novo** 
1. Em **Nome do Pool do Apache Spark**, insira **Spark1**.
1. Em **Tamanho do nó**, insira **Pequeno**.
1. Em **Número de nós**, defina o mínimo como 3 e o máximo como 3
1. Selecione **Examinar + criar** > **Criar**. Seu Pool do Apache Spark estará pronto em alguns segundos.

## <a name="understanding-serverless-apache-spark-pools"></a>Noções básicas sobre pools do Apache Spark sem servidor

Um Pool do Spark sem servidor é uma forma de indicar como um usuário deseja trabalhar com o Spark. Quando você começa a usar um pool, uma sessão do Spark é criada, se necessário. O pool controla quantos recursos do Spark serão usados por essa sessão e por quanto tempo a sessão permaneceu antes de ser colocada em pausa automaticamente. Você paga pelos recursos do Spark usados durante essa sessão, não pelo próprio pool. Dessa forma, um Pool do Spark permite que você trabalhe com o Spark, sem precisar se preocupar com o gerenciamento de clusters. Isso é semelhante a como funciona um pool de SQL sem servidor.

## <a name="analyze-nyc-taxi-data-in-blob-storage-using-spark"></a>Analisar os dados de Táxi de Nova York no armazenamento de blobs usando o Spark

1. No Synapse Studio, acesse o hub **Desenvolver**
2. Crie um Notebook com a linguagem padrão definida como **PySpark (Python)** .
3. Crie uma célula de código e cole o código a seguir nela.
    ```
    from azureml.opendatasets import NycTlcYellow

    data = NycTlcYellow()
    df = data.to_spark_dataframe()
    # Display 10 rows
    display(df.limit(10))
    ```
1. No notebook, no menu **Anexar a**, escolha o Pool do Spark sem servidor **Spark1** que criamos anteriormente.
1. Selecione **Executar** na célula. O Azure Synapse iniciará uma nova sessão do Spark para executar essa célula, se necessário. Se uma nova sessão do Spark for necessária, inicialmente, levará cerca de dois segundos para que ela seja criada. 
1. Se você quiser ver apenas o esquema do dataframe, execute uma célula com o seguinte código:

    ```py
    df.printSchema()
    ```

## <a name="load-the-nyc-taxi-data-into-the-spark-nyctaxi-database"></a>Carregar os dados de táxi de Nova York no banco de dados nyctaxi do Spark

Os dados ficam disponíveis por meio do dataframe chamado **data**. Carregue-os em um banco de dados do Spark chamado **nyctaxi**.

1. Adicione um novo ao notebook e insira o seguinte código:

    ```py
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```
## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>Analisar os dados de táxi de NYC usando Spark e notebooks

1. Retorne ao notebook.
1. Crie uma célula de código e insira o código a seguir. 

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. Execute a célula para mostrar os dados de Táxis de Nova York que carregamos no banco de dados **nyctaxi** do Spark.
1. Crie uma célula de código e insira o código a seguir. Depois, execute a célula para realizar a mesma análise que fizemos anteriormente com o pool de SQL dedicado **SQLPOOL1**. Esse código salva e exibe os resultados da análise em uma tabela chamada **nyctaxi.passengercountstats**.

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


## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Analisar dados com um pool de SQL dedicado](get-started-analyze-sql-pool.md)
