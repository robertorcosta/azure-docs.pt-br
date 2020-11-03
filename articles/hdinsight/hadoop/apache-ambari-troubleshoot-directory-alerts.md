---
title: Alertas de diretório do Apache Ambari no Azure HDInsight
description: Discussão e análise de possíveis razões e soluções para alertas de diretório do Apache Ambari no HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: d8b1f705eb08d1c58b32e0cbd7c57722a6de93cc
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93285535"
---
# <a name="scenario-apache-ambari-directory-alerts-in-azure-hdinsight"></a>Cenário: alertas de diretório do Apache Ambari no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao interagir com clusters do Azure HDInsight.

## <a name="issue"></a>Problema

Você recebe erros do Apache Ambari que são semelhantes a:

```
1/1 local-dirs have errors: [ /mnt/resource/hadoop/yarn/local : Cannot create directory: /mnt/resource/hadoop/yarn/local ]
1/1 log-dirs have errors: [ /mnt/resource/hadoop/yarn/log : Cannot create directory: /mnt/resource/hadoop/yarn/log ]
```

## <a name="cause"></a>Causa

Os diretórios mencionados do alerta Ambari estão ausentes nos nós de trabalho afetados.

## <a name="resolution"></a>Resolução

Crie manualmente os diretórios ausentes nos nós de trabalho afetados.

1. SSH para o nó de trabalho relevante.

1. Obter usuário raiz: `sudo su` .

1. Crie recursivamente os diretórios necessários.

1. Altere o proprietário e o grupo para esses diretórios.

    ```bash
    chown -R yarn /mnt/resource/hadoop/yarn/local
    chgrp -R hadoop /mnt/resource/hadoop/yarn/local
    chown -R yarn /mnt/resource/hadoop/yarn/log
    chgrp -R hadoop /mnt/resource/hadoop/yarn/log
    ```

1. Na interface do usuário do Apache Ambari, desabilite e, em seguida, habilite o alerta.

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]