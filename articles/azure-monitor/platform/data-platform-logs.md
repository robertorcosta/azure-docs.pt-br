---
title: Logs no Azure Monitor | Microsoft Docs
description: Descreve os logs do Azure Monitor usados para análise avançada de dados de monitoramento.
documentationcenter: ''
author: bwren
manager: carmonm
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: 4e5d4af74ab54479a49963369cb99dbc19fca848
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86505269"
---
# <a name="logs-in-azure-monitor"></a>Logs no Azure Monitor

> [!NOTE]
> Todos os dados coletados pelo Azure Monitor se enquadram em um dos dois tipos fundamentais, Métricas e Logs. Este artigo descreve os Logs. Confira [Métricas no Azure Monitor](data-platform-metrics.md) para ver uma descrição detalhada das métricas e [Monitorar dados coletados pelo Azure Monitor](data-platform.md) para uma comparação entre as duas.

No Azure Monitor, os logs são especialmente úteis para realizar análises complexas em dados de uma variedade de origens. Este artigo descreve como os Logs são estruturados no Azure Monitor, o que você pode fazer com os dados e identifica diferentes fontes de dados que armazenam dados em Logs.

> [!NOTE]
> É importante diferenciar os Logs do Azure Monitor e as origens de dados de log no Azure. Por exemplo, os eventos de nível de assinatura no Azure são gravados em um [log de atividades](platform-logs-overview.md) que pode ser visto no menu do Azure Monitor. A maioria dos recursos gravam informações operacionais em um [log de recursos](platform-logs-overview.md) que pode ser encaminhado para diferentes locais. Os Logs do Azure Monitor são uma plataforma de dados de log que coleta logs de atividade e de recursos junto com outros dados de monitoramento para fornecer uma análise profunda de todo o seu conjunto de recursos.

## <a name="what-are-azure-monitor-logs"></a>O que são os Logs do Azure Monitor?

Os Logs do Azure Monitor contêm diferentes tipos de dados organizados em registros com diferentes conjuntos de propriedades para cada um. Os Logs podem conter valores numéricos como as Métricas do Azure Monitor, mas normalmente contêm dados de texto com descrições detalhadas. Além disso, eles são diferentes dos dados de métricas, porque variam em sua estrutura e geralmente não são coletados em intervalos regulares. Os dados telemétricos, como eventos e rastreamentos, são armazenados como Logs do Azure Monitor acrescidos dos dados de desempenho, de modo que todos possam ser combinados para análise.

Um tipo comum de entrada de log é um evento que é coletado esporadicamente. Os eventos são criados por um aplicativo ou serviço e normalmente incluem informações suficientes para fornecer um contexto completo por conta própria. Por exemplo, um evento pode indicar que um recurso específico foi criado ou modificado, um novo host é iniciado em resposta ao maior tráfego ou um erro foi detectado em um aplicativo.

 Como o formato dos dados pode variar, os aplicativos podem criar logs personalizados usando a estrutura que eles exigem. Os dados de métricas podem até mesmo ser armazenados em Logs para serem combinados com outros dados de monitoramento de tendências e outras análises de dados.


## <a name="what-can-you-do-with-azure-monitor-logs"></a>O que você pode fazer com os Logs do Azure Monitor?
A tabela a seguir lista as diferentes maneiras em que você pode usar os Logs no Azure Monitor.


