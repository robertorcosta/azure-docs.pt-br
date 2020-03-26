---
title: O que é o Azure HDInsight
description: Uma introdução ao HDInsight, à pilha e aos componentes da tecnologia Apache Hadoop e Apache Spark, incluindo Kafka, Hive, Storm e HBase para análise de Big Data.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.date: 02/27/2020
ms.openlocfilehash: 31e8506067133304144614ff58974ee21e9680be
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2020
ms.locfileid: "79216424"
---
# <a name="what-is-azure-hdinsight"></a>O que é o Azure HDInsight?

O Azure HDInsight é um serviço de análise totalmente gerenciado, completo e open-source na nuvem para empresas. Você pode usar estruturas de software livre como Hadoop, Apache Spark, Apache Hive, LLAP, Apache Kafka, Apache Storm, R e outros.

## <a name="what-is-hdinsight-and-the-hadoop-technology-stack"></a>O que é o HDInsight e a pilha de tecnologias do Hadoop?

O Azure HDInsight é uma distribuição de nuvem dos componentes do Hadoop. O Azure HDInsight torna mais fácil, rápido e econômico processar grandes quantidades de dados. Você pode usar as estruturas de código aberto mais populares, como Hadoop, Spark, Hive, LLAP, Kafka, Storm, R e muito mais. Com essas estruturas, você pode habilitar uma grande variedade de cenários, como extração, transformação e carregamento (ETL), data warehouse, aprendizado de máquina e IoT.

