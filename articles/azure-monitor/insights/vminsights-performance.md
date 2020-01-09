---
title: Como mapear o desempenho com o Azure Monitor para VMs (versão prévia) | Microsoft Docs
description: O desempenho é um recurso do Monitor do Azure para VMs que descobre automaticamente os componentes do aplicativo nos sistemas Windows e Linux e mapeia a comunicação entre os serviços. Este artigo fornece detalhes sobre como usá-lo em vários cenários.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/15/2019
ms.openlocfilehash: 0d679675758b736455c66066f3df4cb9ea43fdea
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75399291"
---
# <a name="how-to-chart-performance-with-azure-monitor-for-vms-preview"></a>Como mapear o desempenho com o Azure Monitor para VMs (versão prévia)

O Monitor do Azure para VMs inclui um conjunto de gráficos de desempenho que segmentam vários KPIs (principais indicadores de desempenho) para ajudá-lo a determinar o desempenho de uma máquina virtual. Os gráficos mostram a utilização de recursos durante um período de tempo para que você possa identificar afunilamentos, anomalias ou alternar para uma perspectiva listando cada máquina para exibir a utilização de recursos com base na métrica selecionada. Embora haja inúmeros elementos a serem considerados ao lidar com o desempenho, o Azure Monitor para VMs monitora os principais indicadores de desempenho do sistema operacional relacionados ao processador, à memória, ao adaptador de rede e à utilização de disco. O desempenho complementa o recurso de monitoramento de integridade e ajuda a expor problemas que indicam uma possível falha do componente do sistema, suporte ao ajuste e otimização para obter eficiência ou suportar o planejamento da capacidade.  

## <a name="multi-vm-perspective-from-azure-monitor"></a>Perspectiva de várias VMs do Azure Monitor

Do Azure Monitor, o recurso de desempenho fornece uma exibição de todas as VMs monitoradas implantadas em grupos de recursos em suas assinaturas ou em seu ambiente. Para acessar do Monitor do Azure, execute as etapas a seguir. 

1. No portal do Azure, selecione **Monitor**. 
2. Escolher **máquinas virtuais (versão prévia)** na **soluções** seção.
3. Selecione o **desempenho** guia.

![Exibição de desempenho superior N lista de insights VM](./media/vminsights-performance/vminsights-performance-aggview-01.png)

Na guia **Top N Charts**, se você tiver mais de um espaço de trabalho do Log Analytics, escolha a área de trabalho habilitada com a solução do seletor **Workspace** na parte superior da página. O seletor **Grupo** retornará assinaturas, grupos de recursos, [grupos de computadores](../platform/computer-groups.md) e conjuntos de computadores em escala virtual relacionados ao workspace selecionado que você pode usar para filtrar ainda mais os resultados apresentados nos gráficos esta página e nas outras páginas. Sua seleção só se aplica ao recurso Performance e não é transferida para Health ou Map.  

Por padrão, os gráficos mostram as últimas 24 horas. Usando o seletor **TimeRange**, você pode consultar intervalos de tempo históricos de até 30 dias para mostrar como o desempenho parecia no passado.

Os gráficos de utilização de cinco capacidade mostrados na página são:

* % De utilização da CPU - mostra as cinco principais máquinas com a utilização média mais alta do processador 
* Memória disponível - mostra as cinco principais máquinas com a menor quantidade média de memória disponível 
* Espaço em disco lógico usado% - mostra as cinco principais máquinas com o maior espaço em disco usado% em todos os volumes de disco 
* Bytes Sent Rate - mostra as cinco principais máquinas com a maior média de bytes enviados 
* Taxa de recebimento de bytes-mostra os cinco principais computadores com a média mais alta de bytes recebidos 

Clicar no ícone de pino no canto superior direito de qualquer um dos cinco gráficos fixará o gráfico selecionado no último painel do Azure que você exibiu pela última vez.  No painel, você pode redimensionar e reposicionar o gráfico. A seleção do gráfico no painel irá redirecioná-lo para Azure Monitor para VMs e carregar o escopo e a exibição corretos.  

Clicar no ícone localizado à esquerda do ícone de pino em qualquer um dos cinco gráficos abre a exibição de **lista N mais alta** .  Aqui você pode ver a utilização de recursos para essa métrica de desempenho por VM individual em uma exibição de lista e qual máquina está tendências mais alta.  

