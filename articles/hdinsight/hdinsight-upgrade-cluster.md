---
title: Atualizar o cluster HDInsight para uma versão mais recente - Azure
description: Aprenda as diretrizes para atualizar seu cluster do Azure HDInsight para uma versão mais recente.
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 1a1d4a71786ebb1e68f59084086b3256a1c1ea40
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2019
ms.locfileid: "74951149"
---
# <a name="upgrade-hdinsight-cluster-to-a-newer-version"></a>Atualizar o cluster HDInsight para uma versão mais recente

Para aproveitar os novos recursos do HDInsight, é recomendável que os clusters HDInsight sejam atualizados para a versão mais recente. Siga as diretrizes apresentadas abaixo para atualizar as versões do cluster HDInsight.

> [!NOTE]  
> Para obter mais informações sobre versões com suporte do HDInsight, consulte [Versões de componente do HDInsight](hdinsight-component-versioning.md#supported-hdinsight-versions).

## <a name="upgrade-tasks"></a>Tarefas de atualização

O fluxo de trabalho para atualizar o cluster HDInsight serão apresentadas a seguir.
![diagrama de fluxo de trabalho de atualização do HDInsight](./media/hdinsight-upgrade-cluster/upgrade-workflow-diagram.png)

1. Leia cada seção deste documento para entender as alterações que podem ser necessárias ao atualizar o cluster HDInsight.
2. Crie um cluster como um ambiente de teste/garantia de qualidade. Para saber mais sobre como criar um cluster, consulte [Saiba como criar clusters HDInsight baseados em Linux](hdinsight-hadoop-provision-linux-clusters.md)
3. Copie trabalhos, fontes de dados e coletores existentes para o novo ambiente.
4. Execute testes de validação para garantir que os trabalhos funcionem conforme o esperado no novo cluster.

Depois de verificar se tudo está funcionando conforme o esperado, agende o tempo de inatividade para a migração. Durante esse tempo de inatividade, execute as tarefas a seguir:

1. Faça backup de dados transitórios armazenados localmente em nós do cluster. Por exemplo, se você tiver dados armazenados diretamente em um nó principal.
1. [Exclua o cluster existente](./hdinsight-delete-cluster.md).
1. Crie um cluster na mesma sub-rede VNET com a versão do HDI mais recente (ou com suporte) que usa o mesmo armazenamento de dados padrão utilizado pelo cluster anterior. Isso permite que o novo cluster continue trabalhando nos dados de produção existentes.
1. Importe o backup de todos os dados transitórios.
1. Inicie os trabalhos/continue processando usando o novo cluster.

## <a name="next-steps"></a>Próximas etapas

* [Saiba como criar clusters HDInsight baseados em Linux](hdinsight-hadoop-provision-linux-clusters.md)
* [Conectar-se ao HDInsight usando o SSH](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Gerenciar um cluster baseado em Linux usando o Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Notas de versão do HDInsight](./hdinsight-version-release.md)
