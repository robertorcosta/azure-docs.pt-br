---
title: Usar armazenamentos de metadados externos - Azure HDInsight
description: Use external metadata stores with Azure HDInsight clusters, and best practices.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/29/2019
ms.openlocfilehash: 14b36a391778649e96694f1cb1d3a1b4e7ee89ba
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327361"
---
# <a name="use-external-metadata-stores-in-azure-hdinsight"></a>Usar armazenamentos de metadados externos no Azure HDInsight

HDInsight allows you to take control of your data and metadata by deploying key metadata solutions and management databases to external data stores. This feature is currently available for [Apache Hive metastore](#custom-metastore), [Apache Oozie metastore](#apache-oozie-metastore) and [Apache Ambari database](#custom-ambari-db).

O metastore Apache Hive no HDInsight é uma parte essencial da arquitetura do Apache Hadoop. Um metastore é o repositório central de esquema que pode ser usado por outras ferramentas de acesso de Big Data, como Apache Spark, Interactive Query (LLAP), Presto ou Apache Pig. O HDInsight usa um Banco de Dados SQL do Azure como metastore do Hive.

![Arquitetura de armazenamento de metadados Hive do HDInsight](./media/hdinsight-use-external-metadata-stores/metadata-store-architecture.png)

Há duas maneiras de configurar um metastore para seus clusters HDInsight:

* [Metastore padrão](#default-metastore)
* [Metastore personalizado](#custom-metastore)

## <a name="default-metastore"></a>Metastore padrão

Por padrão, o HDInsight cria um metastore com cada tipo de cluster. Em vez disso, você pode especificar um metastore personalizado. O metastore padrão inclui as seguintes considerações:

* Sem custo adicional. O HDInsight cria um metastore com cada tipo de cluster sem nenhum custo adicional para você.

* Cada metastore padrão é parte do ciclo de vida do cluster. Quando você exclui um cluster, o metastore e os metadados correspondentes também são excluídos.

* You can't share the default metastore with other clusters.

* O metastore padrão usa o SQL DB básico do Azure, que possui um limite de cinco DTUs (unidade de transação do banco de dados).
Esse metastore padrão é normalmente usado para cargas de trabalho relativamente simples que não exigem vários clusters e não precisam de metadados preservados além do ciclo de vida do cluster.

## <a name="custom-metastore"></a>Metastore personalizado

O HDInsight também dá suporte a metastores personalizados, que são recomendados para clusters de produção:

* Você pode especificar seu próprio Banco de Dados SQL do Azure com o metastore.

* The lifecycle of the metastore isn't tied to a clusters lifecycle, so you can create and delete clusters without losing metadata. Os metadados, como os esquemas Hive, serão mantidos até mesmo depois de você excluir e recriar o cluster HDInsight.

* Um metastore personalizado permite que você anexe vários clusters e tipos de cluster ao metastore. Por exemplo, um único metastore pode ser compartilhado em clusters Interactive Query, Hive e Spark no HDInsight.

* Você paga o custo de um metastore (Banco de Dados SQL do Azure) de acordo com o nível de desempenho escolhido.

* Você pode expandir o metastore conforme a necessidade.

![Caso de uso do armazenamento de metadados Hive do HDInsight](./media/hdinsight-use-external-metadata-stores/metadata-store-use-case.png)

### <a name="create-and-config-azure-sql-database-for-the-custom-metastore"></a>Create and config Azure SQL Database for the custom metastore

You need to create or have an existing Azure SQL Database before setting up a custom Hive metastore for a HDInsight cluster.  For more information, see [Quickstart: Create a single database in Azure SQL DB](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-get-started?tabs=azure-portal).

To make sure that your HDInsight cluster can access the connected Azure SQL Database, configure Azure SQL Database firewall rules to allow Azure services and resources to access the server.

You can enable this option in the Azure portal by clicking **Set server firewall**, and clicking **ON** underneath **Allow Azure services and resources to access this server** for the Azure SQL Database server or database. For more information, see [Create and manage IP firewall rules](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#use-the-azure-portal-to-manage-server-level-ip-firewall-rules)

![set server firewall button](./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall1.png)

![allow azure services access](./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall2.png)

### <a name="select-a-custom-metastore-during-cluster-creation"></a>Selecione um metastore personalizado durante a criação do cluster

Você pode apontar seu cluster para um Banco de Dados SQL do Azure criado anteriormente durante a criação do cluster, ou configurar o Banco de Dados SQL depois que o cluster é criado. This option is specified with the **Storage > Metastore settings** while creating a new Hadoop, Spark, or interactive Hive cluster from Azure portal.

![Armazenamento de metadados Hive do HDInsight Portal do Azure](./media/hdinsight-use-external-metadata-stores/azure-portal-cluster-storage-metastore.png)

## <a name="hive-metastore-best-practices"></a>Melhores práticas de metastore Hive

Aqui estão algumas melhores práticas gerais de metastore Hive do HDInsight:

* Use um metastore personalizado sempre que possível, para ajudar a separar recursos de computação (seu cluster em execução) e metadados (armazenados no metastore).

* Inicie com uma camada S2, que fornece 50 DTU e 250 GB de armazenamento. Se você vir um afunilamento, poderá expandir o banco de dados.

* Se você pretende que vários clusters HDInsight acessem dados separados, use um banco de dados separado para o metastore em cada cluster. Se você compartilhar um metastore entre vários clusters do HDInsight, isso significa que os clusters usam os mesmos metadados e arquivos de dados do usuário subjacentes.

* Faça backup de seu metastore personalizado periodicamente. O Banco de Dados SQL do Azure gera backups automaticamente, mas o prazo de retenção de backup varia. Para obter mais informações, consulte [Saiba mais sobre os backups automáticos do Banco de Dados SQL](../sql-database/sql-database-automated-backups.md).

* Localize seu cluster do metastore e HDInsight na mesma região, para obter o mais alto desempenho e os menores custos de saída de rede.

* Monitor your metastore for performance and availability using Azure SQL Database Monitoring tools, such as the Azure portal or Azure Monitor logs.

* Quando uma nova versão superior do Azure HDInsight é criada em um banco de dados metastore personalizado existente, o sistema atualiza o esquema do metastore, que é irreversível sem restaurar o backup do banco de dados.

* Se você compartilhar um metastore em vários clusters, verifique se todos os clusters são da mesma versão do HDInsight. Diferentes versões do Hive usam diferentes esquemas de banco de dados metastore. For example, you can't share a metastore across Hive 2.1 and Hive 3.1 versioned clusters.

* In HDInsight 4.0, Spark and Hive use independent catalogs for accessing SparkSQL or Hive tables. A table created by Spark resides in the Spark catalog. A table created by Hive resides in the Hive catalog. This is different than HDInsight 3.6 where Hive and Spark shared common catalog. Hive and Spark Integration in HDInsight 4.0 relies on Hive Warehouse Connector (HWC). HWC works as a bridge between Spark and Hive. [Learn about Hive Warehouse Connector](../hdinsight/interactive-query/apache-hive-warehouse-connector.md).

## <a name="apache-oozie-metastore"></a>Apache Oozie metastore

Apache Oozie é um sistema de coordenação do fluxo de trabalho que gerencia trabalhos do Hadoop.  O Oozie dá suporte a trabalhos do Hadoop para Apache MapReduce, Pig, Hive e outros.  O Oozie usa um metastore para armazenar detalhes sobre fluxos de trabalho atuais e concluídos. Para aumentar o desempenho ao usar o Oozie, você pode usar o Banco de Dados SQL do Azure como um metastore personalizado. O metastore também pode fornecer acesso a dados de trabalho do Oozie depois de você excluir o cluster.

Para obter instruções sobre como criar um metastore Oozie com o Banco de Dados SQL do Azure, confira [Usar o Apache Oozie para fluxos de trabalho](hdinsight-use-oozie-linux-mac.md).

## <a name="custom-ambari-db"></a>BD Ambari personalizado

To use your own external database with Apache Ambari on HDInsight, see [Custom Apache Ambari database](hdinsight-custom-ambari-db.md).

## <a name="next-steps"></a>Próximos passos

* [Configure os clusters no HDInsight com o Apache Hadoop, o Apache Spark, o Apache Kafka e muito mais](./hdinsight-hadoop-provision-linux-clusters.md)
