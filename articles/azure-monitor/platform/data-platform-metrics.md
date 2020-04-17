---
title: Métricas no Monitor Azure | Microsoft Docs
description: Descreve métricas no Azure Monitor, que são dados leves de monitoramento capazes de suportar cenários próximos em tempo real.
documentationcenter: ''
author: bwren
manager: carmonm
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: a02b3df02d455db8a7dfd21f63d659f75a66e446
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457307"
---
# <a name="metrics-in-azure-monitor"></a>Métricas no Azure Monitor

> [!NOTE]
> A plataforma de dados Do Azure Monitor é baseada em dois tipos de dados fundamentais: Métricas e Logs. Este artigo descreve Métricas. Consulte [logs no Azure Monitor](data-platform-logs.md) para obter uma descrição detalhada dos logs e da plataforma de [dados do Azure Monitor](data-platform.md) para uma comparação dos dois.

As métricas no Azure Monitor são leves e capazes de suportar cenários próximos em tempo real, tornando-os particularmente úteis para alertar e detectar problemas rápidos. Este artigo descreve como as métricas são estruturadas, o que você pode fazer com elas e identifica diferentes fontes de dados que armazenam dados em métricas.

## <a name="what-are-metrics"></a>O que são métricas?
Métricas são valores numéricos que descrevem algum aspecto de um sistema em um momento específico. As métricas são coletadas em intervalos regulares e são úteis para alertar porque podem ser amostradas com freqüência, e um alerta pode ser disparado rapidamente com lógica relativamente simples.

## <a name="what-can-you-do-with-azure-monitor-metrics"></a>O que você pode fazer com o Azure Monitor Metrics?
A tabela a seguir lista as diferentes maneiras pelas quais você pode usar dados métricos no Azure Monitor.

