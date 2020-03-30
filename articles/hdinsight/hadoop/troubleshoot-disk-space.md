---
title: Gerencie espaço em disco no Azure HDInsight
description: Solução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/17/2020
ms.openlocfilehash: 577bed7ce342be14a50077a3ffd841cd901b5b31
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77473006"
---
# <a name="manage-disk-space-in-azure-hdinsight"></a>Gerencie espaço em disco no Azure HDInsight

Este artigo descreve etapas de solução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="hive-log-configurations"></a>Configurações do registro de colmeia

1. A partir de um `https://CLUSTERNAME.azurehdinsight.net`navegador `CLUSTERNAME` da Web, navegue até , onde está o nome do seu cluster.

1. Navegue até **Hive** > **Configs** > **Advanced** > Advanced**hive-log4j**. Revise as seguintes configurações:

    * `hive.root.logger=DEBUG,RFA`. Este é o valor padrão, `INFO` modifique o nível de [log](https://logging.apache.org/log4j/2.x/log4j-api/apidocs/org/apache/logging/log4j/Level.html) para imprimir menos entradas de logs.

    * `log4jhive.log.maxfilesize=1024MB`. Este é o valor padrão, modifique conforme desejado.

    * `log4jhive.log.maxbackupindex=10`. Este é o valor padrão, modifique conforme desejado. Se o parâmetro tiver sido omitido, os arquivos de log gerados serão infinitos.

## <a name="yarn-log-configurations"></a>Configurações de log de fio

Revise as seguintes configurações:

* Apache Ambari

    1. A partir de um `https://CLUSTERNAME.azurehdinsight.net`navegador `CLUSTERNAME` da Web, navegue até , onde está o nome do seu cluster.

    1. Navegue até o Gerenciador**avançado** > **de recursos** **da Colmeia** > **Configs** > . **Certifique-se de que a agregação** de log de ativação seja verificada. Se desativados, os nomes nos nomearão localmente e não os agregarão em loja remota na conclusão ou rescisão do aplicativo.

* Verifique se o tamanho do cluster é apropriado para a carga de trabalho. A carga de trabalho pode ter mudado recentemente ou o cluster pode ter sido redimensionado. [Dimensione](../hdinsight-scaling-best-practices.md) o cluster para corresponder a uma carga de trabalho maior.

* `/mnt/resource`pode ser preenchido com arquivos órfãos (como no caso de reinicialização do gerenciador de recursos). Se necessário, `/mnt/resource/hadoop/yarn/log` limpe `/mnt/resource/hadoop/yarn/local`manualmente e .

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure através [do Azure Community Support](https://azure.microsoft.com/support/community/).

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Conectando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se você precisar de mais ajuda, você pode enviar uma solicitação de suporte do [portal Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **Suporte** na barra de menus ou abra o centro **de suporte Ajuda +.** Para obter informações mais [detalhadas, consulte Como criar uma solicitação de suporte ao Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e suporte ao faturamento está incluído na assinatura do Microsoft Azure, e o suporte técnico é fornecido através de um dos Planos de Suporte do [Azure](https://azure.microsoft.com/support/plans/).