Para ver os componentes disponíveis da pilha de tecnologia do Hadoop no HDInsight, confira [Componentes e versões disponíveis com o HDInsight](./hdinsight-component-versioning.md). Para ler mais sobre o Hadoop no HDInsight, consulte a [Página de recursos do Azure para HDInsight](https://azure.microsoft.com/services/hdinsight/).

## <a name="what-is-big-data"></a>O que é big data?

Mais do que nunca, o Big Data está sendo coletado em volumes crescentes, em velocidades mais altas e em uma maior variedade de formatos. Ele pode ser histórico (referente a dados armazenados) ou em tempo real (o que significa que é transmitido da fonte). Consulte [Cenários de uso do HDInsight](#scenarios-for-using-hdinsight) para saber mais sobre os casos de uso mais comuns de Big Data.

## <a name="why-should-i-use-azure-hdinsight"></a>Por que devo usar o Azure HDInsight?

Esta seção lista os recursos do Azure HDInsight.

|Recurso  |Descrição  |
|---------|---------|
|Nativo de nuvem     |     O Azure HDInsight permite a criação de cluster otimizados para [Hadoop](./hadoop/apache-hadoop-linux-tutorial-get-started.md), [Spark](./spark/apache-spark-jupyter-spark-sql.md), [Consulta interativa (LLAP)](./interactive-query/apache-interactive-query-get-started.md), [Kafka](./kafka/apache-kafka-get-started.md), [Storm](./storm/apache-storm-tutorial-get-started-linux.md), [HBase](./hbase/apache-hbase-tutorial-get-started-linux.md) e  [ML Services](./r-server/r-server-overview.md) no Azure. O HDInsight também oferece um SLA de ponta a ponta em todas as suas cargas de trabalho de produção.  |
|De baixo custo e escalonável     | O HDInsight permite [escalar](./hdinsight-administer-use-portal-linux.md#scale-clusters) as cargas de trabalho para cima ou para baixo. É possível reduzir os custos [criando clusters sob demanda](./hdinsight-hadoop-create-linux-clusters-adf.md) e pagando apenas pelo que for usado. Você também pode compilar pipelines de dados para operacionalizar seus trabalhos. A computação e o armazenamento desacoplados fornecem melhor desempenho e flexibilidade. |
|Seguro e em conformidade    | O HDInsight permite a proteção dos ativos de dados corporativos com a [Rede Virtual do Microsoft Azure](./hdinsight-plan-virtual-network-deployment.md), [criptografia](./hdinsight-hadoop-create-linux-clusters-with-secure-transfer-storage.md) e integração com o [Azure Active Directory](./domain-joined/hdinsight-security-overview.md). O HDInsight também atende aos [padrões de conformidade](https://azure.microsoft.com/overview/trusted-cloud) mais populares do setor e do governo.        |
|Monitoramento    | O Azure HDInsight é integrado aos [logs do Azure Monitor](./hdinsight-hadoop-oms-log-analytics-tutorial.md) para fornecer uma interface única com a qual você pode monitorar todos os seus clusters.        |
|Disponibilidade global | O HDInsight está disponível em mais  [regiões](https://azure.microsoft.com/regions/services/) que qualquer outra oferta de análise de Big Data. O Azure HDInsight também está disponível no Azure Governamental, na China e na Alemanha, o que permite atender às necessidades da sua empresa nas principais áreas soberanas. |  
|Produtividade     |  O Microsoft Azure HDInsight permite que você use ferramentas produtivas avançadas para o Hadoop e o Spark com seus ambientes de desenvolvimento preferidos. Esses ambientes de desenvolvimento incluem [Visual Studio](./hadoop/apache-hadoop-visual-studio-tools-get-started.md), [VSCode](./hdinsight-for-vscode.md), [Eclipse](./spark/apache-spark-eclipse-tool-plugin.md) e [IntelliJ](./spark/apache-spark-intellij-tool-plugin.md) para dar suporte ao Scala, Python, R, Java e .NET. Os cientistas de dados também podem colaborar usando notebooks populares como o [Jupyter](./spark/apache-spark-jupyter-notebook-kernels.md) e o [Zeppelin](./spark/apache-spark-zeppelin-notebook.md).    |
|Extensibilidade     |  Você pode estender os clusters do HDInsight com componentes instalados (Hue, Presto, etc.) usando [ações de script](./hdinsight-hadoop-customize-cluster-linux.md), [adicionando nós de borda](./hdinsight-apps-use-edge-node.md) ou [integrando outros aplicativos de Big Data certificados](./hdinsight-apps-install-applications.md). O HDInsight permite a integração perfeita com as soluções de Big Data mais populares com uma implantação com [um clique](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/).|

## <a name="scenarios-for-using-hdinsight"></a>Cenários de uso do HDInsight

O Azure HDInsight pode ser usado para uma variedade de cenários no processamento de Big Data. Podem ser dados históricos (dados que já estão coletados e armazenados) ou dados em tempo real (dados que são transmitidos diretamente da fonte). Os cenários para processar esses dados podem ser resumidos nas seguintes categorias:

### <a name="batch-processing-etl"></a>Processamento em lotes (ETL)

Extração, transformação e carregamento (ETL) é um processo em que os dados estruturados ou não estruturados são extraídos de fontes de dados heterogêneas. Em seguida, ele é transformado em um formato estruturado e carregado no repositório de dados. Você pode usar os dados transformados para ciência de dados ou data warehousing.

### <a name="data-warehousing"></a>Data warehousing

É possível usar o HDInsight para executar consultas interativas em escalas petabyte sobre dados estruturados ou não estruturados em qualquer formato. Também é possível criar modelos conectando-os a ferramentas de BI. Para obter mais informações, [leia esta história de cliente](https://customers.microsoft.com/story/milliman).

![Arquitetura do HDInsight: Data warehouse](./hadoop/media/apache-hadoop-introduction/hdinsight-architecture-data-warehouse.png "Arquitetura de data warehouse do HDInsight")

### <a name="internet-of-things-iot"></a>Internet das coisas (IoT)

Você pode usar o HDInsight para processar os dados de streaming recebidos em tempo real de diferentes tipos de dispositivos. Para obter mais informações, [leia esta postagem de blog do Azure que informa a visualização pública do Apache Kafka no HDInsight com Azure Managed Disks](https://azure.microsoft.com/blog/announcing-public-preview-of-apache-kafka-on-hdinsight-with-azure-managed-disks/).

![Arquitetura do HDInsight: Internet das Coisas](./hadoop/media/apache-hadoop-introduction/hdinsight-architecture-iot.png "Arquitetura de IoT do HDInsight")

### <a name="data-science"></a>Ciência de dados

Você pode usar o HDInsight para compilar aplicativos que extraem informações críticas dos dados. Você também pode usar o Azure Machine Learning antes disso para prever tendências futuras para seu negócio. Para obter mais informações, [leia esta história de cliente](https://customers.microsoft.com/story/pros).

![Arquitetura do HDInsight: ciência de dados](./hadoop/media/apache-hadoop-introduction/hdinsight-architecture-data-science.png "Arquitetura de ciência de dados do HDInsight")

### <a name="hybrid"></a>Híbrido

Você pode usar o HDInsight para estender sua infraestrutura de Big Data local existente ao Azure para aproveitar os recursos de análise avançada da nuvem.

![Arquitetura do HDInsight: híbrida](./hadoop/media/apache-hadoop-introduction/hdinsight-architecture-hybrid.png "Arquitetura híbrida do HDInsight")

## <a name="cluster-types-in-hdinsight"></a>Tipos de cluster no HDInsight

O HDInsight inclui tipos específicos de cluster e recursos de personalização do cluster, como a capacidade de adicionar componentes, utilitários e idiomas. O HDInsight oferece os seguintes tipos de cluster:

|Tipo de cluster | Descrição |
|---|---|
|[Apache Hadoop](./hadoop/apache-hadoop-introduction.md)|uma estrutura que usa HDFS, gerenciamento de recursos YARN e um modelo de programação MapReduce simples para processar e analisar dados em lote em paralelo.|
|[Apache Spark](./spark/apache-spark-overview.md)|uma estrutura de processamento paralelo de software livre que dá suporte ao processamento na memória para melhorar o desempenho dos aplicativos de análise de Big Data. Confira [O que é o Apache Spark no HDInsight?](./spark/apache-spark-overview.md).|
|[HBase no Apache](./hbase/apache-hbase-overview.md)|um banco de dados NoSQL baseado em Hadoop que fornece acesso aleatório e coerência forte para big data não estruturado e semiestruturado (potencialmente, bilhões de linhas vezes milhões de colunas). Confira [O que é o HBase em HDInsight?](./hbase/apache-hbase-overview.md)|
|[ML Services](./r-server/r-server-overview.md)|um servidor para hospedagem e gerenciamento paralelo, processos R distribuídos. Ele fornece aos cientistas de dados, estatísticos e programadores de R o acesso sob demanda a métodos escalonáveis e distribuídos de análise no HDInsight. Consulte a [Visão geral de ML Services no HDInsight](./r-server/r-server-overview.md).|
|[Apache Storm](./storm/apache-storm-overview.md)|um sistema de computação distribuído e em tempo real para processar rapidamente grandes fluxos de dados. O Storm é oferecido como um cluster gerenciado no HDInsight. Consulte [Analisar dados do sensor em tempo real usando o Storm e o Hadoop](./storm/apache-storm-overview.md).|
|[Consulta Interativa do Apache](./interactive-query/apache-interactive-query-get-started.md)|Caching na memória para consultas de Hive interativas e mais rápidas. Veja [Usar a consulta interativa no HDInsight](./interactive-query/apache-interactive-query-get-started.md).|
|[Apache Kafka](./kafka/apache-kafka-introduction.md)|uma plataforma de código-fonte aberto usada para criar aplicativos e pipelines de dados de transmissão. O Kafka também fornece funcionalidade de fila de mensagens, o que permite que você publique e assine fluxos de dados. Consulte [Uma introdução ao Apache Kafka no HDInsight](./kafka/apache-kafka-introduction.md).|

## <a name="open-source-components-in-hdinsight"></a>Componentes de código aberto no HDInsight

O Azure HDInsight permite que você crie clusters com estruturas de software livre, como Hadoop, Spark, Hive, LLAP, Kafka, Storm, HBase e R. Esses clusters, por padrão, vêm com outros componentes de software livre incluídos no cluster, como [Apache Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md), [Avro](https://avro.apache.org/docs/current/spec.html), [Apache Hive](https://hive.apache.org), [HCatalog](https://cwiki.apache.org/confluence/display/Hive/HCatalog/), [Apache Mahout](https://mahout.apache.org/), [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html), [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html), [Apache Phoenix](https://phoenix.apache.org/), [Apache Pig](https://pig.apache.org/), [Apache Sqoop](https://sqoop.apache.org/), [Apache Tez](https://tez.apache.org/), [Apache Oozie](https://oozie.apache.org/), [Apache ZooKeeper](https://zookeeper.apache.org/).  

## <a name="programming-languages-in-hdinsight"></a>Linguagens de programação no HDInsight

Os clusters do HDInsight, incluindo Spark, HBase, Kafka, Hadoop e outros, dão suporte a várias linguagens de programação. Algumas linguagens de programação não são instaladas por padrão. No caso de bibliotecas, módulos ou pacotes que não são instalados por padrão, [use uma ação de script para instalar o componente](./hdinsight-hadoop-script-actions-linux.md).

|Linguagem de programação  |Informações  |
|---------|---------|
|Suporte padrão à linguagem de programação     | Por padrão, os clusters HDInsight são compatíveis com:<ul><li>Java</li><li>Python</li><li>.NET</li><li>Go</li></ul>  |
|Linguagens JVM (máquina virtual Java)     | Muitas linguagens diferentes do Java podem ser executadas em uma máquina virtual do Java (JVM). No entanto, se você executar algumas dessas linguagens, você talvez precise instalar componentes adicionais no cluster. As seguintes linguagens baseadas em JVM são permitidas nos clusters HDInsight: <ul><li>Clojure</li><li>Jython (Python para Java)</li><li>Scala</li></ul>     |
|Linguagens específicas do Hadoop     | Os clusters HDInsight dão suporte às seguintes linguagens que são específicas ao ecossistema da pilha de tecnologias do Hadoop: <ul><li>Pig Latin para trabalhos do Pig</li><li>HiveQL para trabalhos do Hive e SparkSQL</li></ul>        |

## <a name="development-tools-for-hdinsight"></a>Ferramentas de desenvolvimento para HDInsight

Você pode usar ferramentas de desenvolvimento do HDInsight, incluindo IntelliJ, Eclipse, Visual Studio Code e Visual Studio, para criar e enviar a consulta de dados do HDInsight e o trabalho com integração perfeita com o Azure.

* [Azure Toolkit for IntelliJ](./spark/apache-spark-intellij-tool-plugin.md)

* [Azure Toolkit for Eclipse](./spark/apache-spark-eclipse-tool-plugin.md)

* [Ferramentas do Azure HDInsight para VS Code](./hdinsight-for-vscode.md)

* [Ferramentas do Azure Data Lake para Visual Studio](./hadoop/apache-hadoop-visual-studio-tools-get-started.md)

## <a name="business-intelligence-on-hdinsight"></a>Business intelligence no HDInsight

As ferramentas familiares de BI (business intelligence) recuperam, analisam e relatam os dados que estão integrados ao HDInsight usando o suplemento Power Query ou o Driver ODBC do Microsoft Hive:

* [Apache Spark BI usando ferramentas de visualização de dados com o Azure HDInsight](./spark/apache-spark-use-bi-tools.md)

* [Visualize dados do Apache Hive com o Microsoft Power BI no Azure HDInsight](./hadoop/apache-hadoop-connect-hive-power-bi.md)

* [Visualizar dados da consulta interativa do Hive com o Power BI no Azure HDInsight](./interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)

* [Conectar o Excel ao Apache Hadoop com o Power Query](./hadoop/apache-hadoop-connect-excel-power-query.md) (requer Windows)

* [Conectar o Excel ao Apache Hadoop com o Driver ODBC do Microsoft Hive](./hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md) (requer Windows)

## <a name="next-steps"></a>Próximas etapas

* [Criar cluster do Apache Hadoop no HDInsight](./hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md)
