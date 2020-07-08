---
title: Métricas no Azure Monitor | Microsoft Docs
description: Descreve as métricas no Azure Monitor que são dados de monitoramento leves compatíveis com cenários quase em tempo real.
documentationcenter: ''
author: bwren
manager: carmonm
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: 23e4d104697b5b688330c6ab3a93beebf62f3c6a
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.contentlocale: pt-BR
ms.lasthandoff: 05/22/2020
ms.locfileid: "83799965"
---
# <a name="metrics-in-azure-monitor"></a>Métricas no Azure Monitor

> [!NOTE]
> A plataforma de dados do Azure Monitor baseia-se em dois tipos de dados fundamentais: Métricas e logs. Este artigo descreve Métricas. Veja os [Logs no Azure Monitor](data-platform-logs.md) para obter uma descrição detalhada dos logs e a [plataforma de dados do Azure Monitor](data-platform.md) para uma comparação dos dois.

As métricas no Azure Monitor são leves e capazes de dar suporte a cenários quase em tempo real, tornando-os particularmente úteis para alertas e detecção rápida de problemas. Este artigo descreve como as métricas são estruturadas, o que você pode fazer com elas e identifica diferentes fontes de dados que armazenam dados em métricas.

## <a name="what-are-metrics"></a>O que são métricas?
Métricas são valores numéricos que descrevem algum aspecto de um sistema em um momento específico. As métricas são coletadas em intervalos regulares e são úteis para alertar, pois podem ser utilizadas com frequência, e um alerta pode ser acionado rapidamente com uma lógica relativamente simples.

## <a name="what-can-you-do-with-azure-monitor-metrics"></a>O que você pode fazer com as Métricas do Azure Monitor?
A tabela a seguir lista as diferentes maneiras em que você pode usar dados de métrica no Azure Monitor.

