---
title: Nó de cluster fica sem espaço em disco no Azure HDInsight
description: Solução de problemas Problemas problemas de espaço de disco de cluster Apache Hadoop no Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/05/2019
ms.openlocfilehash: fbfd82473b68f5032d19834ac809191d498a5a67
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894121"
---
# <a name="scenario-cluster-node-runs-out-of-disk-space-in-azure-hdinsight"></a>Cenário: Nó de cluster fica sem espaço em disco no Azure HDInsight

Este artigo descreve etapas de solução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="issue"></a>Problema

Um trabalho pode falhar com uma mensagem de erro semelhante a:`/usr/hdp/2.6.3.2-14/hadoop/libexec/hadoop-config.sh: fork: No space left on device.`

Ou você pode receber alerta Apache `local-dirs usable space is below configured utilization percentage`Ambari semelhante a: .

## <a name="cause"></a>Causa

O cache do aplicativo Apache Yarn pode ter consumido todo o espaço disponível em disco. Sua aplicação Spark provavelmente está funcionando de forma ineficiente.

## <a name="resolution"></a>Resolução

1. Use a Ambari UI para determinar qual nó está ficando sem espaço em disco.

1. Determine qual pasta no nó preocupante contribui para a maior parte do espaço do disco. SSH para o nó primeiro, em seguida, executar `df` para listar o uso do disco para todas as montagens. Geralmente é `/mnt` um disco temporário usado pelo OSS. Você pode entrar em uma `sudo du -hs` pasta e digitar para mostrar tamanhos de arquivo resumidos em uma pasta. Se você vir uma `/mnt/resource/hadoop/yarn/local/usercache/livy/appcache/application_1537280705629_0007`pasta semelhante a , isso significa que o aplicativo ainda está em execução. Isso pode ser devido à persistência de RDD ou arquivos de embaralhar intermediários.

1. Para mitigar o problema, mate o aplicativo, que liberará o espaço em disco usado por esse aplicativo.

1. Para, em última análise, resolver o problema, otimize sua aplicação.

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure através [do Azure Community Support](https://azure.microsoft.com/support/community/).

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente conectando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se você precisar de mais ajuda, você pode enviar uma solicitação de suporte do [portal Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **Suporte** na barra de menus ou abra o centro **de suporte Ajuda +.** Para obter informações mais detalhadas, consulte [Como criar uma solicitação de suporte ao Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e suporte ao faturamento está incluído na assinatura do Microsoft Azure, e o suporte técnico é fornecido através de um dos Planos de Suporte do [Azure](https://azure.microsoft.com/support/plans/).
