---
title: Apache Spark lento quando o armazenamento Azure HDInsight tem muitos arquivos
description: O trabalho do Apache Spark é executado lentamente quando o contêiner de armazenamento Azure contém muitos arquivos no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/21/2019
ms.openlocfilehash: e389c05a6de85287bc86eff510e137f470837e56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894338"
---
# <a name="apache-spark-job-run-slowly-when-the-azure-storage-container-contains-many-files-in-azure-hdinsight"></a>Os trabalhos do Apache Spark são executados lentamente quando o contêiner de armazenamento do Azure contém muitos arquivos no Azure HDInsight

Este artigo descreve etapas de solução de problemas e possíveis resoluções para problemas ao usar componentes Apache Spark em clusters Azure HDInsight.

## <a name="issue"></a>Problema

Ao executar um cluster HDInsight, o trabalho do Apache Spark que grava no contêiner de armazenamento Do Zure torna-se lento quando há muitos arquivos/subpastas. Por exemplo, leva 20 segundos para escrever em um novo recipiente, mas cerca de 2 minutos ao escrever em um recipiente que tem 200 mil arquivos.

## <a name="cause"></a>Causa

Este é um problema conhecido da Faísca. A lentidão `ListBlob` vem `GetBlobProperties` das operações durante a execução do trabalho da Spark.

Para rastrear partições, a `FileStatusCache` Spark tem que manter uma que contenha informações sobre a estrutura do diretório. Usando este cache, o Spark pode analisar os caminhos e estar ciente das partições disponíveis. A vantagem de rastrear partições é que o Spark só toca nos arquivos necessários quando você lê dados. Para manter essas informações atualizadas, quando você escreve novos dados, a Spark tem que listar todos os arquivos sob o diretório e atualizar esse cache.

No Spark 2.1, enquanto não precisamos atualizar o cache após cada gravação, a Spark verificará se uma coluna de partição existente corresponde à proposta na solicitação de gravação atual, de modo que também levará a operações de listagem no início de cada gravação.

No Spark 2.2, ao escrever dados com o modo apêndice, esse problema de desempenho deve ser corrigido.

## <a name="resolution"></a>Resolução

Quando você cria um conjunto de dados particionado, é importante usar um esquema de `FileStatusCache`particionamento que limitará o número de arquivos que o Spark tem que listar para atualizar o .

Para cada micro lote nétero onde N % 100 == 0 (100 é apenas um exemplo), mova os dados existentes para outro diretório, que pode ser carregado pela Spark.

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure através [do Azure Community Support](https://azure.microsoft.com/support/community/).

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente conectando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se você precisar de mais ajuda, você pode enviar uma solicitação de suporte do [portal Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **Suporte** na barra de menus ou abra o centro **de suporte Ajuda +.** Para obter informações mais detalhadas, consulte [Como criar uma solicitação de suporte ao Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e suporte ao faturamento está incluído na assinatura do Microsoft Azure, e o suporte técnico é fornecido através de um dos Planos de Suporte do [Azure](https://azure.microsoft.com/support/plans/).
