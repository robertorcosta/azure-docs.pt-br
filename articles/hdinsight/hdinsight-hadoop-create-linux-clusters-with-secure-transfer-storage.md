---
title: Apache Hadoop & armazenamento de transferência segura-Azure HDInsight
description: Saiba como criar clusters HDInsight com contas de armazenamento do Azure com transferência segura habilitada.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/04/2019
ms.openlocfilehash: 09a6b158c4390f881754c90d52a476f0bc249a5a
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2019
ms.locfileid: "74947632"
---
# <a name="create-apache-hadoop-cluster-with-secure-transfer-storage-accounts-in-azure-hdinsight"></a>Criar um cluster Apache Hadoop com contas de armazenamento de transferência segura no Azure HDInsight

O recurso [Transferência segura exigida](../storage/common/storage-require-secure-transfer.md) aprimora a segurança de sua conta de Armazenamento do Azure aplicando todas as solicitações à sua conta por meio de uma conexão segura. Esse recurso e o esquema wasbs só têm suporte da versão 3.6 ou mais recente do cluster HDInsight.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este artigo, você deve ter:

* Assinatura do Azure: para criar uma conta de avaliação gratuita de um mês, navegue até [Azure.Microsoft.com/Free](https://azure.microsoft.com/free).
* Uma conta de armazenamento do Azure com transferência segura habilitada. Para obter as instruções, consulte [Criar uma conta de armazenamento](../storage/common/storage-quickstart-create-account.md) e [Exigir transferência segura](../storage/common/storage-require-secure-transfer.md). Habilitar a transferência de armazenamento seguro depois de criar um cluster requer etapas adicionais não abordadas neste artigo.
* Um contêiner de BLOBs na conta de armazenamento.

## <a name="create-cluster"></a>Criar cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Nesta seção, você criará um cluster Hadoop no HDInsight usando um [modelo do Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md). O modelo está localizado no [GitHub](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-with-existing-default-storage-account/). A experiência do modelo do Resource Manager não é necessária para seguir este artigo. Para outros métodos de criação de cluster e noções básicas sobre as propriedades usadas neste artigo, consulte [Criar clusters HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

1. Clique na imagem a seguir para entrar no Azure e abra o modelo do Resource Manager no Portal do Azure.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-existing-default-storage-account%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-hadoop-create-linux-clusters-with-secure-transfer-storage/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

2. Siga as instruções para criar o cluster com as seguintes especificações:

    * Especifique o HDInsight versão 3.6. É necessário ter a versão 3.6 ou mais recente.
    * Especifique uma conta de armazenamento com transferência segura habilitada.
    * Use um nome curto para a conta de armazenamento.
    * A conta de armazenamento e o contêiner de blob devem ser criados com antecedência.

      Para obter as instruções, confira [Criar cluster](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).

Se você usar ação de script para fornecer seus próprios arquivos de configuração, use wasbs nas seguintes configurações:

* fs.defaultFS (site principal)
* spark.eventLog.dir
* spark.history.fs.logDirectory

## <a name="add-additional-storage-accounts"></a>Adicionar outras contas de armazenamento

Há várias opções para adicionar outras contas de armazenamento com transferência segura habilitada:

* Modifique o modelo do Azure Resource Manager na última seção.
* Crie um cluster usando o [Portal do Azure](https://portal.azure.com) e especifique a conta de armazenamento vinculada.
* Use ações de script para adicionar outras contas de armazenamento com transferência segura habilitada a um cluster existente do HDInsight. Para saber mais, confira [Adicionar outras contas de armazenamento ao HDInsight](hdinsight-hadoop-add-storage.md).

## <a name="next-steps"></a>Próximos passos

Neste artigo, você aprendeu a criar um cluster HDInsight e a habilitar a transferência segura para as contas de armazenamento.

Para saber mais sobre como analisar dados com o HDInsight, consulte os seguintes artigos:

* Para saber mais sobre como usar [Apache Hive](https://hive.apache.org/) com o hdinsight, incluindo como executar consultas de hive do Visual Studio, consulte [usar o Apache Hive com o hdinsight](hadoop/hdinsight-use-hive.md).
* Para saber mais sobre o [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html), uma maneira de escrever programas que processam dados no Hadoop, consulte [usar o Apache Hadoop MapReduce com o HDInsight](hadoop/hdinsight-use-mapreduce.md).
* Para saber mais sobre como usar as Ferramentas do HDInsight para Visual Studio, consulte [Introdução ao uso das ferramentas do Apache Hadoop do Visual Studio para o HDInsight](hadoop/apache-hadoop-visual-studio-tools-get-started.md).

Para saber mais sobre como o HDInsight armazena dados, ou como inserir os dados no HDInsight, consulte os artigos a seguir:

* Para saber mais sobre como o HDInsight usa o Armazenamento do Azure, veja [Usar Armazenamento do Azure com o HDInsight](hdinsight-hadoop-use-blob-storage.md).
* Para obter informações sobre como carregar arquivos no HDInsight, consulte [Carregar dados no HDInsight](hdinsight-upload-data.md).

Para saber mais sobre como criar ou gerenciar um cluster do HDInsight, consulte os artigos a seguir:

* Para saber mais sobre como gerenciar o cluster HDInsight baseado em Linux, confira [Manage HDInsight clusters using Apache Ambari](hdinsight-hadoop-manage-ambari.md) (Gerenciar clusters HDInsight usando o Apache Ambari).
* Para saber mais sobre as opções que você pode selecionar ao criar um cluster HDInsight, confira [Como criar o HDInsight no Linux usando opções personalizadas](hdinsight-hadoop-provision-linux-clusters.md).
* Se você estiver familiarizado com o Linux e Apache Hadoop, mas quiser saber sobre as especificações sobre o Hadoop no HDInsight, consulte [trabalhando com o HDInsight no Linux](hdinsight-hadoop-linux-information.md). Este artigo oferece informações como:

  * URLs para serviços hospedados no cluster, como [Apache Ambari](https://ambari.apache.org/) e [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat)
  * O local dos arquivos do [Apache Hadoop](https://hadoop.apache.org/) e exemplos no sistema de arquivos local
  * O uso do Armazenamento do Azure (WASB) em vez de [Apache Hadoop HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) como armazenamento de dados padrão
