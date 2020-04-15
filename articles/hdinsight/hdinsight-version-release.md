---
title: Visão geral do HDInsight 4.0 – Azure
description: Compare o HDInsight 3.6 com os recursos, as limitações e as recomendações de atualização do HDInsight 4.0.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: d0fd9999abc4a67ded0f66977e1a3ba5310c87be
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383042"
---
# <a name="azure-hdinsight-40-overview"></a>Visão geral do Azure HDInsight 4.0

O Azure HDInsight é um dos serviços mais populares entre os clientes corporativos do Apache Hadoop e apache Spark. HDInsight 4.0 é uma distribuição em nuvem de componentes Apache Hadoop. Este artigo fornece informações sobre a versão mais recente do Azure HDInsight e como atualizar.

## <a name="whats-new-in-hdinsight-40"></a>Novidades do HDInsight 4.0

### <a name="apache-hive-30-and-low-latency-analytical-processing"></a>Colmeia Apache 3.0 e processamento analítico de baixa latência

O processamento analítico de baixa latência do Apache Hive (LLAP) usa servidores de consulta persistentes e cache na memória. Este processo oferece resultados rápidos de consulta SQL em dados em armazenamento remoto em nuvem. Hive LLAP usa um conjunto de daemons persistentes que executam fragmentos de consultas de Colmeia. A execução da consulta em LLAP é semelhante ao Hive sem LLAP, com tarefas de trabalho em execução dentro de daemons de LLAP em vez de contêineres.

Os benefícios do LLAP do Hive incluem:

* Capacidade de fazer análises sql profundas sem sacrificar o desempenho e a adaptabilidade. Como adesões complexas, subconsultas, funções de janelas, classificação, funções definidas pelo usuário e agregações complexas.

* Consultas interativas em dados no mesmo armazenamento em que os dados são preparados, eliminando a necessidade de movê-los do armazenamento para outro mecanismo a fim de realizar o processamento analítico.

* Os resultados da consulta de cache permitem que os resultados da consulta previamente computados sejam reutilizados. Esse cache economiza tempo e recursos gastos executando as tarefas de cluster necessárias para a consulta.

### <a name="hive-dynamic-materialized-views"></a>Exibições materializadas dinâmicas do Hive

A Colmeia agora suporta visões dinâmicas materializadas, ou pré-computação de resumos relevantes. As visualizações aceleram o processamento de consultas em data warehouses. As exibições materializadas podem ser armazenadas nativamente no Hive e podem usar a aceleração de LLAP perfeitamente.

### <a name="hive-transactional-tables"></a>Tabelas transacionais do Hive

HDI 4.0 inclui Colmeia Apache 3. A Colmeia 3 requer atônidade, consistência, isolamento e conformidade de durabilidade para tabelas transacionais que vivem no armazém hive. As tabelas e dados de tabela em conformidade com ACID são acessadas e gerenciadas pelo Hive. Os dados nas tabelas criar, recuperar, atualizar e excluir (CRUD) devem estar no formato de arquivo Optimized Row Column (ORC). Tabelas somente de inserção suportam todos os formatos de arquivo.

* O ACID v2 tem melhorias de desempenho no formato de armazenamento e no mecanismo de execução.

* O ACID é habilitado por padrão para permitir o suporte completo para as atualizações de dados.

* Os recursos aprimorados de ACID permitem que você atualize e exclua no nível de linha.

* Sem sobrecarga de desempenho.

* Sem necessidade de Bucketing.

* O Spark pode ler e gravar em tabelas ACID do Hive por meio do Conector de Warehouse do Hive.

Saiba mais sobre o [Apache Hive 3](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/hive-overview/content/hive_whats_new_in_this_release_hive.html).

### <a name="apache-spark"></a>Apache Spark

O Apache Spark obtém tabelas atualizáveis e transações ACID com o Conector de Warehouse do Hive. O Conector de Warehouse do Hive permite que você registre tabelas transacionais do Hive como tabelas externas no Spark para acessar toda a funcionalidade transacional. As versões anteriores só eram compatíveis com manipulação de partição de tabela. O Conector do Armazém Hive também suporta dataframes de streaming.  Este processo transmite leituras e gravações em tabelas transacionais e de streaming da Spark.

Os executores do Spark podem se conectar diretamente aos daemons de LLAP do Hive para recuperar e atualizar dados de forma transacional, permitindo que o Hive mantenha o controle dos dados.

O Apache Spark no HDInsight 4.0 é compatível com os seguintes cenários:

* Executar o treinamento do modelo de machine learning sobre a mesma tabela transacional usada para relatórios.
* Usar transações ACID para adicionar colunas de AM do Spark com segurança em uma tabela do Hive.
* Executar um trabalho de streaming do Spark no feed de alterações de uma tabela de streaming do Hive.
* Criar arquivos ORC diretamente de um trabalho de Streaming Estruturado do Spark.

Você não precisa mais se preocupar em acidentalmente tentar acessar tabelas transacionais da Colmeia diretamente da Spark. Resultando em resultados inconsistentes, dados duplicados ou corrupção de dados. No HDInsight 4.0, as tabelas do Spark e do Hive são mantidas em Metastores separados. Use o Conector de Data Warehouse do Hive para registrar explicitamente as tabelas transacionais do Hive como tabelas externas do Spark.

Saiba mais sobre o [Apache Spark](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/spark-overview/content/analyzing_data_with_apache_spark.html).

### <a name="apache-oozie"></a>Apache Oozie

O Apache Oozie 4.3.1 está incluído no HDI 4.0 com as seguintes alterações:

* O Oozie não executa mais ações do Hive. A CLI do Hive foi removida e substituída pela BeeLine.

* Você pode excluir dependências indesejáveis do compartilhamento lib por meio da inclusão de um padrão de exclusão no arquivo **job.properties**.

Saiba mais sobre o [Apache Oozie](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/release-notes/content/patch_oozie.html).

## <a name="how-to-upgrade-to-hdinsight-40"></a>Como atualizar para o HDInsight 4.0

Teste minuciosamente seus componentes antes de implementar a versão mais recente em um ambiente de produção. O HDInsight 4.0 está disponível para você iniciar o processo de atualização. HDInsight 3.6 é a opção padrão para evitar contratempos acidentais.

Não há caminho de atualização suportado das versões anteriores do HDInsight para o HDInsight 4.0. Como os formatos metastore e blob data foram alterados, o 4.0 não é compatível com as versões anteriores. É importante que você mantenha seu novo ambiente HDInsight 4.0 separado do seu ambiente de produção atual. Se você implantar o HDInsight 4.0 no ambiente atual, o Metastore será permanentemente atualizado.  

## <a name="limitations"></a>Limitações

* O HDInsight 4.0 não suporta MapReduce for Apache Hive. Use o Apache Tez em vez disso. Saiba mais sobre o [Apache Tez](https://tez.apache.org/).
* O HDInsight 4.0 não suporta o Apache Storm.
* A exibição do Hive não está mais disponível no HDInsight 4.0.
* O intérprete Shell no Apache Zeppelin não é suportado em clusters spark e interactive query.
* Não é possível *desabilitar* o LLAP em um cluster Spark-LLAP. Você só pode desligar llap.
* O Azure Data Lake Storage Gen2 não pode salvar notebooks Jupyter em um cluster Spark.

## <a name="next-steps"></a>Próximas etapas

* [Documentação do Azure HDInsight](index.yml)
* [Notas de Versão](hdinsight-release-notes.md)
