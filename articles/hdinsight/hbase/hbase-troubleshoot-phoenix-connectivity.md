---
title: Problemas de conectividade apache phoenix no Azure HDInsight
description: Problemas de conectividade entre Apache HBase e Apache Phoenix no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/14/2019
ms.openlocfilehash: b886f51bcb2bb7308c49c76563dcb70148bbc583
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887284"
---
# <a name="scenario-apache-phoenix-connectivity-issues-in-azure-hdinsight"></a>Cenário: Problemas de conectividade apache phoenix no Azure HDInsight

Este artigo descreve etapas de solução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="issue"></a>Problema

Não é possível conectar-se ao Apache HBase com o Apache Phoenix. As razões podem variar.

## <a name="cause-incorrect-ip"></a>Causa: IP incorreto

IP incorreto do nó Zookeeper ativo.

### <a name="resolution"></a>Resolução

O IP do nó zookeeper ativo pode ser identificado a partir da UI Ambari seguindo os links para **HBase** > **Quick Links** > **ZK (Active)** > **Zookeeper Info**. Corrija o IP conforme necessário.

---

## <a name="cause-systemcatalog-table-offline"></a>Causa: SISTEMA. TABELA CATÁLOGO offline

Ao executar comandos `!tables`como , você recebe uma mensagem de erro semelhante a:

```output
Error while connecting to sqlline.py (Hbase - phoenix) Setting property: [isolation, TRANSACTION_READ_COMMITTED] issuing: !connect jdbc:phoenix:10.2.0.7 none none org.apache.phoenix.jdbc.PhoenixDriver Connecting to jdbc:phoenix:10.2.0.7 SLF4J: Class path contains multiple SLF4J bindings.
```

Ao executar comandos `count 'SYSTEM.CATALOG'`como , você recebe uma mensagem de erro semelhante a:

```output
ERROR: org.apache.hadoop.hbase.NotServingRegionException: Region SYSTEM.CATALOG,,1485464083256.c0568c94033870c517ed36c45da98129. is not online on 10.2.0.5,16020,1489466172189)
```

### <a name="resolution"></a>Resolução

A partir da UI Apache Ambari, complete as seguintes etapas para reiniciar o serviço HMaster em todos os nós do ZooKeeper:

1. Na seção **Resumo** do HBase, vá para **HBase** > **Active HBase Master**.

1. Na seção **Componentes,** reinicie o serviço HBase Master.

1. Repita essas etapas para todos os serviços **Standby HBase Master** restantes.

Pode levar até cinco minutos para que o serviço HBase Master estabilize e termine a recuperação. Depois `SYSTEM.CATALOG` que a tabela voltar ao normal, o problema de conectividade com Apache Phoenix deve ser resolvido automaticamente.

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure através [do Azure Community Support](https://azure.microsoft.com/support/community/).

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Conectando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se você precisar de mais ajuda, você pode enviar uma solicitação de suporte do [portal Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **Suporte** na barra de menus ou abra o centro **de suporte Ajuda +.** Para obter informações mais [detalhadas, consulte Como criar uma solicitação de suporte ao Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e suporte ao faturamento está incluído na assinatura do Microsoft Azure, e o suporte técnico é fornecido através de um dos Planos de Suporte do [Azure](https://azure.microsoft.com/support/plans/).
