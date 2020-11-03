---
title: Erro de CPU de travamento suave do Watchdog BUG do cluster HDInsight do Azure
description: CPU de travamento soft do BUG do Watchdog aparece em syslogs de kernel do cluster HDInsight do Azure
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/05/2019
ms.openlocfilehash: 33990e40507f088962fd8d8efd241de5d2fcc63b
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289059"
---
# <a name="scenario-watchdog-bug-soft-lockup---cpu-error-from-an-azure-hdinsight-cluster"></a>Cenário: erro "Watchdog: BUG: bloqueio Soft-CPU" de um cluster HDInsight do Azure

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao interagir com clusters do Azure HDInsight.

## <a name="issue"></a>Problema

Os syslogs de kernel contêm a mensagem de erro: `watchdog: BUG: soft lockup - CPU` .

## <a name="cause"></a>Causa

Um [bug](https://bugzilla.kernel.org/show_bug.cgi?id=199437) no kernel do Linux está causando travamentos de CPU.

## <a name="resolution"></a>Resolução

Aplique o patch do kernel. O script abaixo atualiza o kernel do Linux e reinicia as máquinas em momentos diferentes em 24 horas. Execute a ação de script em dois lotes. O primeiro lote está em todos os nós, exceto no nó de cabeçalho. O segundo lote está no nó principal. Não execute no nó principal e em outros nós ao mesmo tempo.

1. Navegue até o cluster HDInsight do portal do Azure.

1. Vá para ações de script.

1. Selecione **Enviar novo** e insira a entrada da seguinte maneira

    | Propriedade | Valor |
    | --- | --- |
    | Tipo de script | -Personalizado |
    | Nome |Correção para o problema de bloqueio soft do kernel |
    | URI do script Bash |`https://raw.githubusercontent.com/hdinsight/hdinsight.github.io/master/ClusterCRUD/KernelSoftLockFix/scripts/KernelSoftLockIssue_FixAndReboot.sh` |
    | Tipo(s) de nó |Trabalho, Zookeeper |
    | Parâmetros |N/D |

    Selecione **persistir esta ação de script...** se você quiser executar o script quando novos nós forem adicionados.

1. Selecione **Criar**.

1. Aguarde até que a execução seja realizada com sucesso.

1. Execute a ação de script no nó de cabeçalho seguindo as mesmas etapas da etapa 3, mas desta vez com os tipos de nó: Head.

1. Aguarde até que a execução seja realizada com sucesso.

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]