---
title: Como mapear o desempenho com o Monitor do Azure para VMs
description: O desempenho é um recurso do Monitor do Azure para VMs que descobre automaticamente os componentes do aplicativo nos sistemas Windows e Linux e mapeia a comunicação entre os serviços. Este artigo fornece detalhes sobre como usá-lo em vários cenários.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/15/2019
ms.openlocfilehash: a50ba39777e6a9d3d609e584c0c7d872f2a65f35
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283711"
---
# <a name="how-to-chart-performance-with-azure-monitor-for-vms"></a>Como mapear o desempenho com o Monitor do Azure para VMs

O Monitor do Azure para VMs inclui um conjunto de gráficos de desempenho que segmentam vários KPIs (principais indicadores de desempenho) para ajudá-lo a determinar o desempenho de uma máquina virtual. Os gráficos mostram a utilização de recursos durante um período de tempo para que você possa identificar afunilamentos, anomalias ou alternar para uma perspectiva listando cada máquina para exibir a utilização de recursos com base na métrica selecionada. Embora existam inúmeros elementos a considerar ao lidar com o desempenho, o Azure Monitor para VMs monitora os principais indicadores de desempenho do sistema operacional relacionados ao processador, memória, adaptador de rede e utilização de disco. O desempenho complementa o recurso de monitoramento de integridade e ajuda a expor problemas que indicam uma possível falha do componente do sistema, suporte ao ajuste e otimização para obter eficiência ou suportar o planejamento da capacidade.  

## <a name="multi-vm-perspective-from-azure-monitor"></a>Perspectiva de várias VMs do Azure Monitor

A partir do Azure Monitor, o recurso Performance fornece uma visão de todas as VMs monitoradas implantadas em grupos de trabalho em suas assinaturas ou em seu ambiente. Para acessar do Monitor do Azure, execute as etapas a seguir. 

1. No portal Azure, selecione **Monitor**. 
2. Escolha **Máquinas Virtuais** na seção **Soluções.**
3. Selecione o **desempenho** guia.

![Exibição de desempenho superior N lista de insights VM](media/vminsights-performance/vminsights-performance-aggview-01.png)

Na guia **Top N Charts**, se você tiver mais de um espaço de trabalho do Log Analytics, escolha a área de trabalho habilitada com a solução do seletor **Workspace** na parte superior da página. O seletor **Grupo** retornará assinaturas, grupos de recursos, [grupos de computadores](../platform/computer-groups.md) e conjuntos de computadores em escala virtual relacionados ao workspace selecionado que você pode usar para filtrar ainda mais os resultados apresentados nos gráficos esta página e nas outras páginas. Sua seleção só se aplica ao recurso Performance e não é transferida para Health ou Map.  

Por padrão, os gráficos mostram as últimas 24 horas. Usando o seletor **TimeRange**, você pode consultar intervalos de tempo históricos de até 30 dias para mostrar como o desempenho parecia no passado.

Os gráficos de utilização de cinco capacidade mostrados na página são:

* % De utilização da CPU - mostra as cinco principais máquinas com a utilização média mais alta do processador 
* Memória disponível - mostra as cinco principais máquinas com a menor quantidade média de memória disponível 
* Espaço em disco lógico usado% - mostra as cinco principais máquinas com o maior espaço em disco usado% em todos os volumes de disco 
* Bytes Sent Rate - mostra as cinco principais máquinas com a maior média de bytes enviados 
* Bytes Receive Rate - mostra as cinco melhores máquinas com maior média de bytes recebidos 

Clicar no ícone de pino no canto superior direito de qualquer um dos cinco gráficos fixará o gráfico selecionado no último painel do Azure que você viu pela última vez.  No painel de controle, você pode redimensionar e reposicionar o gráfico. A seleção do gráfico no painel irá redirecioná-lo para o Monitor Azure para VMs e carregar o escopo e a exibição corretos.  

Clicar no ícone localizado à esquerda do ícone do pino em qualquer um dos cinco gráficos abre a exibição **Da Lista N** superior.  Aqui você pode ver a utilização de recursos para essa métrica de desempenho por VM individual em uma exibição de lista e qual máquina está tendências mais alta.  

![Exibição de lista de N superior de uma métrica de desempenho selecionados](media/vminsights-performance/vminsights-performance-topnlist-01.png)

Quando você clica na máquina virtual, o painel **Propriedades** é expandido no direito de mostrar as propriedades do item selecionado, como informações do sistema relatadas pelo sistema operacional, propriedades da VM do Azure, etc. Clicar em uma das opções na seção **Links Rápidos** irá redirecioná-lo para esse recurso diretamente da VM selecionada.  

![Painel de propriedades da máquina virtual](./media/vminsights-performance/vminsights-properties-pane-01.png)

Alterne para a guia **Gráficos agregados** para visualizar as métricas de desempenho filtradas por medidas médias ou de percentis.  

![Visão geral do desempenho das informações de VM](./media/vminsights-performance/vminsights-performance-aggview-02.png)

Os seguintes gráficos de utilização de capacidade são fornecidos:

* CPU Utilization% - padrões que mostram o percentual médio e superior 95 
* Memória Disponível - padrões mostrando a média, o 5º e o 10º percentil 
* Espaço em disco lógico usado% - padrões mostrando a média e o percentil 95 
* Bytes Sent Rate - padrões mostrando os bytes médios enviados 
* Taxa de recebimento de bytes - padrões que mostra a média de bytes recebidos

