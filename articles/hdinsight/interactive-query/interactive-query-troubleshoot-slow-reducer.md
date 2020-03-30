---
title: Redutor é lento no Azure HDInsight
description: O redutor é lento no Azure HDInsight a partir de possíveis distorções de dados
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 8a9c7ed9f6b5b8ec89bfca6dd59034b11f05f9a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895156"
---
# <a name="scenario-reducer-is-slow-in-azure-hdinsight"></a>Cenário: Redutor é lento no Azure HDInsight

Este artigo descreve etapas de solução de problemas e possíveis resoluções para problemas ao usar componentes de Consulta Interativa em clusters Azure HDInsight.

## <a name="issue"></a>Problema

Ao executar uma consulta, como `insert into table1 partition(a,b) select a,b,c from table2` o plano de consulta, inicia um monte de redutores, mas os dados de cada partição vão para um único redutor. Isso faz com que a consulta seja tão lenta quanto o tempo demorado pelo redutor da maior partição.

## <a name="cause"></a>Causa

Abra [a linha beeline](../hadoop/apache-hadoop-use-hive-beeline.md) `hive.optimize.sort.dynamic.partition`e verifique o valor do conjunto .

O valor desta variável deve ser definido como verdadeiro/falso com base na natureza dos dados.

Se as partições na tabela de entrada forem menores (digamos menos de 10), e assim `true`for o número de partições de saída, e a variável for definida para , isso faz com que os dados sejam classificados globalmente e gravados usando um único redutor por partição. Mesmo que o número de redutores disponíveis seja maior, alguns redutores podem estar atrasados devido à distorção de dados e o paralelismo máximo não pode ser alcançado. Quando alterado `false`para , mais de um redutor pode lidar com uma única partição e vários arquivos menores serão excluídos, resultando em inserção mais rápida. Isso pode afetar mais consultas, porém, devido à presença de arquivos menores.

Um valor `true` de faz sentido quando o número de partições é maior e os dados não são distorcidos. Nesses casos, o resultado da fase do mapa será escrito de forma que cada partição será tratada por um único redutor, resultando em um melhor desempenho de consulta subseqüente.

## <a name="resolution"></a>Resolução

1. Tente reparticionar os dados para normalizar em várias partições.

1. Se #1 não for possível, defina o valor da configuração como falso na sessão beeline e tente a consulta novamente. `set hive.optimize.sort.dynamic.partition=false`. Não é recomendado definir o valor como falso em um nível de cluster. O valor `true` é ótimo e defina o parâmetro conforme necessário com base na natureza dos dados e consultas.

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure através [do Azure Community Support](https://azure.microsoft.com/support/community/).

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente conectando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se você precisar de mais ajuda, você pode enviar uma solicitação de suporte do [portal Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **Suporte** na barra de menus ou abra o centro **de suporte Ajuda +.** Para obter informações mais detalhadas, consulte [Como criar uma solicitação de suporte ao Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e suporte ao faturamento está incluído na assinatura do Microsoft Azure, e o suporte técnico é fornecido através de um dos Planos de Suporte do [Azure](https://azure.microsoft.com/support/plans/).
