---
title: Tempos limite com o comando 'hbase hbck' no Azure HDInsight
description: Problema de tempo com o comando 'hbase hbck' ao corrigir atribuições da região
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/16/2019
ms.openlocfilehash: 5604b42e1611830f3aaea9ae180cdb8142ab0942
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887182"
---
# <a name="scenario-timeouts-with-hbase-hbck-command-in-azure-hdinsight"></a>Cenário: Tempos esgotados com o comando 'hbase hbck' no Azure HDInsight

Este artigo descreve etapas de solução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="issue"></a>Problema

Encontre intervalos `hbase hbck` de tempo com comando ao corrigir atribuições da região.

## <a name="cause"></a>Causa

Uma possível causa de problemas do tempo limite, quando você usa o comando `hbck`, é que várias regiões se encontram no estado "em transição" por um longo tempo. Você pode ver essas regiões como offline na interface do usuário do HBase Master. Como um alto número de regiões está tentando fazer a transição, o HBase Master pode ficar sem tempo e ser incapaz de colocar essas regiões de volta on-line.

## <a name="resolution"></a>Resolução

1. Faça login no cluster HDInsight HBase usando O SSH.

1. Execute `hbase zkcli` o comando para se conectar com o shell Apache ZooKeeper.

1. Executar `rmr /hbase/regions-in-transition` `rmr /hbase-unsecure/regions-in-transition` ou comandar.

1. Saia `hbase zkcli` do shell `exit` usando o comando.

1. A partir da UI Apache Ambari, reinicie o serviço Active HBase Master.

1. Execute o comando `hbase hbck -fixAssignments`.

1. Monitore a "região em transição" do HBase Master UI para garantir que nenhuma região fique presa.

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

- Obtenha respostas de especialistas do Azure através [do Azure Community Support](https://azure.microsoft.com/support/community/).

- Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Conectando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

- Se você precisar de mais ajuda, você pode enviar uma solicitação de suporte do [portal Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **Suporte** na barra de menus ou abra o centro **de suporte Ajuda +.** Para obter informações mais [detalhadas, consulte Como criar uma solicitação de suporte ao Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e suporte ao faturamento está incluído na assinatura do Microsoft Azure, e o suporte técnico é fornecido através de um dos Planos de Suporte do [Azure](https://azure.microsoft.com/support/plans/).
