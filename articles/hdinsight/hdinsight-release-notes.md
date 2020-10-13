---
title: Notas da versão mais recente do Microsoft Azure HDInsight
description: Notas da versão mais recente do Microsoft Azure HDInsight. Obtenha detalhes e dicas de desenvolvimento para Hadoop, Spark, Microsoft R Server, Hive e muito mais.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: 616e3e6c37faa3c085b8531173b557973e09fbf8
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91974559"
---
# <a name="azure-hdinsight-release-notes"></a>Notas de versão do Azure HDInsight

Este artigo fornece informações sobre as atualizações de versão **mais recentes** do Azure HDInsight. Para obter informações sobre versões anteriores, consulte o [Arquivo morto de notas de versão do HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Resumo

O Microsoft Azure HDInsight é um dos serviços mais populares entre os clientes empresariais para análise de software livre no Azure.

## <a name="release-date-10082020"></a>Data de lançamento: 10/08/2020

Esta versão se aplica tanto ao HDInsight 3,6 quanto ao HDInsight 4,0. A versão do HDInsight é disponibilizada para todas as regiões durante vários dias. A data de lançamento mostrada aqui indica a data de lançamento da primeira região. Se você não vir as alterações abaixo, aguarde até que a liberação seja ativada em sua região em vários dias.

## <a name="new-features"></a>Novos recursos
### <a name="hdinsight-private-clusters-with-no-public-ip-and-private-link-preview"></a>Clusters privados do HDInsight sem IP público e link privado (visualização)
O HDInsight agora dá suporte à criação de clusters sem IP público e acesso de link privado aos clusters na versão prévia. Os clientes podem usar as novas configurações avançadas de rede para criar um cluster totalmente isolado sem IP público e usar seus próprios pontos de extremidade privados para acessar o cluster. 

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Migrar para Conjuntos de Dimensionamento de Máquinas Virtuais do Azure
O HDInsight atualmente usa máquinas virtuais do Azure para provisionar o cluster. A partir desta versão, o serviço migrará gradualmente para os [conjuntos de dimensionamento de máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview). Todo o processo pode levar meses. Depois que suas regiões e assinaturas forem migradas, os clusters HDInsight recém-criados serão executados em conjuntos de dimensionamento de máquinas virtuais sem ações do cliente. Nenhuma alteração significativa é esperada.

## <a name="deprecation"></a>Reprovação
#### <a name="deprecation-of-hdinsight-36-ml-services-cluster"></a>Substituição do cluster do HDInsight 3,6 ML Services
O tipo de cluster de serviços do HDInsight 3,6 ML será o fim do suporte até Dec 31 2020. Os clientes não criarão novos clusters de serviços de 3,6 ML depois disso. Os clusters existentes serão executados como estão, sem o suporte da Microsoft. Verifique a expiração do suporte para versões e tipos de cluster do HDInsight [aqui](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#available-versions).

## <a name="behavior-changes"></a>Alterações de comportamento
Nenhuma alteração de comportamento para esta versão.

## <a name="upcoming-changes"></a>Alterações futuras
As alterações a seguir ocorrerão em versões futuras.

### <a name="ability-to-select-different-zookeeper-virtual-machine-sizes-for-spark-hadoop-and-ml-services"></a>Capacidade de selecionar diferentes tamanhos de máquina virtual Zookeeper para serviços Spark, Hadoop e ML
Atualmente, o HDInsight não dá suporte à personalização do tamanho do nó Zookeeper para os tipos de cluster Spark, Hadoop e ML Services. O padrão é A2_v2 tamanhos de máquina virtual/a2, que são fornecidos gratuitamente. Na próxima versão, você pode selecionar um tamanho de máquina virtual Zookeeper que seja mais apropriado para seu cenário. Os nós Zookeeper com tamanho de máquina virtual diferente de A2_v2/a2 serão cobrados. As máquinas virtuais A2_v2 e a2 ainda são fornecidas gratuitamente.

## <a name="bug-fixes"></a>Correções de bug
O HDInsight continua a fazer aprimoramentos de desempenho e confiabilidade do cluster. 

## <a name="component-version-change"></a>Alteração na versão do componente
Nenhuma alteração de versão de componente para esta versão. Você pode encontrar as versões de componente atuais para HDInsight 4,0 e HDInsight 3,6 neste [documento](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions).