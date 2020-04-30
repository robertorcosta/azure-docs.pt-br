---
title: Extrair, transformar e carregar (ETL) em escala - Azure HDInsight
description: Saiba como extrair, transformar e carregar é usado no HDInsight com Apache Hadoop.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/28/2020
ms.openlocfilehash: ee989ccbb2e441256bec71781c538c7761fc7b88
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82232233"
---
# <a name="extract-transform-and-load-etl-at-scale"></a>Extrair, transformar e carregar (ETL) em escala

Extração, transformação e carregamento (ETL) é o processo pelo qual os dados são adquiridos de várias fontes. Coletados em um local padrão, limpos e processados. Em última instância, carregado em um repositório de armazenamento do qual ele pode ser consultado. Processos ETL herdados importam dados, os limpam no local e, em seguida, os armazenam em um mecanismo de dados relacionais. Com o HDInsight, uma ampla variedade de componentes de ambiente de Apache Hadoop dá suporte a ETL em escala.

O uso do HDInsight no processo de ETL pode ser resumido por este pipeline:

![Visão geral do ETL do HDInsight em escala](./media/apache-hadoop-etl-at-scale/hdinsight-etl-at-scale-overview.png)

As seções a seguir exploram cada uma das fases de ETL e seus componentes associados.

## <a name="orchestration"></a>Orquestração

Orquestração se estende por todas as fases do pipeline de ETL. Trabalhos ETL no HDInsight geralmente envolvem vários produtos trabalhando em conjunto com o outro.  Você pode usar o Hive para limpar uma parte dos dados, enquanto o Pig limpa outra parte.  Você pode usar o Azure Data Factory para carregar dados no Banco de Dados SQL do Azure Data Lake Store.

A orquestração é necessária para executar o trabalho apropriado no momento apropriado.

### <a name="apache-oozie"></a>Apache Oozie

Apache Oozie é um sistema de coordenação do fluxo de trabalho que gerencia trabalhos do Hadoop. Oozie é executado em um cluster HDInsight e está integrado com a pilha do Hadoop. O Oozie dá suporte a trabalhos do Hadoop para Apache Hadoop MapReduce, Apache Pig, Apache Hive e Apache Sqoop. Oozie também pode ser usado para agendar trabalhos específicos para um sistema, como programas Java ou scripts de shell.

Para obter mais informações, consulte [usar o Apache Oozie com Apache Hadoop para definir e executar um fluxo de trabalho no HDInsight](../hdinsight-use-oozie-linux-mac.md). Consulte também, [operacionalize o pipeline de dados](../hdinsight-operationalize-data-pipeline.md).

### <a name="azure-data-factory"></a>Fábrica de dados do Azure

O Azure Data Factory fornece recursos de orquestração na forma de plataforma como serviço. É um serviço de integração de dados baseado em nuvem que permite criar fluxos de trabalho controlados por dados na nuvem. Fluxos de trabalho para orquestrar e automatizar a movimentação de dados e a transformação de dados.

Usando o Azure Data Factory, você pode:

1. Criar e agendar fluxos de trabalhos orientados a dados (chamados pipelines) que ingerem dados provenientes de repositórios de dados diferentes.
2. Processe e transforme os dados usando serviços de computação, como Azure HDInsight Hadoop. Ou Spark, Azure Data Lake Analytics, lote do Azure e Azure Machine Learning.
3. Publicar dados de saída em repositórios de dados como o SQL Data Warehouse do Azure para que estes sejam consumidos por aplicativos de business intelligence (BI).

Para obter mais informações sobre o Azure Data Factory, consulte a [documentação](../../data-factory/introduction.md).

## <a name="ingest-file-storage-and-result-storage"></a>Armazenamento de arquivo e armazenamento de resultados de ingestão

Os arquivos de dados de origem geralmente são carregados em um local no Armazenamento do Azure ou no Azure Data Lake Storage. Os arquivos podem estar em qualquer formato, mas normalmente são arquivos simples como CSVs.

### <a name="azure-storage"></a>Armazenamento do Azure

O armazenamento do Azure tem destinos de adaptação específicos. Consulte [escalabilidade e metas de desempenho para o armazenamento de BLOBs](../../storage/blobs/scalability-targets.md). Para nós mais analíticos, o Armazenamento do Azure é dimensionado melhor ao lidar com vários arquivos menores.  O armazenamento do Azure garante o mesmo desempenho, independentemente de como, em que tamanho os arquivos (desde que você esteja dentro dos limites).  Essa garantia significa que você pode armazenar terabytes e ainda obter um desempenho consistente, quer você esteja usando um subconjunto dos dados ou todos os dados.

