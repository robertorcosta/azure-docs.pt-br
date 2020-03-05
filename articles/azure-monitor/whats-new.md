---
title: Novidades da documentação do Azure Monitor
description: Atualizações significativas na documentação do Azure Monitor atualizada a cada mês.
ms.subservice: ''
ms.topic: overview
author: bwren
ms.author: bwren
ms.date: 02/05/2020
ms.openlocfilehash: ecee13850e735f827a5465e0f49039f514afe233
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77657734"
---
# <a name="whats-new-in-azure-monitor-documentation"></a>Quais são as novidades da documentação do Azure Monitor?
Este artigo lista os artigos sobre o Azure Monitor que são novos ou que foram atualizados de maneira significativa. Ele será atualizado na primeira semana de cada mês para incluir as atualizações de artigos do mês anterior.

## <a name="january-2020"></a>Janeiro de 2020

### <a name="general"></a>Geral
- [O que é monitorado pelo Azure Monitor?](monitor-reference.md) – novo artigo.

### <a name="agents"></a>Agentes
- [Coletar dados de log do agente do Azure Log Analytics](platform/log-analytics-agent.md): atualização da tabela de requisitos do firewall de rede.


### <a name="alerts"></a>Alertas
- [Criar e gerenciar grupos de ações no portal do Azure](platform/action-groups.md): remoção de configuração não mais necessária do Functions v2.
- [Criar um alerta de métrica com um modelo do Resource Manager](platform/alerts-metric-create-templates.md): adição de exemplo para o parâmetro *ignoreDataBefore*.  Adição de restrições sobre regras de vários critérios.
- [Como usar a API REST de Alerta do Log Analytics](platform/api-alerts.md): correção de exemplo JSON.


### <a name="application-insights"></a>Application Insights
- [Endereços IP usados pelo Application Insights e pelo Log Analytics](app/ip-addresses.md): atualização da seção Teste de disponibilidade com Como adicionar uma regra de porta de entrada para permitir o tráfego usando grupos de segurança de rede do Azure.
- [Solução de problemas com o Azure Application Insights Profiler](app/profiler-troubleshooting.md): atualização da solução de problemas geral.
- [Amostragem de telemetria no Azure Application Insights](app/sampling.md): atualização e reestruturação para aprimorar a legibilidade de acordo com os comentários dos clientes.


### <a name="data-security"></a>Segurança de dados
- [Configuração da chave gerenciada pelo cliente do Azure Monitor](platform/customer-managed-keys.md): novo artigo.

### <a name="insights-and-solutions"></a>Insights e soluções

#### <a name="azure-monitor-for-containers"></a>Azure Monitor para Contêineres
- [Configurar o Azure Monitor para coleta de dados do agente de contêineres](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-agent-config): adição de detalhes sobre atualização do agente no Red Hat OpenShift no Azure e de informações adicionais para distinguir os métodos de atualização do agente.
- [Criar alertas de desempenho do Azure Monitor para contêineres](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-alerts): revisão de informações e atualização de etapas para criar um alerta sobre os dados de desempenho armazenados no workspace usando alertas de contexto do workspace.
- [Monitoramento do Kubernetes com o Azure Monitor para contêineres](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-analyze): atualização do artigo de visão geral e do artigo de análise sobre o suporte de clusters do Windows Kubernetes.
- [Configurar clusters do Red Hat OpenShift no Azure com o Azure Monitor para contêineres](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-azure-redhat-setup): adição de detalhes sobre atualização do agente no Red Hat OpenShift do Azure e de informações adicionais para distinguir os métodos de atualização do agente.
- [Configurar clusters Kubernetes Híbridos com o Azure Monitor para contêineres](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-hybrid-setup): atualização para refletir o suporte adicionado para a porta segura 10250 com o cAdvisor do Kubelet.
- [Como gerenciar o agente do Azure Monitor para contêineres](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-manage-agent): atualização de detalhes relacionados ao comportamento e à configuração do recorte de métrica com o Red Hat OpenShift no Azure comparado a outros tipos de clusters Kubernetes.
- [Configurar o Azure Monitor para contêineres: integração ao Prometheus](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-prometheus-integration): atualização de detalhes relacionados ao comportamento e à configuração do recorte de métrica com o Red Hat OpenShift no Azure comparado a outros tipos de clusters Kubernetes.
- [Como atualizar o Azure Monitor para contêineres para métricas](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-update-metrics): atualização de detalhes relacionados ao comportamento e à configuração do recorte de métrica com o Red Hat OpenShift no Azure comparado a outros tipos de clusters Kubernetes.


