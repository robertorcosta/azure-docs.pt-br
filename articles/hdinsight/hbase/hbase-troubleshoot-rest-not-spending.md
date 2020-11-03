---
title: O Apache HBase REST não está respondendo a solicitações no Azure HDInsight
description: Resolver o problema com o Apache HBase REST não respondendo a solicitações no Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/01/2019
ms.openlocfilehash: c4a0ef82b951fa43eb4c58050d3148fd2d695026
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93286976"
---
# <a name="scenario-apache-hbase-rest-not-responding-to-requests-in-azure-hdinsight"></a>Cenário: o Apache HBase REST não está respondendo a solicitações no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao interagir com clusters do Azure HDInsight.

## <a name="issue"></a>Problema

O serviço REST do Apache HBase não responde às solicitações no Azure HDInsight.

## <a name="cause"></a>Causa

A causa possível aqui pode ser o serviço REST do Apache HBase está vazando soquetes, o que é especialmente comum quando o serviço está em execução por um longo período (por exemplo, meses). No SDK do cliente, você pode ver uma mensagem de erro semelhante a:

```
System.Net.WebException : Unable to connect to the remote server --->
System.Net.Sockets.SocketException : A connection attempt failed because the connected party did not properly respond after a period of time, or established connection failed because connected host has failed to respond 10.0.0.19:8090
```

## <a name="resolution"></a>Resolução

Reinicie o HBase REST usando o comando abaixo após SSHing para o host. Você também pode usar ações de script para reiniciar esse serviço em todos os nós de trabalho:

```bash
sudo service hdinsight-hbrest restart
```

Esse comando irá parar o servidor de região do HBase no mesmo host. Você pode iniciar manualmente o servidor de região do HBase por meio de Ambari ou deixar a funcionalidade de reinicialização automática do Ambari recuperar o servidor de região do HBase automaticamente.

Se o problema ainda persistir, você poderá instalar o script de mitigação a seguir como um trabalho CRON que é executado a cada 5 minutos em cada nó de trabalho. Esse script de mitigação executa ping no serviço REST e o reinicia caso o serviço REST não responda.

```bash
#!/bin/bash
nc localhost 8090 < /dev/null
if [ $? -ne 0 ]
    then
    echo "RESTServer is not responding. Restarting"
    sudo /usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh restart rest
fi
```

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]