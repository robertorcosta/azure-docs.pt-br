---
title: BindException - Endereço já em uso no Azure HDInsight
description: BindException - Endereço já em uso no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/16/2019
ms.openlocfilehash: 80f984643d6d8be88b381881c6fc1cb1cb5f1815
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887335"
---
# <a name="scenario-bindexception---address-already-in-use-in-azure-hdinsight"></a>Cenário: BindException - Endereço já em uso no Azure HDInsight

Este artigo descreve etapas de solução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="issue"></a>Problema

A operação de reinicialização em um Servidor de Região Apache HBase não é concluída. A `region-server.log` partir `/var/log/hbase` do diretório in nos nós do trabalhador onde o início do servidor da região falha, você pode ver uma mensagem de erro semelhante à seguinte:

```
Caused by: java.net.BindException: Problem binding to /10.2.0.4:16020 : Address already in use
...

Caused by: java.net.BindException: Address already in use
...
```

## <a name="cause"></a>Causa

Reinicialização de servidores da região do Apache HBase durante atividade sumida de carga de trabalho pesada. Abaixo está o que acontece nos bastidores quando um usuário inicia a operação de reinicialização no servidor de região HBase da UI Apache Ambari:

1. O agente Ambari envia uma solicitação de parada para o servidor de região.

1. O agente Ambari espera por 30 segundos para que o servidor da região seja desligado graciosamente

1. Se o seu aplicativo continuar a se conectar com o servidor de região, o servidor não desligará imediatamente. O tempo limite de 30 segundos expira antes do desligamento.

1. Após 30 segundos, o agente do Ambari envia um comando (`kill -9`) de encerramento forçado ao servidor de região.

1. Devido a esse desligamento abrupto, embora o processo do servidor da região seja morto, `AddressBindException`a porta associada ao processo pode não ser liberada, o que eventualmente leva a .

## <a name="resolution"></a>Resolução

Reduza a carga nos servidores da região do HBase antes de começar uma reinicialização. Além disso, convém primeiro liberar todas as tabelas. Para obter uma referência sobre como liberar tabelas, confira [HDInsight HBase: como melhorar o tempo de reinicialização do cluster Apache HBase liberando tabelas](https://web.archive.org/web/20190112153155/https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/).

Alternativamente, tente reiniciar manualmente servidores de região nos nós do trabalhador usando os seguintes comandos:

```bash
sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh stop regionserver"
sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh start regionserver"
```

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure através [do Azure Community Support](https://azure.microsoft.com/support/community/).

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Conectando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se você precisar de mais ajuda, você pode enviar uma solicitação de suporte do [portal Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **Suporte** na barra de menus ou abra o centro **de suporte Ajuda +.** Para obter informações mais [detalhadas, consulte Como criar uma solicitação de suporte ao Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e suporte ao faturamento está incluído na assinatura do Microsoft Azure, e o suporte técnico é fornecido através de um dos Planos de Suporte do [Azure](https://azure.microsoft.com/support/plans/).