|  | Descrição |
|:---|:---|
| **Analisar** | Use o [Log Analytics](../log-query/get-started-portal.md) no portal do Azure para gravar [consultas de log](../log-query/log-query-overview.md) e analisar interativamente os dados de log usando o poderoso mecanismo de análise do Data Explorer.<br>Use o [console de análise do Application Insights](../log-query/log-query-overview.md) no portal do Azure para gravar consultas de log e analisar interativamente os dados de log do Application Insights. |
| **Visualizar** | Fixe os resultados de consultas renderizados como tabelas ou gráficos em um [painel do Azure](../../azure-portal/azure-portal-dashboards.md).<br>Crie uma [pasta de trabalho](../platform/workbooks-overview.md) para combinar com vários conjuntos de dados em um relatório interativo. <br>Exportar os resultados de uma consulta para o [Power BI](powerbi.md) a fim de usar visualizações diferentes e compartilhar com usuários fora do Azure.<br>Exporte os resultados de uma consulta para o [Grafana](grafana-plugin.md) a fim de aproveitar o painel da ferramenta e combinar com outras fontes de dados.|
| **Alerta** | Configurar uma [regra de alerta de log](alerts-log.md) que envia uma notificação ou realiza [ação automatizada](action-groups.md) quando os resultados da consulta correspondem a um resultado específico.<br>Configure uma [regra de alerta de métrica](alerts-metric-logs.md) em determinados logs de dados de log extraídos como métricas. |
| **Recuperar** | Acesse os resultados da consulta de log em uma linha de comando usando a [CLI do Azure](/cli/azure/ext/log-analytics/monitor/log-analytics).<br>Acesse os resultados da consulta de log em uma linha de comando usando os [cmdlets do PowerShell](/powershell/module/az.operationalinsights).<br>Acesse os resultados da consulta de log em um aplicativo personalizado usando a [API REST](https://dev.loganalytics.io/). |
| **Exportar** | Crie um fluxo de trabalho para recuperar dados de log e copie-os para uma localização externa usando os [Aplicativos Lógicos](~/articles/logic-apps/index.yml). |


## <a name="how-is-data-in-azure-monitor-logs-structured"></a>Como os dados são estruturados nos Logs do Azure Monitor?
Os dados coletados pelos Logs do Azure Monitor são armazenados em um [workspace do Log Analytics](../platform/design-logs-deployment.md). Cada workspace contém várias tabelas que armazenam dados de uma origem específica. Embora todas as tabelas compartilhem [algumas propriedades comuns](log-standard-properties.md), cada uma tem um conjunto exclusivo de propriedades de acordo com o tipo de dados que armazena. Um novo workspace terá um conjunto padrão de tabelas e mais tabelas serão adicionadas por diferentes soluções de monitoramento e outros serviços que gravam no workspace em questão.

Os dados de log do Application Insights usam o mesmo mecanismo do Log Analytics como workspaces, mas ficam armazenados separadamente para cada aplicativo monitorado. Cada aplicativo tem um conjunto padrão de tabelas para armazenar dados como solicitações de aplicativo, exceções e exibições de página.

As consultas de log usarão dados de um workspace do Log Analytics ou de um aplicativo do Application Insights. É possível usar uma [consulta entre recursos](../log-query/cross-workspace-query.md) para analisar os dados de aplicativo junto com outros dados de log ou para criar consultas que incluem vários workspaces ou aplicativos.

![Workspaces](media/data-platform-logs/workspaces.png)

## <a name="log-queries"></a>Consultas de logs
Os dados nos Logs do Azure Monitor são recuperados usando uma [consulta de log](../log-query/log-query-overview.md) gravada com a [linguagem de consulta Kusto](../log-query/get-started-queries.md), que permite recuperar, consolidar e analisar rapidamente os dados coletados. Use o [Log Analytics](../log-query/log-query-overview.md) para gravar e testar consultas de log no portal do Azure. Ele permite trabalhar com os resultados de maneira interativa ou fixá-los em um painel para exibi-los com outras visualizações.

![Log Analytics](media/data-platform-logs/log-analytics.png)

Abra o [Log Analytics no Application Insights](../log-query/log-query-overview.md) para analisar os dados do Application Insights.

![Análise do Application Insights](media/data-platform-logs/app-insights-analytics.png)

Também é possível recuperar dados de log usando a [API do Log Analytics](https://dev.loganalytics.io/documentation/overview) e a [API REST do Application Insights](https://dev.applicationinsights.io/documentation/overview).


## <a name="sources-of-azure-monitor-logs"></a>Origens dos Logs do Azure Monitor
O Azure Monitor pode coletar dados de log de várias origens no Azure e de recursos locais. As tabelas a seguir listam as diferentes fontes de dados disponíveis de recursos distintos que gravam dados em Logs de Azure Monitor. Cada uma tem um link para obter detalhes sobre qualquer configuração necessária.

### <a name="azure-tenant-and-subscription"></a>Locatário e assinatura do Azure

| Dados | Descrição |
|:---|:---|
| Logs de auditoria do Azure Active Directory | Definidos nas configurações de Diagnóstico de cada diretório. Confira [Integrar logs do Azure AD aos logs do Azure Monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md).  |
| Logs de atividade | Armazenados separadamente por padrão e podem ser usados para alertas quase em tempo real. Instale a solução de Análise do Log de Atividades para gravar no workspace do Log Analytics. Confira [Coletar e analisar os logs de atividades do Azure no Log Analytics](./activity-log.md). |

### <a name="azure-resources"></a>Recursos do Azure

| Dados | Descrição |
|:---|:---|
| Diagnósticos de recurso | Defina as configurações de diagnóstico para gravar em dados de diagnóstico, incluindo métricas para um workspace do Log Analytics. Confira [Transmitir logs de recurso do Azure para o Log Analytics](./resource-logs.md#send-to-log-analytics-workspace). |
| Soluções de monitoramento | As soluções de monitoramento gravam os dados que coletam no workspace do Log Analytics. Confira [Detalhes da coleta de dados para soluções de gerenciamento no Azure](../monitor-reference.md) para ver uma lista de soluções. Confira [Soluções de monitoramento no Azure Monitor](../insights/solutions.md) para saber detalhes sobre como instalar e usar soluções. |
| Métricas | Envie métricas de plataforma dos recursos do Azure Monitor a um workspace do Log Analytics para reter os dados de log por períodos mais longos e executar análises complexas com outros tipos de dados usando a [linguagem de consulta Kusto](/azure/kusto/query/). Confira [Transmitir logs de recurso do Azure para o Log Analytics](./resource-logs.md#send-to-azure-storage). |
| Armazenamento de tabelas do Azure | Colete dados do armazenamento do Azure em que alguns recursos do Azure gravam dados de monitoramento. Confira [Usar o Armazenamento de Blobs do Azure para IIS e o armazenamento de tabelas do Azure para eventos com o Log Analytics](diagnostics-extension-logs.md). |

### <a name="virtual-machines"></a>Máquinas Virtuais

| Dados | Descrição |
|:---|:---|
|  Fontes de dados de agente | As fontes de dados coletadas dos agentes [Windows](agent-windows.md) e [Linux](../learn/quick-collect-linux-computer.md) incluem eventos, dados de desempenho e logs personalizados. Confira [Fontes de dados do agente no Azure Monitor](data-sources.md) para ver uma lista das fontes de dados e detalhes sobre a configuração. |
| Soluções de monitoramento | As soluções de monitoramento gravam os dados que coletam dos agentes no workspace do Log Analytics. Confira [Detalhes da coleta de dados para soluções de gerenciamento no Azure](../monitor-reference.md) para ver uma lista de soluções. Confira [Soluções de monitoramento no Azure Monitor](../insights/solutions.md) para saber detalhes sobre como instalar e usar soluções. |
| System Center Operations Manager | Conecte o grupo de gerenciamento do Operations Manager ao Azure Monitor para coletar dados de evento e desempenho de agentes locais nos logs. Confira [Conectar o Operations Manager ao Log Analytics](om-agents.md) para ver detalhes dessa configuração. |


### <a name="applications"></a>Aplicativos

| Dados | Descrição |
|:---|:---|
| Solicitações e exceções | Os dados detalhados sobre as solicitações de aplicativo e as exceções estão nas tabelas _requests_, _pageViews_ e _exceptions_. As chamadas a [componentes externos](../app/asp-net-dependencies.md) estão na tabela de _dependências_. |
| Uso e desempenho | O desempenho do aplicativo está disponível nas tabelas _requests_, _browserTimings_ e _performanceCounters_. Os dados de [métricas personalizadas](../app/api-custom-events-metrics.md#trackevent) estão na tabela _customMetrics_.|
| Dados de rastreamento | Os resultados de [rastreamento distribuído](../app/distributed-tracing.md) são armazenados na tabela _traces_. |
| Testes de disponibilidade | Os dados de resumo dos [testes de disponibilidade](../app/monitor-web-app-availability.md) são armazenados na tabela _availabilityResults_. Os dados detalhados desses testes estão em armazenamento separado e são acessados no Application Insights no portal do Azure. |

### <a name="insights"></a>Insights

| Dados | Descrição |
|:---|:---|
| Azure Monitor para contêineres | Dados de inventário e desempenho coletados pelo [Azure Monitor para contêineres](../insights/container-insights-overview.md). Confira [Detalhes de coleta de dados de contêiner](../insights/container-insights-log-search.md#container-records) para ver uma lista das tabelas. |
| Azure Monitor para VMs | Dados de mapa e desempenho coletados pelo [Azure Monitor para VMs](../insights/vminsights-overview.md). Confira [Como consultas logs do Azure Monitor para VMs](../insights/vminsights-log-search.md) para saber detalhes sobre como consultar esses dados. |

### <a name="custom"></a>Personalizado 

| Dados | Descrição |
|:---|:---|
| API REST | Grave dados em um workspace do Log Analytics de qualquer cliente REST. Confira [Enviar dados de log para o Azure Monitor com a API do Coletor de Dados HTTP](data-collector-api.md) para saber detalhes.
| Aplicativo Lógico | Grave todos os dados em um workspace do Log Analytics de um fluxo de trabalho de Aplicativo Lógico com a ação **Coletor de Dados do Azure Log Analytics**. |

### <a name="security"></a>Segurança

| Dados | Descrição |
|:---|:---|
| Central de Segurança do Azure | A [Central de Segurança do Azure](../../security-center/index.yml) armazena os dados que coleta em um workspace do Log Analytics em que eles possam ser analisados com outros dados de log. Confira [Coleta de dados na Central de Segurança do Azure](../../security-center/security-center-enable-data-collection.md) para saber detalhes sobre a configuração do workspace. |
| Azure Sentinel | O [Azure Sentinel](../../sentinel/index.yml) armazena dados de fontes de dados em um workspace do Log Analytics. Confira [Conectar fontes de dados](../../sentinel/connect-data-sources.md).  |


## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre a [plataforma de dados do Azure Monitor](data-platform.md).
- Saiba mais sobre as [métricas no Azure Monitor](data-platform-metrics.md).
- Saiba mais sobre os [dados de monitoramento disponíveis](data-sources.md) para diferentes recursos no Azure.
