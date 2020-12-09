---
title: Azure Monitor de serviços e categorias com suporte de logs de recursos
description: Referência de Azure Monitor entender os serviços com suporte e o esquema de eventos para logs de recursos do Azure.
ms.subservice: logs
ms.topic: reference
ms.date: 06/03/2020
ms.openlocfilehash: 463b1d9d9c3ed1d94728874ba814554deb4f97c6
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96920842"
---
# <a name="supported-categories-for-azure-resource-logs"></a>Categorias com suporte para logs de recursos do Azure

> [!NOTE]
> Os logs de recursos eram anteriormente conhecidos como logs de diagnóstico. O nome foi alterado em outubro de 2019, pois os tipos de logs coletados por Azure Monitor foram deslocados para incluir mais do que apenas o recurso do Azure.

[Azure monitor logs de recursos](./platform-logs-overview.md) são logs emitidos pelos serviços do Azure que descrevem a operação desses serviços ou recursos. Todos os logs de recursos disponíveis por meio de Azure Monitor compartilham um esquema comum de nível superior, com flexibilidade para cada serviço para emitir propriedades exclusivas para seus próprios eventos.

Uma combinação do tipo de recurso (disponível na propriedade `resourceId`) e da `category` identifica exclusivamente um esquema. Há um esquema comum para todos os logs de recursos com campos específicos de serviço, então adicionados para diferentes categorias de log. Para obter mais informações, consulte [esquema específico de serviço e comum para logs de recursos do Azure]()


