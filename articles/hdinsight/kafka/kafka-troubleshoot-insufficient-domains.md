---
title: Não há domínios de falha suficientes no erro de região no Azure HDInsight
description: Falha na criação do cluster devido a domínios de falha insuficientes na região no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/09/2019
ms.openlocfilehash: 8be7e05ac85ce0b1b10edf18d3885a07e016b9ae
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "75895026"
---
# <a name="scenario-cluster-creation-failed-due-to-not-sufficient-fault-domains-in-region-in-azure-hdinsight"></a>Cenário: falha na criação do cluster devido ao `not sufficient fault domains in region` Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao interagir com clusters do Azure HDInsight.

## <a name="issue"></a>Problema

Receber mensagem de erro semelhante a `not sufficient fault domains in region` ao tentar criar Apache Kafka cluster.

## <a name="cause"></a>Causa

Um domínio de falha é um agrupamento lógico de hardware subjacente em um data center do Azure. Cada domínio de falha tem um comutador de rede e uma fonte de alimentação em comum. As máquinas virtuais e os discos gerenciados que implementam os nós em um cluster HDInsight são distribuídos entre esses domínios de falha. Essa arquitetura limita o possível impacto de falhas físicas de hardware.

Cada região do Azure tem um número específico de domínios de falha. Para obter uma lista de domínios e o número de domínios de falha que eles contêm, consulte a documentação sobre [conjuntos de disponibilidade](../../virtual-machines/windows/manage-availability.md).

No HDInsight, os clusters Kafka devem ser provisionados em uma região com pelo menos três domínios de falha.

## <a name="resolution"></a>Resolução

Se a região em que você deseja criar o cluster não tiver domínios de falha suficientes, entre em contato com a equipe de produto para permitir o provisionamento do cluster, mesmo se não houver três domínios de falha.

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio do [Suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport), a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Como se conectar à comunidade do Azure para os recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **Suporte** na barra de menus ou abra o hub **Ajuda + suporte**. Para obter informações mais detalhadas, consulte [Como criar uma solicitação de Suporte do Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso ao Gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [Planos de suporte do Azure](https://azure.microsoft.com/support/plans/).
