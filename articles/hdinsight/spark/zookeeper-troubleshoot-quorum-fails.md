---
title: O servidor Apache ZooKeeper não consegue formar quórum no Azure HDInsight
description: O servidor Apache ZooKeeper não consegue formar quórum no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/20/2019
ms.openlocfilehash: 4e46efaf17ae9bad5df6f1f61f401d3e6de58a85
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78250232"
---
# <a name="apache-zookeeper-server-fails-to-form-a-quorum-in-azure-hdinsight"></a>O servidor Apache ZooKeeper não consegue formar quórum no Azure HDInsight

Este artigo descreve etapas de solução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="issue"></a>Problema

O servidor Apache ZooKeeper não é saudável, os sintomas podem incluir: ambos os Gerentes de Recursos/Nós de Nome estão em modo de espera, as operações simples de HDFS não funcionam, `zkFailoverController` são interrompidas e não podem ser iniciadas, os trabalhos de Fio/Faísca/Livy falham devido a erros do Zookeeper. Os Daemons LLAP também podem falhar em iniciar em clusters Secure Spark ou Interactive Hive. Você pode ver uma mensagem de erro semelhante a:

```
19/06/19 08:27:08 ERROR ZooKeeperStateStore: Fatal Zookeeper error. Shutting down Livy server.
19/06/19 08:27:08 INFO LivyServer: Shutting down Livy server.
```

No Zookeeper Server logs on qualquer host Zookeeper em /var/log/zookeeper/zookeeper-zookeeper-zookeeper-server-\*.out, você também pode ver o seguinte erro:

```
2020-02-12 00:31:52,513 - ERROR [CommitProcessor:1:NIOServerCnxn@178] - Unexpected Exception:
java.nio.channels.CancelledKeyException
```

## <a name="cause"></a>Causa

Quando o volume de arquivos de snapshot for grande ou os arquivos de snapshot estiverem corrompidos, o servidor ZooKeeper falhará em formar um quórum, o que faz com que os serviços relacionados ao ZooKeeper sejam insalubres. O servidor ZooKeeper não removerá arquivos de instantâneos antigos de seu diretório de dados, em vez disso, é uma tarefa periódica a ser executada pelos usuários para manter a saudabilidade do ZooKeeper. Para obter mais informações, consulte [As Forças e Limitações do ZooKeeper](https://zookeeper.apache.org/doc/r3.3.5/zookeeperAdmin.html#sc_strengthsAndLimitations).

## <a name="resolution"></a>Resolução

Verifique o diretório `/hadoop/zookeeper/version-2` de `/hadoop/hdinsight-zookeeper/version-2` dados do ZooKeeper e descubra se o tamanho do arquivo de instantâneos é grande. Tome as seguintes etapas se houver grandes instantâneos:

1. Fazer backup de `/hadoop/zookeeper/version-2` `/hadoop/hdinsight-zookeeper/version-2`instantâneos e .

1. Limpe instantâneos `/hadoop/zookeeper/version-2` `/hadoop/hdinsight-zookeeper/version-2`e.

1. Reinicie todos os servidores ZooKeeper da UI Apache Ambari.

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

- Obtenha respostas de especialistas do Azure através [do Azure Community Support](https://azure.microsoft.com/support/community/).

- Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Conectando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

- Se você precisar de mais ajuda, você pode enviar uma solicitação de suporte do [portal Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **Suporte** na barra de menus ou abra o centro **de suporte Ajuda +.** Para obter informações mais [detalhadas, consulte Como criar uma solicitação de suporte ao Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e suporte ao faturamento está incluído na assinatura do Microsoft Azure, e o suporte técnico é fornecido através de um dos Planos de Suporte do [Azure](https://azure.microsoft.com/support/plans/).
