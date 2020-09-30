---
title: Notas da versão mais recente do Microsoft Azure HDInsight
description: Notas da versão mais recente do Microsoft Azure HDInsight. Obtenha detalhes e dicas de desenvolvimento para Hadoop, Spark, Microsoft R Server, Hive e muito mais.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 09/27/2020
ms.openlocfilehash: feb186fbe216305039fcc0a23a10419c44fd0483
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91535603"
---
# <a name="azure-hdinsight-release-notes"></a>Notas de versão do Azure HDInsight

Este artigo fornece informações sobre as atualizações de versão **mais recentes** do Azure HDInsight. Para obter informações sobre versões anteriores, consulte o [Arquivo morto de notas de versão do HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Resumo

O Microsoft Azure HDInsight é um dos serviços mais populares entre os clientes empresariais para análise de software livre no Azure.

## <a name="release-date-09282020"></a>Data de lançamento: 09/28/2020

Esta versão se aplica tanto ao HDInsight 3,6 quanto ao HDInsight 4,0. A versão do HDInsight é disponibilizada para todas as regiões durante vários dias. A data de lançamento mostrada aqui indica a data de lançamento da primeira região. Se você não vir as alterações abaixo, aguarde até que a liberação seja ativada em sua região em vários dias.

## <a name="new-features"></a>Novos recursos
### <a name="autoscale-for-interactive-query-with-hdinsight-40-is-now-generally-available"></a>O dimensionamento automático para consulta interativa com o HDInsight 4,0 já está disponível para o público geral
O dimensionamento automático para o tipo de cluster de consulta interativa agora é geral disponível (GA) para o HDInsight 4,0. Todos os clusters de consulta interativa 4,0 criados após 27 de agosto de 2020 terão suporte de GA para dimensionamento automático.

### <a name="hbase-cluster-supports-premium-adls-gen2"></a>O cluster HBase dá suporte a ADLS Gen2 Premium
O HDInsight agora dá suporte ao ADLS Gen2 Premium como conta de armazenamento principal para clusters HDInsight HBase 3,6 e 4,0. Junto com as [gravações aceleradas](./hbase/apache-hbase-accelerated-writes.md), você pode obter um melhor desempenho para seus clusters HBase.

### <a name="kafka-partition-distribution-on-azure-fault-domains"></a>Distribuição de partição do Kafka em domínios de falha do Azure
Um domínio de falha é um agrupamento lógico de hardware subjacente em um data center do Azure. Cada domínio de falha tem um comutador de rede e uma fonte de alimentação em comum. Antes do HDInsight Kafka, é possível armazenar todas as réplicas de partição no mesmo domínio de falha. A partir desta versão, o HDInsight agora dá suporte à distribuição automática de partições Kafka com base em domínios de falha do Azure. 

### <a name="encryption-in-transit"></a>Criptografia em trânsito
Os clientes podem habilitar a criptografia em trânsito entre os nós de cluster usando a criptografia IPSec com chaves gerenciadas por plataforma. Essa opção pode ser habilitada no momento da criação do cluster. Veja mais detalhes sobre [como habilitar a criptografia em trânsito](./domain-joined/encryption-in-transit.md).

### <a name="encryption-at-host"></a>Criptografia no host
Quando você habilita a criptografia no host, os dados armazenados no host da VM são criptografados em repouso e os fluxos são criptografados para o serviço de armazenamento. A partir desta versão, você pode **habilitar a criptografia no disco de dados temporário** ao criar o cluster. A criptografia no host só tem suporte em [determinados SKUs de VM em regiões limitadas](https://docs.microsoft.com/azure/virtual-machines/linux/disks-enable-host-based-encryption-portal). O HDInsight dá suporte à [seguinte configuração de nó e SKUs](./hdinsight-supported-node-configuration.md). Veja mais detalhes sobre [como habilitar a criptografia no host](https://docs.microsoft.com/azure/hdinsight/disk-encryption#encryption-at-host-using-platform-managed-keys).

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Migrar para Conjuntos de Dimensionamento de Máquinas Virtuais do Azure
O HDInsight atualmente usa máquinas virtuais do Azure para provisionar o cluster. A partir desta versão, o serviço migrará gradualmente para os [conjuntos de dimensionamento de máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview). Todo o processo pode levar meses. Depois que suas regiões e assinaturas forem migradas, os clusters HDInsight recém-criados serão executados em conjuntos de dimensionamento de máquinas virtuais sem ações do cliente. Nenhuma alteração significativa é esperada.

## <a name="deprecation"></a>Reprovação
Não há preterição para esta versão.

## <a name="behavior-changes"></a>Alterações de comportamento
Nenhuma alteração de comportamento para esta versão.

## <a name="upcoming-changes"></a>Alterações futuras
As alterações a seguir ocorrerão em versões futuras.

### <a name="ability-to-select-different-zookeeper-sku-for-spark-hadoop-and-ml-services"></a>Capacidade de selecionar um SKU Zookeeper diferente para os serviços Spark, Hadoop e ML
Atualmente, o HDInsight não dá suporte à alteração de SKU Zookeeper para tipos de cluster Spark, Hadoop e ML Services. Ele usa A2_v2 SKU/a2 para nós Zookeeper e os clientes não são cobrados por eles. Na próxima versão, os clientes podem alterar o Zookeeper SKU para os serviços Spark, Hadoop e ML, conforme necessário. Nós Zookeeper com SKU diferente de A2_v2/a2 serão cobrados. O SKU padrão ainda será A2_V2/a2 e sem encargos.

## <a name="bug-fixes"></a>Correções de bug
O HDInsight continua a fazer aprimoramentos de desempenho e confiabilidade do cluster. 

## <a name="component-version-change"></a>Alteração na versão do componente
Nenhuma alteração de versão de componente para esta versão. Você pode encontrar as versões de componente atuais para HDInsight 4,0 e HDInsight 3,6 neste [documento](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions).