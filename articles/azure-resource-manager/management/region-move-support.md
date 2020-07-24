---
title: Suporte para mover recursos do Azure entre regiões
description: Lista os tipos de recursos do Azure que podem ser movidos entre regiões do Azure
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 07/21/2020
ms.author: raynew
ms.openlocfilehash: 70f981f2763dd36f0f417faec6c81e168e9856e7
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87040958"
---
# <a name="support-for-moving-azure-resources-across-regions"></a>Suporte para mover recursos do Azure entre regiões

Este artigo confirma se há suporte para um tipo de recurso do Azure para a mudança para outra região do Azure. 

Pule para um namespace de provedor de recursos:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [microsoft.aadiam](#microsoftaadiam)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppService](#microsoftappservice)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.BatchAI](#microsoftbatchai)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Container](#microsoftcontainer)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.ContentModerator](#microsoftcontentmoderator)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerInsights](#microsoftcustomerinsights)
> - [Microsoft.CustomProviders](#microsoftcustomproviders)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataConnect](#microsoftdataconnect)
> - [Microsoft.DataExchange](#microsoftdataexchange)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLake](#microsoftdatalake)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [Microsoft.DataShare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [microsoft.insights](#microsoftinsights)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.LocationBasedServices](#microsoftlocationbasedservices)
> - [Microsoft.LocationServices](#microsoftlocationservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningCompute](#microsoftmachinelearningcompute)
> - [Microsoft.MachineLearningExperimentation](#microsoftmachinelearningexperimentation)
> - [Microsoft.MachineLearningModelManagement](#microsoftmachinelearningmodelmanagement)
> - [Microsoft.MachineLearningOperationalization](#microsoftmachinelearningoperationalization)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft.Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PortalSdk](#microsoftportalsdk)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.ProjectOxford](#microsoftprojectoxford)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Scheduler](#microsoftscheduler)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.Sql](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.SqlVM](#microsoftsqlvm)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Microsoft.TerraformOSS](#microsoftterraformoss)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.Token](#microsofttoken)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [microsoft.visualstudio](#microsoftvisualstudio)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft. WindowsVirtualDesktop](#microsoftwindowsvirtualdesktop)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- | 
> | domainservices | Não | 
> | DomainServices/replicasets | Não | 

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | tenants | Não |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | actionrules | Não | 

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | servers | Não |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | serviço |  Sim (usando o modelo) <br/><br/> [Mova o gerenciamento de API entre regiões](../../api-management/api-management-howto-migrate.md). | 

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | configurationstores | Não | 

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | apiapps | Sim (usando o modelo)<br/><br/> [Mover um aplicativo do serviço de aplicativo para outra região](../../app-service/manage-move-across-regions.md) | 
> | appidentities | Não | 
> | gateways | Não | 


## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | policyassignments | Não |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | automationaccounts | Sim (usando o modelo) <br/><br/> [Usando a replicação geográfica](../../automation/automation-managing-data.md#geo-replication-in-azure-automation) |  
> | automationaccounts/configurations | Não | 
> | automationaccounts/runbooks | Não | 



## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | b2cdirectories | Não | 

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | sqlserverregistrations | Não |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | registrations | Não | 

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | batchaccounts |  As contas do lote não podem ser movidas diretamente de uma região para outra, mas você pode usar um modelo para exportar um modelo, modificá-lo e implantar o modelo na nova região. <br/><br/> Saiba mais sobre como [mover uma conta do lote entre regiões](../../batch/best-practices.md#moving-batch-accounts-across-regions) |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | clusters | Não <br/><br/> O serviço de ia do lote do Azure é [desativado](/previous-versions/azure/batch-ai/overview-what-happened-batch-ai).
> | fileservers | Não | 
> | jobs | Não | 
> | workspaces | Não | 

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | mapapis | Não | 

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | biztalk | Não | 

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | blockchainmembers | Não <br/><br/> A rede blockchain não pode ter nós em regiões diferentes. 
> | watchers | Não | 

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | blueprintassignments | Não | 

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | botservices | Não | 

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | redis | Não | 


## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | cdnwebapplicationfirewallpolicies | Não |
> | perfis | Não | 
> | profiles/endpoints | Não | 

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | certificateorders | Não | 


## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | domainnames | Nenhum trabalho está planejado para serviços clássicos.
> | virtualmachines | Não | 



## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | networksecuritygroups | Nenhum trabalho está planejado para serviços clássicos.
> | reservedips | Não | 
> | virtualnetworks | Não | 

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | storageaccounts | Sim |  


## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | accounts | Não | 
> | Cognitive Search | Com suporte com etapas manuais.<br/><br/> Saiba mais sobre como [mover o serviço de pesquisa cognitiva do Azure para outra região](../../search/search-howto-move-across-regions.md)

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | availabilitysets | Não | 
> | diskencryptionsets | Não | 
> | disks | Não | 
> | galleries | Não | 
> | galleries/images | Não | 
> | galleries/images/versions | Não | 
> | hostgroups | Não | 
> | hostgroups/hosts | Não | 
> | images | Não | 
> | proximityplacementgroups | Não | 
> | restorepointcollections | Não | 
> | sharedvmimages | Não | 
> | sharedvmimages/versions | Não | 
> | snapshots | Não | 
> | virtualmachines | Sim | 
> | virtualmachines/extensions | Não | 
> | virtualmachinescalesets | Não | 

## <a name="microsoftcontainer"></a>Microsoft.Container

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | containergroups | Não | 

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | containergroups | Não | 

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | registries | Não |  
> | registries/buildtasks | Não |  
> | registries/replications | Não | 
> | registries/tasks | Não |  
> | registries/webhooks | Não | 

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | containerservices | Não.<br/><br/> O serviço está [desativado](https://azure.microsoft.com/updates/azure-container-service-will-retire-on-january-31-2020/).
> | managedclusters | Não | 
> | openshiftmanagedclusters | Não | 

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | de dimensionamento da Web | Não | 

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | accounts | Não | 

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | conectores | Não |  

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | hubs | Não |  

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | resourceproviders | Não | 

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | jobs | Não | 

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | databoxedgedevices | Não | 

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | workspaces | Não | 

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | catalogs | Não | 
> | datacatalogs | Não | 

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | connectionmanagers | Não | 

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | pacotes | Não | 
> | planos | Não | 

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | datafactories | Não | 
> | factories | Não |  

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | datalakeaccounts | Não | 

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | accounts | Não | 

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | accounts | Não | 

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | services | Não | 
> | services/projects | Não | 
> | slots | Não | 

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | accounts | Não | 

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | servers | Você pode usar uma réplica de leitura entre regiões para mover um servidor existente. [Saiba mais](../../postgresql/howto-move-regions-portal.md).<br/><br/> Se o serviço for provisionado com o armazenamento de backup com redundância geográfica, você poderá usar a restauração geográfica para restaurar em outras regiões. [Saiba mais](../../mariadb/concepts-business-continuity.md#recover-from-an-azure-regional-data-center-outage).

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | servers | Você pode usar uma réplica de leitura entre regiões para mover um servidor existente. [Saiba mais](../../mysql/howto-move-regions-portal.md).

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | servergroups | Não | 
> | servers | Você pode usar uma réplica de leitura entre regiões para mover um servidor existente. [Saiba mais](../../postgresql/howto-move-regions-portal.md).
> | serversv2 | Não | 

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | artifactsources | Não | 
> | rollouts | Não |  
> | servicetopologies | Não | 
> | servicetopologies/services | Não |  
> | servicetopologies/services/serviceunits | Não | 
> | etapas | Não | 

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | elasticpools | Não. O recurso não está exposto.
> | elasticpools/iothubtenants | Não. O recurso não está exposto.
> | iothubs | Sim. [Saiba mais](../../iot-hub/iot-hub-how-to-clone.md)
> | provisioningservices | Não | 

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | controladores | Não | 
> | Cluster AKS | Não<br/><br/> [Saiba mais](../../dev-spaces/faq.md#can-i-migrate-my-aks-cluster-with-azure-dev-spaces-to-another-region) sobre como mudar para outra região.

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | labcenters | Não | 
> | labs | Não | 
> | labs/environments | Não |  
> | labs/servicerunners | Não | 
> | labs / virtualmachines | Não |  
> | schedules | Não |  

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | databaseaccounts | Não | 

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | domains | Não | 

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | services | Não |  

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | domains | Não |  
> | topics | Não | 

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | clusters | Não |  
> | namespaces | Sim (com modelo)<br/><br/> [Mover um namespace do hub de eventos para outra região](../../event-hubs/move-across-regions.md) | 

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | accounts | Não | 

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | hanainstances | Não | 
> | sapmonitors | Não |  

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | clusters | Não | 

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | services | Não |  

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | machines | Não | 

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | datamanagers |  Não | 

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | jobs |  Não | 

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | accounts | Não | 
> | actiongroups |  Não | 
> | activitylogalerts | Não | 
> | alertrules |  Não | 
> | autoscalesettings |  Não | 
> | components |  Não |  
> | guestdiagnosticsettings | Não | 
> | metricalerts | Não | 
> | notificationgroups | Não | 
> | notificationrules | Não | 
> | scheduledqueryrules |  Não | 
> | webtests |  Não | 
> | workbooks |  Não |  


## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | checknameavailability |  Não.<br/><br/> IoT Central trabalha com regiões geográficas, e não com regiões.
> | grafo | Não

## <a name="microsoftiothub"></a>Microsoft.IoTHub

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> |  iothub |  Sim (clonar Hub) <br/><br/> [Clonar um hub IoT para outra região](../../iot-hub/iot-hub-how-to-clone.md)

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | checknameavailability |  Não |  
> | grafo |  Não | 

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | hsmpools | Não | 
> | vaults |  Não | 


## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | clusters |  Não |  

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | labaccounts | Não | 

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | accounts | Não | 

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | accounts | Não, é um serviço global.

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | hostingenvironments | Não | 
> | integrationaccounts |  Não |  
> | integrationserviceenvironments | Não | 
> | isolatedenvironments | Não | 
> | workflows |  Não |  

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | commitmentplans |  Não | 
> | webservices |  Não | 
> | workspaces |  Não | 

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | operationalizationclusters |  Não | 

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | accounts | Não | 
> | accounts/workspaces | Não | 
> | accounts/workspaces/projects | Não | 
> | teamaccounts | Não | 
> | teamaccounts/workspaces | Não | 
> | teamaccounts/workspaces/projects | Não | 

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | accounts | Não | 

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | hostingaccounts | Não | 

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | workspaces | Não | 

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | userassignedidentities | Não | 

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | accounts |  Não, o Azure Maps é um serviço geoespacial. 

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | classicdevservices | Nenhum trabalho está planejado para serviços clássicos 

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | mediaservices |  Não | 
> | mediaservices/liveevents |  Não | 
> | mediaservices/streamingendpoints |  Não | 

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | appclusters | Não | 

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | assessmentprojects | Não | 
> | migrateprojects | Não | 
> | projects | Não | 

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | netappaccounts | Não | 
> | netappaccounts/capacitypools | Não | 
> | netappaccounts/capacitypools/volumes | Não | 
> | netappaccounts/capacitypools/volumes/mounttargets | Não | 
> | netappaccounts/capacitypools/volumes/snapshots | Não | 

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | applicationgateways | Não | 
> | applicationgatewaywebapplicationfirewallpolicies | Não | 
> | applicationsecuritygroups |  Não |  
> | azurefirewalls |  Não |  
> | bastionhosts | Não | 
> | connections |  Não | 
> | ddoscustompolicies |  Não | 
> | ddosprotectionplans | Não | 
> | dnszones |  Não | 
> | expressroutecircuits | Não | 
> | expressroutecrossconnections | Não | 
> | expressroutegateways | Não | 
> | expressrouteports | Não | 
> | frontdoors | Não | 
> | frontdoorwebapplicationfirewallpolicies | Não | 
> | loadbalancers | Sim <br/><br/> Você pode exportar a configuração existente como um modelo e implantar o modelo na nova região. Saiba como mover um balanceador de carga [externo](../..//load-balancer/move-across-regions-external-load-balancer-portal.md) ou [interno](../../load-balancer/move-across-regions-internal-load-balancer-portal.md) . |
> | localnetworkgateways |  Não | 
> | natgateways |  Não | 
> | networkintentpolicies |  Não | 
> | networkinterfaces | Sim | 
> | networkprofiles | Não | 
> | networksecuritygroups | Sim | 
> | networkwatchers |  Não |  
> | networkwatchers/connectionmonitors |  Não | 
> | networkwatchers/lentes |  Não | 
> | networkwatchers/pingmeshes |  Não | 
> | p2svpngateways | Não | 
> | privatednszones |  Não |  
> | privatednszones/virtualnetworklinks |  Não |  
> | privateendpoints | Não | 
> | privatelinkservices | Não | 
> | publicipaddresses | Sim<br/><br/> Você pode exportar a configuração de endereço IP público existente como um modelo e implantar o modelo na nova região. [Saiba mais](../../virtual-network/move-across-regions-publicip-portal.md) sobre como mover um endereço IP público. |
> | publicipprefixes | Não | 
> | routefilters | Não | 
> | routetables |  Não | 
> | serviceendpointpolicies |  Não | 
> | trafficmanagerprofiles |  Não | 
> | virtualhubs | Não | 
> | virtualnetworkgateways |  Não |  
> | virtualnetworks |  Não | 
> | virtualnetworktaps | Não | 
> | virtualwans | Não | 
> | vpngateways (WAN Virtual) | Não | 
> | vpnsites (WAN Virtual) | Não | 
> | webapplicationfirewallpolicies |  Não | 


## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | namespaces |  Não | 
> | namespaces/notificationhubs |  Não |  

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | workspaces |  Não | 



## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | managementconfigurations |  Não | 
> | Modos de exibição |  Não | 

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | peerings | Não | 

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | dashboards | Não | 

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | rootresources | Não | 

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | workspacecollections |  Não | 

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | capacities |  Não | 

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | accounts | Não | 

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | vaults | Não.<br/><br/> Não há suporte para a movimentação de cofres dos serviços de recuperação para o backup do Azure nas regiões do Azure.<br/><br/> Nos cofres dos serviços de recuperação para Azure Site Recovery, você pode [desabilitar e recriar o cofre](../../site-recovery/move-vaults-across-regions.md) na região de destino. | 


## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | namespaces |  Não | 

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | consultas |  Não |  

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | de dimensionamento da Web |  Não | 

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | flows |  Não |  
> | jobcollections |  Não | 

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | searchservices |  Não | 


## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | iotsecuritysolutions |  Não | 
> | playbookconfigurations | Não | 

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | gateways | Não | 
> | nós | Não | 

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | namespaces |  Não | 

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | de dimensionamento da Web | Não | 
> | clusters |  Não | 
> | clusters/applications | Não | 
> | containergroups | Não | 
> | containergroupsets | Não | 
> | edgeclusters | Não | 
> | networks | Não | 
> | secretstores | Não | 
> | volumes | Não | 

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | de dimensionamento da Web |  Não | 
> | containergroups | Não | 
> | gateways |  Não | 
> | networks |  Não | 
> | segredos |  Não | 
> | volumes |  Não |  

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | signalr |  Não |  

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | appliancedefinitions | Não | 
> | appliances | Não | 
> | applicationdefinitions | Não | 
> | de dimensionamento da Web | Não | 
> | jitrequests | Não | 

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | instancepools | Não | 
> | managedinstances | Sim | 
> | managedinstances/databases | Sim | 
> | servers | Sim | 
> | servers/databases | Sim | 
> | servers/elasticpools | Sim | 
> | virtualclusters | Sim | 

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | sqlvirtualmachinegroups |  Não |  
> | sqlvirtualmachines |  Não |  

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | dwvm | Não | 

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | storageaccounts | Sim<br/><br/> [Mover uma conta de armazenamento do Azure para outra região](../../storage/common/storage-account-move.md) | 

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | caches | Não | 

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | storagesyncservices |  Não | 

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | storagesyncservices | Não | 

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | storagesyncservices | Não | 

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | managers | Não | 

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | streamingjobs |  Não |  


## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | environments | Não | 
> | environments/eventsources | Não | 
> | instances | Não | 
> | instances/environments | Não | 
> | instances/environments/eventsources | Não | 

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | providerregistrations | Não | 
> | recursos | Não | 

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | environments |  Não | 
> | environments/eventsources |  Não |  
> | environments/referencedatasets |  Não | 

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | stores | Não | 

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | imagetemplates | Não | 

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | account |  Não | 
> | account/extension |  Não | 
> | account/project |  Não | 



## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | dedicatedcloudnodes | Não | 
> | dedicatedcloudservices | Não | 
> | virtualmachines | Não | 

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | certificates | Não | 
> | connectiongateways |  Não |  
> | connections |  Não |  
> | customapis |  Não | 
> | hostingenvironments | Não | 
> | serverfarms |  Não |  
> | sites |  Não | 
> | sites/premieraddons |  Não |  
> | sites / slots |  Não |  


## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | deviceservices | Não | 

## <a name="microsoftwindowsvirtualdesktop"></a>Microsoft. WindowsVirtualDesktop

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | applicationgroups | Não | 
> | hostpools | Não | 
> | workspaces | Não | 

## <a name="third-party-services"></a>Serviços de terceiros

No momento, serviços de terceiros não dão suporte à operação de movimentação.
