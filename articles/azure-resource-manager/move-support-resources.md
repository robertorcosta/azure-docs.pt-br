---
title: Mover o suporte de operação por tipo de recurso
description: Lista os tipos de recursos do Azure que podem ser movidos para um novo grupo de recursos ou assinatura.
ms.topic: reference
ms.date: 10/24/2019
ms.openlocfilehash: 182e5fdd081126500c04f44776c9e9b8fad61843
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149776"
---
# <a name="move-operation-support-for-resources"></a>Mover o suporte de operação para recursos
Este artigo lista se um tipo de recurso do Azure é compatível com a operação de movimentação. Ele também fornece informações sobre condições especiais a serem consideradas ao mover um recurso.

Ir para um namespace do provedor de recursos:
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
> - [Microsoft. AzureData](#microsoftazuredata)
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
> - [Microsoft. CustomProviders](#microsoftcustomproviders)
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
> - [Microsoft. DataShare](#microsoftdatashare)
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
> - [Microsoft. Microservices4Spring](#microsoftmicroservices4spring)
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
> - [Microsoft. ResourceGraph](#microsoftresourcegraph)
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
> - [Microsoft.WindowsVirtualDesktop](#microsoftwindowsvirtualdesktop)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | domainservices | Não | Não |
> | DomainServices/replicasets | Não | Não |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | tenants | Não | Não |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | actionrules | sim | sim |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | servers | sim | sim |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | serviço | sim | sim |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | configurationstores | sim | sim |

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | apiapps | Não | Não |
> | appidentities | Não | Não |
> | gateways | Não | Não |

> [!IMPORTANT]
> Consulte [diretrizes de movimentação do serviço de aplicativo](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | policyassignments | Não | Não |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | automationaccounts | sim | sim |
> | automationaccounts/configurações | sim | sim |
> | automationaccounts/runbooks | sim | sim |

> [!IMPORTANT]
> Os Runbooks devem existir no mesmo grupo de recursos que a conta de automação.

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | b2cdirectories | sim | sim |

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | sqlserverregistrations | Não | Não |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | registrations | sim | sim |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | batchaccounts | sim | sim |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | clusters | Não | Não |
> | fileservers | Não | Não |
> | jobs | Não | Não |
> | workspaces | Não | Não |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | mapapis | Não | Não |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | biztalk | sim | sim |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | blockchainmembers | sim | sim |
> | espectadores | Não | Não |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | blueprintassignments | Não | Não |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | botservices | sim | sim |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | redis | sim | sim |

> [!IMPORTANT]
> Se o cache do Azure para instância Redis estiver configurado com uma rede virtual, a instância não poderá ser movida para uma assinatura diferente. Consulte [limitações de movimentação de rede](./move-limitations/networking-move-limitations.md).

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | cdnwebapplicationfirewallpolicies | Não | Não |
> | profiles | sim | sim |
> | perfis/pontos de extremidade | sim | sim |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | certificateorders | sim | sim |

> [!IMPORTANT]
> Consulte [diretrizes de movimentação do serviço de aplicativo](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | domainnames | sim | Não |
> | virtualmachines | sim | Não |

> [!IMPORTANT]
> Consulte [diretrizes de movimentação da implantação clássica](./move-limitations/classic-model-move-limitations.md). Os recursos de implantação clássicos podem ser movidos entre assinaturas com uma operação específica para esse cenário.

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | networksecuritygroups | Não | Não |
> | reservedips | Não | Não |
> | virtualnetworks | Não | Não |

> [!IMPORTANT]
> Consulte [diretrizes de movimentação da implantação clássica](./move-limitations/classic-model-move-limitations.md). Os recursos de implantação clássicos podem ser movidos entre assinaturas com uma operação específica para esse cenário.

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | storageaccounts | sim | Não |

> [!IMPORTANT]
> Consulte [diretrizes de movimentação da implantação clássica](./move-limitations/classic-model-move-limitations.md). Os recursos de implantação clássicos podem ser movidos entre assinaturas com uma operação específica para esse cenário.

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | accounts | sim | sim |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | availabilitysets | sim | sim |
> | diskencryptionsets | Não | Não |
> | disks | sim | sim |
> | galleries | Não | Não |
> | galerias/imagens | Não | Não |
> | galerias/imagens/versões | Não | Não |
> | hosts | Não | Não |
> | hosts/hosts | Não | Não |
> | images | sim | sim |
> | proximityplacementgroups | Não | Não |
> | restorepointcollections | Não | Não |
> | sharedvmimages | Não | Não |
> | sharedvmimages/versões | Não | Não |
> | snapshots | sim | sim |
> | virtualmachines | sim | sim |
> | VirtualMachines/extensões | sim | sim |
> | virtualmachinescalesets | sim | sim |

> [!IMPORTANT]
> Consulte as [diretrizes de movimentação de máquinas virtuais](./move-limitations/virtual-machines-move-limitations.md).

## <a name="microsoftcontainer"></a>Microsoft.Container

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | containergroups | Não | Não |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | containergroups | Não | Não |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | registries | sim | sim |
> | registros/BuildTasks | sim | sim |
> | registros/replicações | sim | sim |
> | registros/tarefas | sim | sim |
> | registros/WebHooks | sim | sim |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | containerservices | Não | Não |
> | managedclusters | Não | Não |
> | openshiftmanagedclusters | Não | Não |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | de dimensionamento da Web | sim | sim |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | conectores | sim | sim |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | hubs | sim | sim |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | resourceproviders | sim | sim |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | jobs | Não | Não |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | databoxedgedevices | Não | Não |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | workspaces | Não | Não |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | catalogs | sim | sim |
> | catálogos de | Não | Não |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | connectionmanagers | Não | Não |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | pacotes | Não | Não |
> | planos | Não | Não |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | datafactories | sim | sim |
> | factories | sim | sim |

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | datalakeaccounts | Não | Não |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | accounts | sim | sim |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | accounts | sim | sim |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | services | Não | Não |
> | serviços/projetos | Não | Não |
> | slots | Não | Não |

## <a name="microsoftdatashare"></a>Microsoft. DataShare

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | servers | sim | sim |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | servers | sim | sim |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | servergroups | Não | Não |
> | servers | sim | sim |
> | serversv2 | sim | sim |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | artifactsources | sim | sim |
> | rollouts | sim | sim |
> | servicetopologies | sim | sim |
> | serviços e pertopologias | sim | sim |
> | pertopologias/serviços/unidades de serviço | sim | sim |
> | etapas | sim | sim |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | elasticpools | Não | Não |
> | elasticpools / iothubtenants | Não | Não |
> | iothubs | sim | sim |
> | provisioningservices | sim | sim |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | controladores | sim | sim |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | labcenters | Não | Não |
> | labs | sim | Não |
> | laboratórios/ambientes | sim | sim |
> | laboratórios/perrunners | sim | sim |
> | laboratórios/VirtualMachines | sim | Não |
> | schedules | sim | sim |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | databaseaccounts | sim | sim |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | domains | sim | sim |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | services | sim | sim |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | domains | sim | sim |
> | topics | sim | sim |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | clusters | sim | sim |
> | namespaces | sim | sim |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | hanainstances | Não | Não |
> | sapmonitors | sim | sim |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | clusters | sim | sim |

> [!IMPORTANT]
> Você pode mover os clusters HDInsight para uma nova assinatura ou grupo de recursos. No entanto, não é possível mover os recursos de rede vinculados ao cluster HDInsight (por exemplo, a rede virtual, NIC ou balanceador de carga) entre assinaturas. Além disso, não é possível mover uma para um novo grupo de recursos uma NIC que está conectada a uma máquina virtual para o cluster.
>
> Ao mover um cluster HDInsight para uma nova assinatura, mova primeiro os outros recursos (como a conta de armazenamento). Em seguida, mova apenas o cluster HDInsight.

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | services | sim | sim |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | maquina | Não | Não |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | datamanagers | sim | sim |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | jobs | sim | sim |

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |
> | actiongroups | sim | sim |
> | activitylogalerts | Não | Não |
> | alertrules | sim | sim |
> | autoscalesettings | sim | sim |
> | components | sim | sim |
> | guestdiagnosticsettings | Não | Não |
> | metricalerts | Não | Não |
> | notificationgroups | Não | Não |
> | notificationrules | Não | Não |
> | scheduledqueryrules | sim | sim |
> | webtests | sim | sim |
> | workbooks | sim | sim |

> [!IMPORTANT]
> Certifique-se de que a mudança para a nova assinatura não exceda as [cotas de assinatura](../azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | iotapps | sim | sim |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | checknameavailability | sim | sim |
> | grafo | sim | sim |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | hsmpools | Não | Não |
> | vaults | sim | sim |

> [!IMPORTANT]
> Os cofres de chaves usados para criptografia de disco não podem ser movidos para um grupo de recursos na mesma assinatura ou entre assinaturas.

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | clusters | sim | sim |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | labaccounts | Não | Não |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | hostingenvironments | Não | Não |
> | integrationaccounts | sim | sim |
> | integrationserviceenvironments | Não | Não |
> | isolatedenvironments | Não | Não |
> | workflows | sim | sim |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | commitmentplans | sim | sim |
> | webservices | sim | Não |
> | workspaces | sim | sim |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | operationalizationclusters | sim | sim |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |
> | contas/espaços de trabalho | Não | Não |
> | contas/espaços de trabalho/projetos | Não | Não |
> | teamaccounts | Não | Não |
> | teamaccounts/espaços de trabalho | Não | Não |
> | teamaccounts/espaços de trabalho/projetos | Não | Não |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | hostingaccounts | Não | Não |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | workspaces | Não | Não |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | userassignedidentities | Não | Não |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | accounts | sim | sim |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | classicdevservices | Não | Não |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | mediaservices | sim | sim |
> | mediaservices/liveevents | sim | sim |
> | mediaservices/streamingendpoints | sim | sim |

## <a name="microsoftmicroservices4spring"></a>Microsoft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | appclusters | Não | Não |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | assessmentprojects | Não | Não |
> | migrateprojects | Não | Não |
> | projects | Não | Não |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | netappaccounts | Não | Não |
> | netappaccounts / capacitypools | Não | Não |
> | netappaccounts/capacitypools/volumes | Não | Não |
> | netappaccounts/capacitypools/volumes/mounttargets | Não | Não |
> | netappaccounts/capacitypools/volumes/instantâneos | Não | Não |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | applicationgateways | Não | Não |
> | applicationgatewaywebapplicationfirewallpolicies | Não | Não |
> | applicationsecuritygroups | sim | sim |
> | azurefirewalls | sim | sim |
> | bastionhosts | Não | Não |
> | connections | sim | sim |
> | ddoscustompolicies | sim | sim |
> | ddosprotectionplans | Não | Não |
> | dnszones | sim | sim |
> | expressroutecircuits | Não | Não |
> | expressroutecrossconnections | Não | Não |
> | expressroutegateways | Não | Não |
> | expressrouteports | Não | Não |
> | frontdoors | Não | Não |
> | frontdoorwebapplicationfirewallpolicies | Não | Não |
> | loadbalancers | Sim-SKU básico<br>Não-SKU padrão | Sim-SKU básico<br>Não-SKU padrão |
> | localnetworkgateways | sim | sim |
> | natgateways | sim | sim |
> | networkintentpolicies | sim | sim |
> | networkinterfaces | sim | sim |
> | networkprofiles | Não | Não |
> | networksecuritygroups | sim | sim |
> | networkwatchers | sim | sim |
> | networkwatchers / connectionmonitors | sim | sim |
> | networkwatchers/lentes | sim | sim |
> | networkwatchers / pingmeshes | sim | sim |
> | p2svpngateways | Não | Não |
> | privatednszones | sim | sim |
> | privatednszones / virtualnetworklinks | sim | sim |
> | privateendpoints | Não | Não |
> | privatelinkservices | Não | Não |
> | publicipaddresses | Sim-SKU básico<br>Não-SKU padrão | Sim-SKU básico<br>Não-SKU padrão |
> | publicipprefixes | sim | sim |
> | routefilters | Não | Não |
> | routetables | sim | sim |
> | serviceendpointpolicies | sim | sim |
> | trafficmanagerprofiles | sim | sim |
> | virtualhubs | Não | Não |
> | virtualnetworkgateways | sim | sim |
> | virtualnetworks | sim | sim |
> | virtualnetworktaps | Não | Não |
> | virtualwans | Não | Não |
> | vpngateways (WAN virtual) | Não | Não |
> | vpnsites (WAN virtual) | Não | Não |
> | webapplicationfirewallpolicies | sim | sim |

> [!IMPORTANT]
> Consulte [diretrizes de movimentação de rede](./move-limitations/networking-move-limitations.md).

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | namespaces | sim | sim |
> | namespaces/notificationhubs | sim | sim |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | workspaces | sim | sim |

> [!IMPORTANT]
> Certifique-se de que a mudança para a nova assinatura não exceda as [cotas de assinatura](../azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | managementconfigurations | sim | sim |
> | solutions | sim | sim |
> | Modos de exibição | sim | sim |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | emparelhamentos | Não | Não |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | dashboards | sim | sim |

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | rootresources | Não | Não |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | workspacecollections | sim | sim |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | capacities | sim | sim |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | vaults | sim | sim |

> [!IMPORTANT]
> Consulte [diretrizes de movimentação dos serviços de recuperação](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | namespaces | sim | sim |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | consultas | sim | sim |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | de dimensionamento da Web | sim | Não |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | flows | sim | sim |
> | jobcollections | sim | sim |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | searchservices | sim | sim |

> [!IMPORTANT]
> Você não pode mover vários recursos de pesquisa em regiões diferentes em uma única operação. Em vez disso, mova-os em operações separadas.

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | iotsecuritysolutions | sim | sim |
> | playbookconfigurations | Não | Não |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | gateways | Não | Não |
> | nós | Não | Não |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | namespaces | sim | sim |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | de dimensionamento da Web | Não | Não |
> | clusters | sim | sim |
> | clusters/aplicativos | Não | Não |
> | containergroups | Não | Não |
> | containergroupsets | Não | Não |
> | edgeclusters | Não | Não |
> | networks | Não | Não |
> | secretstores | Não | Não |
> | volumes | Não | Não |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | de dimensionamento da Web | sim | sim |
> | containergroups | Não | Não |
> | gateways | sim | sim |
> | networks | sim | sim |
> | segredos | sim | sim |
> | volumes | sim | sim |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | signalr | sim | sim |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | appliancedefinitions | Não | Não |
> | appliances | Não | Não |
> | applicationdefinitions | Não | Não |
> | de dimensionamento da Web | Não | Não |
> | jitrequests | Não | Não |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | instancepools | Não | Não |
> | managedinstances | Não | Não |
> | ManagedInstances/bancos de dados | Não | Não |
> | servers | sim | sim |
> | servidores/bancos de dados | sim | sim |
> | servidores/elasticpools | sim | sim |
> | virtualclusters | sim | sim |

> [!IMPORTANT]
> Um banco de dados e um servidor devem estar no mesmo grupo de recursos. Quando você move um SQL Server, todos os seus bancos de dados também são movidos. Este comportamento se aplica ao Banco de Dados SQL do Azure e ao banco de dados SQL Data Warehouse do Azure.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | sqlvirtualmachinegroups | sim | sim |
> | sqlvirtualmachines | sim | sim |

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | dwvm | Não | Não |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | storageaccounts | sim | sim |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | caches | Não | Não |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | sim | sim |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Não | Não |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Não | Não |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | managers | Não | Não |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | streamingjobs | sim | sim |

> [!IMPORTANT]
> Stream Analytics trabalhos não podem ser movidos quando estiver em estado de execução.

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | environments | Não | Não |
> | ambientes/EventSources | Não | Não |
> | instances | Não | Não |
> | instâncias/ambientes | Não | Não |
> | instâncias/ambientes/EventSources | Não | Não |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | providerregistrations | Não | Não |
> | recursos | Não | Não |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | environments | sim | sim |
> | ambientes/EventSources | sim | sim |
> | ambientes/referencedatasets | sim | sim |

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | mercado | Não | Não |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | imagetemplates | Não | Não |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | conta | sim | sim |
> | conta/extensão | sim | sim |
> | conta/projeto | sim | sim |

> [!IMPORTANT]
> Para alterar a assinatura do Azure DevOps, consulte [alterar a assinatura do Azure usada para cobrança](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | dedicatedcloudnodes | Não | Não |
> | dedicatedcloudservices | Não | Não |
> | virtualmachines | Não | Não |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | certificates | Não | sim |
> | connectiongateways | sim | sim |
> | connections | sim | sim |
> | customapis | sim | sim |
> | hostingenvironments | Não | Não |
> | serverfarms | sim | sim |
> | sites | sim | sim |
> | sites/premieraddons | sim | sim |
> | sites/Slots | sim | sim |

> [!IMPORTANT]
> Consulte [diretrizes de movimentação do serviço de aplicativo](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | deviceservices | Não | Não |

## <a name="microsoftwindowsvirtualdesktop"></a>Microsoft.WindowsVirtualDesktop

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Assinatura |
> | ------------- | ----------- | ---------- |
> | applicationgroups | Não | Não |
> | hostpools | Não | Não |
> | workspaces | Não | Não |

## <a name="third-party-services"></a>Serviços de terceiros

No momento, serviços de terceiros não dão suporte à operação de movimentação.

## <a name="next-steps"></a>Próximas etapas
Para ver comandos para mover recursos, confira [Move resources to new resource group or subscription](resource-group-move-resources.md) (Mover recursos para o novo grupo de recursos ou assinatura).

Para obter os mesmos dados como um arquivo de valores separados por vírgula, baixe [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv).
