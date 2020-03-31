---
title: Monitore o servidor de processo de recuperação do site do Azure
description: Este artigo descreve como monitorar o servidor de processo de recuperação do Azure Site usado para recuperação de desastres de vmware/servidor físico
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 54c161c40c881d7626f79fc9bfe1ec1c160480ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257063"
---
# <a name="monitor-the-process-server"></a>Monitorar o servidor de processo

Este artigo descreve como monitorar o servidor de processo [de recuperação de site.](site-recovery-overview.md)

- O servidor de processo é usado quando você configura a recuperação de desastres de VMMs VMware e servidores físicos no local para o Azure.
- Por padrão, o servidor de processo é executado no servidor de configuração. Ele é instalado por padrão quando você implanta o servidor de configuração.
- Opcionalmente, para dimensionar e lidar com um número maior de máquinas replicadas e maiores volumes de tráfego de replicação, você pode implantar servidores de processo adicionais e com escala.

[Saiba mais](vmware-physical-azure-config-process-server-overview.md) sobre a função e implantação de servidores de processo.

## <a name="monitoring-overview"></a>Visão geral de monitoramento

Uma vez que o servidor de processo tem tantas funções, particularmente no cache de dados replicado, compactação e transferência para o Azure, é importante monitorar a saúde do servidor de processos de forma contínua.

Há uma série de situações que geralmente afetam o desempenho do servidor de processo. Problemas que afetam o desempenho terão um efeito em cascata na saúde da VM, eventualmente empurrando o servidor de processo e suas máquinas replicadas para um estado crítico. As situações incluem:

- Um alto número de VMs usa um servidor de processo, aproximando ou excedendo as limitações recomendadas.
- As VMs que usam o servidor de processo têm uma alta taxa de churn.
- O throughput de rede entre VMs e o servidor de processo não é suficiente para carregar dados de replicação para o servidor de processo.
- O throughput de rede entre o servidor de processo e o Azure não é suficiente para carregar dados de replicação do servidor de processo para o Azure.

Todos esses problemas podem afetar o RPO (Recovery Point Objective, objetivo de ponto de recuperação) das VMs. 

**Porque?** Porque gerar um ponto de recuperação para uma VM requer que todos os discos da VM tenham um ponto comum. Se um disco tiver uma alta taxa de churn, a replicação for lenta ou o servidor de processo não for o ideal, isso impacta a eficiência com que os pontos de recuperação são criados.

## <a name="monitor-proactively"></a>Monitorar proativamente

Para evitar problemas com o servidor de processo, é importante:

- Entenda os requisitos específicos para servidores de processo usando [orientação de capacidade e dimensionamento](site-recovery-plan-capacity-vmware.md#capacity-considerations)e certifique-se de que os servidores de processo sejam implantados e executados de acordo com as recomendações.
- Monitore os alertas e soluciona problemas à medida que ocorrem, para manter os servidores de processo funcionando de forma eficiente.


## <a name="process-server-alerts"></a>Alertas de servidor de processo

O servidor de processos gera uma série de alertas de saúde, resumidos na tabela a seguir.

**Tipo de alerta** | **Detalhes**
--- | ---
![Healthy][green] | O servidor de processos está conectado e saudável.
![Aviso][yellow] | A utilização da CPU > 80% nos últimos 15 minutos
![Aviso][yellow] | O uso da memória > 80% nos últimos 15 minutos
![Aviso][yellow] | Cache pasta espaço livre < 30% nos últimos 15 minutos
![Aviso][yellow] | O Site Recovery monitora dados pendentes/de saída a cada cinco minutos e estima que os dados no cache do servidor de processo não podem ser carregados no Azure em 30 minutos.
![Aviso][yellow] | Os serviços do servidor de processo não estão sendo executados nos últimos 15 minutos
![Crítico][red] | A utilização da CPU > 95% nos últimos 15 minutos
![Crítico][red] | O uso da memória > 95% nos últimos 15 minutos
![Crítico][red] | Cache pasta espaço livre < 25% nos últimos 15 minutos
![Crítico][red] | O Site Recovery monitora dados pendentes/de saída a cada cinco minutos e estima que os dados no cache do servidor de processo não podem ser carregados no Azure em 45 minutos.
![Crítico][red] | Sem batimentos cardíacos do servidor de processo por 15 minutos.

![Chave de tabela](./media/vmware-physical-azure-monitor-process-server/table-key.png)

> [!NOTE]
> O estado geral de saúde do servidor de processo baseia-se no pior alerta gerado.



## <a name="monitor-process-server-health"></a>Monitorar a saúde do servidor de processos

Você pode monitorar o estado de saúde dos servidores do seu processo da seguinte forma: 

1. Para monitorar a saúde e o status de replicação de uma máquina replicada, e de seu servidor de processo, no cofre > **itens replicados,** clique na máquina que deseja monitorar.
2. Em **Replication Health,** você pode monitorar o estado de saúde da VM. Clique no status para detalhar os detalhes do erro.

    ![Processe a saúde do servidor no painel vm](./media/vmware-physical-azure-monitor-process-server/vm-ps-health.png)

4. No **Process Server Health,** você pode monitorar o status do servidor de processo. Aprofunde-se para detalhes.

    ![Detalhes do servidor de processo no painel vm](./media/vmware-physical-azure-monitor-process-server/ps-summary.png)

5. A saúde também pode ser monitorada usando a representação gráfica na página vm.
    - Um servidor de processo de saída de escala será destacado em laranja se houver avisos associados a ele, e vermelho se tiver algum problema crítico. 
    - Se o servidor de processo estiver executando a implantação padrão no servidor de configuração, então o servidor de configuração será destacado de acordo.
    - Para detalhar, clique no servidor de configuração ou servidor de processo. Observe quaisquer problemas e quaisquer recomendações de remediação.

Você também pode monitorar servidores de processo no cofre em **Site Recovery Infrastructure**. Em **Gerenciar sua infra-estrutura de recuperação de site,** clique **em Servidores de configuração**. Selecione o servidor de configuração associado ao servidor de processo e aprofunde os detalhes do servidor de processo.


## <a name="next-steps"></a>Próximas etapas

- Se você tiver algum problema com servidores de processo, siga nossa [orientação de solução de problemas](vmware-physical-azure-troubleshoot-process-server.md)
- Se precisar de mais ajuda, poste sua pergunta no [fórum do Azure Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). 

[green]: ./media/vmware-physical-azure-monitor-process-server/green.png
[yellow]: ./media/vmware-physical-azure-monitor-process-server/yellow.png
[red]: ./media/vmware-physical-azure-monitor-process-server/red.png
