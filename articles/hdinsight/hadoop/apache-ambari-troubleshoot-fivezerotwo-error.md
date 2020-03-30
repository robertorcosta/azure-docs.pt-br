---
title: Erro do Apache Ambari UI 502 no Azure HDInsight
description: Erro do Apache Ambari UI 502 ao tentar acessar seu cluster Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/05/2019
ms.openlocfilehash: 2b17c2488e47148e8845433f9c7613e1127fbffa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895751"
---
# <a name="scenario-apache-ambari-ui-502-error-in-azure-hdinsight"></a>Cenário: Erro apache Ambari UI 502 no Azure HDInsight

Este artigo descreve etapas de solução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="issue"></a>Problema

Quando você tenta acessar a UI Apache Ambari para o seu cluster HDInsight, você recebe uma mensagem semelhante a: "502 - O servidor da Web recebeu uma resposta inválida enquanto atuava como um gateway ou servidor proxy."

## <a name="cause"></a>Causa

Em geral, o código de status HTTP 502 significa que o servidor Ambari não está sendo executado corretamente no headnode ativo. Há algumas causas possíveis.

## <a name="resolution"></a>Resolução

Na maioria dos casos, para mitigar o problema, você pode reiniciar o cabeçada ativo. Ou escolha um tamanho vm maior para o seu nó de cabeça.

### <a name="ambari-server-failed-to-start"></a>O servidor Ambari não conseguiu iniciar

Você pode verificar os logs do servidor ambari para descobrir por que o servidor Ambari falhou em iniciar. Uma razão comum é o erro de verificação da consistência do banco de dados. Você pode descobrir isso neste `/var/log/ambari-server/ambari-server-check-database.log`arquivo de log: .

Se você fez alguma modificação no nó do cluster, por favor, desfaça-os. Use sempre a Ambari UI para modificar quaisquer configurações relacionadas ao Hadoop/Spark.

### <a name="ambari-server-taking-100-cpu-utilization"></a>Servidor Ambari tendo 100% de utilização da CPU

Em situações raras, vimos que o processo ambari-servidor tem cerca de 100% de utilização da CPU constantemente. Como atenuação, você pode ssh para o headnode ativo, e matar o processo de servidor Ambari e reiniciá-lo novamente.

```bash
ps -ef | grep AmbariServer
top -p <ambari-server-pid>
kill -9 <ambari-server-pid>
service ambari-server start
```

### <a name="ambari-server-killed-by-oom-killer"></a>Servidor Ambari morto por assassino de oom

Em alguns cenários, seu cabeçanode fica sem memória, e o assassino de oom-linux começa a escolher processos para matar. Você pode verificar essa situação pesquisando o ID do processo AmbariServer, que não deve ser encontrado. Então olhe `/var/log/syslog`para o seu , e olhar para algo assim:

```
Jul 27 15:29:30 xxx-xxxxxx kernel: [874192.703153] java invoked oom-killer: gfp_mask=0x23201ca, order=0, oom_score_adj=0
```

Em seguida, identifique quais processos estão tomando memórias e tente criar mais raízes.

### <a name="other-issues-with-ambari-server"></a>Outros problemas com o servidor Ambari

Raramente o servidor Ambari não consegue lidar com a solicitação recebida, você pode encontrar mais informações olhando os logs do servidor ambari para qualquer erro. Um desses casos é um erro como este:

```
Error Processing URI: /api/v1/clusters/xxxxxx/host_components - (java.lang.OutOfMemoryError) Java heap space
```

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure através [do Azure Community Support](https://azure.microsoft.com/support/community/).

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente conectando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se você precisar de mais ajuda, você pode enviar uma solicitação de suporte do [portal Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **Suporte** na barra de menus ou abra o centro **de suporte Ajuda +.** Para obter informações mais detalhadas, consulte [Como criar uma solicitação de suporte ao Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e suporte ao faturamento está incluído na assinatura do Microsoft Azure, e o suporte técnico é fornecido através de um dos Planos de Suporte do [Azure](https://azure.microsoft.com/support/plans/).
