---
title: Não é possível adicionar nomes ao cluster Azure HDInsight
description: Solução de problemas por que não é possível adicionar nomes ao cluster Apache Hadoop no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: 97d7f34fff324a9959292460e534c15110c3e532
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894090"
---
# <a name="scenario-unable-to-add-nodes-to-azure-hdinsight-cluster"></a>Cenário: Não é possível adicionar nomes ao cluster Azure HDInsight

Este artigo descreve etapas de solução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="issue"></a>Problema

Não é possível adicionar nomes ao cluster Azure HDInsight.

## <a name="cause"></a>Causa

As razões podem variar.

## <a name="resolution"></a>Resolução

Usando o recurso [tamanho do cluster,](../hdinsight-scaling-best-practices.md) calcule o número de núcleos adicionais necessários para o cluster. Isso se baseia no número total de núcleos em novos nós de trabalho. Em seguida, tente uma ou mais das seguintes etapas:

* Verifique se há algum núcleo disponível na localização do cluster.

* Examine o número de núcleos disponíveis em outros locais. Considere recriar seu cluster em um local diferente com núcleos suficientes disponíveis.

* Se você quiser aumentar a cota de núcleo para um local específico, envie um tíquete de suporte para um aumento de cota de núcleo do HDInsight.

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure através [do Azure Community Support](https://azure.microsoft.com/support/community/).

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente conectando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se você precisar de mais ajuda, você pode enviar uma solicitação de suporte do [portal Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **Suporte** na barra de menus ou abra o centro **de suporte Ajuda +.** Para obter informações mais detalhadas, consulte [Como criar uma solicitação de suporte ao Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e suporte ao faturamento está incluído na assinatura do Microsoft Azure, e o suporte técnico é fornecido através de um dos Planos de Suporte do [Azure](https://azure.microsoft.com/support/plans/).
