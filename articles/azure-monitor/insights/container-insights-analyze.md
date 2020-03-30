---
title: Kubernetes monitorando com Monitor Azure para contêineres | Microsoft Docs
description: Este artigo descreve como você pode visualizar e analisar o desempenho de um cluster Kubernetes com o Azure Monitor para contêineres.
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: 227fe70512536790d179797394b6fba22e7eb50e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298366"
---
# <a name="monitor-your-kubernetes-cluster-performance-with-azure-monitor-for-containers"></a>Monitore o desempenho do cluster Kubernetes com o Monitor Azure para contêineres

Com o Azure Monitor para contêineres, você pode usar os gráficos de desempenho e o status de saúde para monitorar a carga de trabalho dos clusters Kubernetes hospedados no Azure Kubernetes Service (AKS), Azure Stack ou em outro ambiente a partir de duas perspectivas. Você pode monitorar diretamente a partir do cluster, ou você pode visualizar todos os clusters em uma assinatura do Azure Monitor. A visualização de instâncias de contêiner do Azure também é possível ao monitorar um cluster AKS específico.

Este artigo ajuda você a entender as duas perspectivas e como o Azure Monitor ajuda você a avaliar, investigar e resolver problemas detectados rapidamente.

Para obter informações sobre como ativar o Monitor Azure para contêineres, consulte [Onboard Azure Monitor para contêineres](container-insights-onboard.md).

O Azure Monitor fornece uma visão de vários clusters que mostra o estado de saúde de todos os clusters Kubernetes monitorados que executam o Linux e o Windows Server 2019 implantados entre grupos de recursos em suas assinaturas. Ele mostra clusters descobertos em todos os ambientes que não são monitorados pela solução. Você pode entender imediatamente a saúde do cluster e, a partir daqui, você pode detalhar até a página de desempenho do nó e do controlador ou navegar para ver gráficos de desempenho para o cluster. Para clusters AKS que foram descobertos e identificados como não monitorados, você pode habilitar o monitoramento para eles a qualquer momento. 

