---
title: Erro de ilegalidadedeexceterpara Apache Spark - Azure HDInsight
description: IllegalArgumentException para atividades do Apache Spark no Azure HDInsight para fábrica de dados do Azure
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: df62dbd8db7d41eb11207c7741aed76cec0ac7a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894373"
---
# <a name="scenario-illegalargumentexception-for-apache-spark-activity-in-azure-hdinsight"></a>Cenário: IlegalArgumentException para atividades do Apache Spark no Azure HDInsight

Este artigo descreve etapas de solução de problemas e possíveis resoluções para problemas ao usar componentes Apache Spark em clusters Azure HDInsight.

## <a name="issue"></a>Problema

Você recebe a seguinte exceção ao tentar executar uma atividade spark em um pipeline da Fábrica de Dados Do Azure:

```error
Exception in thread "main" java.lang.IllegalArgumentException:
Wrong FS: wasbs://additional@xxx.blob.core.windows.net/spark-examples_2.11-2.1.0.jar, expected: wasbs://wasbsrepro-2017-11-07t00-59-42-722z@xxx.blob.core.windows.net
```

## <a name="cause"></a>Causa

Um trabalho spark falhará se o arquivo do frasco de aplicativo não estiver localizado no armazenamento padrão/principal do cluster Spark.

Este é um problema conhecido com a estrutura de código aberto Spark rastreada neste bug: [O trabalho de faísca falha se fs.defaultFS e o jarder de aplicativos forem url diferentes](https://issues.apache.org/jira/browse/SPARK-22587).

Este problema foi resolvido no Spark 2.3.0.

## <a name="resolution"></a>Resolução

Certifique-se de que o jarro do aplicativo esteja armazenado no armazenamento padrão/principal para o cluster HDInsight. No caso da Fábrica de Dados Do Azure, certifique-se de que o serviço vinculado ao ADF seja apontado para o contêiner padrão HDInsight em vez de um contêiner secundário.

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure através [do Azure Community Support](https://azure.microsoft.com/support/community/).

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente conectando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se você precisar de mais ajuda, você pode enviar uma solicitação de suporte do [portal Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **Suporte** na barra de menus ou abra o centro **de suporte Ajuda +.** Para obter informações mais detalhadas, consulte [Como criar uma solicitação de suporte ao Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e suporte ao faturamento está incluído na assinatura do Microsoft Azure, e o suporte técnico é fornecido através de um dos Planos de Suporte do [Azure](https://azure.microsoft.com/support/plans/).
