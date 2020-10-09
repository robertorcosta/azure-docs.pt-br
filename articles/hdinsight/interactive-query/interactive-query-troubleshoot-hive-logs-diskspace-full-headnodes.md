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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "78943961"
---
# <a name="scenario-apache-hive-logs-are-filling-up-the-disk-space-on-the-head-nodes-in-azure-hdinsight"></a>Cenário: os logs de Apache Hive estão preenchendo o espaço em disco nos nós de cabeçalho no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas relacionados a espaço em disco insuficiente nos nós de cabeçalho nos clusters do Azure HDInsight.

## <a name="issue"></a>Problema

Em um cluster Apache Hive/LLAP, os logs indesejados estão ocupando todo o espaço em disco nos nós de cabeçalho. Devido a quais, os problemas a seguir podem ser vistos.

1. O acesso SSH falha porque nenhum espaço é deixado no nó principal.
2. Ambari fornece *erro http: serviço 503 indisponível*.

Os `ambari-agent` logs mostrarão o seguinte quando o problema acontecer.
```
ambari_agent - Controller.py - [54697] - Controller - ERROR - Error:[Errno 28] No space left on device
```
```
ambari_agent - HostCheckReportFileHandler.py - [54697] - ambari_agent.HostCheckReportFileHandler - ERROR - Can't write host check file at /var/lib/ambari-agent/data/hostcheck.result
```

## <a name="cause"></a>Causa

Nas configurações avançadas de Hive-Log4J, o parâmetro *Log4J. appender. RFA. MaxBackupIndex* é omitido. Isso causa infinita geração de arquivos de log.

## <a name="resolution"></a>Resolução

1. Navegue até Resumo de componentes do hive no portal do Ambari e clique na `Configs` guia.

2. Vá para a `Advanced hive-log4j` seção em configurações avançadas.

3. Defina `log4j.appender.RFA` o parâmetro como RollingFileAppender. 

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
5. Defina `hive.root.logger` `INFO,RFA` como a seguir. A configuração padrão é DEBUG, que faz com que os logs se tornem muito grandes.

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

* Obtenha respostas de especialistas do Azure por meio do [Suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte-se a [@AzureSupport](https://twitter.com/azuresupport) – a conta oficial do Microsoft Azure para melhorar a experiência do cliente conectando-se à comunidade do Azure para os recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **Suporte** na barra de menus ou abra o hub **Ajuda + suporte**. Para obter informações mais detalhadas, confira [Como criar uma solicitação de suporte do Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso ao Gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [Planos de suporte do Azure](https://azure.microsoft.com/support/plans/).
