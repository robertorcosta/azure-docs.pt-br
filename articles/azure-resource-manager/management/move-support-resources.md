---
title: Mover suporte de operação por tipo de recurso
description: Lista os tipos de recursos do Azure que podem ser movidos para um novo grupo de recursos ou assinatura.
ms.topic: conceptual
ms.date: 05/21/2020
ms.openlocfilehash: 4f2ed7f1cb24b9896b533fb5d18ac4e57db48e2c
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/22/2020
ms.locfileid: "83780322"
---
# <a name="move-operation-support-for-resources"></a>Mover o suporte de operação para recursos

Este artigo lista se um tipo de recurso do Azure é compatível com a operação de movimentação. Ele também fornece informações sobre condições especiais a serem consideradas ao mover um recurso.

Pule para um namespace de provedor de recursos:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [microsoft.aadiam](#microsoftaadiam)
> - [Microsoft.Addons](#microsoftaddons)
> - [Microsoft.ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppPlatform](#microsoftappplatform)
> - [Microsoft.AppService](#microsoftappservice)
> - [Microsoft.Attestation](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AVS](#microsoftavs)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.AzureStackHCI](#microsoftazurestackhci)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.BatchAI](#microsoftbatchai)
> - [Microsoft.Billing](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.BlockchainTokens](#microsoftblockchaintokens)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Capacity](#microsoftcapacity)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ChangeAnalysis](#microsoftchangeanalysis)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.ClassicSubscription](#microsoftclassicsubscription)
> - [Microsoft.Cognition](#microsoftcognition)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Consumption](#microsoftconsumption)
> - [Microsoft.Container](#microsoftcontainer)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.ContentModerator](#microsoftcontentmoderator)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CostManagementExports](#microsoftcostmanagementexports)
> - [Microsoft.CustomerInsights](#microsoftcustomerinsights)
> - [Microsoft.CustomerLockbox](#microsoftcustomerlockbox)
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
> - [Microsoft.DesktopVirtualization](#microsoftdesktopvirtualization)
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
> - [Microsoft.Experimentation](#microsoftexperimentation)
> - [Microsoft.Falcon](#microsoftfalcon)
> - [Microsoft.Features](#microsoftfeatures)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.Hydra](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [microsoft.insights](#microsoftinsights)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kubernetes](#microsoftkubernetes)
> - [Microsoft.KubernetesConfiguration](#microsoftkubernetesconfiguration)
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
> - [Microsoft.Maintenance](#microsoftmaintenance)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.ManagedNetwork](#microsoftmanagednetwork)
> - [Microsoft.ManagedServices](#microsoftmanagedservices)
> - [Microsoft.Management](#microsoftmanagement)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.Marketplace](#microsoftmarketplace)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.MarketplaceOrdering](#microsoftmarketplaceordering)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft.Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.MixedReality](#microsoftmixedreality)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft.ObjectStore](#microsoftobjectstore)
> - [Microsoft.OffAzure](#microsoftoffazure)
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
> - [Microsoft.Quantum](#microsoftquantum)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.RedHatOpenShift](#microsoftredhatopenshift)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.SerialConsole](#microsoftserialconsole)
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
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Microsoft.Subscription](#microsoftsubscription)
> - [microsoft.support](#microsoftsupport)
> - [Microsoft.Synapse](#microsoftsynapse)
> - [Microsoft.TerraformOSS](#microsoftterraformoss)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.Token](#microsofttoken)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [microsoft.visualstudio](#microsoftvisualstudio)
> - [Microsoft.VMware](#microsoftvmware)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.VnfManager](#microsoftvnfmanager)
> - [Microsoft.VSOnline](#microsoftvsonline)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsESU](#microsoftwindowsesu)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | domainservices | Não | Não |
> | domainservices/oucontainer | Não | Não |
> | Locais | Não | Não |
> | locations/operationresults | Não | Não |
> | operações | Não | Não |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | privatelinkforazuread | Não | Não |
> | tenants | Não | Não |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | operationresults | Não | Não |
> | operações | Não | Não |
> | supportproviders | Não | Não |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | aadsupportcases | Não | Não |
> | addsservices | Não | Não |
> | agentes | Não | Não |
> | anonymousapiusers | Não | Não |
> | configuração | Não | Não |
> | logs | Não | Não |
> | operações | Não | Não |
> | relatórios | Não | Não |
> | servicehealthmetrics | Não | Não |
> | services | Não | Não |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | configurações | Não | Não |
> | generaterecommendations | Não | Não |
> | metadata | Não | Não |
> | operações | Não | Não |
> | filmes | Não | Não |
> | suppressions | Não | Não |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | actionrules | Sim | Sim |
> | alertas | Não | Não |
> | alertslist | Não | Não |
> | alertsmetadata | Não | Não |
> | alertssummary | Não | Não |
> | alertssummarylist | Não | Não |
> | operações | Não | Não |
> | smartdetectoralertrules | Sim | Sim |
> | smartgroups | Não | Não |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | Locais | Não | Não |
> | locations/checknameavailability | Não | Não |
> | locations/operationresults | Não | Não |
> | locations/operationstatuses | Não | Não |
> | operações | Não | Não |
> | servers | Sim | Sim |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checkfeedbackrequired | Não | Não |
> | checknameavailability | Não | Não |
> | checkservicenameavailability | Não | Não |
> | operações | Não | Não |
> | reportfeedback | Não | Não |
> | serviço | Sim | Sim |
> | validateservicename | Não | Não |

> [!IMPORTANT]
> Um serviço de Gerenciamento de API definido para o SKU de consumo não pode ser movido.

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Não | Não |
> | configurationstores | Sim | Sim |
> | configurationstores/eventgridfilters | Não | Não |
> | Locais | Não | Não |
> | locations/operationsstatus | Não | Não |
> | operações | Não | Não |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | Locais | Não | Não |
> | locations/checknameavailability | Não | Não |
> | locations/operationresults | Não | Não |
> | locations/operationstatus | Não | Não |
> | operações | Não | Não |
> | spring | Sim | Sim |

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | apiapps | Não | Não |
> | appidentities | Não | Não |
> | gateways | Não | Não |

> [!IMPORTANT]
> Consulte [Diretrizes de movimentação do Serviço de Aplicativo](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | attestationproviders | Sim | Sim |
> | operações | Não | Não |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checkaccess | Não | Não |
> | classicadministrators | Não | Não |
> | dataaliases | Não | Não |
> | denyassignments | Não | Não |
> | elevateaccess | Não | Não |
> | findorphanroleassignments | Não | Não |
> | locks | Não | Não |
> | operações | Não | Não |
> | permissões | Não | Não |
> | policyassignments | Não | Não |
> | policydefinitions | Não | Não |
> | policysetdefinitions | Não | Não |
> | provideroperations | Não | Não |
> | roleassignments | Não | Não |
> | roleassignmentsusagemetrics | Não | Não |
> | roledefinitions | Não | Não |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | automationaccounts | Sim | Sim |
> | automationaccounts/configurations | Sim | Sim |
> | automationaccounts/jobs | Não | Não |
> | automationaccounts/privateendpointconnectionproxies | Não | Não |
> | automationaccounts/privateendpointconnections | Não | Não |
> | automationaccounts/privatelinkresources | Não | Não |
> | automationaccounts/runbooks | Sim | Sim |
> | automationaccounts/softwareupdateconfigurations | Não | Não |
> | automationaccounts/webhooks | Não | Não |
> | operações | Não | Não |

> [!IMPORTANT]
> Os runbooks devem existir no mesmo grupo de recursos que a conta de automação.

## <a name="microsoftavs"></a>Microsoft.AVS

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | Locais | Não | Não |
> | locations/checkquotaavailability | Não | Não |
> | operações | Não | Não |
> | privateclouds | Sim | Sim |
> | privateclouds/clusters | Não | Não |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | b2cdirectories | Sim | Sim |
> | b2ctenants | Não | Não |
> | checknameavailability | Não | Não |
> | operações | Não | Não |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | datacontrollers | Não | Não |
> | hybriddatamanagers | Não | Não |
> | operações | Não | Não |
> | postgresinstances | Não | Não |
> | sqlinstances | Não | Não |
> | sqlmanagedinstances | Não | Não |
> | sqlserverinstances | Não | Não |
> | sqlserverregistrations | Sim | Sim |
> | sqlserverregistrations/sqlservers | Não | Não |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | cloudmanifestfiles | Não | Não |
> | operações | Não | Não |
> | registrations | Sim | Sim |
> | registrations/customersubscriptions | Não | Não |
> | registrations/products | Não | Não |

## <a name="microsoftazurestackhci"></a>Microsoft.AzureStackHCI

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | Não | Não |
> | operações | Não | Não |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | batchaccounts | Sim | Sim |
> | Locais | Não | Não |
> | locations/accountoperationresults | Não | Não |
> | locations/checknameavailability | Não | Não |
> | locations/quotas | Não | Não |
> | operações | Não | Não |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | Não | Não |
> | fileservers | Não | Não |
> | jobs | Não | Não |
> | operações | Não | Não |
> | workspaces | Não | Não |
> | workspaces/clusters | Não | Não |
> | workspaces/experiments | Não | Não |
> | workspaces/experiments/jobs | Não | Não |
> | workspaces/fileservers | Não | Não |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | billingaccounts | Não | Não |
> | billingaccounts/agreements | Não | Não |
> | billingaccounts/billingpermissions | Não | Não |
> | billingaccounts/billingprofiles | Não | Não |
> | billingaccounts/billingprofiles/availablebalance | Não | Não |
> | billingaccounts/billingprofiles/billingpermissions | Não | Não |
> | billingaccounts/billingprofiles/billingroleassignments | Não | Não |
> | billingaccounts/billingprofiles/billingroledefinitions | Não | Não |
> | billingaccounts/billingprofiles/billingsubscriptions | Não | Não |
> | billingaccounts/billingprofiles/createbillingroleassignment | Não | Não |
> | billingaccounts/billingprofiles/customers | Não | Não |
> | billingaccounts/billingprofiles/instructions | Não | Não |
> | billingaccounts/billingprofiles/invoices | Não | Não |
> | billingaccounts/billingprofiles/invoices/pricesheet | Não | Não |
> | billingaccounts/billingprofiles/invoices/transactions | Não | Não |
> | billingaccounts/billingprofiles/invoicesections | Não | Não |
> | billingaccounts/billingprofiles/invoicesections/billingpermissions | Não | Não |
> | billingaccounts/billingprofiles/invoicesections/billingroleassignments | Não | Não |
> | billingaccounts/billingprofiles/invoicesections/billingroledefinitions | Não | Não |
> | billingaccounts/billingprofiles/invoicesections/billingsubscriptions | Não | Não |
> | billingaccounts/billingprofiles/invoicesections/createbillingroleassignment | Não | Não |
> | billingaccounts/billingprofiles/invoicesections/initiatetransfer | Não | Não |
> | billingaccounts/billingprofiles/invoicesections/products | Não | Não |
> | billingaccounts/billingprofiles/invoicesections/products/transfer | Não | Não |
> | billingaccounts/billingprofiles/invoicesections/products/updateautorenew | Não | Não |
> | billingaccounts/billingprofiles/invoicesections/transactions | Não | Não |
> | billingaccounts/billingprofiles/invoicesections/transfers | Não | Não |
> | billingaccounts/billingprofiles/patchoperations | Não | Não |
> | billingaccounts/billingprofiles/paymentmethods | Não | Não |
> | billingaccounts/billingprofiles/policies | Não | Não |
> | billingaccounts/billingprofiles/pricesheet | Não | Não |
> | billingaccounts/billingprofiles/pricesheetdownloadoperations | Não | Não |
> | billingaccounts/billingprofiles/products | Não | Não |
> | billingaccounts/billingprofiles/transactions | Não | Não |
> | billingaccounts/billingroleassignments | Não | Não |
> | billingaccounts/billingroledefinitions | Não | Não |
> | billingaccounts/billingsubscriptions | Não | Não |
> | billingaccounts/billingsubscriptions/invoices | Não | Não |
> | billingaccounts/createbillingroleassignment | Não | Não |
> | billingaccounts/createinvoicesectionoperations | Não | Não |
> | billingaccounts/customers | Não | Não |
> | billingaccounts/customers/billingpermissions | Não | Não |
> | billingaccounts/customers/billingsubscriptions | Não | Não |
> | billingaccounts/customers/initiatetransfer | Não | Não |
> | billingaccounts/customers/policies | Não | Não |
> | billingaccounts/customers/products | Não | Não |
> | billingaccounts/customers/transactions | Não | Não |
> | billingaccounts/customers/transfers | Não | Não |
> | billingaccounts/departments | Não | Não |
> | billingaccounts/enrollmentaccounts | Não | Não |
> | billingaccounts/invoices | Não | Não |
> | billingaccounts/invoicesections | Não | Não |
> | billingaccounts/invoicesections/billingsubscriptionmoveoperations | Não | Não |
> | billingaccounts/invoicesections/billingsubscriptions | Não | Não |
> | billingaccounts/invoicesections/billingsubscriptions/transfer | Não | Não |
> | billingaccounts/invoicesections/elevate | Não | Não |
> | billingaccounts/invoicesections/initiatetransfer | Não | Não |
> | billingaccounts/invoicesections/patchoperations | Não | Não |
> | billingaccounts/invoicesections/productmoveoperations | Não | Não |
> | billingaccounts/invoicesections/products | Não | Não |
> | billingaccounts/invoicesections/products/transfer | Não | Não |
> | billingaccounts/invoicesections/products/updateautorenew | Não | Não |
> | billingaccounts/invoicesections/producttransfersresults | Não | Não |
> | billingaccounts/invoicesections/transactions | Não | Não |
> | billingaccounts/invoicesections/transfers | Não | Não |
> | billingaccounts/lineofcredit | Não | Não |
> | billingaccounts/listinvoicesectionswithcreatesubscriptionpermission | Não | Não |
> | billingaccounts/operationresults | Não | Não |
> | billingaccounts/patchoperations | Não | Não |
> | billingaccounts/paymentmethods | Não | Não |
> | billingaccounts/products | Não | Não |
> | billingaccounts/transactions | Não | Não |
> | billingperiods | Não | Não |
> | billingpermissions | Não | Não |
> | billingproperty | Não | Não |
> | billingroleassignments | Não | Não |
> | billingroledefinitions | Não | Não |
> | createbillingroleassignment | Não | Não |
> | departments | Não | Não |
> | enrollmentaccounts | Não | Não |
> | invoices | Não | Não |
> | operationresults | Não | Não |
> | operações | Não | Não |
> | operationstatus | Não | Não |
> | transfers | Não | Não |
> | transfers/accepttransfer | Não | Não |
> | transfers/declinetransfer | Não | Não |
> | transfers/operationstatus | Não | Não |
> | transfers/validatetransfer | Não | Não |
> | validateaddress | Não | Não |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | listcommunicationpreference | Não | Não |
> | mapapis | Não | Não |
> | operações | Não | Não |
> | updatecommunicationpreference | Não | Não |

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
> | cordamembers | Não | Não |
> | Locais | Não | Não |
> | locations/blockchainmemberoperationresults | Não | Não |
> | locations/checknameavailability | Não | Não |
> | locations/listconsortiums | Não | Não |
> | locations/watcheroperationresults | Não | Não |
> | operações | Não | Não |
> | watchers | Não | Não |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | operações | Não | Não |
> | tokenservices | Não | Não |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | blueprintassignments | Não | Não |
> | blueprintassignments/assignmentoperations | Não | Não |
> | blueprintassignments/operations | Não | Não |
> | blueprints | Não | Não |
> | blueprints/artifacts | Não | Não |
> | blueprints/versions | Não | Não |
> | blueprints/versions/artifacts | Não | Não |
> | operações | Não | Não |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | botservices | Sim | Sim |
> | botservices/channels | Não | Não |
> | botservices/connections | Não | Não |
> | checknameavailability | Não | Não |
> | listauthserviceproviders | Não | Não |
> | operações | Não | Não |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Não | Não |
> | Locais | Não | Não |
> | locations/operationresults | Não | Não |
> | locations/operationsstatus | Não | Não |
> | operações | Não | Não |
> | redis | Sim | Sim |
> | redis/privateendpointconnections | Não | Não |
> | redis/privatelinkresources | Não | Não |
> | redisenterprise | Não | Não |

> [!IMPORTANT]
> Se a instância do Cache do Azure para Redis estiver configurada com uma rede virtual, a instância não poderá ser movida para uma assinatura diferente. Consulte [Limitações de movimentação de rede](./move-limitations/networking-move-limitations.md).

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | appliedreservations | Não | Não |
> | calculateexchange | Não | Não |
> | calculateprice | Não | Não |
> | calculatepurchaseprice | Não | Não |
> | catalogs | Não | Não |
> | checkoffers | Não | Não |
> | checkpurchasestatus | Não | Não |
> | checkscopes | Não | Não |
> | commercialreservationorders | Não | Não |
> | câmbio | Não | Não |
> | listbenefits | Não | Não |
> | operações | Não | Não |
> | placepurchaseorder | Não | Não |
> | reservationorders | Não | Não |
> | reservationorders/availablescopes | Não | Não |
> | reservationorders/calculaterefund | Não | Não |
> | reservationorders/merge | Não | Não |
> | reservationorders/reservations | Não | Não |
> | reservationorders/reservations/availablescopes | Não | Não |
> | reservationorders/reservations/revisions | Não | Não |
> | reservationorders/return | Não | Não |
> | reservationorders/split | Não | Não |
> | reservationorders/swap | Não | Não |
> | reservations | Não | Não |
> | recursos | Não | Não |
> | validatereservationorder | Não | Não |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | cdnwebapplicationfirewallmanagedrulesets | Não | Não |
> | cdnwebapplicationfirewallpolicies | Sim | Sim |
> | checknameavailability | Não | Não |
> | checkresourceusage | Não | Não |
> | edgenodes | Não | Não |
> | operationresults | Não | Não |
> | operationresults/profileresults | Não | Não |
> | operationresults/profileresults/endpointresults | Não | Não |
> | operationresults/profileresults/endpointresults/customdomainresults | Não | Não |
> | operationresults/profileresults/endpointresults/origingroupresults | Não | Não |
> | operationresults/profileresults/endpointresults/originresults | Não | Não |
> | operações | Não | Não |
> | perfis | Sim | Sim |
> | profiles/endpoints | Sim | Sim |
> | profiles/endpoints/customdomains | Não | Não |
> | profiles/endpoints/origingroups | Não | Não |
> | profiles/endpoints/origins | Não | Não |
> | validateprobe | Não | Não |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | certificateorders | Sim | Sim |
> | certificateorders/certificates | Não | Não |
> | operações | Não | Não |
> | validatecertificateregistrationinformation | Não | Não |

> [!IMPORTANT]
> Consulte [Diretrizes de movimentação do Serviço de Aplicativo](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftchangeanalysis"></a>Microsoft.ChangeAnalysis

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | operações | Não | Não |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | funcionalidades | Não | Não |
> | checkdomainnameavailability | Não | Não |
> | domainnames | Sim | Não |
> | domainnames/capabilities | Não | Não |
> | domainnames/internalloadbalancers | Não | Não |
> | domainnames/servicecertificates | Não | Não |
> | domainnames/slots | Não | Não |
> | domainnames/slots/roles | Não | Não |
> | domainnames/slots/roles/metricdefinitions | Não | Não |
> | domainnames/slots/roles/metrics | Não | Não |
> | movesubscriptionresources | Não | Não |
> | operatingsystemfamilies | Não | Não |
> | operatingsystems | Não | Não |
> | operações | Não | Não |
> | operationstatuses | Não | Não |
> | quotas | Não | Não |
> | resourcetypes | Não | Não |
> | validatesubscriptionmoveavailability | Não | Não |
> | virtualmachines | Sim | Não |
> | virtualmachines/diagnosticsettings | Não | Não |
> | virtualmachines/metricdefinitions | Não | Não |
> | virtualmachines/metrics | Não | Não |

> [!IMPORTANT]
> Consulte [Diretrizes de movimentação da implantação clássica](./move-limitations/classic-model-move-limitations.md). Os recursos da implantação clássica podem ser movidos entre assinaturas com uma operação específica para esse cenário.

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | classicinfrastructureresources | Não | Não |

> [!IMPORTANT]
> Consulte [Diretrizes de movimentação da implantação clássica](./move-limitations/classic-model-move-limitations.md). Os recursos da implantação clássica podem ser movidos entre assinaturas com uma operação específica para esse cenário.

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | funcionalidades | Não | Não |
> | expressroutecrossconnections | Não | Não |
> | expressroutecrossconnections/peerings | Não | Não |
> | gatewaysupporteddevices | Não | Não |
> | networksecuritygroups | Não | Não |
> | operações | Não | Não |
> | quotas | Não | Não |
> | reservedips | Não | Não |
> | virtualnetworks | Não | Não |
> | virtualnetworks/remotevirtualnetworkpeeringproxies | Não | Não |
> | virtualnetworks/virtualnetworkpeerings | Não | Não |

> [!IMPORTANT]
> Consulte [Diretrizes de movimentação da implantação clássica](./move-limitations/classic-model-move-limitations.md). Os recursos da implantação clássica podem ser movidos entre assinaturas com uma operação específica para esse cenário.

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | funcionalidades | Não | Não |
> | checkstorageaccountavailability | Não | Não |
> | disks | Não | Não |
> | images | Não | Não |
> | operações | Não | Não |
> | osimages | Não | Não |
> | osplatformimages | Não | Não |
> | publicimages | Não | Não |
> | quotas | Não | Não |
> | storageaccounts | Sim | Não |
> | storageaccounts/blobservices | Não | Não |
> | storageaccounts/fileservices | Não | Não |
> | storageaccounts/metricdefinitions | Não | Não |
> | storageaccounts/metrics | Não | Não |
> | storageaccounts/queueservices | Não | Não |
> | storageaccounts/services | Não | Não |
> | storageaccounts/services/diagnosticsettings | Não | Não |
> | storageaccounts/services/metricdefinitions | Não | Não |
> | storageaccounts/services/metrics | Não | Não |
> | storageaccounts/tableservices | Não | Não |
> | storageaccounts/vmimages | Não | Não |
> | vmimages | Não | Não |

> [!IMPORTANT]
> Consulte [Diretrizes de movimentação da implantação clássica](./move-limitations/classic-model-move-limitations.md). Os recursos da implantação clássica podem ser movidos entre assinaturas com uma operação específica para esse cenário.

## <a name="microsoftclassicsubscription"></a>Microsoft.ClassicSubscription

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | operações | Não | Não |

> [!IMPORTANT]
> Consulte [Diretrizes de movimentação da implantação clássica](./move-limitations/classic-model-move-limitations.md). Os recursos da implantação clássica podem ser movidos entre assinaturas com uma operação específica para esse cenário.

## <a name="microsoftcognition"></a>Microsoft.Cognition

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | syntheticsaccounts | Não | Não |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Sim | Sim |
> | checkdomainavailability | Não | Não |
> | Locais | Não | Não |
> | locations/checkskuavailability | Não | Não |
> | locations/deletevirtualnetworkorsubnets | Não | Não |
> | locations/operationresults | Não | Não |
> | operações | Não | Não |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | operações | Não | Não |
> | ratecard | Não | Não |
> | usageaggregates | Não | Não |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | availabilitysets | Sim | Sim |
> | diskaccesses | Não | Não |
> | diskencryptionsets | Não | Não |
> | disks | Sim | Sim |
> | galleries | Não | Não |
> | galleries/images | Não | Não |
> | galleries/images/versions | Não | Não |
> | hostgroups | Não | Não |
> | hostgroups/hosts | Não | Não |
> | images | Sim | Sim |
> | Locais | Não | Não |
> | locations/artifactpublishers | Não | Não |
> | locations/capsoperations | Não | Não |
> | locations/diskoperations | Não | Não |
> | locations/loganalytics | Não | Não |
> | locations/operations | Não | Não |
> | locations/publishers | Não | Não |
> | locations/runcommands | Não | Não |
> | locations/usages | Não | Não |
> | locations/virtualmachines | Não | Não |
> | locations/vmsizes | Não | Não |
> | locations/vsmoperations | Não | Não |
> | operações | Não | Não |
> | proximityplacementgroups | Sim | Sim |
> | restorepointcollections | Não | Não |
> | restorepointcollections/restorepoints | Não | Não |
> | sharedvmextensions | Não | Não |
> | sharedvmimages | Não | Não |
> | sharedvmimages/versions | Não | Não |
> | snapshots | Sim | Sim |
> | sshpublickeys | Não | Não |
> | virtualmachines | Sim | Sim |
> | virtualmachines/extensions | Sim | Sim |
> | virtualmachines/metricdefinitions | Não | Não |
> | virtualmachines/runcommands | Não | Não |
> | virtualmachinescalesets | Sim | Sim |
> | virtualmachinescalesets/extensions | Não | Não |
> | virtualmachinescalesets/networkinterfaces | Não | Não |
> | virtualmachinescalesets/publicipaddresses | Não | Não |
> | virtualmachinescalesets/virtualmachines | Não | Não |
> | virtualmachinescalesets/virtualmachines/networkinterfaces | Não | Não |

> [!IMPORTANT]
> Consulte [Diretrizes de movimentação de Máquinas Virtuais](./move-limitations/virtual-machines-move-limitations.md).

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | aggregatedcost | Não | Não |
> | balances | Não | Não |
> | budgets | Não | Não |
> | charges | Não | Não |
> | costtags | Não | Não |
> | credits | Não | Não |
> | events | Não | Não |
> | forecasts | Não | Não |
> | lots | Não | Não |
> | marketplaces | Não | Não |
> | operationresults | Não | Não |
> | operações | Não | Não |
> | operationstatus | Não | Não |
> | pricesheets | Não | Não |
> | products | Não | Não |
> | reservationdetails | Não | Não |
> | reservationrecommendationdetails | Não | Não |
> | reservationrecommendations | Não | Não |
> | reservationsummaries | Não | Não |
> | reservationtransactions | Não | Não |
> | marcas | Não | Não |
> | tenants | Não | Não |
> | terms | Não | Não |
> | usagedetails | Não | Não |

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
> | Locais | Não | Não |
> | locations/cachedimages | Não | Não |
> | locations/capabilities | Não | Não |
> | locations/deletevirtualnetworkorsubnets | Não | Não |
> | locations/operations | Não | Não |
> | locations/usages | Não | Não |
> | operações | Não | Não |
> | serviceassociationlinks | Não | Não |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Não | Não |
> | Locais | Não | Não |
> | locations/authorize | Não | Não |
> | locations/deletevirtualnetworkorsubnets | Não | Não |
> | locations/operationresults | Não | Não |
> | locations/setupauth | Não | Não |
> | operações | Não | Não |
> | registries | Sim | Sim |
> | registries/agentpools | Sim | Sim |
> | registries/agentpools/listqueuestatus | Não | Não |
> | registries/builds | Não | Não |
> | registries/builds/cancel | Não | Não |
> | registries/builds/getloglink | Não | Não |
> | registries/buildtasks | Sim | Sim |
> | registries/buildtasks/listsourcerepositoryproperties | Não | Não |
> | registries/buildtasks/steps | Não | Não |
> | registries/buildtasks/steps/listbuildarguments | Não | Não |
> | registries/eventgridfilters | Não | Não |
> | registries/exportpipelines | Não | Não |
> | registries/generatecredentials | Não | Não |
> | registries/getbuildsourceuploadurl | Não | Não |
> | registries/getcredentials | Não | Não |
> | registries/importimage | Não | Não |
> | registries/importpipelines | Não | Não |
> | registries/listbuildsourceuploadurl | Não | Não |
> | registries/listcredentials | Não | Não |
> | registries/listpolicies | Não | Não |
> | registries/listusages | Não | Não |
> | registries/pipelineruns | Não | Não |
> | registries/privateendpointconnectionproxies | Não | Não |
> | registries/privateendpointconnectionproxies/validate | Não | Não |
> | registries/privateendpointconnections | Não | Não |
> | registries/privatelinkresources | Não | Não |
> | registries/queuebuild | Não | Não |
> | registries/regeneratecredential | Não | Não |
> | registries/regeneratecredentials | Não | Não |
> | registries/replications | Sim | Sim |
> | registries/runs | Não | Não |
> | registries/runs/cancel | Não | Não |
> | registries/runs/listlogsasurl | Não | Não |
> | registries/schedulerun | Não | Não |
> | registries/scopemaps | Não | Não |
> | registries/taskruns | Não | Não |
> | registries/taskruns/listdetails | Não | Não |
> | registries/tasks | Sim | Sim |
> | registries/tasks/listdetails | Não | Não |
> | registries/tokens | Não | Não |
> | registries/updatepolicies | Não | Não |
> | registries/webhooks | Sim | Sim |
> | registries/webhooks/getcallbackconfig | Não | Não |
> | registries/webhooks/listevents | Não | Não |
> | registries/webhooks/ping | Não | Não |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | containerservices | Não | Não |
> | Locais | Não | Não |
> | locations/openshiftclusters | Não | Não |
> | locations/operationresults | Não | Não |
> | locations/operations | Não | Não |
> | locations/orchestrators | Não | Não |
> | managedclusters | Não | Não |
> | openshiftmanagedclusters | Não | Não |
> | operações | Não | Não |

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
> | billingaccounts | Não | Não |
> | budgets | Não | Não |
> | cloudconnectors | Não | Não |
> | conectores | Sim | Sim |
> | departments | Não | Não |
> | dimensions | Não | Não |
> | enrollmentaccounts | Não | Não |
> | exports | Não | Não |
> | externalbillingaccounts | Não | Não |
> | externalbillingaccounts/alerts | Não | Não |
> | externalbillingaccounts/dimensions | Não | Não |
> | externalbillingaccounts/forecast | Não | Não |
> | externalbillingaccounts/query | Não | Não |
> | externalsubscriptions | Não | Não |
> | externalsubscriptions/alerts | Não | Não |
> | externalsubscriptions/dimensions | Não | Não |
> | externalsubscriptions/forecast | Não | Não |
> | externalsubscriptions/query | Não | Não |
> | previsão | Não | Não |
> | operações | Não | Não |
> | Consulta | Não | Não |
> | register | Não | Não |
> | reportconfigs | Não | Não |
> | relatórios | Não | Não |
> | configurações | Não | Não |
> | showbackrules | Não | Não |
> | Modos de exibição | Não | Não |

## <a name="microsoftcostmanagementexports"></a>Microsoft.CostManagementExports

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | operações | Não | Não |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hubs | Não | Não |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | operações | Não | Não |
> | solicitações | Não | Não |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | associations | Não | Não |
> | Locais | Não | Não |
> | locations/operationstatuses | Não | Não |
> | operações | Não | Não |
> | resourceproviders | Sim | Sim |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | jobs | Não | Não |
> | Locais | Não | Não |
> | locations/availableskus | Não | Não |
> | locations/checknameavailability | Não | Não |
> | locations/operationresults | Não | Não |
> | locations/regionconfiguration | Não | Não |
> | locations/validateaddress | Não | Não |
> | locations/validateinputs | Não | Não |
> | operações | Não | Não |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | databoxedgedevices | Não | Não |
> | databoxedgedevices/checknameavailability | Não | Não |
> | operações | Não | Não |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | Locais | Não | Não |
> | locations/getnetworkpolicies | Não | Não |
> | locations/operationstatuses | Não | Não |
> | operações | Não | Não |
> | workspaces | Não | Não |
> | workspaces/dbworkspaces | Não | Não |
> | workspaces/virtualnetworkpeerings | Não | Não |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | catalogs | Sim | Sim |
> | checknameavailability | Não | Não |
> | datacatalogs | Não | Não |
> | Locais | Não | Não |
> | locations/jobs | Não | Não |
> | locations/operationresults | Não | Não |
> | operações | Não | Não |

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
> | checkazuredatafactorynameavailability | Não | Não |
> | checkdatafactorynameavailability | Não | Não |
> | datafactories | Sim | Sim |
> | datafactories/diagnosticsettings | Não | Não |
> | datafactories/metricdefinitions | Não | Não |
> | datafactoryschema | Não | Não |
> | factories | Sim | Sim |
> | factories/integrationruntimes | Não | Não |
> | Locais | Não | Não |
> | locations/configurefactoryrepo | Não | Não |
> | locations/getfeaturevalue | Não | Não |
> | operações | Não | Não |

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
> | accounts/datalakestoreaccounts | Não | Não |
> | accounts/storageaccounts | Não | Não |
> | accounts/storageaccounts/containers | Não | Não |
> | accounts/storageaccounts/containers/listsastokens | Não | Não |
> | Locais | Não | Não |
> | locations/capability | Não | Não |
> | locations/checknameavailability | Não | Não |
> | locations/operationresults | Não | Não |
> | locations/usages | Não | Não |
> | operações | Não | Não |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Sim | Sim |
> | accounts/eventgridfilters | Não | Não |
> | accounts/firewallrules | Não | Não |
> | Locais | Não | Não |
> | locations/capability | Não | Não |
> | locations/checknameavailability | Não | Não |
> | locations/deletevirtualnetworkorsubnets | Não | Não |
> | locations/operationresults | Não | Não |
> | locations/usages | Não | Não |
> | operações | Não | Não |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | Locais | Não | Não |
> | locations/checknameavailability | Não | Não |
> | locations/operationresults | Não | Não |
> | locations/operationstatuses | Não | Não |
> | operações | Não | Não |
> | services | Não | Não |
> | services/projects | Não | Não |
> | slots | Não | Não |

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | backupvaults | Não | Não |
> | Locais | Não | Não |
> | operações | Não | Não |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Sim | Sim |
> | accounts/shares | Não | Não |
> | accounts/shares/datasets | Não | Não |
> | accounts/shares/invitations | Não | Não |
> | accounts/shares/providersharesubscriptions | Não | Não |
> | accounts/shares/synchronizationsettings | Não | Não |
> | accounts/sharesubscriptions | Não | Não |
> | accounts/sharesubscriptions/consumersourcedatasets | Não | Não |
> | accounts/sharesubscriptions/datasetmappings | Não | Não |
> | accounts/sharesubscriptions/triggers | Não | Não |
> | listinvitations | Não | Não |
> | Locais | Não | Não |
> | locations/consumerinvitations | Não | Não |
> | locations/operationresults | Não | Não |
> | locations/rejectinvitation | Não | Não |
> | operações | Não | Não |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Não | Não |
> | Locais | Não | Não |
> | locations/azureasyncoperation | Não | Não |
> | locations/operationresults | Não | Não |
> | locations/performancetiers | Não | Não |
> | locations/privateendpointconnectionazureasyncoperation | Não | Não |
> | locations/privateendpointconnectionoperationresults | Não | Não |
> | locations/privateendpointconnectionproxyazureasyncoperation | Não | Não |
> | locations/privateendpointconnectionproxyoperationresults | Não | Não |
> | locations/recommendedactionsessionsazureasyncoperation | Não | Não |
> | locations/recommendedactionsessionsoperationresults | Não | Não |
> | locations/securityalertpoliciesazureasyncoperation | Não | Não |
> | locations/securityalertpoliciesoperationresults | Não | Não |
> | locations/serverkeyazureasyncoperation | Não | Não |
> | locations/serverkeyoperationresults | Não | Não |
> | operações | Não | Não |
> | servers | Sim | Sim |
> | servers/advisors | Não | Não |
> | servers/privateendpointconnectionproxies | Não | Não |
> | servers/privateendpointconnections | Não | Não |
> | servers/privatelinkresources | Não | Não |
> | servers/querytexts | Não | Não |
> | servers/recoverableservers | Não | Não |
> | servers/topquerystatistics | Não | Não |
> | servers/virtualnetworkrules | Não | Não |
> | servers/waitstatistics | Não | Não |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Não | Não |
> | Locais | Não | Não |
> | locations/administratorazureasyncoperation | Não | Não |
> | locations/administratoroperationresults | Não | Não |
> | locations/azureasyncoperation | Não | Não |
> | locations/operationresults | Não | Não |
> | locations/performancetiers | Não | Não |
> | locations/privateendpointconnectionazureasyncoperation | Não | Não |
> | locations/privateendpointconnectionoperationresults | Não | Não |
> | locations/privateendpointconnectionproxyazureasyncoperation | Não | Não |
> | locations/privateendpointconnectionproxyoperationresults | Não | Não |
> | locations/recommendedactionsessionsazureasyncoperation | Não | Não |
> | locations/recommendedactionsessionsoperationresults | Não | Não |
> | locations/securityalertpoliciesazureasyncoperation | Não | Não |
> | locations/securityalertpoliciesoperationresults | Não | Não |
> | locations/serverkeyazureasyncoperation | Não | Não |
> | locations/serverkeyoperationresults | Não | Não |
> | operações | Não | Não |
> | servers | Sim | Sim |
> | servers/advisors | Não | Não |
> | servers/keys | Não | Não |
> | servers/privateendpointconnectionproxies | Não | Não |
> | servers/privateendpointconnections | Não | Não |
> | servers/privatelinkresources | Não | Não |
> | servers/querytexts | Não | Não |
> | servers/recoverableservers | Não | Não |
> | servers/topquerystatistics | Não | Não |
> | servers/virtualnetworkrules | Não | Não |
> | servers/waitstatistics | Não | Não |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Não | Não |
> | Locais | Não | Não |
> | locations/administratorazureasyncoperation | Não | Não |
> | locations/administratoroperationresults | Não | Não |
> | locations/azureasyncoperation | Não | Não |
> | locations/operationresults | Não | Não |
> | locations/performancetiers | Não | Não |
> | locations/privateendpointconnectionazureasyncoperation | Não | Não |
> | locations/privateendpointconnectionoperationresults | Não | Não |
> | locations/privateendpointconnectionproxyazureasyncoperation | Não | Não |
> | locations/privateendpointconnectionproxyoperationresults | Não | Não |
> | locations/recommendedactionsessionsazureasyncoperation | Não | Não |
> | locations/recommendedactionsessionsoperationresults | Não | Não |
> | locations/securityalertpoliciesazureasyncoperation | Não | Não |
> | locations/securityalertpoliciesoperationresults | Não | Não |
> | locations/serverkeyazureasyncoperation | Não | Não |
> | locations/serverkeyoperationresults | Não | Não |
> | operações | Não | Não |
> | servergroups | Não | Não |
> | servers | Sim | Sim |
> | servers/advisors | Não | Não |
> | servers/keys | Não | Não |
> | servers/privateendpointconnectionproxies | Não | Não |
> | servers/privateendpointconnections | Não | Não |
> | servers/privatelinkresources | Não | Não |
> | servers/querytexts | Não | Não |
> | servers/recoverableservers | Não | Não |
> | servers/topquerystatistics | Não | Não |
> | servers/virtualnetworkrules | Não | Não |
> | servers/waitstatistics | Não | Não |
> | serversv2 | Sim | Sim |
> | singleservers | Sim | Sim |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | artifactsources | Sim | Sim |
> | operationresults | Não | Não |
> | operações | Não | Não |
> | rollouts | Sim | Sim |
> | servicetopologies | Sim | Sim |
> | servicetopologies/services | Sim | Sim |
> | servicetopologies/services/serviceunits | Sim | Sim |
> | etapas | Sim | Sim |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | applicationgroups | Sim | Sim |
> | applicationgroups/applications | Não | Não |
> | applicationgroups/desktops | Não | Não |
> | applicationgroups/startmenuitems | Não | Não |
> | hostpools | Sim | Sim |
> | hostpools/sessionhosts | Não | Não |
> | hostpools/sessionhosts/usersessions | Não | Não |
> | hostpools/usersessions | Não | Não |
> | operações | Não | Não |
> | workspaces | Sim | Sim |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Não | Não |
> | checkprovisioningservicenameavailability | Não | Não |
> | elasticpools | Não | Não |
> | elasticpools/iothubtenants | Não | Não |
> | iothubs | Sim | Sim |
> | iothubs/eventgridfilters | Não | Não |
> | operationresults | Não | Não |
> | operações | Não | Não |
> | provisioningservices | Sim | Sim |
> | usages | Não | Não |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | pipelines | Sim | Sim |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | controladores | Sim | Sim |
> | controllers/listconnectiondetails | Não | Não |
> | Locais | Não | Não |
> | locations/checkcontainerhostmapping | Não | Não |
> | locations/operationresults | Não | Não |
> | operações | Não | Não |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | labcenters | Não | Não |
> | labs | Sim | Não |
> | labs/environments | Sim | Sim |
> | labs/servicerunners | Sim | Sim |
> | labs/virtualmachines | Sim | Não |
> | Locais | Não | Não |
> | locations/operations | Não | Não |
> | operações | Não | Não |
> | schedules | Sim | Sim |

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | digitaltwinsinstances | Não | Não |
> | Locais | Não | Não |
> | operações | Não | Não |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | databaseaccountnames | Não | Não |
> | databaseaccounts | Sim | Sim |
> | Locais | Não | Não |
> | locations/deletevirtualnetworkorsubnets | Não | Não |
> | locations/operationresults | Não | Não |
> | locations/operationsstatus | Não | Não |
> | operationresults | Não | Não |
> | operações | Não | Não |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checkdomainavailability | Não | Não |
> | domains | Sim | Sim |
> | domains/domainownershipidentifiers | Não | Não |
> | generatessorequest | Não | Não |
> | listdomainrecommendations | Não | Não |
> | operações | Não | Não |
> | topleveldomains | Não | Não |
> | validatedomainregistrationinformation | Não | Não |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | Locais | Não | Não |
> | locations/operationresults | Não | Não |
> | operações | Não | Não |
> | services | Sim | Sim |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | domains | Sim | Sim |
> | eventSubscriptions | Não - não pode ser movido independentemente, apenas automaticamente com recurso assinado. | Não - não pode ser movido independentemente, apenas automaticamente com recurso assinado. |
> | domains/topics | Não | Não |
> | eventsubscriptions | Não - não pode ser movido independentemente, apenas automaticamente com recurso assinado. | Não - não pode ser movido independentemente, apenas automaticamente com recurso assinado. |
> | extensiontopics | Não | Não |
> | Locais | Não | Não |
> | locations/eventsubscriptions | Não | Não |
> | locations/operationresults | Não | Não |
> | locations/operationsstatus | Não | Não |
> | locations/topictypes | Não | Não |
> | operationresults | Não | Não |
> | operações | Não | Não |
> | operationsstatus | Não | Não |
> | partnernamespaces | Sim | Sim |
> | partnernamespaces/eventchannels | Não | Não |
> | partnerregistrations | Não | Não |
> | partnertopics | Sim | Sim |
> | partnertopics/eventsubscriptions | Não | Não |
> | systemtopics | Sim | Sim |
> | systemtopics/eventsubscriptions | Não | Não |
> | topics | Sim | Sim |
> | topictypes | Não | Não |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | availableclusterregions | Não | Não |
> | checknameavailability | Não | Não |
> | checknamespaceavailability | Não | Não |
> | clusters | Sim | Sim |
> | Locais | Não | Não |
> | locations/deletevirtualnetworkorsubnets | Não | Não |
> | namespaces | Sim | Sim |
> | namespaces/authorizationrules | Não | Não |
> | namespaces/disasterrecoveryconfigs | Não | Não |
> | namespaces/disasterrecoveryconfigs/checknameavailability | Não | Não |
> | namespaces/eventhubs | Não | Não |
> | namespaces/eventhubs/authorizationrules | Não | Não |
> | namespaces/eventhubs/consumergroups | Não | Não |
> | namespaces/networkrulesets | Não | Não |
> | operações | Não | Não |
> | sku | Não | Não |

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | experimentworkspaces | Não | Não |
> | Locais | Não | Não |
> | locations/operations | Não | Não |

## <a name="microsoftfalcon"></a>Microsoft.Falcon

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | namespaces | Sim | Sim |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | featureproviders | Não | Não |
> | recursos | Não | Não |
> | operações | Não | Não |
> | providers | Não | Não |
> | subscriptionfeatureregistrations | Não | Não |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | automanagedaccounts | Não | Não |
> | automanagedvmconfigurationprofiles | Não | Não |
> | guestconfigurationassignments | Não | Não |
> | operações | Não | Não |
> | software | Não | Não |
> | softwareupdateprofile | Não | Não |
> | softwareupdates | Não | Não |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hanainstances | Não | Não |
> | Locais | Não | Não |
> | locations/operations | Não | Não |
> | locations/operationsstatus | Não | Não |
> | operações | Não | Não |
> | sapmonitors | Sim | Sim |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | dedicatedhsms | Não | Não |
> | Locais | Não | Não |
> | operações | Não | Não |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | Sim | Sim |
> | clusters/applications | Não | Não |
> | clusters/operationresults | Não | Não |
> | Locais | Não | Não |
> | locations/azureasyncoperations | Não | Não |
> | locations/billingspecs | Não | Não |
> | locations/capabilities | Não | Não |
> | locations/operationresults | Não | Não |
> | locations/usages | Não | Não |
> | locations/validatecreaterequest | Não | Não |
> | operações | Não | Não |

> [!IMPORTANT]
> Você pode mover os clusters HDInsight para uma nova assinatura ou grupo de recursos. No entanto, não é possível mover os recursos de rede vinculados ao cluster HDInsight (por exemplo, a rede virtual, NIC ou balanceador de carga) entre assinaturas. Além disso, não é possível mover uma para um novo grupo de recursos uma NIC que está conectada a uma máquina virtual para o cluster.
>
> Ao mover um cluster HDInsight para uma nova assinatura, mova primeiro os outros recursos (como a conta de armazenamento). Em seguida, mova apenas o cluster HDInsight.

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Não | Não |
> | Locais | Não | Não |
> | locations/operationresults | Não | Não |
> | operações | Não | Não |
> | services | Sim | Sim |
> | services/privateendpointconnections | Não | Não |
> | services/privatelinkresources | Não | Não |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | machines | Sim | Sim |
> | machines/extensions | Não | Não |
> | operações | Não | Não |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | datamanagers | Sim | Sim |
> | operações | Não | Não |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | components | Não | Não |
> | Locais | Não | Não |
> | networkscopes | Não | Não |
> | operações | Não | Não |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | jobs | Sim | Sim |
> | Locais | Não | Não |
> | locations/operationresults | Não | Não |
> | operações | Não | Não |

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
> | components/events | Não | Não |
> | components/linkedstorageaccounts | Não | Não |
> | components/metrics | Não | Não |
> | components/pricingplans | Não | Não |
> | components/query | Não | Não |
> | datacollectionrules | Não | Não |
> | diagnosticsettings | Não | Não |
> | diagnosticsettingscategories | Não | Não |
> | eventcategories | Não | Não |
> | eventtypes | Não | Não |
> | extendeddiagnosticsettings | Não | Não |
> | guestdiagnosticsettings | Não | Não |
> | listmigrationdate | Não | Não |
> | Locais | Não | Não |
> | locations/operationresults | Não | Não |
> | logdefinitions | Não | Não |
> | logprofiles | Não | Não |
> | logs | Não | Não |
> | metricalerts | Não | Não |
> | metricbaselines | Não | Não |
> | metricbatch | Não | Não |
> | metricdefinitions | Não | Não |
> | metricnamespaces | Não | Não |
> | Métricas | Não | Não |
> | migratealertrules | Não | Não |
> | migratetonewpricingmodel | Não | Não |
> | myworkbooks | Não | Não |
> | notificationgroups | Não | Não |
> | operações | Não | Não |
> | privatelinkscopeoperationstatuses | Não | Não |
> | privatelinkscopes | Não | Não |
> | privatelinkscopes/privateendpointconnectionproxies | Não | Não |
> | privatelinkscopes/privateendpointconnections | Não | Não |
> | privatelinkscopes/scopedresources | Não | Não |
> | rollbacktolegacypricingmodel | Não | Não |
> | scheduledqueryrules | Sim | Sim |
> | Topologia | Não | Não |
> | transações | Não | Não |
> | vminsightsonboardingstatuses | Não | Não |
> | webtests | Sim | Sim |
> | workbooks | Sim | Sim |
> | workbooktemplates | Sim | Sim |

> [!IMPORTANT]
> Verifique se mover para nova assinatura não excede as [cotas de assinatura](azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | apptemplates | Não | Não |
> | checknameavailability | Não | Não |
> | checksubdomainavailability | Não | Não |
> | iotapps | Sim | Sim |
> | operações | Não | Não |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Sim | Sim |
> | grafo | Sim | Sim |
> | operações | Não | Não |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Não | Não |
> | deletedvaults | Não | Não |
> | hsmpools | Não | Não |
> | Locais | Não | Não |
> | locations/deletedvaults | Não | Não |
> | locations/deletevirtualnetworkorsubnets | Não | Não |
> | locations/operationresults | Não | Não |
> | operações | Não | Não |
> | vaults | Sim | Sim |
> | vaults/accesspolicies | Não | Não |
> | vaults/eventgridfilters | Não | Não |
> | vaults/secrets | Não | Não |

> [!IMPORTANT]
> O Key Vault é usado para criptografia de disco e não pode ser movido para um grupo de recursos na mesma assinatura ou entre assinaturas.

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | connectedclusters | Sim | Sim |
> | Locais | Não | Não |
> | locations/operationstatuses | Não | Não |
> | operações | Não | Não |
> | registeredsubscriptions | Não | Não |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | sourcecontrolconfigurations | Não | Não |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | Sim | Sim |
> | clusters/attacheddatabaseconfigurations | Não | Não |
> | clusters/databases | Não | Não |
> | clusters/databases/dataconnections | Não | Não |
> | clusters/databases/eventhubconnections | Não | Não |
> | clusters/databases/principalassignments | Não | Não |
> | clusters/principalassignments | Não | Não |
> | Locais | Não | Não |
> | locations/checknameavailability | Não | Não |
> | locations/operationresults | Não | Não |
> | operações | Não | Não |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | labaccounts | Não | Não |
> | Locais | Não | Não |
> | locations/operations | Não | Não |
> | operações | Não | Não |
> | users | Não | Não |

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
> | integrationserviceenvironments/managedapis | Sim | Não |
> | isolatedenvironments | Não | Não |
> | Locais | Não | Não |
> | locations/workflows | Não | Não |
> | operações | Não | Não |
> | workflows | Sim | Sim |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | commitmentplans | Não | Não |
> | Locais | Não | Não |
> | locations/operations | Não | Não |
> | locations/operationsstatus | Não | Não |
> | operações | Não | Não |
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
> | accounts/workspaces | Não | Não |
> | accounts/workspaces/projects | Não | Não |
> | teamaccounts | Não | Não |
> | teamaccounts/workspaces | Não | Não |
> | teamaccounts/workspaces/projects | Não | Não |

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
> | Locais | Não | Não |
> | locations/computeoperationsstatus | Não | Não |
> | locations/quotas | Não | Não |
> | locations/updatequotas | Não | Não |
> | locations/usages | Não | Não |
> | locations/vmsizes | Não | Não |
> | locations/workspaceoperationsstatus | Não | Não |
> | operações | Não | Não |
> | workspaces | Não | Não |
> | workspaces/computes | Não | Não |
> | workspaces/eventgridfilters | Não | Não |

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | applyupdates | Não | Não |
> | configurationassignments | Não | Não |
> | maintenanceconfigurations | Sim | Sim |
> | updates | Não | Não |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | identidades | Não | Não |
> | operações | Não | Não |
> | userassignedidentities | Não | Não |

## <a name="microsoftmanagednetwork"></a>Microsoft.ManagedNetwork

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | managednetworks | Não | Não |
> | managednetworks/managednetworkgroups | Não | Não |
> | managednetworks/managednetworkpeeringpolicies | Não | Não |
> | notificação | Não | Não |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | marketplaceregistrationdefinitions | Não | Não |
> | operações | Não | Não |
> | operationstatuses | Não | Não |
> | registrationassignments | Não | Não |
> | registrationdefinitions | Não | Não |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Não | Não |
> | getentities | Não | Não |
> | managementgroups | Não | Não |
> | managementgroups/settings | Não | Não |
> | operationresults | Não | Não |
> | operationresults/asyncoperation | Não | Não |
> | operações | Não | Não |
> | recursos | Não | Não |
> | starttenantbackfill | Não | Não |
> | tenantbackfillstatus | Não | Não |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Sim | Sim |
> | accounts/eventgridfilters | Não | Não |
> | accounts/privateatlases | Sim | Sim |
> | operações | Não | Não |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | listavailableoffers | Não | Não |
> | offers | Não | Não |
> | offertypes | Não | Não |
> | offertypes/publishers | Não | Não |
> | offertypes/publishers/offers | Não | Não |
> | offertypes/publishers/offers/plans | Não | Não |
> | offertypes/publishers/offers/plans/agreements | Não | Não |
> | offertypes/publishers/offers/plans/configs | Não | Não |
> | offertypes/publishers/offers/plans/configs/importimage | Não | Não |
> | operações | Não | Não |
> | privategalleryitems | Não | Não |
> | privatestoreclient | Não | Não |
> | privatestores | Não | Não |
> | privatestores/offers | Não | Não |
> | products | Não | Não |
> | publishers | Não | Não |
> | publishers/offers | Não | Não |
> | publishers/offers/amendments | Não | Não |
> | register | Não | Não |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | classicdevservices | Não | Não |
> | listcommunicationpreference | Não | Não |
> | operações | Não | Não |
> | updatecommunicationpreference | Não | Não |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | Contratos | Não | Não |
> | offertypes | Não | Não |
> | operações | Não | Não |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Não | Não |
> | Locais | Não | Não |
> | locations/checknameavailability | Não | Não |
> | mediaservices | Sim | Sim |
> | mediaservices/accountfilters | Não | Não |
> | mediaservices/assets | Não | Não |
> | mediaservices/assets/assetfilters | Não | Não |
> | mediaservices/contentkeypolicies | Não | Não |
> | mediaservices/eventgridfilters | Não | Não |
> | mediaservices/liveeventoperations | Não | Não |
> | mediaservices/liveevents | Sim | Sim |
> | mediaservices/liveevents/liveoutputs | Não | Não |
> | mediaservices/liveoutputoperations | Não | Não |
> | mediaservices/streamingendpointoperations | Não | Não |
> | mediaservices/streamingendpoints | Sim | Sim |
> | mediaservices/streaminglocators | Não | Não |
> | mediaservices/streamingpolicies | Não | Não |
> | mediaservices/transforms | Não | Não |
> | mediaservices/transforms/jobs | Não | Não |
> | operações | Não | Não |

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
> | Locais | Não | Não |
> | locations/assessmentoptions | Não | Não |
> | locations/checknameavailability | Não | Não |
> | migrateprojects | Sim | Sim |
> | movecollections | Não | Não |
> | operações | Não | Não |
> | projects | Não | Não |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | holographicsbroadcastaccounts | Não | Não |
> | Locais | Não | Não |
> | locations/checknameavailability | Não | Não |
> | objectunderstandingaccounts | Não | Não |
> | operações | Não | Não |
> | remoterenderingaccounts | Sim | Sim |
> | spatialanchorsaccounts | Sim | Sim |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | netappaccounts | Não | Não |
> | netappaccounts/backuppolicies | Não | Não |
> | netappaccounts/capacitypools | Não | Não |
> | netappaccounts/capacitypools/volumes | Não | Não |
> | netappaccounts/capacitypools/volumes/mounttargets | Não | Não |
> | netappaccounts/capacitypools/volumes/snapshots | Não | Não |
> | operações | Não | Não |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | applicationgatewayavailablerequestheaders | Não | Não |
> | applicationgatewayavailableresponseheaders | Não | Não |
> | applicationgatewayavailableservervariables | Não | Não |
> | applicationgatewayavailablessloptions | Não | Não |
> | applicationgatewayavailablewafrulesets | Não | Não |
> | applicationgateways | Não | Não |
> | applicationgatewaywebapplicationfirewallpolicies | Não | Não |
> | applicationsecuritygroups | Sim | Sim |
> | azurefirewallfqdntags | Não | Não |
> | azurefirewalls | Não | Não |
> | bastionhosts | Não | Não |
> | bgpservicecommunities | Não | Não |
> | checkfrontdoornameavailability | Não | Não |
> | checktrafficmanagernameavailability | Não | Não |
> | connections | Sim | Sim |
> | ddoscustompolicies | Sim | Sim |
> | ddosprotectionplans | Não | Não |
> | dnsoperationresults | Não | Não |
> | dnsoperationstatuses | Não | Não |
> | dnszones | Sim | Sim |
> | dnszones/a | Não | Não |
> | dnszones/aaaa | Não | Não |
> | dnszones/all | Não | Não |
> | dnszones/caa | Não | Não |
> | dnszones/cname | Não | Não |
> | dnszones/mx | Não | Não |
> | dnszones/ns | Não | Não |
> | dnszones/ptr | Não | Não |
> | dnszones/recordsets | Não | Não |
> | dnszones/soa | Não | Não |
> | dnszones/srv | Não | Não |
> | dnszones/txt | Não | Não |
> | expressroutecircuits | Não | Não |
> | expressroutegateways | Não | Não |
> | expressrouteserviceproviders | Não | Não |
> | firewallpolicies | Sim | Sim |
> | frontdooroperationresults | Não | Não |
> | frontdoors | Não | Não |
> | frontdoorwebapplicationfirewallmanagedrulesets | Não | Não |
> | frontdoorwebapplicationfirewallpolicies | Não | Não |
> | getdnsresourcereference | Não | Não |
> | internalnotify | Não | Não |
> | ipgroups | Sim | Sim |
> | loadbalancers | Sim - SKU Básico<br>Não - SKU Standard | Sim - SKU Básico<br>Não - SKU Standard |
> | localnetworkgateways | Sim | Sim |
> | Locais | Não | Não |
> | locations/autoapprovedprivatelinkservices | Não | Não |
> | locations/availabledelegations | Não | Não |
> | locations/availableprivateendpointtypes | Não | Não |
> | locations/availableservicealiases | Não | Não |
> | locations/baremetaltenants | Não | Não |
> | locations/batchnotifyprivateendpointsforresourcemove | Não | Não |
> | locations/batchvalidateprivateendpointsforresourcemove | Não | Não |
> | locations/checkacceleratednetworkingsupport | Não | Não |
> | locations/checkdnsnameavailability | Não | Não |
> | locations/checkprivatelinkservicevisibility | Não | Não |
> | locations/commitinternalazurenetworkmanagerconfiguration | Não | Não |
> | locations/effectiveresourceownership | Não | Não |
> | locations/nfvoperationresults | Não | Não |
> | locations/nfvoperations | Não | Não |
> | locations/operationresults | Não | Não |
> | locations/operations | Não | Não |
> | locations/servicetags | Não | Não |
> | locations/setresourceownership | Não | Não |
> | locations/supportedvirtualmachinesizes | Não | Não |
> | locations/usages | Não | Não |
> | locations/validateresourceownership | Não | Não |
> | locations/virtualnetworkavailableendpointservices | Não | Não |
> | natgateways | Sim | Sim |
> | networkexperimentprofiles | Não | Não |
> | networkintentpolicies | Sim | Sim |
> | networkinterfaces | Sim | Sim |
> | networkprofiles | Não | Não |
> | networksecuritygroups | Sim | Sim |
> | networkwatchers | Sim | Não |
> | networkwatchers/connectionmonitors | Sim | Não |
> | networkwatchers/flowlogs | Sim | Não |
> | networkwatchers/pingmeshes | Sim | Não |
> | operações | Não | Não |
> | p2svpngateways | Não | Não |
> | privatednsoperationresults | Não | Não |
> | privatednsoperationstatuses | Não | Não |
> | privatednszones | Sim | Sim |
> | privatednszones/a | Não | Não |
> | privatednszones/aaaa | Não | Não |
> | privatednszones/all | Não | Não |
> | privatednszones/cname | Não | Não |
> | privatednszones/mx | Não | Não |
> | privatednszones/ptr | Não | Não |
> | privatednszones/soa | Não | Não |
> | privatednszones/srv | Não | Não |
> | privatednszones/txt | Não | Não |
> | privatednszones/virtualnetworklinks | Sim | Sim |
> | privatednszonesinternal | Não | Não |
> | privateendpointredirectmaps | Não | Não |
> | privateendpoints | Sim | Sim |
> | privatelinkservices | Não | Não |
> | publicipaddresses | Sim - SKU Básico<br>Não - SKU Standard | Sim - SKU Básico<br>Não - SKU Standard |
> | publicipprefixes | Sim | Sim |
> | routefilters | Não | Não |
> | routetables | Sim | Sim |
> | securitypartnerproviders | Sim | Sim |
> | serviceendpointpolicies | Sim | Sim |
> | trafficmanagergeographichierarchies | Não | Não |
> | trafficmanagerprofiles | Sim | Sim |
> | trafficmanagerprofiles/heatmaps | Não | Não |
> | trafficmanagerusermetricskeys | Não | Não |
> | virtualhubs | Não | Não |
> | virtualnetworkgateways | Sim | Sim |
> | virtualnetworks | Sim | Sim |
> | virtualnetworktaps | Não | Não |
> | virtualrouters | Sim | Sim |
> | virtualwans | Não | Não |
> | vpngateways (WAN Virtual) | Não | Não |
> | vpnserverconfigurations | Não | Não |
> | vpnsites (WAN Virtual) | Não | Não |

> [!IMPORTANT]
> Consulte [Diretrizes de movimentação de rede](./move-limitations/networking-move-limitations.md).

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Não | Não |
> | checknamespaceavailability | Não | Não |
> | namespaces | Sim | Sim |
> | namespaces/notificationhubs | Sim | Sim |
> | operationresults | Não | Não |
> | operações | Não | Não |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | osnamespaces | Sim | Sim |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hypervsites | Sim | Sim |
> | importsites | Sim | Sim |
> | operações | Não | Não |
> | serversites | Sim | Sim |
> | vmwaresites | Sim | Sim |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | Não | Não |
> | deletedworkspaces | Não | Não |
> | linktargets | Não | Não |
> | Locais | Não | Não |
> | locations/operationstatuses | Não | Não |
> | operações | Não | Não |
> | storageinsightconfigs | Não | Não |
> | workspaces | Sim | Sim |
> | workspaces/datasources | Não | Não |
> | workspaces/linkedservices | Não | Não |
> | workspaces/linkedstorageaccounts | Não | Não |
> | workspaces/metadata | Não | Não |
> | workspaces/query | Não | Não |
> | workspaces/scopedprivatelinkproxies | Não | Não |

> [!IMPORTANT]
> Verifique se mover para uma nova assinatura não excede as [cotas de assinatura](azure-subscription-service-limits.md#azure-monitor-limits).
>
> Espaços de trabalho que têm uma conta de automação vinculada não podem ser movidos. Antes de iniciar uma operação de movimentação, não se esqueça de desvincular as contas de automação.

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | managementassociations | Não | Não |
> | managementconfigurations | Sim | Sim |
> | operações | Não | Não |
> | solutions | Sim | Sim |
> | Modos de exibição | Sim | Sim |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checkserviceprovideravailability | Não | Não |
> | legacypeerings | Não | Não |
> | operações | Não | Não |
> | peerasns | Não | Não |
> | peeringlocations | Não | Não |
> | peerings | Sim | Sim |
> | peeringservicecountries | Não | Não |
> | peeringservicelocations | Não | Não |
> | peeringserviceproviders | Não | Não |
> | peeringservices | Não | Não |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | asyncoperationresults | Não | Não |
> | operações | Não | Não |
> | policyevents | Não | Não |
> | policystates | Não | Não |
> | policytrackedresources | Não | Não |
> | remediations | Não | Não |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | consoles | Não | Não |
> | dashboards | Sim | Sim |
> | Locais | Não | Não |
> | locations/consoles | Não | Não |
> | locations/usersettings | Não | Não |
> | operações | Não | Não |
> | usersettings | Não | Não |

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | rootresources | Não | Não |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | Locais | Não | Não |
> | locations/checknameavailability | Não | Não |
> | workspacecollections | Sim | Sim |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | capacities | Sim | Sim |
> | Locais | Não | Não |
> | locations/checknameavailability | Não | Não |
> | locations/operationresults | Não | Não |
> | locations/operationstatuses | Não | Não |
> | operações | Não | Não |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |
> | checknameavailability | Não | Não |
> | operações | Não | Não |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | availableaccounts | Não | Não |
> | providerregistrations | Não | Não |
> | providerregistrations/resourcetyperegistrations | Não | Não |
> | rollouts | Não | Não |

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | Locais | Não | Não |
> | locations/operationstatuses | Não | Não |
> | operações | Não | Não |
> | workspaces | Não | Não |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | backupprotecteditems | Não | Não |
> | Locais | Não | Não |
> | locations/allocatedstamp | Não | Não |
> | locations/allocatestamp | Não | Não |
> | locations/backupaadproperties | Não | Não |
> | locations/backupcrossregionrestore | Não | Não |
> | locations/backupcrrjob | Não | Não |
> | locations/backupcrrjobs | Não | Não |
> | locations/backupcrroperationresults | Não | Não |
> | locations/backupcrroperationsstatus | Não | Não |
> | locations/backupprevalidateprotection | Não | Não |
> | locations/backupstatus | Não | Não |
> | locations/backupvalidatefeatures | Não | Não |
> | locations/checknameavailability | Não | Não |
> | operações | Não | Não |
> | replicationeligibilityresults | Não | Não |
> | vaults | Sim | Sim |

> [!IMPORTANT]
> Consulte [Diretrizes de movimentação de Serviços de Recuperação](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftredhatopenshift"></a>Microsoft.RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | Locais | Não | Não |
> | locations/operationresults | Não | Não |
> | locations/operationsstatus | Não | Não |
> | openshiftclusters | Não | Não |
> | operações | Não | Não |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Não | Não |
> | namespaces | Sim | Sim |
> | namespaces/authorizationrules | Não | Não |
> | namespaces/hybridconnections | Não | Não |
> | namespaces/hybridconnections/authorizationrules | Não | Não |
> | namespaces/privateendpointconnections | Não | Não |
> | namespaces/wcfrelays | Não | Não |
> | namespaces/wcfrelays/authorizationrules | Não | Não |
> | operações | Não | Não |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | operações | Não | Não |
> | consultas | Sim | Sim |
> | resourcechangedetails | Não | Não |
> | resourcechanges | Não | Não |
> | recursos | Não | Não |
> | resourceshistory | Não | Não |
> | subscriptionsstatus | Não | Não |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | availabilitystatuses | Não | Não |
> | childavailabilitystatuses | Não | Não |
> | childresources | Não | Não |
> | emergingissues | Não | Não |
> | events | Não | Não |
> | metadata | Não | Não |
> | Notificações | Não | Não |
> | operações | Não | Não |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checkpolicycompliance | Não | Não |
> | checkresourcename | Não | Não |
> | deployments | Não | Não |
> | deployments/operations | Não | Não |
> | deploymentscripts | Não | Não |
> | deploymentscripts/logs | Não | Não |
> | links | Não | Não |
> | Locais | Não | Não |
> | locations/deploymentscriptoperationresults | Não | Não |
> | notifyresourcejobs | Não | Não |
> | operationresults | Não | Não |
> | operações | Não | Não |
> | providers | Não | Não |
> | resourcegroups | Não | Não |
> | recursos | Não | Não |
> | subscriptions | Não | Não |
> | subscriptions/locations | Não | Não |
> | subscriptions/operationresults | Não | Não |
> | subscriptions/providers | Não | Não |
> | subscriptions/resourcegroups | Não | Não |
> | subscriptions/resourcegroups/resources | Não | Não |
> | subscriptions/resources | Não | Não |
> | subscriptions/tagnames | Não | Não |
> | subscriptions/tagnames/tagvalues | Não | Não |
> | marcas | Não | Não |
> | templatespecs | Não | Não |
> | templatespecs/versions | Não | Não |
> | tenants | Não | Não |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | de dimensionamento da Web | Sim | Não |
> | checkmoderneligibility | Não | Não |
> | checknameavailability | Não | Não |
> | operationresults | Não | Não |
> | operações | Não | Não |
> | saasresources | Não | Não |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Não | Não |
> | checkservicenameavailability | Não | Não |
> | operações | Não | Não |
> | resourcehealthmetadata | Não | Não |
> | searchservices | Sim | Sim |

> [!IMPORTANT]
> Não é possível mover vários recursos de Pesquisa em regiões diferentes em uma operação. Em vez disso, mova-os em operações separadas.

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | adaptivenetworkhardenings | Não | Não |
> | advancedthreatprotectionsettings | Não | Não |
> | alertas | Não | Não |
> | allowedconnections | Não | Não |
> | applicationwhitelistings | Não | Não |
> | assessmentmetadata | Não | Não |
> | assessments | Não | Não |
> | autodismissalertsrules | Não | Não |
> | automations | Sim | Sim |
> | autoprovisioningsettings | Não | Não |
> | complianceresults | Não | Não |
> | compliances | Não | Não |
> | datacollectionagents | Não | Não |
> | devicesecuritygroups | Não | Não |
> | discoveredsecuritysolutions | Não | Não |
> | externalsecuritysolutions | Não | Não |
> | informationprotectionpolicies | Não | Não |
> | iotsecuritysolutions | Sim | Sim |
> | iotsecuritysolutions/analyticsmodels | Não | Não |
> | iotsecuritysolutions/analyticsmodels/aggregatedalerts | Não | Não |
> | iotsecuritysolutions/analyticsmodels/aggregatedrecommendations | Não | Não |
> | jitnetworkaccesspolicies | Não | Não |
> | Locais | Não | Não |
> | locations/alerts | Não | Não |
> | locations/allowedconnections | Não | Não |
> | locations/applicationwhitelistings | Não | Não |
> | locations/discoveredsecuritysolutions | Não | Não |
> | locations/externalsecuritysolutions | Não | Não |
> | locations/jitnetworkaccesspolicies | Não | Não |
> | locations/securitysolutions | Não | Não |
> | locations/securitysolutionsreferencedata | Não | Não |
> | locations/tasks | Não | Não |
> | locations/topologies | Não | Não |
> | operações | Não | Não |
> | políticas | Não | Não |
> | pricings | Não | Não |
> | regulatorycompliancestandards | Não | Não |
> | regulatorycompliancestandards/regulatorycompliancecontrols | Não | Não |
> | regulatorycompliancestandards/regulatorycompliancecontrols/regulatorycomplianceassessments | Não | Não |
> | securitycontacts | Não | Não |
> | securitysolutions | Não | Não |
> | securitysolutionsreferencedata | Não | Não |
> | securitystatuses | Não | Não |
> | securitystatusessummaries | Não | Não |
> | servervulnerabilityassessments | Não | Não |
> | configurações | Não | Não |
> | subassessments | Não | Não |
> | tarefas | Não | Não |
> | topologies | Não | Não |
> | workspacesettings | Não | Não |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | aggregations | Não | Não |
> | alertrules | Não | Não |
> | alertruletemplates | Não | Não |
> | bookmarks | Não | Não |
> | cases | Não | Não |
> | dataconnectors | Não | Não |
> | dataconnectorscheckrequirements | Não | Não |
> | entidades | Não | Não |
> | entityqueries | Não | Não |
> | incidents | Não | Não |
> | officeconsents | Não | Não |
> | operações | Não | Não |
> | configurações | Não | Não |
> | threatintelligence | Não | Não |

## <a name="microsoftserialconsole"></a>Microsoft.SerialConsole

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | consoleservices | Não | Não |
> | Locais | Não | Não |
> | locations/consoleservices | Não | Não |
> | operações | Não | Não |

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
> | checknameavailability | Não | Não |
> | checknamespaceavailability | Não | Não |
> | Locais | Não | Não |
> | locations/deletevirtualnetworkorsubnets | Não | Não |
> | namespaces | Sim | Sim |
> | namespaces/authorizationrules | Não | Não |
> | namespaces/disasterrecoveryconfigs | Não | Não |
> | namespaces/disasterrecoveryconfigs/checknameavailability | Não | Não |
> | namespaces/eventgridfilters | Não | Não |
> | namespaces/networkrulesets | Não | Não |
> | namespaces/queues | Não | Não |
> | namespaces/queues/authorizationrules | Não | Não |
> | namespaces/topics | Não | Não |
> | namespaces/topics/authorizationrules | Não | Não |
> | namespaces/topics/subscriptions | Não | Não |
> | namespaces/topics/subscriptions/rules | Não | Não |
> | operações | Não | Não |
> | premiummessagingregions | Não | Não |
> | sku | Não | Não |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | de dimensionamento da Web | Não | Não |
> | clusters | Sim | Sim |
> | clusters/applications | Não | Não |
> | containergroups | Não | Não |
> | containergroupsets | Não | Não |
> | edgeclusters | Não | Não |
> | Locais | Não | Não |
> | locations/clusterversions | Não | Não |
> | locations/environments | Não | Não |
> | locations/operationresults | Não | Não |
> | locations/operations | Não | Não |
> | managedclusters | Não | Não |
> | networks | Não | Não |
> | operações | Não | Não |
> | secretstores | Não | Não |
> | volumes | Não | Não |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | de dimensionamento da Web | Sim | Sim |
> | containergroups | Não | Não |
> | gateways | Sim | Sim |
> | Locais | Não | Não |
> | locations/applicationoperations | Não | Não |
> | locations/gatewayoperations | Não | Não |
> | locations/networkoperations | Não | Não |
> | locations/secretoperations | Não | Não |
> | locations/volumeoperations | Não | Não |
> | networks | Sim | Sim |
> | operações | Não | Não |
> | segredos | Sim | Sim |
> | volumes | Sim | Sim |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | providerregistrations | Não | Não |
> | providerregistrations/resourcetyperegistrations | Não | Não |
> | rollouts | Não | Não |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | Locais | Não | Não |
> | locations/checknameavailability | Não | Não |
> | locations/operationresults | Não | Não |
> | locations/operationstatuses | Não | Não |
> | locations/usages | Não | Não |
> | operações | Não | Não |
> | signalr | Sim | Sim |
> | signalr/eventgridfilters | Não | Não |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hybridusebenefits | Não | Não |
> | operações | Não | Não |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | applicationdefinitions | Não | Não |
> | de dimensionamento da Web | Não | Não |
> | jitrequests | Não | Não |
> | Locais | Não | Não |
> | locations/operationstatuses | Não | Não |
> | operações | Não | Não |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Não | Não |
> | instancepools | Não | Não |
> | Locais | Sim | Sim |
> | Locais | Não | Não |
> | locations/administratorazureasyncoperation | Não | Não |
> | locations/administratoroperationresults | Não | Não |
> | locations/auditingsettingsazureasyncoperation | Não | Não |
> | locations/auditingsettingsoperationresults | Não | Não |
> | locations/capabilities | Não | Não |
> | locations/databaseazureasyncoperation | Não | Não |
> | locations/databaseoperationresults | Não | Não |
> | locations/databaserestoreazureasyncoperation | Não | Não |
> | locations/deletevirtualnetworkorsubnets | Não | Não |
> | locations/deletevirtualnetworkorsubnetsazureasyncoperation | Não | Não |
> | locations/deletevirtualnetworkorsubnetsoperationresults | Não | Não |
> | locations/dnsaliasasyncoperation | Não | Não |
> | locations/dnsaliasoperationresults | Não | Não |
> | locations/elasticpoolazureasyncoperation | Não | Não |
> | locations/elasticpooloperationresults | Não | Não |
> | locations/encryptionprotectorazureasyncoperation | Não | Não |
> | locations/encryptionprotectoroperationresults | Não | Não |
> | locations/extendedauditingsettingsazureasyncoperation | Não | Não |
> | locations/extendedauditingsettingsoperationresults | Não | Não |
> | locations/failovergroupazureasyncoperation | Não | Não |
> | locations/failovergroupoperationresults | Não | Não |
> | locations/firewallrulesazureasyncoperation | Não | Não |
> | locations/firewallrulesoperationresults | Não | Não |
> | locations/instancefailovergroupazureasyncoperation | Não | Não |
> | locations/instancefailovergroupoperationresults | Não | Não |
> | locations/instancefailovergroups | Não | Não |
> | locations/instancepoolazureasyncoperation | Não | Não |
> | locations/instancepooloperationresults | Não | Não |
> | locations/jobagentazureasyncoperation | Não | Não |
> | locations/jobagentoperationresults | Não | Não |
> | locations/longtermretentionbackupazureasyncoperation | Não | Não |
> | locations/longtermretentionbackupoperationresults | Não | Não |
> | locations/longtermretentionbackups | Não | Não |
> | locations/longtermretentionmanagedinstancebackupazureasyncoperation | Não | Não |
> | locations/longtermretentionmanagedinstancebackupoperationresults | Não | Não |
> | locations/longtermretentionmanagedinstancebackups | Não | Não |
> | locations/longtermretentionmanagedinstances | Não | Não |
> | locations/longtermretentionpolicyazureasyncoperation | Não | Não |
> | locations/longtermretentionpolicyoperationresults | Não | Não |
> | locations/longtermretentionservers | Não | Não |
> | locations/manageddatabaseazureasyncoperation | Não | Não |
> | locations/manageddatabasecompleterestoreazureasyncoperation | Não | Não |
> | locations/manageddatabasecompleterestoreoperationresults | Não | Não |
> | locations/manageddatabaseoperationresults | Não | Não |
> | locations/manageddatabaserestoreazureasyncoperation | Não | Não |
> | locations/manageddatabaserestoreoperationresults | Não | Não |
> | locations/managedinstanceazureasyncoperation | Não | Não |
> | locations/managedinstanceencryptionprotectorazureasyncoperation | Não | Não |
> | locations/managedinstanceencryptionprotectoroperationresults | Não | Não |
> | locations/managedinstancekeyazureasyncoperation | Não | Não |
> | locations/managedinstancekeyoperationresults | Não | Não |
> | locations/managedinstancelongtermretentionpolicyazureasyncoperation | Não | Não |
> | locations/managedinstancelongtermretentionpolicyoperationresults | Não | Não |
> | locations/managedinstanceoperationresults | Não | Não |
> | locations/managedinstancetdecertazureasyncoperation | Não | Não |
> | locations/managedinstancetdecertoperationresults | Não | Não |
> | locations/managedserversecurityalertpoliciesazureasyncoperation | Não | Não |
> | locations/managedserversecurityalertpoliciesoperationresults | Não | Não |
> | locations/managedshorttermretentionpolicyazureasyncoperation | Não | Não |
> | locations/managedshorttermretentionpolicyoperationresults | Não | Não |
> | locations/notifyazureasyncoperation | Não | Não |
> | locations/privateendpointconnectionazureasyncoperation | Não | Não |
> | locations/privateendpointconnectionoperationresults | Não | Não |
> | locations/privateendpointconnectionproxyazureasyncoperation | Não | Não |
> | locations/privateendpointconnectionproxyoperationresults | Não | Não |
> | locations/securityalertpoliciesazureasyncoperation | Não | Não |
> | locations/securityalertpoliciesoperationresults | Não | Não |
> | locations/serveradministratorazureasyncoperation | Não | Não |
> | locations/serveradministratoroperationresults | Não | Não |
> | locations/serverazureasyncoperation | Não | Não |
> | locations/serverkeyazureasyncoperation | Não | Não |
> | locations/serverkeyoperationresults | Não | Não |
> | locations/serveroperationresults | Não | Não |
> | locations/shorttermretentionpolicyazureasyncoperation | Não | Não |
> | locations/shorttermretentionpolicyoperationresults | Não | Não |
> | locations/syncagentoperationresults | Não | Não |
> | locations/syncdatabaseids | Não | Não |
> | locations/syncgroupoperationresults | Não | Não |
> | locations/syncmemberoperationresults | Não | Não |
> | locations/tdecertazureasyncoperation | Não | Não |
> | locations/tdecertoperationresults | Não | Não |
> | locations/usages | Não | Não |
> | locations/virtualclusterazureasyncoperation | Não | Não |
> | locations/virtualclusteroperationresults | Não | Não |
> | locations/virtualnetworkrulesazureasyncoperation | Não | Não |
> | locations/virtualnetworkrulesoperationresults | Não | Não |
> | locations/vulnerabilityassessmentscanazureasyncoperation | Não | Não |
> | locations/vulnerabilityassessmentscanoperationresults | Não | Não |
> | managedinstances | Não | Não |
> | managedinstances/administrators | Não | Não |
> | managedinstances/databases | Não | Não |
> | managedinstances/databases/backuplongtermretentionpolicies | Não | Não |
> | managedinstances/databases/vulnerabilityassessments | Não | Não |
> | managedinstances/metricdefinitions | Não | Não |
> | managedinstances/metrics | Não | Não |
> | managedinstances/recoverabledatabases | Não | Não |
> | managedinstances/tdecertificates | Não | Não |
> | managedinstances/vulnerabilityassessments | Não | Não |
> | operações | Não | Não |
> | servers | Sim | Sim |
> | servers/administratoroperationresults | Não | Não |
> | servers/administrators | Não | Não |
> | servers/advisors | Não | Não |
> | servers/aggregateddatabasemetrics | Não | Não |
> | servers/auditingpolicies | Não | Não |
> | servers/auditingsettings | Não | Não |
> | servers/automatictuning | Não | Não |
> | servers/communicationlinks | Não | Não |
> | servers/connectionpolicies | Não | Não |
> | servers/databases | Sim | Sim |
> | servers/databases/advisors | Não | Não |
> | servers/databases/auditingpolicies | Não | Não |
> | servers/databases/auditingsettings | Não | Não |
> | servers/databases/auditrecords | Não | Não |
> | servers/databases/automatictuning | Não | Não |
> | servers/databases/backuplongtermretentionpolicies | Não | Não |
> | servers/databases/backupshorttermretentionpolicies | Não | Não |
> | servers/databases/connectionpolicies | Não | Não |
> | servers/databases/datamaskingpolicies | Não | Não |
> | servers/databases/datamaskingpolicies/rules | Não | Não |
> | servers/databases/extensions | Não | Não |
> | servers/databases/geobackuppolicies | Não | Não |
> | servers/databases/metricdefinitions | Não | Não |
> | servers/databases/metrics | Não | Não |
> | servers/databases/recommendedsensitivitylabels | Não | Não |
> | servers/databases/securityalertpolicies | Não | Não |
> | servers/databases/syncgroups | Não | Não |
> | servers/databases/syncgroups/syncmembers | Não | Não |
> | servers/databases/topqueries | Não | Não |
> | servers/databases/topqueries/querytext | Não | Não |
> | servers/databases/transparentdataencryption | Não | Não |
> | servers/databases/vulnerabilityassessment | Não | Não |
> | servers/databases/vulnerabilityassessments | Não | Não |
> | servers/databases/vulnerabilityassessmentscans | Não | Não |
> | servers/databases/vulnerabilityassessmentsettings | Não | Não |
> | servers/databases/workloadgroups | Não | Não |
> | servers/databasesecuritypolicies | Não | Não |
> | servers/disasterrecoveryconfiguration | Não | Não |
> | servers/dnsaliases | Não | Não |
> | servers/elasticpoolestimates | Não | Não |
> | servers/elasticpools | Sim | Sim |
> | servers/elasticpools/advisors | Não | Não |
> | servers/elasticpools/metricdefinitions | Não | Não |
> | servers/elasticpools/metrics | Não | Não |
> | servers/encryptionprotector | Não | Não |
> | servers/extendedauditingsettings | Não | Não |
> | servers/failovergroups | Não | Não |
> | servers/import | Não | Não |
> | servers/importexportoperationresults | Não | Não |
> | servers/jobaccounts | Sim | Sim |
> | servers/jobagents | Sim | Sim |
> | servers/jobagents/jobs | Não | Não |
> | servers/jobagents/jobs/executions | Não | Não |
> | servers/jobagents/jobs/steps | Não | Não |
> | servers/keys | Não | Não |
> | servers/operationresults | Não | Não |
> | servers/recommendedelasticpools | Não | Não |
> | servers/recoverabledatabases | Não | Não |
> | servers/restorabledroppeddatabases | Não | Não |
> | servers/securityalertpolicies | Não | Não |
> | servers/serviceobjectives | Não | Não |
> | servers/syncagents | Não | Não |
> | servers/tdecertificates | Não | Não |
> | servers/usages | Não | Não |
> | servers/virtualnetworkrules | Não | Não |
> | servers/vulnerabilityassessments | Não | Não |
> | virtualclusters | Sim | Sim |

> [!IMPORTANT]
> Um banco de dados e o servidor devem estar no mesmo grupo de recursos. Quando você move um SQL Server, todos os seus bancos de dados também são movidos. Este comportamento se aplica ao Banco de Dados SQL do Azure e ao banco de dados SQL Data Warehouse do Azure.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | Locais | Não | Não |
> | locations/availabilitygrouplisteneroperationresults | Não | Não |
> | locations/operationtypes | Não | Não |
> | locations/sqlvirtualmachinegroupoperationresults | Não | Não |
> | locations/sqlvirtualmachineoperationresults | Não | Não |
> | operações | Não | Não |
> | sqlvirtualmachinegroups | Sim | Sim |
> | sqlvirtualmachinegroups/availabilitygrouplisteners | Não | Não |
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
> | checknameavailability | Não | Não |
> | Locais | Não | Não |
> | locations/asyncoperations | Não | Não |
> | locations/checknameavailability | Não | Não |
> | locations/deletevirtualnetworkorsubnets | Não | Não |
> | locations/usages | Não | Não |
> | operações | Não | Não |
> | storageaccounts | Sim | Sim |
> | storageaccounts/blobservices | Não | Não |
> | storageaccounts/fileservices | Não | Não |
> | storageaccounts/listaccountsas | Não | Não |
> | storageaccounts/listservicesas | Não | Não |
> | storageaccounts/queueservices | Não | Não |
> | storageaccounts/services | Não | Não |
> | storageaccounts/services/metricdefinitions | Não | Não |
> | storageaccounts/tableservices | Não | Não |
> | usages | Não | Não |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | caches | Não | Não |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | Locais | Não | Não |
> | locations/checknameavailability | Não | Não |
> | locations/workflows | Não | Não |
> | operações | Não | Não |
> | storagesyncservices | Sim | Sim |
> | storagesyncservices/registeredservers | Não | Não |
> | storagesyncservices/syncgroups | Não | Não |
> | storagesyncservices/syncgroups/cloudendpoints | Não | Não |
> | storagesyncservices/syncgroups/serverendpoints | Não | Não |
> | storagesyncservices/workflows | Não | Não |

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
> | operações | Não | Não |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | Não | Não |
> | Locais | Não | Não |
> | locations/quotas | Não | Não |
> | operações | Não | Não |
> | streamingjobs | Sim | Sim |

> [!IMPORTANT]
> Os trabalhos do Stream Analytics não podem ser movidos durante o estado de execução.

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | environments | Não | Não |
> | environments/eventsources | Não | Não |
> | instances | Não | Não |
> | instances/environments | Não | Não |
> | instances/environments/eventsources | Não | Não |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | cancel | Não | Não |
> | createsubscription | Não | Não |
> | enable | Não | Não |
> | operationresults | Não | Não |
> | operações | Não | Não |
> | renomear | Não | Não |
> | subscriptiondefinitions | Não | Não |
> | subscriptionoperations | Não | Não |
> | subscriptions | Não | Não |

## <a name="microsoftsupport"></a>microsoft.support

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Não | Não |
> | operationresults | Não | Não |
> | operações | Não | Não |
> | operationsstatus | Não | Não |
> | services | Não | Não |
> | services/problemclassifications | Não | Não |
> | supporttickets | Não | Não |

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Não | Não |
> | operações | Não | Não |
> | workspaces | Sim | Sim |
> | workspaces/bigdatapools | Sim | Sim |
> | workspaces/operationresults | Não | Não |
> | workspaces/operationstatuses | Não | Não |
> | workspaces/sqlpools | Sim | Sim |

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
> | environments/accesspolicies | Não | Não |
> | environments/eventsources | Sim | Sim |
> | environments/referencedatasets | Sim | Sim |
> | operações | Não | Não |

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | stores | Sim | Sim |
> | stores/accesspolicies | Não | Não |
> | stores/services | Não | Não |
> | stores/services/tokens | Não | Não |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | imagetemplates | Não | Não |
> | operações | Não | Não |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | account | Não | Não |
> | account/extension | Não | Não |
> | account/project | Não | Não |
> | checknameavailability | Não | Não |
> | operações | Não | Não |

> [!IMPORTANT]
> Para alterar a assinatura do Azure DevOps, consulte [alterar a assinatura do Azure usada para cobrança](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftvmware"></a>Microsoft.VMware

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | arczones | Não | Não |
> | Locais | Não | Não |
> | locations/operationstatuses | Não | Não |
> | operações | Não | Não |
> | resourcepools | Não | Não |
> | vcenters | Não | Não |
> | virtualmachines | Não | Não |
> | virtualmachinetemplates | Não | Não |
> | virtualnetworks | Não | Não |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | dedicatedcloudnodes | Não | Não |
> | dedicatedcloudservices | Não | Não |
> | Locais | Não | Não |
> | locations/availabilities | Não | Não |
> | locations/operationresults | Não | Não |
> | locations/privateclouds | Não | Não |
> | locations/privateclouds/resourcepools | Não | Não |
> | locations/privateclouds/virtualmachinetemplates | Não | Não |
> | locations/privateclouds/virtualnetworks | Não | Não |
> | locations/usages | Não | Não |
> | operações | Não | Não |
> | virtualmachines | Não | Não |

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | dispositivos | Não | Não |
> | Locais | Não | Não |
> | locations/operationstatuses | Não | Não |
> | operações | Não | Não |
> | vnfs | Não | Não |

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |
> | operações | Não | Não |
> | planos | Não | Não |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | availablestacks | Não | Não |
> | billingmeters | Não | Não |
> | certificates | Não | Sim |
> | checknameavailability | Não | Não |
> | connectiongateways | Sim | Sim |
> | connections | Sim | Sim |
> | customapis | Sim | Sim |
> | deletedsites | Não | Não |
> | deploymentlocations | Não | Não |
> | georegions | Não | Não |
> | hostingenvironments | Não | Não |
> | hostingenvironments/eventgridfilters | Não | Não |
> | hostingenvironments/multirolepools | Não | Não |
> | hostingenvironments/workerpools | Não | Não |
> | ishostingenvironmentnameavailable | Não | Não |
> | ishostnameavailable | Não | Não |
> | isusernameavailable | Não | Não |
> | kubeenvironments | Sim | Sim |
> | listsitesassignedtohostname | Não | Não |
> | Locais | Não | Não |
> | locations/apioperations | Não | Não |
> | locations/connectiongatewayinstallations | Não | Não |
> | locations/deletedsites | Não | Não |
> | locations/deletevirtualnetworkorsubnets | Não | Não |
> | locations/extractapidefinitionfromwsdl | Não | Não |
> | locations/getnetworkpolicies | Não | Não |
> | locations/listwsdlinterfaces | Não | Não |
> | locations/managedapis | Não | Não |
> | locations/operationresults | Não | Não |
> | locations/operations | Não | Não |
> | locations/runtimes | Não | Não |
> | operações | Não | Não |
> | publishingusers | Não | Não |
> | filmes | Não | Não |
> | resourcehealthmetadata | Não | Não |
> | runtimes | Não | Não |
> | serverfarms | Sim | Sim |
> | serverfarms/eventgridfilters | Não | Não |
> | sites | Sim | Sim |
> | sites/eventgridfilters | Não | Não |
> | sites/hostnamebindings | Não | Não |
> | sites/networkconfig | Não | Não |
> | sites/premieraddons | Sim | Sim |
> | sites/slots | Sim | Sim |
> | sites/slots/eventgridfilters | Não | Não |
> | sites/slots/hostnamebindings | Não | Não |
> | sites/slots/networkconfig | Não | Não |
> | sourcecontrols | Não | Não |
> | staticsites | Não | Não |
> | validade | Não | Não |
> | verifyhostingenvironmentvnet | Não | Não |

> [!IMPORTANT]
> Consulte [Diretrizes de movimentação do Serviço de Aplicativo](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftwindowsesu"></a>Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | Locais | Não | Não |
> | locations/operationstatuses | Não | Não |
> | multipleactivationkeys | Não | Não |
> | operações | Não | Não |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | deviceservices | Não | Não |
> | operações | Não | Não |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | components | Não | Não |
> | componentssummary | Não | Não |
> | monitorinstances | Não | Não |
> | monitorinstancessummary | Não | Não |
> | monitors | Não | Não |
> | notificationsettings | Não | Não |
> | operações | Não | Não |

## <a name="third-party-services"></a>Serviços de terceiros

No momento, serviços de terceiros não dão suporte à operação de movimentação.

## <a name="next-steps"></a>Próximas etapas
Para ver comandos para mover recursos, confira [Move resources to new resource group or subscription](move-resource-group-and-subscription.md) (Mover recursos para o novo grupo de recursos ou assinatura).

Para obter os mesmos dados como um arquivo de valores separados por vírgula, baixe [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv).
