---
title: O que são Apache Hadoop e MapReduce – Azure HDInsight
description: Uma introdução ao HDInsight e aos componentes e a pilha de tecnologias do Apache Hadoop.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.date: 02/27/2020
ms.openlocfilehash: 7e8dd69b7c58e090c30ea1aa59feddab610dd3c5
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2020
ms.locfileid: "78244883"
---
# <a name="what-is-apache-hadoop-in-azure-hdinsight"></a>O que é o Apache Hadoop no Azure HDInsight?

O [Apache Hadoop](https://hadoop.apache.org/) era a estrutura de código aberto original para processamento distribuído e análise de conjuntos de Big Data em clusters. O ecossistema do Hadoop inclui software e utilitários relacionados, inclusive o Apache Hive, o Apache HBase, o Spark, o Kafka e muitos outros.

O HDInsight do Azure é um serviço de análise totalmente gerenciado, completo e de fonte aberta na nuvem para empresas. O tipo de cluster do Apache Hadoop no Azure HDInsight permite usar HDFS, YARN e gerenciamento de recursos, bem como um modelo de programação MapReduce simples, para processar e analisar dados em lote em paralelo.

Para ver os componentes disponíveis da pilha de tecnologia do Hadoop no HDInsight, confira [Componentes e versões disponíveis com o HDInsight](../hdinsight-component-versioning.md). Para ler mais sobre o Hadoop no HDInsight, consulte a [Página de recursos do Azure para HDInsight](https://azure.microsoft.com/services/hdinsight/).

## <a name="what-is-mapreduce"></a>O que é o MapReduce

O MapReduce do Apache Hadoop é uma estrutura de software para gravar trabalhos que processam grandes quantidades de dados. Dados de entrada são divididos em partes independentes. Cada bloco é processado em paralelo em todos os nós no cluster. Um trabalho do MapReduce consiste em duas funções:

* **Mapeador**: Consome dados de entrada, analisa-os (normalmente com operações de classificação e filtro) e emite tuplas (pares chave-valor)

* **Redutor**: Consome tuplas emitidas pelo Mapeador e executa uma operação de resumo que cria um resultado menor e combinado dos dados do Mapeador

Um exemplo básico de trabalho de contagem de palavras do MapReduce está ilustrado no diagrama abaixo:

 ![HDI.WordCountDiagram](./media/apache-hadoop-introduction/hdi-word-count-diagram.gif)

A saída deste trabalho é uma contagem de quantas vezes cada palavra ocorreu no texto.

* O mapeador utiliza cada linha do texto de entrada como uma entrada e a divide em palavras. Ele emite um par de chave/valor cada vez que ocorre uma palavra seguida de um 1. A saída será classificada antes de ser enviada ao redutor.
* Em seguida, o redutor soma essas contagens individuais para cada palavra e emite um par de chave/valor único que contém a palavra seguido pela soma de suas ocorrências.

O MapReduce pode ser implementado em várias linguagens. Java é a implementação mais comum e é usado para fins de demonstração neste documento.

## <a name="development-languages"></a>Linguagens de desenvolvimento

As linguagens ou frameworks que são baseados em Java e a Máquina Virtual Java podem ser executadas diretamente como um trabalho do MapReduce. O exemplo usado neste documento é um aplicativo MapReduce em Java. Linguagens não Java, como C#, Python ou executáveis autônomos devem usar o **streaming do Hadoop**.

O streaming do Hadoop se comunica com o mapeador e redutor por STDIN e STDOUT. O mapeador e redutor leem os dados uma linha por vez do STDIN e gravam a saída em STDOUT. Cada linha lida ou emitida pelo mapeador e redutor deve estar no formato de um par de chave/valor, delimitado por um caractere de tabulação:

    [key]/t[value]

Para saber mais, confira [Streaming do Hadoop](https://hadoop.apache.org/docs/current/hadoop-streaming/HadoopStreaming.html).

Para ver exemplos do uso de streaming do Hadoop com o HDInsight, confira os seguintes documentos:

* [Desenvolver trabalhos do MapReduce em C#](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)

## <a name="next-steps"></a>Próximas etapas

* [Criar cluster do Apache Hadoop no HDInsight](apache-hadoop-linux-create-cluster-get-started-portal.md)
