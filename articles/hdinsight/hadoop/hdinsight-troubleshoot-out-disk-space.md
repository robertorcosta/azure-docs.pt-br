---
title: O nó do cluster fica sem espaço em disco no Azure HDInsight
description: Solução de problemas de Apache Hadoop de espaço em disco do nó de cluster no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/30/2020
ms.openlocfilehash: f1a994ad07980c67e37d00bffb7e605ed610bb08
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289080"
---
# <a name="scenario-cluster-node-runs-out-of-disk-space-in-azure-hdinsight"></a>Cenário: o nó de cluster é executado sem espaço em disco no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao interagir com clusters do Azure HDInsight.

## <a name="issue"></a>Problema

Um trabalho pode falhar com mensagem de erro semelhante a: `/usr/hdp/2.6.3.2-14/hadoop/libexec/hadoop-config.sh: fork: No space left on device.`

Ou você pode receber um alerta do Apache Ambari semelhante a: `local-dirs usable space is below configured utilization percentage` .

## <a name="cause"></a>Causa

O cache de aplicativos Apache yarn pode ter consumido todo o espaço em disco disponível. O aplicativo Spark provavelmente está sendo executado de forma ineficiente.

## <a name="resolution"></a>Resolução

1. Use a interface do usuário do Ambari para determinar qual nó está ficando sem espaço em disco.

1. Determine qual pasta no nó preocupantes contribui para a maior parte do espaço em disco. Use SSH para o nó primeiro e, em seguida, execute `df` para listar o uso do disco para todas as montagens. Normalmente, é `/mnt` um disco temporário usado pelo OSS. Você pode inserir em uma pasta e, em seguida, digitar `sudo du -hs` para mostrar os tamanhos de arquivo resumidos em uma pasta. Se você vir uma pasta semelhante a `/mnt/resource/hadoop/yarn/local/usercache/livy/appcache/application_1537280705629_0007` , isso significará que o aplicativo ainda está em execução. Isso pode ser devido à persistência de RDD ou a arquivos aleatórios intermediários.

1. Para atenuar o problema, encerre o aplicativo, que liberará o espaço em disco usado por esse aplicativo.

1. Se o problema ocorrer frequentemente nos nós de trabalho, você poderá ajustar as configurações de cache local YARN no cluster.

    Abra a interface do usuário do Ambari navegue até YARN--> configurações--> avançado.  
    Adicione as duas propriedades a seguir à seção yarn-site.xml personalizada e salve:

    ```
    yarn.nodemanager.localizer.cache.target-size-mb=2048
    yarn.nodemanager.localizer.cache.cleanup.interval-ms=300000
    ```

1. Se a acima não corrigir permanentemente o problema, Otimize seu aplicativo.

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]