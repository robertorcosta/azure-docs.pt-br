---
title: Notas da versão mais recente do Microsoft Azure HDInsight
description: Notas da versão mais recente do Microsoft Azure HDInsight. Obtenha detalhes e dicas de desenvolvimento para Hadoop, Spark, Microsoft R Server, Hive e muito mais.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/12/2020
ms.openlocfilehash: 1ae4e2a1e0d67a0a09c19b517245ffc6d92d17df
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2020
ms.locfileid: "94629913"
---
# <a name="azure-hdinsight-release-notes"></a>Notas de versão do Azure HDInsight

Este artigo fornece informações sobre as atualizações de versão **mais recentes** do Azure HDInsight. Para obter informações sobre versões anteriores, consulte o [Arquivo morto de notas de versão do HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Resumo

O Microsoft Azure HDInsight é um dos serviços mais populares entre os clientes empresariais para análise de software livre no Azure.

Se você quiser assinar as notas de versão, Assista a versões neste [repositório GitHub](https://github.com/hdinsight/release-notes/releases).

## <a name="release-date-11092020"></a>Data de lançamento: 11/09/2020

Esta versão se aplica tanto ao HDInsight 3,6 quanto ao HDInsight 4,0. A versão do HDInsight é disponibilizada para todas as regiões durante vários dias. A data de lançamento mostrada aqui indica a data de lançamento da primeira região. Se você não vir as alterações abaixo, aguarde até que a liberação seja ativada em sua região em vários dias.

## <a name="new-features"></a>Novos recursos
### <a name="hdinsight-identity-broker-hib-is-now-ga"></a>O agente de identidade do HDInsight (HIB) agora está disponível
O HIB (agente de identidade do HDInsight) que habilita a autenticação OAuth para clusters ESP agora está disponível com esta versão. Os clusters HIB criados após esta versão terão os recursos de HIB mais recentes:
- Alta disponibilidade (HA)
- Suporte para MFA (autenticação multifator)
- Usuários federados entram sem sincronização de hash de senha para AAD-DS para obter mais informações, consulte a [documentação do Hib](https://docs.microsoft.com/azure/hdinsight/domain-joined/identity-broker).

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Migrar para Conjuntos de Dimensionamento de Máquinas Virtuais do Azure
O HDInsight atualmente usa máquinas virtuais do Azure para provisionar o cluster. A partir desta versão, o serviço migrará gradualmente para os [conjuntos de dimensionamento de máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview). Todo o processo pode levar meses. Depois que suas regiões e assinaturas forem migradas, os clusters HDInsight recém-criados serão executados em conjuntos de dimensionamento de máquinas virtuais sem ações do cliente. Nenhuma alteração significativa é esperada.

## <a name="deprecation"></a>Reprovação
### <a name="deprecation-of-hdinsight-36-ml-services-cluster"></a>Substituição do cluster do HDInsight 3,6 ML Services
O tipo de cluster de serviços do HDInsight 3,6 ML será o fim do suporte até dezembro de 31 2020. Os clientes não criarão novos clusters de serviços de 3,6 ML após dezembro de 31 2020. Os clusters existentes serão executados como estão, sem o suporte da Microsoft. Verifique a expiração do suporte para versões e tipos de cluster do HDInsight [aqui](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#available-versions).

### <a name="disabled-vm-sizes"></a>Tamanhos de VM desabilitados
A partir de novembro de 16 2020, o HDInsight bloqueará novos clientes que criam clusters usando standand_A8, standand_A9, standand_A10 e standand_A11 tamanhos de VM. Os clientes existentes que usaram esses tamanhos de VM nos últimos três meses não serão afetados. A partir do formulário de janeiro de 9 2021, o HDInsight bloqueará todos os clientes que criam clusters usando standand_A8, standand_A9, standand_A10 e standand_A11 tamanhos de VM. Os clusters existentes serão executados como estão. Considere migrar para o HDInsight 4,0 para evitar a interrupção potencial do sistema/suporte.

## <a name="behavior-changes"></a>Alterações de comportamento
Nenhuma alteração de comportamento para esta versão.

## <a name="upcoming-changes"></a>Alterações futuras
As alterações a seguir ocorrerão em versões futuras.

### <a name="ability-to-select-different-zookeeper-virtual-machine-sizes-for-spark-hadoop-and-ml-services"></a>Capacidade de selecionar diferentes tamanhos de máquina virtual Zookeeper para serviços Spark, Hadoop e ML
Atualmente, o HDInsight não dá suporte à personalização do tamanho do nó Zookeeper para os tipos de cluster Spark, Hadoop e ML Services. O padrão é A2_v2 tamanhos de máquina virtual/a2, que são fornecidos gratuitamente. Na próxima versão, você pode selecionar um tamanho de máquina virtual Zookeeper que seja mais apropriado para seu cenário. Os nós Zookeeper com tamanho de máquina virtual diferente de A2_v2/a2 serão cobrados. As máquinas virtuais A2_v2 e a2 ainda são fornecidas gratuitamente.

### <a name="default-cluster-version-will-be-changed-to-40"></a>A versão de cluster padrão será alterada para 4,0
A partir de fevereiro de 2021, a versão padrão do cluster HDInsight será alterada de 3,6 para 4,0. Para obter mais informações sobre as versões disponíveis, consulte [versões disponíveis](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#available-versions). Saiba mais sobre o que há de novo no [HDInsight 4,0](https://docs.microsoft.com/azure/hdinsight/hdinsight-version-release)

### <a name="hdinsight-36-end-of-support-on-june-30-2021"></a>Fim do suporte do HDInsight 3,6 em junho de 30 2021
O HDInsight 3,6 será o fim do suporte. A partir do formulário de junho de 30 2021, os clientes não podem criar novos clusters HDInsight 3,6. Os clusters existentes serão executados como estão, sem o suporte da Microsoft. Considere migrar para o HDInsight 4,0 para evitar a interrupção potencial do sistema/suporte.

## <a name="bug-fixes"></a>Correções de bug
O HDInsight continua a fazer aprimoramentos de desempenho e confiabilidade do cluster. 
### <a name="fix-issue-for-restarting-vms-in-cluster"></a>Correção do problema para reiniciar as VMs no cluster
O problema para reiniciar as VMs no cluster foi corrigido, você pode usar o [PowerShell ou a API REST para reinicializar os nós no cluster](https://docs.microsoft.com/azure/hdinsight/cluster-reboot-vm) novamente.

## <a name="component-version-change"></a>Alteração na versão do componente
Nenhuma alteração de versão de componente para esta versão. Você pode encontrar as versões de componente atuais para HDInsight 4,0 e HDInsight 3,6 neste [documento](./hdinsight-component-versioning.md).
