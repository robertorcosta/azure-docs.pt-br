---
title: Logs no Monitor Azure | Microsoft Docs
description: Descreve registros no Azure Monitor que são usados para análise avançada de dados de monitoramento.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: dd499a82ba1011d96772d6722e25a434d43a6bf0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480191"
---
# <a name="logs-in-azure-monitor"></a>Logs no Azure Monitor

> [!NOTE]
> Todos os dados coletados pelo Azure Monitor se encaixam em um dos dois tipos fundamentais, Métricas e Logs. Este artigo descreve Logs. Consulte [Métricas no Monitor Do Azure](data-platform-metrics.md) para obter uma descrição detalhada das métricas e [para monitorar os dados coletados pelo Azure Monitor](data-platform.md) para uma comparação dos dois.

Os logs no Azure Monitor são especialmente úteis para realizar análises complexas entre dados de várias fontes. Este artigo descreve como os Logs são estruturados no Azure Monitor, o que você pode fazer com os dados e identifica diferentes fontes de dados que armazenam dados no Logs.

> [!NOTE]
> É importante distinguir entre registros do Monitor Do Azure e fontes de dados de log no Azure. Por exemplo, eventos de nível de assinatura no Azure são gravados em um registro de [atividades](platform-logs-overview.md) que você pode exibir no menu do Monitor do Azure. A maioria dos recursos gravará informações operacionais em um [registro de recursos](platform-logs-overview.md) que você pode encaminhar para diferentes locais. O Azure Monitor Logs é uma plataforma de dados de log que coleta registros de atividades e registros de recursos, juntamente com outros dados de monitoramento para fornecer análises profundas em todo o seu conjunto de recursos.

## <a name="what-are-azure-monitor-logs"></a>O que são registros do Monitor Do Azure?

Os logs no Azure Monitor contêm diferentes tipos de dados organizados em registros com diferentes conjuntos de propriedades para cada tipo. Os logs podem conter valores numéricos como o Azure Monitor Metrics, mas normalmente contêm dados de texto com descrições detalhadas. Eles diferem ainda mais dos dados métricos, pois variam em sua estrutura e muitas vezes não são coletados em intervalos regulares. Telemetria, como eventos e rastreamentos, são armazenados Logs do Monitor Do Azure, além de dados de desempenho para que todos possam ser combinados para análise.

Um tipo comum de entrada de registro é um evento, que é coletado esporadicamente. Os eventos são criados por um aplicativo ou serviço e normalmente incluem informações suficientes para fornecer contexto completo por conta própria. Por exemplo, um evento pode indicar que um recurso específico foi criado ou modificado, um novo host é iniciado em resposta ao maior tráfego ou um erro foi detectado em um aplicativo.

 Como o formato dos dados pode variar, os aplicativos podem criar logs personalizados usando a estrutura que eles exigem. Os dados métricos podem até ser armazenados em Logs para combiná-los com outros dados de monitoramento para tendências e outras análises de dados.


## <a name="what-can-you-do-with-azure-monitor-logs"></a>O que você pode fazer com o Azure Monitor Logs?
A tabela a seguir lista as diferentes maneiras pelas quais você pode usar logs no Monitor Azure.


