---
title: Azure Resource Logs suportados serviços e esquemas
description: Entenda os serviços suportados e o esquema de eventos para logs de recursos do Azure.
ms.subservice: logs
ms.topic: reference
ms.date: 10/22/2019
ms.openlocfilehash: 352310a6e489a96c38e85b16e9504d8eb9be38b1
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457239"
---
# <a name="supported-services-schemas-and-categories-for-azure-resource-logs"></a>Serviços, esquemas e categorias suportadas para logs de recursos do Azure

> [!NOTE]
> Os registros de recursos eram anteriormente conhecidos como registros de diagnóstico.

[Os registros de recursos do Azure Monitor](../../azure-monitor/platform/platform-logs-overview.md) são registros emitidos pelos serviços do Azure que descrevem a operação desses serviços ou recursos. Todos os registros de recursos disponíveis através do Azure Monitor compartilham um esquema comum de alto nível, com flexibilidade para cada serviço para emitir propriedades únicas para seus próprios eventos.

Uma combinação do tipo de recurso (disponível na propriedade `resourceId`) e da `category` identifica exclusivamente um esquema. Este artigo descreve o esquema de nível superior para logs de recursos e links para os esquemas de cada serviço.

## <a name="top-level-resource-logs-schema"></a>Esquema de logs de recursos de nível superior

| Nome | Obrigatório/Opcional | Descrição |
|---|---|---|
| time | Obrigatório | O carimbo de data/hora (UTC) do evento. |
| resourceId | Obrigatório | A ID do recurso que emitiu o evento. Para serviços de locatário, isso é o /tenants/tenant-id/providers/provider-name do formulário. |
| tenantId | Necessário para os logs de locatário | A ID de locatário do que esse evento está vinculado ao locatário do Active Directory. Essa propriedade só é usada para logs de nível de locatário, ele não aparece nos logs de nível do recurso. |
| operationName | Obrigatório | O nome da operação representada por esse evento. Se o evento representa uma operação RBAC, esse é o nome da operação RBAC (por exemplo, Microsoft.Storage/storageAccounts/blobServices/blobs/Read). Normalmente modeladas na forma de uma operação do Resource Manager, mesmo que não sejam as operações do Resource Manager documentadas reais (`Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>`) |
| operationVersion | Opcional | A api-version associada à operação, se a operationName foi executada usando uma API (por exemplo, `http://myservice.windowsazure.net/object?api-version=2016-06-01`). Se não houver nenhuma API que corresponde a essa operação, a versão representará a versão dessa operação, caso as propriedades associadas à operação sejam alteradas no futuro. |
| category | Obrigatório | A categoria de log do evento. Categoria é a granularidade na qual você pode habilitar ou desabilitar os logs em determinado recurso. As propriedades exibidas no blob de propriedades de um evento são as mesmas em uma categoria de log e um tipo de recurso específicos. As categorias de registro típicas são "Auditoria" "Operacional" "Execução" e "Solicitação". |
| resultType | Opcional | O status do evento. Os valores típicos incluem Iniciado, Em Andamento, Com Êxito, Com Falha, Ativo e Resolvido. |
| resultSignature | Opcional | O substatus do evento. Se essa operação corresponder a uma chamada à API REST, esse será o código de status HTTP da chamada REST correspondente. |
| resultDescription | Opcional | A descrição de texto estático dessa operação, por exemplo, "Obter arquivo de armazenamento." |
| durationMs | Opcional | A duração da operação em milissegundos. |
| callerIpAddress | Opcional | O endereço IP do chamador, caso a operação corresponda a uma chamada à API proveniente de uma entidade com um endereço IP disponível publicamente. |
| correlationId | Opcional | Um GUID usado para agrupar um conjunto de eventos relacionados. Normalmente, se dois eventos têm o mesmo operationName, mas dois status diferentes (por exemplo, "Started" e "Succeeded"), eles compartilham o mesmo ID de correlação. Isso também pode representar outras relações entre os eventos. |
| identidade | Opcional | Um blob JSON que descreve a identidade do usuário ou do aplicativo que realizou a operação. Normalmente, isso inclui a autorização e as declarações/token JWT do Active Directory. |
| Nível | Opcional | O nível de severidade do evento. Precisa ser Informativo, Aviso, Erro ou Crítico. |
| local | Opcional | A região do recurso que emite o evento, por exemplo, "Leste dos EUA" ou "França Sul" |
| properties | Opcional | As propriedades estendidas relacionadas a essa categoria específica de eventos. Todas as propriedades personalizadas/únicas devem ser colocadas dentro desta "Parte B" do esquema. |

