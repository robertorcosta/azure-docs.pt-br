---
title: Solucionar problemas do desempenho da máquina virtual do Azure no Linux ou Windows
description: Este artigo descreve a solução genérica de problemas de desempenho da máquina virtual (VM) através do monitoramento e observação de gargalos e fornece possíveis remediações para problemas que possam ocorrer.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/18/2019
ms.author: v-miegge
ms.openlocfilehash: 176b0634fe2c7ee2f47162e439c4ea16bde77a8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75772611"
---
# <a name="troubleshoot-azure-virtual-machine-performance-on-linux-or-windows"></a>Solucionar problemas do desempenho da máquina virtual do Azure no Linux ou Windows

Este artigo descreve a solução genérica de problemas de desempenho da máquina virtual (VM) através do monitoramento e observação de gargalos e fornece possíveis remediações para problemas que possam ocorrer. Além do monitoramento, você também pode usar o Perfinsights, que pode fornecer um relatório com recomendações de práticas recomendadas e gargalos principais em torno de IO/CPU/Memory. O Perfinsights está disponível para [VMs Windows](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfInsights) e [Linux](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfinsights-linux) no Azure.

Este artigo passará por meio do monitoramento para diagnosticar gargalos de desempenho.

## <a name="enabling-monitoring"></a>Habilitar o monitoramento

### <a name="azure-iaas-virtual-machine-monitoring"></a>Monitoramento de máquinavirtual Azure IAAS

Para monitorar a VM convidada, use o Monitor de VM do Azure, que irá alertá-lo para certas condições de recursos de alto nível. Para verificar se você tem os diagnósticos de VM ativados, consulte [a visão geral dos registros do Azure Resource](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-resource-logs). Se você ver o seguinte, então provavelmente não terá os diagnósticos ativados:

![O monitoramento não está ativado](media/troubleshoot-performance-virtual-machine-linux-windows/1-virtual-machines-monitoring-not-enabled.png)
 
### <a name="enable-vm-diagnostics-through-microsoft-azure-portal"></a>Habilite os diagnósticos de VM através do portal Microsoft Azure

Para habilitar diagnósticos em VM:

1. Vá para a VM
2. Clique **em Configurações de diagnóstico**
3. Selecione a conta de armazenamento e clique **em Ativar o monitoramento em nível de hóspedes**.

   ![Clique em Configurações e, em seguida, Diagnósticos](media/troubleshoot-performance-virtual-machine-linux-windows/2-virtual-machines-diagnostics.png)

Você pode verificar a conta de armazenamento usada para configuração de Diagnóstico a partir da guia **Agente** em **Configurações de Diagnóstico**.

![Verificar conta de armazenamento](media/troubleshoot-performance-virtual-machine-linux-windows/3-check-storage-account.png)

### <a name="enable-storage-account-diagnostics-through-azure-portal"></a>Habilite o diagnóstico da conta de armazenamento através do portal Azure

O armazenamento é um nível muito importante quando pretendemos analisar o desempenho de IO para uma máquina virtual no Azure. Para métricas relacionadas ao armazenamento, precisamos habilitar o diagnóstico como um passo adicional. Isso também pode ser habilitado, se quisermos apenas analisar os contadores relacionados ao armazenamento.

1. Identifique qual conta de armazenamento (ou contas) sua VM está usando selecionando a VM. Clique **em Configurações**e clique **em Discos:**

   ![Clique em Configurações e, em seguida, discos](media/troubleshoot-performance-virtual-machine-linux-windows/4-storage-disks-disks-selection.png)

2. No portal, vá para a conta de armazenamento (ou contas) para a VM e trabalhe através das seguintes etapas:

   1. Clique em visão geral da conta armazenamento encontrada com o passo acima.
   2. Métricas padrão seriam mostradas. 

    ![Métricas padrão](media/troubleshoot-performance-virtual-machine-linux-windows/5-default-metrics.png)

3. Clique em qualquer uma das métricas, que mostrará outra lâmina com mais opções para configurar e adicionar métricas.

   ![Adicionar Métricas](media/troubleshoot-performance-virtual-machine-linux-windows/6-add-metrics.png)

Para configurar essas opções:

1.  Selecione **Métricas**.
2.  Selecione o **Recurso** (conta de armazenamento).
3.  Selecione o **namespace**
4.  Selecione **Métrica**.
5.  Selecione o tipo **de Agregação**
6.  Você pode fixar esta visão no painel.

## <a name="observing-bottlenecks"></a>Observando gargalos

Uma vez que estamos no processo inicial de configuração para métricas necessárias, e postando habilitando os diagnósticos para VM e conta de armazenamento relacionado, podemos mudar para a fase de análise.

