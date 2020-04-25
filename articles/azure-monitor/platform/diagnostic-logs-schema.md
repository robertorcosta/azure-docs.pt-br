---
title: Serviços e esquemas com suporte dos logs de recursos do Azure
description: Entenda os serviços com suporte e o esquema de eventos para logs de recursos do Azure.
ms.subservice: logs
ms.topic: reference
ms.date: 10/22/2019
ms.openlocfilehash: 7183c0b268342d08fe7c0ed79c7fa589e3e28afe
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82128461"
---
# <a name="supported-services-schemas-and-categories-for-azure-resource-logs"></a>Serviços, esquemas e categorias com suporte para logs de recursos do Azure

> [!NOTE]
> Os logs de recursos eram anteriormente conhecidos como logs de diagnóstico.

[Azure monitor logs de recursos](../../azure-monitor/platform/platform-logs-overview.md) são logs emitidos pelos serviços do Azure que descrevem a operação desses serviços ou recursos. Todos os logs de recursos disponíveis por meio de Azure Monitor compartilham um esquema comum de nível superior, com flexibilidade para cada serviço para emitir propriedades exclusivas para seus próprios eventos.

Uma combinação do tipo de recurso (disponível na propriedade `resourceId`) e da `category` identifica exclusivamente um esquema. Este artigo descreve o esquema de nível superior para logs de recursos e links para o Schemata para cada serviço.

## <a name="top-level-resource-logs-schema"></a>Esquema de logs de recursos de nível superior

| Nome | Obrigatório/Opcional | Descrição |
|---|---|---|
| time | Necessária | O carimbo de data/hora (UTC) do evento. |
| resourceId | Necessária | A ID do recurso que emitiu o evento. Para serviços de locatário, isso é o /tenants/tenant-id/providers/provider-name do formulário. |
| tenantId | Necessário para os logs de locatário | A ID de locatário do que esse evento está vinculado ao locatário do Active Directory. Essa propriedade só é usada para logs de nível de locatário, ele não aparece nos logs de nível do recurso. |
| operationName | Necessária | O nome da operação representada por esse evento. Se o evento representa uma operação RBAC, esse é o nome da operação RBAC (por exemplo, Microsoft.Storage/storageAccounts/blobServices/blobs/Read). Normalmente modeladas na forma de uma operação do Resource Manager, mesmo que não sejam as operações do Resource Manager documentadas reais (`Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>`) |
| operationVersion | Opcional | A api-version associada à operação, se a operationName foi executada usando uma API (por exemplo, `http://myservice.windowsazure.net/object?api-version=2016-06-01`). Se não houver nenhuma API que corresponde a essa operação, a versão representará a versão dessa operação, caso as propriedades associadas à operação sejam alteradas no futuro. |
| category | Necessária | A categoria de log do evento. Categoria é a granularidade na qual você pode habilitar ou desabilitar os logs em determinado recurso. As propriedades exibidas no blob de propriedades de um evento são as mesmas em uma categoria de log e um tipo de recurso específicos. As categorias de log típicas são "auditoria" "operacional" "execução" e "solicitação". |
| resultType | Opcional | O status do evento. Os valores típicos incluem Iniciado, Em Andamento, Com Êxito, Com Falha, Ativo e Resolvido. |
| resultSignature | Opcional | O substatus do evento. Se essa operação corresponder a uma chamada à API REST, esse será o código de status HTTP da chamada REST correspondente. |
| resultDescription | Opcional | A descrição de texto estático dessa operação, por exemplo, "Obter arquivo de armazenamento". |
| durationMs | Opcional | A duração da operação em milissegundos. |
| callerIpAddress | Opcional | O endereço IP do chamador, caso a operação corresponda a uma chamada à API proveniente de uma entidade com um endereço IP disponível publicamente. |
| correlationId | Opcional | Um GUID usado para agrupar um conjunto de eventos relacionados. Normalmente, se dois eventos têm o mesmo operationName, mas dois status diferentes (por exemplo, "Iniciado" e "êxito"), eles compartilham a mesma ID de correlação. Isso também pode representar outras relações entre os eventos. |
| identidade | Opcional | Um blob JSON que descreve a identidade do usuário ou do aplicativo que realizou a operação. Normalmente, isso inclui a autorização e as declarações/token JWT do Active Directory. |
| Nível | Opcional | O nível de severidade do evento. Precisa ser Informativo, Aviso, Erro ou Crítico. |
| local | Opcional | A região do recurso que emite o evento, por exemplo, "Leste dos EUA" ou "sul da França" |
| properties | Opcional | As propriedades estendidas relacionadas a essa categoria específica de eventos. Todas as propriedades personalizadas/exclusivas devem ser colocadas dentro dessa "parte B" do esquema. |

