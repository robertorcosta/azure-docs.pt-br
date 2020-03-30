---
title: Exceção de armazenamento após redefinição de conexão no Azure HDInsight
description: Exceção de armazenamento após redefinição de conexão no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/08/2019
ms.openlocfilehash: a7af6407191577112f936bfb9048985e85c868ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887216"
---
# <a name="scenario-storage-exception-after-connection-reset-in-azure-hdinsight"></a>Cenário: Exceção de armazenamento após redefinição de conexão no Azure HDInsight

Este artigo descreve etapas de solução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="issue"></a>Problema

Não é possível criar uma nova tabela Apache HBase.

## <a name="cause"></a>Causa

Durante um processo de truncamento de tabela, houve um problema de conexão de armazenamento. A entrada da tabela foi excluída na tabela de metadados HBase. Todos, exceto um arquivo blob foi excluído.

Embora não houvesse nenhuma `/hbase/data/default/ThatTable` bolha de pasta chamada sentada no armazenamento. O driver WASB encontrou a existência do arquivo blob acima e `/hbase/data/default/ThatTable` não permitiria criar qualquer bolha chamada porque assumiu que as pastas-pai existiam, criando assim tabela falhará.

## <a name="resolution"></a>Resolução

1. A partir de Apache Ambari UI, reinicie o HMaster ativo. Isso permitirá que um dos dois HMaster em espera se torne o ativo e o novo HMaster ativo recarregará as informações da tabela de metadados. Assim, você não `already-deleted` verá a tabela em HMaster UI.

1. Você pode encontrar o arquivo blob órfão de ferramentas `hdfs dfs -ls /xxxxxx/yyyyy`de ui como Cloud Explorer ou executando comando como . Corra `hdfs dfs -rmr /xxxxx/yyyy` para apagar essa bolha. Por exemplo, `hdfs dfs -rmr /hbase/data/default/ThatTable/ThatFile`.

Agora você pode criar uma nova tabela com o mesmo nome no HBase.

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure através [do Azure Community Support](https://azure.microsoft.com/support/community/).

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Conectando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se você precisar de mais ajuda, você pode enviar uma solicitação de suporte do [portal Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **Suporte** na barra de menus ou abra o centro **de suporte Ajuda +.** Para obter informações mais [detalhadas, consulte Como criar uma solicitação de suporte ao Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e suporte ao faturamento está incluído na assinatura do Microsoft Azure, e o suporte técnico é fornecido através de um dos Planos de Suporte do [Azure](https://azure.microsoft.com/support/plans/).
