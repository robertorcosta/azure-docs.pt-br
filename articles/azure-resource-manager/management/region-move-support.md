---
title: Suporte para mover recursos do Azure entre regiões
description: Lista os tipos de recursos do Azure que podem ser movidos entre regiões do Azure
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 01/20/2020
ms.author: raynew
ms.openlocfilehash: 9bc7dc66ccf3049ac878f7871c816e5ade1afde5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "76760701"
---
# <a name="support-for-moving-azure-resources-across-regions"></a>Suporte para mover recursos do Azure entre regiões

Este artigo confirma se há suporte para um tipo de recurso do Azure para a mudança para outra região do Azure. 

Ir para um namespace do provedor de recursos:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [microsoft.aadiam](#microsoftaadiam)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft. AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppService](#microsoftappservice)
> - [Microsoft. Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft. AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft. batch](#microsoftbatch)
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
> - [Microsoft. Cognitivaservices](#microsoftcognitiveservices)
> - [Microsoft. Compute](#microsoftcompute)
> - [Microsoft.Container](#microsoftcontainer)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.ContentModerator](#microsoftcontentmoderator)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerInsights](#microsoftcustomerinsights)
> - [Microsoft. CustomProviders](#microsoftcustomproviders)
> - [Microsoft. Data Box](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataConnect](#microsoftdataconnect)
> - [Microsoft.DataExchange](#microsoftdataexchange)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLake](#microsoftdatalake)
> - [Microsoft. DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [Microsoft. DataShare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft. Devices](#microsoftdevices)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft. EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft. EventHub](#microsofteventhub)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft. HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [Microsoft. insights](#microsoftinsights)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft. keyvault](#microsoftkeyvault)
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
> - [Microsoft. Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft. Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft. OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft. OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft. emparelhamento](#microsoftpeering)
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
> - [Microsoft. SQL](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.SqlVM](#microsoftsqlvm)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft. StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Microsoft.TerraformOSS](#microsoftterraformoss)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft. token](#microsofttoken)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [microsoft.visualstudio](#microsoftvisualstudio)
> - [Microsoft. VMwareCloudSimple](#microsoftvmwarecloudsimple)
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
> | serviço |  Sim | 

## <a name="microsoftappconfiguration"></a>Microsoft. AppConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | configurationstores | Não | 

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | apiapps | Não | 
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
> | automationaccounts | Não | 
> | automationaccounts/configurações | Não | 
> | automationaccounts/runbooks | Não | 



## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | b2cdirectories | Não | 

## <a name="microsoftazuredata"></a>Microsoft. AzureData

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
> | batchaccounts | Não |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | clusters | Não | 
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
> | blockchainmembers | Não |
> | inspetores | Não | 

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
> | profiles | Não | 
> | perfis/pontos de extremidade | Não | 

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | certificateorders | Não | 


## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | domainnames | Não |  
> | virtualmachines | Não | 



## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | networksecuritygroups | Não |
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
> | contas | Não | 

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | availabilitysets | Não | 
> | diskencryptionsets | Não | 
> | disks | Não | 
> | galleries | Não | 
> | galerias/imagens | Não | 
> | galerias/imagens/versões | Não | 
> | hosts | Não | 
> | hosts/hosts | Não | 
> | images | Não | 
> | proximityplacementgroups | Não | 
> | restorepointcollections | Não | 
> | sharedvmimages | Não | 
> | sharedvmimages/versões | Não | 
> | snapshots | Não | 
> | virtualmachines | Sim | 
> | VirtualMachines/extensões | Não | 
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
> | registros/BuildTasks | Não |  
> | registros/replicações | Não | 
> | registros/tarefas | Não |  
> | registros/WebHooks | Não | 

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | containerservices | Não | 
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
> | contas | Não | 

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

## <a name="microsoftcustomproviders"></a>Microsoft. CustomProviders

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
> | catálogos de | Não | 

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
> | contas | Não | 

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | contas | Não | 

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | services | Não | 
> | serviços/projetos | Não | 
> | slots | Não | 

## <a name="microsoftdatashare"></a>Microsoft. DataShare

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | contas | Não | 

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | servers | Não |  

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | servers | Não |  

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | servergroups | Não | 
> | servers | Não |  
> | serversv2 | Não | 

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | artifactsources | Não | 
> | rollouts | Não |  
> | servicetopologies | Não | 
> | serviços e pertopologias | Não |  
> | pertopologias/serviços/unidades de serviço | Não | 
> | etapas | Não | 

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | elasticpools | Não | 
> | elasticpools / iothubtenants | Não | 
> | iothubs | Sim | 
> | provisioningservices | Não | 

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | controladores | Não | 

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | labcenters | Não | 
> | labs | Não | 
> | laboratórios/ambientes | Não |  
> | laboratórios/perrunners | Não | 
> | laboratórios/VirtualMachines | Não |  
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

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft. EnterpriseKnowledgeGraph

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
> | namespaces | Não | 

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | contas | Não | 

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

## <a name="microsofthealthcareapis"></a>Microsoft. HealthcareApis

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | services | Não |  

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | maquina | Não | 

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
> | contas | Não | 
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
> | iotapps |  Não |  

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
> | contas | Não | 

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | contas | Não | 

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
> | contas | Não | 
> | contas/espaços de trabalho | Não | 
> | contas/espaços de trabalho/projetos | Não | 
> | teamaccounts | Não | 
> | teamaccounts/espaços de trabalho | Não | 
> | teamaccounts/espaços de trabalho/projetos | Não | 

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | contas | Não | 

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
> | contas |  Não |  

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | classicdevservices | Não | 

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | mediaservices |  Não | 
> | mediaservices/liveevents |  Não | 
> | mediaservices/streamingendpoints |  Não | 

## <a name="microsoftmicroservices4spring"></a>Microsoft. Microservices4Spring

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
> | netappaccounts / capacitypools | Não | 
> | netappaccounts/capacitypools/volumes | Não | 
> | netappaccounts/capacitypools/volumes/mounttargets | Não | 
> | netappaccounts/capacitypools/volumes/instantâneos | Não | 

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
> | loadbalancers | Sim-SKU básico<br>Não-SKU padrão | Sim-SKU básico<br> -SKU padrão Sim |
> | localnetworkgateways |  Não | 
> | natgateways |  Não | 
> | networkintentpolicies |  Não | 
> | networkinterfaces | Sim | 
> | networkprofiles | Não | 
> | networksecuritygroups | Sim | 
> | networkwatchers |  Não |  
> | networkwatchers / connectionmonitors |  Não | 
> | networkwatchers/lentes |  Não | 
> | networkwatchers / pingmeshes |  Não | 
> | p2svpngateways | Não | 
> | privatednszones |  Não |  
> | privatednszones / virtualnetworklinks |  Não |  
> | privateendpoints | Não | 
> | privatelinkservices | Não | 
> | publicipaddresses | Sim-SKU básico<br>Não-SKU padrão | Sim-SKU básico<br>Não-SKU padrão |
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
> | vpngateways (WAN virtual) | Não | 
> | vpnsites (WAN virtual) | Não | 
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

## <a name="microsoftpeering"></a>Microsoft. emparelhamento

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | emparelhamentos | Não | 

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
> | contas | Não | 

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | vaults | Não. [Desabilitar o cofre e recriar](https://docs.microsoft.com/azure/site-recovery/move-vaults-across-regions) para site Recovery  | 


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
> | clusters/aplicativos | Não | 
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
> | ManagedInstances/bancos de dados | Sim | 
> | servers | Sim | 
> | servidores/bancos de dados | Sim | 
> | servidores/elasticpools | Sim | 
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
> | storageaccounts | Sim | 

## <a name="microsoftstoragecache"></a>Microsoft. StorageCache

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
> | ambientes/EventSources | Não | 
> | instances | Não | 
> | instâncias/ambientes | Não | 
> | instâncias/ambientes/EventSources | Não | 

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
> | ambientes/EventSources |  Não |  
> | ambientes/referencedatasets |  Não | 

## <a name="microsofttoken"></a>Microsoft. token

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
> | conta/extensão |  Não | 
> | conta/projeto |  Não | 



## <a name="microsoftvmwarecloudsimple"></a>Microsoft. VMwareCloudSimple

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
> | sites/Slots |  Não |  


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