## <a name="service-specific-schemas-for-resource-logs"></a>Esquemas específicos de serviço para logs de recursos
O esquema para os logs de diagnóstico de recurso varia dependendo do recurso e da categoria do log. Esta lista mostra todos os serviços que disponibilizam registros de recursos e links para o serviço e esquema específico de categoria quando disponível.

| Serviço | Esquema e Documentos |
| --- | --- |
| Azure Active Directory | [Visão geral,](../../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md) [esquema de registro](../../active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema.md) de auditoria e esquema de [insign-ins](../../active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) |
| Serviços de análise | https://azure.microsoft.com/blog/azure-analysis-services-integration-with-azure-diagnostic-logs/ |
| Gerenciamento de API | [Logs de recursos de gerenciamento de API](../../api-management/api-management-howto-use-azure-monitor.md#diagnostic-logs) |
| Gateways do Aplicativo |[Registro para gateway de aplicativo](../../application-gateway/application-gateway-diagnostics.md) |
| Automação do Azure |[Análise de log para automação do Azure](../../automation/automation-manage-send-joblogs-log-analytics.md) |
| Lote do Azure |[Registro de lotes azure](../../batch/batch-diagnostics.md) |
| Banco de Dados do Azure para MySQL | [Banco de dados do Azure para logs de Diagnóstico do MySQL](../../mysql/concepts-server-logs.md#diagnostic-logs) |
| Banco de Dados do Azure para PostgreSQL | [Banco de dados Azure para logs PostgreSQL](../../postgresql/concepts-server-logs.md#diagnostic-logs) |
| Azure Data Explorer | [Logs do Azure Data Explorer](/azure/data-explorer/using-diagnostic-logs) |
| Serviços Cognitivos | [Registro para serviços cognitivos do Azure](../../cognitive-services/diagnostic-logging.md) |
| Registro de Contêiner | [Registro de registro de contêineres do Azure](../../container-registry/container-registry-diagnostics-audit-logs.md) |
| Rede de Distribuição de Conteúdo | [Logs do Azure para CDN](../../cdn/cdn-azure-diagnostic-logs.md) |
| CosmosDB | [Registro em log do Azure Cosmos DB](../../cosmos-db/logging.md) |
| Data Factory | [Monitorar data factories usando o Azure Monitor](../../data-factory/monitor-using-azure-monitor.md) |
| Data Lake Analytics |[Acessando logs para Azure Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Data Lake Store |[Acessando logs para Azure Data Lake Store](../../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Hubs de Eventos |[Logs do Azure Event Hubs](../../event-hubs/event-hubs-diagnostic-logs.md) |
| ExpressRoute | Esquema não disponível. |
| Firewall do Azure | Esquema não disponível. |
| Hub IoT | [Operações do Hub IoT](../../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| Key Vault |[Registro do cofre da chave azure](../../key-vault/general/logging.md) |
| Serviço do Kubernetes |[Registro de Kubernetes azure](../../aks/view-master-logs.md#log-event-schema) |
| Load Balancer |[Análise de log para Balanceador de Carga do Azure](../../load-balancer/load-balancer-monitor-log.md) |
| Aplicativos Lógicos |[Esquema de acompanhamento personalizado dos Aplicativos Lógicos B2B](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Grupos de segurança de rede |[Análise de log para NSGs (Network Security Groups, análise de rede)](../../virtual-network/virtual-network-nsg-manage-log.md) |
| Proteção contra DDOS | [Gerenciar Proteção contra DDoS do Azure Standard](../../virtual-network/manage-ddos-protection.md) |
| Power BI dedicado | [Login para Power BI incorporado no Azure](https://docs.microsoft.com/power-bi/developer/azure-pbie-diag-logs) |
| Serviços de Recuperação | [Modelo de dados para backup do Azure](../../backup/backup-azure-reports-data-model.md)|
| Search |[Habilitação e uso da análise de tráfego de pesquisa](../../search/search-traffic-analytics.md) |
| Barramento de Serviço |[Registros de ônibus de serviço do Azure](../../service-bus-messaging/service-bus-diagnostic-logs.md) |
| Banco de Dados SQL | [Registro de banco de dados Azure SQL](../../sql-database/sql-database-metrics-diag-logging.md) |
| Stream Analytics |[Registros de trabalho](../../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Gerenciador de Tráfego | [Esquema de log do gerenciador de tráfego](../../traffic-manager/traffic-manager-diagnostic-logs.md) |
| Redes Virtuais | Esquema não disponível. |
| Gateways de Rede Virtual | Esquema não disponível. |

## <a name="supported-log-categories-per-resource-type"></a>Categorias de log com suporte por tipo de recurso

Algumas categorias só podem ser suportadas para tipos específicos de recursos. Esta é a lista de todos os que estão disponíveis de alguma forma.  Por exemplo, as categorias Microsoft.Sql/servers/databases não estão disponíveis para todos os tipos de bancos de dados. Para obter mais informações, consulte [informações sobre o registro de diagnóstico do Banco de Dados SQL](../../sql-database/sql-database-metrics-diag-logging.md). 

|Tipo de recurso|Categoria|Nome de exibição da categoria|
|---|---|---|
|Serviços Microsoft.AAD/domain|Systemsecurity|Systemsecurity|
|Serviços Microsoft.AAD/domain|AccountManagement|AccountManagement|
|Serviços Microsoft.AAD/domain|LogonLogoff|LogonLogoff|
|Serviços Microsoft.AAD/domain|ObjectAccess|ObjectAccess|
|Serviços Microsoft.AAD/domain|PolicyChange|PolicyChange|
|Serviços Microsoft.AAD/domain|PrivilégioUse|PrivilégioUse|
|Serviços Microsoft.AAD/domain|Rastreamento de detalhes|Rastreamento de detalhes|
|Serviços Microsoft.AAD/domain|DiretórioserviceAccess|DiretórioserviceAccess|
|Serviços Microsoft.AAD/domain|Logon de contas|Logon de contas|
|microsoft.aadiam/tenants|Signin|Signin|
|Microsoft.AnalysisServices/servers|Mecanismo|Mecanismo|
|Microsoft.AnalysisServices/servers|Serviço|Serviço|
|Microsoft.ApiManagement/service|GatewayLogs|Logs relacionados ao Gateway ApiManagement|
|Microsoft.AppPlatform/Spring|Console de aplicativos|Console de aplicativos|
|Microsoft.Automation/automationAccounts|JobLogs|Logs de trabalho|
|Microsoft.Automation/automationAccounts|JobStreams|Transmissões de trabalho|
|Microsoft.Automation/automationAccounts|DscNodeStatus|Status do nó DSC|
|Microsoft.Batch/batchAccounts|ServiceLog|Logs de serviço|
|Microsoft.BatchAI/espaços de trabalho|BaiClusterEvent|BaiClusterEvent|
|Microsoft.BatchAI/espaços de trabalho|BaiClusterNodeEvent|BaiClusterNodeEvent|
|Microsoft.BatchAI/espaços de trabalho|BaiJobEvent|BaiJobEvent|
|Microsoft.Blockchain/blockchainMembers|Aplicativo blockchain|Aplicativo blockchain|
|Microsoft.Blockchain/blockchainMembers|Proxy|Proxy|
|Microsoft.Cdn/profiles/endpoints|CoreAnalytics|Obtém as métricas do ponto de extremidade, como largura de banda, saída etc.|
|Microsoft.ClassicNetwork/networksecuritygroups|Evento de fluxo de regra de grupo de segurança de rede|Evento de fluxo de regra de grupo de segurança de rede|
|Microsoft.CognitiveServices/accounts|Audit|Logs de Auditoria|
|Microsoft.CognitiveServices/accounts|RequestResponse|Logs de Solicitação e Resposta|
|Microsoft.ContainerRegistry/registries|Eventos de registro de contêineres|Registros de eventos repositórios (Visualização)|
|Microsoft.ContainerRegistry/registries|ContêinerRegistroEventos de login|Eventos de login (visualização)|
|Microsoft.ContainerService/managedClusters|kube-apiserver|Servidor de API do Kubernetes|
|Microsoft.ContainerService/managedClusters|kube-controller-manager|Gerenciador do Controlador do Kubernetes|
|Microsoft.ContainerService/managedClusters|kube-scheduler|Agendador do Kubernetes|
|Microsoft.ContainerService/managedClusters|kube-auditoria|Auditoria Kubernetes|
|Microsoft.ContainerService/managedClusters|cluster-autoscaler|Dimensionador automático de cluster do Kubernetes|
|Microsoft.Databricks/espaços de trabalho|dbfs|Sistema de arquivos do Databricks|
|Microsoft.Databricks/espaços de trabalho|clusters|Clusters de tijolos de dados|
|Microsoft.Databricks/espaços de trabalho|accounts|Contas databricks|
|Microsoft.Databricks/espaços de trabalho|jobs|Empregos databricks|
|Microsoft.Databricks/espaços de trabalho|notebook|Databricks Notebook|
|Microsoft.Databricks/espaços de trabalho|ssh|Databricks SSH|
|Microsoft.Databricks/espaços de trabalho|workspace|Databricks Workspace|
|Microsoft.Databricks/espaços de trabalho|segredos|Segredos de Databricks|
|Microsoft.Databricks/espaços de trabalho|Sqlpermissions|Databricks SQLPermissões|
|Microsoft.Databricks/espaços de trabalho|exemplosPools|Pools de instâncias|
|Microsoft.DataCatalog/datacatalogs|ScanStatusLogEvent|ScanStatus|
|Microsoft.DataFactory/factories|ActivityRuns|Registro de execuções de atividade de pipeline|
|Microsoft.DataFactory/factories|PipelineRuns|Registro de execuções de pipeline|
|Microsoft.DataFactory/factories|TriggerRuns|Registro de execuções de gatilho|
|Microsoft.DataLakeAnalytics/accounts|Audit|Logs de Auditoria|
|Microsoft.DataLakeAnalytics/accounts|Requests|Logs de solicitação|
|Microsoft.DataLakeStore/accounts|Audit|Logs de Auditoria|
|Microsoft.DataLakeStore/accounts|Requests|Logs de solicitação|
|Microsoft.DataShare/contas|Compartilhamentos|Compartilhamentos|
|Microsoft.DataShare/contas|Assinaturas de compartilhamento|Compartilhar assinaturas|
|Microsoft.DataShare/contas|SentShareSnapshots|Instantâneos de compartilhamento enviados|
|Microsoft.DataShare/contas|ReceivedShareSnapshots|Instantâneos de compartilhamento recebidos|
|Microsoft.DBforMySQL/servers|MySqlSlowLogs|Logs do MySQL Server|
|Microsoft.DBforMySQL/servers|MySqlAuditLogs|Logs de auditoria MySQL|
|Microsoft.DBforPostgreSQL/servers|PostgreSQLLogs|Logs do PostgreSQL Server|
|Microsoft.DBforPostgreSQL/servers|QueryStoreRuntimeStatistics|Estatísticas de tempo de execução da loja postgreSQL|
|Microsoft.DBforPostgreSQL/servers|QueryStoreWaitStatistics|Estatísticas de espera da loja de consultas postgreSQL|
|Microsoft.DBforPostgreSQL/serversv2|PostgreSQLLogs|Logs do PostgreSQL Server|
|Microsoft.DBforPostgreSQL/serversv2|QueryStoreRuntimeStatistics|Estatísticas de tempo de execução da loja postgreSQL|
|Microsoft.DBforPostgreSQL/serversv2|QueryStoreWaitStatistics|Estatísticas de espera da loja de consultas postgreSQL|
|Microsoft.DesktopVirtualização/espaços de trabalho|Ponto de verificação|Ponto de verificação|
|Microsoft.DesktopVirtualização/espaços de trabalho|Erro|Erro|
|Microsoft.DesktopVirtualização/espaços de trabalho|Gerenciamento|Gerenciamento|
|Microsoft.DesktopVirtualização/espaços de trabalho|Feed|Feed|
|Microsoft.DesktopVirtualização/aplicativoGrupos|Ponto de verificação|Ponto de verificação|
|Microsoft.DesktopVirtualização/aplicativoGrupos|Erro|Erro|
|Microsoft.DesktopVirtualização/aplicativoGrupos|Gerenciamento|Gerenciamento|
|Microsoft.DesktopVirtualização/hostPools|Ponto de verificação|Ponto de verificação|
|Microsoft.DesktopVirtualização/hostPools|Erro|Erro|
|Microsoft.DesktopVirtualização/hostPools|Gerenciamento|Gerenciamento|
|Microsoft.DesktopVirtualização/hostPools|Conexão|Conexão|
|Microsoft.DesktopVirtualização/hostPools|Registro de host|Registro de host|
|Microsoft.Devices/IotHubs|conexões|conexões|
|Microsoft.Devices/IotHubs|DeviceTelemetry|Telemetria de Dispositivo|
|Microsoft.Devices/IotHubs|C2DCommands|Comandos C2C|
|Microsoft.Devices/IotHubs|DeviceIdentityOperations|Operações de Identidade do Dispositivo|
|Microsoft.Devices/IotHubs|FileUploadOperations|Operações de Upload de Arquivo|
|Microsoft.Devices/IotHubs|Rotas|Rotas|
|Microsoft.Devices/IotHubs|D2CTwinOperations|D2CTwinOperations|
|Microsoft.Devices/IotHubs|C2CTwinOperations|Operações de Gêmeos C2D|
|Microsoft.Devices/IotHubs|TwinQueries|Consultas de Gêmeos|
|Microsoft.Devices/IotHubs|JobsOperations|Operações de Trabalhos|
|Microsoft.Devices/IotHubs|DirectMethods|Métodos diretos|
|Microsoft.Devices/IotHubs|DistributedTracing|Rastreamento Distribuído (Versão Prévia)|
|Microsoft.Devices/IotHubs|Configurações|Configurações|
|Microsoft.Devices/IotHubs|Fluxos de dispositivos|Fluxos de dispositivos (Visualização)|
|Microsoft.Devices/provisioningServices|DeviceOperations|Operações do Dispositivo|
|Microsoft.Devices/provisioningServices|ServiceOperations|Operações de serviço|
|Microsoft.DocumentDB/databaseAccounts|DataPlaneRequests|DataPlaneRequests|
|Microsoft.DocumentDB/databaseAccounts|MongoRequests|MongoRequests|
|Microsoft.DocumentDB/databaseAccounts|QueryRuntimeStatistics|QueryRuntimeStatistics|
|Microsoft.DocumentDB/databaseAccounts|Estatísticas de chave de partição|Estatísticas de chave de partição|
|Microsoft.DocumentDB/databaseAccounts|Solicitações de controlplane|Solicitações de controlplane|
|Microsoft.EnterpriseKnowledgeGraph/services|AuditEvent|Registro de eventos de auditoria|
|Microsoft.EnterpriseKnowledgeGraph/services|DataIssue|Registro de DataIssue|
|Microsoft.EnterpriseKnowledgeGraph/services|Requests|Log de configuração|
|Microsoft.EventHub/namespaces|ArchiveLogs|Logs de arquivo|
|Microsoft.EventHub/namespaces|OperationalLogs|Logs operacionais|
|Microsoft.EventHub/namespaces|AutoScaleLogs|Logs de Escala Automática|
|Microsoft.EventHub/namespaces|KafkaCoordinatorLogs|Registros do Coordenador kafka|
|Microsoft.EventHub/namespaces|KafkaUserErrorLogs|Registros de erros do usuário kafka|
|Microsoft.EventHub/namespaces|EventHubVNetConnectionEvent|Logs de conexão de filtragem VNet/IP|
|Microsoft.EventHub/namespaces|Gerenciamento de chavesdo clienteLogs de usuário|Registros de chaves gerenciados pelo cliente|
|Microsoft.HealthcareApis/serviços|AuditLogs|Logs de auditoria|
|Microsoft.Insights/AutoscaleSettings|AutoscaleEvaluations|Avaliações de Dimensionamento Automático|
|Microsoft.Insights/AutoscaleSettings|AutoscaleScaleActions|Ações de Dimensionamento Automático|
|Microsoft.IoTSpaces/Graph|Trace|Trace|
|Microsoft.IoTSpaces/Graph|Operacional|Operacional|
|Microsoft.IoTSpaces/Graph|Audit|Audit|
|Microsoft.IoTSpaces/Graph|UserDefinedFunction|UserDefinedFunction|
|Microsoft.IoTSpaces/Graph|Entrada|Entrada|
|Microsoft.IoTSpaces/Graph|Saída|Saída|
|Microsoft.KeyVault/vaults|AuditEvent|Logs de Auditoria|
|Microsoft.Kusto/Clusters|Ingestion sucedeu|Operações de ingestão bem sucedidas|
|Microsoft.Kusto/Clusters|Ingestion falha|Operações de ingestão fracassadas|
|Microsoft.Logic/workflows|WorkflowRuntime|Eventos de diagnóstico de runtime de fluxo de trabalho|
|Microsoft.Logic/integrationAccounts|IntegrationAccountTrackingEvents|Acompanhar os eventos da Conta de Integração|
|Microsoft.MachineLearningServices/workspaces|AmlComputeClusterEvent|AmlComputeClusterEvent|
|Microsoft.MachineLearningServices/workspaces|AmlComputeClusterNodeEvent|AmlComputeClusterNodeEvent|
|Microsoft.MachineLearningServices/workspaces|AmlComputeJobEvent|AmlComputeJobEvent|
|Microsoft.Media/mediaservices|Solicitações de entrega de chaves|Principais solicitações de entrega|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent|Network Security Group Event|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|Contador de regras de grupo de segurança de rede|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupFlowEvent|Evento de fluxo de regra de grupo de segurança de rede|
|Microsoft.Network/publicIPAddresses|DDoSProtectionNotifications|Notificações de proteção contra DDoS|
|Microsoft.Network/publicIPAddresses|DDoSMitigationFlowLogs|Logs de fluxo de decisões de mitigação de DDoS|
|Microsoft.Network/publicIPAddresses|DDoSMitigationReports|Relatórios de mitigações de DDoS|
|Microsoft.Network/virtualNetworks|VMProtectionAlerts|Alertas de proteção da VM|
|Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog|Log de acesso do Gateway de Aplicativo|
|Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|Log de desempenho do Gateway de Aplicativo|
|Microsoft.Network/applicationGateways|ApplicationGatewayFirewallLog|Log de firewall do Gateway de Aplicativo|
|Microsoft.Network/azurefirewalls|AzureFirewallApplicationRule|Regra de Aplicativo de Firewall do Azure|
|Microsoft.Network/azurefirewalls|AzureFirewallNetworkRule|Regra de Rede de Firewall do Azure|
|Microsoft.Network/virtualNetworkGateways|GatewayDiagnosticLog|Logs de Diagnóstico de Gateway|
|Microsoft.Network/virtualNetworkGateways|TunnelDiagnosticLog|Logs de Diagnóstico de Túnel|
|Microsoft.Network/virtualNetworkGateways|RouteDiagnosticLog|Logs de Diagnóstico de Rota|
|Microsoft.Network/virtualNetworkGateways|IKEDiagnosticLog|Logs de Diagnóstico IKE|
|Microsoft.Network/virtualNetworkGateways|P2SDiagnosticLog|Logs de Diagnóstico P2S|
|Microsoft.Network/trafficManagerProfiles|ProbeHealthStatusEvents|Evento de Resultados de Integridade de Investigação do Gerenciador de Tráfego|
|Microsoft.Network/expressRouteCircuits|PeeringRouteLog|Logs de Tabela de Rota de Emparelhamento|
|Microsoft.Network/vpnGateways|GatewayDiagnosticLog|Logs de Diagnóstico de Gateway|
|Microsoft.Network/vpnGateways|TunnelDiagnosticLog|Logs de Diagnóstico de Túnel|
|Microsoft.Network/vpnGateways|RouteDiagnosticLog|Logs de Diagnóstico de Rota|
|Microsoft.Network/vpnGateways|IKEDiagnosticLog|Logs de Diagnóstico IKE|
|Microsoft.Network/frontdoors|FrontdoorAccessLog|Log de acesso do Frontdoor|
|Microsoft.Network/frontdoors|FrontdoorWebApplicationFirewallLog|Log de firewall do aplicativo Web Frontdoor|
|Microsoft.Network/p2sVpnGateways|GatewayDiagnosticLog|Logs de Diagnóstico de Gateway|
|Microsoft.Network/p2sVpnGateways|IKEDiagnosticLog|Logs de Diagnóstico IKE|
|Microsoft.Network/p2sVpnGateways|P2SDiagnosticLog|Logs de Diagnóstico P2S|
|Microsoft.Network/bastionHosts|BastionAuditLogs|Registros de auditoria de bastião|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent|Eventos de alerta do Load Balancer|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|Status de integridade da investigação do Load Balancer|
|Microsoft.PowerBIDedicated/capacities|Mecanismo|Mecanismo|
|Microsoft.RecoveryServices/Vaults|AzureBackupReport|Dados de relatórios de backup do Azure|
|Microsoft.RecoveryServices/Vaults|CoreAzureBackup|Dados de backup do Core Azure|
|Microsoft.RecoveryServices/Vaults|AddonAzureBackupJobs|Dados de trabalho de backup do Addon Azure|
|Microsoft.RecoveryServices/Vaults|AddonAzureBackupAlertas|Dados de alerta de backup do Addon Azure|
|Microsoft.RecoveryServices/Vaults|Política de backup do AddonAzure|Dados da política de backup do Addon Azure|
|Microsoft.RecoveryServices/Vaults|AddonAzureBackupArmazenamento|Dados de armazenamento de backup do Addon Azure|
|Microsoft.RecoveryServices/Vaults|AddonAzureBackupProtectedInstance|Dados de instância protegida do backup do Addon Azure|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryJobs|Trabalhos do Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryEvents|Eventos do Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicatedItems|Itens replicados do Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationStats|Estatísticas de Replicação do Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryRecoveryPoints|Pontos de Recuperação do Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationDataUploadRate|Taxa de Carregamento de Dados de Replicação do Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryProtectedDiskDataChurn|Rotatividade de Dados de Disco Protegido do Azure Site Recovery|
|Microsoft.Search/searchServices|OperationLogs|Logs de operação|
|Microsoft.ServiceBus/namespaces|OperationalLogs|Logs operacionais|
|Microsoft.Sql/servers/databases|SQLInsights|Insights do SQL|
|Microsoft.Sql/servers/databases|AutomaticTuning|Ajuste automático|
|Microsoft.Sql/servers/databases|QueryStoreRuntimeStatistics|Estatísticas de Runtime do Repositório de Consultas|
|Microsoft.Sql/servers/databases|QueryStoreWaitStatistics|Estatísticas de Espera do Repositório de Consultas|
|Microsoft.Sql/servers/databases|Errors|Errors|
|Microsoft.Sql/servers/databases|DatabaseWaitStatistics|Estatísticas de Espera do Banco de Dados|
|Microsoft.Sql/servers/databases|Tempos limite|Tempos limite|
|Microsoft.Sql/servers/databases|Bloqueios|Bloqueios|
|Microsoft.Sql/servers/databases|Deadlocks|Deadlocks|
|Microsoft.Sql/servers/databases|Audit|Logs de Auditoria|
|Microsoft.Sql/servers/databases|SQLSecurityAuditEvents|Evento de Auditoria de Segurança do SQL|
|Microsoft.Sql/servers/databases|DmsWorkers|Trabalhos DMS|
|Microsoft.Sql/servers/databases|ExecRequests|Solicitações de Exec|
|Microsoft.Sql/servers/databases|RequestSteps|Etapas de solicitação|
|Microsoft.Sql/servers/databases|SqlRequests|Solicitações de Sql|
|Microsoft.Sql/servers/databases|Esperas|Esperas|
|Microsoft.Sql/managedInstances|ResourceUsageStats|Estatísticas de uso de recursos|
|Microsoft.Sql/managedInstances|SQLSecurityAuditEvents|Evento de Auditoria de Segurança do SQL|
|Microsoft.Sql/managedInstances/databases|SQLInsights|Insights do SQL|
|Microsoft.Sql/managedInstances/databases|QueryStoreRuntimeStatistics|Estatísticas de Runtime do Repositório de Consultas|
|Microsoft.Sql/managedInstances/databases|QueryStoreWaitStatistics|Estatísticas de Espera do Repositório de Consultas|
|Microsoft.Sql/managedInstances/databases|Errors|Errors|
|Microsoft.Storage/storageAccounts/tableServices|ArmazenamentoRead|ArmazenamentoRead|
|Microsoft.Storage/storageAccounts/tableServices|ArmazenamentoWrite|ArmazenamentoWrite|
|Microsoft.Storage/storageAccounts/tableServices|Exclusão de armazenamento|Exclusão de armazenamento|
|Microsoft.Storage/storageAccounts/blobServices|ArmazenamentoRead|ArmazenamentoRead|
|Microsoft.Storage/storageAccounts/blobServices|ArmazenamentoWrite|ArmazenamentoWrite|
|Microsoft.Storage/storageAccounts/blobServices|Exclusão de armazenamento|Exclusão de armazenamento|
|Microsoft.Storage/storageAccounts/fileServices|ArmazenamentoRead|ArmazenamentoRead|
|Microsoft.Storage/storageAccounts/fileServices|ArmazenamentoWrite|ArmazenamentoWrite|
|Microsoft.Storage/storageAccounts/fileServices|Exclusão de armazenamento|Exclusão de armazenamento|
|Microsoft.Storage/storageAccounts/queueServices|ArmazenamentoRead|ArmazenamentoRead|
|Microsoft.Storage/storageAccounts/queueServices|ArmazenamentoWrite|ArmazenamentoWrite|
|Microsoft.Storage/storageAccounts/queueServices|Exclusão de armazenamento|Exclusão de armazenamento|
|Microsoft.StreamAnalytics/streamingjobs|Execução|Execução|
|Microsoft.StreamAnalytics/streamingjobs|Criação|Criação|
|ambientes microsoft.web/hosting|AppServiceEnvironmentLogs|Logs de plataforma de ambiente de serviço de aplicativos|
|microsoft.web/sites|FunctionAppLogs|Logs de aplicativos de função|
|microsoft.web/sites|AppServiceHTTPLogs|Logs HTTP|
|microsoft.web/sites|AppServiceConsoleLogs|Logs do console do serviço de aplicativos|
|microsoft.web/sites|AppServiceAppLogs|Logs de aplicativos de serviço de aplicativos|
|microsoft.web/sites|AppServiceFileAuditLogs|Logs de auditoria de alteração de conteúdo do site|
|microsoft.web/sites|AppServiceAuditLogs|Logs de auditoria de acesso|
|microsoft.web/sites/slots|FunctionAppLogs|Logs de aplicativos de função|
|microsoft.web/sites/slots|AppServiceHTTPLogs|Logs HTTP|
|microsoft.web/sites/slots|AppServiceConsoleLogs|Registros de console|
|microsoft.web/sites/slots|AppServiceAppLogs|Logs de aplicativo|
|microsoft.web/sites/slots|AppServiceFileAuditLogs|Logs de auditoria de alteração de conteúdo do site|
|microsoft.web/sites/slots|AppServiceAuditLogs|Logs de auditoria de acesso|

## <a name="next-steps"></a>Próximas etapas

* [Saiba mais sobre registros de recursos](../../azure-monitor/platform/platform-logs-overview.md)
* [Fluxo de logins de recursos para **hubs de eventos**](../../azure-monitor/platform/resource-logs-stream-event-hubs.md)
* [Alterar as configurações de diagnóstico do registro de recursos usando a API REST do Monitor Do Azure](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)
* [Analisar logs do Armazenamento do Azure com o Log Analytics](../../azure-monitor/platform/collect-azure-metrics-logs.md)
