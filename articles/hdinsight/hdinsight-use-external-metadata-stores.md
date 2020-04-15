---
title: Usar armazenamentos de metadados externos - Azure HDInsight
description: Use armazenamentos de metadados externos com clusters Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/03/2020
ms.openlocfilehash: e53164d1e25f8a8d0a14d21c0544d95cf912fe9f
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81313955"
---
# <a name="use-external-metadata-stores-in-azure-hdinsight"></a>Usar armazenamentos de metadados externos no Azure HDInsight

O HDInsight permite que você assuma o controle de seus dados e metadados com armazenamentos de dados externos. Este recurso está disponível para [metastore Apache Hive,](#custom-metastore) [metastore Apache Oozie](#apache-oozie-metastore)e [banco de dados Apache Ambari](#custom-ambari-db).

O metastore Apache Hive no HDInsight é uma parte essencial da arquitetura do Apache Hadoop. Um metastore é o repositório central do esquema. O metastore é usado por outras ferramentas de acesso a big data, como Apache Spark, Interactive Query (LLAP), Presto ou Apache Pig. O HDInsight usa um Banco de Dados SQL do Azure como metastore do Hive.

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
Este metastore padrão é normalmente usado para cargas de trabalho relativamente simples. Cargas de trabalho que não requerem vários clusters e não precisam de metadados preservados além do ciclo de vida do cluster.

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

Crie ou tenha um banco de dados Azure SQL existente antes de configurar um metastore hive personalizado para um cluster HDInsight.  Para obter mais informações, consulte [Quickstart: Crie um único banco de dados no Azure SQL DB](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-get-started?tabs=azure-portal).

Ao criar o cluster, o serviço HDInsight precisa se conectar ao metastore externo e verificar suas credenciais. Configure as regras de firewall do Banco de Dados Azure SQL para permitir que os serviços e recursos do Azure acessem o servidor. Habilite essa opção no portal Azure selecionando **Set server firewall**. Em seguida, **selecione Não** abaixo **Negar acesso à rede pública**e **Sim** abaixo Permita que **os serviços e recursos do Azure acessem esse servidor** para o servidor ou banco de dados Do Azure SQL. Para obter mais informações, consulte [Criar e gerenciar regras de firewall IP](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#use-the-azure-portal-to-manage-server-level-ip-firewall-rules)

![definir botão de firewall servidor](./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall1.png)

![permitir acesso a serviços azure](./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall2.png)

### <a name="select-a-custom-metastore-during-cluster-creation"></a>Selecione um metastore personalizado durante a criação do cluster

Você pode apontar seu cluster para um banco de dados Azure SQL criado anteriormente a qualquer momento. Para criação de cluster através do portal, a opção é especificada nas **configurações de Armazenamento > Metastore**.

![Armazenamento de metadados Hive do HDInsight Portal do Azure](./media/hdinsight-use-external-metadata-stores/azure-portal-cluster-storage-metastore.png)

## <a name="hive-metastore-guidelines"></a>Diretrizes do metaloja da colmeia

* Use um metastore personalizado sempre que possível, para ajudar a separar recursos de computação (seu cluster em execução) e metadados (armazenados no metastore).

* Inicie com uma camada S2, que fornece 50 DTU e 250 GB de armazenamento. Se você vir um afunilamento, poderá expandir o banco de dados.

* Se você pretende que vários clusters HDInsight acessem dados separados, use um banco de dados separado para o metastore em cada cluster. Se você compartilhar um metastore entre vários clusters do HDInsight, isso significa que os clusters usam os mesmos metadados e arquivos de dados do usuário subjacentes.

* Faça backup de seu metastore personalizado periodicamente. O Banco de Dados SQL do Azure gera backups automaticamente, mas o prazo de retenção de backup varia. Para obter mais informações, consulte [Saiba mais sobre os backups automáticos do Banco de Dados SQL](../sql-database/sql-database-automated-backups.md).

* Localize seu cluster metastore e HDInsight na mesma região. Essa configuração fornecerá o mais alto desempenho e as menores taxas de saída de rede.

* Monitore seu metastore para obter desempenho e disponibilidade usando as ferramentas de monitoramento de banco de dados Do Azure SQL ou logs do Monitor Do Azure.

* Quando uma nova versão mais alta do Azure HDInsight é criada contra um banco de dados de metastore personalizado existente, o sistema atualiza o esquema do metastore. A atualização é irreversível sem restaurar o banco de dados do backup.

* Se você compartilhar um metastore em vários clusters, verifique se todos os clusters são da mesma versão do HDInsight. Diferentes versões do Hive usam diferentes esquemas de banco de dados metastore. Por exemplo, você não pode compartilhar um metastore em clusters com versões Hive 2.1 e Hive 3.1.

* No HDInsight 4.0, a Spark and Hive usa catálogos independentes para acessar as tabelas SparkSQL ou Hive. Uma tabela criada pela Spark vive no catálogo da Spark. Uma tabela criada por Hive vive no catálogo da Colmeia. Esse comportamento é diferente do HDInsight 3.6, onde hive e spark compartilhavam catálogo comum. A integração de colmeias e faíscas no HDInsight 4.0 conta com o Conector do Armazém Colmeia (HWC). HWC funciona como uma ponte entre Spark e Hive. [Saiba mais sobre o conector do armazém colmeia](../hdinsight/interactive-query/apache-hive-warehouse-connector.md).

## <a name="apache-oozie-metastore"></a>Metaloja Apache Oozie

Apache Oozie é um sistema de coordenação do fluxo de trabalho que gerencia trabalhos do Hadoop. O Oozie dá suporte a trabalhos do Hadoop para Apache MapReduce, Pig, Hive e outros.  Oozie usa uma metaloja para armazenar detalhes sobre fluxos de trabalho. Para aumentar o desempenho ao usar o Oozie, você pode usar o Banco de Dados SQL do Azure como um metastore personalizado. O metastore fornece acesso aos dados de trabalho do Oozie depois de excluir seu cluster.

Para obter instruções sobre como criar um metastore Oozie com o Banco de Dados SQL do Azure, confira [Usar o Apache Oozie para fluxos de trabalho](hdinsight-use-oozie-linux-mac.md).

## <a name="custom-ambari-db"></a>BD Ambari personalizado

Para usar seu próprio banco de dados externo com o Apache Ambari no HDInsight, consulte [o banco de dados Personal Apache Ambari](hdinsight-custom-ambari-db.md).

## <a name="next-steps"></a>Próximas etapas

* [Configure os clusters no HDInsight com o Apache Hadoop, o Apache Spark, o Apache Kafka e muito mais](./hdinsight-hadoop-provision-linux-clusters.md)
