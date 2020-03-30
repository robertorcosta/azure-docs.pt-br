---
title: Usar Grafana no Microsoft Azure HDInsight
description: Saiba como acessar o painel grafana com clusters Apache Hadoop no Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/27/2019
ms.openlocfilehash: cd515bfd1dc57e78a041ed96686e1ba692bf6d3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79082856"
---
# <a name="access-grafana-in-azure-hdinsight"></a>Acessar Grafana no Microsoft Azure HDInsight

[Grafana](https://grafana.com/) é um popular construtor de gráficos e painéis de código aberto. O Grafana é rico; não apenas permite que os usuários criem painéis personalizáveis e compartilha´veis, mas também oferece painéis de amostra/com script, integração LDAP, fontes de dados múltiplas e muito mais.

Atualmente, no Azure HDInsight, o Grafana é suportado com os tipos de cluster Spark, HBase, Kafka e Interactive Query. Ele não é suportado para clusters com o Enterprise Security Pack ativado.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-an-apache-hadoop-cluster"></a>Criar um cluster do Apache Hadoop

Consulte [Criar clusters Apache Hadoop usando o portal Azure](../hdinsight-hadoop-create-linux-clusters-portal.md). Para **o tipo Cluster,** selecione **Spark,** **Kafka,** **HBase**ou **Consulta Interativa.**

## <a name="access-the-grafana-dashboard"></a>Acessar o painel do Grafana

1. A partir de um `https://CLUSTERNAME.azurehdinsight.net/grafana/` navegador da Web, navegue até onde CLUSTERNAME é o nome do seu cluster.

1. Insira as credenciais de usuário do cluster Hadoop.

1. A aparência do painel do Grafana é semelhante a este exemplo:

    ![Painel web HDInsight Grafana](./media/hdinsight-grafana/hdinsight-grafana-dashboard.png "Painel HDInsight Grafana")

## <a name="clean-up-resources"></a>Limpar recursos

Se não for continuar a usar este aplicativo, exclua o cluster que criou seguindo estas etapas:

1. Faça login no [portal Azure](https://portal.azure.com/).

1. Na caixa **Pesquisar** na parte superior, digite **HDInsight**.

1. Selecione **Clusters do HDInsight** em **Serviços**.

1. Na lista de clusters HDInsight que aparece, selecione o **...** ao lado do cluster que você criou.

1. Selecione **Excluir**. Selecione **Sim**.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como analisar dados com o HDInsight, consulte os seguintes artigos:

* [Use a Colmeia Apache com HDInsight](../hadoop/hdinsight-use-hive.md).

* [Use MapReduce com HDInsight](../hadoop/hdinsight-use-mapreduce.md).

* [Comece a usar as ferramentas do Visual Studio Hadoop para HDInsight](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).
