---
title: Usar armazenamentos de metadados externos - Azure HDInsight
description: Use armazenamentos externos de metadados com clusters Azure HDInsight e práticas recomendadas.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/02/2020
ms.openlocfilehash: edb2d256d3e5d98c52dbdff1162e0e030ebe2be3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272156"
---
# <a name="use-external-metadata-stores-in-azure-hdinsight"></a>Usar armazenamentos de metadados externos no Azure HDInsight

O HDInsight permite que você assuma o controle de seus dados e metadados, implantando as principais soluções de metadados e bancos de dados de gerenciamento em armazenamentos de dados externos. Este recurso está atualmente disponível para [metastore Apache Hive,](#custom-metastore) [metastore Apache Oozie](#apache-oozie-metastore) e [banco de dados Apache Ambari](#custom-ambari-db).

O metastore Apache Hive no HDInsight é uma parte essencial da arquitetura do Apache Hadoop. Um metastore é o repositório central de esquema que pode ser usado por outras ferramentas de acesso de Big Data, como Apache Spark, Interactive Query (LLAP), Presto ou Apache Pig. O HDInsight usa um Banco de Dados SQL do Azure como metastore do Hive.

![Arquitetura de armazenamento de metadados Hive do HDInsight](./media/hdinsight-use-external-metadata-stores/metadata-store-architecture.png)

Há duas maneiras de configurar um metastore para seus clusters HDInsight:

* [Metastore padrão](#default-metastore)
* [Metastore personalizado](#custom-metastore)

## <a name="default-metastore"></a>Metastore padrão

Por padrão, o HDInsight cria um metastore com cada tipo de cluster. Em vez disso, você pode especificar um metastore personalizado. O metastore padrão inclui as seguintes considerações:

* Sem custo adicional. O HDInsight cria um metastore com cada tipo de cluster sem nenhum custo adicional para você.

* Cada metastore padrão é parte do ciclo de vida do cluster. Quando você exclui um cluster, o metastore e os metadados correspondentes também são excluídos.

* Você não pode compartilhar o metastore padrão com outros clusters.

* O metastore padrão usa o SQL DB básico do Azure, que possui um limite de cinco DTUs (unidade de transação do banco de dados).
Esse metastore padrão é normalmente usado para cargas de trabalho relativamente simples que não exigem vários clusters e não precisam de metadados preservados além do ciclo de vida do cluster.

## <a name="custom-metastore"></a>Metastore personalizado

O HDInsight também dá suporte a metastores personalizados, que são recomendados para clusters de produção:

* Você pode especificar seu próprio Banco de Dados SQL do Azure com o metastore.

* O ciclo de vida do metastore não está vinculado a um ciclo de vida de clusters, então você pode criar e excluir clusters sem perder metadados. Os metadados, como os esquemas Hive, serão mantidos até mesmo depois de você excluir e recriar o cluster HDInsight.

* Um metastore personalizado permite que você anexe vários clusters e tipos de cluster ao metastore. Por exemplo, um único metastore pode ser compartilhado em clusters Interactive Query, Hive e Spark no HDInsight.

* Você paga o custo de um metastore (Banco de Dados SQL do Azure) de acordo com o nível de desempenho escolhido.

* Você pode expandir o metastore conforme a necessidade.

* O cluster e o metastore externo devem ser hospedados na mesma região.

![Caso de uso do armazenamento de metadados Hive do HDInsight](./media/hdinsight-use-external-metadata-stores/metadata-store-use-case.png)

### <a name="create-and-config-azure-sql-database-for-the-custom-metastore"></a>Criar e configurá-lo Banco de Dados SQL do Azure para o metastore personalizado

Você precisa criar ou ter um banco de dados Azure SQL existente antes de configurar uma metaloja hive personalizada para um cluster HDInsight.  Para obter mais informações, consulte [Quickstart: Crie um único banco de dados no Azure SQL DB](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-get-started?tabs=azure-portal).

Para garantir que seu cluster HDInsight possa acessar o banco de dados Azure SQL conectado, configure as regras de firewall do Banco de Dados SQL do Azure para permitir que os serviços e recursos do Azure acessem o servidor.

Você pode habilitar essa opção no portal Azure clicando em **Definir firewall de servidor**e clicando em **ON** abaixo Permitir que os serviços e recursos do **Azure acessem este servidor** para o servidor ou banco de dados Do Azure SQL. Para obter mais informações, consulte [Criar e gerenciar regras de firewall IP](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#use-the-azure-portal-to-manage-server-level-ip-firewall-rules)

![definir botão de firewall servidor](./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall1.png)

![permitir acesso a serviços azure](./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall2.png)

### <a name="select-a-custom-metastore-during-cluster-creation"></a>Selecione um metastore personalizado durante a criação do cluster

Você pode apontar seu cluster para um Banco de Dados SQL do Azure criado anteriormente durante a criação do cluster, ou configurar o Banco de Dados SQL depois que o cluster é criado. Esta opção é especificada com as **configurações de Armazenamento > Metastore** enquanto cria um novo cluster Hadoop, Spark ou colmeia interativo do portal Azure.

![Armazenamento de metadados Hive do HDInsight Portal do Azure](./media/hdinsight-use-external-metadata-stores/azure-portal-cluster-storage-metastore.png)

## <a name="hive-metastore-best-practices"></a>Melhores práticas de metastore Hive

Aqui estão algumas melhores práticas gerais de metastore Hive do HDInsight:

* Use um metastore personalizado sempre que possível, para ajudar a separar recursos de computação (seu cluster em execução) e metadados (armazenados no metastore).

* Inicie com uma camada S2, que fornece 50 DTU e 250 GB de armazenamento. Se você vir um afunilamento, poderá expandir o banco de dados.

* Se você pretende que vários clusters HDInsight acessem dados separados, use um banco de dados separado para o metastore em cada cluster. Se você compartilhar um metastore entre vários clusters do HDInsight, isso significa que os clusters usam os mesmos metadados e arquivos de dados do usuário subjacentes.

* Faça backup de seu metastore personalizado periodicamente. O Banco de Dados SQL do Azure gera backups automaticamente, mas o prazo de retenção de backup varia. Para obter mais informações, consulte [Saiba mais sobre os backups automáticos do Banco de Dados SQL](../sql-database/sql-database-automated-backups.md).

* Localize seu cluster do metastore e HDInsight na mesma região, para obter o mais alto desempenho e os menores custos de saída de rede.

* Monitore seu metastore para obter desempenho e disponibilidade usando ferramentas de monitoramento de banco de dados Do Azure SQL, como o portal Azure ou os logs do Monitor Azure.

* Quando uma nova versão superior do Azure HDInsight é criada em um banco de dados metastore personalizado existente, o sistema atualiza o esquema do metastore, que é irreversível sem restaurar o backup do banco de dados.

* Se você compartilhar um metastore em vários clusters, verifique se todos os clusters são da mesma versão do HDInsight. Diferentes versões do Hive usam diferentes esquemas de banco de dados metastore. Por exemplo, você não pode compartilhar um metastore em clusters com versões Hive 2.1 e Hive 3.1.

* No HDInsight 4.0, a Spark and Hive usa catálogos independentes para acessar as tabelas SparkSQL ou Hive. Uma tabela criada pela Spark reside no catálogo da Spark. Uma tabela criada pela Colmeia reside no catálogo da Colmeia. Isso é diferente do HDInsight 3.6, onde hive e spark compartilharam catálogo comum. A integração de colmeias e faíscas no HDInsight 4.0 conta com o Conector do Armazém Colmeia (HWC). HWC funciona como uma ponte entre Spark e Hive. [Saiba mais sobre o conector do armazém colmeia](../hdinsight/interactive-query/apache-hive-warehouse-connector.md).

## <a name="apache-oozie-metastore"></a>Metaloja Apache Oozie

Apache Oozie é um sistema de coordenação do fluxo de trabalho que gerencia trabalhos do Hadoop.  O Oozie dá suporte a trabalhos do Hadoop para Apache MapReduce, Pig, Hive e outros.  O Oozie usa um metastore para armazenar detalhes sobre fluxos de trabalho atuais e concluídos. Para aumentar o desempenho ao usar o Oozie, você pode usar o Banco de Dados SQL do Azure como um metastore personalizado. O metastore também pode fornecer acesso a dados de trabalho do Oozie depois de você excluir o cluster.

Para obter instruções sobre como criar um metastore Oozie com o Banco de Dados SQL do Azure, confira [Usar o Apache Oozie para fluxos de trabalho](hdinsight-use-oozie-linux-mac.md).

## <a name="custom-ambari-db"></a>BD Ambari personalizado

Para usar seu próprio banco de dados externo com o Apache Ambari no HDInsight, consulte [o banco de dados Personal Apache Ambari](hdinsight-custom-ambari-db.md).

## <a name="next-steps"></a>Próximas etapas

* [Configure os clusters no HDInsight com o Apache Hadoop, o Apache Spark, o Apache Kafka e muito mais](./hdinsight-hadoop-provision-linux-clusters.md)