## <a name="costs"></a>Custos

 Há custos associados ao envio e armazenamento de dados no Log Analytics e/ou no Hub de eventos. Os logs de recursos são um tipo de dados que você pode enviar para esses locais. Há um custo adicional [para exportar algumas categorias de logs de recursos](https://azure.microsoft.com/pricing/details/monitor/). Outras são livres de custos de exportação. As especificidades são listadas na tabela a seguir.

## <a name="supported-log-categories-per-resource-type"></a>Categorias de log com suporte por tipo de recurso

A seguir está uma lista dos tipos de logs disponíveis para cada tipo de recurso. 

Algumas categorias só podem ter suporte para tipos específicos de recursos. Consulte a documentação específica do recurso se você sentir que falta um recurso. Por exemplo, as categorias Microsoft. SQL/servidores/bancos de dados não estão disponíveis para todos os tipos de bancos de dados. Para obter mais informações, consulte [informações sobre o log de diagnóstico do banco de dados SQL](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md). 

Se ainda houver algo ausente, você poderá abrir um comentário do GitHub na parte inferior deste artigo.
## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

Custo: gratuito 

|Categoria |Nome de exibição da categoria|
|---|---|
|Mecanismo|Mecanismo|
|Serviço|Serviço|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

Custo: gratuito 

|Categoria |Nome de exibição da categoria|
|---|---|
|GatewayLogs|Logs relacionados ao Gateway ApiManagement|


## <a name="microsoftappplatformspring"></a>Microsoft.AppPlatform/Spring

Custo: gratuito 

|Categoria |Nome de exibição da categoria|
|---|---|
|ApplicationConsole|Console de aplicativo|
|SystemLogs|Logs do sistema|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

Custo: gratuito 

|Categoria |Nome de exibição da categoria|
|---|---|
|DscNodeStatus|Status do nó DSC|
|JobLogs|Logs de trabalho|
|JobStreams|Transmissões de trabalho|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

Custo: gratuito 

|Categoria |Nome de exibição da categoria|
|---|---|
|ServiceLog|Logs de serviço|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/workspaces

Custo: gratuito 

|Categoria |Nome de exibição da categoria|
|---|---|
|BaiClusterEvent|BaiClusterEvent|
|BaiClusterNodeEvent|BaiClusterNodeEvent|
|BaiJobEvent|BaiJobEvent|


## <a name="microsoftblockchainblockchainmembers"></a>Microsoft.Blockchain/blockchainMembers

Custo: gratuito 

|Categoria |Nome de exibição da categoria|
|---|---|
|BlockchainApplication|Aplicativo Blockchain|
|FabricOrderer|Solicitante de malha|
|FabricPeer|Ponto de malha|
|Proxy|Proxy|


## <a name="microsoftblockchaincordamembers"></a>Microsoft. Blockchain/cordaMembers

Custo: gratuito 

|Categoria |Nome de exibição da categoria|
|---|---|
|BlockchainApplication|Aplicativo Blockchain|


## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft. CDN/cdnwebapplicationfirewallpolicies

Custo: gratuito 

|Categoria |Nome de exibição da categoria|
|---|---|
|WebApplicationFirewallLogs|Logs de firewall de aplicação Web|


## <a name="microsoftcdnprofiles"></a>Microsoft.Cdn/profiles

Custo: gratuito 

|Categoria |Nome de exibição da categoria|
|---|---|
|AzureCdnAccessLog|Log de acesso da CDN do Azure|


## <a name="microsoftcdnprofilesendpoints"></a>Microsoft.Cdn/profiles/endpoints

Custo: gratuito 

|Categoria |Nome de exibição da categoria|
|---|---|
|CoreAnalytics|Obtém as métricas do ponto de extremidade, como largura de banda, saída etc.|


## <a name="microsoftclassicnetworknetworksecuritygroups"></a>Microsoft.ClassicNetwork/networksecuritygroups

Custo: gratuito 

|Categoria |Nome de exibição da categoria|
|---|---|
|Evento de fluxo de regra de grupo de segurança de rede|Evento de fluxo de regra de grupo de segurança de rede|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

Custo: gratuito 

|Categoria |Nome de exibição da categoria|
|---|---|
|Audit|Logs de Auditoria|
|RequestResponse|Logs de Solicitação e Resposta|
|Trace|Logs de rastreamento|


## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

Custo: gratuito 

|Categoria |Nome de exibição da categoria|
|---|---|
|ContainerRegistryLoginEvents|Eventos de logon|
|ContainerRegistryRepositoryEvents|Logs do RepositoryEvent|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

Custo: gratuito 

|Categoria |Nome de exibição da categoria|
|---|---|
|cluster-autoscaler|Dimensionador automático de cluster do Kubernetes|
|kube-apiserver|Servidor de API do Kubernetes|
|Kube-auditoria|Kubernetes auditoria|
|kube-controller-manager|Gerenciador do Controlador do Kubernetes|
|kube-scheduler|Agendador do Kubernetes|


## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft. CustomProviders/resourceproviders

Custo: gratuito 

|Categoria |Nome de exibição da categoria|
|---|---|
|AuditLogs|Logs de auditoria para chamadas MiniRP|


## <a name="microsoftdatabricksworkspaces"></a>Microsoft. databricks/espaços de trabalho

Custo: gratuito 

|Categoria |Nome de exibição da categoria|
|---|---|
|accounts|Contas do databricks|
|clusters|Clusters do databricks|
|dBFS|Sistema de arquivos do Databricks|
|instancePools|Pools de instância|
|jobs|Trabalhos do databricks|
|notebook|Databricks Notebook|
|segredos|Segredos do databricks|
|sqlpermissions|Sqlpermissões do databricks|
|ssh|SSH do databricks|
|workspace|Espaço de trabalho do databricks|


## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

Custo: gratuito 

|Categoria |Nome de exibição da categoria|
|---|---|
|ActivityRuns|Registro de execuções de atividade de pipeline|
|PipelineRuns|Registro de execuções de pipeline|
|TriggerRuns|Registro de execuções de gatilho|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

Custo: gratuito 

|Categoria |Nome de exibição da categoria|
|---|---|
|Audit|Logs de Auditoria|
|Requests|Logs de solicitação|


## <a name="microsoftdatashareaccounts"></a>Microsoft.DataShare/accounts

Custo: gratuito 

|Categoria |Nome de exibição da categoria|
|---|---|
|ReceivedShareSnapshots|Instantâneos de compartilhamento recebidos|
|SentShareSnapshots|Instantâneos de compartilhamento enviados|
|Compartilhamentos|Compartilhamentos|
|ShareSubscriptions|Compartilhar assinaturas|


## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

Custo: gratuito 

|Categoria |Nome de exibição da categoria|
|---|---|
|MySqlAuditLogs|Logs de auditoria do MariaDB|
|MySqlSlowLogs|Logs do servidor MariaDB|


## <a name="microsoftdbformysqlflexibleservers"></a>Microsoft.DBforMySQL/flexibleServers

Custo: gratuito 

|Categoria |Nome de exibição da categoria|
|---|---|
|MySqlAuditLogs|Logs de auditoria do MySQL|
|MySqlSlowLogs|Logs lentos do MySQL|


## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

Custo: gratuito 

|Categoria |Nome de exibição da categoria|
|---|---|
|MySqlAuditLogs|Logs de auditoria do MySQL|
|MySqlSlowLogs|Logs do MySQL Server|


## <a name="microsoftdbforpostgresqlflexibleservers"></a>Microsoft.DBforPostgreSQL/flexibleServers

Custo: gratuito 

|Categoria |Nome de exibição da categoria|
|---|---|
|PostgreSQLLogs|Logs do PostgreSQL Server|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

Custo: gratuito 

|Categoria |Nome de exibição da categoria|
|---|---|
|PostgreSQLLogs|Logs do PostgreSQL Server|
|QueryStoreRuntimeStatistics|As estatísticas de tempo de execução Repositório de Consultas PostgreSQL|
|QueryStoreWaitStatistics|Estatísticas de espera Repositório de Consultas PostgreSQL|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

Custo: gratuito 

|Categoria |Nome de exibição da categoria|
|---|---|
|PostgreSQLLogs|Logs do PostgreSQL Server|


## <a name="microsoftdesktopvirtualizationapplicationgroups"></a>Microsoft. DesktopVirtualization/applicationgroups

Custo: gratuito 

|Categoria |Nome de exibição da categoria|
|---|---|
|Ponto de verificação|Ponto de verificação|
|Erro|Erro|
|Gerenciamento|Gerenciamento|


## <a name="microsoftdesktopvirtualizationhostpools"></a>Microsoft. DesktopVirtualization/hostpools

Custo: gratuito 

|Categoria |Nome de exibição da categoria|
|---|---|
|Ponto de verificação|Ponto de verificação|
|Conexão|Conexão|
|Erro|Erro|
|HostRegistration|HostRegistration|
|Gerenciamento|Gerenciamento|


## <a name="microsoftdesktopvirtualizationworkspaces"></a>Microsoft. DesktopVirtualization/Workspaces

Custo: gratuito 

|Categoria |Nome de exibição da categoria|
|---|---|
|Ponto de verificação|Ponto de verificação|
|Erro|Erro|
|Feed|Feed|
|Gerenciamento|Gerenciamento|


## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

Custo: gratuito 

|Categoria |Nome de exibição da categoria|
|---|---|
|C2DCommands|Comandos C2C|
|C2CTwinOperations|Operações de Gêmeos C2D|
|Configurações|Configurações|
|conexões|conexões|
|D2CTwinOperations|D2CTwinOperations|
|DeviceIdentityOperations|Operações de Identidade do Dispositivo|
|DeviceStreams|Fluxos de dispositivo (visualização)|
|DeviceTelemetry|Telemetria de Dispositivo|
|DirectMethods|Métodos diretos|
|DistributedTracing|Rastreamento Distribuído (Versão Prévia)|
|FileUploadOperations|Operações de Upload de Arquivo|
|JobsOperations|Operações de Trabalhos|
|Rotas|Rotas|
|TwinQueries|Consultas de Gêmeos|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

Custo: gratuito 

|Categoria |Nome de exibição da categoria|
|---|---|
|DeviceOperations|Operações do Dispositivo|
|ServiceOperations|Operações de serviço|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

Custo: gratuito 

|Categoria |Nome de exibição da categoria|
|---|---|
|CassandraRequests|CassandraRequests|
|ControlPlaneRequests|ControlPlaneRequests|
|DataPlaneRequests|DataPlaneRequests|
|GremlinRequests|GremlinRequests|
|MongoRequests|MongoRequests|
|PartitionKeyRUConsumption|PartitionKeyRUConsumption|
|PartitionKeyStatistics|PartitionKeyStatistics|
|QueryRuntimeStatistics|QueryRuntimeStatistics|


## <a name="microsofteventgriddomains"></a>Microsoft.EventGrid/domains

Custo: gratuito 

|Categoria |Nome de exibição da categoria|
|---|---|
|DeliveryFailures|Logs de falha de entrega|
|PublishFailures|Publicar logs de falha|


## <a name="microsofteventgridsystemtopics"></a>Microsoft. EventGrid/systemTopics

Custo: gratuito 

|Categoria |Nome de exibição da categoria|
|---|---|
|DeliveryFailures|Logs de falha de entrega|


## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

Custo: gratuito 

|Categoria |Nome de exibição da categoria|
|---|---|
|DeliveryFailures|Logs de falha de entrega|
|PublishFailures|Publicar logs de falha|


## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

Custo: gratuito 

|Categoria |Nome de exibição da categoria|
|---|---|
|ArchiveLogs|Logs de arquivo|
|AutoScaleLogs|Logs de Escala Automática|
|CustomerManagedKeyUserLogs|Logs de chave gerenciado pelo cliente|
|EventHubVNetConnectionEvent|Logs de conexão de filtragem de VNet/IP|
|KafkaCoordinatorLogs|Logs do coordenador de Kafka|
|KafkaUserErrorLogs|Logs de erros do usuário Kafka|
|OperationalLogs|Logs operacionais|


## <a name="microsofthealthcareapisservices"></a>Microsoft.HealthcareApis/services

Custo: gratuito 

|Categoria |Nome de exibição da categoria|
|---|---|
|AuditLogs|Logs de auditoria|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

Custo: gratuito 

|Categoria |Nome de exibição da categoria|
|---|---|
|AutoscaleEvaluations|Avaliações de Dimensionamento Automático|
|AutoscaleScaleActions|Ações de Dimensionamento Automático|


## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

Custo: gratuito 

|Categoria |Nome de exibição da categoria|
|---|---|
|AppAvailabilityResults|Resultados da disponibilidade|
|AppBrowserTimings|Tempos do navegador|
|AppDependencies|Dependências|
|AppEvents|Eventos|
|AppExceptions|Exceções|
|AppMetrics|Métricas|
|AppPageViews|Exibições de página|
|AppPerformanceCounters|Contadores de desempenho|
|AppRequests|Requests|
|AppSystemEvents|Eventos do sistema|
|AppTraces|Rastreamentos|


## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

Custo: gratuito 

|Categoria |Nome de exibição da categoria|
|---|---|
|AuditEvent|Logs de Auditoria|


## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

Custo: gratuito 

|Categoria |Nome de exibição da categoria|
|---|---|
|Comando|Comando|
|FailedIngestion|Operações de ingestão com falha|
|IngestionBatching|Envio em lote de ingestão|
|Consulta|Consulta|
|SucceededIngestion|Operações de ingestão com êxito|
|TableDetails|Detalhes da tabela|
|TableUsageStatistics|Estatísticas de uso de tabela|


## <a name="microsoftlogicintegrationaccounts"></a>Microsoft.Logic/integrationAccounts

Custo: gratuito 

|Categoria |Nome de exibição da categoria|
|---|---|
|IntegrationAccountTrackingEvents|Acompanhar os eventos da Conta de Integração|


## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

Custo: gratuito 

|Categoria |Nome de exibição da categoria|
|---|---|
|WorkflowRuntime|Eventos de diagnóstico de runtime de fluxo de trabalho|


## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

Custo: gratuito 

|Categoria |Nome de exibição da categoria|
|---|---|
|AmlComputeClusterEvent|AmlComputeClusterEvent|
|AmlComputeClusterNodeEvent|AmlComputeClusterNodeEvent|
|AmlComputeCpuGpuUtilization|AmlComputeCpuGpuUtilization|
|AmlComputeJobEvent|AmlComputeJobEvent|
|AmlRunStatusChangedEvent|AmlRunStatusChangedEvent|


## <a name="microsoftmediamediaservices"></a>Microsoft.Media/mediaservices

Custo: gratuito 

|Categoria |Nome de exibição da categoria|
|---|---|
|KeyDeliveryRequests|Solicitações de entrega de chave|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

Custo: gratuito 

|Categoria |Nome de exibição da categoria|
|---|---|
|ApplicationGatewayAccessLog|Log de acesso do Gateway de Aplicativo|
|ApplicationGatewayFirewallLog|Log de firewall do Gateway de Aplicativo|
|ApplicationGatewayPerformanceLog|Log de desempenho do Gateway de Aplicativo|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

Custo: gratuito 

|Categoria |Nome de exibição da categoria|
|---|---|
|AzureFirewallApplicationRule|Regra de Aplicativo de Firewall do Azure|
|AzureFirewallNetworkRule|Regra de Rede de Firewall do Azure|


## <a name="microsoftnetworkbastionhosts"></a>Microsoft. Network/bastionHosts

Custo: gratuito 

|Categoria |Nome de exibição da categoria|
|---|---|
|BastionAuditLogs|Logs de auditoria de bastiões|


## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

Custo: gratuito 

|Categoria |Nome de exibição da categoria|
|---|---|
|PeeringRouteLog|Logs de Tabela de Rota de Emparelhamento|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

Custo: gratuito 

|Categoria |Nome de exibição da categoria|
|---|---|
|FrontdoorAccessLog|Log de acesso do Frontdoor|
|FrontdoorWebApplicationFirewallLog|Log de firewall do aplicativo Web Frontdoor|


## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

Custo: gratuito 

|Categoria |Nome de exibição da categoria|
|---|---|
|LoadBalancerAlertEvent|Eventos de alerta do Load Balancer|
|LoadBalancerProbeHealthStatus|Status de integridade da investigação do Load Balancer|


## <a name="microsoftnetworknetworksecuritygroups"></a>Microsoft.Network/networksecuritygroups

Custo: gratuito 

|Categoria |Nome de exibição da categoria|
|---|---|
|NetworkSecurityGroupEvent|Network Security Group Event|
|NetworkSecurityGroupFlowEvent|Evento de fluxo de regra de grupo de segurança de rede|
|NetworkSecurityGroupRuleCounter|Contador de regras de grupo de segurança de rede|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

Custo: gratuito 

|Categoria |Nome de exibição da categoria|
|---|---|
|DDoSMitigationFlowLogs|Logs de fluxo de decisões de mitigação de DDoS|
|DDoSMitigationReports|Relatórios de mitigações de DDoS|
|DDoSProtectionNotifications|Notificações de proteção contra DDoS|


## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

Custo: gratuito 

|Categoria |Nome de exibição da categoria|
|---|---|
|ProbeHealthStatusEvents|Evento de Resultados de Integridade de Investigação do Gerenciador de Tráfego|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

Custo: gratuito 

|Categoria |Nome de exibição da categoria|
|---|---|
|GatewayDiagnosticLog|Logs de Diagnóstico de Gateway|
|IKEDiagnosticLog|Logs de Diagnóstico IKE|
|P2SDiagnosticLog|Logs de Diagnóstico P2S|
|RouteDiagnosticLog|Logs de Diagnóstico de Rota|
|TunnelDiagnosticLog|Logs de Diagnóstico de Túnel|


## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft.Network/virtualNetworks

Custo: gratuito 

|Categoria |Nome de exibição da categoria|
|---|---|
|VMProtectionAlerts|Alertas de proteção da VM|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

Custo: gratuito 

|Categoria |Nome de exibição da categoria|
|---|---|
|Mecanismo|Mecanismo|


## <a name="microsoftrecoveryservicesvaults"></a>Microsoft.RecoveryServices/Vaults

Custo: gratuito 

|Categoria |Nome de exibição da categoria|
|---|---|
|AddonAzureBackupAlerts|Dados de alerta de backup do Azure addon|
|AddonAzureBackupJobs|Dados do trabalho de backup do Azure addon|
|AddonAzureBackupPolicy|Dados de política de backup do Azure addon|
|AddonAzureBackupProtectedInstance|Dados de instância protegida do backup do Azure addon|
|AddonAzureBackupStorage|Dados de armazenamento de backup do Azure addon|
|AzureBackupReport|Dados de relatórios de backup do Azure|
|AzureSiteRecoveryEvents|Eventos do Azure Site Recovery|
|AzureSiteRecoveryJobs|Trabalhos do Azure Site Recovery|
|AzureSiteRecoveryProtectedDiskDataChurn|Rotatividade de Dados de Disco Protegido do Azure Site Recovery|
|AzureSiteRecoveryRecoveryPoints|Pontos de Recuperação do Azure Site Recovery|
|AzureSiteRecoveryReplicatedItems|Itens replicados do Azure Site Recovery|
|AzureSiteRecoveryReplicationDataUploadRate|Taxa de Carregamento de Dados de Replicação do Azure Site Recovery|
|AzureSiteRecoveryReplicationStats|Estatísticas de Replicação do Azure Site Recovery|
|CoreAzureBackup|Principais dados de backup do Azure|


## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

Custo: gratuito 

|Categoria |Nome de exibição da categoria|
|---|---|
|HybridConnectionsEvent|Eventos de HybridConnections|


## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

Custo: gratuito 

|Categoria |Nome de exibição da categoria|
|---|---|
|OperationLogs|Logs de operação|


## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

Custo: gratuito 

|Categoria |Nome de exibição da categoria|
|---|---|
|OperationalLogs|Logs operacionais|


## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

Custo: gratuito 

|Categoria |Nome de exibição da categoria|
|---|---|
|AllLogs|Logs do serviço de Signaler do Azure.|


## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

Custo: gratuito 

|Categoria |Nome de exibição da categoria|
|---|---|
|DevOpsOperationsAudit|Logs de auditoria de operações do DevOps|
|ResourceUsageStats|Estatísticas de uso de recursos|
|SQLSecurityAuditEvents|Evento de Auditoria de Segurança do SQL|


## <a name="microsoftsqlmanagedinstancesdatabases"></a>Microsoft.Sql/managedInstances/databases

Custo: gratuito 

|Categoria |Nome de exibição da categoria|
|---|---|
|Errors|Errors|
|QueryStoreRuntimeStatistics|Estatísticas de Runtime do Repositório de Consultas|
|QueryStoreWaitStatistics|Estatísticas de Espera do Repositório de Consultas|
|SQLInsights|Insights do SQL|


## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

Custo: gratuito 

|Categoria |Nome de exibição da categoria|
|---|---|
|AutomaticTuning|Ajuste automático|
|Blocos|Blocos|
|DatabaseWaitStatistics|Estatísticas de Espera do Banco de Dados|
|Deadlocks|Deadlocks|
|DevOpsOperationsAudit|Logs de auditoria de operações do DevOps|
|DmsWorkers|Trabalhos DMS|
|Errors|Errors|
|ExecRequests|Solicitações de Exec|
|QueryStoreRuntimeStatistics|Estatísticas de Runtime do Repositório de Consultas|
|QueryStoreWaitStatistics|Estatísticas de Espera do Repositório de Consultas|
|RequestSteps|Etapas de solicitação|
|SQLInsights|Insights do SQL|
|SqlRequests|Solicitações de Sql|
|SQLSecurityAuditEvents|Evento de Auditoria de Segurança do SQL|
|Tempos limite|Tempos limite|
|Esperas|Esperas|


## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

Custo: pago conforme descrito na seção de logs da plataforma da [página de preços Azure monitor.](https://azure.microsoft.com/pricing/details/monitor/) 

|Categoria |Nome de exibição da categoria|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

Custo: pago conforme descrito na seção de logs da plataforma da [página de preços Azure monitor.](https://azure.microsoft.com/pricing/details/monitor/) 

|Categoria |Nome de exibição da categoria|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

Custo: pago conforme descrito na seção de logs da plataforma da [página de preços Azure monitor.](https://azure.microsoft.com/pricing/details/monitor/) 
 
|Categoria |Nome de exibição da categoria|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

Custo: pago conforme descrito na seção de logs da plataforma da [página de preços Azure monitor.](https://azure.microsoft.com/pricing/details/monitor/) 
 
|Categoria |Nome de exibição da categoria|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

Custo: gratuito 

|Categoria |Nome de exibição da categoria|
|---|---|
|Criação|Criação|
|Execução|Execução|


## <a name="microsoftsynapseworkspaces"></a>Microsoft.Synapse/workspaces

Custo: gratuito 

|Categoria |Nome de exibição da categoria|
|---|---|
|BuiltinSqlReqsEnded|Solicitações de pool de SQL internas encerradas|
|GatewayApiRequests|Solicitações de API de gateway Synapse|
|SQLSecurityAuditEvents|Evento de Auditoria de Segurança do SQL|
|SynapseRbacOperations|Operações de RBAC Synapse|


## <a name="microsoftsynapseworkspacesbigdatapools"></a>Microsoft. Synapse/Workspaces/bigDataPools

Custo: gratuito 

|Categoria |Nome de exibição da categoria|
|---|---|
|BigDataPoolAppsEnded|Aplicativos de pool de Big data encerrados|


## <a name="microsoftsynapseworkspacessqlpools"></a>Microsoft. Synapse/espaços de trabalho/sqlpools

Custo: gratuito 

|Categoria |Nome de exibição da categoria|
|---|---|
|DmsWorkers|Trabalhos DMS|
|ExecRequests|Solicitações de Exec|
|RequestSteps|Etapas de solicitação|
|SqlRequests|Solicitações de Sql|
|SQLSecurityAuditEvents|Evento de auditoria de segurança do SQL|
|Esperas|Esperas|


## <a name="microsoftwebhostingenvironments"></a>microsoft.web/hostingenvironments

Custo: gratuito 

|Categoria |Nome de exibição da categoria|
|---|---|
|AppServiceEnvironmentPlatformLogs|Ambiente do Serviço de Aplicativondo logs da plataforma|


## <a name="microsoftwebsites"></a>microsoft.web/sites

Custo: gratuito 


|Categoria |Nome de exibição da categoria|
|---|---|
|AppServiceAppLogs|Logs de aplicativo do serviço de aplicativo|
|AppServiceAuditLogs|Acessar logs de auditoria|
|AppServiceConsoleLogs|Logs do console do serviço de aplicativo|
|AppServiceFileAuditLogs|Logs de auditoria de alteração de conteúdo do site|
|AppServiceHTTPLogs|Logs de HTTP|
|FunctionAppLogs|Logs do aplicativo de funções|


## <a name="microsoftwebsitesslots"></a>microsoft.web/sites/slots

Custo: gratuito 


|Categoria |Nome de exibição da categoria|
|---|---|
|AppServiceAppLogs|Logs de aplicativo do serviço de aplicativo|
|AppServiceAuditLogs|Acessar logs de auditoria|
|AppServiceConsoleLogs|Logs do console do serviço de aplicativo|
|AppServiceFileAuditLogs|Logs de auditoria de alteração de conteúdo do site|
|AppServiceHTTPLogs|Logs de HTTP|
|FunctionAppLogs|Logs do aplicativo de funções|


## <a name="next-steps"></a>Próximas etapas

* [Saiba mais sobre os logs de recursos](./platform-logs-overview.md)
* [Transmitir logs de recurso de recurso para **hubs de eventos**](./resource-logs.md#send-to-azure-event-hubs)
* [Alterar as configurações de diagnóstico do log de recursos usando a API REST do Azure Monitor](/rest/api/monitor/diagnosticsettings)
* [Analisar logs do Armazenamento do Azure com o Log Analytics](./resource-logs.md#send-to-log-analytics-workspace)

