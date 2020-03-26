---
title: O que é o Apache HBase no Azure HDInsight?
description: Uma introdução ao Apache HBase no HDInsight, uma compilação de banco de dados NoSQL no Hadoop. Aprenda sobre casos de uso e compare HBase com outros clusters Hadoop.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 03/03/2020
ms.openlocfilehash: 97814f4d22629fd74f395887a7361a3aabe55012
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2020
ms.locfileid: "78271844"
---
# <a name="what-is-apache-hbase-in-azure-hdinsight"></a>O que é o Apache HBase no Azure HDInsight

O [Apache HBase](https://hbase.apache.org/) é um banco de dados NoSQL de software livre, que se baseia no [Apache Hadoop](https://hadoop.apache.org/) e é modelado com base no [Google BigTable](https://cloud.google.com/bigtable/). O HBase fornece acesso aleatório e uma coerência forte para grandes quantidades de dados não estruturados e semiestruturados em um banco de dados sem esquema, organizado por famílias de colunas.

Da perspectiva do usuário, o HBase é semelhante a um banco de dados. Os dados são armazenados nas linhas e colunas de uma tabela e os dados em uma linha são agrupados por família de colunas. O HBase é um banco de dados sem esquema, no aspecto de que nem as colunas nem os tipos de dados armazenados nelas precisam ser definidos antes de serem utilizados. O código-fonte aberto é dimensionado linearmente para lidar com petabytes de dados em milhares de nós. Ele pode fazer uso da redundância de dados, do processamento em lote e de outros recursos que são fornecidos por aplicativos distribuídos no ecossistema do Hadoop.

## <a name="how-is-apache-hbase-implemented-in-azure-hdinsight"></a>Como o Apache HBase é implementado no Azure HDInsight?

O HBase do HDInsight é oferecido como um cluster gerenciado integrado ao ambiente do Azure. Os clusters são configurados para armazenar dados diretamente no [Armazenamento do Azure](./../hdinsight-hadoop-use-blob-storage.md), que fornece baixa latência e maior elasticidade nas escolhas de desempenho e custo. Isso permite que os clientes criem sites interativos que trabalham com grandes conjuntos de dados, de modo a criar serviços que armazenam dados de sensor e telemetria de milhões de pontos de extremidade e analisar esses dados por meio de trabalhos do Hadoop. O HBase e o Hadoop são bons pontos iniciais para projetos de big data no Azure e, especificamente, podem possibilitar que aplicativos em tempo real trabalhem com grandes conjuntos de dados.

A implementação do HDInsight utiliza a arquitetura de expansão do HBase para fornecer o compartilhamento automático de tabelas, uma sólida consistência para leituras e gravações e failover automático. O desempenho é aprimorado pelo cache na memória para leituras e streaming de alta produtividade para gravações. O cluster do HBase pode ser criado dentro da rede virtual. Para obter detalhes, consulte [Criar clusters do HDInsight na Rede Virtual do Azure](./apache-hbase-provision-vnet.md).

## <a name="how-is-data-managed-in-hdinsight-hbase"></a>Como os dados no HBase do HDInsight são gerenciados?

Os dados podem ser gerenciados no HBase usando os comandos `create`, `get`, `put` e `scan` do shell do HBase. Os dados são gravados no banco de dados usando `put` e lidos usando `get`. O comando `scan` é utilizado para obter dados de múltiplas linhas em uma tabela. Os dados também podem ser gerenciados utilizando a API C# do HBase, que oferece uma biblioteca de cliente sobre a API REST do HBase. Um banco de dados HBase também pode ser consultado usando o [Apache Hive](https://hive.apache.org/). Para ver uma introdução a esses modelos de programação, consulte [Introdução ao uso do Apache HBase com Apache Hadoop no HDInsight](./apache-hbase-tutorial-get-started-linux.md). Também estão disponíveis coprocessadores, que permitem o processamento de dados em nós que hospedam o banco de dados.

> [!NOTE]  
> Não há suporte para thrift pelo HBase no HDInsight.

## <a name="use-cases-for-apache-hbase"></a>Casos de uso para o Apache HBase

O caso de uso canônico para o qual o BigTable (e por extensão, o HBase) foi criado a partir da pesquisa na web. Os mecanismos de pesquisa criam índices que mapeiam termos nas páginas da web que os contêm. Mas há muitos outros casos de uso aos quais o HBase se ajusta, muitos dos quais são descritos nesta seção.

|Cenário |DESCRIÇÃO |
|---|---|
|Repositório de valor-chave|O HBase pode ser usado como um repositório de pares chave-valor e é adequado para gerenciar sistemas de mensagens. O Facebook usa o HBase para o sistema de mensagens. Ele é ideal para armazenar e gerenciar comunicações pela Internet. O WebTable utiliza o HBase para pesquisar e gerenciar tabelas extraídas de páginas da Web.|
|Dados de sensor|O HBase é útil para capturar dados que são coletados gradativamente de várias fontes. Isso inclui Análise Social, séries temporais, manter painéis interativos atualizados com tendências e contadores e gerenciar sistemas de log de auditoria. Os exemplos incluem o terminal de comerciantes Bloomberg e o OpenTSDB (Open Time Series Database), que armazena e oferece acesso a métricas coletadas sobre a integridade dos sistemas de servidores.|
|Consulta em tempo real|[Apache Phoenix](https://phoenix.apache.org/) é um mecanismo de consulta SQL para o HBase no Apache. Ele é acessado como um driver JDBC e permite consultar e gerenciar tabelas do HBase com o SQL.|
|HBase como uma plataforma|Os aplicativos podem ser executados sobre o HBase utilizando-o como um armazenamento de dados. Exemplos incluem Phoenix, [OpenTSDB](http://opentsdb.net/), Kiji e Titan. Os aplicativos também podem ser integrados ao HBase. Os exemplos incluem [Apache Hive](https://hive.apache.org/), [Apache Pig](https://pig.apache.org/), [Solr](https://lucene.apache.org/solr/), [Apache Storm](https://storm.apache.org/), [Apache Flume](https://flume.apache.org/), [ Apache Impala](https://impala.apache.org/), [Apache Spark](https://spark.apache.org/) , [Ganglia](http://ganglia.info/), e [Apache Drill](https://drill.apache.org/).|

## <a name="next-steps"></a>Próximas etapas

* [Introdução ao uso do Apache HBase com o Apache Hadoop no HDInsight](./apache-hbase-tutorial-get-started-linux.md)
* [Criar clusters do HDInsight na Rede Virtual do Azure](./apache-hbase-provision-vnet.md)
* [Configurar a replicação do Apache HBase no HDInsight](apache-hbase-replication.md)
