---
title: Tempos limite com o comando 'hbase hbck' no Azure HDInsight
description: Problema de tempo limite com o comando ' HBase hbck ' ao corrigir atribuições de região
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/16/2019
ms.openlocfilehash: 5604b42e1611830f3aaea9ae180cdb8142ab0942
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "75887182"
---
# <a name="scenario-timeouts-with-hbase-hbck-command-in-azure-hdinsight"></a>Cenário: tempos limite com o comando ' HBase hbck ' no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao interagir com clusters do Azure HDInsight.

## <a name="issue"></a>Problema

Encontre tempos limite com o `hbase hbck` comando ao corrigir atribuições de região.

## <a name="cause"></a>Causa

Uma possível causa de problemas do tempo limite, quando você usa o comando `hbck`, é que várias regiões se encontram no estado "em transição" por um longo tempo. Você pode ver essas regiões como offline na interface do usuário do HBase Master. Como um grande número de regiões está tentando fazer a transição, HBase Master pode atingir o tempo limite e não conseguir colocar as regiões online novamente.

## <a name="resolution"></a>Resolução

1. Entre no cluster HBase do HDInsight usando SSH.

1. Execute o `hbase zkcli` comando para se conectar ao Apache ZooKeeper Shell.

1. Executar `rmr /hbase/regions-in-transition` ou `rmr /hbase-unsecure/regions-in-transition` comando.

1. Saia do `hbase zkcli` shell usando o `exit` comando.

1. Na interface do usuário do Apache Ambari, reinicie o serviço do Active HBase Master.

1. Execute o comando `hbase hbck -fixAssignments`.

1. Monitore a interface do usuário do HBase Master "região em transição" para garantir que nenhuma região fique presa.

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

- Obtenha respostas de especialistas do Azure por meio do [Suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

- Conecte-se com [@AzureSupport](https://twitter.com/azuresupport), a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Como se conectar à comunidade do Azure para os recursos certos: respostas, suporte e especialistas.

- Se precisar de mais ajuda, poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **Suporte** na barra de menus ou abra o hub **Ajuda + suporte**. Para obter informações mais detalhadas, consulte [Como criar uma solicitação de Suporte do Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso ao Gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [Planos de suporte do Azure](https://azure.microsoft.com/support/plans/).
