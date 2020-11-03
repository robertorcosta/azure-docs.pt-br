---
title: Apache Hive conexões com o Apache Zookeeper – Azure HDInsight
description: Apache Hive exibição inacessível devido a problemas do Apache Zookeeper no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 1ac237edeea777225b7cf99946d359c921331b18
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93288919"
---
# <a name="scenario-apache-hive-fails-to-establish-a-connection-to-apache-zookeeper-in-azure-hdinsight"></a>Cenário: Apache Hive falha ao estabelecer uma conexão com o Apache Zookeeper no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao usar componentes de consulta interativa em clusters do Azure HDInsight.

## <a name="issue"></a>Problema

A exibição do hive está inacessível e os logs em `/var/log/hive` mostram um erro semelhante ao seguinte:

```
ERROR [Curator-Framework-0]: curator.ConnectionState (ConnectionState.java:checkTimeouts(200)) - Connection timed out for connection string (zk0-cluster.cloud.wbmi.com:2181,zk1-cluster.cloud.wbmi.com:2181,zk2-cluster.cloud.wbmi.com:2181) and timeout (15000) / elapsed (21852)
```

## <a name="cause"></a>Causa

É possível que o hive falhe ao estabelecer uma conexão com o Zookeeper, o que impede a inicialização do modo de exibição do hive.

## <a name="resolution"></a>Resolução

1. Verifique se o serviço Zookeeper está íntegro.

1. Verifique se o serviço Zookeeper tem uma entrada ZNode para o hive Server2. O valor estará ausente ou incorreto.

    ```
    /usr/hdp/2.6.2.25-1/zookeeper/bin/zkCli.sh -server zk1-wbwdhs
    [zk: zk0-cluster(CONNECTED) 0] ls /hiveserver2-hive2
    ```

1. Para restabelecer a conectividade, reinicialize os nós Zookeeper e reinicialize o HiveServer2.

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]