---
title: Depurar operações de arquivo WASB no Azure HDInsight
description: Descreve etapas de solução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/18/2020
ms.openlocfilehash: f1707c7f8d6324678c8bf5a470bbded1e58c719e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77470710"
---
# <a name="debug-wasb-file-operations-in-azure-hdinsight"></a>Depurar operações de arquivo WASB no Azure HDInsight

Há momentos em que você pode querer entender quais operações o driver WASB começou com o Azure Storage. Para o lado do cliente, o driver WASB produz logs para cada operação do sistema de arquivos no nível **DEBUG.** O driver WASB usa log4j para controlar o nível de registro e o padrão é o nível **DE INFO.** Para registros de análise do lado do servidor do Azure Storage, consulte [o registro de análise saquedo do Azure Storage](../../storage/common/storage-analytics-logging.md).

Um registro produzido será semelhante a:

```log
18/05/13 04:15:55 DEBUG NativeAzureFileSystem: Moving wasb://xxx@yyy.blob.core.windows.net/user/livy/ulysses.txt/_temporary/0/_temporary/attempt_20180513041552_0000_m_000000_0/part-00000 to wasb://xxx@yyy.blob.core.windows.net/user/livy/ulysses.txt/part-00000
```

## <a name="turn-on-wasb-debug-log-for-file-operations"></a>Ativar registro de depuração WASB para operações de arquivos

1. A partir de um `https://CLUSTERNAME.azurehdinsight.net/#/main/services/SPARK2/configs`navegador `CLUSTERNAME` da Web, navegue até , onde está o nome do seu cluster Spark.

1. Navegue até **propriedades avançadas de spark2 log4j**.

    1. Modificar `log4j.appender.console.Threshold=INFO` `log4j.appender.console.Threshold=DEBUG`para .

    1. Adicione `log4j.logger.org.apache.hadoop.fs.azure.NativeAzureFileSystem=DEBUG`.

1. Navegue até **propriedades avançadas de livy2 log4j**.

    Adicione `log4j.logger.org.apache.hadoop.fs.azure.NativeAzureFileSystem=DEBUG`.

1. Salve as alterações.

## <a name="additional-logging"></a>Registro em log adicional

Os registros acima devem fornecer uma compreensão de alto nível das operações do sistema de arquivos. Se os registros acima ainda não estiverem fornecendo informações úteis ou se `fs.azure.storage.client.logging=true` você `core-site`quiser investigar chamadas de api de armazenamento blob, adicione ao . Esta configuração habilitará os logs java sdk para driver de armazenamento wasb e imprimirá cada chamada para o servidor de armazenamento blob. Remova a configuração após investigações porque ela poderia encher o disco rapidamente e poderia retardar o processo.

Se o backend for baseado no Azure Data Lake, use a seguinte configuração log4j para o componente (por exemplo, faísca/tez/hdfs):

```
log4j.logger.com.microsoft.azure.datalake.store=ALL,adlsFile
log4j.additivity.com.microsoft.azure.datalake.store=true
log4j.appender.adlsFile=org.apache.log4j.FileAppender
log4j.appender.adlsFile.File=/var/log/adl/adl.log
log4j.appender.adlsFile.layout=org.apache.log4j.PatternLayout
log4j.appender.adlsFile.layout.ConversionPattern=%p\t%d{ISO8601}\t%r\t%c\t[%t]\t%m%n
```

Procure os registros `/var/log/adl/adl.log` dos registros.

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure através [do Azure Community Support](https://azure.microsoft.com/support/community/).

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Conectando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se você precisar de mais ajuda, você pode enviar uma solicitação de suporte do [portal Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **Suporte** na barra de menus ou abra o centro **de suporte Ajuda +.** Para obter informações mais [detalhadas, consulte Como criar uma solicitação de suporte ao Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e suporte ao faturamento está incluído na assinatura do Microsoft Azure, e o suporte técnico é fornecido através de um dos Planos de Suporte do [Azure](https://azure.microsoft.com/support/plans/).
