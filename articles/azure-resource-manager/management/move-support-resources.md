---
title: Mover o suporte de operação por tipo de recurso
description: Lista os tipos de recursos do Azure que podem ser movidos para um novo grupo de recursos ou assinatura.
ms.topic: conceptual
ms.date: 01/22/2020
ms.openlocfilehash: 3fe404b8b501056f75ac45ccebc5bdbed1acbe43
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705472"
---
# <a name="move-operation-support-for-resources"></a>Mover o suporte de operação para recursos
Este artigo lista se um tipo de recurso do Azure é compatível com a operação de movimentação. Ele também fornece informações sobre condições especiais a serem consideradas ao mover um recurso.

Ir para um namespace do provedor de recursos:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [microsoft.aadiam](#microsoftaadiam)
> - [Microsoft. Advisor](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft. AppPlatform](#microsoftappplatform)
> - [Microsoft.AppService](#microsoftappservice)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft. AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.BatchAI](#microsoftbatchai)
> - [Microsoft. billing](#microsoftbilling)
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
> - [Microsoft. consumo](#microsoftconsumption)
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
> - [Microsoft. dataprotection](#microsoftdataprotection)
> - [Microsoft. DataShare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft. DevOps](#microsoftdevops)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
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
> - [Microsoft. kubernetes](#microsoftkubernetes)
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
> - [Microsoft. Managedservices](#microsoftmanagedservices)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft. Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft. ObjectStore](#microsoftobjectstore)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PortalSdk](#microsoftportalsdk)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft. ProjectBabylon](#microsoftprojectbabylon)
> - [Microsoft.ProjectOxford](#microsoftprojectoxford)
> - [Microsoft. ProviderHub](#microsoftproviderhub)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft. ResourceGraph](#microsoftresourcegraph)
> - [Microsoft. ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Scheduler](#microsoftscheduler)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft. SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft. Services](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft. SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.Sql](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.SqlVM](#microsoftsqlvm)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Microsoft. Subscription](#microsoftsubscription)
> - [microsoft.support](#microsoftsupport)
> - [Microsoft.TerraformOSS](#microsoftterraformoss)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.Token](#microsofttoken)
> - [microsoft.visualstudio](#microsoftvisualstudio)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft. VSOnline](#microsoftvsonline)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft. WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | domainservices | Não | Não |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | tenants | Não | Não |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | configurações | Não | Não |
> | filmes | Não | Não |
> | suppressions | Não | Não |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | actionrules | Sim | Sim |
> | alertas | Não | Não |
> | alertssummary | Não | Não |
> | smartdetectoralertrules | Sim | Sim |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | servidores | Sim | Sim |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | serviço | Sim | Sim |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | configurationstores | Sim | Sim |

## <a name="microsoftappplatform"></a>Microsoft. AppPlatform

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | surge | Sim | Sim |

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | apiapps | Não | Não |
> | appidentities | Não | Não |
> | gateways | Não | Não |

> [!IMPORTANT]
> Consulte [diretrizes de movimentação do serviço de aplicativo](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | CheckAccess | Não | Não |
> | denyassignments | Não | Não |
> | findorphanroleassignments | Não | Não |
> | locks | Não | Não |
> | permissões | Não | Não |
> | policyassignments | Não | Não |
> | PolicyDefinitions | Não | Não |
> | policysetdefinitions | Não | Não |
> | RoleAssignments | Não | Não |
> | roleassignmentsusagemetrics | Não | Não |
> | RoleDefinitions | Não | Não |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | automationaccounts | Sim | Sim |
> | automationaccounts/configurações | Sim | Sim |
> | automationaccounts/runbooks | Sim | Sim |

> [!IMPORTANT]
> Os Runbooks devem existir no mesmo grupo de recursos que a conta de automação.

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | b2cdirectories | Sim | Sim |

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | hybriddatamanagers | Não | Não |
> | postgresinstances | Não | Não |
> | sqlbigdataclusters | Não | Não |
> | SQLInstances | Não | Não |
> | sqlserverregistrations | Sim | Sim |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | registrations | Sim | Sim |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | batchaccounts | Sim | Sim |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | Não | Não |
> | fileservers | Não | Não |
> | jobs | Não | Não |
> | workspaces | Não | Não |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | billingperiods | Não | Não |
> | billingpermissions | Não | Não |
> | billingroleassignments | Não | Não |
> | billingroledefinitions | Não | Não |
> | createbillingroleassignment | Não | Não |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | mapapis | Não | Não |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | BizTalk | Não | Não |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | blockchainmembers | Não | Não |
> | espectadores | Não | Não |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | blueprintassignments | Não | Não |
> | blueprints | Não | Não |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | botservices | Sim | Sim |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | redis | Sim | Sim |

> [!IMPORTANT]
> Se o cache do Azure para instância Redis estiver configurado com uma rede virtual, a instância não poderá ser movida para uma assinatura diferente. Consulte [limitações de movimentação de rede](./move-limitations/networking-move-limitations.md).

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | cdnwebapplicationfirewallpolicies | Sim | Sim |
> | perfis | Sim | Sim |
> | perfis/pontos de extremidade | Sim | Sim |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | certificateorders | Sim | Sim |

> [!IMPORTANT]
> Consulte [diretrizes de movimentação do serviço de aplicativo](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | domainnames | Sim | Não |
> | virtualmachines | Sim | Não |

> [!IMPORTANT]
> Consulte [diretrizes de movimentação da implantação clássica](./move-limitations/classic-model-move-limitations.md). Os recursos de implantação clássicos podem ser movidos entre assinaturas com uma operação específica para esse cenário.

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | networksecuritygroups | Não | Não |
> | reservedips | Não | Não |
> | virtualnetworks | Não | Não |

> [!IMPORTANT]
> Consulte [diretrizes de movimentação da implantação clássica](./move-limitations/classic-model-move-limitations.md). Os recursos de implantação clássicos podem ser movidos entre assinaturas com uma operação específica para esse cenário.

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | storageaccounts | Sim | Não |

> [!IMPORTANT]
> Consulte [diretrizes de movimentação da implantação clássica](./move-limitations/classic-model-move-limitations.md). Os recursos de implantação clássicos podem ser movidos entre assinaturas com uma operação específica para esse cenário.

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Sim | Sim |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | availabilitysets | Sim | Sim |
> | diskencryptionsets | Não | Não |
> | disks | Sim | Sim |
> | galleries | Não | Não |
> | galerias/imagens | Não | Não |
> | galerias/imagens/versões | Não | Não |
> | hosts | Não | Não |
> | hosts/hosts | Não | Não |
> | images | Sim | Sim |
> | proximityplacementgroups | Não | Não |
> | restorepointcollections | Não | Não |
> | sharedvmimages | Não | Não |
> | sharedvmimages/versões | Não | Não |
> | snapshots | Sim | Sim |
> | virtualmachines | Sim | Sim |
> | VirtualMachines/extensões | Sim | Sim |
> | virtualmachinescalesets | Sim | Sim |

> [!IMPORTANT]
> Consulte as [diretrizes de movimentação de máquinas virtuais](./move-limitations/virtual-machines-move-limitations.md).

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | aggregatedcost | Não | Não |
> | saldos | Não | Não |
> | Orçamentos | Não | Não |
> | contabiliza | Não | Não |
> | costtags | Não | Não |
> | credits | Não | Não |
> | events | Não | Não |
> | Previsão | Não | Não |
> | lots | Não | Não |
> | Marketplaces | Não | Não |
> | operationresults | Não | Não |
> | OperationStatus | Não | Não |
> | pricesheets | Não | Não |
> | products | Não | Não |
> | reservationdetails | Não | Não |
> | reservationrecommendations | Não | Não |
> | reservationsummaries | Não | Não |
> | reservationtransactions | Não | Não |
> | marcas | Não | Não |
> | tenants | Não | Não |
> | terms | Não | Não |
> | usagedetails | Não | Não |

## <a name="microsoftcontainer"></a>Microsoft.Container

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | containergroups | Não | Não |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | containergroups | Não | Não |
> | serviceassociationlinks | Não | Não |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | registries | Sim | Sim |
> | registros/BuildTasks | Sim | Sim |
> | registros/replicações | Sim | Sim |
> | registros/taskruns | Sim | Sim |
> | registros/tarefas | Sim | Sim |
> | registros/WebHooks | Sim | Sim |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | containerservices | Não | Não |
> | managedclusters | Não | Não |
> | openshiftmanagedclusters | Não | Não |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | aplicativos | Não | Não |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | alertas | Não | Não |
> | Orçamentos | Não | Não |
> | conectores | Sim | Sim |
> | dimensões | Não | Não |
> | exporta | Não | Não |
> | externalsubscriptions | Não | Não |
> | forecast | Não | Não |
> | Consulta | Não | Não |
> | reportconfigs | Não | Não |
> | relatórios | Não | Não |
> | showbackrules | Não | Não |
> | Modos de exibição | Não | Não |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | hubs | Não | Não |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | associações | Não | Não |
> | resourceproviders | Sim | Sim |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | jobs | Não | Não |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | databoxedgedevices | Não | Não |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | workspaces | Não | Não |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | catalogs | Sim | Sim |
> | catálogos de | Não | Não |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | connectionmanagers | Não | Não |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | pacotes | Não | Não |
> | planos | Não | Não |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | datafactories | Sim | Sim |
> | factories | Sim | Sim |

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | datalakeaccounts | Não | Não |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Sim | Sim |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Sim | Sim |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | services | Não | Não |
> | serviços/projetos | Não | Não |
> | slots | Não | Não |

## <a name="microsoftdataprotection"></a>Microsoft. dataprotection

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | backupvaults | Não | Não |

## <a name="microsoftdatashare"></a>Microsoft. DataShare

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Sim | Sim |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | servidores | Sim | Sim |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | servidores | Sim | Sim |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | servergroups | Não | Não |
> | servidores | Sim | Sim |
> | serversv2 | Sim | Sim |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | artifactsources | Sim | Sim |
> | rollouts | Sim | Sim |
> | servicetopologies | Sim | Sim |
> | serviços e pertopologias | Sim | Sim |
> | pertopologias/serviços/unidades de serviço | Sim | Sim |
> | etapas | Sim | Sim |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | elasticpools | Não | Não |
> | elasticpools / iothubtenants | Não | Não |
> | iothubs | Sim | Sim |
> | provisioningservices | Sim | Sim |

## <a name="microsoftdevops"></a>Microsoft. DevOps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | pipelines | Sim | Sim |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | controladores | Sim | Sim |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | labcenters | Não | Não |
> | labs | Sim | Não |
> | laboratórios/ambientes | Sim | Sim |
> | laboratórios/perrunners | Sim | Sim |
> | laboratórios/VirtualMachines | Sim | Não |
> | schedules | Sim | Sim |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | databaseaccounts | Sim | Sim |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | domains | Sim | Sim |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | services | Sim | Sim |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | domains | Sim | Sim |
> | eventSubscriptions | Não, não pode ser movido de forma independente, mas automaticamente movido com o recurso assinado. | Não, não pode ser movido de forma independente, mas automaticamente movido com o recurso assinado. |
> | EventSubscriptions | Não, não pode ser movido de forma independente, mas automaticamente movido com o recurso assinado. | Não, não pode ser movido de forma independente, mas automaticamente movido com o recurso assinado. |
> | extensiontopics | Não | Não |
> | topics | Sim | Sim |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | Sim | Sim |
> | namespaces | Sim | Sim |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | guestconfigurationassignments | Não | Não |
> | software | Não | Não |
> | softwareupdateprofile | Não | Não |
> | softwareupdates | Não | Não |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | hanainstances | Não | Não |
> | sapmonitors | Sim | Sim |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | Sim | Sim |

> [!IMPORTANT]
> Você pode mover os clusters HDInsight para uma nova assinatura ou grupo de recursos. No entanto, não é possível mover os recursos de rede vinculados ao cluster HDInsight (por exemplo, a rede virtual, NIC ou balanceador de carga) entre assinaturas. Além disso, não é possível mover uma para um novo grupo de recursos uma NIC que está conectada a uma máquina virtual para o cluster.
>
> Ao mover um cluster HDInsight para uma nova assinatura, mova primeiro os outros recursos (como a conta de armazenamento). Em seguida, mova apenas o cluster HDInsight.

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | services | Sim | Sim |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | maquina | Sim | Sim |
> | máquinas/extensões | Não | Não |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | datamanagers | Sim | Sim |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | jobs | Sim | Sim |

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | actiongroups | Sim | Sim |
> | activitylogalerts | Não | Não |
> | alertrules | Sim | Sim |
> | automatedexportsettings | Não | Não |
> | autoscalesettings | Sim | Sim |
> | baseline | Não | Não |
> | calculatebaseline | Não | Não |
> | components | Sim | Sim |
> | DiagnosticSettings | Não | Não |
> | diagnosticsettingscategories | Não | Não |
> | eventtypes | Não | Não |
> | extendeddiagnosticsettings | Não | Não |
> | logdefinitions | Não | Não |
> | logs | Não | Não |
> | metricalerts | Não | Não |
> | metricbaselines | Não | Não |
> | metricdefinitions | Não | Não |
> | metricnamespaces | Não | Não |
> | Métricas | Não | Não |
> | minhas pastas de trabalho | Não | Não |
> | scheduledqueryrules | Sim | Sim |
> | Topologia | Não | Não |
> | transações | Não | Não |
> | vminsightsonboardingstatuses | Não | Não |
> | webtests | Sim | Sim |
> | workbooks | Sim | Sim |
> | workbooktemplates | Sim | Sim |

> [!IMPORTANT]
> Certifique-se de que a mudança para a nova assinatura não exceda as [cotas de assinatura](azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | iotapps | Sim | Sim |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Sim | Sim |
> | grafo | Sim | Sim |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | vaults | Sim | Sim |

> [!IMPORTANT]
> Os cofres de chaves usados para criptografia de disco não podem ser movidos para um grupo de recursos na mesma assinatura ou entre assinaturas.

## <a name="microsoftkubernetes"></a>Microsoft. kubernetes

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | connectedclusters | Não | Não |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | Sim | Sim |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | labaccounts | Não | Não |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | hostingenvironments | Não | Não |
> | integrationaccounts | Sim | Sim |
> | integrationserviceenvironments | Sim | Não |
> | integrationserviceenvironments / managedapis | Sim | Não |
> | isolatedenvironments | Não | Não |
> | workflows | Sim | Sim |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | commitmentplans | Sim | Sim |
> | webservices | Sim | Não |
> | workspaces | Sim | Sim |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | operationalizationclusters | Não | Não |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |
> | contas/espaços de trabalho | Não | Não |
> | contas/espaços de trabalho/projetos | Não | Não |
> | teamaccounts | Não | Não |
> | teamaccounts/espaços de trabalho | Não | Não |
> | teamaccounts/espaços de trabalho/projetos | Não | Não |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | hostingaccounts | Não | Não |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | workspaces | Não | Não |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | identidades | Não | Não |
> | userassignedidentities | Não | Não |

## <a name="microsoftmanagedservices"></a>Microsoft. Managedservices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | registrationassignments | Não | Não |
> | registrationdefinitions | Não | Não |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Sim | Sim |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | classicdevservices | Não | Não |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | mediaservices | Sim | Sim |
> | mediaservices/liveevents | Sim | Sim |
> | mediaservices/streamingendpoints | Sim | Sim |

## <a name="microsoftmicroservices4spring"></a>Microsoft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | appclusters | Não | Não |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | assessmentprojects | Sim | Sim |
> | migrateprojects | Sim | Sim |
> | projects | Não | Não |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | netappaccounts | Não | Não |
> | netappaccounts / backuppolicies | Não | Não |
> | netappaccounts / capacitypools | Não | Não |
> | netappaccounts/capacitypools/volumes | Não | Não |
> | netappaccounts/capacitypools/volumes/mounttargets | Não | Não |
> | netappaccounts/capacitypools/volumes/instantâneos | Não | Não |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | applicationgateways | Não | Não |
> | applicationgatewaywebapplicationfirewallpolicies | Não | Não |
> | applicationsecuritygroups | Sim | Sim |
> | azurefirewalls | Sim | Sim |
> | bastionhosts | Não | Não |
> | connections | Sim | Sim |
> | ddoscustompolicies | Sim | Sim |
> | ddosprotectionplans | Não | Não |
> | dnszones | Sim | Sim |
> | expressroutecircuits | Não | Não |
> | expressroutegateways | Não | Não |
> | frontdoors | Não | Não |
> | frontdoorwebapplicationfirewallpolicies | Não | Não |
> | loadbalancers | Sim-SKU básico<br>Não-SKU padrão | Sim-SKU básico<br>Não-SKU padrão |
> | localnetworkgateways | Sim | Sim |
> | networkexperimentprofiles | Sim | Sim |
> | networkintentpolicies | Sim | Sim |
> | networkinterfaces | Sim | Sim |
> | networkprofiles | Não | Não |
> | networksecuritygroups | Sim | Sim |
> | networkwatchers | Sim | Sim |
> | networkwatchers / connectionmonitors | Sim | Sim |
> | networkwatchers / flowlogs | Sim | Sim |
> | networkwatchers/lentes | Sim | Sim |
> | networkwatchers / pingmeshes | Sim | Sim |
> | p2svpngateways | Não | Não |
> | privatednszones | Sim | Sim |
> | privatednszones / virtualnetworklinks | Sim | Sim |
> | privateendpointredirectmaps | Não | Não |
> | privateendpoints | Não | Não |
> | privatelinkservices | Não | Não |
> | publicipaddresses | Sim-SKU básico<br>Não-SKU padrão | Sim-SKU básico<br>Não-SKU padrão |
> | publicipprefixes | Sim | Sim |
> | routefilters | Não | Não |
> | routetables | Sim | Sim |
> | serviceendpointpolicies | Sim | Sim |
> | trafficmanagerprofiles | Sim | Sim |
> | virtualhubs | Não | Não |
> | virtualnetworkgateways | Sim | Sim |
> | virtualnetworks | Sim | Sim |
> | virtualnetworktaps | Não | Não |
> | virtualrouters | Sim | Sim |
> | virtualwans | Não | Não |
> | vpngateways (WAN virtual) | Não | Não |
> | vpnserverconfigurations | Não | Não |
> | vpnsites (WAN virtual) | Não | Não |
> | webapplicationfirewallpolicies | Sim | Sim |

> [!IMPORTANT]
> Consulte [diretrizes de movimentação de rede](./move-limitations/networking-move-limitations.md).

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | namespaces | Sim | Sim |
> | namespaces/notificationhubs | Sim | Sim |

## <a name="microsoftobjectstore"></a>Microsoft. ObjectStore

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | osnamespaces | Sim | Sim |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | storageinsightconfigs | Não | Não |
> | workspaces | Sim | Sim |

> [!IMPORTANT]
> Certifique-se de que a mudança para a nova assinatura não exceda as [cotas de assinatura](azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | managementassociations | Não | Não |
> | managementconfigurations | Sim | Sim |
> | solutions | Sim | Sim |
> | Modos de exibição | Sim | Sim |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | emparelhamentos | Sim | Sim |
> | peeringservices | Não | Não |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | policyevents | Não | Não |
> | policystates | Não | Não |
> | policytrackedresources | Não | Não |
> | remediations | Não | Não |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | dashboards | Sim | Sim |

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | rootresources | Não | Não |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | workspacecollections | Sim | Sim |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | capacities | Sim | Sim |

## <a name="microsoftprojectbabylon"></a>Microsoft. ProjectBabylon

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |

## <a name="microsoftproviderhub"></a>Microsoft. ProviderHub

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | rollouts | Não | Não |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | backupprotecteditems | Não | Não |
> | replicationeligibilityresults | Não | Não |
> | vaults | Sim | Sim |

> [!IMPORTANT]
> Consulte [diretrizes de movimentação dos serviços de recuperação](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | namespaces | Sim | Sim |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | consultas | Sim | Sim |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | availabilitystatuses | Não | Não |
> | childavailabilitystatuses | Não | Não |
> | childresources | Não | Não |
> | events | Não | Não |
> | Notificações | Não | Não |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | deploymentscripts | Não | Não |
> | links | Não | Não |
> | marcas | Não | Não |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | aplicativos | Sim | Não |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | jobcollections | Sim | Sim |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | searchservices | Sim | Sim |

> [!IMPORTANT]
> Você não pode mover vários recursos de pesquisa em regiões diferentes em uma única operação. Em vez disso, mova-os em operações separadas.

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | adaptivenetworkhardenings | Não | Não |
> | advancedthreatprotectionsettings | Não | Não |
> | assessmentmetadata | Não | Não |
> | avaliações | Não | Não |
> | automações | Sim | Sim |
> | complianceresults | Não | Não |
> | conformidades | Não | Não |
> | datacollectionagents | Não | Não |
> | datacollectionresults | Não | Não |
> | devicesecuritygroups | Não | Não |
> | informationprotectionpolicies | Não | Não |
> | iotsecuritysolutions | Sim | Sim |
> | servervulnerabilityassessments | Não | Não |

## <a name="microsoftsecurityinsights"></a>Microsoft. SecurityInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | agregações | Não | Não |
> | alertrules | Não | Não |
> | alertruletemplates | Não | Não |
> | indicadores | Não | Não |
> | cases | Não | Não |
> | dataconnecters | Não | Não |
> | entidades | Não | Não |
> | entityqueries | Não | Não |
> | officeconsents | Não | Não |
> | configurações | Não | Não |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | gateways | Não | Não |
> | nós | Não | Não |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | namespaces | Sim | Sim |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | aplicativos | Não | Não |
> | clusters | Sim | Sim |
> | clusters/aplicativos | Não | Não |
> | containergroups | Não | Não |
> | containergroupsets | Não | Não |
> | edgeclusters | Não | Não |
> | networks | Não | Não |
> | secretstores | Não | Não |
> | volumes | Não | Não |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | aplicativos | Sim | Sim |
> | gateways | Sim | Sim |
> | networks | Sim | Sim |
> | segredos | Sim | Sim |
> | volumes | Sim | Sim |

## <a name="microsoftservices"></a>Microsoft. Services

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | rollouts | Não | Não |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | signalr | Sim | Sim |

## <a name="microsoftsoftwareplan"></a>Microsoft. SoftwarePlan

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | hybridusebenefits | Não | Não |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | applicationdefinitions | Não | Não |
> | aplicativos | Não | Não |
> | jitrequests | Não | Não |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | instancepools | Não | Não |
> | managedinstances | Não | Não |
> | ManagedInstances/bancos de dados | Não | Não |
> | servidores | Sim | Sim |
> | servidores/bancos de dados | Sim | Sim |
> | servidores/elasticpools | Sim | Sim |
> | virtualclusters | Sim | Sim |

> [!IMPORTANT]
> Um banco de dados e um servidor devem estar no mesmo grupo de recursos. Quando você move um SQL Server, todos os seus bancos de dados também são movidos. Este comportamento se aplica ao Banco de Dados SQL do Azure e ao banco de dados SQL Data Warehouse do Azure.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | sqlvirtualmachinegroups | Sim | Sim |
> | sqlvirtualmachines | Sim | Sim |

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | dwvm | Não | Não |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | storageaccounts | Sim | Sim |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Sim | Sim |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Não | Não |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Não | Não |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | managers | Não | Não |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | streamingjobs | Sim | Sim |

> [!IMPORTANT]
> Stream Analytics trabalhos não podem ser movidos quando estiver em estado de execução.

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | environments | Não | Não |
> | ambientes/EventSources | Não | Não |
> | instâncias | Não | Não |
> | instâncias/ambientes | Não | Não |
> | instâncias/ambientes/EventSources | Não | Não |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | CreateSubscription | Não | Não |

## <a name="microsoftsupport"></a>microsoft.support

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | createsupportticket | Não | Não |
> | supporttickets | Não | Não |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | providerregistrations | Não | Não |
> | recursos | Não | Não |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | environments | Sim | Sim |
> | ambientes/EventSources | Sim | Sim |
> | ambientes/referencedatasets | Sim | Sim |

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | stores | Sim | Sim |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | account | Não | Não |
> | conta/extensão | Não | Não |
> | conta/projeto | Não | Não |

> [!IMPORTANT]
> Para alterar a assinatura do Azure DevOps, consulte [alterar a assinatura do Azure usada para cobrança](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | dedicatedcloudnodes | Não | Não |
> | dedicatedcloudservices | Não | Não |
> | virtualmachines | Não | Não |

## <a name="microsoftvsonline"></a>Microsoft. VSOnline

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Sim | Sim |
> | planos | Sim | Sim |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | certificates | Não | Sim |
> | connectiongateways | Sim | Sim |
> | connections | Sim | Sim |
> | customapis | Sim | Sim |
> | hostingenvironments | Não | Não |
> | serverfarms | Sim | Sim |
> | sites | Sim | Sim |
> | sites/premieraddons | Sim | Sim |
> | sites/Slots | Sim | Sim |
> | staticsites | Não | Não |

> [!IMPORTANT]
> Consulte [diretrizes de movimentação do serviço de aplicativo](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | deviceservices | Não | Não |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscription |
> | ------------- | ----------- | ---------- |
> | components | Não | Não |
> | monitorinstances | Não | Não |
> | monitors | Não | Não |
> | notificationsettings | Não | Não |

## <a name="third-party-services"></a>Serviços de terceiros

No momento, serviços de terceiros não dão suporte à operação de movimentação.

## <a name="next-steps"></a>Próximos passos
Para ver comandos para mover recursos, confira [Move resources to new resource group or subscription](move-resource-group-and-subscription.md) (Mover recursos para o novo grupo de recursos ou assinatura).

Para obter os mesmos dados como um arquivo de valores separados por vírgula, baixe [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv).
