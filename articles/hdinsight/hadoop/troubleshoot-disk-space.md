---
title: Gerenciar o espaço em disco no Azure HDInsight
description: Etapas de solução de problemas e possíveis resoluções para gerenciar problemas de espaço em disco ao interagir com clusters do Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/17/2020
ms.openlocfilehash: 0628033ecf6f2e51cf18f61c6e5b36042557dc7b
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/15/2020
ms.locfileid: "90530154"
---
# <a name="manage-disk-space-in-azure-hdinsight"></a>Gerenciar o espaço em disco no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao interagir com clusters do Azure HDInsight.

## <a name="hive-log-configurations"></a>Configurações de log do hive

1. Em um navegador da Web, navegue até `https://CLUSTERNAME.azurehdinsight.net`, em que `CLUSTERNAME` é o nome do cluster.

1. Navegue até configurações do **Hive**  >  **Configs**  >  **avançado**avançado  >  **Hive-Log4J**. Examine as seguintes configurações:

    * `hive.root.logger=DEBUG,RFA`. Esse é o valor padrão, modifique o [nível de log](https://logging.apache.org/log4j/2.x/log4j-api/apidocs/org/apache/logging/log4j/Level.html) para `INFO` para imprimir menos entradas de logs.

    * `log4jhive.log.maxfilesize=1024MB`. Esse é o valor padrão, modifique conforme desejado.

    * `log4jhive.log.maxbackupindex=10`. Esse é o valor padrão, modifique conforme desejado. Se o parâmetro tiver sido omitido, os arquivos de log gerados serão intermináveis.

## <a name="yarn-log-configurations"></a>Configurações de log do yarn

Examine as seguintes configurações:

* Apache Ambari

    1. Em um navegador da Web, navegue até `https://CLUSTERNAME.azurehdinsight.net`, em que `CLUSTERNAME` é o nome do cluster.

    1. Navegue até configurações do **Hive**  >  **Configs**  >  **Advanced**  >  **Gerenciador de recursos**avançado. Verifique se **habilitar a agregação de log** está marcado. Se desabilitado, os nós de nome manterão os logs localmente e não os agregarão no armazenamento remoto na conclusão ou término do aplicativo.

* Verifique se o tamanho do cluster é apropriado para a carga de trabalho. A carga de trabalho pode ter sido alterada recentemente ou o cluster pode ter sido redimensionado. [Escalar verticalmente](../hdinsight-scaling-best-practices.md) o cluster para corresponder a uma carga de trabalho maior.

* `/mnt/resource` pode ser preenchido com arquivos órfãos (como no caso da reinicialização do Resource Manager). Se necessário, limpe manualmente `/mnt/resource/hadoop/yarn/log` e `/mnt/resource/hadoop/yarn/local` .

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio do [Suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport), a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Como se conectar à comunidade do Azure para os recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **Suporte** na barra de menus ou abra o hub **Ajuda + suporte**. Para obter informações mais detalhadas, consulte [Como criar uma solicitação de Suporte do Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). O acesso ao Gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [Planos de suporte do Azure](https://azure.microsoft.com/support/plans/).
