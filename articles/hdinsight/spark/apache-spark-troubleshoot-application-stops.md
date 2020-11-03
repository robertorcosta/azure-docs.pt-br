---
title: Apache Spark aplicativo de streaming é interrompido após 24 dias no Azure HDInsight
description: Um aplicativo Apache Spark streaming é interrompido após a execução por 24 dias e não há erros nos arquivos de log.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 83bdb4a9913ae817204fb37320f5bdb8174d5baf
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93288024"
---
# <a name="scenario-apache-spark-streaming-application-stops-after-executing-for-24-days-in-azure-hdinsight"></a>Cenário: o aplicativo de streaming de Apache Spark é interrompido após a execução por 24 dias no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções de problemas ao usar os componentes do Apache Spark nos clusters do Azure HDInsight.

## <a name="issue"></a>Problema

Um aplicativo Apache Spark streaming é interrompido após a execução por 24 dias e não há erros nos arquivos de log.

## <a name="cause"></a>Causa

O `livy.server.session.timeout` valor controla por quanto tempo o Apache Livy deve aguardar a conclusão de uma sessão. Depois que o comprimento da sessão atingir o `session.timeout` valor, a sessão Livy e o aplicativo serão automaticamente eliminados.

## <a name="resolution"></a>Resolução

Para trabalhos de longa execução, aumente o valor de `livy.server.session.timeout` usando a interface do usuário do amAmbari. Você pode acessar a configuração Livy da interface do usuário do Ambari usando a URL `https://<yourclustername>.azurehdinsight.net/#/main/services/LIVY/configs` .

Substitua `<yourclustername>` pelo nome do seu cluster HDInsight, conforme mostrado no Portal.

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]