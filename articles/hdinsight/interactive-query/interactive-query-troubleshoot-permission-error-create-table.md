---
title: Erro de permissão negada com a tabela Apache Hive no Azure HDInsight
description: Permissão negada ao tentar criar uma tabela de Apache Hive no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/09/2019
ms.openlocfilehash: b6e71f5f2c389926a2e6267fadeedebdce1c51d9
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93288889"
---
# <a name="scenario-permission-denied-error-when-trying-to-create-an-apache-hive-table-in-azure-hdinsight"></a>Cenário: erro de permissão negada ao tentar criar uma tabela de Apache Hive no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao usar componentes de consulta interativa em clusters do Azure HDInsight.

## <a name="issue"></a>Problema

Você verá o seguinte erro ao tentar criar uma tabela:

```
java.sql.SQLException: Error while compiling statement: FAILED: HiveAccessControlException Permission denied: user [hdiuser] does not have [ALL] privilege on [wasbs://data@xxxxx.blob.core.windows.net/path/table]
```

Você verá uma mensagem de erro semelhante se executar o seguinte comando de armazenamento HDFS:

```
hdfs dfs -mkdir wasbs://data@xxxxx.blob.core.windows.net/path/table
```

## <a name="cause"></a>Causa

A capacidade de criar uma tabela no Apache Hive é decidida pelas permissões aplicadas à conta de armazenamento do cluster. Se as permissões da conta de armazenamento de cluster estiverem incorretas, você não poderá criar tabelas. Isso significa que você pode ter as políticas corretas do Ranger para a criação da tabela e ainda ver erros de "permissão negada".

## <a name="resolution"></a>Resolução

Isso é causado por uma falta de permissões suficientes no contêiner de armazenamento que está sendo usado. O usuário que cria a tabela Hive precisa de permissões de leitura, gravação e execução no contêiner. Para obter mais informações, consulte [práticas recomendadas para a autorização do hive usando o Apache Ranger no HDP 2,2](https://hortonworks.com/blog/best-practices-for-hive-authorization-using-apache-ranger-in-hdp-2-2/).

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]