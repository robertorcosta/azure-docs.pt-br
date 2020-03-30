---
title: Desempenho ruim em consultas apache hive llap no Azure HDInsight
description: As consultas no Apache Hive LLAP estão sendo executadas mais devagar do que o esperado no Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 8bd20849b15f8c8d5a14653f702f78c6404d82e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895134"
---
# <a name="scenario-poor-performance-in-apache-hive-llap-queries-in-azure-hdinsight"></a>Cenário: Baixo desempenho em consultas do Apache Hive LLAP no Azure HDInsight

Este artigo descreve etapas de solução de problemas e possíveis resoluções para problemas ao usar componentes de Consulta Interativa em clusters Azure HDInsight.

## <a name="issue"></a>Problema

As configurações de cluster padrão não estão suficientemente sintonizadas para sua carga de trabalho. As consultas em Hive LLAP estão sendo executadas mais devagar do que o esperado.

## <a name="cause"></a>Causa

Isso pode acontecer devido a uma variedade de razões.

## <a name="resolution"></a>Resolução

O LLAP é otimizado para consultas que envolvem adesões e agregados. Consultas como as seguintes não funcionam bem em um cluster de Colmeia Interativa:

```
select * from table where column = "columnvalue"
```

Para melhorar o desempenho da consulta de pontos no HIVE LLAP, defina as seguintes configurações:

```
hive.llap.io.enabled=false; (disable LLAP IO)
hive.optimize.index.filter=false; (disable ORC row index)
hive.exec.orc.split.strategy=BI; (to avoid recombining splits)
```

Você também pode aumentar o uso do cache LLAP para melhorar o desempenho com a seguinte alteração de configuração:

```
hive.fetch.task.conversion=none
```

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure através [do Azure Community Support](https://azure.microsoft.com/support/community/).

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente conectando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se você precisar de mais ajuda, você pode enviar uma solicitação de suporte do [portal Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **Suporte** na barra de menus ou abra o centro **de suporte Ajuda +.** Para obter informações mais detalhadas, consulte [Como criar uma solicitação de suporte ao Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e suporte ao faturamento está incluído na assinatura do Microsoft Azure, e o suporte técnico é fornecido através de um dos Planos de Suporte do [Azure](https://azure.microsoft.com/support/plans/).
