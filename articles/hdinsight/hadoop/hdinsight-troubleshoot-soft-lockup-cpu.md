---
title: Erro de CPU de bloqueio macio watchdog BUG do cluster Azure HDInsight
description: Watchdog BUG soft lockup CPU aparece em syslogs de kernel do cluster Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/05/2019
ms.openlocfilehash: 701e314ad2a3762b1e8ca022ce18d9435ce2db37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894117"
---
# <a name="scenario-watchdog-bug-soft-lockup---cpu-error-from-an-azure-hdinsight-cluster"></a>Cenário: erro "watchdog: BUG: soft lockup - CPU" de um cluster Azure HDInsight

Este artigo descreve etapas de solução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="issue"></a>Problema

Os syslogs do kernel `watchdog: BUG: soft lockup - CPU`contêm a mensagem de erro: .

## <a name="cause"></a>Causa

Um [bug](https://bugzilla.kernel.org/show_bug.cgi?id=199437) no Linux Kernel está causando travamentos suaves da CPU.

## <a name="resolution"></a>Resolução

Aplique patch de kernel. O script abaixo atualiza o kernel linux e reinicializa as máquinas em diferentes momentos ao longo de 24 horas. Execute a ação do script em dois lotes. O primeiro lote está em todos os nós, exceto node. O segundo lote é no nó da cabeça. Não corra em nó de cabeça e outros nódulos ao mesmo tempo.

1. Navegue até o cluster HDInsight a partir do portal Azure.

1. Vá para ações de script.

1. Selecione **Enviar Novo** e digite a entrada a seguir

    | Propriedade | Valor |
    | --- | --- |
    | Tipo de script | -Personalizado |
    | Nome |Corrigir para problema de bloqueio macio do kernel |
    | URI do script Bash |`https://raw.githubusercontent.com/hdinsight/hdinsight.github.io/master/ClusterCRUD/KernelSoftLockFix/scripts/KernelSoftLockIssue_FixAndReboot.sh` |
    | Tipo(s) de nó |Trabalhador, Zookeeper |
    | Parâmetros |N/D |

    Selecione **Persistir esta ação de script...** se você quiser executar o script quando novos nós forem adicionados.

1. Selecione **Criar**.

1. Espere a execução ter sucesso.

1. Execute a ação de script no nó Cabeça seguindo os mesmos passos do passo 3, mas desta vez com os tipos de nó: Cabeça.

1. Espere a execução ter sucesso.

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure através [do Azure Community Support](https://azure.microsoft.com/support/community/).

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente conectando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se você precisar de mais ajuda, você pode enviar uma solicitação de suporte do [portal Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **Suporte** na barra de menus ou abra o centro **de suporte Ajuda +.** Para obter informações mais detalhadas, consulte [Como criar uma solicitação de suporte ao Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e suporte ao faturamento está incluído na assinatura do Microsoft Azure, e o suporte técnico é fornecido através de um dos Planos de Suporte do [Azure](https://azure.microsoft.com/support/plans/).
