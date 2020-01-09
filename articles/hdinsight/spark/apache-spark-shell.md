---
title: Usar um Shell Interativo do Spark no Azure HDInsight
description: Um Shell interativo do Spark fornece um processo de leitura/execução/impressão para executar um comando do Spark de cada vez e ver os resultados.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/12/2019
ms.openlocfilehash: f088b8210b8170d22e84d131f0a72f5f8caa3b92
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435223"
---
# <a name="run-apache-spark-from-the-spark-shell"></a>Execute o Apache Spark no Shell Spark

Interativo [Apache Spark](https://spark.apache.org/) Shell fornece um ambiente de REPL (read-executar-print loop) para executar um comando Spark por vez e ver os resultados. Esse processo é útil para desenvolvimento e depuração. O Spark fornece um shell para cada um dos idiomas com suporte: Scala, Python e R.

## <a name="run-an-apache-spark-shell"></a>Executar um Shell do Apache Spark

1. Use o [comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) para se conectar ao cluster. Edite o comando a seguir substituindo CLUSTERname pelo nome do cluster e, em seguida, digite o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. O Spark fornece shells para escala (Spark-Shell) e Python (pyspark). Em sua sessão SSH, insira um dos seguintes comandos:

    ```bash
    spark-shell
    pyspark
    ```

    Agora, é possível inserir comandos do Spark na linguagem correta.

1. Alguns comandos de exemplo básicos:

    ```scala
    // Load data
    var data = spark.read.format("csv").option("header", "true").option("inferSchema", "true").load("/HdiSamples/HdiSamples/SensorSampleData/building/building.csv")

    // Show data
    data.show()

    // Select certain columns
    data.select($"BuildingID", $"Country").show(10)

    // exit shell
    :q
    ```

## <a name="sparksession-and-sparkcontext-instances"></a>Instâncias SparkSession e SparkContext

Por padrão, ao executar o shell do Spark, as instâncias SparkSession e SparkContext são automaticamente instanciadas.

Para acessar a instância de SparkSession, digite `spark`. Para acessar a instância de SparkContext, digite `sc`.

## <a name="important-shell-parameters"></a>Parâmetros de shell importantes

O comando do shell do Spark (`spark-shell`ou `pyspark`) dá suporte a muitos parâmetros de linha de comando. Para ver uma lista completa de parâmetros, inicie o shell do Spark com a opção `--help`. Alguns desses parâmetros só podem ser aplicados a `spark-submit`, que o Shell do Spark encapsula.

| switch | descrição | exemplo |
| --- | --- | --- |
| --master MASTER_URL | Especifica a URL mestre. No HDInsight, esse valor é sempre `yarn`. | `--master yarn`|
| --jars JAR_LIST | Lista separada por vírgulas de jars locais a ser incluída em classpaths de driver e executor. No HDInsight, essa lista é composta de caminhos para o sistema de arquivos padrão no Armazenamento do Microsoft Azure ou no Data Lake Storage. | `--jars /path/to/examples.jar` |
| --packages MAVEN_COORDS | Lista separada por vírgulas de coordenadas maven de jars para incluir nos classpaths de driver e executor. Pesquisa o repositório local maven e, em seguida, o maven central e todos os repositórios remotos adicionais especificados com `--repositories`. O formato das coordenadas é *groupId*:*artifactId*:*version*. | `--packages "com.microsoft.azure:azure-eventhubs:0.14.0"`|
| --py-files LIST | Apenas para Python, uma lista separada por vírgulas de arquivos .zip, .egg ou .py a ser colocada no PYTHONPATH. | `--pyfiles "samples.py"` |

## <a name="next-steps"></a>Próximos passos

- Ver [Introdução ao Apache Spark no Azure HDInsight](apache-spark-overview.md) para uma visão geral.
- Consulte [Criar um cluster Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md) para trabalhar com clusters Spark e SparkSQL.
- Ver [o que é o Streaming estruturado do Apache Spark?](apache-spark-streaming-overview.md) para gravar aplicativos que processam dados de streaming com o Spark.
