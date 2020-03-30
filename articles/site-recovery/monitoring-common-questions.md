---
title: Perguntas comuns sobre o monitoramento da recuperação do site do Azure
description: Obtenha respostas para perguntas comuns sobre o monitoramento de recuperação do site do Azure, usando monitoramento embutido e Monitor do Azure (Log Analytics)
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.date: 07/31/2019
ms.topic: conceptual
ms.author: raynew
ms.openlocfilehash: c1d30a9cdd2cd6ca288edd609a2e2e7bee9174d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "68718256"
---
# <a name="common-questions-about-site-recovery-monitoring"></a>Perguntas comuns sobre monitoramento de recuperação de sites

Este artigo responde a perguntas comuns sobre o monitoramento da [recuperação do site](site-recovery-overview.md)do Azure, usando monitoramento de recuperação de site embutido e o Azure Monitor (Log Analytics).

## <a name="general"></a>Geral

### <a name="how-is-the-rpo-value-logged-different-from-the-latest-available-recovery-point"></a>Como o valor de RPO é registrado diferente do último ponto de recuperação disponível?

A Recuperação do Site usa um processo assíncrono de várias etapas para replicar máquinas ao Azure.

- Na penúltima etapa da replicação, as alterações recentes no computador, em conjunto com metadados, são copiadas para uma conta de armazenamento em cache/log.
- Essas alterações, juntamente com a tag que identifica um ponto recuperável, são escritas na conta de armazenamento/disco gerenciado na região de destino.
- A recuperação do site agora pode gerar um ponto recuperável para a máquina.
- Neste momento, o RPO foi atendido pelas alterações enviadas para a conta de armazenamento até agora. Em outras palavras, o RPO da máquina neste momento é igual à quantidade de tempo que é decorrido do carimbo de tempo correspondente ao ponto recuperável.
- Agora, o Site Recovery seleciona os dados carregados da conta de armazenamento e aplica-os aos discos de réplica criados para o computador.
- O Site Recovery, em seguida, gera um ponto de recuperação e disponibiliza esse ponto para recuperação no failover.
- Assim, o último ponto de recuperação disponível indica o carimbo de tempo correspondente ao último ponto de recuperação que já foi processado e aplicado aos discos de réplica.


Um tempo de sistema incorreto na máquina de origem replicante, ou em servidores de infra-estrutura locais, distorcerá o valor de RPO calculado. Para relatórios precisos de RPO, verifique se o relógio do sistema está correto em todos os servidores e computadores.



## <a name="inbuilt-site-recovery-logging"></a>Registro de recuperação de site embutido


### <a name="why-is-the-vm-count-in-the-vault-infrastructure-view-different-from-the-total-count-shown-in-replicated-items"></a>Por que a contagem de VM na exibição de infra-estrutura do cofre é diferente da contagem total mostrada em Itens Replicados?

O modo de exibição de infraestrutura do cofre é com escopo por cenários de replicação. Apenas máquinas no cenário de replicação atualmente selecionado são incluídas na contagem para a exibição. Além disso, contamos apenas as VMs configuradas para replicar no Azure. Falhas em máquinas, ou máquinas replicadas de volta a um local no local, não são contadas na exibição.

### <a name="why-is-the-count-of-replicated-items-in-essentials-different-from-the-total-count-of-replicated-items-on-the-dashboard"></a>Por que a contagem de itens replicados no Essentials é diferente da contagem total de itens replicados no painel?

Apenas as máquinas para as quais a replicação inicial foi concluída estão incluídas na contagem mostrada no Essentials. O total de itens replicados inclui todas as máquinas no cofre, incluindo aquelas para as quais a replicação inicial está em andamento.

## <a name="azure-monitor-logging"></a>Registro em log do Azure Monitor


### <a name="how-often-does-site-recovery-send-diagnostic-logs-to-azure-monitor-log"></a>Com que frequência o Site Recovery envia registros de diagnóstico para o Azure Monitor Log? 

- AzureSiteRecoveryReplicationStats e AzureSiteRecoveryOs pontos são enviados a cada 15 minutos.  
- AzureSiteRecoveryReplicationDataUploadRate e AzureSiteRecoveryProtectedDiskDataChurn são enviados a cada cinco minutos. 
- AzureSiteRecoveryJobs é enviado no gatilho e conclusão de um trabalho.
- AzureSiteRecoveryEvents é enviado sempre que um evento é gerado. 
- AzureSiteRecoveryReplicadItens são enviados sempre que houver qualquer alteração de ambiente. Normalmente, o tempo de atualização dos dados é de 15 minutos após uma alteração. 

### <a name="how-long-is-data-kept-in-azure-monitor-logs"></a>Quanto tempo os dados são mantidos nos registros do Monitor do Azure? 

Por padrão, a retenção é de 31 dias. Você pode aumentar o período na seção **Uso e Custo Estimado** no espaço de trabalho do Log Analytics. Clique em **Retenção de Dados**e escolha o intervalo.

### <a name="whats-the-size-of-the-diagnostic-logs"></a>Qual é o tamanho dos registros de diagnóstico? 

Normalmente, o tamanho de um tronco é de 15-20 KB. 


## <a name="next-steps"></a>Próximas etapas

Saiba como monitorar com [o monitoramento embutido do Site Recovery](site-recovery-monitor-and-troubleshoot.md)ou com o Monitor [Azure.](monitor-log-analytics.md)


