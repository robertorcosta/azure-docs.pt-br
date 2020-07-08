---
title: Notas da versão mais recente do Microsoft Azure HDInsight
description: Notas da versão mais recente do Microsoft Azure HDInsight. Obtenha detalhes e dicas de desenvolvimento para Hadoop, Spark, Microsoft R Server, Hive e muito mais.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/11/2020
ms.openlocfilehash: a4faab9ac6d5e1c39c1120e09dae792b95892d60
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85564406"
---
# <a name="release-notes"></a>Notas de versão

Este artigo fornece informações sobre as atualizações de versão **mais recentes** do Azure HDInsight. Para obter informações sobre versões anteriores, consulte o [Arquivo morto de notas de versão do HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Resumo

O Microsoft Azure HDInsight é um dos serviços mais populares entre os clientes empresariais para análise de software livre no Azure.

## <a name="release-date-06112020"></a>Data de lançamento: 06/11/2020

Esta versão se aplica tanto ao HDInsight 3.6 quanto ao 4.0. A versão do HDInsight é disponibilizada para todas as regiões durante vários dias. A data de lançamento mostrada aqui indica a data de lançamento da primeira região. Se você não vir as alterações abaixo, aguarde até que a liberação seja ativada em sua região em vários dias.

## <a name="new-features"></a>Novos recursos
### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Migrar para Conjuntos de Dimensionamento de Máquinas Virtuais do Azure
O HDInsight usa máquinas virtuais do Azure para provisionar o cluster agora. Nesta versão, os clusters HDInsight novos criados começam a usar o conjunto de dimensionamento de máquinas virtuais do Azure. A alteração está sendo distribuída gradualmente. Você não deve esperar nenhuma alteração significativa. Veja mais sobre os [conjuntos de dimensionamento de máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview).
 
### <a name="reboot-vms-in-hdinsight-cluster"></a>Reinicializar VMs no cluster HDInsight
Nesta versão, damos suporte à reinicialização de VMs no cluster HDInsight para reinicializar nós sem resposta. Atualmente, você só pode fazer isso por meio da API, o PowerShell e o suporte à CLI estão a caminho. Para obter mais informações sobre a API, consulte [este documento](https://github.com/Azure/azure-rest-api-specs/codeowners/master/specification/hdinsight/resource-manager/Microsoft.HDInsight/stable/2018-06-01-preview/virtualMachines.json).
 
## <a name="deprecation"></a>Reprovação
### <a name="deprecation-of-spark-21-and-22-in-hdinsight-36-spark-cluster"></a>Substituição do Spark 2.1 e 2.2 no cluster do Spark do HDInsight 3.6
A partir de julho de 1 2020, os clientes não podem criar novos clusters Spark com Spark 2,1 e 2,2 no HDInsight 3,6. Os clusters existentes serão executados como estão, sem o suporte da Microsoft. Considere a possibilidade de migrar para o Spark 2.3 no HDInsight 3.6 até 30 de junho de 2020 para evitar a interrupção potencial do sistema/suporte.
 
### <a name="deprecation-of-spark-23-in-hdinsight-40-spark-cluster"></a>Substituição do Spark 2.3 no cluster do Spark do HDInsight 4.0
A partir de julho de 1 2020, os clientes não podem criar novos clusters Spark com o Spark 2,3 no HDInsight 4,0. Os clusters existentes serão executados como estão, sem o suporte da Microsoft. Considere a possibilidade de migrar para o Spark 2.4 no HDInsight 4.0 até 30 de junho de 2020 para evitar a interrupção potencial do sistema/suporte.
 
### <a name="deprecation-of-kafka-11-in-hdinsight-40-kafka-cluster"></a>Substituição do Kafka 1.1 no cluster Kafka do HDInsight 4.0
A partir de 1º de julho de 2020, os clientes não poderão criar clusters Kafka com Kafka 1.1 no HDInsight 4.0. Os clusters existentes serão executados como estão, sem o suporte da Microsoft. Considere a possibilidade de migrar para o Kafka 2.1 no HDInsight 4.0 até 30 de junho de 2020 para evitar a interrupção potencial do sistema/suporte.
 
## <a name="behavior-changes"></a>Alterações de comportamento
### <a name="esp-spark-cluster-head-node-size-change"></a>Alteração de tamanho do nó de cabeçalho do cluster do Spark do ESP 
O tamanho mínimo permitido do nó de cabeçalho para o cluster do Spark do ESP é alterado para Standard_D13_V2. As VMs com poucos núcleos e memória como nó de cabeçalho podem causar problemas de cluster ESP devido à capacidade relativamente baixa de CPU e memória. A partir do lançamento, use SKUs maiores que Standard_D13_V2 e Standard_E16_V3 como nó de cabeçalho para clusters do Spark do ESP.
 
### <a name="a-minimum-4-core-vm-is-required-for-head-node"></a>Uma VM de quatro núcleos mínima é necessária para o nó de cabeçalho 
Uma VM de quatro núcleos mínima é necessária para o nó de cabeçalho para garantir a alta disponibilidade e a confiabilidade dos clusters do HDInsight. A partir de 6 de abril de 2020, os clientes podem escolher apenas a VM de quatro núcleos ou mais como nó principal para os novos clusters HDInsight. Os clusters existentes continuarão a ser executados conforme o esperado. 
 
### <a name="cluster-worker-node-provisioning-change"></a>Alteração de provisionamento de nó de trabalho do cluster
Quando 80% dos nós de trabalho estiverem prontos, o cluster entrará no estágio **operacional** . Nesse estágio, os clientes podem fazer todas as operações do plano de dados, como executar scripts e trabalhos. Mas os clientes não podem realizar nenhuma operação de plano de controle, como dimensionar/reduzir verticalmente. Somente a exclusão tem suporte.
 
Após o estágio **operacional** , o cluster aguardará outros 60 minutos para os 20% nós de trabalho restantes. No final desses 60 minutos, o cluster se moverá para o estágio **em execução** , mesmo que todos os nós de trabalho ainda não estejam disponíveis. Depois que um cluster entra no estágio **em execução** , você pode usá-lo como de costume. Ambas as operações do plano de controle, como expansão/redução, e operações de plano de dados como executar scripts e trabalhos são aceitas. Se alguns dos nós de trabalho solicitados não estiverem disponíveis, o cluster será marcado como êxito parcial. Você é cobrado pelos nós que foram implantados com êxito. 
 
### <a name="create-new-service-principal-through-hdinsight"></a>Criar nova entidade de serviço por meio do HDInsight
Anteriormente, com a criação do cluster, os clientes podem criar uma nova entidade de serviço para acessar a conta do ADLS Gen 1 conectada no portal do Azure. A partir de junho de 15 2020, os clientes não podem criar uma nova entidade de serviço no fluxo de trabalho de criação do HDInsight, somente a entidade de serviço existente tem suporte Consulte [criar entidade de serviço e certificados usando Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

### <a name="time-out-for-script-actions-with-cluster-creation"></a>Tempo limite para ações de script com a criação do cluster
O HDInsight dá suporte à execução de ações de script com a criação do cluster. A partir desta versão, todas as ações de script com a criação do cluster devem ser concluídas dentro de **60 minutos**ou expiram. As ações de script enviadas aos clusters em execução não são afetadas. Veja mais detalhes [aqui](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux#script-action-in-the-cluster-creation-process).
 
## <a name="upcoming-changes"></a>Alterações futuras
Não há alterações futuras que você precise prestar atenção.
 
## <a name="bug-fixes"></a>Correções de bug
O HDInsight continua a fazer aprimoramentos de desempenho e confiabilidade do cluster. 
 
## <a name="component-version-change"></a>Alteração na versão do componente
### <a name="hbase-20-to-216"></a>HBase 2.0 para 2.1.6
A versão do HBase é atualizada da versão 2,0 para 2.1.6.
 
### <a name="spark-240-to-244"></a>Spark 2.4.0 para 2.4.4
A versão do Spark é atualizada da versão 2.4.0 para 2.4.4.
 
### <a name="kafka-210-to-211"></a>Kafka 2.1.0 para 2.1.1
A versão do Kafka é atualizada da versão 2.1.0 para 2.1.1.
 
Você pode encontrar as versões de componente atuais para o HDInsight 4,0 Ad HDInsight 3,6 neste [documento](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions)

## <a name="known-issues"></a>Problemas conhecidos

### <a name="hive-warehouse-connector-issue"></a>Problema do conector de depósito do hive
Há um problema para o conector do depósito do hive nesta versão. A correção será incluída na próxima versão. Os clusters existentes criados antes desta versão não são afetados. Evite a remoção e a recriação do cluster, se possível. Abra o tíquete de suporte se precisar de mais ajuda sobre isso.
