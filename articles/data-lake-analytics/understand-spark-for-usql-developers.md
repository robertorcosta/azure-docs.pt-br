---
title: Entenda apache spark para desenvolvedores Azure Data Lake Analytics U-SQL.
description: Este artigo descreve os conceitos do Apache Spark para ajudá-lo a diferenças entre os desenvolvedores do U-SQL.
author: guyhay
ms.author: guyhay
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: conceptual
ms.custom: understand-apache-spark-for-usql-developers
ms.date: 10/15/2019
ms.openlocfilehash: 594e1055c4c063e4e151fefa3e183e6e799c90b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73648422"
---
# <a name="understand-apache-spark-for-u-sql-developers"></a>Noções básicas do Apache Spark para desenvolvedores do U-SQL

A Microsoft suporta vários serviços de Analytics, como [o Azure Databricks](../azure-databricks/what-is-azure-databricks.md) e [o Azure HDInsight,](../hdinsight/hdinsight-overview.md) bem como o Azure Data Lake Analytics. Ouvimos dos desenvolvedores que eles têm uma clara preferência por soluções de código aberto à medida que constroem pipelines de análise. Para ajudar os desenvolvedores do U-SQL a entender o Apache Spark, e como você pode transformar seus scripts U-SQL em Apache Spark, criamos essa orientação.

Ele inclui uma série de passos que você pode dar, e várias alternativas.

## <a name="steps-to-transform-u-sql-to-apache-spark"></a>Passos para transformar o U-SQL em Apache Spark

1. Transforme seus oleodutos de orquestração de trabalho.

   Se você usar [a Fábrica de Dados do Azure](../data-factory/introduction.md) para orquestrar seus scripts do Azure Data Lake Analytics, você terá que ajustá-los para orquestrar os novos programas spark.
2. Entenda as diferenças entre como o U-SQL e o Spark gerenciam dados

   Se você quiser mover seus dados do [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) para [o Azure Data Lake Storage Gen2,](../storage/blobs/data-lake-storage-introduction.md)você terá que copiar os dados do arquivo e os dados mantidos pelo catálogo. Observe que o Azure Data Lake Analytics só suporta o Azure Data Lake Storage Gen1. Veja [Entender os formatos de dados do Spark](understand-spark-data-formats.md)
3. Transforme seus scripts U-SQL em Spark

   Antes de transformar seus scripts U-SQL, você terá que escolher um serviço de análise. Alguns dos serviços de computação disponíveis disponíveis são:
      - [Fluxo de dados da fábrica de dados do Azure](../data-factory/concepts-data-flow-overview.md) O mapeamento de fluxos de dados são transformações de dados projetadas visualmente que permitem aos engenheiros de dados desenvolver uma lógica de transformação de dados gráficos sem escrever código. Embora não sejam adequados para executar códigode usuário complexo, eles podem representar facilmente as transformações tradicionais de fluxo de dados semelhantes ao SQL
      - [Colmeia Azure HDInsight](../hdinsight/hadoop/apache-hadoop-using-apache-hive-as-an-etl-tool.md) A Colmeia Apache no HDInsight é adequada para as operações Extract, Transform e Load (ETL). Isso significa que você vai traduzir seus scripts U-SQL para Apache Hive.
      - Apache Spark Engines, como [Azure HDInsight Spark](../hdinsight/spark/apache-spark-overview.md) ou [Azure Databricks](../azure-databricks/what-is-azure-databricks.md) Isso significa que você vai traduzir seus scripts U-SQL para Spark. Para obter mais informações, consulte [Entenda os formatos de dados do Spark](understand-spark-data-formats.md)

> [!CAUTION]
> Tanto [o Azure Databricks](../azure-databricks/what-is-azure-databricks.md) quanto [o Azure HDInsight Spark](../hdinsight/spark/apache-spark-overview.md) são serviços de cluster e não trabalhos sem servidor, como o Azure Data Lake Analytics. Você terá que considerar como provisionar os clusters para obter a relação custo/desempenho apropriada e como gerenciar sua vida útil para minimizar seus custos. Esses serviços têm diferentes características de desempenho com código de usuário escrito em .NET, então você terá que escrever invólucros ou reescrever seu código em um idioma suportado. Para obter mais informações, consulte [Entenda os formatos de dados spark,](understand-spark-data-formats.md) [entenda os conceitos de código Apache Spark para desenvolvedores U-SQL,](understand-spark-code-concepts.md) [.Net para Apache Spark](https://dotnet.microsoft.com/apps/data/spark)

## <a name="next-steps"></a>Próximas etapas

- [Entenda os formatos de dados spark para desenvolvedores U-SQL](understand-spark-data-formats.md)
- [Entenda os conceitos de código Spark para desenvolvedores U-SQL](understand-spark-code-concepts.md)
- [Atualize suas soluções de análise de Big Data do Azure Data Lake Storage Gen1 para o Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-upgrade.md)
- [.NET para Apache Spark](https://docs.microsoft.com/dotnet/spark/what-is-apache-spark-dotnet)
- [Transformar dados usando a atividade do Hive do Hadoop no Azure Data Factory](../data-factory/transform-data-using-hadoop-hive.md)
- [Transformar dados usando a atividade do Spark no Azure Data Factory](../data-factory/transform-data-using-spark.md)
- [O que é o Apache Spark no Azure HDInsight](../hdinsight/spark/apache-spark-overview.md)
