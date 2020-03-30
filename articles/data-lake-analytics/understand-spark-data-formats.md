---
title: Entenda os formatos de dados apache spark para desenvolvedores Azure Data Lake Analytics U-SQL.
description: Este artigo descreve os conceitos do Apache Spark para ajudar os desenvolvedores U_SQL a entender as diferenças entre os formatos de dados U-SQL e Spark.
author: guyhay
ms.author: guyhay
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: conceptual
ms.custom: understand-apache-spark-data-formats
ms.date: 01/31/2019
ms.openlocfilehash: 36f39503ca32f1ee4b422ae7b1cf9abf48716f07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73648435"
---
# <a name="understand-differences-between-u-sql-and-spark-data-formats"></a>Entenda as diferenças entre os formatos de dados U-SQL e Spark

Se você quiser usar [o Azure Databricks](../azure-databricks/what-is-azure-databricks.md) ou [o Azure HDInsight Spark,](../hdinsight/spark/apache-spark-overview.md)recomendamos que você migre seus dados do [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) para [o Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md).

Além de mover seus arquivos, você também vai querer tornar seus dados, armazenados em tabelas U-SQL, acessíveis ao Spark.

## <a name="move-data-stored-in-azure-data-lake-storage-gen1-files"></a>Mover dados armazenados em arquivos Azure Data Lake Storage Gen1

Os dados armazenados em arquivos podem ser movidos de várias maneiras:

- Escreva um pipeline [da Fábrica de Dados do Azure](../data-factory/introduction.md) para copiar os dados da conta [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) para a conta [Azure Data Lake Storage Gen2.](../storage/blobs/data-lake-storage-introduction.md)
- Escreva um trabalho spark que leia os dados da conta [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) e escreva-os na conta [Azure Data Lake Storage Gen2.](../storage/blobs/data-lake-storage-introduction.md) Com base no seu caso de uso, você pode querer escrevê-lo em um formato diferente, como Parquet, se você não precisar preservar o formato original do arquivo.

Recomendamos que você revise o artigo [Atualize suas soluções de análise de big data do Azure Data Lake Storage Gen1 para o Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-upgrade.md)

## <a name="move-data-stored-in-u-sql-tables"></a>Mover dados armazenados em tabelas U-SQL

As tabelas U-SQL não são compreendidas pela Spark. Se você tiver dados armazenados em tabelas U-SQL, você executará um trabalho U-SQL que extrai os dados da tabela e os salva em um formato que o Spark entenda. O formato mais apropriado é criar um conjunto de arquivos Parquet seguindo o layout da pasta do metastore da Colmeia.

A saída pode ser alcançada no U-SQL com o saída Parquet incorporado e usando o particionamento de saída dinâmica com conjuntos de arquivos para criar as pastas de partição. [Processe mais arquivos do que nunca e use o Parquet](https://blogs.msdn.microsoft.com/azuredatalake/2018/06/11/process-more-files-than-ever-and-use-parquet-with-azure-data-lake-analytics) fornece um exemplo de como criar tais dados consumíveis spark.

Após essa transformação, você copia os dados conforme descrito no capítulo [Mover dados armazenados nos arquivos Azure Data Lake Storage Gen1](#move-data-stored-in-azure-data-lake-storage-gen1-files).

## <a name="caveats"></a>Advertências

- Semântica de dados Ao copiar arquivos, a cópia ocorrerá no nível byte. Assim, os mesmos dados devem aparecer na conta [Azure Data Lake Storage Gen2.](../storage/blobs/data-lake-storage-introduction.md) Note, no entanto, Spark pode interpretar alguns caracteres de forma diferente. Por exemplo, ele pode usar um padrão diferente para um delimitador de linha em um arquivo CSV.
    Além disso, se você estiver copiando dados digitados (de tabelas), então Parquet e Spark podem ter precisão e escala diferentes para alguns dos valores digitados (por exemplo, um carro alegórico) e podem tratar valores nulos de forma diferente. Por exemplo, U-SQL tem a semântica C# para valores nulos, enquanto spark tem uma lógica de três valores para valores nulos.

- As tabelas U-SQL da organização de dados (particionamento) fornecem particionamento de dois níveis. O nível`PARTITIONED BY`externo ( ) é por valor e mapeia principalmente no esquema de particionamento Hive/Spark usando hierarquias de pastas. Você precisará garantir que os valores nulos sejam mapeados para a pasta certa. O nível`DISTRIBUTED BY`interno ( ) no U-SQL oferece 4 esquemas de distribuição: round robin, range, hash e hash direto.
    As tabelas Hive/Spark suportam apenas particionamento de valor ou particionamento de hash, usando uma função hash diferente da U-SQL. Quando você desembede os dados da tabela U-SQL, provavelmente só será capaz de mapear a particionamento de valor para o Spark e pode precisar fazer um ajuste adicional do seu layout de dados, dependendo das consultas finais do Spark.

## <a name="next-steps"></a>Próximas etapas

- [Entenda os conceitos de código Spark para desenvolvedores U-SQL](understand-spark-code-concepts.md)
- [Atualize suas soluções de análise de Big Data do Azure Data Lake Storage Gen1 para o Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-upgrade.md)
- [.NET para Apache Spark](https://docs.microsoft.com/dotnet/spark/what-is-apache-spark-dotnet)
- [Transformar dados usando a atividade do Spark no Azure Data Factory](../data-factory/transform-data-using-spark.md)
- [Transformar dados usando a atividade do Hive do Hadoop no Azure Data Factory](../data-factory/transform-data-using-hadoop-hive.md)
- [O que é o Apache Spark no Azure HDInsight](../hdinsight/spark/apache-spark-overview.md)
