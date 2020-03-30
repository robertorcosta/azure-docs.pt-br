---
title: Erro do intérprete Apache Hive Zeppelin - Azure HDInsight
description: O Intérprete Apache Zeppelin Hive JDBC está apontando para a URL errada no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 20309babb9ece0ae20e7442543b0d378f9a51060
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895054"
---
# <a name="scenario-apache-hive-zeppelin-interpreter-gives-a-zookeeper-error-in-azure-hdinsight"></a>Cenário: Intérprete de Hive Zeppelin Apache dá um erro de Zookeeper no Azure HDInsight

Este artigo descreve etapas de solução de problemas e possíveis resoluções para problemas ao usar componentes de Consulta Interativa em clusters Azure HDInsight.

## <a name="issue"></a>Problema

Em um cluster Apache Hive LLAP, o intérprete Zeppelin fornece a seguinte mensagem de erro ao tentar executar uma consulta:

```
java.sql.SQLException: org.apache.hive.jdbc.ZooKeeperHiveClientException: Unable to read HiveServer2 configs from ZooKeeper
```

## <a name="cause"></a>Causa

O Intérprete Zeppelin Hive JDBC está apontando para a URL errada.

## <a name="resolution"></a>Resolução

1. Navegue até o resumo do componente Colmeia e copie a "Url Hive JDBC" na área de transferência.

1. Navegue até `https://clustername.azurehdinsight.net/zeppelin/#/interpreter`

1. Editar as configurações do JDBC: atualize o valor de hive.url para a URL da Colmeia JDBC copiada na etapa 1

1. Salvar e, em seguida, tentar novamente a consulta

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure através [do Azure Community Support](https://azure.microsoft.com/support/community/).

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente conectando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se você precisar de mais ajuda, você pode enviar uma solicitação de suporte do [portal Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **Suporte** na barra de menus ou abra o centro **de suporte Ajuda +.** Para obter informações mais detalhadas, consulte [Como criar uma solicitação de suporte ao Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e suporte ao faturamento está incluído na assinatura do Microsoft Azure, e o suporte técnico é fornecido através de um dos Planos de Suporte do [Azure](https://azure.microsoft.com/support/plans/).