|  |  |
|:---|:---|
| Analisar | Use o [Metrics Explorer](metrics-charts.md) para analisar as métricas coletadas em um gráfico e comparar métricas de recursos diferentes. |
| Visualizar | Fixe um gráfico do Metrics Explorer a um [painel do Azure](../learn/tutorial-app-dashboards.md).<br>Crie uma [pasta de trabalho](../platform/workbooks-overview.md) para combinar com vários conjuntos de dados em um relatório interativo. Exporte os resultados de uma consulta para o [Grafana](grafana-plugin.md) para aproveitar os painéis dele e combinar com outras fontes de dados. |
| Alerta | Configurar uma [regra de alerta da métrica](alerts-metric.md) que envia uma notificação ou realiza uma [ação automatizada](action-groups.md) quando o valor da métrica ultrapassa o limite definido. |
| Automatizar |  Usar o [Dimensionamento automático](autoscale-overview.md) para aumentar ou diminuir os recursos com base em um valor de métrica que está ultrapassando um limite. |
| Exportação | [Roteie métricas para logs](resource-logs-collect-storage.md) a fim de analisar dados nas Métricas do Azure Monitor junto com os dados nos Logs do Azure Monitor e para armazenar valores de métrica por mais de 93 dias.<br>Transmitir métricas para um [Hub de Eventos](stream-monitoring-data-event-hubs.md) para encaminhá-las a sistemas externos. |
| Recuperar | Acessar valores de métrica de uma linha de comando usando [cmdlets do PowerShell](https://docs.microsoft.com/powershell/module/az.applicationinsights)<br>Acessar valores de métrica do aplicativo personalizado usando a [API REST](rest-api-walkthrough.md).<br>Acesse valores de métrica de uma linha de comando usando a [CLI](/cli/azure/monitor/metrics). |
| Archive | [Arquive](..//learn/tutorial-archive-data.md) o histórico de desempenho ou integridade do recurso para fins de conformidade, auditoria ou geração de relatórios offline. |

## <a name="how-is-data-in-azure-monitor-metrics-structured"></a>Como os dados nas Métricas do Azure Monitor são estruturados?
Os dados coletados pelas Métricas do Azure Monitor são armazenados em um banco de dados de série temporal que é otimizado para analisar dados com carimbo de data/hora. Cada conjunto de valores de métrica é uma série temporal com as seguintes propriedades:

* A hora em que o valor foi coletado
* O recurso ao qual a rede virtual está associada
* Um namespace que funciona como uma categoria para a métrica
* Um nome de métrica
* O valor em si
* Algumas métricas podem ter várias dimensões, conforme descrito em [Métricas multidimensionais](#multi-dimensional-metrics). As métricas personalizadas podem ter até 10 dimensões.

## <a name="multi-dimensional-metrics"></a>Métricas multidimensionais
Um dos desafios dos dados de métrica é que eles geralmente têm informações limitadas para fornecer contexto para valores coletados. O Azure Monitor resolve esse desafio com métricas multidimensionais. Dimensões de uma métrica são pares nome-valor que contêm dados adicionais para descrever o valor da métrica. Por exemplo, uma métrica _Espaço em disco disponível_ pode ter uma dimensão chamada _Unidade_ com valores _C:_ , _D:_ , que permitiria a exibição do espaço em disco disponível em todas as unidades ou para cada unidade individualmente.

O exemplo abaixo ilustra dois conjuntos de dados de uma métrica hipotética chamada _Taxa de Transferência de Rede_. O primeiro conjunto de dados não tem nenhuma dimensão. O segundo conjunto de dados mostra os valores com duas dimensões, _Endereço IP_ e _Direção_:

### <a name="network-throughput"></a>Taxa de Transferência de Rede

| Timestamp     | Valor da Métrica |
| ------------- |:-------------|
| 9/8/2017 8h14 | 1\.331,8 Kbps |
| 9/8/2017 8h15 | 1\.141,4 Kbps |
| 9/8/2017 8h16 | 1\.110,2 Kbps |

Essa métrica não dimensional pode responder apenas a uma pergunta básica, como “qual era minha taxa de transferência de rede em determinado horário?”

### <a name="network-throughput--two-dimensions-ip-and-direction"></a>Taxa de Transferência de Rede + duas dimensões (“IP” e “Direção”)

| Timestamp     | Dimensão “IP”   | Dimensão “Direção” | Valor da Métrica|
| ------------- |:-----------------|:------------------- |:-----------|
| 9/8/2017 8h14 | IP="192.168.5.2" | Direction="Send"    | 646,5 Kbps |
| 9/8/2017 8h14 | IP="192.168.5.2" | Direction="Receive" | 420,1 Kbps |
| 9/8/2017 8h14 | IP="10.24.2.15"  | Direction="Send"    | 150,0 Kbps |
| 9/8/2017 8h14 | IP="10.24.2.15"  | Direction="Receive" | 115,2 Kbps |
| 9/8/2017 8h15 | IP="192.168.5.2" | Direction="Send"    | 515,2 Kbps |
| 9/8/2017 8h15 | IP="192.168.5.2" | Direction="Receive" | 371,1 Kbps |
| 9/8/2017 8h15 | IP="10.24.2.15"  | Direction="Send"    | 155,0 Kbps |
| 9/8/2017 8h15 | IP="10.24.2.15"  | Direction="Receive" | 100,1 Kbps |

Essa métrica pode responder a perguntas como “qual era a taxa de transferência de rede para cada endereço IP?” e “quantos dados foram enviados vs. recebidos?” As métricas multidimensionais trazem um valor analítico e de diagnóstico adicional em comparação às métricas não dimensionais.

## <a name="interacting-with-azure-monitor-metrics"></a>Interagindo com as Métricas do Azure Monitor
Use o [Metrics Explorer](metrics-charts.md) para analisar interativamente os dados no banco de dados de métricas e ver o gráfico de várias métricas ao longo do tempo. É possível fixar os gráficos em um painel para exibi-los com outras visualizações. Também é possível recuperar métricas usando a [API REST de monitoramento do Azure](rest-api-walkthrough.md).

![Metrics Explorer](media/data-platform/metrics-explorer.png)

## <a name="sources-of-azure-monitor-metrics"></a>Fontes das Métricas do Azure Monitor
Há três fontes fundamentais de métricas coletadas pelo Azure Monitor. Após essas métricas serem coletadas no banco de dados de métricas do Azure Monitor, elas poderão ser avaliadas em conjunto, independentemente da origem que tiverem.

As **métricas de plataforma** são criadas pelos recursos do Azure e esclarecem a integridade e o desempenho. Cada tipo de recurso cria um [conjunto distinto de métricas](metrics-supported.md) sem a necessidade de configuração. Métricas de plataforma são coletadas de recursos do Azure com frequência de um minuto (exceto se especificado de outra forma na definição da métrica). 

**As métricas do SO convidado** são coletadas do sistema operacional convidado de uma máquina virtual. Habilite as métricas do sistema operacional convidado para máquinas virtuais do Windows com a [WAD (Extensão de Diagnóstico do Windows)](../platform/diagnostics-extension-overview.md) e para máquinas virtuais do Linux com o [Agente InfluxData Telegraf](https://www.influxdata.com/time-series-platform/telegraf/).

As **métricas de aplicativo** são criadas pelo Application Insights para seus aplicativos monitorados e ajudam você a detectar problemas de desempenho e acompanhar as tendências de como o aplicativo está sendo usado. Isso inclui valores como _Tempo de resposta do servidor_ e _Exceções de navegador_.

As **métricas personalizadas** são métricas definidas além das métricas padrão disponibilizadas automaticamente. Você pode [definir métricas personalizadas em seu aplicativo](../app/api-custom-events-metrics.md) que é monitorado pelo Application Insights ou criar métricas personalizadas para um serviço do Azure usando a [API de métricas personalizadas](metrics-store-custom-rest-api.md).

## <a name="retention-of-metrics"></a>Retenção de Métricas
Para a maioria dos recursos no Azure, as métricas são armazenadas por 93 dias. Há algumas exceções:

**Métricas do SO convidado**
-   **Métricas de SO convidado clássico**. Esses são contadores de desempenho coletados pela [WAD (Extensão de Diagnóstico do Windows)](../platform/diagnostics-extension-overview.md) ou pela [LAD (Extensão de Diagnóstico do Linux)](../../virtual-machines/extensions/diagnostics-linux.md) e roteados para uma conta do Armazenamento do Azure. A retenção para essas métricas é de 14 dias.
-   **Métricas de SO convidado enviadas para as Métricas do Azure Monitor**. Esses são os contadores de desempenho coletados pela [WAD (Extensão de Diagnóstico do Windows)](diagnostics-extension-overview.md) e enviados para o [coletor de dados do Azure Monitor](diagnostics-extension-overview.md#data-destinations) ou por meio do [Agente InfluxData Telegraf](https://www.influxdata.com/time-series-platform/telegraf/) em computadores Linux. A retenção para essas métricas é de 93 dias.
-   **Métricas de SO convidado coletadas pelo agente do Log Analytics**. Esses são contadores de desempenho coletados pelo agente do Log Analytics e enviados a um workspace do Log Analytics. A retenção para essas métricas é de 31 dias e pode ser estendida para até dois anos.

**Métricas baseadas em log do Application Insights**. 
- Nos bastidores, as [métricas baseadas em log](../app/pre-aggregated-metrics-log-metrics.md) se traduzem em consultas de log. A retenção delas corresponde à retenção de eventos em logs subjacentes. Para recursos do Application Insights, os logs são armazenados por 90 dias.


> [!NOTE]
> Você pode [enviar métricas de plataforma para recursos do Azure Monitor a um workspace do Log Analytics](resource-logs-collect-storage.md) para medição de tendência a longo prazo.





## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre a [Plataforma de dados do Azure Monitor](data-platform.md).
- Saiba mais sobre os [dados de log no Azure Monitor](data-platform-logs.md).
- Saiba mais sobre os [dados de monitoramento disponíveis](data-sources.md) para diferentes recursos no Azure.
