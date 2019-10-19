---
title: Esquemas e serviços com suporte dos logs de diagnóstico do Azure
description: Entenda os serviços com suporte e o esquema de eventos para os logs de diagnóstico do Azure.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: reference
author: rboucher
ms.author: robb
ms.date: 10/11/2018
ms.openlocfilehash: f2d71972fa8acc930800a70193f688246ee7415c
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555567"
---
# <a name="supported-services-schemas-and-categories-for-azure-diagnostic-logs"></a>Serviços, esquemas e categorias com suporte para logs de diagnóstico do Azure

[Azure monitor logs de diagnóstico](../../azure-monitor/platform/resource-logs-overview.md) são logs emitidos pelos serviços do Azure que descrevem a operação desses serviços ou recursos. Todos os logs de diagnóstico disponíveis por meio do Azure Monitor compartilham um esquema comum de nível superior, com flexibilidade para cada serviço para emitir propriedades exclusivas para seus próprios eventos.

Uma combinação do tipo de recurso (disponível na propriedade `resourceId`) e o `category` identificar exclusivamente um esquema. Este artigo descreve o esquema de nível superior para logs de diagnóstico e links para o Schemata para cada serviço.

## <a name="top-level-diagnostic-logs-schema"></a>Esquema de logs de diagnóstico de nível superior

| NaME | Obrigatório/opcional | Descrição |
|---|---|---|
| Momento | Obrigatório | O carimbo de data/hora (UTC) do evento. |
| Identificação | Obrigatório | A ID de recurso do recurso que emitiu o evento. Para serviços de locatário, esse é o formato/Tenants/Tenant-ID/Providers/Provider-Name. |
| tenantId | Necessário para logs de locatário | A ID de locatário do locatário de Active Directory ao qual esse evento está vinculado. Essa propriedade é usada somente para logs em nível de locatário, ela não aparece em logs de nível de recurso. |
| OperationName | Obrigatório | O nome da operação representada por este evento. Se o evento representar uma operação RBAC, esse será o nome da operação RBAC (por exemplo, Microsoft. Storage/storageAccounts/blobservices/BLOBs/leitura). Normalmente modelados na forma de uma operação do Resource Manager, mesmo que não sejam operações reais documentadas do Resource Manager (`Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>`) |
| operationVersion | Opcional | A versão de API associada à operação, se a operationName tiver sido executada usando uma API (por exemplo, `http://myservice.windowsazure.net/object?api-version=2016-06-01`). Se não houver nenhuma API que corresponda a essa operação, a versão representará a versão dessa operação, caso as propriedades associadas à operação sejam alteradas no futuro. |
| Categorias | Obrigatório | A categoria de log do evento. Categoria é a granularidade na qual você pode habilitar ou desabilitar logs em um recurso específico. As propriedades que aparecem dentro do blob de propriedades de um evento são as mesmas dentro de uma categoria de log e tipo de recurso específicos. As categorias de log típicas são "auditoria" "operacional" "execução" e "solicitação". |
| resultType | Opcional | O status do evento. Os valores típicos incluem iniciado, em andamento, bem-sucedido, com falha, ativo e resolvido. |
| resultSignature | Opcional | O substatus do evento. Se essa operação corresponder a uma chamada à API REST, esse será o código de status HTTP da chamada REST correspondente. |
| resultDescription | Opcional | A descrição de texto estático desta operação, por exemplo, "Obter arquivo de armazenamento". |
| durationMs | Opcional | A duração da operação em milissegundos. |
| callerIpAddress | Opcional | O endereço IP do chamador, se a operação corresponder a uma chamada à API que venha de uma entidade com um endereço IP disponível publicamente. |
| correlationId | Opcional | Um GUID usado para agrupar um conjunto de eventos relacionados. Normalmente, se dois eventos tiverem a mesma operationName, mas dois status diferentes (por exemplo, "Iniciado" e "êxito"), eles compartilham a mesma ID de correlação. Isso também pode representar outras relações entre eventos. |
| identidade | Opcional | Um blob JSON que descreve a identidade do usuário ou aplicativo que realizou a operação. Normalmente, isso incluirá a autorização e o token de declarações/JWT do Active Directory. |
| Nível | Opcional | O nível de severidade do evento. Deve ser uma das informações, aviso, erro ou crítico. |
| Local | Opcional | A região do recurso que emite o evento, por exemplo, "Leste dos EUA" ou "sul da França" |
| propriedades | Opcional | Todas as propriedades estendidas relacionadas a esta determinada categoria de eventos. Todas as propriedades personalizadas/exclusivas devem ser colocadas dentro dessa "parte B" do esquema. |