![Exibição de lista de N superior de uma métrica de desempenho selecionados](./media/vminsights-performance/vminsights-performance-topnlist-01.png)

Quando você clica na máquina virtual, o painel **Propriedades** é expandido à direita para mostrar as propriedades do item selecionado, como informações do sistema relatadas pelo sistema operacional, propriedades da VM do Azure etc. Ao clicar em uma das opções na seção **links rápidos** , você será redirecionado para esse recurso diretamente da VM selecionada.  

![Painel de propriedades da máquina virtual](./media/vminsights-performance/vminsights-properties-pane-01.png)

Alterne para a guia **Gráficos agregados** para visualizar as métricas de desempenho filtradas por medidas médias ou de percentis.  

![Visão geral do desempenho das informações de VM](./media/vminsights-performance/vminsights-performance-aggview-02.png)

Os seguintes gráficos de utilização de capacidade são fornecidos:

* CPU Utilization% - padrões que mostram o percentual médio e superior 95 
* Memória disponível-padrões mostrando a média, o 5 primeiros e o 10º percentil 
* Espaço em disco lógico usado% - padrões mostrando a média e o percentil 95 
* Bytes Sent Rate - padrões mostrando os bytes médios enviados 
* Taxa de recebimento de bytes - padrões que mostra a média de bytes recebidos

Você também pode alterar a granularidade dos gráficos dentro do intervalo de tempo, selecionando **Avg**, **Min**, **Max**, **percentis 50**,  **90 º**, e **95 º** no seletor de percentil.

Para exibir a utilização de recursos por VM individual em um modo de exibição de lista e ver qual computador está se tendência com maior utilização, selecione a guia **lista N superior** .  A página da **lista N superior** mostra os 20 principais computadores classificados pelo mais utilizado pelo 95 º percentil para a% de *utilização da CPU*de métrica.  Você pode ver mais máquinas, selecionando **carga mais**, e os resultados se expandem para mostrar as máquinas da parte superior a 500. 

>[!NOTE]
>A lista não é possível mostrar mais de 500 máquinas cada vez.  
>

![Exemplo de página de lista de N superior](./media/vminsights-performance/vminsights-performance-topnlist-01.png)

Para filtrar os resultados em uma máquina virtual específica na lista, insira o nome do computador na caixa de texto **Pesquisar por nome**.  

Se você em vez disso, seria exibir utilização de uma métrica de desempenho diferentes, do **métrica** lista suspensa, selecione **memória disponível**, **% de espaço em disco lógico usado**,  **Bytes/s de recebimento de rede**, ou **bytes/s de rede enviados** e a lista é atualizada para mostrar a utilização no escopo dessa métrica.  

A seleção de uma máquina virtual a partir da lista abre o painel **Propriedades** no lado direito da página e, a partir daqui, você pode selecionar **Detalhes de desempenho**.  A página **Detalhes da Máquina Virtual** é aberta e tem o escopo definido para essa VM, com experiência semelhante ao acessar o VM Insights Performance diretamente da VM do Azure.  

## <a name="view-performance-directly-from-an-azure-vm"></a>Exibir desempenho diretamente de uma VM do Azure

Para acessar diretamente de uma máquina virtual, execute as etapas a seguir.

1. No Portal do Azure, selecione **Máquinas Virtuais**. 
2. Na lista, escolha uma máquina virtual e, na **Monitoring** seção escolher **Insights (visualização)** .  
3. Selecione o **desempenho** guia. 

Esta página inclui não apenas gráficos de utilização de desempenho, mas também uma tabela mostrando cada disco lógico descoberto, sua capacidade, utilização e média total de cada medida.  

Os seguintes gráficos de utilização de capacidade são fornecidos:

* CPU Utilization% - padrões que mostram o percentual médio e superior 95 
* Memória disponível-padrões mostrando a média, o 5 primeiros e o 10º percentil 
* Espaço em disco lógico usado% - padrões mostrando a média e o percentil 95 
* IOPS de disco lógico - padrões mostrando a média e o percentil 95
* MB / s de disco lógico - padrões mostrando a média e o percentil 95
* Max Logical Disk Used% - padrões mostrando a média e o percentil 95
* Bytes Sent Rate - padrões mostrando os bytes médios enviados 
* Taxa de recebimento de bytes - padrões que mostra a média de bytes recebidos

