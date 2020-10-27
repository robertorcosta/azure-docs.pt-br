---
title: RpcTimeoutException para Apache Spark Thrift-Azure HDInsight
description: Você verá erros 502 ao processar grandes conjuntos de dados usando Apache Spark servidor thrift
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: fdf8a6d36d6b9d112ef5b3ea50991ac7d98fcbae
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/26/2020
ms.locfileid: "92547258"
---
# <a name="scenario-rpctimeoutexception-for-apache-spark-thrift-server-in-azure-hdinsight"></a>Cenário: RpcTimeoutException para o servidor de Apache Spark Thrift no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções de problemas ao usar os componentes do Apache Spark nos clusters do Azure HDInsight.

## <a name="issue"></a>Problema

O aplicativo Spark falha com uma `org.apache.spark.rpc.RpcTimeoutException` exceção e uma mensagem: `Futures timed out` , como no exemplo a seguir:

```
org.apache.spark.rpc.RpcTimeoutException: Futures timed out after [120 seconds]. This timeout is controlled by spark.rpc.askTimeout
 at org.apache.spark.rpc.RpcTimeout.org$apache$spark$rpc$RpcTimeout$$createRpcTimeoutException(RpcTimeout.scala:48)
```

`OutOfMemoryError` e `overhead limit exceeded` os erros também podem aparecer no `sparkthriftdriver.log` como no exemplo a seguir:

```
WARN  [rpc-server-3-4] server.TransportChannelHandler: Exception in connection from /10.0.0.17:53218
java.lang.OutOfMemoryError: GC overhead limit exceeded
```

## <a name="cause"></a>Causa

Esses erros são causados por uma falta de recursos de memória durante o processamento de dados. Se o processo de coleta de lixo do Java for iniciado, ele poderá levar ao aplicativo Spark para parar de responder. As consultas começarão a atingir o tempo limite e interromperão o processamento. O `Futures timed out` erro indica um cluster sob sobrecarga grave.

## <a name="resolution"></a>Resolução

Aumente o tamanho do cluster adicionando mais nós de trabalho ou aumentando a capacidade de memória dos nós de cluster existentes. Você também pode ajustar o pipeline de dados para reduzir a quantidade de dados que estão sendo processados de uma vez.

O `spark.network.timeout` controla o tempo limite para todas as conexões de rede. Aumentar o tempo limite da rede pode permitir mais tempo para que algumas operações críticas sejam concluídas, mas isso não resolverá completamente o problema.

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio do [Suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte-se a [@AzureSupport](https://twitter.com/azuresupport) – a conta oficial do Microsoft Azure para melhorar a experiência do cliente conectando-se à comunidade do Azure para os recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **Suporte** na barra de menus ou abra o hub **Ajuda + suporte** . Para obter informações mais detalhadas, confira [Como criar uma solicitação de suporte do Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). O acesso ao Gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [Planos de suporte do Azure](https://azure.microsoft.com/support/plans/).