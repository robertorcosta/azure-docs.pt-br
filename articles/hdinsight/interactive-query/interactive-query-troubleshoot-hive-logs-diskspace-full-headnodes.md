---
title: Logs de Apache Hive preenchendo espaço em disco-Azure HDInsight
description: Os logs de Apache Hive estão preenchendo o espaço em disco nos nós de cabeçalho no Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: nisgoel
ms.author: nisgoel
ms.reviewer: jasonh
ms.date: 10/05/2020
ms.openlocfilehash: a102c9f375b37579cf6f92b08d67f762d3dfd26a
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92220883"
---
# <a name="scenario-apache-hive-logs-are-filling-up-the-disk-space-on-the-head-nodes-in-azure-hdinsight"></a>Cenário: os logs de Apache Hive estão preenchendo o espaço em disco nos nós de cabeçalho no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas relacionados a espaço em disco insuficiente nos nós de cabeçalho nos clusters do Azure HDInsight.

## <a name="issue"></a>Problema

Em um cluster Apache Hive/LLAP, os logs indesejados estão ocupando todo o espaço em disco nos nós de cabeçalho. Devido a quais, os problemas a seguir podem ser vistos.

1. O acesso SSH falha porque nenhum espaço é deixado no nó principal.
2. Ambari fornece *erro http: serviço 503 indisponível*.
3. Falha na reinicialização do HiveServer2 Interactive.

Os `ambari-agent` logs mostrarão o seguinte quando o problema acontecer.
```
ambari_agent - Controller.py - [54697] - Controller - ERROR - Error:[Errno 28] No space left on device
```
```
ambari_agent - HostCheckReportFileHandler.py - [54697] - ambari_agent.HostCheckReportFileHandler - ERROR - Can't write host check file at /var/lib/ambari-agent/data/hostcheck.result
```

## <a name="cause"></a>Causa

Nas configurações avançadas de Hive-Log4J, a agenda de exclusão padrão atual é definida para arquivos com mais de 30 dias, com base na data da última modificação.

## <a name="resolution"></a>Resolução

1. Navegue até Resumo de componentes do hive no portal do Ambari e clique na `Configs` guia.

2. Vá para a `Advanced hive-log4j` seção em configurações avançadas.

3. Defina `appender.RFA.strategy.action.condition.age` o parâmetro como uma idade de sua escolha. Exemplo por 14 dias: `appender.RFA.strategy.action.condition.age = 14D`

4. Se você não vir nenhuma configuração relacionada, acrescente essas configurações a seguir.
    ```
    # automatically delete hive log
    appender.RFA.strategy.action.type = Delete
    appender.RFA.strategy.action.basePath = ${sys:hive.log.dir}
    appender.RFA.strategy.action.condition.type = IfLastModified
    appender.RFA.strategy.action.condition.age = 30D
    appender.RFA.strategy.action.PathConditions.type = IfFileName
    appender.RFA.strategy.action.PathConditions.regex = hive*.*log.*
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
