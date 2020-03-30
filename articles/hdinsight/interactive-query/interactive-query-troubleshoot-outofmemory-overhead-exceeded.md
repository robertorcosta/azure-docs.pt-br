---
title: A adesão à Colmeia Apache leva ao erro OutOfMemory - Azure HDInsight
description: Lidando com erros de OutOfMemory "Limite de sobrecarga gc excedido erro"
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: ab334dfb15044fd0734a107c12003ca2c1f86906
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895180"
---
# <a name="scenario-joins-in-apache-hive-leads-to-an-outofmemory-error-in-azure-hdinsight"></a>Cenário: A adesão à Colmeia Apache leva a um erro outofmemory no Azure HDInsight

Este artigo descreve etapas de solução de problemas e possíveis resoluções para problemas ao usar componentes de Consulta Interativa em clusters Azure HDInsight.

## <a name="issue"></a>Problema

O comportamento padrão para as junções da Colmeia Apache é carregar todo o conteúdo de uma tabela na memória para que uma junta possa ser realizada sem ter que executar um passo Mapa/Reduzir. Se a tabela Colmeia for muito grande para caber na memória, a consulta pode falhar.

## <a name="cause"></a>Causa

Ao executar as junções na colmeia de tamanho suficiente, encontra-se o seguinte erro:

```
Caused by: java.lang.OutOfMemoryError: GC overhead limit exceeded error.
```

## <a name="resolution"></a>Resolução

Evite que a Colmeia insera as tabelas na memória em partes (em vez de executar um passo Mapa/Reduzir) definindo o seguinte valor de configuração da Colmeia:

```
hive.auto.convert.join=false
```

## <a name="next-steps"></a>Próximas etapas

Se a definição desse valor não resolveu seu problema, visite um dos seguintes...

* Obtenha respostas de especialistas do Azure através [do Azure Community Support](https://azure.microsoft.com/support/community/).

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente conectando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se você precisar de mais ajuda, você pode enviar uma solicitação de suporte do [portal Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **Suporte** na barra de menus ou abra o centro **de suporte Ajuda +.** Para obter informações mais detalhadas, consulte [Como criar uma solicitação de suporte ao Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e suporte ao faturamento está incluído na assinatura do Microsoft Azure, e o suporte técnico é fornecido através de um dos Planos de Suporte do [Azure](https://azure.microsoft.com/support/plans/).
