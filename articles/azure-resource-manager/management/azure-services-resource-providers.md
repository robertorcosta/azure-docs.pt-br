---
title: Provedores de recursos pelos serviços do Azure
description: Lista todos os namespaces do provedor de recursos para Azure Resource Manager e mostra o serviço do Azure para esse namespace.
ms.topic: conceptual
ms.date: 11/11/2019
ms.openlocfilehash: d16c5fe148f8b6c907c897963e72cb84bc9ae2d5
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76984363"
---
# <a name="resource-providers-for-azure-services"></a>Provedores de recursos para serviços do Azure

Este artigo mostra como os namespaces do provedor de recursos são mapeados para os serviços do Azure.

## <a name="match-resource-provider-to-service"></a>Corresponder provedor de recursos ao serviço

| Namespace do provedor de recursos | Serviço do Azure |
| --------------------------- | ------------- |
| Microsoft.AAD | [Azure Active Directory Domain Services](../../active-directory-domain-services/index.yml) |
| Microsoft.Addons | núcleo |
| Microsoft.ADHybridHealthService | [Azure Active Directory](/azure/active-directory/) |
| Microsoft.Advisor | [Assistente do Azure](../../advisor/index.yml) |
| Microsoft.AlertsManagement | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.AnalysisServices | [Azure Analysis Services](/azure/analysis-services/) |
| Microsoft.ApiManagement | [Gerenciamento de API](../../api-management/index.yml) |
| Microsoft.AppConfiguration | núcleo |
| Microsoft.Attestation | Serviço de atestado do Azure |
| Microsoft.Authorization | [Azure Resource Manager](../index.yml) |
| Microsoft.Automation | [Automação](../../automation/index.yml) |
| Microsoft.AzureActiveDirectory | [Azure Active Directory B2C](../../active-directory-b2c/index.yml) |
| Microsoft.AzureStack | núcleo |
| Microsoft.Batch | [Batch](../../batch/index.yml) |
| Microsoft.Billing | [Gerenciamento de custos e cobrança](/azure/billing/) |
| Microsoft.BingMaps | [Bing Mapas](https://docs.microsoft.com/BingMaps/#pivot=main&panel=BingMapsAPI) |
| Microsoft.Blockchain | [Serviço Blockchain do Azure](/azure/blockchain/workbench/) |
| Microsoft.Blueprint | [Plantas do Azure](/azure/governance/blueprints/) |
| Microsoft.BotService | [Serviço de bot do Azure](/azure/bot-service/) |
| Microsoft.Cache | [Cache Redis do Azure](/azure/azure-cache-for-redis/) |
| Microsoft.Capacity | núcleo |
| Microsoft.Cdn | [Rede de distribuição de conteúdo](../../cdn/index.yml) |
| Microsoft.CertificateRegistration | [Certificados do serviço de aplicativo](../../app-service/configure-ssl-certificate.md#import-an-app-service-certificate) |
| Microsoft. ChangeAnalysis | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.ClassicCompute | Máquina virtual do modelo de implantação clássico |
| Microsoft.ClassicInfrastructureMigrate | Migração do modelo de implantação clássico |
| Microsoft.ClassicNetwork | Rede virtual do modelo de implantação clássico |
| Microsoft.ClassicStorage | Armazenamento de modelo de implantação clássico |
| Microsoft.ClassicSubscription | Modelo de implantação clássica |
| Microsoft.CognitiveServices | [Serviços cognitivas](/azure/cognitive-services/) |
| Microsoft.Commerce | núcleo |
| Microsoft.Compute | [Máquinas virtuais](/azure/virtual-machines/)<br />[Conjuntos de Escala de Máquina Virtual](/azure/virtual-machine-scale-sets/) |
| Microsoft.Consumption | [Gerenciamento de custos](/azure/cost-management/) |
| Microsoft.ContainerInstance | [Instâncias de contêiner](/azure/container-instances/) |
| Microsoft.ContainerRegistry | [Registro de contêiner](/azure/container-registry/) |
| Microsoft.ContainerService | [AKS (Serviço de Kubernetes do Azure)](/azure/aks/) |
| Microsoft.CostManagement | [Gerenciamento de custos](/azure/cost-management/) |
| Microsoft. CostManagementExports | [Gerenciamento de custos](/azure/cost-management/) |
| Microsoft. CustomerLockbox | Sistema de Proteção de Dados do Cliente para Microsoft Azure |
| Microsoft.CustomProviders | [Provedores personalizados do Azure](../custom-providers/overview.md) |
| Microsoft.DataBox | [Azure Data Box](/azure/databox-family/) |
| Microsoft.DataBoxEdge | [Borda da caixa de dados do Azure](../../databox-online/data-box-edge-overview.md) |
| Microsoft.Databricks | [Azure Databricks](/azure/azure-databricks/) |
| Microsoft.DataCatalog | [Catálogo de dados](/azure/data-catalog/) |
| Microsoft.DataFactory | [Data Factory](/azure/data-factory/) |
| Microsoft.DataLakeAnalytics | [Data Lake Analytics](/azure/data-lake-analytics/) |
| Microsoft.DataLakeStore | [Azure Data Lake Store](../../storage/blobs/data-lake-storage-introduction.md) |
| Microsoft.DataMigration | [Serviço de migração de banco de dados do Azure](/azure/dms/) |
| Microsoft. DataShare | [Compartilhamento de dados do Azure](/azure/data-share/) |
| Microsoft.DBforMariaDB | [Banco de dados do Azure para MariaDB](/azure/mariadb/) |
| Microsoft.DBforMySQL | [Banco de dados do Azure para MySQL](/azure/mysql/) |
| Microsoft.DBforPostgreSQL | [Banco de dados do Azure para PostgreSQL](/azure/postgresql/) |
| Microsoft. DesktopVirtualization | [Área de trabalho virtual do Windows](/azure/virtual-desktop/) |
| Microsoft.DeploymentManager | [Deployment Manager do Azure](../templates/deployment-manager-overview.md) |
| Microsoft.Devices | [Hub IoT](/azure/iot-hub/)<br />[Serviço de Provisionamento de Dispositivos no Hub IoT](/azure/iot-dps/) |
| Microsoft. DevOps | [Azure DevOps](/azure/devops/) |
| Microsoft.DevSpaces | [Azure Dev Spaces](/azure/dev-spaces/) |
| Microsoft.DevTestLab | [Azure Lab Services](../../lab-services/index.yml) |
| Microsoft.DocumentDB | [Azure Cosmos DB](../../cosmos-db/index.yml) |
| Microsoft.DomainRegistration | [Serviço de aplicativo](/azure/app-service/) |
| Microsoft.EnterpriseKnowledgeGraph | Grafo de conhecimento corporativo |
| Microsoft.EventGrid | [Grade de eventos](/azure/event-grid/) |
| Microsoft.EventHub | [Hubs de Evento](../../event-hubs/index.yml) |
| Microsoft.Features | [Azure Resource Manager](../index.yml) |
| Microsoft.Genomics | [Microsoft Genomics](/azure/genomics/) |
| Microsoft.GuestConfiguration | [Azure Policy](../../governance/policy/index.yml) |
| Microsoft.HanaOnAzure | [SAP HANA no Azure](../../virtual-machines/workloads/sap/hana-overview-architecture.md) |
| Microsoft.HardwareSecurityModules | [HSM Dedicado do Azure](../../dedicated-hsm/index.yml) |
| Microsoft.HDInsight | [HDInsight](../../hdinsight/index.yml) |
| Microsoft.HealthcareApis | [API do Azure para FHIR](../../healthcare-apis/index.yml) |
| Microsoft.HybridCompute | [Arco do Azure](../../azure-arc/index.yml) |
| Microsoft.HybridData | [StorSimple](/azure/storsimple/) |
| Microsoft.ImportExport | [Importação/exportação do Azure](../../storage/common/storage-import-export-service.md) |
| microsoft.insights | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.IoTCentral | [IoT Central](/azure/iot-central/) |
| Microsoft.IoTSpaces | [Gêmeos digital do Azure](../../digital-twins/index.yml) |
| Microsoft.KeyVault | [Key Vault](../../key-vault/index.yml) |
| Microsoft.Kusto | [Data Explorer do Azure](../../data-explorer/index.yml) |
| Microsoft.LabServices | [Azure Lab Services](../../lab-services/index.yml) |
| Microsoft.Logic | [Aplicativos Lógicos](../../logic-apps/index.yml) |
| Microsoft.MachineLearning | [Machine Learning Studio](../../machine-learning/studio/index.yml) |
| Microsoft.MachineLearningServices | [Serviço de Machine Learning](../../machine-learning/index.yml) |
| Microsoft.ManagedIdentity | [Identidades gerenciadas para os recursos do Azure](../../active-directory/managed-identities-azure-resources/index.yml) |
| Microsoft. Managedservices | [Azure Lighthouse](/azure/lighthouse/) |
| Microsoft.Management | [Grupos de Gerenciamento](/azure/governance/management-groups/) |
| Microsoft.Maps | [Mapas do Azure](../../azure-maps/index.yml) |
| Microsoft.Marketplace | núcleo |
| Microsoft.MarketplaceApps | núcleo |
| Microsoft.MarketplaceOrdering | núcleo |
| Microsoft.Media | [Serviços de Mídia](../../media-services/index.yml) |
| Microsoft.Migrate | [Migrações para Azure](../../migrate/migrate-overview.md) |
| Microsoft.MixedReality | [Âncoras Espaciais do Azure](/azure/spatial-anchors/) |
| Microsoft.NetApp | [Azure NetApp Files](../../azure-netapp-files/index.yml) |
| Microsoft.Network | [Rede Virtual](../../virtual-network/index.yml)<br />[Balanceador de Carga](../../load-balancer/index.yml)<br />[Gateway de Aplicativo](../../application-gateway/index.yml)<br />[Azure DNS](../../dns/index.yml)<br />[ExpressRoute](../../expressroute/index.yml)<br />[Gateway de VPN](../../vpn-gateway/index.yml)<br />[Gerenciador de Tráfego](../../traffic-manager/index.yml)<br />[Observador de Rede](../../network-watcher/index.yml)<br />[Firewall do Azure](../../firewall/index.yml)<br />[Azure Front Door Service](../../frontdoor/index.yml)<br />[Bastiões do Azure](/azure/bastion/) |
| Microsoft.NotificationHubs | [Hubs de Notificação](../../notification-hubs/index.yml) |
| Microsoft.OffAzure | [Migrações para Azure](../../migrate/migrate-overview.md) |
| Microsoft.OperationalInsights | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.OperationsManagement | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.Peering | Serviço de emparelhamento Microsoft Azure |
| Microsoft.PolicyInsights | [Azure Policy](../../governance/policy/index.yml) |
| Microsoft.Portal | [Azure portal](/azure/azure-portal/) |
| Microsoft.PowerBI | [Power BI](/power-bi/power-bi-overview) |
| Microsoft.PowerBIDedicated | [Power BI Embedded](/azure/power-bi-embedded/) |
| Microsoft.RecoveryServices | [Recuperação de Site](../../site-recovery/index.yml) |
| Microsoft.Relay | [Retransmissão do Azure](../../service-bus-relay/relay-what-is-it.md) |
| Microsoft.ResourceGraph | [Grafo de recursos do Azure](/azure/governance/resource-graph/) |
| Microsoft.ResourceHealth | núcleo |
| Microsoft.Resources | [Azure Resource Manager](../index.yml) |
| Microsoft.SaaS | núcleo |
| Microsoft.Scheduler | [Agendador](/azure/scheduler/) |
| Microsoft.Search | [Azure Search](../../search/index.yml) |
| Microsoft.Security | [Central de segurança](../../security-center/index.yml) |
| Microsoft. SecurityInsights | [Sentinela do Azure](/azure/sentinel/) |
| Microsoft. SerialConsole | [Console serial do Azure](../../virtual-machines/troubleshooting/serial-console-windows.md) |
| Microsoft.ServiceBus | [Barramento de Serviço](/azure/service-bus/) |
| Microsoft.ServiceFabric | [Service Fabric](../../service-fabric/index.yml) |
| Microsoft.ServiceFabricMesh | [Malha do Service Fabric](../../service-fabric-mesh/index.yml) |
| Microsoft.SignalRService | [Serviço Azure SignalR](../../azure-signalr/index.yml) |
| Microsoft.SiteRecovery | [Recuperação de Site](../../site-recovery/index.yml) |
| Microsoft.Solutions | [Aplicativos gerenciados do Azure](../managed-applications/index.yml) |
| Microsoft.Sql | [Banco de Dados SQL do Azure](../../sql-database/index.yml)<br />[SQL Data Warehouse](/azure/sql-data-warehouse/) |
| Microsoft.SqlVirtualMachine | [SQL Server nas Máquinas Virtuais do Azure](../../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md) |
| Microsoft.Storage | [Storage](../../storage/index.yml) |
| Microsoft.StorageCache | [Cache do HPC do Azure](/azure/hpc-cache/) |
| Microsoft.StorageSync | [Storage](../../storage/index.yml) |
| Microsoft.StorSimple | [StorSimple](/azure/storsimple/) |
| Microsoft.StreamAnalytics | [Stream Analytics](../../stream-analytics/index.yml) |
| Microsoft.Subscription | núcleo |
| microsoft.support | núcleo |
| Microsoft.TimeSeriesInsights | [Time Series Insights](../../time-series-insights/index.yml) |
| Microsoft.VirtualMachineImages | [Construtor de imagens do Azure](../../virtual-machines/linux/image-builder-overview.md) |
| microsoft.visualstudio | [DevOps do Azure](/azure/devops/?view=azure-devops) |
| Microsoft.VMwareCloudSimple | [Solução do Azure VMware por CloudSimple](/azure/vmware-cloudsimple/) |
| Microsoft.Web | [Serviço de Aplicativo](../../app-service/index.yml)<br />[Funções](../../azure-functions/index.yml) |
| Microsoft.WindowsIoT | [Serviços do Windows 10 IoT Core](https://docs.microsoft.com/windows-hardware/manufacture/iot/iotcoreservicesoverview) |
| Microsoft.WorkloadMonitor | [Azure Monitor](../../azure-monitor/index.yml) |

## <a name="next-steps"></a>Próximos passos

Para obter mais informações sobre provedores de recursos, consulte [tipos e provedores de recursos do Azure](resource-providers-and-types.md)