|  |  |
|:---|:---|
| Analisar | Use [o metrics explorer](metrics-charts.md) para analisar métricas coletadas em um gráfico e comparar métricas de diferentes recursos. |
| Visualizar | Fixar um gráfico do explorador de métricas a um [painel do Azure](../learn/tutorial-app-dashboards.md).<br>Crie uma [carteira de trabalho](../app/usage-workbooks.md) para combinar com vários conjuntos de dados em um relatório interativo. Exportar os resultados de uma consulta para [a Grafana](grafana-plugin.md) para alavancar seu dashboarding e combinar com outras fontes de dados. |
| Alerta | Configure uma [regra de alerta métrico](alerts-metric.md) que envie uma notificação ou tome ações [automatizadas](action-groups.md) quando o valor métrico cruzar um limite. |
| Automatizar |  Use [autoescala](autoscale-overview.md) para aumentar ou diminuir recursos com base em um valor métrico que cruza um limite. |
| Exportação | [Route Metrics to Logs](resource-logs-collect-storage.md) para analisar dados no Azure Monitor Metrics juntamente com dados no Azure Monitor Logs e para armazenar valores métricos por mais de 93 dias.<br>Fluxo de métricas para um [Hub de Eventos](stream-monitoring-data-event-hubs.md) para encaminhá-los para sistemas externos. |
| Recuperar | Acesse valores métricos de uma linha de comando usando [cmdlets PowerShell](https://docs.microsoft.com/powershell/module/az.applicationinsights)<br>Acesse os valores métricos do aplicativo personalizado usando [a API REST](rest-api-walkthrough.md).<br>Acesse os valores métricos de uma linha de comando usando [CLI](/cli/azure/monitor/metrics). |
| Archive | [Arquive](..//learn/tutorial-archive-data.md) o histórico de desempenho ou integridade do recurso para fins de conformidade, auditoria ou geração de relatórios offline. |

## <a name="how-is-data-in-azure-monitor-metrics-structured"></a>Como os dados do Azure Monitor Metrics são estruturados?
Os dados coletados pelo Azure Monitor Metrics são armazenados em um banco de dados de séries temporias que é otimizado para analisar dados carimbados por tempo. Cada conjunto de valores métricos é uma série temporal com as seguintes propriedades:

* O tempo que o valor foi coletado
* O recurso com o que o valor está associado
* Um namespace que age como uma categoria para a métrica
* Um nome métrico
* O valor em si
* Algumas métricas podem ter múltiplas dimensões, conforme descrito em [métricas multidimensionais](#multi-dimensional-metrics). As métricas personalizadas podem ter até 10 dimensões.

## <a name="multi-dimensional-metrics"></a>Métricas multidimensionais
Um dos desafios para os dados métricos é que muitas vezes tem informações limitadas para fornecer contexto para os valores coletados. O Azure Monitor aborda esse desafio com métricas multidimensionais. Dimensões de uma métrica são pares nome-valor que contêm dados adicionais para descrever o valor da métrica. Por exemplo, uma métrica _espaço em disco disponível_ poderia ter uma dimensão chamada _Drive_ com valores _C:_, _D:_, que permitiria visualizar o espaço disponível em disco em todas as unidades ou para cada unidade individualmente.

O exemplo abaixo ilustra dois conjuntos de dados de uma métrica hipotética chamada _Taxa de Transferência de Rede_. O primeiro conjunto de dados não tem nenhuma dimensão. O segundo conjunto de dados mostra os valores com duas dimensões, _Endereço IP_ e _Direção_:

### <a name="network-throughput"></a>Taxa de Transferência de Rede

| Timestamp     | Valor da Métrica |
| ------------- |:-------------|
| 9/8/2017 8h14 | 1.331,8 Kbps |
| 9/8/2017 8h15 | 1.141,4 Kbps |
| 9/8/2017 8h16 | 1.110,2 Kbps |

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
Use [o Metrics Explorer](metrics-charts.md) para analisar interativamente os dados em seu banco de dados métrico e mapear os valores de várias métricas ao longo do tempo. Você pode fixar os gráficos em um painel para visualizá-los com outras visualizações. Também é possível recuperar métricas usando a [API REST de monitoramento do Azure](rest-api-walkthrough.md).

![Metrics Explorer](media/data-platform/metrics-explorer.png)

## <a name="sources-of-azure-monitor-metrics"></a>Fontes de Métricas do Monitor Do Azure
Há três fontes fundamentais de métricas coletadas pelo Azure Monitor. Uma vez que essas métricas são coletadas no banco de dados métrico do Azure Monitor, elas podem ser avaliadas em conjunto, independentemente de sua origem.

As **métricas de plataforma** são criadas pelos recursos do Azure e esclarecem a integridade e o desempenho. Cada tipo de recurso cria um [conjunto distinto de métricas](metrics-supported.md) sem a necessidade de configuração. As métricas da plataforma são coletadas dos recursos do Azure em uma frequência de um minuto, a menos que especificado em contrário na definição da métrica. 

**As métricas do Sistema Operacional convidado** são coletadas do sistema operacional convidado de uma máquina virtual. Habilite as métricas do sistema operacional convidado para máquinas virtuais Windows com [o Windows Diagnostic Extension (WAD)](../platform/diagnostics-extension-overview.md) e para máquinas virtuais Linux com o Agente de [Telegraf InfluxData .](https://www.influxdata.com/time-series-platform/telegraf/)

As **métricas de aplicativo** são criadas pelo Application Insights para seus aplicativos monitorados e ajudam você a detectar problemas de desempenho e acompanhar as tendências de como o aplicativo está sendo usado. Isso inclui valores como _Tempo de resposta do servidor_ e _Exceções de navegador_.

**Métricas personalizadas** são métricas que você define, além das métricas padrão que estão automaticamente disponíveis. Você pode [definir métricas personalizadas em seu aplicativo](../app/api-custom-events-metrics.md) que são monitoradas pelo Application Insights ou criar métricas personalizadas para um serviço Azure usando a [API de métricas personalizadas](metrics-store-custom-rest-api.md).

## <a name="retention-of-metrics"></a>Retenção de Métricas
Para a maioria dos recursos no Azure, as métricas são armazenadas por 93 dias. Há algumas exceções:

**Métricas do Sistema Operacional convidado**
-   **Métricas clássicas do sistema operacional convidado**. Estes são contadores de desempenho coletados pelo [Windows Diagnostic Extension (WAD)](../platform/diagnostics-extension-overview.md) ou pelo [Linux Diagnostic Extension (LAD)](../../virtual-machines/extensions/diagnostics-linux.md) e encaminhados para uma conta de armazenamento Azure. A retenção dessas métricas é de 14 dias.
-   **Métricas do Sistema Operacional convidado enviadas para o Azure Monitor Metrics**. Estes são contadores de desempenho coletados pelo [Windows Diagnostic Extension (WAD)](diagnostics-extension-overview.md) e enviados para o dissipador de dados do [Azure Monitor,](diagnostics-extension-overview.md#data-destinations)ou através do [Agente de Telegraf InfluxData](https://www.influxdata.com/time-series-platform/telegraf/) em máquinas Linux. A retenção dessas métricas é de 93 dias.
-   **Métricas do Sistema Operacional convidado coletadas pelo agente log analytics**. Estes são contadores de desempenho coletados pelo agente log analytics e enviados para um espaço de trabalho do Log Analytics. A retenção dessas métricas é de 31 dias, podendo ser prorrogada por até 2 anos.

**Métricas baseadas em log do Application Insights**. 
- Por trás da cena, [métricas baseadas em log](../app/pre-aggregated-metrics-log-metrics.md) se traduzem em consultas de log. Sua retenção corresponde à retenção de eventos em logs subjacentes. Para os recursos do Application Insights, os logs são armazenados por 90 dias.


> [!NOTE]
> Você pode [enviar métricas da plataforma para os recursos do Azure Monitor para um espaço de trabalho do Log Analytics](resource-logs-collect-storage.md) para tendências de longo prazo.





## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre a [plataforma de dados do Azure Monitor](data-platform.md).
- Saiba mais sobre [os dados de log no Azure Monitor](data-platform-logs.md).
- Saiba mais sobre os [dados de monitoramento disponíveis](data-sources.md) para diferentes recursos no Azure.
