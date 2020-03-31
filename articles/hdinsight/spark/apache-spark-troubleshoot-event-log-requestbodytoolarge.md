---
title: RequestBodyTooGrande erro do aplicativo Apache Spark - Azure HDInsight
description: NativeAzureFileSystem ... RequestBodyTooLarge aparece no log do aplicativo de streaming Apache Spark no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 777d06670238a7625d190c92f78a55cd4794d226
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894393"
---
# <a name="nativeazurefilesystemrequestbodytoolarge-appear-in-apache-spark-streaming-app-log-in-hdinsight"></a>"NativeAzureFileSystem... RequestBodyTooLarge" aparecem no aplicativo de streaming Apache Spark log no HDInsight

Este artigo descreve etapas de solução de problemas e possíveis resoluções para problemas ao usar componentes Apache Spark em clusters Azure HDInsight.

## <a name="issue"></a>Problema

O erro: `NativeAzureFileSystem ... RequestBodyTooLarge` aparece no registro do driver de um aplicativo de streaming Apache Spark.

## <a name="cause"></a>Causa

O arquivo de registro de eventos Spark provavelmente está atingindo o limite de comprimento do arquivo para WASB.

No Spark 2.3, cada aplicativo Spark gera um arquivo de registro de eventos Spark. O arquivo de registro de eventos Spark para um aplicativo de streaming Spark continua a crescer enquanto o aplicativo está em execução. Hoje, um arquivo no WASB tem um limite de bloco de 50000, e o tamanho padrão do bloco é de 4 MB. Assim, na configuração padrão, o tamanho máximo do arquivo é de 195 GB. No entanto, o Azure Storage aumentou o tamanho máximo do bloco para 100 MB, o que efetivamente elevou o limite de arquivo único para 4,75 TB. Para obter mais informações, confira [Escalabilidade e metas de desempenho do Armazenamento de Blobs](../../storage/blobs/scalability-targets.md).

## <a name="resolution"></a>Resolução

Existem três soluções disponíveis para este erro:

* Aumente o tamanho do bloco para até 100 MB. Na Ambari UI, modifique `fs.azure.write.request.size` a propriedade de `Custom core-site` configuração Do HDFS (ou crie-a na seção). Defina a propriedade para um valor maior, por exemplo: 33554432. Salve a configuração atualizada e reinicie os componentes afetados.

* Pare periodicamente e reenvie o trabalho de streaming de faíscas.

* Use o HDFS para armazenar registros de eventos spark. O uso do HDFS para armazenamento pode resultar na perda de dados de eventos spark durante o dimensionamento de clusters ou upgrades do Azure.

    1. Faça alterações para `spark.eventlog.dir` e `spark.history.fs.logDirectory` via Ambari UI:

        ```
        spark.eventlog.dir = hdfs://mycluster/hdp/spark2-events
        spark.history.fs.logDirectory = "hdfs://mycluster/hdp/spark2-events"
        ```

    1. Criar diretórios no HDFS:

        ```
        hadoop fs -mkdir -p hdfs://mycluster/hdp/spark2-events
        hadoop fs -chown -R spark:hadoop hdfs://mycluster/hdp
        hadoop fs -chmod -R 777 hdfs://mycluster/hdp/spark2-events
        hadoop fs -chmod -R o+t hdfs://mycluster/hdp/spark2-events
        ```

    1. Reinicie todos os serviços afetados via Ambari UI.

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure através [do Azure Community Support](https://azure.microsoft.com/support/community/).

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente conectando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se você precisar de mais ajuda, você pode enviar uma solicitação de suporte do [portal Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **Suporte** na barra de menus ou abra o centro **de suporte Ajuda +.** Para obter informações mais detalhadas, consulte [Como criar uma solicitação de suporte ao Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e suporte ao faturamento está incluído na assinatura do Microsoft Azure, e o suporte técnico é fornecido através de um dos Planos de Suporte do [Azure](https://azure.microsoft.com/support/plans/).
