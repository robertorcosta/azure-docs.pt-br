---
title: Apache Hive Logs preenchendo espaço em disco - Azure HDInsight
description: Os registros da Colmeia Apache estão preenchendo o espaço do disco nos nós da cabeça no Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: nisgoel
ms.author: nisgoel
ms.reviewer: jasonh
ms.date: 03/05/2020
ms.openlocfilehash: d843b942702d335065a5f3798572e34c71b4cd0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78943961"
---
# <a name="scenario-apache-hive-logs-are-filling-up-the-disk-space-on-the-head-nodes-in-azure-hdinsight"></a>Cenário: Os registros da Colmeia Apache estão preenchendo o espaço do disco nos nós da cabeça no Azure HDInsight

Este artigo descreve etapas de solução de problemas e possíveis resoluções para problemas relacionados a espaço de disco não suficiente nos nós da cabeça nos clusters Azure HDInsight.

## <a name="issue"></a>Problema

Em um cluster Apache Hive/LLAP, logs indesejados estão ocupando todo o espaço do disco nos nós da cabeça. Devido a isso, questões seguintes podiam ser vistas.

1. O acesso SSH falha por causa de nenhum espaço no nó da cabeça.
2. Ambari dá *HTTP ERROR: 503 Service Indisponível*.

Os `ambari-agent` registros mostrariam o seguinte quando o problema acontecesse.
```
ambari_agent - Controller.py - [54697] - Controller - ERROR - Error:[Errno 28] No space left on device
```
```
ambari_agent - HostCheckReportFileHandler.py - [54697] - ambari_agent.HostCheckReportFileHandler - ERROR - Can't write host check file at /var/lib/ambari-agent/data/hostcheck.result
```

## <a name="cause"></a>Causa

Em configurações avançadas do Hive-log4j, o parâmetro *log4j.appender.RFA.MaxBackupIndex* é omitido. Causa uma geração infinita de arquivos de log.

## <a name="resolution"></a>Resolução

1. Navegue até o resumo do componente Hive no `Configs` portal Ambari e clique na guia.

2. Vá para `Advanced hive-log4j` a seção dentro das configurações avançadas.

3. Definir `log4j.appender.RFA` parâmetro como RollingFileAppender. 

4. Definir `log4j.appender.RFA.MaxFileSize` `log4j.appender.RFA.MaxBackupIndex` e como segue.

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
5. Definir `hive.root.logger` `INFO,RFA` para o seguinte. A configuração padrão é DEBUG, o que faz com que os logs se tornem muito grandes.

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

* Obtenha respostas de especialistas do Azure através [do Azure Community Support](https://azure.microsoft.com/support/community/).

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente conectando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se você precisar de mais ajuda, você pode enviar uma solicitação de suporte do [portal Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **Suporte** na barra de menus ou abra o centro **de suporte Ajuda +.** Para obter informações mais detalhadas, consulte [Como criar uma solicitação de suporte ao Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e suporte ao faturamento está incluído na assinatura do Microsoft Azure, e o suporte técnico é fornecido através de um dos Planos de Suporte do [Azure](https://azure.microsoft.com/support/plans/).
