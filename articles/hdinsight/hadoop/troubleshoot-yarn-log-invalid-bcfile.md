---
title: Não é possível ler o log do Apache yarn no Azure HDInsight
description: Etapas de solução de problemas e possíveis resoluções para problemas ao interagir com clusters do Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: f7861ae4980f13fbd87780ab2d26fa3376f33532
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "76776190"
---
# <a name="scenario-unable-to-read-apache-yarn-log-in-azure-hdinsight"></a>Cenário: não é possível ler o log do Apache yarn no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao interagir com clusters do Azure HDInsight.

## <a name="issue"></a>Problema

Os logs do Apache yarn encontrados na conta de armazenamento não são legíveis. O analisador de arquivo não funciona e produz a seguinte mensagem de erro:

```
java.io.IOException: Not a valid BCFile.
```

## <a name="cause"></a>Causa

O log do Apache yarn é agregado no `IndexFile` formato, o que não é suportado pelo analisador de arquivo.

## <a name="resolution"></a>Resolução

1. Em um navegador da Web, navegue até `https://CLUSTERNAME.azurehdinsight.net`, em que `CLUSTERNAME` é o nome do cluster.

1. Na interface do usuário do amAmbari, navegue até **yarn**  >  **configurações**  >  **avançadas**avançado  >  **yarn-site**.

1. Para o armazenamento WASB: o valor padrão para `yarn.log-aggregation.file-formats` é `IndexedFormat,TFile` . Altere o valor para `TFile` .

1. Para o armazenamento ADLS: o valor padrão para `yarn.nodemanager.log-aggregation.compression-type` é `gz` . Altere o valor para `none` .

1. Salve a alteração e reinicie todos os serviços afetados.

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio do [Suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport), a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Como se conectar à comunidade do Azure para os recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **Suporte** na barra de menus ou abra o hub **Ajuda + suporte**. Para obter informações mais detalhadas, consulte [Como criar uma solicitação de Suporte do Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). O acesso ao Gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [Planos de suporte do Azure](https://azure.microsoft.com/support/plans/).