Clicar no ícone de pino no canto superior direito de qualquer um dos gráficos fixa o gráfico selecionado ao último painel do Azure exibido. No painel, você pode redimensionar e reposicionar o gráfico. A seleção do gráfico no painel redireciona você para Azure Monitor para VMs e carrega a exibição de detalhes de desempenho da VM.  

![Diretamente o insights VM-desempenho da VM a exibir](./media/vminsights-performance/vminsights-performance-directvm-01.png)

## <a name="view-performance-directly-from-an-azure-virtual-machine-scale-set"></a>Exibir o desempenho diretamente de um conjunto de dimensionamento de máquinas virtuais do Azure

Para acessar diretamente de um conjunto de dimensionamento de máquinas virtuais do Azure, execute as etapas a seguir.

1. Na portal do Azure, selecione **conjuntos de dimensionamento de máquinas virtuais**.
2. Na lista, escolha uma VM e, na seção **monitoramento** , escolha **insights (versão prévia)** para exibir a guia **desempenho** .

Esta página carrega a exibição de desempenho Azure Monitor, no escopo do conjunto de dimensionamento selecionado. Isso permite que você veja as primeiras N instâncias no conjunto de dimensionamento no conjunto de métricas monitoradas, exiba o desempenho agregado em todo o conjunto de dimensionamento e veja as tendências para métricas selecionadas em todas as instâncias individuais N o conjunto de dimensionamento. A seleção de uma instância na exibição de lista permite que você carregue o mapa ou navegue até um modo de exibição de desempenho detalhado para essa instância.

Clicar no ícone de pino no canto superior direito de qualquer um dos gráficos fixa o gráfico selecionado ao último painel do Azure exibido. No painel, você pode redimensionar e reposicionar o gráfico. A seleção do gráfico no painel redireciona você para Azure Monitor para VMs e carrega a exibição de detalhes de desempenho da VM.  

![Desempenho de informações da VM diretamente da exibição do conjunto de dimensionamento de máquinas virtuais](./media/vminsights-performance/vminsights-performance-directvmss-01.png)

>[!NOTE]
>Você também pode acessar um modo de exibição de desempenho detalhado para uma instância específica do modo de exibição de instâncias para seu conjunto de dimensionamento. Navegue até **instâncias** na seção **configurações** e, em seguida, escolha **insights (versão prévia)** .

## <a name="alerts"></a>Alertas  

As métricas de desempenho ativadas como parte do Monitor do Azure para VMs não incluem regras de alerta pré-configuradas. Há [alertas de integridade](vminsights-health.md#alerts) correspondentes aos problemas de desempenho detectados em sua VM do Azure, como alta utilização da CPU, memória insuficiente disponível, pouco espaço em disco, etc.  No entanto, esses alertas de integridade se aplicam somente a todas as VMs habilitadas para Azure Monitor para VMs. 

No entanto, só podemos coletar e armazenar um subconjunto das métricas de desempenho necessárias no espaço de trabalho do Log Analytics. Se sua estratégia de monitoramento exigir análise ou alerta que inclua outras métricas de desempenho para avaliar efetivamente a capacidade ou a integridade da máquina virtual, ou você precisar da flexibilidade para especificar seus próprios critérios de alerta ou lógica, é possível configurar a [coleta desses desempenhos contadores](../platform/data-sources-performance-counters.md) no Log Analytics e definir [alertas de log](../platform/alerts-log.md). Embora o Log Analytics permita que você realize análises complexas com outros tipos de dados e forneça uma retenção mais longa para suportar a análise de tendências, as métricas, por outro lado, são leves e capazes de suportar cenários quase em tempo real. Eles são coletados pelo [Agente de Diagnóstico do Azure](../../virtual-machines/windows/monitor.md) e armazenados no repositório de métricas do Monitor do Azure, permitindo que você crie alertas com menor latência e a um custo menor.

Revise a visão geral da [coleção de métricas e logs com o Monitor do Azure](../platform/data-platform.md) para entender melhor as diferenças fundamentais e outras considerações antes de configurar a coleta dessas métricas adicionais e regras de alerta.  

## <a name="next-steps"></a>Próximos passos

- Saiba como usar [pastas de trabalho](vminsights-workbooks.md) incluídas com o Azure monitor para VMs para analisar ainda mais as métricas de desempenho e rede.  

- Para saber mais sobre dependências de aplicativo descobertas, consulte [exibir mapa de Azure monitor para VMs](vminsights-maps.md).
