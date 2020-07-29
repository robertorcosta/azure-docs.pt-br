---
title: Problemas de pulsação do Apache Ambari no Azure HDInsight
description: Revisão de vários motivos para problemas de pulsação do Apache Ambari no Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/06/2020
ms.openlocfilehash: ab88f65d535be2aef5f0b26fa1171c03276466e8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "77057066"
---
# <a name="apache-ambari-heartbeat-issues-in-azure-hdinsight"></a>Problemas de pulsação do Apache Ambari no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao interagir com clusters do Azure HDInsight.

## <a name="scenario-high-cpu-utilization"></a>Cenário: alta utilização da CPU

### <a name="issue"></a>Problema

O agente Ambari tem alta utilização da CPU, o que resulta em alertas da interface do usuário do Ambari que, para alguns nós, a pulsação do agente do Ambari é perdida. O alerta de pulsação perdida geralmente é transitório.

### <a name="cause"></a>Causa

Devido a vários bugs do ambari Agent, em casos raros, o ambari-Agent pode ter alta utilização de CPU de percentual (perto de 100).

### <a name="resolution"></a>Resolução

1. Identificar a ID do processo (PID) do ambari-Agent:

    ```bash
    ps -ef | grep ambari_agent
    ```

1. Em seguida, execute o seguinte comando para mostrar a utilização da CPU:

    ```bash
    top -p <ambari-agent-pid>
    ```

1. Reinicie o ambari-Agent para atenuar o problema:

    ```bash
    service ambari-agent restart
    ```

1. Se a reinicialização não funcionar, encerre o processo do ambari e inicie-o:

    ```bash
    kill -9 <ambari-agent-pid>
    service ambari-agent start
    ```

---

## <a name="scenario-ambari-agent-not-started"></a>Cenário: agente Ambari não iniciado

### <a name="issue"></a>Problema

O agente Ambari não foi iniciado, o que resulta em alertas da interface do usuário do Ambari que, para alguns nós, a pulsação do agente do Ambari é perdida.

### <a name="cause"></a>Causa

Os alertas são causados pelo agente do Ambari não estar em execução.

### <a name="resolution"></a>Resolução

1. Confirme o status do ambari-Agent:

    ```bash
    service ambari-agent status
    ```

1. Confirme se os serviços do controlador de failover estão em execução:

    ```bash
    ps -ef | grep failover
    ```

    Se os serviços do controlador de failover não estiverem em execução, provavelmente devido a um problema, impeça que o hdinsight-Agent inicie o controlador de failover. Verifique o log do hdinsight-Agent do `/var/log/hdinsight-agent/hdinsight-agent.out` arquivo.

## <a name="scenario-heartbeat-lost-for-ambari"></a>Cenário: pulsação perdida para Ambari

### <a name="issue"></a>Problema

O agente de pulsação Ambari foi perdido.

### <a name="cause"></a>Causa

Os logs do OMS estão causando alta utilização da CPU.

### <a name="resolution"></a>Resolução

* Desabilite o log de Azure Monitor usando o cmdlet [Disable-AzHDInsightMonitoring](https://docs.microsoft.com/powershell/module/az.hdinsight/disable-azhdinsightmonitoring) do PowerShell.
* Excluir o `mdsd.warn` arquivo de log

---

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio do [Suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte-se a [@AzureSupport](https://twitter.com/azuresupport) – a conta oficial do Microsoft Azure para melhorar a experiência do cliente conectando-se à comunidade do Azure para os recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **Suporte** na barra de menus ou abra o hub **Ajuda + suporte**. Para obter informações mais detalhadas, confira [Como criar uma solicitação de suporte do Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso ao Gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [Planos de suporte do Azure](https://azure.microsoft.com/support/plans/).
