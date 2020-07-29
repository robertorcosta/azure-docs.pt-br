---
title: Não é possível adicionar nós ao cluster HDInsight do Azure
description: Solucionar problemas por que não é possível adicionar nós ao cluster Apache Hadoop no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: 97d7f34fff324a9959292460e534c15110c3e532
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "75894090"
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

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio do [Suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte-se a [@AzureSupport](https://twitter.com/azuresupport) – a conta oficial do Microsoft Azure para melhorar a experiência do cliente conectando-se à comunidade do Azure para os recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **Suporte** na barra de menus ou abra o hub **Ajuda + suporte**. Para obter informações mais detalhadas, confira [Como criar uma solicitação de suporte do Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso ao Gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [Planos de suporte do Azure](https://azure.microsoft.com/support/plans/).
