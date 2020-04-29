---
title: Usando o Apache Hive e uma Ferramenta ETL – Azure HDInsight
description: Use o Apache Hive para extrair, transformar e carregar (ETL) dados no Azure HDInsight.
ms.service: hdinsight
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/22/2019
ms.openlocfilehash: be331f36a6305b05ce83a2b2d5fdfb73a154ce3d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77623112"
---
# <a name="use-apache-hive-as-an-extract-transform-and-load-etl-tool"></a>Usar o Apache Hive como uma ferramenta de extração, transformação e carregamento (ETL)

Você normalmente precisa limpar e transformar os dados de entrada antes de carregá-los em um destino adequado para análise. Operações de extração, transformação e carregamento (ETL) são usadas para preparar os dados e carregá-los em um destino de dados.  O Apache Hive no HDInsight pode ler dados não estruturados, processar os dados conforme necessário e, em seguida, carregá-los em um data warehouse relacional para sistemas de suporte à decisão. Nessa abordagem, os dados são extraídos da origem e armazenados no armazenamento escalonável, como blobs de Armazenamento do Microsoft Azure ou Azure Data Lake Storage. Os dados são, então, transformados usando uma sequência de consultas de Hive e, finalmente, organizados no Hive em preparação para o carregamento em massa no armazenamento de dados de destino.

## <a name="use-case-and-model-overview"></a>Visão geral de caso de uso e modelo

A figura a seguir mostra uma visão geral de caso de uso e de modelo para a automação de ETL. Dados de entrada são transformados para gerar a saída apropriada.  Durante essa transformação, os dados podem sofrer alterações de forma, tipo de dados e até de idioma.  Processos de ETL podem converter de imperial para métrica, alterar fusos horários e melhorar a precisão para alinhar corretamente com os dados existentes no destino.  Os processos de ETL também podem combinar novos dados com os dados existentes para manter o relatório atualizado ou para fornecer mais informações sobre os dados existentes.  Aplicativos, como ferramentas e serviços de relatório podem, então, consumir esses dados no formato desejado.

![Apache Hive como arquitetura de ETL](./media/apache-hadoop-using-apache-hive-as-an-etl-tool/hdinsight-etl-architecture.png)

O Hadoop normalmente é usado em processos de ETL que importam um grande número de arquivos de texto (como CSVs) ou um menor, mas frequentemente alterando o número de arquivos de texto, ou ambos.  O Hive é uma excelente ferramenta para usar para preparar os dados antes de carregá-los para o destino de dados.  O Hive permite que você crie um esquema em CSV e use uma linguagem semelhante a SQL para gerar os programas de MapReduce que interagem com os dados.

As etapas típicas para usar o Hive para executar ETL são as seguintes:

1. Descarregue dados no Azure Data Lake Storage ou no Armazenamento de Blobs do Azure.
2. Crie um banco de dados do armazenamento de metadados (usando o Banco de Dados SQL do Azure) para o Hive usar no armazenamento de seus esquemas.
3. Crie um cluster HDInsight e conecte o armazenamento de dados.
4. Defina o esquema para aplicar em tempo de leitura em dados no armazenamento de dados:

    ```
    DROP TABLE IF EXISTS hvac;

    --create the hvac table on comma-separated sensor data stored in Azure Storage blobs

    CREATE EXTERNAL TABLE hvac(`date` STRING, time STRING, targettemp BIGINT,
        actualtemp BIGINT,
        system BIGINT,
        systemage BIGINT,
        buildingid BIGINT)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
    STORED AS TEXTFILE LOCATION 'wasbs://{container}@{storageaccount}.blob.core.windows.net/HdiSamples/SensorSampleData/hvac/';
    ```

5. Transforme os dados e carregue-os no destino.  Há várias maneiras de usar o Hive durante a transformação e o carregamento:

    * Consulte e prepare dados usando o Hive e salve-os como um CSV no Azure Data Lake Storage ou no armazenamento de blobs do Azure.  Em seguida, use uma ferramenta como o SQL Server Integration Services (SSIS) para adquirir esses CSVs e carregue os dados em um banco de dados relacional de destino como o SQL Server.
    * Consulte os dados diretamente do Excel ou C# usando o driver ODBC do Hive.
    * Use [Apache Sqoop](apache-hadoop-use-sqoop-mac-linux.md) para ler os arquivos CSV simples preparados e carregue-os no banco de dados relacional de destino.

## <a name="data-sources"></a>Fontes de dados

Fontes de dados são normalmente dados externos que podem ser correspondidos com os dados existentes no seu armazenamento de dados, por exemplo:

* Dados de mídia social, arquivos de log, sensores e aplicativos que geram arquivos de dados.
* Conjuntos de dados obtidos de provedores de dados, como estatísticas de tempo ou números de vendas de fornecedores.
* Fluxo de dados capturados, filtrados e processado por meio de uma ferramenta ou estrutura adequada.

<!-- TODO: (see Collecting and loading data into HDInsight). -->

## <a name="output-targets"></a>Destinos de saída

Você pode usar o Hive para gerar dados de saída para uma variedade de destinos incluindo:

* Um banco de dados relacional, como SQL Server ou Banco de Dados SQL do Azure.
* Um data warehouse, como o SQL Data Warehouse do Azure.
* Excel.
* Tabela e armazenamento de blob do Azure.
* Aplicativos ou serviços que exigem que os dados sejam processados em formatos específicos ou como arquivos que contêm tipos específicos de estrutura de informações.
* Um repositório de documentos JSON como [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

## <a name="considerations"></a>Considerações

O modelo de ETL normalmente é usado quando você deseja:

* Carregar dados de transmissão ou grandes volumes de dados semiestruturados ou não estruturados de fontes externas para um banco de dados ou sistema de informações existente.
* Limpar, transformar e validar os dados antes de carregá-los, talvez usando mais de uma passagem de transformação pelo cluster.
* Gere relatórios e visualizações que são atualizadas regularmente. Por exemplo, se o relatório demora muito para ser gerado durante o dia, você pode agendar para que ele seja executado durante a noite. Para executar automaticamente uma consulta de Hive, você pode usar os [aplicativos lógicos do Azure](../../logic-apps/logic-apps-overview.md) e o PowerShell.

Se o destino dos dados não for um banco de dado, você poderá gerar um arquivo no formato apropriado dentro da consulta, por exemplo, um CSV. Esse arquivo pode ser importado para o Excel ou Power BI.

Se você precisar executar várias operações nos dados como parte do processo de ETL, considere como você as gerencia. Se as operações são controladas por um programa externo, em vez de como um fluxo de trabalho dentro da solução, você precisa decidir se algumas operações podem ser executadas em paralelo e detectar quando cada trabalho é concluído. Usar um mecanismo de fluxo de trabalho como o Oozie no Hadoop pode ser mais fácil do que tentar orquestrar uma sequência de operações usando scripts externos ou programas personalizados. Para obter mais informações sobre o Oozie, consulte [Fluxo de trabalho e o orquestração de trabalhos](https://msdn.microsoft.com/library/dn749829.aspx).

## <a name="next-steps"></a>Próximas etapas

* [ETL em escala](apache-hadoop-etl-at-scale.md)
* [Operacionalizar um pipeline de dados](../hdinsight-operationalize-data-pipeline.md)

<!-- * [ETL Deep Dive](../hdinsight-etl-deep-dive.md) -->
