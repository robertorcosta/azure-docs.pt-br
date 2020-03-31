---
title: RpcTimeoutException para brechó Apache Spark - Azure HDInsight
description: Você vê 502 erros ao processar grandes conjuntos de dados usando o servidor de economia Apache Spark
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: b15ac80295a0113eb0c384e1cc3185f3304c39c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894276"
---
# <a name="scenario-rpctimeoutexception-for-apache-spark-thrift-server-in-azure-hdinsight"></a>Cenário: RpcTimeoutException para servidor de brechó Apache Spark no Azure HDInsight

Este artigo descreve etapas de solução de problemas e possíveis resoluções para problemas ao usar componentes Apache Spark em clusters Azure HDInsight.

## <a name="issue"></a>Problema

O aplicativo Spark `org.apache.spark.rpc.RpcTimeoutException` falha com `Futures timed out`uma exceção e uma mensagem: , como no exemplo a seguir:

```
org.apache.spark.rpc.RpcTimeoutException: Futures timed out after [120 seconds]. This timeout is controlled by spark.rpc.askTimeout
 at org.apache.spark.rpc.RpcTimeout.org$apache$spark$rpc$RpcTimeout$$createRpcTimeoutException(RpcTimeout.scala:48)
```

`OutOfMemoryError`e `overhead limit exceeded` erros também podem `sparkthriftdriver.log` aparecer no exemplo a seguir:

```
WARN  [rpc-server-3-4] server.TransportChannelHandler: Exception in connection from /10.0.0.17:53218
java.lang.OutOfMemoryError: GC overhead limit exceeded
```

## <a name="cause"></a>Causa

Esses erros são causados pela falta de recursos de memória durante o processamento de dados. Se o processo de coleta de lixo Java começar, pode levar à suspensão da aplicação spark. As consultas começarão a cronometrar e parar o processamento. O `Futures timed out` erro indica um aglomerado estresse severo.

## <a name="resolution"></a>Resolução

Aumente o tamanho do cluster adicionando mais nós de trabalhadorou aumentando a capacidade de memória dos nós de cluster existentes. Você também pode ajustar o pipeline de dados para reduzir a quantidade de dados que estão sendo processados de uma só vez.

O `spark.network.timeout` controle do tempo para todas as conexões de rede. Aumentar o tempo de tempo da rede pode permitir mais tempo para que algumas operações críticas sejam concluídas, mas isso não resolverá o problema completamente.

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure através [do Azure Community Support](https://azure.microsoft.com/support/community/).

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente conectando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se você precisar de mais ajuda, você pode enviar uma solicitação de suporte do [portal Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **Suporte** na barra de menus ou abra o centro **de suporte Ajuda +.** Para obter informações mais detalhadas, consulte [Como criar uma solicitação de suporte ao Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e suporte ao faturamento está incluído na assinatura do Microsoft Azure, e o suporte técnico é fornecido através de um dos Planos de Suporte do [Azure](https://azure.microsoft.com/support/plans/).