### <a name="accessing-the-monitoring"></a>Acessando o monitoramento

Selecione a VM Azure que deseja investigar e selecione **Monitoramento**.

![Selecionar monitoramento](media/troubleshoot-performance-virtual-machine-linux-windows/7-select-monitoring.png)
 
### <a name="timelines-of-observation"></a>Cronogramas de observação

Para identificar se você tem algum gargalo de recursos, revise seus dados. Se você achar que sua máquina está funcionando bem, mas foi relatado que o desempenho foi recentemente degradado, revise um intervalo de tempo de dados que engloba dados métricos de desempenho antes da alteração relatada, durante e após o problema.

### <a name="check-for-cpu-bottleneck"></a>Verifique se há gargalo da CPU

![Verifique o gargalo da CPU](media/troubleshoot-performance-virtual-machine-linux-windows/8-cpu-bottleneck-time-range.png)

1. Edite o Gráfico.
2. Defina o intervalo de tempo.
3. Em seguida, você precisa adicionar no contador: CPU Percentage Guest OS
4. Salve.

### <a name="cpu-observe-trends"></a>Cpu observar tendências

Ao olhar para os problemas de desempenho, esteja ciente das tendências e entenda se elas afetam você. Nas próximas seções, usaremos os gráficos de monitoramento do portal para mostrar tendências. Eles também podem ser úteis para fazer referênciacruzada sustais de comportamentos de recursos no mesmo período de tempo. Para personalizar os gráficos, clique na [plataforma de dados Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform).

Spiking – Spiking pode estar relacionado a uma tarefa agendada/evento conhecido. Se você puder identificar a tarefa, determine se a tarefa é executada no nível de desempenho necessário. Se o desempenho for aceitável, talvez não seja necessário aumentar os recursos.

Spike up e Constant – Muitas vezes indica uma nova carga de trabalho. Se não for uma carga de trabalho reconhecida, habilite o monitoramento na VM para descobrir qual processo (ou processos) causa o comportamento. Uma vez que o processo é reconhecido, determine se o aumento do consumo está sendo causado por código ineficiente ou consumo normal. Se o consumo normal, decida se o processo opera no nível de desempenho necessário.

Constante – Determine se sua VM sempre foi executada neste nível ou se ela só foi executada nesse nível desde que os diagnósticos foram ativados. Se sim, identifique o processo (ou processos) que causam o problema e considere adicionar mais desse recurso.

Aumentando constantemente – Um aumento constante no consumo é, muitas vezes, um código ineficiente ou um processo que assume mais carga de trabalho do usuário.

### <a name="high-cpu-utilization-remediation"></a>Alta remediação de utilização da CPU

Se o seu aplicativo ou processo não estiver sendo executado no nível correto de desempenho e você estiver vendo 95% + a constante de utilização da CPU, você pode executar qualquer uma das seguintes tarefas:

* Para alívio imediato - Aumente o tamanho da VM para um tamanho com mais núcleos
* Entenda o problema – localize o aplicativo/processo e soque-o de acordo.

Se você aumentou a VM, e a CPU ainda está em execução de 95%, determine se essa configuração está oferecendo melhor desempenho ou maior rendimento do aplicativo a um nível aceitável. Caso assim, soluciona solucionar problemas nesse processo de aplicação individual.

Você pode usar o Perfinsights para [Windows](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfInsights) ou [Linux](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfinsights-linux) para analisar qual processo está impulsionando o consumo de CPU. 

## <a name="check-for-memory-bottleneck"></a>Verifique se há gargalo de memória

Para visualizar as métricas:

1. Adicione uma seção.
2. Adicione um azulejo.
3. Abra a Galeria.
4. Selecione o Uso de Memória e arraste. Quando o azulejo estiver encaixado, clique com o botão direito do mouse e selecione **6x4**.

### <a name="memory-observe-trends"></a>Memória observar tendências

O Uso da Memória mostra quanta memória está sendo consumida com a VM. Entenda a tendência e se ela mapeia até o momento em que você vê problemas. Você deve sempre ter mais de 100 MB de memória disponível.

Spike e Consumo Constante Constante - A alta utilização da memória pode não ser a causa do mau desempenho, pois alguns aplicativos, como motores de banco de dados relacionais, alocam uma grande quantidade de memória, e essa utilização pode não ser significativa. No entanto, se houver vários aplicativos com fome de memória, você poderá ver um desempenho ruim da contenção da memória causando corte e paginação/troca em disco. Esse desempenho ruim é muitas vezes uma causa perceptível do impacto no desempenho do aplicativo.

