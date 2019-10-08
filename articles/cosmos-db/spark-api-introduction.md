---
title: Introdução à análise operacional interna no Azure Cosmos DB com o Apache Spark
description: Saiba como usar o suporte interno para o Apache Spark no Azure Cosmos DB para executar análise operacional e IA
ms.service: cosmos-db
ms.topic: overview
ms.date: 09/26/2019
author: rimman
ms.author: rimman
ms.openlocfilehash: 952dd084bccd67a0a8833002d73d3aaf1d5dfb25
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338621"
---
# <a name="built-in-operational-analytics-in-azure-cosmos-db-with-apache-spark"></a>Análise operacional interna no Azure Cosmos DB com o Apache Spark

Com o Azure Cosmos DB, você pode executar análise de baixa latência globalmente distribuída e IA em dados transacionais. Com suporte nativo para Apache Spark e Jupyter Notebooks, o Azure Cosmos DB ajuda a reduzir o tempo de geração de insights. Porque os dados são geridos e fornecidos e as análises são executadas na réplica de banco de dados local em uma região do Azure. Você pode executar consultas do Apache Spark diretamente em dados multimodelo indexados armazenados em suas partições de dados.

O suporte interno para o Apache Spark no Azure Cosmos DB permite que você execute uma análise no Apache Spark de seus dados armazenados em uma conta do Azure Cosmos. Ele fornece o suporte nativo para que os trabalhos do Apache Spark sejam executados diretamente nos bancos de dados do Cosmos distribuídos globalmente. Com esses recursos, os desenvolvedores, engenheiros de dados e os cientistas de dados podem usar o Azure Cosmos DB como uma plataforma de dados flexível, escalonável e de alto desempenho para executar as cargas de trabalho **OLTP e OLAP/HTAP**.

O suporte do Apache Spark no Azure Cosmos DB oferece os seguintes benefícios:

* É possível obter o melhor tempo à análise de dados e usuários distribuídos geograficamente.

* É possível simplificar a arquitetura da solução e diminuir o [Custo Total de Propriedade](total-cost-ownership.md) (TCO). O sistema terá o menor número de componentes de processamento de dados e evitará qualquer movimentação de dados desnecessária entre eles.

* Cria uma [segurança](secure-access-to-data.md), [conformidade](compliance.md) e o limite de auditoria que abrange todos os dados em gerenciamento.

* Fornece análises “sempre ativas” ou [altamente disponíveis](high-availability.md) do usuário final que contam com SLAs rigorosos.

![Suporte do Apache Spark na visualização do Azure Cosmos DB](./media/spark-api-introduction/spark-api-visualization.png)
 
Usando o suporte do Apache Spark no Azure Cosmos DB, você pode criar e implantar soluções, como modelos de aprendizado profundo e IA, análise preditiva, recomendações, IoT, cliente 360, detecção de fraudes, sentimento do texto e análise da sequência de cliques. Essas soluções funcionam diretamente em seus dados do Azure Cosmos DB.

É possível configurar o lote e trabalho ETL de streaming no Azure Cosmos DB, sem ter que sair do serviço de banco de dados ou de serviços de computação adicionais. Você pode dimensionar elasticamente o ambiente de computação quando você precisa realizar o trabalho ETL e reduzi-lo novamente quando o trabalho é concluído.

O suporte do Apache Spark no Azure Cosmos DB oferece suporte interno do Machine Learning nos tempos de execução do Apache Spark. Os tempos de execução incluem Spark MLLib, Microsoft Machine Learning para Apache Spark, Azure Machine Learning e Serviços Cognitivos. Com esses recursos, os cientistas de dados, engenheiros de dados e analistas de dados podem criar e operacionalizar os modelos de machine learning diretamente no Azure Cosmos DB, em uma fração de tempo e com baixo custo.


## <a name="key-benefits"></a>Principais benefícios

### <a name="low-latency-operational-analytics-and-ai"></a>Análises operacionais de baixa latência e IA

Com o Apache Spark no banco de dados distribuído globalmente do Azure Cosmos, agora você pode obter tempo de insight rapidamente em todo o mundo. O Azure Cosmos DB possibilita **análise operacional de baixa latência distribuída globalmente** em escala elástica com três técnicas principais:

* Uma vez que seu banco de dados do Azure Cosmos é distribuído globalmente, todos os dados são ingeridos localmente, onde se encontram os produtores de dados (por exemplo, os usuários). As consultas são atendidas em relação às réplicas locais mais próximas de produtores e consumidores de dados, independentemente de onde estejam localizados no mundo.

* Todas as suas consultas analíticas são executadas diretamente nos dados armazenados indexados dentro de partições de dados precisar de nenhuma movimentação de dados desnecessários.

* Como o Apache Spark é colocado com o Azure Cosmos DB, menos traduções intermediárias e movimentações de dados ocorrem, resultando em um melhor desempenho e escalabilidade.

* Todos os principais recursos do Azure Cosmos DB, como vários mestres, failover automático, Zonas de Disponibilidade, etc., estão disponíveis para o Apache Spark interno no Azure Cosmos DB.

### <a name="unified-serverless-experience-for-apache-spark"></a>Experiência unificada sem servidor para o Apache Spark

