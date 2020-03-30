---
title: Problemas com JDBC/ODBC & estrutura apache thrift - Azure HDInsight
description: Não é possível baixar grandes conjuntos de dados usando a estrutura de software JDBC/ODBC e Apache Thrift no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 23693dcae2f361b88440ec88ca39fd8ed229d85a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894269"
---
# <a name="unable-to-download-large-data-sets-using-jdbcodbc-and-apache-thrift-software-framework-in-hdinsight"></a>Não é possível baixar grandes conjuntos de dados usando jdbc/ODBC e apache thrift estrutura de software em HDInsight

Este artigo descreve etapas de solução de problemas e possíveis resoluções para problemas ao usar componentes Apache Spark em clusters Azure HDInsight.

## <a name="issue"></a>Problema

Ao tentar baixar grandes conjuntos de dados usando JDBC/ODBC e a estrutura de software Apache Thrift no Azure HDInsight, você recebe uma mensagem de erro semelhante à seguinte:

```
org.apache.spark.SparkException: Kryo serialization failed:
Buffer overflow. Available: 0, required: 36518. To avoid this, increase spark.kryoserializer.buffer.max value.
```

## <a name="cause"></a>Causa

Essa exceção é causada pelo processo de serialização que tenta usar mais espaço de buffer do que é permitido. No Spark 2.0.0, `org.apache.spark.serializer.KryoSerializer` a classe é usada para serializar objetos quando os dados são acessados através da estrutura de software Do Brechó Apache. Uma classe diferente é usada para dados que serão enviados pela rede ou armazenados em cache em forma serializada.

## <a name="resolution"></a>Resolução

Aumente `Kryoserializer` o valor de buffer. Adicione uma `spark.kryoserializer.buffer.max` tecla nomeada `2048` e defina-a em ignição2 config abaixo `Custom spark2-thrift-sparkconf`.

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure através [do Azure Community Support](https://azure.microsoft.com/support/community/).

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente conectando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se você precisar de mais ajuda, você pode enviar uma solicitação de suporte do [portal Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **Suporte** na barra de menus ou abra o centro **de suporte Ajuda +.** Para obter informações mais detalhadas, consulte [Como criar uma solicitação de suporte ao Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e suporte ao faturamento está incluído na assinatura do Microsoft Azure, e o suporte técnico é fornecido através de um dos Planos de Suporte do [Azure](https://azure.microsoft.com/support/plans/).