O Armazenamento do Azure tem vários tipos diferentes de blobs.  Um *append blob* é uma ótima opção para armazenar logs da web ou dados do sensor.  

Vários BLOBs podem ser distribuídos em vários servidores para expandir o acesso a eles. Mas um único blob só pode ser servido por um único servidor. Embora os blobs possam ser agrupados logicamente em contêineres de blob, o particionamento não é afetado de forma alguma por esse agrupamento.

O Armazenamento do Azure também tem uma camada de API WebHDFS para o armazenamento de blob.  Todos os serviços no HDInsight podem acessar arquivos no armazenamento de BLOBs do Azure para limpeza de dados e processamento de dados. Semelhante a como esses serviços usariam o HDFS (sistema de arquivos distribuído Hadoop).

Os dados são normalmente incluídos no Armazenamento do Azure usando o PowerShell, o SDK do Armazenamento do Azure ou AZCopy.

### <a name="azure-data-lake-storage"></a>Armazenamento do Azure Data Lake

Azure Data Lake Storage (ADLS) é um repositório gerenciado e de hiperescala. Um repositório para dados de análise que são compatíveis com o HDFS.  O ADLS usa um paradigma de design semelhante ao HDFS. O ADLS oferece adaptabilidade ilimitada para a capacidade total e o tamanho de arquivos individuais. ADLS é bom ao trabalhar com arquivos grandes, já que um arquivo grande pode ser armazenado em vários nós.  O particionamento de dados em ADLS é feito em segundo plano.  Você obtém uma enorme taxa de transferência para executar trabalhos analíticos com milhares de executores simultâneos que leem e gravam centenas de terabytes de dados com eficiência.

Normalmente, os dados são ingeridos no ADLS usando Azure Data Factory. Ou ADLS SDKs, AdlCopy Service, Apache DistCp ou Apache Sqoop.  Qual desses serviços depende em grande parte de onde os dados são.  Se a dados estão atualmente em um cluster de Hadoop existente, você pode usar o Apache DistCp, AdlCopy Service ou o Azure Data Factory.  Para dados no armazenamento de BLOBs do Azure, você pode usar Azure Data Lake Storage SDK do .NET, Azure PowerShell ou Azure Data Factory.

O ADLS também é otimizado para inclusão de eventos usando o Hub de Eventos do Azure ou Apache Storm.

#### <a name="considerations-for-both-storage-options"></a>Considerações para ambas as opções de armazenamento

Para carregar conjuntos de dados no intervalo terabyte, a latência de rede pode ser um problema grave, especialmente se os dados estão vindo de uma localização no local.  Nesses casos, você pode usar as opções a seguir:

* Azure ExpressRoute: O Azure ExpressRoute permite criar conexões privadas entre os datacenters do Azure e a sua infraestrutura local. Essas conexões proporcionam uma opção confiável para transferir grandes quantidades de dados. Para obter mais informações, confira a [documentação do ExpressRoute do Azure](../../expressroute/expressroute-introduction.md).

* Carregamento de dados "offline". Você poderá usar o [serviço de Importação/Exportação do Azure](../../storage/common/storage-import-export-service.md) para enviar unidades de disco rígido com seus dados para um datacenter do Azure. Seus dados são carregados pela primeira vez nos Blobs de Armazenamento do Azure. Em seguida, você poderá usar o Azure Data Factory ou a ferramenta AdlCopy para copiar os dados dos blobs do Armazenamento do Azure para o Data Lake Storage.

### <a name="azure-sql-data-warehouse"></a>SQL Data Warehouse do Azure

O SQL DW do Azure é uma ótima opção para armazenar resultados preparados.  O Azure HDInsight pode ser usado para fazer esses serviços para o SQL DW do Azure.

O SQL Data Warehouse do Azure (SQL DW) é um repositório de banco de dados relacional otimizado para cargas de trabalho analíticas.  O SQL DW do Azure é dimensionado com base em tabelas particionadas.  Tabelas podem ser particionadas em vários nós.  Nós do SQL DW do Azure são selecionados no momento da criação.  Eles podem ser dimensionados após o fato, mas isso é um processo ativo que pode exigir a movimentação de dados. Para obter mais informações, consulte [SQL data warehouse-gerenciar computação](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md).

### <a name="apache-hbase"></a>HBase no Apache

O Apache HBase é um repositório de chave-valor disponível no Azure HDInsight.  O Apache HBase é um banco de dados NoSQL de código-fonte aberto, que é compilado no Hadoop e modelado com base em Google BigTable. O HBase fornece acesso aleatório de alto desempenho e consistência forte para grandes quantidades de dados não estruturados e semiestruturadoss. Dados em um banco de dado sem esquema organizado por famílias de colunas.