Você também pode alterar a granularidade dos gráficos dentro do intervalo de tempo, selecionando **Avg**, **Min**, **Max**, **percentis 50**, ** 90 º**, e **95 º** no seletor de percentil.

Para visualizar a utilização de recursos por VM individual em uma exibição de lista e ver qual máquina está em tendência com maior utilização, selecione a guia **Lista N** superior.  A página **Top N List** mostra as 20 melhores máquinas classificadas pelas mais utilizadas pelo percentil 95 para a porcentagem métrica de utilização da CPU *%*.  Você pode ver mais máquinas, selecionando **carga mais**, e os resultados se expandem para mostrar as máquinas da parte superior a 500. 

>[!NOTE]
>A lista não é possível mostrar mais de 500 máquinas cada vez.  
>

![Exemplo de página de lista de N superior](./media/vminsights-performance/vminsights-performance-topnlist-01.png)

Para filtrar os resultados em uma máquina virtual específica na lista, insira o nome do computador na caixa de texto **Pesquisar por nome**.  

Se você em vez disso, seria exibir utilização de uma métrica de desempenho diferentes, do **métrica** lista suspensa, selecione **memória disponível**, **% de espaço em disco lógico usado**, ** Bytes/s de recebimento de rede**, ou **bytes/s de rede enviados** e a lista é atualizada para mostrar a utilização no escopo dessa métrica.  

A seleção de uma máquina virtual a partir da lista abre o painel **Propriedades** no lado direito da página e, a partir daqui, você pode selecionar **Detalhes de desempenho**.  A página **Detalhes da Máquina Virtual** é aberta e tem o escopo definido para essa VM, com experiência semelhante ao acessar o VM Insights Performance diretamente da VM do Azure.  

## <a name="view-performance-directly-from-an-azure-vm"></a>Exibir desempenho diretamente de uma VM do Azure

Para acessar diretamente de uma máquina virtual, execute as seguintes etapas.

1. No Portal do Azure, selecione **Máquinas Virtuais**. 
2. Na lista, escolha uma VM e na seção **Monitoramento** escolha **Insights**.  
3. Selecione o **desempenho** guia. 

Esta página inclui não apenas gráficos de utilização de desempenho, mas também uma tabela mostrando cada disco lógico descoberto, sua capacidade, utilização e média total de cada medida.  

Os seguintes gráficos de utilização de capacidade são fornecidos:

* CPU Utilization% - padrões que mostram o percentual médio e superior 95 
* Memória Disponível - padrões mostrando a média, o 5º e o 10º percentil 
* Espaço em disco lógico usado% - padrões mostrando a média e o percentil 95 
* IOPS de disco lógico - padrões mostrando a média e o percentil 95
* MB / s de disco lógico - padrões mostrando a média e o percentil 95
* Max Logical Disk Used% - padrões mostrando a média e o percentil 95
* Bytes Sent Rate - padrões mostrando os bytes médios enviados 
* Taxa de recebimento de bytes - padrões que mostra a média de bytes recebidos

Clicando no ícone do pino no canto superior direito de qualquer um dos gráficos, o gráfico selecionado para o último painel do Azure que você visualizou. No painel de controle, você pode redimensionar e reposicionar o gráfico. Selecionar o gráfico no painel de controle redireciona você para o Monitor Azure para VMs e carrega a visualização detalhada de desempenho para a VM.  

![Diretamente o insights VM-desempenho da VM a exibir](./media/vminsights-performance/vminsights-performance-directvm-01.png)

## <a name="view-performance-directly-from-an-azure-virtual-machine-scale-set"></a>Exibir o desempenho diretamente de um conjunto de escala de máquina virtual do Azure

Para acessar diretamente a partir de um conjunto de escalade máquina virtual do Azure, execute as seguintes etapas.

1. No portal Azure, selecione **Conjuntos de escalas de máquinas virtuais**.
2. Na lista, escolha uma VM e na seção **Monitoramento** escolha **Insights** para exibir a guia **Desempenho.**

Esta página carrega a exibição de desempenho do Monitor Do Azure, escopo do conjunto de escalas selecionado. Isso permite que você veja as Instâncias N principais no conjunto de escala situado no conjunto de métricas monitoradas, visualize o desempenho agregado em todo o conjunto de escalas e veja as tendências das métricas selecionadas nas instâncias individuais no conjunto de escalas. Selecionar uma instância na exibição da lista permite que você carregue seu mapa ou navegue em uma exibição detalhada de desempenho para essa instância.

Clicando no ícone do pino no canto superior direito de qualquer um dos gráficos, o gráfico selecionado para o último painel do Azure que você visualizou. No painel de controle, você pode redimensionar e reposicionar o gráfico. Selecionar o gráfico no painel de controle redireciona você para o Monitor Azure para VMs e carrega a visualização detalhada de desempenho para a VM.  

![VM insights Desempenho diretamente da exibição do conjunto de escala de máquina virtual](./media/vminsights-performance/vminsights-performance-directvmss-01.png)

>[!NOTE]
>Você também pode acessar uma exibição detalhada de desempenho para uma instância específica a partir da exibição Instâncias para o conjunto de escalas. Navegue até **Instâncias** na seção **Configurações** e, em seguida, escolha **Insights**.



## <a name="next-steps"></a>Próximas etapas

- Aprenda a usar [as workbooks](vminsights-workbooks.md) incluídas no Monitor Azure para VMs para analisar ainda mais o desempenho e as métricas de rede.  

- Para saber mais sobre as dependências descobertas dos aplicativos, consulte [Exibir o Monitor do Azure para VMs Map](vminsights-maps.md).
