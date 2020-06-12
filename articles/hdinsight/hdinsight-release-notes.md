---
title: Notas da versão mais recente do Microsoft Azure HDInsight
description: Notas da versão mais recente do Microsoft Azure HDInsight. Obtenha detalhes e dicas de desenvolvimento para Hadoop, Spark, Microsoft R Server, Hive e muito mais.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/12/2020
ms.openlocfilehash: b7489c49b7469feacfd5b982615419741d286998
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/26/2020
ms.locfileid: "83849698"
---
# <a name="release-notes"></a>Notas de versão

Este artigo fornece informações sobre as atualizações de versão **mais recentes** do Azure HDInsight. Para obter informações sobre versões anteriores, consulte o [Arquivo morto de notas de versão do HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Resumo

O Microsoft Azure HDInsight é um dos serviços mais populares entre os clientes empresariais para análise de software livre no Azure.

## <a name="release-date-01092020"></a>Data de lançamento: 09/01/2020

Esta versão se aplica tanto ao HDInsight 3.6 quanto ao 4.0. A versão do HDInsight é disponibilizada para todas as regiões durante vários dias. A data de lançamento mostrada aqui indica a data de lançamento da primeira região. Se você não vir as alterações abaixo, aguarde até que, após vários dias, a versão esteja ativa em sua região.

> [!IMPORTANT]  
> O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para saber mais, confira o [artigo sobre controle de versão do HDInsight](hdinsight-component-versioning.md).

## <a name="new-features"></a>Novos recursos
### <a name="tls-12-enforcement"></a>Imposição do TLS 1.2
Os protocolos TLS e SSL são protocolos criptográficos que fornecem segurança de comunicações em uma rede de computadores. Saiba mais sobre o [TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0). O HDInsight usa o TLS 1.2 em pontos de extremidade HTTPs públicos, mas o TLS 1.1 ainda é compatível com versões anteriores. 

Com esta versão, os clientes podem optar pelo TLS 1.2 apenas para todas as conexões por meio do ponto de extremidade do cluster público. Para dar suporte a isso, a nova propriedade **minSupportedTlsVersion** é introduzida e pode ser especificada durante a criação do cluster. Se a propriedade não estiver definida, o cluster ainda será compatível com TLS 1.0, 1.1 e 1.2, comportamento igual ao atual. Os clientes podem definir o valor dessa propriedade como "1.2", o que significa que o cluster só é compatível com TLS 1.2 e superior. Para obter mais informações, confira [Protocolo TLS](./transport-layer-security.md).

### <a name="bring-your-own-key-for-disk-encryption"></a>Bring Your Own Key para criptografia de disco
Todos os discos gerenciados no HDInsight são protegidos com o SSE (Criptografia do Serviço de Armazenamento) do Azure. Por padrão, os dados nesses discos são criptografados usando chaves gerenciadas pela Microsoft. Nesta versão e nas posteriores, você pode usar a criptografia de disco BYOK (Bring Your Own Key) e gerenciá-la usando o Azure Key Vault. A criptografia BYOK é uma configuração de uma etapa realizada durante a criação do cluster sem nenhum custo adicional. Basta registrar o HDInsight como uma identidade gerenciada com o Azure Key Vault e adicionar a chave de criptografia ao criar o cluster. Para obter mais informações, confira [Criptografia de disco de chave gerenciada pelo cliente](https://docs.microsoft.com/azure/hdinsight/disk-encryption).

## <a name="deprecation"></a>Reprovação
Nenhuma desativação para esta versão. Para se preparar para desativações futuras, confira [Alterações futuras](#upcoming-changes).

## <a name="behavior-changes"></a>Alterações de comportamento
Nenhuma alteração de comportamento para esta versão. Para se preparar para alterações futuras, confira [Alterações futuras](#upcoming-changes).

## <a name="upcoming-changes"></a>Alterações futuras
As alterações a seguir ocorrerão em versões futuras. 

### <a name="deprecation-of-spark-21-and-22-in-hdinsight-36-spark-cluster"></a>Substituição do Spark 2.1 e 2.2 no cluster do Spark do HDInsight 3.6
A partir de 1º de julho de 2020, os clientes não poderão criar clusters Spark com Spark 2.1 e 2.2 no HDInsight 3.6. Os clusters existentes serão executados como estão, sem o suporte da Microsoft. Considere a possibilidade de migrar para o Spark 2.3 no HDInsight 3.6 até 30 de junho de 2020 para evitar a interrupção potencial do sistema/suporte.

### <a name="deprecation-of-spark-23-in-hdinsight-40-spark-cluster"></a>Substituição do Spark 2.3 no cluster do Spark do HDInsight 4.0
A partir de 1º de julho de 2020, os clientes não poderão criar clusters Spark com o Spark 2.3 no HDInsight 4.0. Os clusters existentes serão executados como estão, sem o suporte da Microsoft. Considere a possibilidade de migrar para o Spark 2.4 no HDInsight 4.0 até 30 de junho de 2020 para evitar a interrupção potencial do sistema/suporte.

### <a name="deprecation-of-kafka-11-in-hdinsight-40-kafka-cluster"></a>Substituição do Kafka 1.1 no cluster Kafka do HDInsight 4.0
A partir de 1º de julho de 2020, os clientes não poderão criar clusters Kafka com Kafka 1.1 no HDInsight 4.0. Os clusters existentes serão executados como estão, sem o suporte da Microsoft. Considere a possibilidade de migrar para o Kafka 2.1 no HDInsight 4.0 até 30 de junho de 2020 para evitar a interrupção potencial do sistema/suporte.

### <a name="hbase-20-to-216"></a>HBase 2.0 para 2.1.6
Na próxima versão do HDInsight 4.0, a versão do HBase será atualizada da versão 2.0 para a 2.1.6

### <a name="spark-240-to-244"></a>Spark 2.4.0 para 2.4.4
Na próxima versão do HDInsight 4.0, a versão do Spark será atualizada da versão 2.4.0 para a 2.4.4

### <a name="kafka-210-to-211"></a>Kafka 2.1.0 para 2.1.1
Na próxima versão do HDInsight 4.0, a versão do Kafka será atualizada da versão 2.1.0 para a 2.1.1

### <a name="a-minimum-4-core-vm-is-required-for-head-node"></a>Uma VM de quatro núcleos mínima é necessária para o nó de cabeçalho 
Uma VM de quatro núcleos mínima é necessária para o nó de cabeçalho para garantir a alta disponibilidade e a confiabilidade dos clusters do HDInsight. A partir de 6 de abril de 2020, os clientes podem escolher apenas a VM de quatro núcleos ou mais como nó principal para os novos clusters HDInsight. Os clusters existentes continuarão a ser executados conforme o esperado. 

### <a name="esp-spark-cluster-node-size-change"></a>Alteração do tamanho do nó de cluster do Spark do ESP 
Na próxima versão, o tamanho mínimo de nó permitido para o cluster do Spark do ESP será alterado para Standard_D13_V2. As VMs da série A podem causar problemas de cluster do ESP devido à capacidade relativamente baixa de CPU e de memória. As VMs da série A serão preteridas para a criação de clusters do ESP.

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Migrar para Conjuntos de Dimensionamento de Máquinas Virtuais do Azure
O HDInsight atualmente usa máquinas virtuais do Azure para provisionar o cluster. Na próxima versão, em vez disso, o HDInsight usará os Conjuntos de Dimensionamento de Máquinas Virtuais do Azure. Saiba mais sobre os Conjuntos de Dimensionamento de Máquinas Virtuais do Azure.

## <a name="bug-fixes"></a>Correções de bug
O HDInsight continua a fazer aprimoramentos de desempenho e confiabilidade do cluster. 

## <a name="component-version-change"></a>Alteração na versão do componente
Nenhuma alteração de versão de componente para esta versão. Você pode encontrar as versões de componente atuais para o HDInsight 4.0 e HDInsight 3.6 aqui.