Os dados são armazenados nas linhas de uma tabela e os dados contidos nas linhas são agrupados por famílias de colunas. O HBase é um banco de dados sem esquema. As colunas e os tipos de dados armazenados nelas não precisam ser definidos antes de usá-las. O código-fonte aberto é dimensionado linearmente para lidar com petabytes de dados em milhares de nós. O HBase pode contar com redundância de dados, processamento em lotes e outros recursos que são fornecidos por aplicativos distribuídos no ambiente do Hadoop.

O HBase é um excelente destino para dados de sensor e de log para análise futura.

A adaptação do HBase depende do número de nós no cluster HDInsight.

### <a name="azure-sql-database-and-azure-database"></a>Banco de Dados SQL do Azure e Azure Database

O Azure oferece três bancos de dados relacionais diferentes como plataforma-como-um-serviço (PAAS).

* [Banco de Dados SQL do Azure](../../sql-database/sql-database-technical-overview.md) é uma implementação do Microsoft SQL Server. Para obter mais informações sobre o desempenho, consulte [Ajustando o desempenho no Banco de Dados SQL do Azure](../../sql-database/sql-database-performance-guidance.md).
* [Banco de Dados do Azure para MySQL](../../mysql/overview.md) é uma implementação do Oracle MySQL.
* [Banco de Dados do Azure para PostgreSQL](../../postgresql/quickstart-create-server-database-portal.md) é uma implementação do Oracle PostgreSQL.

Esses produtos são escalados verticalmente, o que significa que eles são dimensionados adicionando mais CPU e memória.  Você também pode optar por usar discos premium com os produtos para melhorar o desempenho de I/O.

## <a name="azure-analysis-services"></a>Azure Analysis Services

A Azure Analysis Services (AAS) é um mecanismo de dados analíticos usado em suporte a decisões e análise de negócios. A AAS fornece os dados analíticos para relatórios de negócios e aplicativos cliente, como Power BI. Além disso, o Excel, Reporting Services relatórios e outras ferramentas de visualização de dados.

Cubos de análise podem ser dimensionados alterando camadas para cada cubo individual.  Para obter mais informações, consulte [Preços do Azure Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/).

## <a name="extract-and-load"></a>Extrair e carregar

Depois que os dados estiverem no Azure, você pode usar vários serviços para extraí-los e carregá-los em outros produtos.  O HDInsight oferece suporte a Sqoop e Flume.

### <a name="apache-sqoop"></a>Apache Sqoop

O Apache Sqoop é uma ferramenta projetada para transferir com eficiência os dados entre fontes de dados estruturados, semi-estruturados e não estruturados.

Sqoop usa MapReduce para importar e exportar os dados, para fornecer tolerância a falhas e a operação em paralelo.

### <a name="apache-flume"></a>Apache Flume

`Apache Flume`o é um serviço distribuído, confiável e disponível para coletar, agregar e mover grandes quantidades de dados de log com eficiência. O Flume tem uma arquitetura flexível baseada em fluxos de dados de streaming. O Flume é robusto e tolerante a falhas com mecanismos de confiabilidade ajustáveis e vários mecanismos de failover e recuperação. O Flume usa um modelo simples de dados extensíveis que permite a aplicação analítica on-line.

O Apache Flume não pode ser usado com o Azure HDInsight.  Uma instalação local do Hadoop pode usar o Flume para enviar os dados para Azure Storage Blobs ou o Azure Data Lake Storage.  Para obter mais informações, consulte [Usando o Apache Flume com HDInsight](https://web.archive.org/web/20190217104751/https://blogs.msdn.microsoft.com/bigdatasupport/2014/03/18/using-apache-flume-with-hdinsight/).

## <a name="transform"></a>Transformar

Uma vez que dados estiverem no local escolhido, você precisa limpá-los, combiná-os ou prepará-los para um padrão de uso específico.  Hive, Pig e Spark SQL são boas opções para esse tipo de trabalho.  Todos eles têm suporte no HDInsight.

## <a name="next-steps"></a>Próximas etapas

* [Usando o Apache Hive como uma ferramenta de ETL](apache-hadoop-using-apache-hive-as-an-etl-tool.md)
* [Usar Azure Data Lake Storage Gen2 com clusters do Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
* [Mover dados do banco de dados SQL do Azure para Apache Hive tabela](./apache-hadoop-use-sqoop-mac-linux.md)
