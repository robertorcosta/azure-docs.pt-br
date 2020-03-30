---
title: Aplicativo Apache Spark Streaming pára após 24 dias no Azure HDInsight
description: Um aplicativo Apache Spark Streaming pára após a execução por 24 dias e não há erros nos arquivos de log.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: ff410ea1b6c54d2f58babeb20c68fe95033e9728
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894428"
---
# <a name="scenario-apache-spark-streaming-application-stops-after-executing-for-24-days-in-azure-hdinsight"></a>Cenário: Aplicativo Apache Spark Streaming pára após ser executado por 24 dias no Azure HDInsight

Este artigo descreve etapas de solução de problemas e possíveis resoluções para problemas ao usar componentes Apache Spark em clusters Azure HDInsight.

## <a name="issue"></a>Problema

Um aplicativo Apache Spark Streaming pára após a execução por 24 dias e não há erros nos arquivos de log.

## <a name="cause"></a>Causa

O `livy.server.session.timeout` valor controla quanto tempo Apache Livy deve esperar por uma sessão para ser concluída. Uma vez que o `session.timeout` comprimento da sessão atinja o valor, a sessão Delivy e o aplicativo são automaticamente mortos.

## <a name="resolution"></a>Resolução

Para trabalhos de longa `livy.server.session.timeout` duração, aumente o valor do uso da UI Ambari. Você pode acessar a configuração Livy a partir `https://<yourclustername>.azurehdinsight.net/#/main/services/LIVY/configs`da UI Ambari usando a URL .

Substitua pelo `<yourclustername>` nome do seu cluster HDInsight como mostrado no portal.

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure através [do Azure Community Support](https://azure.microsoft.com/support/community/).

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente conectando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se você precisar de mais ajuda, você pode enviar uma solicitação de suporte do [portal Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **Suporte** na barra de menus ou abra o centro **de suporte Ajuda +.** Para obter informações mais detalhadas, consulte [Como criar uma solicitação de suporte ao Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e suporte ao faturamento está incluído na assinatura do Microsoft Azure, e o suporte técnico é fornecido através de um dos Planos de Suporte do [Azure](https://azure.microsoft.com/support/plans/).
