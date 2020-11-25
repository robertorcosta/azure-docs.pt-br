---
title: Não é possível adicionar nós ao cluster HDInsight do Azure
description: Solucionar problemas por que não é possível adicionar nós ao cluster Apache Hadoop no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: 089eab563c83384bdb7e1681044fa89f5eb7f6c0
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96014662"
---
# <a name="scenario-unable-to-add-nodes-to-azure-hdinsight-cluster"></a>Cenário: não é possível adicionar nós ao cluster HDInsight do Azure

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao interagir com clusters do Azure HDInsight.

## <a name="issue"></a>Problema

Não é possível adicionar nós ao cluster HDInsight do Azure.

## <a name="cause"></a>Causa

Os motivos podem variar.

## <a name="resolution"></a>Resolução

Usando o recurso de [tamanho do cluster](../hdinsight-scaling-best-practices.md) , calcule o número de núcleos adicionais necessários para o cluster. Isso se baseia no número total de núcleos em novos nós de trabalho. Em seguida, tente uma ou mais das seguintes etapas:

* Verifique se há algum núcleo disponível no local do cluster.

* Examine o número de núcleos disponíveis em outros locais. Considere recriar seu cluster em um local diferente com núcleos suficientes disponíveis.

* Se você quiser aumentar a cota de núcleo para um local específico, envie um tíquete de suporte para um aumento de cota de núcleo do HDInsight.

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]