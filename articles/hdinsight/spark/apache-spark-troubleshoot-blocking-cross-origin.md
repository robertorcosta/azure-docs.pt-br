---
title: Erro 404 Jupyter-"bloqueando a API de origem cruzada"-Azure HDInsight
description: Erro do Jupyter Server 404 "não encontrado" devido a "bloqueio da API de origem cruzada" no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 472dde22e5df32e7d2a276b0515a0f1edafe52b3
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/26/2020
ms.locfileid: "92545660"
---
# <a name="scenario-jupyter-server-404-not-found-error-due-to-blocking-cross-origin-api-in-azure-hdinsight"></a>Cenário: erro do Jupyter Server 404 "não encontrado" devido a "bloquear API de origem cruzada" no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções de problemas ao usar os componentes do Apache Spark nos clusters do Azure HDInsight.

## <a name="issue"></a>Problema

Ao acessar o serviço Jupyter no HDInsight, você verá uma caixa de erro dizendo "não encontrado". Se você verificar os logs do Jupyter, verá algo assim:

```log
[W 2018-08-21 17:43:33.352 NotebookApp] 404 PUT /api/contents/PySpark/notebook.ipynb (10.16.0.144) 4504.03ms referer=https://pnhr01hdi-corpdir.msappproxy.net/jupyter/notebooks/PySpark/notebook.ipynb
Blocking Cross Origin API request.  
Origin: https://xxx.xxx.xxx, Host: pnhr01.j101qxjrl4zebmhb0vmhg044xe.ax.internal.cloudapp.net:8001
```

Você também pode ver um endereço IP no campo "origem" no log do Jupyter.

## <a name="cause"></a>Causa

Esse erro pode ser causado por algumas coisas:

- Se você tiver configurado regras de NSG (grupo de segurança de rede) para restringir o acesso ao cluster. Restringir o acesso com regras NSG ainda permitirá que você acesse diretamente o Apache Ambari e outros serviços usando o endereço IP em vez do nome do cluster. No entanto, ao acessar o Jupyter, você pode ver um erro 404 "não encontrado".

- Se você tiver dado ao seu gateway do HDInsight um nome DNS personalizado diferente do padrão `xxx.azurehdinsight.net` .

## <a name="resolution"></a>Resolução

1. Modifique os arquivos jupyter.py nestes dois locais:

    ```bash
    /var/lib/ambari-server/resources/common-services/JUPYTER/1.0.0/package/scripts/jupyter.py
    /var/lib/ambari-agent/cache/common-services/JUPYTER/1.0.0/package/scripts/jupyter.py
    ```

1. Localize a linha que diz: `NotebookApp.allow_origin='\"https://{2}.{3}\"'` e altere-a para: `NotebookApp.allow_origin='\"*\"'` .

1. Reinicie o serviço Jupyter de Ambari.

1. Digitar `ps aux | grep jupyter` no prompt de comando deve mostrar que ele permite que qualquer URL se conecte a ele.

Isso é menos seguro do que a configuração que já tínhamos em vigor. Mas é presumido que o acesso ao cluster seja restrito e que um de fora tenha permissão para se conectar ao cluster, pois temos NSG em vigor.

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio do [Suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte-se a [@AzureSupport](https://twitter.com/azuresupport) – a conta oficial do Microsoft Azure para melhorar a experiência do cliente conectando-se à comunidade do Azure para os recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **Suporte** na barra de menus ou abra o hub **Ajuda + suporte** . Para obter informações mais detalhadas, confira [Como criar uma solicitação de suporte do Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). O acesso ao Gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [Planos de suporte do Azure](https://azure.microsoft.com/support/plans/).