#### <a name="azure-monitor-for-vms"></a>Azure Monitor para VMs
- [Perguntas frequentes sobre o Azure Monitor para VMs (GA)](insights/vminsights-ga-release-faq.md): informações sobre como atualizar o workspace e os agentes para a nova versão.

#### <a name="office-365"></a>Office 365
- [Solução de gerenciamento do Office 365 no Azure](insights/solution-office-365.md): adição de detalhes e perguntas frequentes sobre a migração para a solução do Office 365 no Azure Sentinel. Remoção da seção sobre integração.



### <a name="logs"></a>Logs
- [Gerenciar workspaces do Log Analytics no Azure Monitor](platform/manage-access.md): atualizações de Não ações.
- [Gerenciar o uso e os custos dos logs do Azure Monitor](platform/manage-cost-storage.md): adição de esclarecimento sobre o cálculo do volume de dados na seção Modelo de preços.
- [Usar modelos do Azure Resource Manager para criar e configurar um workspace do Log Analytics](platform/template-workspace-configuration.md): atualização de modelo com novos tipos de preço.


### <a name="platform-logs"></a>Logs de plataforma
- [Coletar o log de atividades do Azure com as configurações de diagnóstico – Azure Monitor](platform/diagnostic-settings-legacy.md): informações adicionais sobre as propriedades alteradas.
- [Exportar o log de atividades do Azure](platform/activity-log-export.md): atualização para as alterações na interface do usuário. 





## <a name="december-2019"></a>Dezembro de 2019

### <a name="agents"></a>Agentes
- [Conectar computadores Linux ao Azure Monitor](platform/agent-linux.md): novo artigo.

### <a name="alerts"></a>Alertas
- [Criar um alerta de métrica com um modelo do Resource Manager](platform/alerts-metric-create-templates.md): adição de exemplo de métrica personalizada.
- [Como criar alertas com limites dinâmicos no Azure Monitor](platform/alerts-dynamic-thresholds.md): adição de seção sobre como interpretar gráficos de limites dinâmicos.
- [Visão geral do monitoramento de alertas e notificações no Azure](platform/alerts-overview.md): atualização de consulta do Resource Graph.
- [Recursos compatíveis com alertas de métrica no Azure Monitor](platform/alerts-metric-near-real-time.md): atualização de métricas e dimensões compatíveis.
- [Alternar a API de alertas herdada do Log Analytics para a nova API de Alertas do Azure](platform/alerts-log-api-switch.md): adição de observação sobre o nome de alerta modificado.
- [Entender como funcionam os alertas de métrica no Azure Monitor.](platform/alerts-metric-overview.md) : adição de tipos de recursos compatíveis com o monitoramento em escala.

### <a name="application-insights"></a>Application Insights
- [Application Insights para aplicativos do Worker Service (aplicativos não HTTP)](app/worker-service.md): adição do nível de registros em log padrão ao código C#. Atualização da versão de referência do pacote.
- [Referência de ApplicationInsights.config – Azure](app/configuration-with-applicationinsights-config.md): atualização de código de exemplo.
- [Automatizar o Azure Application Insights com o PowerShell](app/powershell.md): atualização do modelo do Resource Manager.
- [Pacotes NuGet do Application Insights do Azure Monitor](app/nuget.md): atualização de versões de pacote.
- [Criar um recurso do Azure Application Insights](app/create-new-resource.md): adição de observação ao nome global exclusivo.
- [Diagnóstico com o Live Metrics Stream – Azure Application Insights](app/live-stream.md): atualização do requisito de versão do SDK do ASP.NET Core.
- [Contadores de eventos no Application Insights](app/eventcounters.md): atualização de categoria e tabela para customMetrics.
- [Explorar os logs de rastreamento do Java no Azure Application Insights](app/java-trace-logs.md): adição de configuração do limite de log do agente Java.
- [Endereços IP usados pelo Application Insights e pelo Log Analytics](app/ip-addresses.md): atualização de endereços IP do Live Metrics Stream.
- [Monitorar o desempenho dos Serviços de Aplicativos do Azure](app/azure-web-apps.md): adição de suporte para o ASP.NET Core 3.0. 
- [Monitorar aplicativos Python com o Azure Monitor (versão prévia)](app/opencensus-python.md): adição de esclarecimento sobre o mapeamento de esquema do OpenCensus Python para o esquema do Azure Monitor
- [Notas sobre a versão do Azure Application Insights](app/release-notes.md): adição de observações para versões mais antigas.
- [Canais de telemetria no Azure Application Insights](app/telemetry-channels.md): atualização da duração de dados descartados durante o período estendido de conexão perdida.
- [Amostragem de telemetria no Azure Application Insights](app/sampling.md): correção de snippet de código de TelemetryInitializer personalizado.
- [Solução de problemas do Application Insights em um projeto Web Java](app/java-troubleshoot.md): remoção da instrução sobre o não suporte à coleta de dependências no JDK 9.

