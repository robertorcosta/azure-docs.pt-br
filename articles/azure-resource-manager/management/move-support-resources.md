---
title: Mover suporte de operação por tipo de recurso
description: Lista os tipos de recursos do Azure que podem ser movidos para um novo grupo de recursos ou assinatura.
ms.topic: conceptual
ms.date: 03/17/2020
ms.openlocfilehash: 2250283136608161956716abadb63b9f706bf581
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460408"
---
# <a name="move-operation-support-for-resources"></a>Mover o suporte de operação para recursos
Este artigo lista se um tipo de recurso do Azure é compatível com a operação de movimentação. Ele também fornece informações sobre condições especiais a serem consideradas ao mover um recurso.

Pule para um namespace do provedor de recursos:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [microsoft.aadiam](#microsoftaadiam)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppPlatform](#microsoftappplatform)
> - [Microsoft.AppService](#microsoftappservice)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.BatchAI](#microsoftbatchai)
> - [Microsoft.Billing](#microsoftbilling)
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
> - [Microsoft.Consumption](#microsoftconsumption)
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
> - [Microsoft.DataProtection](#microsoftdataprotection)
> - [Microsoft.DataShare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft.DevOps](#microsoftdevops)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DigitalTwins](#microsoftdigitaltwins)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Falcon](#microsoftfalcon)
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
> - [Microsoft.ManagedServices](#microsoftmanagedservices)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft.Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft.ObjectStore](#microsoftobjectstore)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PortalSdk](#microsoftportalsdk)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.ProjectBabylon](#microsoftprojectbabylon)
> - [Microsoft.ProjectOxford](#microsoftprojectoxford)
> - [Microsoft.ProviderHub](#microsoftproviderhub)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.Services](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SoftwarePlan](#microsoftsoftwareplan)
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
> - [Microsoft.Subscription](#microsoftsubscription)
> - [microsoft.support](#microsoftsupport)
> - [Microsoft.TerraformOSS](#microsoftterraformoss)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.Token](#microsofttoken)
> - [microsoft.visualstudio](#microsoftvisualstudio)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.VSOnline](#microsoftvsonline)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | domainservices | Não | Não |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | tenants | Não | Não |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | configurações | Não | Não |
> | filmes | Não | Não |
> | suppressions | Não | Não |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | regras de ação | Sim | Sim |
> | alertas | Não | Não |
> | alerta | Não | Não |
> | smartdetectoralertrules | Sim | Sim |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | servers | Sim | Sim |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | serviço | Sim | Sim |

> [!IMPORTANT]
> Um serviço de gerenciamento de API definido como SKU de consumo não pode ser movido.

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | configuraçõeslojas | Sim | Sim |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | Primavera | Sim | Sim |

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | apiapps | Não | Não |
> | appidentities | Não | Não |
> | gateways | Não | Não |

> [!IMPORTANT]
> Consulte [a orientação de movimentação do App Service](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | Checkaccess | Não | Não |
> | negações | Não | Não |
> | findorphanroleassignments | Não | Não |
> | locks | Não | Não |
> | permissões | Não | Não |
> | policyassignments | Não | Não |
> | Policydefinitions | Não | Não |
> | definições de policyset | Não | Não |
> | atribuições de papéis | Não | Não |
> | roleassignmentsusemetrics | Não | Não |
> | roledefinições | Não | Não |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | automationaccounts | Sim | Sim |
> | contas/configurações de automação | Sim | Sim |
> | contas de automação / runbooks | Sim | Sim |

> [!IMPORTANT]
> Os runbooks devem existir no mesmo grupo de recursos da Conta de Automação.

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | b2cdirectories | Sim | Sim |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | híbridosgestores de dados | Não | Não |
> | postgresinstances | Não | Não |
> | sqlinstances | Não | Não |
> | Sqlserverregistrations | Sim | Sim |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | registrations | Sim | Sim |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | batchaccounts | Sim | Sim |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | Não | Não |
> | fileservers | Não | Não |
> | jobs | Não | Não |
> | workspaces | Não | Não |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | períodos de faturamento | Não | Não |
> | permissões de faturamento | Não | Não |
> | billingroletribuições | Não | Não |
> | billingroledefinições | Não | Não |
> | criaçãodedesignroleassignment | Não | Não |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | mapapis | Não | Não |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | biztalk | Não | Não |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | blockchainmembers | Não | Não |
> | Observadores | Não | Não |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | blueprintassignments | Não | Não |
> | blueprints | Não | Não |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | botservices | Sim | Sim |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | redis | Sim | Sim |

> [!IMPORTANT]
> Se a ocorrência do Cache do Azure para Redis for configurada com uma rede virtual, a instância não poderá ser movida para uma assinatura diferente. Consulte [as limitações de movimento de rede](./move-limitations/networking-move-limitations.md).

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | cdnwebapplicationpolicies | Sim | Sim |
> | perfis | Sim | Sim |
> | perfis / pontos finais | Sim | Sim |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | certificateorders | Sim | Sim |

> [!IMPORTANT]
> Consulte [a orientação de movimentação do App Service](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | domainnames | Sim | Não |
> | virtualmachines | Sim | Não |

> [!IMPORTANT]
> Consulte [a orientação de movimento de implantação clássica](./move-limitations/classic-model-move-limitations.md). Os recursos clássicos de implantação podem ser movidos através de assinaturas com uma operação específica para esse cenário.

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | networksecuritygroups | Não | Não |
> | reservedips | Não | Não |
> | virtualnetworks | Não | Não |

> [!IMPORTANT]
> Consulte [a orientação de movimento de implantação clássica](./move-limitations/classic-model-move-limitations.md). Os recursos clássicos de implantação podem ser movidos através de assinaturas com uma operação específica para esse cenário.

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storageaccounts | Sim | Não |

> [!IMPORTANT]
> Consulte [a orientação de movimento de implantação clássica](./move-limitations/classic-model-move-limitations.md). Os recursos clássicos de implantação podem ser movidos através de assinaturas com uma operação específica para esse cenário.

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Sim | Sim |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | availabilitysets | Sim | Sim |
> | conjuntos de criptografia de disco | Não | Não |
> | disks | Sim | Sim |
> | galleries | Não | Não |
> | galerias / imagens | Não | Não |
> | galerias / imagens / versões | Não | Não |
> | grupos de host | Não | Não |
> | hostgroups / hosts | Não | Não |
> | images | Sim | Sim |
> | proximityplacementgroups | Sim | Sim |
> | restorepointcollections | Não | Não |
> | sharedvmimages | Não | Não |
> | sharedvmimages / versões | Não | Não |
> | snapshots | Sim | Sim |
> | chaves públicas ssh | Não | Não |
> | virtualmachines | Sim | Sim |
> | máquinas virtuais / extensões | Sim | Sim |
> | virtualmachinescalesets | Sim | Sim |

> [!IMPORTANT]
> Veja [as máquinas virtuais moverem orientações](./move-limitations/virtual-machines-move-limitations.md).

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | custo agregado | Não | Não |
> | Saldos | Não | Não |
> | Orçamentos | Não | Não |
> | Acusações | Não | Não |
> | costtags | Não | Não |
> | credits | Não | Não |
> | events | Não | Não |
> | Previsões | Não | Não |
> | lots | Não | Não |
> | Mercados | Não | Não |
> | resultados de operação | Não | Não |
> | estado de operação | Não | Não |
> | folhas de preços | Não | Não |
> | products | Não | Não |
> | detalhes de reservas | Não | Não |
> | recomendações de reservas | Não | Não |
> | resumos de reservas | Não | Não |
> | transações de reservas | Não | Não |
> | marcas | Não | Não |
> | tenants | Não | Não |
> | terms | Não | Não |
> | detalhes de uso | Não | Não |

## <a name="microsoftcontainer"></a>Microsoft.Container

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | containergroups | Não | Não |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | containergroups | Não | Não |
> | serviceassociationlinks | Não | Não |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | registries | Sim | Sim |
> | registros / tarefas de construção | Sim | Sim |
> | registros / replicação | Sim | Sim |
> | registros / taskruns | Sim | Sim |
> | registros / tarefas | Sim | Sim |
> | registros / webhooks | Sim | Sim |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | containerservices | Não | Não |
> | managedclusters | Não | Não |
> | openshiftmanagedclusters | Não | Não |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | de dimensionamento da Web | Não | Não |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | alertas | Não | Não |
> | Orçamentos | Não | Não |
> | conectores | Sim | Sim |
> | dimensões | Não | Não |
> | exporta | Não | Não |
> | assinaturas externas | Não | Não |
> | forecast | Não | Não |
> | Consulta | Não | Não |
> | reportconfigs | Não | Não |
> | relatórios | Não | Não |
> | regras showback | Não | Não |
> | Modos de exibição | Não | Não |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hubs | Não | Não |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | associações | Não | Não |
> | provedores de recursos | Sim | Sim |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | jobs | Não | Não |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | databoxedgedevices | Não | Não |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | workspaces | Não | Não |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | catalogs | Sim | Sim |
> | catálogos de dados | Não | Não |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | connectionmanagers | Não | Não |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | pacotes | Não | Não |
> | planos | Não | Não |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | datafactories | Sim | Sim |
> | factories | Sim | Sim |

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | datalakeaccounts | Não | Não |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Sim | Sim |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Sim | Sim |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | services | Não | Não |
> | serviços/projetos | Não | Não |
> | slots | Não | Não |

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | cofres de backup | Não | Não |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Sim | Sim |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | servers | Sim | Sim |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | servers | Sim | Sim |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | servergroups | Não | Não |
> | servers | Sim | Sim |
> | servidoresv2 | Sim | Sim |
> | servidores únicos | Sim | Sim |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | artifactsources | Sim | Sim |
> | rollouts | Sim | Sim |
> | servicetopologies | Sim | Sim |
> | serviços/serviços | Sim | Sim |
> | serviços/serviços/ unidades de serviços | Sim | Sim |
> | etapas | Sim | Sim |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | elasticpools | Não | Não |
> | elásticos / iothubtenants | Não | Não |
> | iothubs | Sim | Sim |
> | provisioningservices | Sim | Sim |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | Pipelines | Sim | Sim |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | controladores | Sim | Sim |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | labcenters | Não | Não |
> | labs | Sim | Não |
> | laboratórios / ambientes | Sim | Sim |
> | laboratórios / corredores de serviços | Sim | Sim |
> | laboratórios / máquinas virtuais | Sim | Não |
> | schedules | Sim | Sim |

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | digitaltwinsinstances | Não | Não |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | databaseaccounts | Sim | Sim |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | domains | Sim | Sim |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | services | Sim | Sim |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | domains | Sim | Sim |
> | eventSubscriptions | Não - não pode ser movido independentemente, mas movido automaticamente com recurso subscrito. | Não - não pode ser movido independentemente, mas movido automaticamente com recurso subscrito. |
> | assinaturas de eventos | Não - não pode ser movido independentemente, mas movido automaticamente com recurso subscrito. | Não - não pode ser movido independentemente, mas movido automaticamente com recurso subscrito. |
> | tópicos de extensão | Não | Não |
> | espaços de nome de parceiros | Sim | Sim |
> | parceirostópicos | Sim | Sim |
> | tópicos sistemas | Sim | Sim |
> | topics | Sim | Sim |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | Sim | Sim |
> | namespaces | Sim | Sim |

## <a name="microsoftfalcon"></a>Microsoft.Falcon

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | namespaces | Sim | Sim |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | opções de configuração de hóspedes | Não | Não |
> | software | Não | Não |
> | perfil de atualização de software | Não | Não |
> | atualizações de software | Não | Não |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hanainstances | Não | Não |
> | sapmonitors | Sim | Sim |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | Sim | Sim |

> [!IMPORTANT]
> Você pode mover os clusters HDInsight para uma nova assinatura ou grupo de recursos. No entanto, não é possível mover os recursos de rede vinculados ao cluster HDInsight (por exemplo, a rede virtual, NIC ou balanceador de carga) entre assinaturas. Além disso, não é possível mover uma para um novo grupo de recursos uma NIC que está conectada a uma máquina virtual para o cluster.
>
> Ao mover um cluster HDInsight para uma nova assinatura, mova primeiro os outros recursos (como a conta de armazenamento). Em seguida, mova apenas o cluster HDInsight.

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | services | Sim | Sim |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | Máquinas | Sim | Sim |
> | máquinas / extensões | Não | Não |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | datamanagers | Sim | Sim |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | jobs | Sim | Sim |

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | actiongroups | Sim | Sim |
> | activitylogalerts | Não | Não |
> | alertrules | Sim | Sim |
> | autoscalesettings | Sim | Sim |
> | baseline | Não | Não |
> | calculatebaseline | Não | Não |
> | components | Sim | Sim |
> | Diagnosticsettings | Não | Não |
> | diagnósticoscategorias | Não | Não |
> | eventtypes | Não | Não |
> | configurações de diagnóstico estendidas | Não | Não |
> | definições de log | Não | Não |
> | logs | Não | Não |
> | metricalerts | Não | Não |
> | linhas de base métricas | Não | Não |
> | definições métricas | Não | Não |
> | métricanamespaces | Não | Não |
> | Métricas | Não | Não |
> | myworkbooks | Não | Não |
> | privatelinkscopes | Sim | Sim |
> | scheduledqueryrules | Sim | Sim |
> | Topologia | Não | Não |
> | transações | Não | Não |
> | vminsightsonboardingstatuss | Não | Não |
> | webtests | Sim | Sim |
> | workbooks | Sim | Sim |
> | modelos de carteiras de trabalho | Sim | Sim |

> [!IMPORTANT]
> Certifique-se de que a mudança para nova assinatura não exceda [as cotas de assinatura](azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | iotapps | Sim | Sim |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Sim | Sim |
> | grafo | Sim | Sim |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | vaults | Sim | Sim |

> [!IMPORTANT]
> Os Cofres-chave usados para criptografia de disco não podem ser movidos para um grupo de recursos na mesma assinatura ou entre assinaturas.

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | Sim | Sim |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | labaccounts | Não | Não |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hostingenvironments | Não | Não |
> | integrationaccounts | Sim | Sim |
> | integrationserviceenvironments | Sim | Não |
> | ambientes de integração de serviços / managedapis | Sim | Não |
> | isolatedenvironments | Não | Não |
> | workflows | Sim | Sim |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | commitmentplans | Sim | Sim |
> | webservices | Sim | Não |
> | workspaces | Sim | Sim |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | operationalizationclusters | Não | Não |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |
> | contas / espaços de trabalho | Não | Não |
> | contas / espaços de trabalho / projetos | Não | Não |
> | teamaccounts | Não | Não |
> | contas de equipe / espaços de trabalho | Não | Não |
> | contas de equipe / espaços de trabalho / projetos | Não | Não |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hostingaccounts | Não | Não |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | workspaces | Não | Não |
> | espaços de trabalho / computação | Não | Não |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | identidades | Não | Não |
> | userassignedidentities | Não | Não |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | atribuições de registro | Não | Não |
> | definições de registro | Não | Não |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Sim | Sim |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | classicdevservices | Não | Não |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | mediaservices | Sim | Sim |
> | mediaservices / liveevents | Sim | Sim |
> | mediaservices / streamingendpoints | Sim | Sim |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | appclusters | Não | Não |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | assessmentprojects | Sim | Sim |
> | migrateprojects | Sim | Sim |
> | projects | Não | Não |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | netappaccounts | Não | Não |
> | netappaccounts / backuppolicies | Não | Não |
> | netappaccounts / capacitypools | Não | Não |
> | netappaccounts / capacitypools / volumes | Não | Não |
> | netappaccounts / capacitypools / volumes / alvos de montagem | Não | Não |
> | netappaccounts / capacitypools / volumes / snapshots | Não | Não |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | applicationgateways | Não | Não |
> | applicationgatewaywebapplicationpoliciesdefirewall | Não | Não |
> | applicationsecuritygroups | Sim | Sim |
> | azurefirewalls | Sim | Sim |
> | bastionhosts | Não | Não |
> | connections | Sim | Sim |
> | ddoscustompolicies | Sim | Sim |
> | ddosprotectionplans | Não | Não |
> | dnszones | Sim | Sim |
> | expressroutecircuits | Não | Não |
> | expressroutegateways | Não | Não |
> | políticas de firewall | Sim | Sim |
> | frontdoors | Não | Não |
> | frontdoorwebapplicationfirewallpolicies | Não | Não |
> | ipgroups | Sim | Sim |
> | loadbalancers | Sim - SKU básico<br>Não - Padrão SKU | Sim - SKU básico<br>Não - Padrão SKU |
> | localnetworkgateways | Sim | Sim |
> | natgateways | Sim | Sim |
> | perfis de experimentos em rede | Sim | Sim |
> | networkintentpolicies | Sim | Sim |
> | networkinterfaces | Sim | Sim |
> | networkprofiles | Não | Não |
> | networksecuritygroups | Sim | Sim |
> | networkwatchers | Sim | Não |
> | observadores de rede / monitores de conexão | Sim | Não |
> | observadores de rede / fluxos | Sim | Não |
> | observadores de rede / pingmeshes | Sim | Não |
> | p2svpngateways | Não | Não |
> | privatednszones | Sim | Sim |
> | privatednszones / virtualnetworklinks | Sim | Sim |
> | privateendpointredirectmaps | Não | Não |
> | privateendpoints | Sim | Sim |
> | privatelinkservices | Não | Não |
> | publicipaddresses | Sim - SKU básico<br>Não - Padrão SKU | Sim - SKU básico<br>Não - Padrão SKU |
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
> | configurações de vpnserver | Não | Não |
> | vpnsites (WAN virtual) | Não | Não |
> | webapplicationfirewallpolicies | Sim | Sim |

> [!IMPORTANT]
> Consulte [a orientação de movimento de rede](./move-limitations/networking-move-limitations.md).

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | namespaces | Sim | Sim |
> | namespaces / notificationhubs | Sim | Sim |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | osnamespaces | Sim | Sim |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storageinsightconfigs | Não | Não |
> | workspaces | Sim | Sim |

> [!IMPORTANT]
> Certifique-se de que a mudança para nova assinatura não exceda [as cotas de assinatura](azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | managementassociations | Não | Não |
> | managementconfigurations | Sim | Sim |
> | solutions | Sim | Sim |
> | Modos de exibição | Sim | Sim |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | peerings | Sim | Sim |
> | serviços de peering | Não | Não |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | eventos políticos | Não | Não |
> | estados políticos | Não | Não |
> | políticadesviadorecursos | Não | Não |
> | remediations | Não | Não |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | dashboards | Sim | Sim |

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | rootresources | Não | Não |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | workspacecollections | Sim | Sim |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | capacities | Sim | Sim |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | rollouts | Não | Não |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | itens protegidos de backup | Não | Não |
> | resultados de elegibilidade de replicação | Não | Não |
> | vaults | Sim | Sim |

> [!IMPORTANT]
> Consulte [a orientação de movimentação dos Serviços de Recuperação](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | namespaces | Sim | Sim |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | consultas | Sim | Sim |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | disponibilidadestatuss | Não | Não |
> | status de disponibilidade infantil | Não | Não |
> | recursos infantis | Não | Não |
> | events | Não | Não |
> | Notificações | Não | Não |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | implantaçõesscripts | Não | Não |
> | links | Não | Não |
> | marcas | Não | Não |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | de dimensionamento da Web | Sim | Não |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | searchservices | Sim | Sim |

> [!IMPORTANT]
> Você não pode mover vários recursos de Pesquisa em diferentes regiões em uma operação. Em vez disso, mova-os em operações separadas.

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hardenings adaptativos | Não | Não |
> | configurações avançadasdeproteção contra ameaças | Não | Não |
> | avaliaçãometadata | Não | Não |
> | avaliações | Não | Não |
> | Automações | Sim | Sim |
> | resultados de conformidade | Não | Não |
> | Conformidades | Não | Não |
> | agentes de coleta de dados | Não | Não |
> | grupos de segurança de dispositivos | Não | Não |
> | políticas de proteção de informações | Não | Não |
> | iotsecuritysolutions | Sim | Sim |
> | avaliações de vulnerabilidade sustais | Não | Não |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | agregações | Não | Não |
> | alertrules | Não | Não |
> | modelos de regras de alerta | Não | Não |
> | indicadores | Não | Não |
> | cases | Não | Não |
> | conectores de dados | Não | Não |
> | requisitos de verificação de dataconnectors | Não | Não |
> | entidades | Não | Não |
> | entityqueries | Não | Não |
> | Incidentes | Não | Não |
> | consentimentos escritório | Não | Não |
> | configurações | Não | Não |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | gateways | Não | Não |
> | nós | Não | Não |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | namespaces | Sim | Sim |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | de dimensionamento da Web | Não | Não |
> | clusters | Sim | Sim |
> | clusters/aplicações | Não | Não |
> | containergroups | Não | Não |
> | containergroupsets | Não | Não |
> | edgeclusters | Não | Não |
> | managedclusters | Não | Não |
> | networks | Não | Não |
> | secretstores | Não | Não |
> | volumes | Não | Não |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | de dimensionamento da Web | Sim | Sim |
> | gateways | Sim | Sim |
> | networks | Sim | Sim |
> | segredos | Sim | Sim |
> | volumes | Sim | Sim |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | rollouts | Não | Não |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | signalr | Sim | Sim |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | híbridosusebenefícios | Não | Não |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | applicationdefinitions | Não | Não |
> | de dimensionamento da Web | Não | Não |
> | jitrequests | Não | Não |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | piscinas de instâncias | Não | Não |
> | managedinstances | Não | Não |
> | instâncias/bancos de dados gerenciados | Não | Não |
> | servers | Sim | Sim |
> | servidores /bancos de dados | Sim | Sim |
> | servidores / elásticos | Sim | Sim |
> | servidores / contas de trabalho | Sim | Sim |
> | servidores / agentes de trabalho | Sim | Sim |
> | virtualclusters | Sim | Sim |

> [!IMPORTANT]
> Um banco de dados e um servidor devem estar no mesmo grupo de recursos. Quando você move um SQL Server, todos os seus bancos de dados também são movidos. Este comportamento se aplica ao Banco de Dados SQL do Azure e ao banco de dados SQL Data Warehouse do Azure.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | sqlvirtualmachinegroups | Sim | Sim |
> | sqlvirtualmachines | Sim | Sim |

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | dwvm | Não | Não |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storageaccounts | Sim | Sim |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Sim | Sim |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Não | Não |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Não | Não |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | managers | Não | Não |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | streamingjobs | Sim | Sim |

> [!IMPORTANT]
> Os trabalhos do Stream Analytics não podem ser movidos quando estão em estado de execução.

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | environments | Não | Não |
> | ambientes / fontes de eventos | Não | Não |
> | instances | Não | Não |
> | instâncias / ambientes | Não | Não |
> | casos / ambientes / fontes de eventos | Não | Não |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | criar assinatura | Não | Não |

## <a name="microsoftsupport"></a>microsoft.support

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | supporttickets | Não | Não |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | providerregistrations | Não | Não |
> | recursos | Não | Não |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | environments | Sim | Sim |
> | ambientes / fontes de eventos | Sim | Sim |
> | ambientes / conjuntos de dados de referência | Sim | Sim |

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | stores | Sim | Sim |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | account | Não | Não |
> | conta/extensão | Sim | Sim |
> | conta/ projeto | Sim | Sim |

> [!IMPORTANT]
> Para alterar a assinatura do Azure DevOps, consulte [alterar a assinatura do Azure usada para faturamento](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | dedicadoscloudnodes | Não | Não |
> | serviços dedicados à nuvem | Não | Não |
> | virtualmachines | Não | Não |

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Sim | Sim |
> | planos | Sim | Sim |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | certificates | Não | Sim |
> | connectiongateways | Sim | Sim |
> | connections | Sim | Sim |
> | customapis | Sim | Sim |
> | hostingenvironments | Não | Não |
> | serverfarms | Sim | Sim |
> | sites | Sim | Sim |
> | sites / premieraddons | Sim | Sim |
> | sites / slots | Sim | Sim |
> | estáticos | Não | Não |

> [!IMPORTANT]
> Consulte [a orientação de movimentação do App Service](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | deviceservices | Não | Não |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | components | Não | Não |
> | monitoresinstâncias | Não | Não |
> | monitors | Não | Não |
> | configurações de notificação | Não | Não |

## <a name="third-party-services"></a>Serviços de terceiros

No momento, serviços de terceiros não dão suporte à operação de movimentação.

## <a name="next-steps"></a>Próximas etapas
Para ver comandos para mover recursos, confira [Move resources to new resource group or subscription](move-resource-group-and-subscription.md) (Mover recursos para o novo grupo de recursos ou assinatura).

Para obter os mesmos dados como um arquivo de valores separados por vírgula, baixe [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv).
