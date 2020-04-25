---
title: O Apache ZooKeeper Server falha em formar um quorum no Azure HDInsight
description: O Apache ZooKeeper Server falha em formar um quorum no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/20/2019
ms.openlocfilehash: 41ac109e5c5379e6085dd57a3fcd8119915558fb
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82133282"
---
# <a name="apache-zookeeper-server-fails-to-form-a-quorum-in-azure-hdinsight"></a>O Apache ZooKeeper Server falha em formar um quorum no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao interagir com clusters do Azure HDInsight.

## <a name="issue"></a>Problema

Apache ZooKeeper servidor não estiver íntegro, os sintomas podem incluir: os dois gerenciadores de recursos/nós de nome estão no modo de espera, as operações `zkFailoverController` simples de HDFS não funcionam, são interrompidas e não podem ser iniciadas, os trabalhos yarn/Spark/Livy falham devido a erros ZooKeeper. Os daemons do LLAP também podem falhar ao iniciar em clusters do hive interativo ou Spark. Você poderá ver uma mensagem de erro semelhante a:

```
19/06/19 08:27:08 ERROR ZooKeeperStateStore: Fatal Zookeeper error. Shutting down Livy server.
19/06/19 08:27:08 INFO LivyServer: Shutting down Livy server.
```

No servidor Zookeeper faz logon em qualquer host Zookeeper em/var/log/Zookeeper/Zookeeper-Zookeeper-Server-\*. out, você também pode ver o seguinte erro:

```
2020-02-12 00:31:52,513 - ERROR [CommitProcessor:1:NIOServerCnxn@178] - Unexpected Exception:
java.nio.channels.CancelledKeyException
```

## <a name="cause"></a>Causa

Quando o volume de arquivos de instantâneo for grande ou os arquivos de instantâneo estiverem corrompidos, o servidor ZooKeeper não conseguirá formar um quorum, o que faz com que os serviços relacionados ao ZooKeeper não estejam íntegros. O servidor ZooKeeper não removerá arquivos de instantâneo antigos de seu diretório de dados, em vez disso, é uma tarefa periódica a ser executada pelos usuários para manter a integridade do ZooKeeper. Para obter mais informações, consulte [pontos fortes e limitações do ZooKeeper](https://zookeeper.apache.org/doc/r3.3.5/zookeeperAdmin.html#sc_strengthsAndLimitations).

## <a name="resolution"></a>Resolução

Verifique o diretório `/hadoop/zookeeper/version-2` de dados `/hadoop/hdinsight-zookeeper/version-2` do ZooKeeper e descubra se o tamanho do arquivo de instantâneos é grande. Execute as seguintes etapas se existirem instantâneos grandes:

1. Verifique o status de outros servidores ZooKeeper no mesmo quorum para certificar-se de que eles estão funcionando bem com o`echo stat | nc {zk_host_ip} 2181 (or 2182)`comando "".  

1. Faça logon no host ZooKeeper problemático, instantâneos de backup e logs `/hadoop/zookeeper/version-2` de `/hadoop/hdinsight-zookeeper/version-2`transações no e, em seguida, limpe esses arquivos nos dois diretórios. 

1. Reinicie o servidor ZooKeeper problemático no Ambari ou no host ZooKeeper. Em seguida, reinicie o serviço que tem problemas.

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

- Obtenha respostas de especialistas do Azure por meio do [suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

- Conecte- [@AzureSupport](https://twitter.com/azuresupport) se com a conta de Microsoft Azure oficial para melhorar a experiência do cliente. Conectando a Comunidade do Azure aos recursos certos: respostas, suporte e especialistas.

- Se precisar de mais ajuda, você poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **suporte** na barra de menus ou abra o Hub **ajuda + suporte** . Para obter informações mais detalhadas, consulte [como criar uma solicitação de suporte do Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [planos de suporte do Azure](https://azure.microsoft.com/support/plans/).
