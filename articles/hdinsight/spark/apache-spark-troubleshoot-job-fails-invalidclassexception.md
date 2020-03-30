---
title: Erro invalidClassException do Apache Spark - Azure HDInsight
description: O trabalho do Apache Spark falha com o InvalidClassException, incompatível com a versão de classe, no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: be50f8716835b0842f854842e5340b0bb8594136
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894360"
---
# <a name="apache-spark-job-fails-with-invalidclassexception-class-version-mismatch-in-azure-hdinsight"></a>O trabalho do Apache Spark falha com o InvalidClassException, incompatível com a versão de classe, no Azure HDInsight

Este artigo descreve etapas de solução de problemas e possíveis resoluções para problemas ao usar componentes Apache Spark em clusters Azure HDInsight.

## <a name="issue"></a>Problema

Você tenta criar um trabalho apache spark em um cluster Spark 2.x. Falha com um erro semelhante ao seguinte:

```
18/09/18 09:32:26 WARN TaskSetManager: Lost task 0.0 in stage 1.0 (TID 1, wn7-dev-co.2zyfbddadfih0xdq0cdja4g.ax.internal.cloudapp.net, executor 4): java.io.InvalidClassException:
org.apache.commons.lang3.time.FastDateFormat; local class incompatible: stream classdesc serialVersionUID = 2, local class serialVersionUID = 1
        at java.io.ObjectStreamClass.initNonProxy(ObjectStreamClass.java:699)
        at java.io.ObjectInputStream.readNonProxyDesc(ObjectInputStream.java:1885)
        at java.io.ObjectInputStream.readClassDesc(ObjectInputStream.java:1751)
        at java.io.ObjectInputStream.readOrdinaryObject(ObjectInputStream.java:2042)
        at java.io.ObjectInputStream.readObject0(ObjectInputStream.java:1573)
```

## <a name="cause"></a>Causa

Este erro pode ser causado pela `spark.yarn.jars` adição de um frasco adicional à configuração, especificamente um frasco sombreado que inclui uma versão diferente do `commons-lang3` pacote e introduz uma incompatibilidade de classe. Por padrão, o Spark 2.1/2/3 `commons-lang3`usa a versão 3.5 de .

> [!TIP]
> Para sombrear uma biblioteca é colocar seu conteúdo em seu próprio frasco, mudando seu pacote. Isso difere da embalagem da biblioteca, que é colocar a biblioteca em seu próprio frasco sem reembalagem.

## <a name="resolution"></a>Resolução

Remova o frasco ou recompile o frasco personalizado (AzureLogAppender) e use [o plugin maven-shade](https://maven.apache.org/plugins/maven-shade-plugin/examples/class-relocation.html) para realocar classes.

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure através [do Azure Community Support](https://azure.microsoft.com/support/community/).

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente conectando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se você precisar de mais ajuda, você pode enviar uma solicitação de suporte do [portal Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **Suporte** na barra de menus ou abra o centro **de suporte Ajuda +.** Para obter informações mais detalhadas, consulte [Como criar uma solicitação de suporte ao Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e suporte ao faturamento está incluído na assinatura do Microsoft Azure, e o suporte técnico é fornecido através de um dos Planos de Suporte do [Azure](https://azure.microsoft.com/support/plans/).