## <a name="service-specific-schemas-for-resource-logs"></a>Esquemas específicos do serviço para logs de recursos
O esquema para os logs de diagnóstico de recurso varia dependendo do recurso e da categoria do log. Esta lista mostra todos os serviços que disponibilizam logs de recursos e links para o serviço e o esquema específico de categoria, quando disponíveis.

| Serviço | Esquema e Documentos |
| --- | --- |
| Azure Active Directory | [Visão geral](../../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md), esquema de [log de auditoria](../../active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema.md) e [esquemas de entradas](../../active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) |
| Serviços de análise | https://azure.microsoft.com/blog/azure-analysis-services-integration-with-azure-diagnostic-logs/ |
| Gerenciamento de API | [Logs de recursos de gerenciamento de API](../../api-management/api-management-howto-use-azure-monitor.md#resource-logs) |
| Gateways do Aplicativo |[Registro em log do gateway de aplicativo](../../application-gateway/application-gateway-diagnostics.md) |
| Automação do Azure |[Análise de log para automação do Azure](../../automation/automation-manage-send-joblogs-log-analytics.md) |
| Lote do Azure |[Log do lote do Azure](../../batch/batch-diagnostics.md) |
| Banco de Dados do Azure para MySQL | [Banco de dados do Azure para logs de Diagnóstico do MySQL](../../mysql/concepts-server-logs.md#diagnostic-logs) |
| Banco de Dados do Azure para PostgreSQL | [Logs do banco de dados do Azure para PostgreSQL](../../postgresql/concepts-server-logs.md#resource-logs) |
| Azure Data Explorer | [Logs de Data Explorer do Azure](/azure/data-explorer/using-diagnostic-logs) |
| Serviços Cognitivos | [Registro em log para serviços cognitivas do Azure](../../cognitive-services/diagnostic-logging.md) |
| Registro de Contêiner | [Registro em log do registro de contêiner do Azure](../../container-registry/container-registry-diagnostics-audit-logs.md) |
| Rede de Distribuição de Conteúdo | [Logs do Azure para CDN](../../cdn/cdn-azure-diagnostic-logs.md) |
| CosmosDB | [Registro em log do Azure Cosmos DB](../../cosmos-db/logging.md) |
| Data Factory | [Monitorar data factories usando o Azure Monitor](../../data-factory/monitor-using-azure-monitor.md) |
| Data Lake Analytics |[Acessando logs para Azure Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Data Lake Store |[Acessando logs para Azure Data Lake Store](../../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Hubs de Eventos |[Logs de hubs de eventos do Azure](../../event-hubs/event-hubs-diagnostic-logs.md) |
| ExpressRoute | Esquema não disponível. |
| Firewall do Azure | Esquema não disponível. |
| Hub IoT | [Operações do Hub IoT](../../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| Key Vault |[Log de Azure Key Vault](../../key-vault/general/logging.md) |
| Serviço do Kubernetes |[Log de kubernetes do Azure](../../aks/view-master-logs.md#log-event-schema) |
| Load Balancer |[Análise de log para Balanceador de Carga do Azure](../../load-balancer/load-balancer-monitor-log.md) |
| Aplicativos Lógicos |[Esquema de acompanhamento personalizado dos Aplicativos Lógicos B2B](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Grupos de segurança de rede |[Log Analytics para grupos de segurança de rede (NSGs)](../../virtual-network/virtual-network-nsg-manage-log.md) |
| Proteção contra DDOS | [Gerenciar Proteção contra DDoS do Azure Standard](../../virtual-network/manage-ddos-protection.md) |
| Power BI dedicado | [Registro em log para Power BI Embedded no Azure](https://docs.microsoft.com/power-bi/developer/azure-pbie-diag-logs) |
| Serviços de Recuperação | [Modelo de dados para o backup do Azure](../../backup/backup-azure-reports-data-model.md)|
| Search |[Habilitação e uso da análise de tráfego de pesquisa](../../search/search-traffic-analytics.md) |
| Service Bus |[Logs do barramento de serviço do Azure](../../service-bus-messaging/service-bus-diagnostic-logs.md) |
| Banco de dados do SQL | [Log do banco de dados SQL do Azure](../../sql-database/sql-database-metrics-diag-logging.md) |
| Stream Analytics |[Logs de trabalho](../../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Gerenciador de Tráfego | [Esquema de log do Gerenciador de tráfego](../../traffic-manager/traffic-manager-diagnostic-logs.md) |
| Redes Virtuais | Esquema não disponível. |
| Gateways de Rede Virtual | Esquema não disponível. |

## <a name="supported-log-categories-per-resource-type"></a>Categorias de log com suporte por tipo de recurso

Algumas categorias só podem ter suporte para tipos específicos de recursos. Essa é uma lista de todas as que estão disponíveis em algum formato.  Por exemplo, as categorias Microsoft. SQL/servidores/bancos de dados não estão disponíveis para todos os tipos de bancos de dados. Para obter mais informações, consulte [informações sobre o log de diagnóstico do banco de dados SQL](../../sql-database/sql-database-metrics-diag-logging.md). 

|Tipo de recurso|Categoria|Nome de exibição da categoria|
|---|---|---|
|Microsoft. AAD/DomainServices|SystemSecurity|SystemSecurity|
|Microsoft. AAD/DomainServices|AccountManagement|AccountManagement|
|Microsoft. AAD/DomainServices|LogonLogoff|LogonLogoff|
|Microsoft. AAD/DomainServices|Objectaccess|Objectaccess|
|Microsoft. AAD/DomainServices|PolicyChange|PolicyChange|
|Microsoft. AAD/DomainServices|PrivilegeUse|PrivilegeUse|
|Microsoft. AAD/DomainServices|DetailTracking|DetailTracking|
|Microsoft. AAD/DomainServices|DirectoryServiceAccess|DirectoryServiceAccess|
|Microsoft. AAD/DomainServices|AccountLogon|AccountLogon|
|microsoft.aadiam/tenants|Signin|Signin|
|Microsoft.AnalysisServices/servers|Mecanismo|Mecanismo|
|Microsoft.AnalysisServices/servers|Serviço|Serviço|
|Microsoft.ApiManagement/service|GatewayLogs|Logs relacionados ao Gateway ApiManagement|
|Microsoft. AppPlatform/Spring|ApplicationConsole|Console de aplicativo|
|Microsoft.Automation/automationAccounts|JobLogs|Logs de trabalho|
|Microsoft.Automation/automationAccounts|JobStreams|Transmissões de trabalho|
|Microsoft.Automation/automationAccounts|DscNodeStatus|Status do nó DSC|
|Microsoft.Batch/batchAccounts|ServiceLog|Logs de serviço|
|Microsoft. BatchAI/Workspaces|BaiClusterEvent|BaiClusterEvent|
|Microsoft. BatchAI/Workspaces|BaiClusterNodeEvent|BaiClusterNodeEvent|
|Microsoft. BatchAI/Workspaces|BaiJobEvent|BaiJobEvent|
|Microsoft. Blockchain/blockchainMembers|BlockchainApplication|Aplicativo Blockchain|
|Microsoft. Blockchain/blockchainMembers|Proxy|Proxy|
|Microsoft.Cdn/profiles/endpoints|CoreAnalytics|Obtém as métricas do ponto de extremidade, como largura de banda, saída etc.|
|Microsoft.ClassicNetwork/networksecuritygroups|Evento de fluxo de regra de grupo de segurança de rede|Evento de fluxo de regra de grupo de segurança de rede|
|Microsoft.CognitiveServices/accounts|Audit|Logs de Auditoria|
|Microsoft.CognitiveServices/accounts|RequestResponse|Logs de Solicitação e Resposta|
|Microsoft.ContainerRegistry/registries|ContainerRegistryRepositoryEvents|Logs do RepositoryEvent (visualização)|
|Microsoft.ContainerRegistry/registries|ContainerRegistryLoginEvents|Eventos de logon (versão prévia)|
|Microsoft.ContainerService/managedClusters|kube-apiserver|Servidor de API do Kubernetes|
|Microsoft.ContainerService/managedClusters|kube-controller-manager|Gerenciador do Controlador do Kubernetes|
|Microsoft.ContainerService/managedClusters|kube-scheduler|Agendador do Kubernetes|
|Microsoft.ContainerService/managedClusters|Kube-auditoria|Kubernetes auditoria|
|Microsoft.ContainerService/managedClusters|cluster-autoscaler|Dimensionador automático de cluster do Kubernetes|
|Microsoft. databricks/espaços de trabalho|dBFS|Sistema de arquivos do Databricks|
|Microsoft. databricks/espaços de trabalho|clusters|Clusters do databricks|
|Microsoft. databricks/espaços de trabalho|accounts|Contas do databricks|
|Microsoft. databricks/espaços de trabalho|jobs|Trabalhos do databricks|
|Microsoft. databricks/espaços de trabalho|notebook|Databricks Notebook|
|Microsoft. databricks/espaços de trabalho|ssh|SSH do databricks|
|Microsoft. databricks/espaços de trabalho|workspace|Espaço de trabalho do databricks|
|Microsoft. databricks/espaços de trabalho|segredos|Segredos do databricks|
|Microsoft. databricks/espaços de trabalho|sqlpermissions|Sqlpermissões do databricks|
|Microsoft. databricks/espaços de trabalho|instancePools|Pools de instância|
|Microsoft. datacatalog/datacatalogs|ScanStatusLogEvent|ScanStatus|
|Microsoft.DataFactory/factories|ActivityRuns|Registro de execuções de atividade de pipeline|
|Microsoft.DataFactory/factories|PipelineRuns|Registro de execuções de pipeline|
|Microsoft.DataFactory/factories|TriggerRuns|Registro de execuções de gatilho|
|Microsoft.DataLakeAnalytics/accounts|Audit|Logs de Auditoria|
|Microsoft.DataLakeAnalytics/accounts|Requests|Logs de solicitação|
|Microsoft.DataLakeStore/accounts|Audit|Logs de Auditoria|
|Microsoft.DataLakeStore/accounts|Requests|Logs de solicitação|
|Microsoft. DataShare/accounts|Compartilhamentos|Compartilhamentos|
|Microsoft. DataShare/accounts|ShareSubscriptions|Compartilhar assinaturas|
|Microsoft. DataShare/accounts|SentShareSnapshots|Instantâneos de compartilhamento enviados|
|Microsoft. DataShare/accounts|ReceivedShareSnapshots|Instantâneos de compartilhamento recebidos|
|Microsoft.DBforMySQL/servers|MySqlSlowLogs|Logs do MySQL Server|
|Microsoft.DBforMySQL/servers|MySqlAuditLogs|Logs de auditoria do MySQL|
|Microsoft.DBforPostgreSQL/servers|PostgreSQLLogs|Logs do PostgreSQL Server|
|Microsoft.DBforPostgreSQL/servers|QueryStoreRuntimeStatistics|As estatísticas de tempo de execução Repositório de Consultas PostgreSQL|
|Microsoft.DBforPostgreSQL/servers|QueryStoreWaitStatistics|Estatísticas de espera Repositório de Consultas PostgreSQL|
|Microsoft. DBforPostgreSQL/serversv2|PostgreSQLLogs|Logs do PostgreSQL Server|
|Microsoft. DBforPostgreSQL/serversv2|QueryStoreRuntimeStatistics|As estatísticas de tempo de execução Repositório de Consultas PostgreSQL|
|Microsoft. DBforPostgreSQL/serversv2|QueryStoreWaitStatistics|Estatísticas de espera Repositório de Consultas PostgreSQL|
|Microsoft. DesktopVirtualization/Workspaces|Ponto de verificação|Ponto de verificação|
|Microsoft. DesktopVirtualization/Workspaces|Erro|Erro|
|Microsoft. DesktopVirtualization/Workspaces|Gerenciamento|Gerenciamento|
|Microsoft. DesktopVirtualization/Workspaces|Feed|Feed|
|Microsoft. DesktopVirtualization/applicationGroups|Ponto de verificação|Ponto de verificação|
|Microsoft. DesktopVirtualization/applicationGroups|Erro|Erro|
|Microsoft. DesktopVirtualization/applicationGroups|Gerenciamento|Gerenciamento|
|Microsoft. DesktopVirtualization/hostPools|Ponto de verificação|Ponto de verificação|
|Microsoft. DesktopVirtualization/hostPools|Erro|Erro|
|Microsoft. DesktopVirtualization/hostPools|Gerenciamento|Gerenciamento|
|Microsoft. DesktopVirtualization/hostPools|Conexão|Conexão|
|Microsoft. DesktopVirtualization/hostPools|HostRegistration|HostRegistration|
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
|Microsoft.Devices/IotHubs|DeviceStreams|Fluxos de dispositivo (visualização)|
|Microsoft.Devices/provisioningServices|DeviceOperations|Operações do Dispositivo|
|Microsoft.Devices/provisioningServices|ServiceOperations|Operações de serviço|
|Microsoft.DocumentDB/databaseAccounts|DataPlaneRequests|DataPlaneRequests|
|Microsoft.DocumentDB/databaseAccounts|MongoRequests|MongoRequests|
|Microsoft.DocumentDB/databaseAccounts|QueryRuntimeStatistics|QueryRuntimeStatistics|
|Microsoft.DocumentDB/databaseAccounts|PartitionKeyStatistics|PartitionKeyStatistics|
|Microsoft.DocumentDB/databaseAccounts|ControlPlaneRequests|ControlPlaneRequests|
|Microsoft. EnterpriseKnowledgeGraph/serviços|AuditEvent|Log do AuditEvent|
|Microsoft. EnterpriseKnowledgeGraph/serviços|Emissão de problemas|Log de emissão de problemas|
|Microsoft. EnterpriseKnowledgeGraph/serviços|Requests|Log de configuração|
|Microsoft.EventHub/namespaces|ArchiveLogs|Logs de arquivo|
|Microsoft.EventHub/namespaces|OperationalLogs|Logs operacionais|
|Microsoft.EventHub/namespaces|AutoScaleLogs|Logs de Escala Automática|
|Microsoft.EventHub/namespaces|KafkaCoordinatorLogs|Logs do coordenador de Kafka|
|Microsoft.EventHub/namespaces|KafkaUserErrorLogs|Logs de erros do usuário Kafka|
|Microsoft.EventHub/namespaces|EventHubVNetConnectionEvent|Logs de conexão de filtragem de VNet/IP|
|Microsoft.EventHub/namespaces|CustomerManagedKeyUserLogs|Logs de chave gerenciado pelo cliente|
|Microsoft. HealthcareApis/serviços|AuditLogs|Logs de auditoria|
|Microsoft.Insights/AutoscaleSettings|AutoscaleEvaluations|Avaliações de Dimensionamento Automático|
|Microsoft.Insights/AutoscaleSettings|AutoscaleScaleActions|Ações de Dimensionamento Automático|
|Microsoft.IoTSpaces/Graph|Trace|Trace|
|Microsoft.IoTSpaces/Graph|Operacional|Operacional|
|Microsoft.IoTSpaces/Graph|Audit|Audit|
|Microsoft.IoTSpaces/Graph|UserDefinedFunction|UserDefinedFunction|
|Microsoft.IoTSpaces/Graph|Entrada|Entrada|
|Microsoft.IoTSpaces/Graph|Saída|Saída|
|Microsoft.KeyVault/vaults|AuditEvent|Logs de Auditoria|
|Microsoft.Kusto/Clusters|SucceededIngestion|Operações de ingestão com êxito|
|Microsoft.Kusto/Clusters|FailedIngestion|Operações de ingestão com falha|
|Microsoft.Logic/workflows|WorkflowRuntime|Eventos de diagnóstico de runtime de fluxo de trabalho|
|Microsoft.Logic/integrationAccounts|IntegrationAccountTrackingEvents|Acompanhar os eventos da Conta de Integração|
|Microsoft.MachineLearningServices/workspaces|AmlComputeClusterEvent|AmlComputeClusterEvent|
|Microsoft.MachineLearningServices/workspaces|AmlComputeClusterNodeEvent|AmlComputeClusterNodeEvent|
|Microsoft.MachineLearningServices/workspaces|AmlComputeJobEvent|AmlComputeJobEvent|
|Microsoft. Media/mediaservices|KeyDeliveryRequests|Solicitações de entrega de chave|
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
|Microsoft. Network/vpnGateways|GatewayDiagnosticLog|Logs de Diagnóstico de Gateway|
|Microsoft. Network/vpnGateways|TunnelDiagnosticLog|Logs de Diagnóstico de Túnel|
|Microsoft. Network/vpnGateways|RouteDiagnosticLog|Logs de Diagnóstico de Rota|
|Microsoft. Network/vpnGateways|IKEDiagnosticLog|Logs de Diagnóstico IKE|
|Microsoft.Network/frontdoors|FrontdoorAccessLog|Log de acesso do Frontdoor|
|Microsoft.Network/frontdoors|FrontdoorWebApplicationFirewallLog|Log de firewall do aplicativo Web Frontdoor|
|Microsoft. Network/p2sVpnGateways|GatewayDiagnosticLog|Logs de Diagnóstico de Gateway|
|Microsoft. Network/p2sVpnGateways|IKEDiagnosticLog|Logs de Diagnóstico IKE|
|Microsoft. Network/p2sVpnGateways|P2SDiagnosticLog|Logs de Diagnóstico P2S|
|Microsoft. Network/bastionHosts|BastionAuditLogs|Logs de auditoria de bastiões|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent|Eventos de alerta do Load Balancer|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|Status de integridade da investigação do Load Balancer|
|Microsoft.PowerBIDedicated/capacities|Mecanismo|Mecanismo|
|Microsoft.RecoveryServices/Vaults|AzureBackupReport|Dados de relatórios de backup do Azure|
|Microsoft.RecoveryServices/Vaults|CoreAzureBackup|Principais dados de backup do Azure|
|Microsoft.RecoveryServices/Vaults|AddonAzureBackupJobs|Dados do trabalho de backup do Azure addon|
|Microsoft.RecoveryServices/Vaults|AddonAzureBackupAlerts|Dados de alerta de backup do Azure addon|
|Microsoft.RecoveryServices/Vaults|AddonAzureBackupPolicy|Dados de política de backup do Azure addon|
|Microsoft.RecoveryServices/Vaults|AddonAzureBackupStorage|Dados de armazenamento de backup do Azure addon|
|Microsoft.RecoveryServices/Vaults|AddonAzureBackupProtectedInstance|Dados de instância protegida do backup do Azure addon|
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
|Microsoft.Storage/storageAccounts/tableServices|StorageRead|StorageRead|
|Microsoft.Storage/storageAccounts/tableServices|StorageWrite|StorageWrite|
|Microsoft.Storage/storageAccounts/tableServices|StorageDelete|StorageDelete|
|Microsoft.Storage/storageAccounts/blobServices|StorageRead|StorageRead|
|Microsoft.Storage/storageAccounts/blobServices|StorageWrite|StorageWrite|
|Microsoft.Storage/storageAccounts/blobServices|StorageDelete|StorageDelete|
|Microsoft.Storage/storageAccounts/fileServices|StorageRead|StorageRead|
|Microsoft.Storage/storageAccounts/fileServices|StorageWrite|StorageWrite|
|Microsoft.Storage/storageAccounts/fileServices|StorageDelete|StorageDelete|
|Microsoft.Storage/storageAccounts/queueServices|StorageRead|StorageRead|
|Microsoft.Storage/storageAccounts/queueServices|StorageWrite|StorageWrite|
|Microsoft.Storage/storageAccounts/queueServices|StorageDelete|StorageDelete|
|Microsoft.StreamAnalytics/streamingjobs|Execução|Execução|
|Microsoft.StreamAnalytics/streamingjobs|Criação|Criação|
|Microsoft. Web/hostingenvironments|AppServiceEnvironmentPlatformLogs|Ambiente do Serviço de Aplicativondo logs da plataforma|
|microsoft.web/sites|FunctionAppLogs|Logs do aplicativo de funções|
|microsoft.web/sites|AppServiceHTTPLogs|Logs de HTTP|
|microsoft.web/sites|AppServiceConsoleLogs|Logs do console do serviço de aplicativo|
|microsoft.web/sites|AppServiceAppLogs|Logs de aplicativo do serviço de aplicativo|
|microsoft.web/sites|AppServiceFileAuditLogs|Logs de auditoria de alteração de conteúdo do site|
|microsoft.web/sites|AppServiceAuditLogs|Acessar logs de auditoria|
|microsoft.web/sites/slots|FunctionAppLogs|Logs do aplicativo de funções|
|microsoft.web/sites/slots|AppServiceHTTPLogs|Logs de HTTP|
|microsoft.web/sites/slots|AppServiceConsoleLogs|Logs do console|
|microsoft.web/sites/slots|AppServiceAppLogs|Logs de aplicativo|
|microsoft.web/sites/slots|AppServiceFileAuditLogs|Logs de auditoria de alteração de conteúdo do site|
|microsoft.web/sites/slots|AppServiceAuditLogs|Acessar logs de auditoria|

## <a name="next-steps"></a>Próximas etapas

* [Saiba mais sobre os logs de recursos](../../azure-monitor/platform/platform-logs-overview.md)
* [Transmitir logs de recurso de recurso para **hubs de eventos**](../../azure-monitor/platform/resource-logs-stream-event-hubs.md)
* [Alterar as configurações de diagnóstico do log de recursos usando a API REST do Azure Monitor](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)
* [Analisar logs do Armazenamento do Azure com o Log Analytics](../../azure-monitor/platform/collect-azure-metrics-logs.md)
