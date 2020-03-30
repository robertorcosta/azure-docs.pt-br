---
title: Erro jupyter 404 - "Blocking Cross Origin API" - Azure HDInsight
description: Jupyter server 404 erro "não encontrado" devido ao "Bloqueio da API de Origem Cruzada" no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: e241657186582955d21981f7dfe18856724aa692
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894422"
---
# <a name="scenario-jupyter-server-404-not-found-error-due-to-blocking-cross-origin-api-in-azure-hdinsight"></a>Cenário: Jupyter server 404 erro "não encontrado" devido à "API de bloqueio de origem cruzada" no Azure HDInsight

Este artigo descreve etapas de solução de problemas e possíveis resoluções para problemas ao usar componentes Apache Spark em clusters Azure HDInsight.

## <a name="issue"></a>Problema

Quando você acessa o serviço Jupyter no HDInsight, você vê uma caixa de erro dizendo "Não encontrado". Se você verificar os registros do Jupyter, verá algo assim:

```log
[W 2018-08-21 17:43:33.352 NotebookApp] 404 PUT /api/contents/PySpark/notebook.ipynb (10.16.0.144) 4504.03ms referer=https://pnhr01hdi-corpdir.msappproxy.net/jupyter/notebooks/PySpark/notebook.ipynb
Blocking Cross Origin API request.  
Origin: https://xxx.xxx.xxx, Host: pnhr01.j101qxjrl4zebmhb0vmhg044xe.ax.internal.cloudapp.net:8001
```

Você também pode ver um endereço IP no campo "Origem" no registro Jupyter.

## <a name="cause"></a>Causa

Este erro pode ser causado por algumas coisas:

- Se você configurou as regras do Grupo de Segurança de Rede (NSG) para restringir o acesso ao cluster. Restringir o acesso às regras do NSG ainda permitirá que você acesse diretamente o Apache Ambari e outros serviços usando o endereço IP em vez do nome do cluster. No entanto, ao acessar o Jupyter, você pode ver um erro 404 "Não Encontrado".

- Se você deu ao seu gateway HDInsight um nome `xxx.azurehdinsight.net`DNS personalizado diferente do padrão .

## <a name="resolution"></a>Resolução

1. Modifique os arquivos jupyter.py nesses dois lugares:

    ```bash
    /var/lib/ambari-server/resources/common-services/JUPYTER/1.0.0/package/scripts/jupyter.py
    /var/lib/ambari-agent/cache/common-services/JUPYTER/1.0.0/package/scripts/jupyter.py
    ```

1. Encontre a linha `NotebookApp.allow_origin='\"https://{2}.{3}\"'` que diz: `NotebookApp.allow_origin='\"*\"'`E mude para: .

1. Reinicie o serviço Jupyter de Ambari.

1. Digitar `ps aux | grep jupyter` no prompt de comando deve mostrar que ele permite que qualquer URL se conecte a ele.

Este é um ambiente menos seguro do que o que já tínhamos no lugar. Mas presume-se que o acesso ao cluster é restrito e que um de fora é permitido conectar-se ao cluster como temos NSG no lugar.

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure através [do Azure Community Support](https://azure.microsoft.com/support/community/).

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente conectando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se você precisar de mais ajuda, você pode enviar uma solicitação de suporte do [portal Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **Suporte** na barra de menus ou abra o centro **de suporte Ajuda +.** Para obter informações mais detalhadas, consulte [Como criar uma solicitação de suporte ao Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e suporte ao faturamento está incluído na assinatura do Microsoft Azure, e o suporte técnico é fornecido através de um dos Planos de Suporte do [Azure](https://azure.microsoft.com/support/plans/).
