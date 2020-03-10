---
title: Logs de Apache Hive preenchendo espaço em disco-Azure HDInsight
description: Os logs de Apache Hive estão preenchendo o espaço em disco nos nós de cabeçalho no Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: nisgoel
ms.author: nisgoel
ms.reviewer: jasonh
ms.date: 03/05/2020
ms.openlocfilehash: d843b942702d335065a5f3798572e34c71b4cd0e
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2020
ms.locfileid: "78943961"
---
# <a name="scenario-apache-hive-logs-are-filling-up-the-disk-space-on-the-head-nodes-in-azure-hdinsight"></a>Cenário: os logs de Apache Hive estão preenchendo o espaço em disco nos nós de cabeçalho no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas relacionados a espaço em disco insuficiente nos nós de cabeçalho nos clusters do Azure HDInsight.

## <a name="issue"></a>Problema

Em um cluster Apache Hive/LLAP, os logs indesejados estão ocupando todo o espaço em disco nos nós de cabeçalho. Devido a quais, os problemas a seguir podem ser vistos.

1. O acesso SSH falha porque nenhum espaço é deixado no nó principal.
2. Ambari fornece *erro http: serviço 503 indisponível*.

Os logs de `ambari-agent` mostrarão o seguinte quando o problema acontecer.
```
ambari_agent - Controller.py - [54697] - Controller - ERROR - Error:[Errno 28] No space left on device
```
```
ambari_agent - HostCheckReportFileHandler.py - [54697] - ambari_agent.HostCheckReportFileHandler - ERROR - Can't write host check file at /var/lib/ambari-agent/data/hostcheck.result
```

## <a name="cause"></a>Causa

Nas configurações avançadas de Hive-Log4J, o parâmetro *Log4J. appender. RFA. MaxBackupIndex* é omitido. Isso causa infinita geração de arquivos de log.

## <a name="resolution"></a>Resolução

1. Navegue até Resumo de componentes do hive no portal do Ambari e clique na guia `Configs`.

2. Vá para a seção `Advanced hive-log4j` dentro de configurações avançadas.

3. Defina `log4j.appender.RFA` parâmetro como RollingFileAppender. 

4. Defina `log4j.appender.RFA.MaxFileSize` e `log4j.appender.RFA.MaxBackupIndex` da seguinte maneira.

```
log4jhive.log.maxfilesize=1024MB
log4jhive.log.maxbackupindex=10

log4j.appender.RFA=org.apache.log4j.RollingFileAppender
log4j.appender.RFA.File=${hive.log.dir}/${hive.log.file}
log4j.appender.RFA.MaxFileSize=${log4jhive.log.maxfilesize}
log4j.appender.RFA.MaxBackupIndex=${log4jhive.log.maxbackupindex}
log4j.appender.RFA.layout=org.apache.log4j.PatternLayout
log4j.appender.RFA.layout.ConversionPattern=%d{ISO8601} %-5p [%t] %c{2}: %m%n
```
5. Defina `hive.root.logger` como `INFO,RFA` da seguinte maneira. A configuração padrão é DEBUG, que faz com que os logs se tornem muito grandes.

```
# Define some default values that can be overridden by system properties
hive.log.threshold=ALL
hive.root.logger=INFO,RFA
hive.log.dir=${java.io.tmpdir}/${user.name}
hive.log.file=hive.log
```

6. Salve as configurações e reinicie os componentes necessários.

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio do [suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte-se com o [@AzureSupport](https://twitter.com/azuresupport) -a conta de Microsoft Azure oficial para melhorar a experiência do cliente conectando a Comunidade do Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, você poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **suporte** na barra de menus ou abra o Hub **ajuda + suporte** . Para obter informações mais detalhadas, consulte [como criar uma solicitação de suporte do Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [planos de suporte do Azure](https://azure.microsoft.com/support/plans/).
