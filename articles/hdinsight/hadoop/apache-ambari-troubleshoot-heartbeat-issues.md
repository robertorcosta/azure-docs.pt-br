---
title: Problemas de pulsação do Apache Ambari no Azure HDInsight
description: Análise de várias razões para problemas de batimentos cardíacos Apache Ambari no Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/06/2020
ms.openlocfilehash: ab88f65d535be2aef5f0b26fa1171c03276466e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057066"
---
# <a name="apache-ambari-heartbeat-issues-in-azure-hdinsight"></a>Problemas de pulsação do Apache Ambari no Azure HDInsight

Este artigo descreve etapas de solução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="scenario-high-cpu-utilization"></a>Cenário: Alta utilização da CPU

### <a name="issue"></a>Problema

O agente Ambari tem alta utilização da CPU, o que resulta em alertas da Ambari UI que para alguns nódulos o batimento cardíaco do agente Ambari é perdido. O alerta perdido por batimentos cardíacos geralmente é passageiro.

### <a name="cause"></a>Causa

Devido a vários bugs de ambari-agent, em casos raros, seu ambari-agente pode ter alta (perto de 100) utilização percentual da CPU.

### <a name="resolution"></a>Resolução

1. Identificar o ID do processo (pid) do ambari-agente:

    ```bash
    ps -ef | grep ambari_agent
    ```

1. Em seguida, execute o seguinte comando para mostrar a utilização da CPU:

    ```bash
    top -p <ambari-agent-pid>
    ```

1. Reiniciar o ambari-agent para mitigar o problema:

    ```bash
    service ambari-agent restart
    ```

1. Se a reinicialização não funcionar, mate o processo de ambari-agent e, em seguida, inicie-o:

    ```bash
    kill -9 <ambari-agent-pid>
    service ambari-agent start
    ```

---

## <a name="scenario-ambari-agent-not-started"></a>Cenário: Agente Ambari não iniciado

### <a name="issue"></a>Problema

O agente Ambari ainda não começou, o que resulta em alertas da Ambari UI que para alguns nós o batimento cardíaco do agente Ambari está perdido.

### <a name="cause"></a>Causa

Os alertas são causados pelo agente Ambari não correr.

### <a name="resolution"></a>Resolução

1. Confirme o status do ambari-agente:

    ```bash
    service ambari-agent status
    ```

1. Confirme se os serviços do controlador failover estão sendo executados:

    ```bash
    ps -ef | grep failover
    ```

    Se os serviços do controlador failover não estão sendo executados, é provável que devido a um problema impeça o hdinsight-agent de iniciar o controlador failover. Verifique o registro do `/var/log/hdinsight-agent/hdinsight-agent.out` agente hdinsight do arquivo.

## <a name="scenario-heartbeat-lost-for-ambari"></a>Cenário: Batimentos cardíacos perdidos para Ambari

### <a name="issue"></a>Problema

O agente de batimentos cardíacos Ambari estava perdido.

### <a name="cause"></a>Causa

Os registros do OMS estão causando alta utilização da CPU.

### <a name="resolution"></a>Resolução

* Desativar o registro do Monitor Do Azure usando o cmdlet [Disable-AzHDInsightMonitoring](https://docs.microsoft.com/powershell/module/az.hdinsight/disable-azhdinsightmonitoring) PowerShell.
* Exclua `mdsd.warn` o arquivo de log

---

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure através [do Azure Community Support](https://azure.microsoft.com/support/community/).

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente conectando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se você precisar de mais ajuda, você pode enviar uma solicitação de suporte do [portal Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **Suporte** na barra de menus ou abra o centro **de suporte Ajuda +.** Para obter informações mais detalhadas, consulte [Como criar uma solicitação de suporte ao Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e suporte ao faturamento está incluído na assinatura do Microsoft Azure, e o suporte técnico é fornecido através de um dos Planos de Suporte do [Azure](https://azure.microsoft.com/support/plans/).