Como um banco de dados multimodelo, o Azure Cosmos DB agora expande seu suporte para APIs OSS, fornecendo uma **experiência sem servidor unificada para o Apache Spark** com chave-valor, documento, grafo, modelos de dados da família de coluna. Modelos de dados diferentes têm suporte usando o MongoDB, Cassandra, Gremlin, Etcd e APIs do SQL, todos operando nos mesmos dados subjacentes. 

Com o suporte do Apache Spark no Azure Cosmos DB, você pode dar suporte nativo a aplicativos escritos em Scala, Python e Java e usar várias bibliotecas diretamente integradas para SQL. Essas bibliotecas incluem ([Spark SQL](https://spark.apache.org/sql/)), machine learning (Apache Spark [MLlib](https://spark.apache.org/mllib/)), processamento de streaming ([Streaming Estruturado do Apache Spark](https://spark.apache.org/streaming/)) e o processamento de grafos (Spark [GraphFrames]( https://docs.databricks.com/spark/latest/graph-analysis/graphframes/user-guide-python.html)). Essas ferramentas facilitam usar o Apache Spark para uma série de casos de uso. Não é necessário lidar com o gerenciamento do Apache Spark ou clusters do Apache Spark. É possível usar as APIs do Apache Spark e **notebooks Jupyter** para análise e API do SQL ou qualquer uma das APIs OSS NoSQL, como Cassandra para processamento transacional nos mesmos dados subjacentes ao mesmo tempo.

### <a name="no-schema-or-index-management"></a>Sem gerenciamento de esquema ou de índice

Ao contrário dos bancos de dados analíticos tradicionais, com o Azure Cosmos DB, os engenheiros e cientistas de dados não precisam mais lidar com o esquema complicado e o gerenciamento de índice. O mecanismo de banco de dados no Azure Cosmos DB não exige gerenciamento explícito de esquema ou índice e é capaz de indexar automaticamente todos os dados ingeridos para servir as consultas do Apache Spark rapidamente.

### <a name="consistency-choices"></a>Opções de consistência

Como os trabalhos do Apache Spark são executados nas partições de dados do seu banco de dados Azure Cosmos, as consultas apresentaram as [cinco opções de consistência bem definidas](consistency-levels.md). Esses modelos de consistência oferecem a flexibilidade de escolher consistência estrita para fornecer os resultados mais precisos para algoritmos de machine learning sem comprometer a latência e a alta disponibilidade.

### <a name="comprehensive-slas"></a>SLAs abrangentes

Os trabalhos do Apache Spark terão os benefícios do Azure Cosmos DB como líder abrangente do setor [SLAs](https://azure.microsoft.com/support/legal/sla/documentdb/v1_1/) (99,999) sem sobrecarga de gerenciamento de clusters separados do Apache Spark. Esses SLAs abrangem a taxa de transferência, latência no 99 º percentil, consistência e alta disponibilidade. 

### <a name="mixed-operational-and-analytical-workloads-with-complete-isolation"></a>Cargas de trabalho operacionais e analíticas mistas com isolamento completo

A integração do Apache Spark no Azure Cosmos DB associa a separação transacional e analítica, que tem sido um dos pontos problemáticos importantes do cliente ao criar aplicativos nativos de nuvem em escala global. As cargas de trabalho OLTP e OLAP são executadas lado a lado e não interferem entre si.

### <a name="low-latency-analytical-and-transactional-storage"></a>Armazenamento analítico e transacional de baixa latência

O Azure Cosmos DB armazena nativamente os dados em duas camadas de armazenamento diferentes: armazenamento transacional (orientado por linhas) e analítico (orientado por colunas, no formato de arquivo Apache Parquet). Ele replica os dados em cada nível globalmente e permite que os usuários gerenciem dados de maneira independente nessas camadas com base nas políticas de retenção.

Essa arquitetura de armazenamento permite cargas de trabalho transacionais e analíticas críticas nos mesmos dados distribuídos globalmente. Com o Cosmos DB, você não precisa de nenhuma operação ETL nem realizar nenhuma movimentação de dados desnecessária. Você pode simplesmente executar cargas de trabalho transacionais e analíticas nos mesmos dados subjacentes. As cargas de trabalho transacionais podem usar APIs de acesso transacional conhecidas, incluindo SQL, Cassandra, MongoDB, Gremlin e etcd. Enquanto as cargas de trabalho analíticas e de IA podem usar o Apache Spark SQL interno, as estruturas de ML e toda a cadeia de ferramentas do Apache Spark.

### <a name="snapshots-and-historical-analytical-queries"></a>Instantâneos e consultas analíticas históricas

Você pode criar instantâneos sob demanda ou na agenda dos dados compactados colunares armazenados na camada analítica para executar consultas analíticas diretamente em um instantâneo específico. Esse recurso habilita consultas analíticas de flashback ou de volta no tempo, reversões, trilhas de auditoria histórica completas e aprendizado de máquina e experimentos de IA que podem ser reproduzidos.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre os benefícios do Azure Cosmos DB, consulte o artigo de [visão geral](introduction.md).
* [Introdução à API do Azure Cosmos DB para MongoDB](mongodb-introduction.md)
* [Introdução à API do Cassandra no Azure Cosmos DB](cassandra-introduction.md)
* [Introdução à API do Gremlin no Azure Cosmos DB](graph-introduction.md)
* [Introdução à API de Tabela no Azure Cosmos DB](table-introduction.md)