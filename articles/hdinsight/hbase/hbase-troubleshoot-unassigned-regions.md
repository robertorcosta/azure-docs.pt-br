---
title: Problemas com servidores de região no Azure HDInsight
description: Problemas com servidores de região no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/16/2019
ms.openlocfilehash: 85aeafb2c4461b50d399e40d9abff2ac04b677c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272754"
---
# <a name="issues-with-region-servers-in-azure-hdinsight"></a>Problemas com servidores de região no Azure HDInsight

Este artigo descreve etapas de solução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="scenario-unassigned-regions"></a>Cenário: Regiões não atribuídas

### <a name="issue"></a>Problema

Ao `hbase hbck` executar o comando, você vê uma mensagem de erro semelhante a:

```
multiple regions being unassigned or holes in the chain of regions
```

A partir do Apache HBase Master UI, você pode ver o número de regiões que estão desequilibradas em todos os servidores da região. Depois, você pode executar o comando `hbase hbck` para ver os espaços na cadeia de regiões.

### <a name="cause"></a>Causa

Os buracos podem ser o resultado de regiões offline.

### <a name="resolution"></a>Resolução

Conserte as atribuições. Siga as etapas abaixo para exibir as regiões não atribuídas de volta no estado normal:

1. Faça login no cluster HDInsight HBase usando O SSH.

1. Executar `hbase zkcli` comando para conectar com a shell ZooKeeper.

1. Executar `rmr /hbase/regions-in-transition` `rmr /hbase-unsecure/regions-in-transition` ou comandar.

1. Saia da concha `exit` do zookeeper usando o comando.

1. Abra a interface do usuário do Apache Ambari e reinicie o serviço Active HBase Master.

1. Executar `hbase hbck` comando novamente (sem mais opções). Verifique a saída e certifique-se de que todas as regiões estão sendo atribuídas.

---

## <a name="scenario-dead-region-servers"></a>Cenário: Servidores da região morta

### <a name="issue"></a>Problema

Os servidores da região não conseguem iniciar.

### <a name="cause"></a>Causa

Vários diretórios wal de divisão.

1. Obter lista de WALs atuais: `hadoop fs -ls -R /hbase/WALs/ > /tmp/wals.out`.

1. Inspecione o `wals.out` arquivo. Se houver muitos diretórios de divisão (começando com *-spliting), o servidor da região provavelmente está falhando por causa desses diretórios.

### <a name="resolution"></a>Resolução

1. Pare o HBase do portal Ambari.

1. Execute `hadoop fs -ls -R /hbase/WALs/ > /tmp/wals.out` para obter uma nova lista de WALs.

1. Mova os diretórios *de divisão `splitWAL`para uma pasta temporária e exclua os diretórios *de divisão.

1. Execute `hbase zkcli` o comando para se conectar com o zookeeper shell.

1. Execute `rmr /hbase-unsecure/splitWAL`.

1. Reiniciar o serviço HBase.

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure através [do Azure Community Support](https://azure.microsoft.com/support/community/).

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Conectando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se você precisar de mais ajuda, você pode enviar uma solicitação de suporte do [portal Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **Suporte** na barra de menus ou abra o centro **de suporte Ajuda +.** Para obter informações mais [detalhadas, consulte Como criar uma solicitação de suporte ao Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e suporte ao faturamento está incluído na assinatura do Microsoft Azure, e o suporte técnico é fornecido através de um dos Planos de Suporte do [Azure](https://azure.microsoft.com/support/plans/).