As principais diferenças no monitoramento de um cluster do Windows Server com o Azure Monitor para contêineres em comparação com um cluster Linux são descritas [aqui](container-insights-overview.md#what-does-azure-monitor-for-containers-provide) no artigo visão geral.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Faça login no [portal Azure](https://portal.azure.com). 

## <a name="multi-cluster-view-from-azure-monitor"></a>Exibição de vários clusters do Azure Monitor

Para ver o estado de saúde de todos os clusters Kubernetes implantados, selecione **Monitor** do painel esquerdo no portal Azure. Na seção **Insights**, selecione **Contêineres**. 

![Exemplo de painel com vários clusters no Azure Monitor](./media/container-insights-analyze/azmon-containers-multiview.png)

Você pode escopo dos resultados apresentados na grade para mostrar clusters que são:

* **Clusters Azure** - AKS e AKS-Engine hospedados no Azure Kubernetes Service
* **Azure Stack (Visualização)** - Clusters AKS-Engine hospedados no Azure Stack
* **Non-Azure (Preview)** - Clusters Kubernetes hospedados no local
* **Tudo** - Veja todos os clusters Kubernetes hospedados no Azure, Azure Stack e ambientes locais que estão a bordo do Azure Monitor para contêineres

Para visualizar clusters de um ambiente específico, selecione-os na pílula **Ambientes** no canto superior esquerdo da página.

![Exemplo de seletor de pílula de ambiente](./media/container-insights-analyze/clusters-multiview-environment-pill.png)

Na guia **Clusters Monitorados,** você aprende o seguinte:

- Quantos clusters estão em estado crítico ou insalubre, versus quantos estão saudáveis ou não relatando (referido como um estado desconhecido).
- Se todas as implantações do [Azure Kubernetes Engine (AKS-engine)](https://github.com/Azure/aks-engine) são saudáveis.
- Quantos nós e pods de usuário e sistema são implantados por cluster.
- Quanto espaço em disco está disponível e se há um problema de capacidade.

Os status de integridade incluídos são: 

* **Saudável**: Não são detectados problemas para a VM, e está funcionando conforme necessário. 
* **Crítico**: São detectados problemas críticos ou mais críticos que devem ser abordados para restaurar o estado operacional normal, como esperado.
* **Atenção**: São detectados um ou mais problemas que devem ser abordados ou a condição de saúde pode se tornar crítica.
* **Desconhecido**: Se o serviço não foi capaz de fazer uma conexão com o nó ou pod, o status muda para um estado desconhecido.
* **Não encontrado**: O espaço de trabalho, o grupo de recursos ou a assinatura que contém o espaço de trabalho para esta solução foram excluídos.
* **Não autorizado**: O usuário não tem permissões necessárias para ler os dados no espaço de trabalho.
* **Erro**: Ocorreu um erro ao tentar ler dados do espaço de trabalho.
* **Mal configurado**: O Monitor Azure para contêineres não foi configurado corretamente no espaço de trabalho especificado.
* **Dados**: Os dados não foram reportados ao espaço de trabalho nos últimos 30 minutos.

O estado de saúde calcula o estado geral do cluster como o *pior dos* três estados com uma exceção. Se algum dos três estados for desconhecido, o estado geral do cluster mostra **Desconhecido.** 

A tabela a seguir fornece uma análise do cálculo que controla os estados de saúde para um cluster monitorado na visão multi-cluster.

| |Status |Disponibilidade |  
|-------|-------|-----------------|  
|**Pod de usuário**| | |  
| |Healthy |100% |  
| |Aviso |90 – 99% |  
| |Crítico |<90% |  
| |Unknown (desconhecido) |Se não tiver sido relatado nos últimos 30 minutos |  
|**Pod do sistema**| | |  
| |Healthy |100% |
| |Aviso |N/D |
| |Crítico |<100% |
| |Unknown (desconhecido) |Se não tiver sido relatado nos últimos 30 minutos |
|**Nó** | | |
| |Healthy |>85% |
| |Aviso |60 – 84% |
| |Crítico |<60% |
| |Unknown (desconhecido) |Se não tiver sido relatado nos últimos 30 minutos |

Na lista de clusters, você pode detalhar até a página **Cluster** selecionando o nome do cluster. Em seguida, vá para a página de desempenho **Nodes** selecionando o rollup de álos na coluna **'''''Dedos'** para esse cluster específico. Ou, você pode detalhar a página de desempenho **dos Controladores** selecionando o rollup dos **pods** do usuário ou da coluna **Pods do Sistema.**

## <a name="view-performance-directly-from-a-cluster"></a>Exibir o desempenho diretamente de um cluster

O acesso ao Monitor Azure para contêineres está disponível diretamente a partir de um cluster AKS selecionando**O Cluster** **Insights** > no painel esquerdo ou quando você selecionou um cluster a partir da exibição de vários clusters. As informações sobre seu cluster estão organizadas em quatro perspectivas:

- Cluster
- Nós 
- Controladores 
- Contêineres

>[!NOTE]
>A experiência descrita no restante deste artigo também é aplicável para visualizar o desempenho e o estado de saúde de seus clusters Kubernetes hospedados no Azure Stack ou em outro ambiente quando selecionados a partir da exibição de vários clusters. 

A página padrão abre e exibe quatro gráficos de desempenho de linha que mostram as principais métricas de desempenho do seu cluster. 

![Gráficos de desempenho de exemplo na guia de Cluster](./media/container-insights-analyze/containers-cluster-perfview.png)

Os gráficos de desempenho exibem quatro métricas de desempenho:

- **Utilização da&nbsp;CPU do nó**: Uma perspectiva agregada da utilização da CPU para todo o cluster. Para filtrar os resultados para o intervalo de tempo, selecione **Avg**, **Min**, **50th**, **90th**, **95th**, ou **Max** no seletor de percentis acima do gráfico. Os filtros podem ser usados individualmente ou combinados. 
- **Utilização da&nbsp;memória do nó**: Uma perspectiva agregada de utilização da memória para todo o cluster. Para filtrar os resultados para o intervalo de tempo, selecione **Avg**, **Min**, **50th**, **90th**, **95th**, ou **Max** no seletor de percentis acima do gráfico. Os filtros podem ser usados individualmente ou combinados. 
- **Contagem de nós**: uma contagem de nós e o status do Kubernetes. Os status dos nós de cluster representados são Total, Ready e Not Ready. Eles podem ser filtrados individualmente ou combinados no seletor acima do gráfico. 
- **Contagem de cápsulas ativas**: Uma contagem de pods e status de Kubernetes. Os status dos pods representados são Total, Pendente, Em execução, Desconhecido, Bem Sucedido ou Falho. Eles podem ser filtrados individualmente ou combinados no seletor acima do gráfico. 

Use as teclas de seta esquerda e direita para percorrer cada ponto de dados do gráfico. Use as teclas de seta para cima e para baixo para percorrer as linhas de percentil. Selecione o ícone de pino no canto superior direito de qualquer um dos gráficos para fixar o gráfico selecionado no último painel azure que você visualizou. No painel de controle, você pode redimensionar e reposicionar o gráfico. Selecionar o gráfico no painel de controle redireciona você para o Monitor Do Azure para contêineres e carrega o escopo e a exibição corretos.

O Azure Monitor para contêineres também suporta o explorador de métricas do Azure Monitor, onde você pode criar seus [próprios gráficos](../platform/metrics-getting-started.md)de enredo, correlacionar e investigar tendências e fixar em dashboards. A partir do explorador de métricas, você também pode usar os critérios que você definiu para visualizar suas métricas como base de uma [regra de alerta baseada em métricas](../platform/alerts-metric.md). 

## <a name="view-container-metrics-in-metrics-explorer"></a>Exibir métricas de contêiner no explorador de métricas

No explorador de métricas, você pode visualizar métricas de utilização de nó e pod agregadas do Azure Monitor para contêineres. A tabela a seguir resume os detalhes para ajudá-lo a entender como usar os gráficos métricos para visualizar métricas de contêineres.

|Namespace | Métrica | Descrição | 
|----------|--------|-------------|
| insights.container/nós | |
| | cpuUsageMillicores | Medição agregada da utilização da CPU em todo o cluster. É um núcleo de CPU dividido em 1000 unidades (mili = 1000). Usado para determinar o uso de núcleos em um recipiente onde muitas aplicações podem estar usando um núcleo.| 
| | cpuUsagePercentage | Utilização média da CPU agregada medida em porcentagem em todo o cluster.|
| | memoryRssBytes | Memória RSS do recipiente usada em bytes.| 
| | memoryRssPercentage | Memória RSS do contêiner usada em porcentagem.|
| | memóriaWorkingSetBytes | Memória do conjunto de trabalho do contêiner usada.| 
| | memóriaConfiguração do conjunto de trabalho | Memória do conjunto de trabalho do contêiner usada em porcentagem. | 
| | nódulosContagem | Uma contagem de nódulos de Kubernetes.|
| insights.container/pods | |
| | Contagem de pods | Uma contagem de cápsulas de Kubernetes.|

Você pode [dividir](../platform/metrics-charts.md#apply-splitting-to-a-chart) uma métrica para vê-la por dimensão e visualizar como diferentes segmentos dela se comparam entre si. Para um nó, você pode segmentar o gráfico pela dimensão *do host.* A partir de um pod, você pode segmentá-lo pelas seguintes dimensões:

* Controller
* Kubernetes namespace
* Nó
* Fase

## <a name="analyze-nodes-controllers-and-container-health"></a>Analisar nódulos, controladores e saúde de contêineres

Quando você alterna para as **guias 'Nós,** **Controladores'** e **Contêineres',** um painel de propriedade é exibido automaticamente no lado direito da página. Ele mostra as propriedades do item selecionado, que inclui os rótulos definidos para organizar objetos Kubernetes. Quando um nó Linux é selecionado, a seção **Capacidade de disco local** também mostra o espaço de disco disponível e a porcentagem usada para cada disco apresentado ao nó. Selecione **>>** o link no painel para visualizar ou ocultar o painel.

Como expandir os objetos na hierarquia, as atualizações de painel de propriedades com base no objeto selecionado. No painel, você também pode visualizar registros de contêineres kubernetes (stdout/stderror), eventos e métricas de pod selecionando o link **Exibir dados ao vivo (visualização)** na parte superior do painel. Para obter mais informações sobre a configuração necessária para conceder e controlar o acesso para visualizar esses dados, consulte [Configurar os dados ao vivo (visualização)](container-insights-livedata-setup.md). Enquanto você analisa os recursos do cluster, você pode ver esses dados do contêiner em tempo real. Para obter mais informações sobre esse recurso, consulte [Como visualizar registros, eventos e métricas de pod do Kubernetes em tempo real](container-insights-livedata-overview.md). Para exibir os dados de log do Kubernetes armazenados em seu espaço de trabalho com base em pesquisas de log **pré-definidas, selecione Exibir registros** de contêineres da lista de desoneração do View in **analytics.** Para obter informações adicionais sobre este tópico, consulte [os registros de pesquisa para analisar dados](container-insights-log-search.md#search-logs-to-analyze-data).

Use a opção **+ Adicionar filtro** na parte superior da página para filtrar os resultados para a exibição por **Serviço**, **Nó,** **Namespace**ou **Pool de Nó**. Depois de selecionar o escopo do filtro, selecione um dos valores mostrados no campo **Selecionar valores.** Depois que o filtro é configurado, ele é aplicado globalmente enquanto visualiza qualquer perspectiva do cluster AKS. A fórmula dá suporte apenas ao sinal de igual. Você pode adicionar mais filtros depois do primeiro para restringir ainda mais os resultados. Por exemplo, se você especificar um filtro por **Nó,** você só poderá selecionar **Serviço** ou **Namespace** para o segundo filtro.

A especificação de um filtro em uma guia continua a ser aplicada quando você seleciona outra. Ele é excluído depois de selecionar o símbolo **x** ao lado do filtro especificado. 

Mude para a guia **'Nodes'** e a hierarquia da linha segue o modelo de objeto Kubernetes, que começa com um nó no seu cluster. Expanda o nó para exibir uma ou mais cápsulas em execução no nó. Se mais de um contêiner for agrupado a um pod, eles são exibidos como a última linha na hierarquia. Você também pode visualizar quantas cargas de trabalho não relacionadas ao pod estão sendo executados no host se o host tiver pressão de processador ou memória.

![Exemplo da hierarquia do Nó Kubernetes na exibição de desempenho](./media/container-insights-analyze/containers-nodes-view.png)

Os contêineres do Windows Server que executam o Sistema Operacional Windows Server 2019 são mostrados depois de todos os nós baseados em Linux na lista. Quando você expande um nó do Servidor Windows, você pode visualizar um ou mais pods e contêineres que são executados no nó. Depois que um nó é selecionado, o painel propriedades mostra informações da versão. As informações do agente são excluídas porque os nós do Windows Server não têm um agente instalado. 

![Hierarquia de nó de exemplo com os nós do Windows Server listados](./media/container-insights-analyze/nodes-view-windows.png) 

Os nós virtuais Azure Container Instances que executam o Sistema Operacional Linux são mostrados após o último nó de cluster AKS na lista. Quando você expande um nó virtual 'Instâncias de contêiner', você pode visualizar uma ou mais cápsulas e contêineres que são executados no nó. As métricas não são coletadas e relatadas para nós, apenas para pods.

![Hierarquia de nós de exemplo com Instâncias de Contêiner listadas](./media/container-insights-analyze/nodes-view-aci.png)

A partir de um nó expandido, você pode perfurar a partir do pod ou contêiner que é executado no nó para o controlador para visualizar dados de desempenho filtrados para esse controlador. Selecione o valor na coluna **Controlador** para o nó específico.
 
![Exemplo de drill down do nó para o controlador na exibição de desempenho](./media/container-insights-analyze/drill-down-node-controller.png)

Selecione controladores ou contêineres no topo da página para revisar o status e a utilização de recursos desses objetos. Para rever a utilização da memória, na lista de itens de baixa **Métrica,** selecione O **conjunto de trabalho**Memória **RSS** ou Memória . **RSS de Memória** só tem suporte para a versão do Kubernetes 1.8 e posteriores. Caso contrário, você vê valores para **Min&nbsp; ** como *NaN&nbsp;*, que é um valor de tipo de dados numérico que representa um valor indefinido ou inrepresentável.

![Exibição do desempenho de nós do contêiner](./media/container-insights-analyze/containers-node-metric-dropdown.png)

**O conjunto de trabalho de memória** mostra a memória residente e a memória virtual (cache) incluída e é um total do que o aplicativo está usando. **Memória RSS** mostra apenas a memória principal (que não é nada além da memória residente em outras palavras). Esta métrica mostra a capacidade real da memória disponível. Qual é a diferença entre memória residente e memória virtual?

- Memória residente ou memória principal, é a quantidade real de memória da máquina disponível para os nós do cluster.

- A memória virtual é reservada ao espaço em disco rígido (cache) usado pelo sistema operacional para trocar dados da memória para o disco quando pressão de memória e, em seguida, buscá-los de volta à memória quando necessário.

Por padrão, os dados de desempenho são baseados nas últimas seis horas, mas você pode alterar a janela usando a opção **TimeRange** no canto superior esquerdo. Você também pode filtrar os resultados dentro do intervalo de tempo selecionando **Min**, **Avg**, **50th**, **90th**, **95th**, e **Max** no seletor de percentil. 

![Seleção de percentil para filtragem de dados](./media/container-insights-analyze/containers-metric-percentile-filter.png)

Quando você passa o tempo sobre o gráfico de barras a coluna **Tendência,** cada barra mostra o uso da CPU ou da memória, dependendo de qual métrica é selecionada, dentro de um período de amostra de 15 minutos. Depois de selecionar o gráfico de tendências através de um teclado, use a tecla Alt+Page up ou alt+Page down para percorrer cada barra individualmente. Você tem os mesmos detalhes que teria se você pairasse sobre o bar.

![Exemplo de hover-over do gráfico de barras de tendência](./media/container-insights-analyze/containers-metric-trend-bar-01.png) 

No exemplo seguinte, para o primeiro nó da lista, *aks-nodepool1-*, o valor para **Containers** é 9. Este valor é um acúmulo do número total de contêineres implantados.

![Rollup de recipientes por nó exemplo](./media/container-insights-analyze/containers-nodes-containerstotal.png)

Essas informações podem ajudá-lo a identificar rapidamente se você tem um equilíbrio adequado de contêineres entre nós em seu cluster. 

As informações apresentadas quando você visualiza a guia Nós são **descritas** na tabela a seguir.

| Coluna | Descrição | 
|--------|-------------|
| Nome | O nome do host. |
| Status | Exibição de Kubernetes do status do nó. |
| Min&nbsp;%,&nbsp;Avg %,&nbsp;50º&nbsp;%, 90º %, 95º&nbsp;%, Max&nbsp;%  | Percentual médio de nós com base no percentil pela duração selecionada. |
| Min, Avg, 50th, 90th, 95th, Max | Valor real dos nódulos médios com base no percentil durante a duração do tempo selecionado. O valor médio é medido a partir do limite de CPU/Memória definido para um nó. Para pods e contêineres, é o valor médio relatado pelo hospedeiro. |
| Contêineres | Número de contêineres. |
| Tempo de atividade | Representa a hora desde que um nó foi iniciado ou reiniciado. |
| Controller | Somente para os contêineres e pods. Ele mostra em qual controlador ele reside. Nem todos os pods estão em um controlador, assim, alguns poderão exibir **N/D**. | 
| Tendência&nbsp;Min %,&nbsp;Avg&nbsp;%, 50º %, 90º&nbsp;%, 95º&nbsp;%, Max&nbsp;% | Tendência de gráfico de barras representa o percentual médio de métrica de percentil do controlador. |

Você pode notar uma carga de trabalho depois de expandir um nó chamado **Outro processo**. Ele representa processos não contêineres que são executados em seu nó, e inclui:

* Processos não-containerizados do Kubernetes autogerenciados ou gerenciados

* Processos de tempo de execução de contêineres  

* Kubelet  

* Processos do sistema em execução em seu nó

* Outras cargas de trabalho não-Kubernetes em execução em hardware de nó ou VM

É calculado por: *Uso total do CAdvisor* - *Uso a partir de processo containerized*.  

No seletor, selecione **Controladores**.

![Exibir controladores selecionados](./media/container-insights-analyze/containers-controllers-tab.png)

Aqui você pode ver a saúde de desempenho de seus controladores e controladores de nó virtuais de casos de contêiner ou pods de nó virtual não conectados a um controlador.

![\<Exibição de desempenho dos controladores de> de nome](./media/container-insights-analyze/containers-controllers-view.png)

A hierarquia da linha começa com um controlador. Quando você expande um controlador, você visualiza um ou mais pods. Expanda um pod e a última linha mostrará o contêiner agrupado para o pod. A partir de um controlador expandido, você pode perfurar até o nó em que está sendo executado para visualizar dados de desempenho filtrados para esse nó. Os pods de instâncias de contêiner não conectados a um controlador são listados em último na lista.

![Hierarquia de Controladores de exemplo com pods de Instâncias de Contêiner listados](./media/container-insights-analyze/controllers-view-aci.png)

Selecione o valor na coluna **Nó** para o controlador específico.

![Exemplo de drill down do nó para o controlador na exibição de desempenho](./media/container-insights-analyze/drill-down-controller-node.png)

As informações exibidas quando você visualiza controladores são descritas na tabela a seguir.

| Coluna | Descrição | 
|--------|-------------|
| Nome | O nome do controlador.|
| Status | O status de rollup dos recipientes após o término do funcionamento com status como *OK*, *Terminado,* *Falhou,* *Parou*ou *Pausado*. Se o contêiner estiver funcionando, mas o status não foi exibido corretamente ou não foi captado pelo agente e não respondeu por mais de 30 minutos, o status é *desconhecido*. Detalhes adicionais do ícone de status são fornecidos na tabela a seguir.|
| Min&nbsp;%,&nbsp;Avg %,&nbsp;50º&nbsp;%, 90º %, 95º&nbsp;%, Max&nbsp;%| Média de rollup do percentual médio de cada entidade para a métrica e o percentil selecionados. |
| Min, Avg, 50th, 90th, 95th, Max  | Rollup da média de milinúcleo de CPU ou desempenho da memória do contêiner para o percentil selecionado. O valor médio é medido usando o limite de CPU/memória definido para um pod. |
| Contêineres | Número total de contêineres para o controlador ou pod. |
| Reinícios | Rollup da contagem de reinicialização dos contêineres. |
| Tempo de atividade | Representa o tempo desde o início de um contêiner. |
| Nó | Somente para os contêineres e pods. Ele mostra em qual controlador ele reside. | 
| Tendência&nbsp;Min %,&nbsp;Avg&nbsp;%, 50º %, 90º&nbsp;%, 95º&nbsp;%, Max&nbsp;% | A tendência de gráfico de barras representa a métrica percentil do controlador. |

Os ícones no campo de status indicam o status on-line dos contêineres.
 
| ícone | Status | 
|--------|-------------|
| ![Ícone de status de execução pronto](./media/container-insights-analyze/containers-ready-icon.png) | Execução (Pronto)|
| ![Ícone de status de espera ou pausado](./media/container-insights-analyze/containers-waiting-icon.png) | Aguardando ou em pausa|
| ![Ícone do status de execução informado pela última vez](./media/container-insights-analyze/containers-grey-icon.png) | Último relatado correndo, mas não respondeu por mais de 30 minutos|
| ![Ícone de status de êxito](./media/container-insights-analyze/containers-green-icon.png) | Parou com sucesso ou houve falha ao parar|

O ícone de status mostra uma contagem com base no que o pod fornece. Ele mostra os dois piores estados e, quando você passa o mouse sobre o status, mostra um status de rollup de todos os pods no contêiner. Se não houver um estado pronto, o valor de status mostrará **(0)**.

No seletor, selecione **Contêineres**.

![Selecionar exibição de contêineres](./media/container-insights-analyze/containers-containers-tab.png)

Aqui, você pode exibir a integridade de desempenho de seus contêineres do Kubernetes do Azure e das Instâncias de Contêiner do Azure. 

![\<Exibição de desempenho de contêineres> nome](./media/container-insights-analyze/containers-containers-view.png)

Em um contêiner, você pode fazer drill down até um pod ou nó para exibir dados de desempenho filtrados para esse objeto. Selecione o valor na coluna **Pod** ou **Nó** para o contêiner específico.

![Exemplo de detalhamento do nó para os contêineres na exibição de desempenho](./media/container-insights-analyze/drill-down-controller-node.png)

As informações exibidas quando você visualiza os recipientes são descritas na tabela a seguir.

| Coluna | Descrição | 
|--------|-------------|
| Nome | O nome do controlador.|
| Status | Status dos contêineres, se houver. Detalhes adicionais do ícone de status são fornecidos na tabela a seguir.|
| Min&nbsp;%,&nbsp;Avg %,&nbsp;50º&nbsp;%, 90º %, 95º&nbsp;%, Max&nbsp;% | O rollup do percentual médio de cada entidade para a métrica e o percentil selecionados. |
| Min, Avg, 50th, 90th, 95th, Max | O rollup da média do desempenho de memória ou do milinúcleo da CPU do contêiner para o percentual selecionado. O valor médio é medido usando o limite de CPU/memória definido para um pod. |
| Pod | Contêiner no qual reside o pod.| 
| Nó |  Nó em que reside o contêiner. | 
| Reinícios | Representa o tempo desde o início de um contêiner. |
| Tempo de atividade | Representa a hora desde que um contêiner foi iniciado ou reiniciado. |
| Tendência&nbsp;Min %,&nbsp;Avg&nbsp;%, 50º %, 90º&nbsp;%, 95º&nbsp;%, Max&nbsp;% | A tendência de gráfico de barras representa o percentual métrico médio do contêiner. |

Os ícones no campo de status indicam os status on-line dos pods, conforme descrito na tabela a seguir.
 
| ícone | Status |  
|--------|-------------|  
| ![Ícone de status de execução pronto](./media/container-insights-analyze/containers-ready-icon.png) | Execução (Pronto)|  
| ![Ícone de status de espera ou pausado](./media/container-insights-analyze/containers-waiting-icon.png) | Aguardando ou em pausa|  
| ![Ícone do status de execução informado pela última vez](./media/container-insights-analyze/containers-grey-icon.png) | Relatado pela última vez como em execução, mas sem responder por mais de 30 minutos|  
| ![Ícone de status encerrado](./media/container-insights-analyze/containers-terminated-icon.png) | Parou com sucesso ou houve falha ao parar|  
| ![Ícone de status com falha](./media/container-insights-analyze/containers-failed-icon.png) | Estado com falha |  

## <a name="workbooks"></a>Pastas de trabalho

As pastas de trabalho combinam texto, [consultas de log,](../log-query/query-language.md)métricas e parâmetros em [relatórios](../platform/data-platform-metrics.md)interativos ricos. As Pastas de Trabalho são editáveis por qualquer membro da equipe com acesso aos mesmos recursos do Azure.

O Monitor Azure para contêineres inclui quatro livros de trabalho para você começar:

- **Capacidade do disco**: Apresenta gráficos interativos de uso de disco para cada disco apresentado ao nó dentro de um contêiner pelas seguintes perspectivas:

    - Aumento percentual de uso do disco para todos os discos.
    - Espaço livre em disco para todos os discos.
    - Uma grade que mostra o disco de cada nó, sua porcentagem de espaço usado, tendência de porcentagem de espaço usado, espaço livre em disco (GiB) e tendência de espaço livre em disco (GiB). Quando uma linha é selecionada na tabela, a porcentagem de espaço usado e espaço livre em disco (GiB) é mostrada abaixo da linha. 

- **Disk IO**: Apresenta gráficos interativos de utilização de disco para cada disco apresentado ao nó dentro de um contêiner pelas seguintes perspectivas:

    - O Disco I/O resumiu em todos os discos por ler bytes/seg, escrever bytes/seg e ler e escrever tendências bytes/seg.
    - Oito gráficos de desempenho mostram indicadores-chave de desempenho para ajudar a medir e identificar gargalos de I/O do disco.

- **Kubelet**: Inclui dois grids que mostram as principais estatísticas operacionais do nó:

    - A visão geral da grade do nó resume a operação total, o total de erros e as operações bem-sucedidas em porcentagem e tendência para cada nó.
    - A visão geral por tipo de operação resume para cada operação a operação total, erros totais e operações bem sucedidas por porcentagem e tendência.

- **Rede**: Apresenta gráficos interativos de utilização de rede para o adaptador de rede de cada nó, e uma grade apresenta os principais indicadores de desempenho para ajudar a medir o desempenho de seus adaptadores de rede.

Você acessa essas regras selecionando cada uma da lista de parada **do View Workbooks.**

![Exibir lista de parada de livros de trabalho](./media/container-insights-analyze/view-workbooks-dropdown-list.png)

## <a name="next-steps"></a>Próximas etapas

- Revisão [Crie alertas de desempenho com o Azure Monitor para contêineres](container-insights-alerts.md) para aprender como criar alertas para alta utilização de CPU e memória para suportar seus DevOps ou processos e procedimentos operacionais.

- Exibir [exemplos de consulta de log](container-insights-log-search.md#search-logs-to-analyze-data) para ver consultas e exemplos predefinidos para avaliar ou personalizar para alertar, visualizar ou analisar seus clusters.

- Veja [a saúde do monitor de cluster](container-insights-health.md) sustais para saber como ver o estado de saúde do seu cluster Kubernetes.
