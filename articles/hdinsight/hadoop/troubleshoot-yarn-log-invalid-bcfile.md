---
title: Não é possível ler o login do Apache Yarn no Azure HDInsight
description: Solução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: f7861ae4980f13fbd87780ab2d26fa3376f33532
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76776190"
---
# <a name="scenario-unable-to-read-apache-yarn-log-in-azure-hdinsight"></a>Cenário: Não é possível ler o login do Apache Yarn no Azure HDInsight

Este artigo descreve etapas de solução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="issue"></a>Problema

Os registros apache sarn encontrados na conta de armazenamento não são legíveis por humanos. O analisador de arquivos não funciona e produz a seguinte mensagem de erro:

```
java.io.IOException: Not a valid BCFile.
```

## <a name="cause"></a>Causa

O registro apache yarn `IndexFile` é agregado em formato, que não é suportado pelo analisador de arquivos.

## <a name="resolution"></a>Resolução

1. A partir de um `https://CLUSTERNAME.azurehdinsight.net`navegador `CLUSTERNAME` da Web, navegue até , onde está o nome do seu cluster.

1. Da UI Ambari, navegue até **o yarn** > **Configs** > **Advanced** > **yarn-site**.

1. Para armazenamento WASB: O `yarn.log-aggregation.file-formats` `IndexedFormat,TFile`valor padrão para é . Alterar o `TFile`valor para .

1. Para armazenamento ADLS: O `yarn.nodemanager.log-aggregation.compression-type` `gz`valor padrão para é . Alterar o `none`valor para .

1. Salve a alteração e reinicie todos os serviços afetados.

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure através [do Azure Community Support](https://azure.microsoft.com/support/community/).

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Conectando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se você precisar de mais ajuda, você pode enviar uma solicitação de suporte do [portal Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **Suporte** na barra de menus ou abra o centro **de suporte Ajuda +.** Para obter informações mais [detalhadas, consulte Como criar uma solicitação de suporte ao Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e suporte ao faturamento está incluído na assinatura do Microsoft Azure, e o suporte técnico é fornecido através de um dos Planos de Suporte do [Azure](https://azure.microsoft.com/support/plans/).