|  |  |
|:---|:---|
| Analisar | Use [o Log Analytics](../log-query/get-started-portal.md) no portal Azure para gravar [consultas de log](../log-query/log-query-overview.md) e analisar interativamente os dados de log usando o poderoso mecanismo de análise do Data Explorer.<br>Use o [console de análise Application Insights](../app/analytics.md) no portal Azure para escrever consultas de log e analisar interativamente os dados de log do Application Insights. |
| Visualizar | Os resultados da consulta de pinos renderizados como tabelas ou gráficos para [um painel do Azure](../../azure-portal/azure-portal-dashboards.md).<br>Crie uma [carteira de trabalho](../app/usage-workbooks.md) para combinar com vários conjuntos de dados em um relatório interativo. <br>Exportar os resultados de uma consulta para o [Power BI](powerbi.md) a fim de usar visualizações diferentes e compartilhar com usuários fora do Azure.<br>Exportar os resultados de uma consulta para [a Grafana](grafana-plugin.md) para alavancar seu dashboarding e combinar com outras fontes de dados.|
| Alerta | Configurar uma [regra de alerta de log](alerts-log.md) que envia uma notificação ou realiza [ação automatizada](action-groups.md) quando os resultados da consulta correspondem a um resultado específico.<br>Configure uma [regra de alerta métrica](alerts-metric-logs.md) em certos logs de dados extraídos como métricas. |
| Recuperar | Aconsulta de log de acesso resulta de uma linha de comando usando [o Azure CLI](/cli/azure/ext/log-analytics/monitor/log-analytics).<br>A consulta de log de acesso resulta de uma linha de comando usando [cmdlets PowerShell](https://docs.microsoft.com/powershell/module/az.operationalinsights).<br>Acesse os resultados da consulta de log de um aplicativo personalizado usando [a API REST](https://dev.loganalytics.io/). |
| Exportação | Crie um fluxo de trabalho para recuperar dados de log e copiá-los para um local externo usando [aplicativos lógicos](~/articles/logic-apps/index.yml). |


## <a name="how-is-data-in-azure-monitor-logs-structured"></a>Como os dados do Azure Monitor Logs são estruturados?
Os dados coletados pelo Azure Monitor Logs são armazenados em um [espaço de trabalho do Log Analytics](../platform/design-logs-deployment.md). Cada espaço de trabalho contém várias tabelas que armazenam dados de uma determinada fonte. Embora todas as tabelas [compartilhem algumas propriedades comuns,](log-standard-properties.md)cada uma tem um conjunto único de propriedades, dependendo do tipo de dados que armazena. Um novo espaço de trabalho terá um conjunto padrão de tabelas, e mais tabelas serão adicionadas por diferentes soluções de monitoramento e outros serviços que escrevem no espaço de trabalho.

Os dados de log do Application Insights usam o mesmo mecanismo Log Analytics que os espaços de trabalho, mas são armazenados separadamente para cada aplicativo monitorado. Cada aplicativo tem um conjunto padrão de tabelas para conter dados, como solicitações de aplicativos, exceções e visualizações de página.

As consultas de log usarão dados de um espaço de trabalho do Log Analytics ou de um aplicativo Application Insights. Você pode usar uma [consulta de recursos cruzados](../log-query/cross-workspace-query.md) para analisar dados de aplicativos em conjunto com outros dados de log ou para criar consultas, incluindo vários espaços de trabalho ou aplicativos.

![Workspaces](media/data-platform-logs/workspaces.png)

## <a name="log-queries"></a>Consultas de logs
Os dados no Azure Monitor Logs são recuperados usando uma [consulta de log](../log-query/log-query-overview.md) escrita com o idioma de consulta [kusto](../log-query/get-started-queries.md), que permite recuperar, consolidar e analisar rapidamente os dados coletados. Use [o Log Analytics](../log-query/portals.md) para escrever e testar consultas de log no portal Azure. Ele permite que você trabalhe com resultados de forma interativa ou fixá-los em um painel para visualizá-los com outras visualizações.

![Log Analytics](media/data-platform-logs/log-analytics.png)

Abra [o Log Analytics do Application Insights](../app/analytics.md) para analisar os dados do Application Insights.

![Análise do Application Insights](media/data-platform-logs/app-insights-analytics.png)

Você também pode recuperar dados de log usando a [API do Log Analytics](https://dev.loganalytics.io/documentation/overview) e a [API REST do Application Insights](https://dev.applicationinsights.io/documentation/overview).


## <a name="sources-of-azure-monitor-logs"></a>Fontes de Logs de Monitor do Azure
O Azure Monitor pode coletar dados de log de várias origens no Azure e de recursos locais. As tabelas a seguir listam as diferentes fontes de dados disponíveis a partir de diferentes recursos que gravam dados no Azure Monitor Logs. Cada um tem um link para detalhes sobre qualquer configuração necessária.

### <a name="azure-tenant-and-subscription"></a>Inquilino e assinatura do Azure

| Dados | Descrição |
|:---|:---|
| Logs de auditoria do Azure Active Directory | Configurado através das configurações de Diagnóstico para cada diretório. Consulte [os logs do AD Integra azure com logs do Monitor Do Azure](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md).  |
| Logs de atividade | Armazenado separadamente por padrão e pode ser usado para alertas quase em tempo real. Instale a solução Activity log Analytics para gravar no espaço de trabalho do Log Analytics. Consulte [Coletar e analisar os logs de atividades do Azure no Log Analytics](activity-log-collect.md). |

### <a name="azure-resources"></a>Recursos do Azure

| Dados | Descrição |
|:---|:---|
| Diagnóstico de recursos | Configure as configurações de diagnóstico para gravar em dados de diagnóstico, incluindo métricas para um espaço de trabalho do Log Analytics. Consulte [os logs de recursos do Stream Azure no Log Analytics](resource-logs-collect-workspace.md). |
| Soluções de monitoramento | As soluções de monitoramento registram dados coletados no espaço de trabalho do Log Analytics. Consulte [detalhes de coleta de dados para soluções de gerenciamento no Azure](../insights/solutions-inventory.md) para obter uma lista de soluções. Consulte [soluções de monitoramento no Azure Monitor](../insights/solutions.md) para obter detalhes sobre a instalação e o uso de soluções. |
| Métricas | Envie métricas da plataforma para os recursos do Azure Monitor para um espaço de trabalho do Log Analytics para reter dados de log por períodos mais longos e para realizar análises complexas com outros tipos de dados usando a [linguagem de consulta Kusto](/azure/kusto/query/). Consulte [os logs de recursos do Stream Azure no Log Analytics](resource-logs-collect-storage.md). |
| Armazenamento de tabelas do Azure | Coletar dados do armazenamento Azure onde alguns recursos do Azure gravam dados de monitoramento. Consulte [Usar o armazenamento de blob do Azure para armazenamento de mesa IIS e Azure para eventos com o Log Analytics](diagnostics-extension-logs.md). |

### <a name="virtual-machines"></a>Máquinas Virtuais

| Dados | Descrição |
|:---|:---|
|  Fontes de dados de agente | As fontes de dados coletadas dos agentes [do Windows](agent-windows.md) e [Linux](../learn/quick-collect-linux-computer.md) incluem eventos, dados de desempenho e registros personalizados. Consulte [as fontes de dados do Agente no Azure Monitor](data-sources.md) para obter uma lista de fontes de dados e detalhes sobre a configuração. |
| Soluções de monitoramento | As soluções de monitoramento registram dados coletados dos agentes para o espaço de trabalho do Log Analytics. Consulte [detalhes de coleta de dados para soluções de gerenciamento no Azure](../insights/solutions-inventory.md) para obter uma lista de soluções. Consulte [soluções de monitoramento no Azure Monitor](../insights/solutions.md) para obter detalhes sobre a instalação e o uso de soluções. |
| System Center Operations Manager | Conecte o grupo de gerenciamento do Operations Manager ao Azure Monitor para coletar dados de eventos e desempenho de agentes locais em logs. Consulte [O Connect Operations Manager para registrar o Analytics](om-agents.md) para obter detalhes sobre essa configuração. |


### <a name="applications"></a>Aplicativos

| Dados | Descrição |
|:---|:---|
| Solicitações e exceções | Dados detalhados sobre solicitações e exceções de aplicativos estão nas _tabelas de solicitações,_ _pageViews_e _exceções._ As chamadas para [componentes externos](../app/asp-net-dependencies.md) estão na tabela _de dependências._ |
| Uso e desempenho | O desempenho do aplicativo está disponível nas _tabelas solicitações,_ _navegadortimings_ e _performanceCounters._ Os dados para [métricas personalizadas](../app/api-custom-events-metrics.md#trackevent) estão na tabela _customMetrics._|
| Dados de rastreamento | Os resultados do [rastreamento distribuído](../app/distributed-tracing.md) são armazenados na tabela _de traçados._ |
| Testes de disponibilidade | Os dados de resumo dos testes de [disponibilidade](../app/monitor-web-app-availability.md) são armazenados na tabela _disponibilidadeResultados._ Os dados detalhados desses testes estão em armazenamento separado e acessados a partir de Insights de Aplicativos no portal Azure. |

### <a name="insights"></a>Insights

| Dados | Descrição |
|:---|:---|
| Azure Monitor para contêineres | Dados de inventário e desempenho coletados pelo [Azure Monitor para contêineres](../insights/container-insights-overview.md). Consulte [os detalhes da coleta de dados](../insights/container-insights-log-search.md#container-records) do contêiner para obter uma lista das tabelas. |
| Azure Monitor para VMs | Mapeie e dados de desempenho coletados pelo [Monitor Azure para VMs](../insights/vminsights-overview.md). Consulte [Como consultar os registros do Azure Monitor para VMs para](../insights/vminsights-log-search.md) obter detalhes sobre a consulta a esses dados. |

### <a name="custom"></a>Personalizado 

| Dados | Descrição |
|:---|:---|
| API REST | Escreva dados em um espaço de trabalho do Log Analytics de qualquer cliente REST. Consulte [Enviar dados de log para o Azure Monitor com a API http de coletor de dados](data-collector-api.md) para obter detalhes.
| Aplicativo Lógico | Escreva todos os dados em um espaço de trabalho do Log Analytics a partir de um fluxo de trabalho do Logic App com a ação **de coletor de dados do Azure Log Analytics.** |

### <a name="security"></a>Segurança

| Dados | Descrição |
|:---|:---|
| Central de Segurança do Azure | [O Azure Security Center](/azure/security-center/) armazena dados que coleta em um espaço de trabalho do Log Analytics, onde podem ser analisados com outros dados de log. Consulte [a coleta de dados no Azure Security Center](../../security-center/security-center-enable-data-collection.md) para obter detalhes sobre a configuração do espaço de trabalho. |
| Azure Sentinel | [O Azure Sentinel](/azure/sentinel/) armazena dados de fontes de dados em um espaço de trabalho do Log Analytics. Consulte [Conectar fontes de dados](/azure/sentinel/connect-data-sources).  |


## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre a [plataforma de dados do Azure Monitor](data-platform.md).
- Saiba mais sobre [métricas no Azure Monitor](data-platform-metrics.md).
- Saiba mais sobre os [dados de monitoramento disponíveis](data-sources.md) para diferentes recursos no Azure.
