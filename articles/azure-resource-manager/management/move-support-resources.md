---
title: Mover suporte de operação por tipo de recurso
description: Lista os tipos de recursos do Azure que podem ser movidos para um novo grupo de recursos ou assinatura.
ms.topic: conceptual
ms.date: 07/06/2020
ms.openlocfilehash: 7f241e12200101e2f8f9efa7cf31e4483b2d4229
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86044525"
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
> - [O. APPSERVICE](#microsoftappservice)
> - [Microsoft.Attestation](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AVS](#microsoftavs)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.AzureStackHCI](#microsoftazurestackhci)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.Billing](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [MICROSOFT. BIZTALKservices](#microsoftbiztalkservices)
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
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft. ConnectedCache](#microsoftconnectedcache)
> - [Microsoft.Consumption](#microsoftconsumption)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [O. CONTENTMODERATOR](#microsoftcontentmoderator)
> - [O. CORTANAANALYTICS](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CostManagementExports](#microsoftcostmanagementexports)
> - [O. CUSTOMERINSIGHTS](#microsoftcustomerinsights)
> - [Microsoft.CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft.CustomProviders](#microsoftcustomproviders)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [O. DataConnect](#microsoftdataconnect)
> - [O. Troca de](#microsoftdataexchange)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [O. DATALAKE](#microsoftdatalake)
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
> - [O. GENOMICS](#microsoftgenomics)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft. HybridNetwork](#microsofthybridnetwork)
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
> - [O. LOCATIONBASEDSERVICES](#microsoftlocationbasedservices)
> - [O. ARQUIVO LOCATIONSERVICES](#microsoftlocationservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [O. MACHINELEARNINGCOMPUTE](#microsoftmachinelearningcompute)
> - [O. MACHINELEARNINGEXPERIMENTATION](#microsoftmachinelearningexperimentation)
> - [O. MACHINELEARNINGMODELMANAGEMENT](#microsoftmachinelearningmodelmanagement)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.Maintenance](#microsoftmaintenance)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [O. MANAGEDNETWORK](#microsoftmanagednetwork)
> - [Microsoft.ManagedServices](#microsoftmanagedservices)
> - [Microsoft.Management](#microsoftmanagement)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.Marketplace](#microsoftmarketplace)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.MarketplaceOrdering](#microsoftmarketplaceordering)
> - [Microsoft.Media](#microsoftmedia)
> - [O. MICROSERVICES4SPRING](#microsoftmicroservices4spring)
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
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft. PowerPlatform](#microsoftpowerplatform)
> - [Microsoft.ProjectBabylon](#microsoftprojectbabylon)
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
> - [O. SERVERMANAGEMENT](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [O. SERVIÇOS](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.Sql](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [O. STORAGECACHE](#microsoftstoragecache)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [O. STORAGESYNCDEV](#microsoftstoragesyncdev)
> - [O. STORAGESYNCINT](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [O. STREAMANALYTICSEXPLORER](#microsoftstreamanalyticsexplorer)
> - [Microsoft.Subscription](#microsoftsubscription)
> - [microsoft.support](#microsoftsupport)
> - [Microsoft.Synapse](#microsoftsynapse)
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
> - [Microsoft. WorkloadBuilder](#microsoftworkloadbuilder)
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
> | azureadmetrics | Não | Não |
> | diagnosticsettings | Não | Não |
> | diagnosticsettingscategories | Não | Não |
> | operações | Não | Não |
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
> | actionrules | Não | Não |
> | alertas | Não | Não |
> | alertslist | Não | Não |
> | alertsmetadata | Não | Não |
> | alertssummary | Não | Não |
> | alertssummarylist | Não | Não |
> | operações | Não | Não |
> | smartdetectoralertrules | Não | Não |
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
> | servers | Não | Não |

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
> | configurationstores | Não | Não |
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
> | spring | Não | Não |
> | Spring/apps | Não | Não |
> | Spring/aplicativos/implantações | Não | Não |

## <a name="microsoftappservice"></a>O. APPSERVICE

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
> | attestationproviders | Não | Não |
> | defaultfornecedores | Não | Não |
> | Locais | Não | Não |
> | locais/defaultfornecedor | Não | Não |
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
> | operationstatus | Não | Não |
> | permissões | Não | Não |
> | policyassignments | Não | Não |
> | policydefinitions | Não | Não |
> | policysetdefinitions | Não | Não |
> | privatelinkassociations | Não | Não |
> | provideroperations | Não | Não |
> | resourcemanagementprivatelinks | Não | Não |
> | roleassignments | Não | Não |
> | roleassignmentsusagemetrics | Não | Não |
> | roledefinitions | Não | Não |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | automationaccounts | Sim | Sim |
> | automationaccounts/configurations | Não | Não |
> | automationaccounts/jobs | Não | Não |
> | automationaccounts/privateendpointconnectionproxies | Não | Não |
> | automationaccounts/privateendpointconnections | Não | Não |
> | automationaccounts/privatelinkresources | Não | Não |
> | automationaccounts/runbooks | Não | Não |
> | automationaccounts/softwareupdateconfigurations | Não | Não |
> | automationaccounts/webhooks | Não | Não |
> | operações | Não | Não |

> [!IMPORTANT]
> Os Runbooks devem existir no mesmo grupo de recursos que a conta de automação.
>
> Para obter informações, consulte [mover sua conta de automação do Azure para outra assinatura](../../automation/how-to/move-account.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftavs"></a>Microsoft.AVS

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | Locais | Não | Não |
> | locations/checkquotaavailability | Não | Não |
> | localizações/checktrialavailability | Não | Não |
> | operações | Não | Não |
> | privateclouds | Não | Não |
> | privateclouds/clusters | Não | Não |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | b2cdirectories | Sim | Sim |
> | b2ctenants | Não | Não |
> | checknameavailability | Não | Não |
> | guestusages | Sim | Sim |
> | operações | Não | Não |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | datacontrollers | Sim | Sim |
> | hybriddatamanagers | Sim | Sim |
> | operações | Não | Não |
> | postgresinstances | Sim | Sim |
> | sqlinstances | Sim | Sim |
> | sqlmanagedinstances | Sim | Sim |
> | sqlserverinstances | Sim | Sim |
> | sqlserverregistrations | Sim | Sim |
> | sqlserverregistrations/sqlservers | Não | Não |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | cloudmanifestfiles | Não | Não |
> | edgesubscriptions | Não | Não |
> | operações | Não | Não |
> | registrations | Não | Não |
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
> | billingaccounts/departamentos/billingpermissions | Não | Não |
> | billingaccounts/departamentos/billingroleassignments | Não | Não |
> | billingaccounts/departamentos/billingroledefinitions | Não | Não |
> | billingaccounts/enrollmentaccounts | Não | Não |
> | billingaccounts / enrollmentaccounts / billingpermissions | Não | Não |
> | billingaccounts / enrollmentaccounts / billingroleassignments | Não | Não |
> | billingaccounts / enrollmentaccounts / billingroledefinitions | Não | Não |
> | billingaccounts/invoices | Não | Não |
> | billingaccounts/faturas/transações | Não | Não |
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

## <a name="microsoftbiztalkservices"></a>MICROSOFT. BIZTALKservices

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
> | botservices | Não | Não |
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
> | Redis/eventgridfilters | Não | Não |
> | redis/privatelinkresources | Não | Não |
> | redisenterprise | Sim | Sim |

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
> | cdnwebapplicationfirewallpolicies | Não | Não |
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
> | profiles/endpoints | Não | Não |
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
> | domainnames | Não | Não |
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
> | virtualmachines | Não | Não |
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
> | storageaccounts | Não | Não |
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

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |
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
> | cloudservices | Não | Não |
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
> | swiftlets | Não | Não |
> | virtualmachines | Sim | Sim |
> | virtualmachines/extensions | Sim | Sim |
> | virtualmachines/metricdefinitions | Não | Não |
> | virtualmachines/runcommands | Sim | Sim |
> | virtualmachinescalesets | Sim | Sim |
> | virtualmachinescalesets/extensions | Não | Não |
> | virtualmachinescalesets/networkinterfaces | Não | Não |
> | virtualmachinescalesets/publicipaddresses | Não | Não |
> | virtualmachinescalesets/virtualmachines | Não | Não |
> | virtualmachinescalesets/virtualmachines/networkinterfaces | Não | Não |

> [!IMPORTANT]
> Consulte [Diretrizes de movimentação de Máquinas Virtuais](./move-limitations/virtual-machines-move-limitations.md).

## <a name="microsoftconnectedcache"></a>Microsoft. ConnectedCache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | cachenodes | Não | Não |

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
> | registries/agentpools | Não | Não |
> | registries/agentpools/listqueuestatus | Não | Não |
> | registries/builds | Não | Não |
> | registries/builds/cancel | Não | Não |
> | registries/builds/getloglink | Não | Não |
> | registries/buildtasks | Não | Não |
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
> | registries/replications | Não | Não |
> | registries/runs | Não | Não |
> | registries/runs/cancel | Não | Não |
> | registries/runs/listlogsasurl | Não | Não |
> | registries/schedulerun | Não | Não |
> | registries/scopemaps | Não | Não |
> | registries/taskruns | Não | Não |
> | registries/taskruns/listdetails | Não | Não |
> | registries/tasks | Não | Não |
> | registries/tasks/listdetails | Não | Não |
> | registries/tokens | Não | Não |
> | registries/updatepolicies | Não | Não |
> | registries/webhooks | Não | Não |
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

## <a name="microsoftcontentmoderator"></a>O. CONTENTMODERATOR

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | de dimensionamento da Web | Não | Não |

## <a name="microsoftcortanaanalytics"></a>O. CORTANAANALYTICS

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
> | conectores | Não | Não |
> | costallocationrules | Não | Não |
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

## <a name="microsoftcustomerinsights"></a>O. CUSTOMERINSIGHTS

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
> | resourceproviders | Não | Não |

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
> | availableskus | Não | Não |
> | databoxedgedevices | Sim | Sim |
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
> | catalogs | Não | Não |
> | checknameavailability | Não | Não |
> | datacatalogs | Não | Não |
> | Locais | Não | Não |
> | locations/jobs | Não | Não |
> | locations/operationresults | Não | Não |
> | operações | Não | Não |

## <a name="microsoftdataconnect"></a>O. DataConnect

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | connectionmanagers | Não | Não |

## <a name="microsoftdataexchange"></a>O. Troca de

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
> | datafactories | Não | Não |
> | datafactories/diagnosticsettings | Não | Não |
> | datafactories/metricdefinitions | Não | Não |
> | datafactoryschema | Não | Não |
> | factories | Sim | Sim |
> | factories/integrationruntimes | Não | Não |
> | Locais | Não | Não |
> | locations/configurefactoryrepo | Não | Não |
> | locations/getfeaturevalue | Não | Não |
> | operações | Não | Não |

## <a name="microsoftdatalake"></a>O. DATALAKE

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | datalakeaccounts | Não | Não |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |
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
> | accounts | Não | Não |
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
> | resourceoperationgatekeepers | Não | Não |

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
> | artifactsources | Não | Não |
> | operationresults | Não | Não |
> | operações | Não | Não |
> | rollouts | Não | Não |
> | servicetopologies | Não | Não |
> | servicetopologies/services | Não | Não |
> | servicetopologies/services/serviceunits | Não | Não |
> | etapas | Não | Não |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | applicationgroups | Não | Não |
> | applicationgroups/applications | Não | Não |
> | applicationgroups/desktops | Não | Não |
> | applicationgroups/startmenuitems | Não | Não |
> | hostpools | Não | Não |
> | hostpools/sessionhosts | Não | Não |
> | hostpools/sessionhosts/usersessions | Não | Não |
> | hostpools/usersessions | Não | Não |
> | operações | Não | Não |
> | workspaces | Não | Não |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Não | Não |
> | checkprovisioningservicenameavailability | Não | Não |
> | elasticpools | Sim | Sim |
> | elasticpools/iothubtenants | Sim | Sim |
> | iothubs | Sim | Sim |
> | iothubs/eventgridfilters | Não | Não |
> | iothubs/securitysettings | Não | Não |
> | operationresults | Não | Não |
> | operações | Não | Não |
> | provisioningservices | Sim | Sim |
> | usages | Não | Não |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | pipelines | Não | Não |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | controladores | Não | Não |
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
> | labs | Não | Não |
> | labs/environments | Não | Não |
> | labs/servicerunners | Não | Não |
> | labs / virtualmachines | Não | Não |
> | Locais | Não | Não |
> | locations/operations | Não | Não |
> | operações | Não | Não |
> | schedules | Não | Não |

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | digitaltwinsinstances | Não | Não |
> | digitaltwinsinstances/pontos de extremidade | Não | Não |
> | digitaltwinsinstances / operationresults | Não | Não |
> | Locais | Não | Não |
> | locations/checknameavailability | Não | Não |
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
> | services | Não | Não |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | domains | Não | Não |
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
> | partnernamespaces | Não | Não |
> | partnernamespaces/eventchannels | Não | Não |
> | partnerregistrations | Não | Não |
> | partnertopics | Não | Não |
> | partnertopics/eventsubscriptions | Não | Não |
> | systemtopics | Não | Não |
> | systemtopics/eventsubscriptions | Não | Não |
> | topics | Não | Não |
> | topictypes | Não | Não |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | availableclusterregions | Não | Não |
> | checknameavailability | Não | Não |
> | checknamespaceavailability | Não | Não |
> | clusters | Não | Não |
> | Locais | Não | Não |
> | locations/deletevirtualnetworkorsubnets | Não | Não |
> | namespaces | Não | Não |
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
> | namespaces | Não | Não |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | featureproviders | Não | Não |
> | recursos | Não | Não |
> | operações | Não | Não |
> | providers | Não | Não |
> | subscriptionfeatureregistrations | Não | Não |

## <a name="microsoftgenomics"></a>O. GENOMICS

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
> | sapmonitors | Não | Não |

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
> | services | Não | Não |
> | services/privateendpointconnections | Não | Não |
> | services/privatelinkresources | Não | Não |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | Locais | Não | Não |
> | locations/operationresults | Não | Não |
> | locations/operationstatus | Não | Não |
> | machines | Não | Não |
> | machines/extensions | Sim | Sim |
> | operações | Não | Não |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | datamanagers | Não | Não |
> | operações | Não | Não |

## <a name="microsofthybridnetwork"></a>Microsoft. HybridNetwork

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | dispositivos | Não | Não |
> | Locais | Não | Não |
> | locations/operationstatuses | Não | Não |
> | operações | Não | Não |
> | vnfs | Não | Não |

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
> | actiongroups | Não | Não |
> | activitylogalerts | Não | Não |
> | alertrules | Sim | Sim |
> | autoscalesettings | Sim | Sim |
> | baseline | Não | Não |
> | calculatebaseline | Não | Não |
> | components | Sim | Sim |
> | components/events | Não | Não |
> | components/linkedstorageaccounts | Não | Não |
> | componentes/metadados | Não | Não |
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
> | webtesters/testresultfile | Não | Não |
> | workbooks | Não | Não |
> | workbooktemplates | Não | Não |

> [!IMPORTANT]
> Verifique se mover para nova assinatura não excede as [cotas de assinatura](azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | apptemplates | Não | Não |
> | checknameavailability | Não | Não |
> | checksubdomainavailability | Não | Não |
> | iotapps | Não | Não |
> | operações | Não | Não |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Não | Não |
> | grafo | Não | Não |
> | operações | Não | Não |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Não | Não |
> | deletedvaults | Não | Não |
> | hsmpools | Sim | Sim |
> | Locais | Não | Não |
> | locations/deletedvaults | Não | Não |
> | locations/deletevirtualnetworkorsubnets | Não | Não |
> | locations/operationresults | Não | Não |
> | managedhsms | Não | Não |
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
> | connectedclusters | Não | Não |
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
> | clusters | Não | Não |
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

## <a name="microsoftlocationbasedservices"></a>O. LOCATIONBASEDSERVICES

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |

## <a name="microsoftlocationservices"></a>O. ARQUIVO LOCATIONSERVICES

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
> | integrationserviceenvironments | Não | Não |
> | integrationserviceenvironments/managedapis | Não | Não |
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
> | webservices | Não | Não |
> | workspaces | Não | Não |

## <a name="microsoftmachinelearningcompute"></a>O. MACHINELEARNINGCOMPUTE

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | operationalizationclusters | Não | Não |

## <a name="microsoftmachinelearningexperimentation"></a>O. MACHINELEARNINGEXPERIMENTATION

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |
> | accounts/workspaces | Não | Não |
> | accounts/workspaces/projects | Não | Não |
> | teamaccounts | Não | Não |
> | teamaccounts/workspaces | Não | Não |
> | teamaccounts/workspaces/projects | Não | Não |

## <a name="microsoftmachinelearningmodelmanagement"></a>O. MACHINELEARNINGMODELMANAGEMENT

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |

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
> | maintenanceconfigurations | Não | Não |
> | updates | Não | Não |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | identidades | Não | Não |
> | operações | Não | Não |
> | userassignedidentities | Não | Não |

## <a name="microsoftmanagednetwork"></a>O. MANAGEDNETWORK

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
> | accounts | Não | Não |
> | accounts/eventgridfilters | Não | Não |
> | accounts/privateatlases | Não | Não |
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
> | mediaservices | Não | Não |
> | mediaservices/accountfilters | Não | Não |
> | mediaservices/assets | Não | Não |
> | mediaservices/assets/assetfilters | Não | Não |
> | mediaservices/contentkeypolicies | Não | Não |
> | mediaservices/eventgridfilters | Não | Não |
> | mediaservices/liveeventoperations | Não | Não |
> | mediaservices/liveevents | Não | Não |
> | mediaservices/liveevents/liveoutputs | Não | Não |
> | mediaservices/liveoutputoperations | Não | Não |
> | mediaservices/streamingendpointoperations | Não | Não |
> | mediaservices/streamingendpoints | Não | Não |
> | mediaservices/streaminglocators | Não | Não |
> | mediaservices/streamingpolicies | Não | Não |
> | mediaservices/transforms | Não | Não |
> | mediaservices/transforms/jobs | Não | Não |
> | operações | Não | Não |

## <a name="microsoftmicroservices4spring"></a>O. MICROSERVICES4SPRING

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | appclusters | Não | Não |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | assessmentprojects | Não | Não |
> | Locais | Não | Não |
> | locations/assessmentoptions | Não | Não |
> | locations/checknameavailability | Não | Não |
> | migrateprojects | Não | Não |
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
> | remoterenderingaccounts | Não | Não |
> | spatialanchorsaccounts | Não | Não |

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
> | applicationsecuritygroups | Não | Não |
> | azurefirewallfqdntags | Não | Não |
> | azurefirewalls | Não | Não |
> | bastionhosts | Não | Não |
> | bgpservicecommunities | Não | Não |
> | checkfrontdoornameavailability | Não | Não |
> | checktrafficmanagernameavailability | Não | Não |
> | connections | Não | Não |
> | ddoscustompolicies | Não | Não |
> | ddosprotectionplans | Não | Não |
> | dnsoperationresults | Não | Não |
> | dnsoperationstatuses | Não | Não |
> | dnszones | Não | Não |
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
> | firewallpolicies | Não | Não |
> | frontdooroperationresults | Não | Não |
> | frontdoors | Não | Não |
> | frontdoors / frontendendpoints | Não | Não |
> | frontdoorwebapplicationfirewallmanagedrulesets | Não | Não |
> | frontdoorwebapplicationfirewallpolicies | Não | Não |
> | getdnsresourcereference | Não | Não |
> | internalnotify | Não | Não |
> | ipallocations | Não | Não |
> | ipgroups | Não | Não |
> | loadbalancers | Sim - SKU Básico<br>Não - SKU Standard | Sim - SKU Básico<br>Não - SKU Standard |
> | localnetworkgateways | Não | Não |
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
> | natgateways | Não | Não |
> | networkexperimentprofiles | Não | Não |
> | networkintentpolicies | Não | Não |
> | networkinterfaces | Não | Não |
> | networkprofiles | Não | Não |
> | networksecuritygroups | Não | Não |
> | networkwatchers | Sim | Não |
> | networkwatchers/connectionmonitors | Sim | Não |
> | networkwatchers/flowlogs | Sim | Não |
> | networkwatchers/pingmeshes | Sim | Não |
> | operações | Não | Não |
> | p2svpngateways | Não | Não |
> | privatednsoperationresults | Não | Não |
> | privatednsoperationstatuses | Não | Não |
> | privatednszones | Não | Não |
> | privatednszones/a | Não | Não |
> | privatednszones/aaaa | Não | Não |
> | privatednszones/all | Não | Não |
> | privatednszones/cname | Não | Não |
> | privatednszones/mx | Não | Não |
> | privatednszones/ptr | Não | Não |
> | privatednszones/soa | Não | Não |
> | privatednszones/srv | Não | Não |
> | privatednszones/txt | Não | Não |
> | privatednszones/virtualnetworklinks | Não | Não |
> | privatednszonesinternal | Não | Não |
> | privateendpointredirectmaps | Não | Não |
> | privateendpoints | Não | Não |
> | privatelinkservices | Não | Não |
> | publicipaddresses | Sim - SKU Básico<br>Não - SKU Standard | Sim - SKU Básico<br>Não - SKU Standard |
> | publicipprefixes | Não | Não |
> | routefilters | Não | Não |
> | routetables | Não | Não |
> | securitypartnerproviders | Não | Não |
> | serviceendpointpolicies | Não | Não |
> | trafficmanagergeographichierarchies | Não | Não |
> | trafficmanagerprofiles | Não | Não |
> | trafficmanagerprofiles/heatmaps | Não | Não |
> | trafficmanagerusermetricskeys | Não | Não |
> | virtualhubs | Não | Não |
> | virtualnetworkgateways | Não | Não |
> | virtualnetworks | Não | Não |
> | virtualnetworktaps | Não | Não |
> | virtualrouters | Não | Não |
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
> | namespaces | Não | Não |
> | namespaces/notificationhubs | Não | Não |
> | operationresults | Não | Não |
> | operações | Não | Não |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | osnamespaces | Não | Não |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hypervsites | Não | Não |
> | importsites | Não | Não |
> | operações | Não | Não |
> | serversites | Não | Não |
> | vmwaresites | Não | Não |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | Sim | Sim |
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
> | managementconfigurations | Não | Não |
> | operações | Não | Não |
> | solutions | Não | Não |
> | Modos de exibição | Não | Não |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checkserviceprovideravailability | Não | Não |
> | legacypeerings | Não | Não |
> | operações | Não | Não |
> | peerasns | Não | Não |
> | peeringlocations | Não | Não |
> | peerings | Não | Não |
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
> | dashboards | Não | Não |
> | Locais | Não | Não |
> | locations/consoles | Não | Não |
> | locations/usersettings | Não | Não |
> | operações | Não | Não |
> | usersettings | Não | Não |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | Locais | Não | Não |
> | locations/checknameavailability | Não | Não |
> | operações | Não | Não |
> | privatelinkservicesforpowerbi | Não | Não |
> | privatelinkservicesforpowerbi / operationresults | Não | Não |
> | tenants | Não | Não |
> | workspacecollections | Não | Não |

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

## <a name="microsoftpowerplatform"></a>Microsoft. PowerPlatform

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | operações | Não | Não |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |
> | checknameavailability | Não | Não |
> | operações | Não | Não |

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | availableaccounts | Não | Não |
> | providerregistrations | Não | Não |
> | providerregistrations/condistribuições | Não | Não |
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
> | namespaces | Não | Não |
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
> | calculatetemplatehash | Não | Não |
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
> | de dimensionamento da Web | Não | Não |
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
> | alertssuppressionrules | Não | Não |
> | allowedconnections | Não | Não |
> | applicationwhitelistings | Não | Não |
> | assessmentmetadata | Não | Não |
> | assessments | Não | Não |
> | autodismissalertsrules | Não | Não |
> | automations | Não | Não |
> | autoprovisioningsettings | Não | Não |
> | complianceresults | Não | Não |
> | compliances | Não | Não |
> | datacollectionagents | Não | Não |
> | devicesecuritygroups | Não | Não |
> | discoveredsecuritysolutions | Não | Não |
> | externalsecuritysolutions | Não | Não |
> | informationprotectionpolicies | Não | Não |
> | iotsecuritysolutions | Não | Não |
> | iotsecuritysolutions/analyticsmodels | Não | Não |
> | iotsecuritysolutions/analyticsmodels/aggregatedalerts | Não | Não |
> | iotsecuritysolutions/analyticsmodels/aggregatedrecommendations | Não | Não |
> | iotsecuritysolutions / iotalerts | Não | Não |
> | iotsecuritysolutions / iotalerttypes | Não | Não |
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
> | securescorecontroldefinitions | Não | Não |
> | securescorecontrols | Não | Não |
> | securescores | Não | Não |
> | securescores / securescorecontrols | Não | Não |
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
> | automationrules | Não | Não |
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

## <a name="microsoftservermanagement"></a>O. SERVERMANAGEMENT

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
> | namespaces | Não | Não |
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
> | clusters | Não | Não |
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
> | de dimensionamento da Web | Não | Não |
> | containergroups | Não | Não |
> | gateways | Não | Não |
> | Locais | Não | Não |
> | locations/applicationoperations | Não | Não |
> | locations/gatewayoperations | Não | Não |
> | locations/networkoperations | Não | Não |
> | locations/secretoperations | Não | Não |
> | locations/volumeoperations | Não | Não |
> | networks | Não | Não |
> | operações | Não | Não |
> | segredos | Não | Não |
> | volumes | Não | Não |

## <a name="microsoftservices"></a>O. SERVIÇOS

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
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
> | servers | Não | Não |
> | servers/administratoroperationresults | Não | Não |
> | servers/administrators | Não | Não |
> | servers/advisors | Não | Não |
> | servers/aggregateddatabasemetrics | Não | Não |
> | servers/auditingsettings | Não | Não |
> | servers/automatictuning | Não | Não |
> | servers/communicationlinks | Não | Não |
> | servers/databases | Não | Não |
> | servers/databases/advisors | Não | Não |
> | servers/databases/auditingsettings | Não | Não |
> | servers/databases/auditrecords | Não | Não |
> | servers/databases/automatictuning | Não | Não |
> | servers/databases/backuplongtermretentionpolicies | Não | Não |
> | servers/databases/backupshorttermretentionpolicies | Não | Não |
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
> | servers/elasticpools | Não | Não |
> | servers/elasticpools/advisors | Não | Não |
> | servers/elasticpools/metricdefinitions | Não | Não |
> | servers/elasticpools/metrics | Não | Não |
> | servers/encryptionprotector | Não | Não |
> | servers/extendedauditingsettings | Não | Não |
> | servers/failovergroups | Não | Não |
> | servers/import | Não | Não |
> | servers/importexportoperationresults | Não | Não |
> | servers/jobaccounts | Não | Não |
> | servers/jobagents | Não | Não |
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
> | virtualclusters | Não | Não |

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

## <a name="microsoftstoragecache"></a>O. STORAGECACHE

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
> | locations/operationresults | Não | Não |
> | locations/operations | Não | Não |
> | locations/workflows | Não | Não |
> | operações | Não | Não |
> | storagesyncservices | Não | Não |
> | storagesyncservices/registeredservers | Não | Não |
> | storagesyncservices/syncgroups | Não | Não |
> | storagesyncservices/syncgroups/cloudendpoints | Não | Não |
> | storagesyncservices/syncgroups/serverendpoints | Não | Não |
> | storagesyncservices/workflows | Não | Não |

## <a name="microsoftstoragesyncdev"></a>O. STORAGESYNCDEV

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Não | Não |

## <a name="microsoftstoragesyncint"></a>O. STORAGESYNCINT

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
> | clusters | Sim | Sim |
> | Locais | Não | Não |
> | locations/quotas | Não | Não |
> | operações | Não | Não |
> | streamingjobs | Sim | Sim |

> [!IMPORTANT]
> Os trabalhos do Stream Analytics não podem ser movidos durante o estado de execução.

## <a name="microsoftstreamanalyticsexplorer"></a>O. STREAMANALYTICSEXPLORER

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
> | privatelinkhubs | Não | Não |
> | workspaces | Não | Não |
> | workspaces/bigdatapools | Não | Não |
> | workspaces/operationresults | Não | Não |
> | workspaces/operationstatuses | Não | Não |
> | workspaces/sqlpools | Não | Não |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | environments | Não | Não |
> | environments/accesspolicies | Não | Não |
> | environments/eventsources | Não | Não |
> | environments/referencedatasets | Não | Não |
> | operações | Não | Não |

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | stores | Não | Não |
> | stores/accesspolicies | Não | Não |
> | stores/services | Não | Não |
> | stores/services/tokens | Não | Não |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | imagetemplates | Não | Não |
> | imagetemplates / runoutputs | Não | Não |
> | Locais | Não | Não |
> | locations/operations | Não | Não |
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
> | registeredsubscriptions | Não | Não |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | availablestacks | Não | Não |
> | billingmeters | Não | Não |
> | certificates | Não | Não |
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
> | kubeenvironments | Não | Não |
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
> | sites/premieraddons | Não | Não |
> | sites / slots | Não | Não |
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

## <a name="microsoftworkloadbuilder"></a>Microsoft. WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | Locais | Não | Não |
> | locations/operationstatuses | Não | Não |
> | operações | Não | Não |
> | cargas de trabalho | Não | Não |

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