## <a name="service-specific-schemas-for-resource-diagnostic-logs"></a>Esquemas específicos do serviço para logs de diagnóstico de recursos
O esquema para logs de diagnóstico de recurso varia de acordo com a categoria de recurso e log. Esta lista mostra todos os serviços que disponibilizam logs de diagnóstico e links para o serviço e o esquema específico de categoria, quando disponíveis.

| Serviço | Documentos de & de esquema |
| --- | --- |
| Azure Active Directory | [Visão geral](../../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md), esquema de [log de auditoria](../../active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema.md) e [esquemas de entradas](../../active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) |
| Analysis Services | https://azure.microsoft.com/blog/azure-analysis-services-integration-with-azure-diagnostic-logs/ |
| Gerenciamento de API | [Logs de diagnóstico do gerenciamento de API](../../api-management/api-management-howto-use-azure-monitor.md#diagnostic-logs) |
| Gateways de Aplicativo |[Log de diagnóstico do Gateway de Aplicativo](../../application-gateway/application-gateway-diagnostics.md) |
| Automação do Azure |[Análise de log para automação do Azure](../../automation/automation-manage-send-joblogs-log-analytics.md) |
| Lote do Azure |[Logs de diagnóstico do Lote do Azure](../../batch/batch-diagnostics.md) |
| Banco de Dados do Azure para MySQL | [Logs de diagnóstico do banco de dados do Azure para MySQL](../../mysql/concepts-server-logs.md#diagnostic-logs) |
| Banco de Dados do Azure para PostgreSQL | [Logs de diagnóstico do banco de dados do Azure para PostgreSQL](../../postgresql/concepts-server-logs.md#diagnostic-logs) |
| Azure Data Explorer | [Logs de diagnóstico do Azure Data Explorer](../../data-explorer/using-diagnostic-logs.md) |
| Serviços cognitivos | [Log de diagnóstico para serviços cognitivas do Azure](../../cognitive-services/diagnostic-logging.md) |
| Rede de Fornecimento de Conteúdo | [Logs de diagnóstico do Azure para CDN](../../cdn/cdn-azure-diagnostic-logs.md) |
| CosmosDB | [Log de Azure Cosmos DB](../../cosmos-db/logging.md) |
| Data Factory | [Monitorar fábricas de dados usando Azure Monitor](../../data-factory/monitor-using-azure-monitor.md) |
| Data Lake Analytics |[Acessando os logs de diagnóstico do Azure Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Repositório Data Lake |[Acessando os logs de diagnóstico do Azure Data Lake Store](../../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Hubs de Eventos |[Logs de diagnóstico dos hubs de eventos do Azure](../../event-hubs/event-hubs-diagnostic-logs.md) |
| Rota Expressa | Esquema não disponível. |
| Firewall do Azure | Esquema não disponível. |
| Hub IoT | [Operações do Hub IoT](../../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| Key Vault |[Logs do Cofre da Chave do Azure](../../key-vault/key-vault-logging.md) |
| Serviço kubernetes |[Log de kubernetes do Azure](../../aks/view-master-logs.md#log-event-schema) |
| Load Balancer |[Log Analytics para o Azure Load Balancer](../../load-balancer/load-balancer-monitor-log.md) |
| aplicativos Lógicos |[Aplicativos Lógicos B2B o esquema de acompanhamento personalizado](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Grupos de Segurança de Rede |[Análise de logs para NSGs (grupos de segurança de rede)](../../virtual-network/virtual-network-nsg-manage-log.md) |
| Proteção contra DDOS | [Gerenciar a proteção contra DDoS do Azure Standard](../../virtual-network/manage-ddos-protection.md) |
| Power BI dedicado | [Log de diagnóstico para Power BI Embedded no Azure](https://docs.microsoft.com/power-bi/developer/azure-pbie-diag-logs) |
| Serviços de Recuperação | [Modelo de dados para o backup do Azure](../../backup/backup-azure-reports-data-model.md)|
| Pesquisa |[Habilitação e uso da análise de tráfego de pesquisa](../../search/search-traffic-analytics.md) |
| Service Bus |[Logs de diagnóstico do barramento de serviço do Azure](../../service-bus-messaging/service-bus-diagnostic-logs.md) |
| Banco de dados SQL | [Log de diagnóstico do banco de dados SQL do Azure](../../sql-database/sql-database-metrics-diag-logging.md) |
| Stream Analytics |[Logs de diagnóstico do trabalho](../../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Gerenciador de Tráfego | [Esquema de log do Gerenciador de tráfego](../../traffic-manager/traffic-manager-diagnostic-logs.md) |
| Redes Virtuais | Esquema não disponível. |
| Gateways de Rede Virtual | Esquema não disponível. |

## <a name="supported-log-categories-per-resource-type"></a>Categorias de log com suporte por tipo de recurso
|Tipo de recurso|Categoria|Nome de exibição da categoria|
|---|---|---|
|Microsoft. AnalysisServices/Servers|Motores|Motores|
|Microsoft. AnalysisServices/Servers|Serviço|Serviço|
|Microsoft. ApiManagement/Service|GatewayLogs|Logs relacionados ao gateway ApiManagement|
|Microsoft. Automation/automationAccounts|JobLogs|Logs de trabalho|
|Microsoft. Automation/automationAccounts|JobStreams|Fluxos de trabalho|
|Microsoft. Automation/automationAccounts|DscNodeStatus|Status do nó DSC|
|Microsoft. batch/batchAccounts|ServiceLog|Logs de serviço|
|Microsoft. CDN/perfis/pontos de extremidade|CoreAnalytics|Obtém as métricas do ponto de extremidade, por exemplo, largura de banda, saída etc.|
|Microsoft. ClassicNetwork/networksecuritygroups|Evento de fluxo de regra de grupo de segurança de rede|Evento de fluxo de regra de grupo de segurança de rede|
|Microsoft. Cognitivaservices/contas|Submeti|Logs de Auditoria|
|Microsoft. Cognitivaservices/contas|RequestResponse|Logs de solicitação e resposta|
|Microsoft. ContainerService/managedClusters|Kube-apiserver|Servidor de API kubernetes|
|Microsoft. ContainerService/managedClusters|Kube-Controller-Manager|Gerenciador do controlador kubernetes|
|Microsoft. ContainerService/managedClusters|cluster-autoescalar|Autoescalar do cluster kubernetes|
|Microsoft. ContainerService/managedClusters|Kube-Agendador|Agendador de kubernetes|
|Microsoft. ContainerService/managedClusters|Proteja|Webhook de autenticação|
|Microsoft. CustomerInsights/hubs|AuditEvents|AuditEvents|
|Microsoft. datafactory/fábricas|ActivityRuns|Log de execuções de atividade de pipeline|
|Microsoft. datafactory/fábricas|PipelineRuns|Log de execuções de pipeline|
|Microsoft. datafactory/fábricas|TriggerRuns|Log de execuções de gatilho|
|Microsoft.DataLakeAnalytics/accounts|Submeti|Logs de Auditoria|
|Microsoft.DataLakeAnalytics/accounts|Solicitações|Logs de solicitação|
|Microsoft. DataLakeStore/accounts|Submeti|Logs de Auditoria|
|Microsoft. DataLakeStore/accounts|Solicitações|Logs de solicitação|
|Microsoft. DBforMySQL/servidores|MySqlSlowLogs|Logs do servidor MySQL|
|Microsoft. DBforPostgreSQL/servidores|PostgreSQLLogs|Logs do servidor PostgreSQL|
|Microsoft.Devices/IotHubs|conexões|conexões|
|Microsoft.Devices/IotHubs|DeviceTelemetry|Telemetria do dispositivo|
|Microsoft.Devices/IotHubs|C2DCommands|Comandos C2D|
|Microsoft.Devices/IotHubs|DeviceIdentityOperations|Operações de identidade do dispositivo|
|Microsoft.Devices/IotHubs|FileUploadOperations|Operações de upload de arquivo|
|Microsoft.Devices/IotHubs|Circula|Circula|
|Microsoft.Devices/IotHubs|D2CTwinOperations|D2CTwinOperations|
|Microsoft.Devices/IotHubs|C2DTwinOperations|Operações C2D|
|Microsoft.Devices/IotHubs|TwinQueries|Consultas de entrelaçamento|
|Microsoft.Devices/IotHubs|JobsOperations|Operações de trabalhos|
|Microsoft.Devices/IotHubs|DirectMethods|Métodos diretos|
|Microsoft.Devices/IotHubs|E2EDiagnostics|Diagnóstico de E2E (versão prévia)|
|Microsoft.Devices/IotHubs|Figura|Figura|
|Microsoft. Devices/provisioningServices|DeviceOperations|Operações do dispositivo|
|Microsoft. Devices/provisioningServices|Peroperations|Operações de serviço|
|Microsoft. DocumentDB/databaseAccounts|DataPlaneRequests|DataPlaneRequests|
|Microsoft. DocumentDB/databaseAccounts|MongoRequests|MongoRequests|
|Microsoft. DocumentDB/databaseAccounts|QueryRuntimeStatistics|QueryRuntimeStatistics|
|Microsoft. EventHub/namespaces|ArchiveLogs|Logs de arquivo|
|Microsoft. EventHub/namespaces|OperationalLogs|Logs operacionais|
|Microsoft. EventHub/namespaces|AutoScaleLogs|Logs de dimensionamento automático|
|Microsoft. insights/AutoscaleSettings|AutoscaleEvaluations|Avaliações de dimensionamento automático|
|Microsoft. insights/AutoscaleSettings|AutoscaleScaleActions|Ações de escala de dimensionamento automático|
|Microsoft. IoTSpaces/Graph|Rastreamento|Rastreamento|
|Microsoft. IoTSpaces/Graph|Eficiência|Eficiência|
|Microsoft. IoTSpaces/Graph|Submeti|Submeti|
|Microsoft. IoTSpaces/Graph|UserDefinedFunction|UserDefinedFunction|
|Microsoft. IoTSpaces/Graph|Entrada|Entrada|
|Microsoft. IoTSpaces/Graph|Saída|Saída|
|Microsoft. keyvault/cofres|AuditEvent|Logs de Auditoria|
|Microsoft. Logic/workflows|WorkflowRuntime|Eventos de diagnóstico de tempo de execução do fluxo de trabalho|
|Microsoft. Logic/integrationAccounts|IntegrationAccountTrackingEvents|Eventos de acompanhamento da conta de integração|
|Microsoft. Network/networksecuritygroups|NetworkSecurityGroupEvent|Evento de grupo de segurança de rede|
|Microsoft. Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|Contador de regras do grupo de segurança de rede|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent|Load Balancer eventos de alerta|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|Status de integridade da investigação de Load Balancer|
|Microsoft.Network/publicIPAddresses|DDoSProtectionNotifications|Notificações de proteção contra DDoS|
|Microsoft.Network/publicIPAddresses|DDoSMitigationFlowLogs|Logs de fluxo de decisões de mitigação de DDoS|
|Microsoft.Network/publicIPAddresses|DDoSMitigationReports|Relatórios de mitigações de DDoS|
|Microsoft.Network/virtualNetworks|VMProtectionAlerts|Alertas de proteção da VM|
|Microsoft. Network/applicationGateways|ApplicationGatewayAccessLog|Log de acesso do gateway de aplicativo|
|Microsoft. Network/applicationGateways|ApplicationGatewayPerformanceLog|Log de desempenho do gateway de aplicativo|
|Microsoft. Network/applicationGateways|ApplicationGatewayFirewallLog|Log do firewall do gateway de aplicativo|
|Microsoft. Network/securegateways|AzureFirewallApplicationRule|Regra de aplicativo de firewall do Azure|
|Microsoft. Network/securegateways|AzureFirewallNetworkRule|Regra de rede do firewall do Azure|
|Microsoft. Network/azurefirewalls|AzureFirewallApplicationRule|Regra de aplicativo de firewall do Azure|
|Microsoft. Network/azurefirewalls|AzureFirewallNetworkRule|Regra de rede do firewall do Azure|
|Microsoft. Network/virtualNetworkGateways|GatewayDiagnosticLog|Logs de diagnóstico do gateway|
|Microsoft. Network/virtualNetworkGateways|TunnelDiagnosticLog|Logs de diagnóstico de túnel|
|Microsoft. Network/virtualNetworkGateways|RouteDiagnosticLog|Rotear logs de diagnóstico|
|Microsoft. Network/virtualNetworkGateways|IKEDiagnosticLog|Logs de diagnóstico do IKE|
|Microsoft. Network/virtualNetworkGateways|P2SDiagnosticLog|Logs de diagnóstico do P2S|
|Microsoft. Network/trafficManagerProfiles|ProbeHealthStatusEvents|Evento de resultados de integridade de investigação do Traffic Manager|
|Microsoft. Network/expressRouteCircuits|PeeringRouteLog|Logs de tabela de rotas de emparelhamento|
|Microsoft. Network/frontdoors|FrontdoorAccessLog|Log de acesso do frontdoor|
|Microsoft. Network/frontdoors|FrontdoorWebApplicationFirewallLog|Log do firewall do aplicativo Web frontdoor|
|Microsoft. PowerBIDedicated/capacidades|Motores|Motores|
|Microsoft. Recoveryservices/cofres|AzureBackupReport|Dados de relatórios de backup do Azure|
|Microsoft. Recoveryservices/cofres|AzureSiteRecoveryJobs|Trabalhos de Azure Site Recovery|
|Microsoft. Recoveryservices/cofres|AzureSiteRecoveryEvents|Eventos de Azure Site Recovery|
|Microsoft. Recoveryservices/cofres|AzureSiteRecoveryReplicatedItems|Azure Site Recovery itens replicados|
|Microsoft. Recoveryservices/cofres|AzureSiteRecoveryReplicationStats|Estatísticas de replicação Azure Site Recovery|
|Microsoft. Recoveryservices/cofres|AzureSiteRecoveryRecoveryPoints|Pontos de recuperação do Azure Site Recovery|
|Microsoft. Recoveryservices/cofres|AzureSiteRecoveryReplicationDataUploadRate|Taxa de upload de dados de replicação Azure Site Recovery|
|Microsoft. Recoveryservices/cofres|AzureSiteRecoveryProtectedDiskDataChurn|Azure Site Recovery a rotatividade de dados de disco protegido|
|Microsoft. Search/SearchServices|OperationLogs|Logs de Operação|
|Microsoft. ServiceBus/namespaces|OperationalLogs|Logs operacionais|
|Microsoft.Sql/servers/databases|SQLInsights|Insights do SQL|
|Microsoft.Sql/servers/databases|AutomaticTuning|Ajuste automático|
|Microsoft.Sql/servers/databases|QueryStoreRuntimeStatistics|Estatísticas de tempo de execução Repositório de Consultas|
|Microsoft.Sql/servers/databases|QueryStoreWaitStatistics|Estatísticas de espera Repositório de Consultas|
|Microsoft.Sql/servers/databases|Los|Los|
|Microsoft.Sql/servers/databases|DatabaseWaitStatistics|Estatísticas de espera do banco de dados|
|Microsoft.Sql/servers/databases|Tempos limite|Tempos limite|
|Microsoft.Sql/servers/databases|Trava|Trava|
|Microsoft.Sql/servers/databases|Deadlocks|Deadlocks|
|Microsoft.Sql/servers/databases|Submeti|Logs de Auditoria|
|Microsoft.Sql/servers/databases|SQLSecurityAuditEvents|Evento de auditoria de segurança do SQL|
|Microsoft.Sql/servers/databases|DmsWorkers|Trabalhos DMS|
|Microsoft.Sql/servers/databases|ExecRequests|Solicitações de exec|
|Microsoft.Sql/servers/databases|RequestSteps|Etapas da solicitação|
|Microsoft.Sql/servers/databases|Requests|Solicitações SQL|
|Microsoft.Sql/servers/databases|Aguarda|Aguarda|
|Microsoft. SQL/managedInstances|ResourceUsageStats|Estatísticas de uso de recursos|
|Microsoft. SQL/managedInstances|SQLSecurityAuditEvents|Evento de auditoria de segurança do SQL|
|Microsoft. SQL/managedInstances/bancos de dados|SQLInsights|Insights do SQL|
|Microsoft. SQL/managedInstances/bancos de dados|QueryStoreRuntimeStatistics|Estatísticas de tempo de execução Repositório de Consultas|
|Microsoft. SQL/managedInstances/bancos de dados|QueryStoreWaitStatistics|Estatísticas de espera Repositório de Consultas|
|Microsoft. SQL/managedInstances/bancos de dados|Los|Los|
|Microsoft. StreamAnalytics/streamingjobs|Execução|Execução|
|Microsoft. StreamAnalytics/streamingjobs|Criação|Criação|
|Microsoft. Web/sites|FunctionExecutionLogs|Logs de execução de função|
|Microsoft. Web/sites/Slots|FunctionExecutionLogs|Logs de execução de função|

## <a name="next-steps"></a>Próximas etapas

* [Saiba mais sobre os logs de diagnóstico](../../azure-monitor/platform/resource-logs-overview.md)
* [Transmitir logs de diagnóstico de recurso para os **hubs de eventos**](../../azure-monitor/platform/resource-logs-stream-event-hubs.md)
* [Alterar as configurações de diagnóstico de recurso usando a API REST do Azure Monitor](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)
* [Analisar logs do armazenamento do Azure com Log Analytics](../../azure-monitor/platform/collect-azure-metrics-logs.md)