Aumento constante do consumo – Uma possível aplicação de "aquecimento", esse consumo é comum entre os motores de banco de dados que iniciam. No entanto, isso também pode ser um sinal de perda de memória em um aplicativo. Identifique a aplicação e entenda se o comportamento é esperado.

Página ou Uso de arquivo de swap – Verifique se você está\) usando o arquivo de `/dev/sdb`paginação do Windows (localizado em D: ou o arquivo Linux Swap (localizado) está sendo muito usado. Se você não tiver nada nesses volumes, exceto esses arquivos, verifique se há leitura/gravações altas nesses discos. Esta questão é indicativa de baixas condições de memória.

### <a name="high-memory-utilization-remediation"></a>Remediação de alta utilização da memória

Para resolver a alta utilização da memória, execute qualquer uma das seguintes tarefas:

* Para alívio imediato ou uso de página ou arquivo de troca - Aumente o tamanho da VM para uma com mais memória e, em seguida, monitore.
* Entenda o problema – localize aplicativos/processos e soluciona problemas para identificar aplicativos de memória de alto consumo.
* Se você conhece o aplicativo, veja se a alocação de memória pode ser tampada.

Se depois de atualizar para uma VM maior, você descobrir que ainda tem um aumento constante constante até 100%, identifique o aplicativo/processo e solucionem problemas.

Você pode usar o Perfinsights para [Windows](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfInsights) ou [Linux](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfinsights-linux) para analisar qual processo está impulsionando o consumo de memória. 

## <a name="check-for-disk-bottleneck"></a>Verifique se há gargalo de disco

Para verificar o subsistema de armazenamento da VM, verifique os diagnósticos no nível de VM do Azure usando os contadores no VM Diagnostics e também no Storage Account Diagnostics.

Para dentro da solução de problemas específicas da VM, você pode usar o Perfinsights para [Windows](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfInsights) ou [Linux,](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfinsights-linux)o que pode ajudar a analisar qual processo está conduzindo os IO's. 

Observe que não temos contadores para contas de armazenamento redundante e premium. Para questões relacionadas a esses contadores, levante um caso de apoio.

### <a name="viewing-storage-account-diagnostics-in-monitoring"></a>Visualização de diagnósticos de contas de armazenamento no monitoramento

Para trabalhar nos itens abaixo, vá para a conta de armazenamento da VM no portal:

![Visualização de diagnósticos de contas de armazenamento no monitoramento](media/troubleshoot-performance-virtual-machine-linux-windows/9-virtual-machine-storage-account.png)

1. Edite o Gráfico de Monitoramento.
2. Defina o intervalo de tempo.
3. Adicione os contadores descritos nas etapas abaixo.
4. Salve as alterações.

### <a name="disk-observe-trends-standard-storage-only"></a>Tendências de observação em disco (somente armazenamento padrão)

Para identificar problemas com o armazenamento, veja as métricas de desempenho dos Diagnósticos da Conta de Armazenamento e do VM Diagnostics.

Para cada verificação abaixo, procure as principais tendências quando os problemas ocorrerem dentro do intervalo de tempo do problema.

#### <a name="check-azure-storage-availability--add-the-storage-account-metric-availability"></a>Verifique a disponibilidade de armazenamento do Azure – Adicione a métrica da conta de armazenamento: disponibilidade

Se você vir uma queda na disponibilidade, pode haver um problema com a plataforma, verifique o [Status do Azure](https://azure.microsoft.com/status/). Se nenhum problema for mostrado lá, levante uma nova solicitação de suporte.

#### <a name="check-for-azure-storage-timeout---add-the-storage-account-metrics"></a>Verifique o tempo de tempo de armazenamento do Azure - Adicione as métricas da conta de armazenamento:

* Erro de saída de tempo do cliente
* Erro de saída de tempo do servidor
* AverageE2ELatency
* AverageServerLatency
* TotalRequests

Os valores nas métricas *TimeOutError indicam que uma operação de IO demorou muito tempo e foi cronometrada. Trabalhar nos próximos passos ajudará a identificar possíveis causas.

O AverageServerLatency aumenta ao mesmo tempo no TimeOutErrors pode ser um problema da plataforma. Levante um novo pedido de apoio nesta situação.

AverageE2ELatency representa a latência do cliente. Verifique como o IOPS está sendo executado pelo aplicativo. Procure uma métrica totalrequests de aumento ou constantemente alta. Esta métrica representa o IOPS. Se você está começando a atingir os limites da conta de armazenamento ou vHD único, a latência pode estar relacionada ao estrangulamento.

#### <a name="check-for-azure-storage-throttling---add-the-storage-account-metrics-throttlingerror"></a>Verifique o estrangulamento do armazenamento do Azure - Adicione as métricas da conta de armazenamento: ThrottlingError

Os valores para estrangulamento indicam que você está sendo estrangulado no nível da conta de armazenamento, o que significa que você atinge o limite IOPS da conta. Você pode determinar se você está atingindo o limiar de IOPs verificando a métrica **TotalRequests**.

Observe que cada VHD tem um limite de 500 IOPS ou 60 MBits, mas está vinculado ao limite cumulativo de 20000 IOPS por conta de armazenamento.

Com esta métrica, você não pode dizer qual bolha está causando o estrangulamento e quais são afetados por ela. No entanto, ou você está atingindo os limites iOPS ou Ingress/Egress da conta de armazenamento.

Para identificar se você está atingindo o limite do IOPS, entre no diagnóstico da Conta de Armazenamento e verifique o TotalRequests, procurando se você está se aproximando de 20 mil Solicitações Totais. Identifique uma mudança no padrão, se você está vendo o limite pela primeira vez, ou se esse limite acontece em um determinado momento.

Com novas ofertas de disco o armazenamento Padrão, os limites de IOPS e Throughput podem diferir, mas o limite cumulativo da conta Standard Storage é o IOPS 20000 (o armazenamento Premium tem limites diferentes na conta ou no nível de disco). Leia mais sobre diferentes ofertas padrão de disco de armazenamento e limites por disco:

* [Metas de escalabilidade e desempenho para discos VM no Windows](https://docs.microsoft.com/azure/virtual-machines/windows/disk-scalability-targets).

#### <a name="references"></a>Referências

* [Metas de escalabilidade e desempenho para contas de armazenamento blob de página premium](../../storage/blobs/scalability-targets-premium-page-blobs.md)

A largura de banda da conta de armazenamento é medida pelas Métricas da Conta de Armazenamento: TotalIngress e TotalEgress. Você tem diferentes limites para largura de banda, dependendo do tipo de redundância e regiões.

* [Metas de escalabilidade e desempenho das contas de Armazenamento Standard](../../storage/common/scalability-targets-standard-account.md)

Verifique os limites totalingress e totalegress contra os limites de Ingress e Egress para o tipo de redundância da conta de armazenamento e região.

Verifique os limites de throughput dos VHDs anexados à VM. Adicione o disco vm métricas ler e gravar.

Novas ofertas de disco o armazenamento Padrão têm diferentes limites de IOPS e throughput (IOPS não são expostos por VHD). Veja os dados para ver se você está atingindo os limites de MB de throughput combinado dos VHD(s) no nível de VM usando Leitura e Gravação de Disco e, em seguida, otimizar sua configuração de armazenamento VM para escalar os limites de VHD únicos. Leia mais sobre diferentes ofertas padrão de disco de armazenamento e limites por disco:

* [Metas de escalabilidade e desempenho para discos VM no Windows](https://docs.microsoft.com/azure/virtual-machines/windows/disk-scalability-targets).

### <a name="high-disk-utilizationlatency-remediation"></a>Alta utilização/remediação de latência de disco

Reduza a latência do cliente e otimize o IO vm para escalar os limites de VHD passados

* [Otimização de IO para Windows no Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-performance-best-practices/)

* [Otimização de IO para Linux no Azure](https://blogs.msdn.microsoft.com/igorpag/2014/10/23/azure-storage-secrets-and-linux-io-optimizations/)

#### <a name="reduce-throttling"></a>Reduzir o estrangulamento

Se atingir limites superiores das contas de armazenamento, reequilibre os VHDs entre contas de armazenamento. Consulte [as metas de escalabilidade e desempenho do armazenamento do Azure](https://azure.microsoft.com/documentation/articles/storage-scalability-targets/).

### <a name="increase-throughput-and-reduce-latency"></a>Aumentar o throughput e reduzir a latência

Se você tiver um aplicativo sensível à latência e exigir alta taxa de inscrição, migre seus VHDs para o armazenamento Azure Premium usando a VM da série DS e GS.

Estes artigos discutem os cenários específicos:

* [Migrando para o Armazenamento do Azure Premium](https://azure.microsoft.com/documentation/articles/storage-migration-to-premium-storage/)

* [Use o armazenamento premium do Azure com o SQL Server](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-use-premium-storage/)

## <a name="next-steps"></a>Próximas etapas

Se você precisar de mais ajuda em qualquer momento deste artigo, entre em contato com os especialistas do Azure nos [fóruns MSDN Azure e Stack Overflow](https://azure.microsoft.com/support/forums/).

Alternativamente, registre um incidente de suporte ao Azure. Vá ao site de suporte do [Azure](https://azure.microsoft.com/support/options/) e **selecione Obter suporte**.