### <a name="insights-and-solutions"></a>Insights e soluções
- [Perguntas frequentes sobre o Azure Monitor para contêineres](insights/container-insights-faq.md): adição de pergunta sobre os campos Imagem e Nome.
- [Solução Análise de SQL do Azure no Azure Monitor](insights/azure-sql.md): atualização do suporte de Instância Gerenciada em Esperas do banco de dados.
- [Configurar o Azure Monitor para coleta de dados do agente de contêineres](insights/container-insights-agent-config.md): adição de configuração para enrich_container_logs.
- [Configurar clusters Kubernetes Híbridos com o Azure Monitor para contêineres](insights/container-insights-hybrid-setup.md): adição da seção de solução de problemas.
- [Monitorar o status de replicação do Active Directory com o Azure Monitor](insights/ad-replication-status.md): atualização de pré-requisito do .NET Framework.
- [Solução Monitor de Desempenho de Rede no Azure](insights/network-performance-monitor.md): adição de regiões com suporte.
- [Otimizar seu ambiente do Active Directory com o Azure Monitor](insights/ad-assessment.md): atualização de pré-requisito do .NET Framework.
- [Otimizar seu ambiente do SQL Server com o Azure Monitor](insights/sql-assessment.md): atualização de pré-requisito do .NET Framework.
- [Otimizar seu ambiente do System Center Operations Manager com o Azure Log Analytics](insights/scom-assessment.md): atualização de pré-requisito do .NET Framework.
- [Conexões compatíveis com o Conector de Gerenciamento de Serviços de TI no Azure Log Analytics](platform/itsmc-connections.md): adição de Nova York aos pré-requisitos de ID do cliente e segredo do cliente.

### <a name="logs"></a>Logs
- [Excluir e recuperar o workspace do Azure Log Analytics](platform/delete-workspace.md): adição de método do PowerShell.
- [Como criar sua implantação de logs do Azure Monitor](platform/design-logs-deployment.md): aumento da taxa de ingestão para um workspace.

### <a name="metrics"></a>Métricas
- [Métricas de plataforma do Azure Monitor exportáveis por meio de Configurações de Diagnóstico](platform/metrics-supported-export-diagnostic-settings.md): novo artigo.

### <a name="platform-logs"></a>Logs de plataforma
Atualização de vários artigos como parte da reestruturação de conteúdo dos logs de plataforma de acordo com o novo recurso de configuração do log de atividades usando as configurações de diagnóstico.

- [Arquivar logs de recursos do Azure na conta de armazenamento](platform/resource-logs-collect-storage.md)
- [Esquema de eventos do log de atividades do Azure](platform/activity-log-schema.md)
- [Limites do serviço Azure Monitor](service-limits.md)
- [Coletar e analisar os logs de atividades do Azure no workspace do Log Analytics](platform/activity-log-collect.md)
- [Coletar o log de atividades do Azure com as configurações de diagnóstico (versão prévia) – Azure Monitor](platform/diagnostic-settings-legacy.md)
- [Coletar os logs de atividades do Azure em um workspace do Log Analytics nos locatários do Azure](platform/activity-log-collect-tenants.md)
- [Coletar os logs de recursos do Azure no workspace do Log Analytics](platform/resource-logs-collect-workspace.md)
- [Criar uma configuração de diagnóstico no Azure usando o modelo do Resource Manager](platform/diagnostic-settings-template.md)
- [Criar uma configuração de diagnóstico para coletar logs e métricas no Azure](platform/diagnostic-settings.md)
- [Exportar o log de atividades do Azure](platform/activity-log-export.md)
- [Visão geral dos logs de plataforma do Azure](platform/platform-logs-overview.md)
- [Transmitir dados de monitoramento do Azure para um hub de eventos](platform/stream-monitoring-data-event-hubs.md)
- [Transmitir logs de plataforma do Azure para um hub de eventos](platform/resource-logs-stream-event-hubs.md)

### <a name="quickstarts-and-tutorials"></a>Inícios Rápidos e tutoriais

- [Criar um gráfico de métricas no Azure Monitor](learn/tutorial-metrics-explorer.md): novo artigo.
- [Coletar logs de recursos de um recurso do Azure e analisá-los com o Azure Monitor](learn/tutorial-resource-logs.md): novo artigo.
- [Monitorar um recurso do Azure com o Azure Monitor](learn/quick-monitor-azure-resource.md): novo artigo.
   
## <a name="next-steps"></a>Próximas etapas

- Caso deseje enviar sua contribuição para a documentação do Azure Monitor, confira o [Guia do colaborador do Docs](https://docs.microsoft.com/contribute/).