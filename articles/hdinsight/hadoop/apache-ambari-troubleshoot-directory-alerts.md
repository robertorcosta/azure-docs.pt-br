---
title: Alertas de diretório apache ambari no Azure HDInsight
description: Discussão e análise de possíveis razões e soluções para alertas de diretório Apache Ambari no HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: 7fd287377a82caeaecea264f0165d12ced57f5cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76722831"
---
# <a name="scenario-apache-ambari-directory-alerts-in-azure-hdinsight"></a>Cenário: Alertas de diretório Apache Ambari no Azure HDInsight

Este artigo descreve etapas de solução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="issue"></a>Problema

Você recebe erros do Apache Ambari que são semelhantes a:

```
1/1 local-dirs have errors: [ /mnt/resource/hadoop/yarn/local : Cannot create directory: /mnt/resource/hadoop/yarn/local ]
1/1 log-dirs have errors: [ /mnt/resource/hadoop/yarn/log : Cannot create directory: /mnt/resource/hadoop/yarn/log ]
```

## <a name="cause"></a>Causa

Os diretórios mencionados do alerta Ambari estão faltando no nó do trabalhador afetado.

## <a name="resolution"></a>Resolução

Crie manualmente diretórios ausentes nos nós de trabalhador afetados.

1. SSH para o nó trabalhador relevante.

1. Obter usuário `sudo su`raiz: .

1. Criar recursivamente diretórios necessários.

1. Mude de dono e grupo para esses diretórios.

    ```bash
    chown -R yarn /mnt/resource/hadoop/yarn/local
    chgrp -R hadoop /mnt/resource/hadoop/yarn/local
    chown -R yarn /mnt/resource/hadoop/yarn/log
    chgrp -R hadoop /mnt/resource/hadoop/yarn/log
    ```

1. Da UI Apache Ambari, desabilite e ative o alerta.

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure através [do Azure Community Support](https://azure.microsoft.com/support/community/).

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Conectando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se você precisar de mais ajuda, você pode enviar uma solicitação de suporte do [portal Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **Suporte** na barra de menus ou abra o centro **de suporte Ajuda +.** Para obter informações mais [detalhadas, consulte Como criar uma solicitação de suporte ao Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e suporte ao faturamento está incluído na assinatura do Microsoft Azure, e o suporte técnico é fornecido através de um dos Planos de Suporte do [Azure](https://azure.microsoft.com/support/plans/).
