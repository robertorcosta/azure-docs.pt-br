---
title: CPU pegged no cluster Apache HBase - Azure HDInsight
description: Solucionar problemas cpu no servidor de região no cluster Apache HBase no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/01/2019
ms.openlocfilehash: 16c994029e91d743f1c2a7e2eab51eb86fc378e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887301"
---
# <a name="scenario-pegged-cpu-on-region-server-in-apache-hbase-cluster-in-azure-hdinsight"></a>Cenário: CPU atrelada no servidor de região no cluster Apache HBase no Azure HDInsight

Este artigo descreve etapas de solução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="issue"></a>Problema

O processo de servidor de região do Apache HBase começa a ocupar cerca de 200% de CPU, fazendo com que os alertas de disparem no processo hbase master e cluster para não funcionar em plena capacidade.

## <a name="cause"></a>Causa

Se você estiver executando o cluster HBase v3.4, você pode ter sido atingido por um bug potencial causado pela atualização do jdk para a versão 1.7.0_151. O sintoma que vemos é que o processo do servidor de região `top` começa a ocupar cerca de 200% de CPU (para verificar isso executar o `ps -aux | grep` comando; se há um processo ocupando perto de 200% cpu obter seu pid e confirmar que é processo de servidor de região por execução ).

## <a name="resolution"></a>Resolução

1. Instale o jdk 1.8 em todos os nós do cluster conforme abaixo:

    * Execute a `https://raw.githubusercontent.com/Azure/hbase-utils/master/scripts/upgradetojdk18allnodes.sh`ação do script . Certifique-se de selecionar a opção de executar em todos os nós.

    * Alternativamente, você pode fazer login em cada `sudo add-apt-repository ppa:openjdk-r/ppa -y && sudo apt-get -y update && sudo apt-get install -y openjdk-8-jdk`nó individual e executar o comando .

1. Vá para Ambari `https://<clusterdnsname>.azurehdinsight.net`UI - .

1. Navegue até **HBase->Configs->Advanced->Advanced** `hbase-env configs` e altere a variável `JAVA_HOME` para `export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64`. Guarde o troco de configuração.

1. [Opcional, mas recomendado] [Lave todas as tabelas no cluster](https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/).

1. A partir da Ambari UI novamente, reinicie todos os serviços HBase que precisam ser reiniciados.

1. Dependendo dos dados do cluster, pode levar alguns minutos até uma hora para o cluster atingir o estado estável. A maneira como você confirma que o cluster atinge o estado estável é verificando a UI HMaster (todos os servidores da região devem estar ativos) a partir de Ambari (atualização) ou do headnode executar o shell HBase e, em seguida, executar o comando status.

Para verificar se a atualização foi bem sucedida, verifique se os processos HBase relevantes foram iniciados usando a versão java apropriada - por exemplo, para verificação do servidor de região como:

```
ps -aux | grep regionserver, and verify the version like '''/usr/lib/jvm/java-8-openjdk-amd64/bin/java
```

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure através [do Azure Community Support](https://azure.microsoft.com/support/community/).

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente conectando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se você precisar de mais ajuda, você pode enviar uma solicitação de suporte do [portal Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **Suporte** na barra de menus ou abra o centro **de suporte Ajuda +.** Para obter informações mais detalhadas, consulte [Como criar uma solicitação de suporte ao Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e suporte ao faturamento está incluído na assinatura do Microsoft Azure, e o suporte técnico é fornecido através de um dos Planos de Suporte do [Azure](https://azure.microsoft.com/support/plans/).
