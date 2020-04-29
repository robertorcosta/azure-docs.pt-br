---
title: Provedores de recursos pelos serviços do Azure
description: Lista todos os namespaces do provedor de recursos para Azure Resource Manager e mostra o serviço do Azure para esse namespace.
ms.topic: conceptual
ms.date: 03/17/2020
ms.openlocfilehash: 55fbe4ae383e5275d185e2a03224e77660a01ef5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81382502"
---
# <a name="resource-providers-for-azure-services"></a>Provedores de recursos para serviços do Azure

Este artigo mostra como os namespaces do provedor de recursos são mapeados para os serviços do Azure.

## <a name="match-resource-provider-to-service"></a>Corresponder provedor de recursos ao serviço

| Namespace do provedor de recursos | Serviço do Azure |
| --------------------------- | ------------- |
| Microsoft.AAD | [Azure Active Directory Domain Services](../../active-directory-domain-services/index.yml) |
| Microsoft.Addons | core |
| Microsoft.ADHybridHealthService | [Azure Active Directory](/azure/active-directory/) |
| Microsoft.Advisor | [Assistente do Azure](../../advisor/index.yml) |
| Microsoft.AlertsManagement | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.AnalysisServices | [Azure Analysis Services](/azure/analysis-services/) |
| Microsoft.ApiManagement | [Gerenciamento de API](../../api-management/index.yml) |
| Microsoft. AppConfiguration | core |
| Microsoft. AppPlatform | [Azure Spring Cloud](../../spring-cloud/spring-cloud-overview.md) |
| Microsoft.Attestation | Serviço de atestado do Azure |
| Microsoft.Authorization | [Azure Resource Manager](../index.yml) |
| Microsoft.Automation | [Automação](../../automation/index.yml) |
| Microsoft.AzureActiveDirectory | [Azure Active Directory B2C](../../active-directory-b2c/index.yml) |
| Microsoft. AzureData | Registro de SQL Server |
| Microsoft.AzureStack | core |
| Microsoft.Batch | [Lote](../../batch/index.yml) |
| Microsoft.Billing | [Gerenciamento de Custos e Cobrança](/azure/billing/) |
| Microsoft.BingMaps | [Bing Mapas](https://docs.microsoft.com/BingMaps/#pivot=main&panel=BingMapsAPI) |
| Microsoft.Blockchain | [Serviço do Azure Blockchain](/azure/blockchain/workbench/) |
| Microsoft.Blueprint | [Azure Blueprints](/azure/governance/blueprints/) |
| Microsoft.BotService | [Serviço de Bot do Azure](/azure/bot-service/) |
| Microsoft.Cache | [Cache Redis do Azure](/azure/azure-cache-for-redis/) |
| Microsoft.Capacity | core |
| Microsoft.Cdn | [Rede de Distribuição de Conteúdo](../../cdn/index.yml) |
| Microsoft.CertificateRegistration | [Certificados do serviço de aplicativo](../../app-service/configure-ssl-certificate.md#import-an-app-service-certificate) |
| Microsoft. ChangeAnalysis | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.ClassicCompute | Máquina virtual do modelo de implantação clássico |
| Microsoft.ClassicInfrastructureMigrate | Migração do modelo de implantação clássico |
| Microsoft.ClassicNetwork | Rede virtual do modelo de implantação clássico |
| Microsoft.ClassicStorage | Armazenamento de modelo de implantação clássico |
| Microsoft. ClassicSubscription | Modelo de implantação clássica |
| Microsoft.CognitiveServices | [Serviços cognitivas](/azure/cognitive-services/) |
| Microsoft.Commerce | core |
| Microsoft.Compute | [Máquinas Virtuais](/azure/virtual-machines/)<br />[Conjuntos de Dimensionamento de Máquinas Virtuais](/azure/virtual-machine-scale-sets/) |
| Microsoft.Consumption | [Gerenciamento de custos](/azure/cost-management/) |
| Microsoft.ContainerInstance | [Instâncias de Contêiner](/azure/container-instances/) |
| Microsoft.ContainerRegistry | [Registro de Contêiner](/azure/container-registry/) |
| Microsoft.ContainerService | [AKS (Serviço de Kubernetes do Azure)](/azure/aks/) |
| Microsoft.CostManagement | [Gerenciamento de custos](/azure/cost-management/) |
| Microsoft. CostManagementExports | [Gerenciamento de custos](/azure/cost-management/) |
| Microsoft. CustomerLockbox | Sistema de Proteção de Dados do Cliente para Microsoft Azure |
| Microsoft. CustomProviders | [Provedores Personalizados do Azure](../custom-providers/overview.md) |
| Microsoft.DataBox | [Azure Data Box](/azure/databox-family/) |
| Microsoft.DataBoxEdge | [Azure Stack Edge](../../databox-online/data-box-edge-overview.md) |
| Microsoft.Databricks | [Azure Databricks](/azure/azure-databricks/) |
| Microsoft.DataCatalog | [Catálogo de Dados](/azure/data-catalog/) |
| Microsoft.DataFactory | [Data Factory](/azure/data-factory/) |
| Microsoft.DataLakeAnalytics | [Data Lake Analytics](/azure/data-lake-analytics/) |
| Microsoft.DataLakeStore | [Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-introduction.md) |
| Microsoft.DataMigration | [Serviço de Migração de Banco de Dados do Azure](/azure/dms/) |
| Microsoft. DataShare | [Azure Data Share](/azure/data-share/) |
| Microsoft.DBforMariaDB | [Banco de Dados do Azure para MariaDB](/azure/mariadb/) |
| Microsoft.DBforMySQL | [Banco de Dados do Azure para MySQL](/azure/mysql/) |
| Microsoft.DBforPostgreSQL | [Banco de Dados do Azure para PostgreSQL](/azure/postgresql/) |
| Microsoft. DesktopVirtualization | [Área de Trabalho Virtual do Windows](/azure/virtual-desktop/) |
| Microsoft.DeploymentManager | [Deployment Manager do Azure](../templates/deployment-manager-overview.md) |
| Microsoft.Devices | [Hub IoT do Azure](/azure/iot-hub/)<br />[Serviço de Provisionamento de Dispositivos no Hub IoT do Azure](/azure/iot-dps/) |
| Microsoft. DevOps | [Azure DevOps](/azure/devops/) |
| Microsoft.DevSpaces | [Espaços de Desenvolvimento do Azure](/azure/dev-spaces/) |
| Microsoft.DevTestLab | [Azure Lab Services](../../lab-services/index.yml) |
| Microsoft. DigitalTwins | [Gêmeos Digitais do Azure](../../digital-twins/about-digital-twins.md) |
| Microsoft.DocumentDB | [Azure Cosmos DB](../../cosmos-db/index.yml) |
| Microsoft.DomainRegistration | [Serviço de Aplicativo](/azure/app-service/) |
| Microsoft. EnterpriseKnowledgeGraph | Grafo de conhecimento corporativo |
| Microsoft.EventGrid | [Grade de Eventos](/azure/event-grid/) |
| Microsoft.EventHub | [Hubs de Evento](../../event-hubs/index.yml) |
| Microsoft.Features | [Azure Resource Manager](../index.yml) |
| Microsoft.GuestConfiguration | [Azure Policy](../../governance/policy/index.yml) |
| Microsoft.HanaOnAzure | [SAP HANA em Instâncias Grandes do Azure](../../virtual-machines/workloads/sap/hana-overview-architecture.md) |
| Microsoft.HardwareSecurityModules | [HSM Dedicado do Azure](../../dedicated-hsm/index.yml) |
| Microsoft.HDInsight | [HDInsight](../../hdinsight/index.yml) |
| Microsoft. HealthcareApis | [API do Azure para FHIR](../../healthcare-apis/index.yml) |
| Microsoft.HybridCompute | [Azure Arc](../../azure-arc/index.yml) |
| Microsoft.HybridData | [StorSimple](/azure/storsimple/) |
| Microsoft.ImportExport | [Importação/Exportação do Azure](../../storage/common/storage-import-export-service.md) |
| microsoft.insights | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.IoTCentral | [Azure IoT Central](/azure/iot-central/) |
| Microsoft.IoTSpaces | [Gêmeos Digitais do Azure](../../digital-twins/index.yml) |
| Microsoft.KeyVault | [Cofre da Chave](../../key-vault/index.yml) |
| Microsoft. kubernetes | [AKS (Serviço de Kubernetes do Azure)](/azure/aks/) |
| Microsoft.Kusto | [Azure Data Explorer](/azure/data-explorer/) |
| Microsoft.LabServices | [Azure Lab Services](../../lab-services/index.yml) |
| Microsoft.Logic | [Aplicativos Lógicos](../../logic-apps/index.yml) |
| Microsoft.MachineLearning | [Machine Learning Studio](../../machine-learning/studio/index.yml) |
| Microsoft.MachineLearningServices | [Azure Machine Learning](../../machine-learning/index.yml) |
| Microsoft. Maintenance | [Manutenção do Azure](../../virtual-machines/maintenance-control-cli.md) |
| Microsoft.ManagedIdentity | [Identidades gerenciadas dos recursos do Azure](../../active-directory/managed-identities-azure-resources/index.yml) |
| Microsoft. Managedservices | [Azure Lighthouse](/azure/lighthouse/) |
| Microsoft.Management | [Grupos de Gerenciamento](/azure/governance/management-groups/) |
| Microsoft.Maps | [Mapas do Azure](../../azure-maps/index.yml) |
| Microsoft.Marketplace | core |
| Microsoft.MarketplaceApps | core |
| Microsoft.MarketplaceOrdering | core |
| Microsoft.Media | [Serviços de mídia](../../media-services/index.yml) |
| Microsoft.Migrate | [Migrações para Azure](../../migrate/migrate-overview.md) |
| Microsoft. MixedReality | [Âncoras Espaciais do Azure](/azure/spatial-anchors/) |
| Microsoft.NetApp | [Azure NetApp Files](../../azure-netapp-files/index.yml) |
| Microsoft.Network | [Gateway de Aplicativo](../../application-gateway/index.yml)<br />[Azure Bastion](/azure/bastion/)<br />[Proteção contra DDoS do Azure](../../virtual-network/ddos-protection-overview.md)<br />[DNS do Azure](../../dns/index.yml)<br />[Azure ExpressRoute](../../expressroute/index.yml)<br />[Firewall do Azure](../../firewall/index.yml)<br />[Azure Front Door Service](../../frontdoor/index.yml)<br />[Link Privado do Azure](../../private-link/index.yml)<br />[Load Balancer](../../load-balancer/index.yml)<br />[Observador de Rede](../../network-watcher/index.yml)<br />[Gerenciador de Tráfego](../../traffic-manager/index.yml)<br />[Rede Virtual](../../virtual-network/index.yml)<br />[WAN Virtual](../../virtual-wan/index.yml)<br />[Gateway de VPN](../../vpn-gateway/index.yml)<br /> |
| Microsoft.NotificationHubs | [Hubs de Notificação](../../notification-hubs/index.yml) |
| Microsoft.OffAzure | [Migrações para Azure](../../migrate/migrate-overview.md) |
| Microsoft.OperationalInsights | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.OperationsManagement | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft. emparelhamento | [Serviço de emparelhamento do Azure](../../peering-service/index.yml) |
| Microsoft.PolicyInsights | [Azure Policy](../../governance/policy/index.yml) |
| Microsoft.Portal | [Azure portal](/azure/azure-portal/) |
| Microsoft.PowerBI | [Power BI](/power-bi/power-bi-overview) |
| Microsoft.PowerBIDedicated | [Power BI Embedded](/azure/power-bi-embedded/) |
| Microsoft.RecoveryServices | [Azure Site Recovery](../../site-recovery/index.yml) |
| Microsoft. RedHatOpenShift | [Red Hat OpenShift no Azure](../../virtual-machines/linux/openshift-get-started.md) |
| Microsoft.Relay | [Retransmissão do Azure](../../service-bus-relay/relay-what-is-it.md) |
| Microsoft.ResourceGraph | [Gráfico de Recursos do Azure](/azure/governance/resource-graph/) |
| Microsoft.ResourceHealth | [Integridade do Serviço do Azure](../../service-health/index.yml)|
| Microsoft.Resources | [Azure Resource Manager](../index.yml) |
| Microsoft.SaaS | core |
| Microsoft.Scheduler | [Agendador](/azure/scheduler/) |
| Microsoft.Search | [Pesquisa Cognitiva do Azure](../../search/index.yml) |
| Microsoft.Security | [Central de Segurança](../../security-center/index.yml) |
| Microsoft. SecurityInsights | [Azure Sentinel](/azure/sentinel/) |
| Microsoft. SerialConsole | [Console serial do Azure para Windows](../../virtual-machines/troubleshooting/serial-console-windows.md) |
| Microsoft.ServiceBus | [Barramento de Serviço](/azure/service-bus/) |
| Microsoft.ServiceFabric | [Service Fabric](../../service-fabric/index.yml) |
| Microsoft.ServiceFabricMesh | [Malha do Service Fabric](../../service-fabric-mesh/index.yml) |
| Microsoft.SignalRService | [Serviço Azure SignalR](../../azure-signalr/index.yml) |
| Microsoft. SoftwarePlan | Licença |
| Microsoft.Solutions | [Aplicativos gerenciados do Azure](../managed-applications/index.yml) |
| Microsoft.Sql | [Banco de Dados SQL do Azure](../../sql-database/index.yml)<br />[Azure Synapse Analytics](/azure/sql-data-warehouse/) |
| Microsoft.SqlVirtualMachine | [SQL Server nas Máquinas Virtuais do Azure](../../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md) |
| Microsoft.Storage | [Armazenamento](../../storage/index.yml) |
| Microsoft. StorageCache | [Azure HPC Cache](/azure/hpc-cache/) |
| Microsoft.StorageSync | [Armazenamento](../../storage/index.yml) |
| Microsoft.StorSimple | [StorSimple](/azure/storsimple/) |
| Microsoft.StreamAnalytics | [Stream Analytics do Azure](../../stream-analytics/index.yml) |
| Microsoft.Subscription | core |
| microsoft.support | core |
| Microsoft. Synapse | [Azure Synapse Analytics](/azure/sql-data-warehouse/) |
| Microsoft.TimeSeriesInsights | [Azure Time Series Insights](../../time-series-insights/index.yml) |
| Microsoft.VirtualMachineImages | [Construtor de Imagens do Azure](../../virtual-machines/linux/image-builder-overview.md) |
| microsoft.visualstudio | [Azure DevOps](/azure/devops/?view=azure-devops) |
| Microsoft. VMwareCloudSimple | [Solução VMware no Azure pela CloudSimple](/azure/vmware-cloudsimple/) |
| Microsoft.Web | [Serviço de Aplicativo](../../app-service/index.yml)<br />[Azure Functions](../../azure-functions/index.yml) |
| Microsoft.WindowsIoT | [Serviços do Windows 10 IoT Core](https://docs.microsoft.com/windows-hardware/manufacture/iot/iotcoreservicesoverview) |
| Microsoft.WorkloadMonitor | [Azure Monitor](../../azure-monitor/index.yml) |

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre provedores de recursos, consulte [tipos e provedores de recursos do Azure](resource-providers-and-types.md)
