---
title: Exclusão completa de modo
description: Mostra como os tipos de recurso lidam com a exclusão de modo completo em modelos do Azure Resource Manager.
ms.topic: conceptual
ms.date: 10/27/2019
ms.openlocfilehash: e8e473ba697dd64d2562a2e1efc03f1ba5b384e6
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149933"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>Exclusão de recursos do Azure para implantações do modo completo

Este artigo descreve como os tipos de recurso lidam com a exclusão quando ela não ocorre em um modelo implantado no modo completo.

Os tipos de recurso marcados com **Sim** são excluídos quando o tipo não está no modelo implantado com o modo completo.

Os tipos de recurso marcados **sem não são excluídos** automaticamente quando não estão no modelo; no entanto, elas serão excluídas se o recurso pai for excluído. Para obter uma descrição completa do comportamento, consulte [Modos de implantação do Azure Resource Manager](deployment-modes.md).

Se você implantar em [mais de um grupo de recursos em um modelo](resource-manager-cross-resource-group-deployment.md), os recursos no grupo de recursos especificado na operação de implantação poderão ser excluídos. Os recursos nos grupos de recursos secundários não são excluídos.

Ir para um namespace do provedor de recursos:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [Microsoft. addons](#microsoftaddons)
> - [Microsoft. ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft. Advisor](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft. AppPlatform](#microsoftappplatform)
> - [Microsoft. atestador](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft. Azconfig](#microsoftazconfig)
> - [Microsoft. Azure. Geneva](#microsoftazuregeneva)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft. AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft. billing](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft. Capacity](#microsoftcapacity)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft. ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft. Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft. consumo](#microsoftconsumption)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft. CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft. CustomProviders](#microsoftcustomproviders)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [Microsoft. DataShare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft. DesktopVirtualization](#microsoftdesktopvirtualization)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft. DevOps](#microsoftdevops)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft. DynamicsLcs](#microsoftdynamicslcs)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft. Features](#microsoftfeatures)
> - [Microsoft. Gallery](#microsoftgallery)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft. HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft. Hydra](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [Microsoft. Intune](#microsoftintune)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft. Managedservices](#microsoftmanagedservices)
> - [Microsoft. Management](#microsoftmanagement)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft. Marketplace](#microsoftmarketplace)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft. MarketplaceOrdering](#microsoftmarketplaceordering)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft. Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft. MixedReality](#microsoftmixedreality)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft. ObjectStore](#microsoftobjectstore)
> - [Microsoft. OffAzure](#microsoftoffazure)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft. RemoteApp](#microsoftremoteapp)
> - [Microsoft. ResourceGraph](#microsoftresourcegraph)
> - [Microsoft. ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Scheduler](#microsoftscheduler)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft. SecurityGraph](#microsoftsecuritygraph)
> - [Microsoft. SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft. Services](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SiteRecovery](#microsoftsiterecovery)
> - [Microsoft. SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft. SQL](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft. StorageReplication](#microsoftstoragereplication)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft. Subscription](#microsoftsubscription)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft. WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft. WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | DomainServices | sim |
> | DomainServices/oucontainer | Não |
> | DomainServices/ReplicaSets | sim |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | supportProviders | Não |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | aadsupportcases | Não |
> | addsservices | Não |
> | agentes | Não |
> | anonymousapiusers | Não |
> | configuração | Não |
> | logs | Não |
> | relatórios | Não |
> | servicehealthmetrics | Não |
> | services | Não |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | configurações | Não |
> | generateRecommendations | Não |
> | metadata | Não |
> | filmes | Não |
> | suppressions | Não |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | actionRules | sim |
> | alertas | Não |
> | alertsList | Não |
> | alertsMetaData | Não |
> | alertsSummary | Não |
> | alertsSummaryList | Não |
> | Receber | Não |
> | smartDetectorAlertRules | sim |
> | smartDetectorRuntimeEnvironments | Não |
> | smartGroups | Não |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | servers | sim |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | reportFeedback | Não |
> | serviço | sim |
> | validateServiceName | Não |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | configurationStores | sim |
> | configurationStores/eventGridFilters | Não |

## <a name="microsoftappplatform"></a>Microsoft. AppPlatform

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | Spring | sim |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | attestationProviders | Não |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | classicAdministrators | Não |
> | Aliases de dataalias | Não |
> | denyAssignments | Não |
> | elevateAccess | Não |
> | findOrphanRoleAssignments | Não |
> | locks | Não |
> | permissões | Não |
> | policyAssignments | Não |
> | policyDefinitions | Não |
> | policySetDefinitions | Não |
> | providerOperations | Não |
> | roleAssignments | Não |
> | roleDefinitions | Não |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | automationaccounts | sim |
> | automationAccounts/configurations | sim |
> | automationAccounts/runbooks | Não |
> | automationAccounts/runbooks | sim |
> | automationAccounts/configurations | Não |
> | automationAccounts/runbooks | Não |

## <a name="microsoftazconfig"></a>Microsoft. Azconfig

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | configurationStores | sim |
> | configurationStores/eventGridFilters | Não |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | environments | Não |
> | environments/accounts | Não |
> | environments/accounts/namespaces | Não |
> | environments/accounts/namespaces/configurations | Não |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | b2cDirectories | sim |
> | b2ctenants | Não |

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | hybridDataManagers | sim |
> | postgresInstances | sim |
> | sqlBigDataClusters | sim |
> | SQLInstances | sim |
> | sqlServerRegistrations | sim |
> | sqlServerRegistrations/sqlservers | Não |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | registrations | sim |
> | registrations/customerSubscriptions | Não |
> | registrations/products | Não |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | batchAccounts | sim |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | billingAccounts | Não |
> | billingAccounts/contratos | Não |
> | billingAccounts/billingPermissions | Não |
> | billingAccounts/billingProfiles | Não |
> | billingAccounts/billingProfiles/billingPermissions | Não |
> | billingAccounts/billingProfiles/billingRoleAssignments | Não |
> | billingAccounts/billingProfiles/billingRoleDefinitions | Não |
> | billingAccounts/billingProfiles/billingSubscriptions | Não |
> | billingAccounts/billingProfiles/createBillingRoleAssignment | Não |
> | billingAccounts/billingProfiles/clientes | Não |
> | billingAccounts/billingProfiles/invoices | Não |
> | billingAccounts/billingProfiles/invoices/pricesheet | Não |
> | billingAccounts/billingProfiles/invoiceSections | Não |
> | billingAccounts/billingProfiles/invoiceSections/billingPermissions | Não |
> | billingAccounts/billingProfiles/invoiceSections/billingRoleAssignments | Não |
> | billingAccounts/billingProfiles/invoiceSections/billingRoleDefinitions | Não |
> | billingAccounts/billingProfiles/invoiceSections/billingSubscriptions | Não |
> | billingAccounts/billingProfiles/invoiceSections/createBillingRoleAssignment | Não |
> | billingAccounts/billingProfiles/invoiceSections/initiateTransfer | Não |
> | billingAccounts/billingProfiles/invoiceSections/produtos | Não |
> | billingAccounts/billingProfiles/invoiceSections/produtos/transferência | Não |
> | billingAccounts/billingProfiles/invoiceSections/Products/updateAutoRenew | Não |
> | billingAccounts/billingProfiles/invoiceSections/transações | Não |
> | billingAccounts/billingProfiles/invoiceSections/transferências | Não |
> | billingAccounts/BillingProfiles/patchOperations | Não |
> | billingAccounts/billingProfiles/paymentMethods | Não |
> | billingAccounts/billingProfiles/policies | Não |
> | billingAccounts/billingProfiles/pricesheet | Não |
> | billingAccounts/billingProfiles/pricesheetDownloadOperations | Não |
> | billingAccounts/billingProfiles/products | Não |
> | billingAccounts/billingProfiles/transactions | Não |
> | billingAccounts/billingRoleAssignments | Não |
> | billingAccounts/billingRoleDefinitions | Não |
> | billingAccounts/billingSubscriptions | Não |
> | billingAccounts/createBillingRoleAssignment | Não |
> | billingAccounts/createInvoiceSectionOperations | Não |
> | billingAccounts/clientes | Não |
> | billingAccounts/clientes/billingPermissions | Não |
> | billingAccounts/clientes/billingSubscriptions | Não |
> | billingAccounts/clientes/initiateTransfer | Não |
> | billingAccounts/clientes/políticas | Não |
> | billingAccounts/clientes/produtos | Não |
> | billingAccounts/clientes/transações | Não |
> | billingAccounts/clientes/transferências | Não |
> | billingAccounts/departments | Não |
> | billingAccounts/enrollmentAccounts | Não |
> | billingAccounts/invoices | Não |
> | billingAccounts/invoiceSections | Não |
> | billingAccounts/invoiceSections/billingSubscriptionMoveOperations | Não |
> | billingAccounts/invoiceSections/billingSubscriptions | Não |
> | billingAccounts/invoiceSections/billingSubscriptions/transfer | Não |
> | billingAccounts/invoiceSections/Elevate | Não |
> | billingAccounts/invoiceSections/initiateTransfer | Não |
> | billingAccounts/invoiceSections/patchOperations | Não |
> | billingAccounts/invoiceSections/productMoveOperations | Não |
> | billingAccounts/invoiceSections/products | Não |
> | billingAccounts/invoiceSections/produtos/transferência | Não |
> | billingAccounts/invoiceSections/Products/updateAutoRenew | Não |
> | billingAccounts/invoiceSections/transações | Não |
> | billingAccounts/invoiceSections/transfers | Não |
> | billingAccounts/lineOfCredit | Não |
> | billingAccounts/patchOperations | Não |
> | billingAccounts/paymentMethods | Não |
> | billingAccounts/products | Não |
> | billingAccounts/transactions | Não |
> | billingPeriods | Não |
> | billingPermissions | Não |
> | billingProperty | Não |
> | billingRoleAssignments | Não |
> | billingRoleDefinitions | Não |
> | createBillingRoleAssignment | Não |
> | departments | Não |
> | enrollmentAccounts | Não |
> | invoices | Não |
> | transfers | Não |
> | transfers/acceptTransfer | Não |
> | transfers/declineTransfer | Não |
> | transfers/operationStatus | Não |
> | transferências/validateTransfer | Não |
> | validateAddress | Não |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | mapApis | sim |
> | updateCommunicationPreference | Não |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | blockchainMembers | sim |
> | cordaMembers | sim |
> | espectadores | sim |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | blueprintAssignments | Não |
> | blueprintAssignments/assignmentOperations | Não |
> | blueprintAssignments/operations | Não |
> | blueprints | Não |
> | blueprints/artifacts | Não |
> | blueprints/versions | Não |
> | blueprints/versions/artifacts | Não |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | botServices | sim |
> | botServices/channels | Não |
> | botServices/connections | Não |
> | idiomas | Não |
> | modelos | Não |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | Redis | sim |
> | RedisConfigDefinition | Não |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | appliedReservations | Não |
> | calculateExchange | Não |
> | calculatePrice | Não |
> | calculatePurchasePrice | Não |
> | catalogs | Não |
> | commercialReservationOrders | Não |
> | câmbio | Não |
> | placePurchaseOrder | Não |
> | reservationOrders | Não |
> | reservationOrders/calculateRefund | Não |
> | reservationOrders/merge | Não |
> | reservationOrders/reservations | Não |
> | reservationOrders/reservations/revisions | Não |
> | reservationOrders/return | Não |
> | reservationOrders/split | Não |
> | reservationOrders/swap | Não |
> | reservations | Não |
> | recursos | Não |
> | validateReservationOrder | Não |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | Não |
> | CdnWebApplicationFirewallPolicies | sim |
> | edgenodes | Não |
> | profiles | sim |
> | profiles/endpoints | sim |
> | profiles/endpoints/origins | Não |
> | profiles/endpoints/origins | Não |
> | validateProbe | Não |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | certificateOrders | sim |
> | certificateOrders/certificates | Não |
> | validateCertificateRegistrationInformation | Não |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | recursos | Não |
> | domainNames | sim |
> | domainNames/capabilities | Não |
> | domainNames/internalLoadBalancers | Não |
> | domainNames/serviceCertificates | Não |
> | domainNames/slots | Não |
> | domainNames/slots | Não |
> | nome_do_domínio/Slots/funções/metricDefinitions | Não |
> | DomainNames/Slots/funções/métricas | Não |
> | moveSubscriptionResources | Não |
> | operatingSystemFamilies | Não |
> | operatingSystems | Não |
> | quotas | Não |
> | resourceTypes | Não |
> | validateSubscriptionMoveAvailability | Não |
> | virtualMachines | sim |
> | virtualMachines/diagnosticSettings | Não |
> | virtualMachines/metricDefinitions | Não |
> | virtualMachines/metrics | Não |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | Migração de infraestrutura clássica | Não |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | recursos | Não |
> | expressRouteCrossConnections | Não |
> | expressRouteCrossConnections/peerings | Não |
> | gatewaySupportedDevices | Não |
> | networkSecurityGroups | sim |
> | quotas | Não |
> | reservedIps | sim |
> | virtualNetworks | sim |
> | virtualNetworks/virtualNetworkPeerings | Não |
> | virtualNetworks/virtualNetworkPeerings | Não |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | recursos | Não |
> | disks | Não |
> | images | Não |
> | osImages | Não |
> | osPlatformImages | Não |
> | publicImages | Não |
> | quotas | Não |
> | storageAccounts | sim |
> | storageAccounts/blobServices | Não |
> | storageAccounts/blobServices | Não |
> | storageAccounts/metricDefinitions | Não |
> | storageAccounts/métricas | Não |
> | storageAccounts/blobServices | Não |
> | storageAccounts/services | Não |
> | storageAccounts/services/diagnosticSettings | Não |
> | storageAccounts/services/metricDefinitions | Não |
> | storageAccounts/serviços/métricas | Não |
> | storageAccounts/blobServices | Não |
> | storageAccounts/vmImages | Não |
> | vmImages | Não |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | accounts | sim |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | RateCard | Não |
> | UsageAggregates | Não |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | availabilitySets | sim |
> | diskEncryptionSets | sim |
> | disks | sim |
> | galleries | sim |
> | galerias/aplicativos | Não |
> | galerias/aplicativos/versões | Não |
> | galleries/images | Não |
> | galleries/images/versions | Não |
> | Hosts | sim |
> | Hosts/hosts | sim |
> | images | sim |
> | proximityPlacementGroups | sim |
> | restorePointCollections | sim |
> | restorePointCollections/restorePoints | Não |
> | sharedVMExtensions | sim |
> | sharedVMExtensions/versões | Não |
> | sharedVMImages | sim |
> | sharedVMImages/versions | Não |
> | snapshots | sim |
> | virtualMachines | sim |
> | virtualMachines/extensions | sim |
> | virtualMachines/metricDefinitions | Não |
> | virtualMachineScaleSets | sim |
> | virtualMachines/extensions | Não |
> | virtualMachineScaleSets/networkInterfaces | Não |
> | virtualMachineScaleSets/publicIPAddresses | Não |
> | virtualMachineScaleSets/virtualMachines | Não |
> | virtualMachineScaleSets/networkInterfaces | Não |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | AggregatedCost | Não |
> | Saldos | Não |
> | Orçamentos | Não |
> | Charges | Não |
> | CostTags | Não |
> | credits | Não |
> | events | Não |
> | Previsões | Não |
> | lots | Não |
> | Marketplaces | Não |
> | Pricesheets | Não |
> | products | Não |
> | ReservationDetails | Não |
> | ReservationRecommendations | Não |
> | ReservationSummaries | Não |
> | ReservationTransactions | Não |
> | Marcas | Não |
> | tenants | Não |
> | Termos | Não |
> | UsageDetails | Não |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | containerGroups | sim |
> | serviceAssociationLinks | Não |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | registries | sim |
> | registries/builds | Não |
> | registries/builds/cancel | Não |
> | registries/builds/getLogLink | Não |
> | registries/buildTasks | sim |
> | registries/buildTasks/steps | Não |
> | registries/eventGridFilters | Não |
> | registros/generateCredentials | Não |
> | registries/getBuildSourceUploadUrl | Não |
> | registries/GetCredentials | Não |
> | registries/importImage | Não |
> | registries/queueBuild | Não |
> | registries/regenerateCredential | Não |
> | registries/regenerateCredentials | Não |
> | registries/replications | sim |
> | registries/runs | Não |
> | registries/runs/cancel | Não |
> | registries/scheduleRun | Não |
> | registros/scopeMaps | Não |
> | registries/tasks | sim |
> | registros/tokens | Não |
> | registries/updatePolicies | Não |
> | registries/webhooks | sim |
> | registries/webhooks/getCallbackConfig | Não |
> | registries/webhooks/ping | Não |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | containerServices | sim |
> | managedClusters | sim |
> | openShiftManagedClusters | sim |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | accounts | sim |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | Alertas | Não |
> | BillingAccounts | Não |
> | Orçamentos | Não |
> | CloudConnectors | Não |
> | Conectores | sim |
> | Departments | Não |
> | Dimensões | Não |
> | EnrollmentAccounts | Não |
> | Exportações | Não |
> | ExternalBillingAccounts | Não |
> | ExternalBillingAccounts/alertas | Não |
> | ExternalBillingAccounts/dimensões | Não |
> | ExternalBillingAccounts/previsão | Não |
> | ExternalBillingAccounts/consulta | Não |
> | ExternalSubscriptions | Não |
> | ExternalSubscriptions/alertas | Não |
> | ExternalSubscriptions/dimensões | Não |
> | ExternalSubscriptions/previsão | Não |
> | ExternalSubscriptions/consulta | Não |
> | Visto | Não |
> | Consultar | Não |
> | register | Não |
> | Reportconfigs | Não |
> | Relatórios | Não |
> | Configurações | Não |
> | showbackRules | Não |
> | Modos de exibição | Não |

## <a name="microsoftcustomerlockbox"></a>Microsoft. CustomerLockbox

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | solicitações | Não |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | SAS | Não |
> | resourceProviders | sim |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | jobs | sim |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | DataBoxEdgeDevices | sim |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | workspaces | sim |
> | workspaces/virtualNetworkPeerings | Não |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | catalogs | sim |
> | catálogos de | sim |
> | datacatalogs/fontes de fonte | Não |
> | datacatalogs/fontes de pesquisa/verificações | Não |
> | datacatalogs/fontes/exames/conjuntos de valores | Não |
> | datacatalogs/fontes/exames/gatilhos | Não |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | dataFactories | sim |
> | dataFactories/diagnosticSettings | Não |
> | dataFactories/metricDefinitions | Não |
> | dataFactorySchema | Não |
> | factories | sim |
> | factories/integrationRuntimes | Não |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | accounts | sim |
> | accounts/dataLakeStoreAccounts | Não |
> | accounts/storageAccounts | Não |
> | accounts/storageAccounts/containers | Não |
> | contas/transferAnalyticsUnits | Não |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | accounts | sim |
> | accounts/eventGridFilters | Não |
> | accounts/firewallRules | Não |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | services | sim |
> | services/projects | sim |

## <a name="microsoftdatashare"></a>Microsoft. DataShare

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | accounts | sim |
> | contas/compartilhamentos | Não |
> | contas/compartilhamentos/conjuntos de valores | Não |
> | contas/compartilhamentos/convites | Não |
> | contas/compartilhamentos/providersharesubscriptions | Não |
> | contas/compartilhamentos/synchronizationSettings | Não |
> | contas/sharesubscriptions | Não |
> | contas/sharesubscriptions/consumerSourceDataSets | Não |
> | contas/sharesubscriptions/datasetmappings | Não |
> | contas/sharesubscriptions/gatilhos | Não |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | servers | sim |
> | servers/advisors | Não |
> | servidores/privateEndpointConnectionProxies | Não |
> | servidores/privateEndpointConnections | Não |
> | servidores/privateLinkResources | Não |
> | servers/queryTexts | Não |
> | servers/recoverableServers | Não |
> | servers/topQueryStatistics | Não |
> | servers/virtualNetworkRules | Não |
> | servers/waitStatistics | Não |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | servers | sim |
> | servers/advisors | Não |
> | servidores/privateEndpointConnectionProxies | Não |
> | servidores/privateEndpointConnections | Não |
> | servidores/privateLinkResources | Não |
> | servers/queryTexts | Não |
> | servers/recoverableServers | Não |
> | servers/topQueryStatistics | Não |
> | servers/virtualNetworkRules | Não |
> | servers/waitStatistics | Não |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | serverGroups | sim |
> | servers | sim |
> | servers/advisors | Não |
> | servers/keys | Não |
> | servidores/privateEndpointConnectionProxies | Não |
> | servidores/privateEndpointConnections | Não |
> | servidores/privateLinkResources | Não |
> | servers/queryTexts | Não |
> | servers/recoverableServers | Não |
> | servers/topQueryStatistics | Não |
> | servers/virtualNetworkRules | Não |
> | servers/waitStatistics | Não |
> | serversv2 | sim |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | artifactSources | sim |
> | rollouts | sim |
> | pertopologias | sim |
> | serviços e pertopologias | sim |
> | pertopologias/serviços/unidades de serviço | sim |
> | etapas | sim |

## <a name="microsoftdesktopvirtualization"></a>Microsoft. DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | applicationgroups | sim |
> | applicationgroups/aplicativos | Não |
> | applicationgroups/desktops | Não |
> | applicationgroups/startmenuitems | Não |
> | hostpools | sim |
> | hostpools/sessionhosts | Não |
> | hostpools/sessionhosts/usersessions | Não |
> | hostpools/usersessions | Não |
> | workspaces | sim |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | ElasticPools | sim |
> | ElasticPools/IotHubTenants | sim |
> | IotHubs | sim |
> | IotHubs/eventGridFilters | Não |
> | ProvisioningServices | sim |
> | usages | Não |

## <a name="microsoftdevops"></a>Microsoft. DevOps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | pipelines | sim |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | controladores | sim |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | labcenters | sim |
> | labs | sim |
> | laboratórios/ambientes | sim |
> | labs/serviceRunners | sim |
> | labs/virtualMachines | sim |
> | schedules | sim |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | databaseAccounts | Não |
> | databaseAccounts | sim |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | domains | sim |
> | domains/domainOwnershipIdentifiers | Não |
> | generateSsoRequest | Não |
> | topLevelDomains | Não |
> | validateDomainRegistrationInformation | Não |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | lcsprojects | Não |
> | lcsprojects/clouddeployments | Não |
> | lcsprojects/connectors | Não |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | services | sim |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | domains | sim |
> | domains/topics | Não |
> | eventSubscriptions | Não |
> | extensionTopics | Não |
> | topics | sim |
> | topicTypes | Não |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | clusters | sim |
> | namespaces | sim |
> | namespaces/authorizationrules | Não |
> | namespaces/disasterrecoveryconfigs | Não |
> | namespaces/eventhubs | Não |
> | namespaces/eventhubs/authorizationrules | Não |
> | namespaces/eventhubs/consumergroups | Não |
> | namespaces/networkrulesets | Não |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | recursos | Não |
> | providers | Não |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | enroll | Não |
> | galleryitems | Não |
> | generateartifactaccessuri | Não |
> | myareas | Não |
> | myareas/areas | Não |
> | myareas/areas/areas | Não |
> | myareas/areas/areas/galleryitems | Não |
> | myareas/areas/galleryitems | Não |
> | myareas/galleryitems | Não |
> | register | Não |
> | recursos | Não |
> | retrieveresourcesbyid | Não |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | accounts | sim |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | autoManagedVmConfigurationProfiles | sim |
> | configurationProfileAssignments | Não |
> | guestConfigurationAssignments | Não |
> | software | Não |
> | softwareUpdateProfile | Não |
> | softwareUpdates | Não |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | hanaInstances | sim |
> | sapMonitors | sim |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | dedicatedHSMs | sim |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | clusters | sim |
> | clusters/applications | Não |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | services | sim |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | maquina | sim |
> | máquinas/extensões | sim |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | Gerenciadores de DataManager | sim |

## <a name="microsofthydra"></a>Microsoft. Hydra

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | components | sim |
> | networkScopes | sim |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | jobs | sim |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | diagnosticSettings | Não |
> | diagnosticSettingsCategories | Não |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | appTemplates | Não |
> | IoTApps | sim |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | Grafo | sim |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | deletedVaults | Não |
> | hsmPools | sim |
> | vaults | sim |
> | vaults/accessPolicies | Não |
> | cofres/eventGridFilters | Não |
> | vaults/secrets | Não |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | clusters | sim |
> | clusters/attacheddatabaseconfigurations | Não |
> | clusters/databases | Não |
> | clusters/databases/dataconnections | Não |
> | clusters/databases/eventhubconnections | Não |
> | clusters/sharedidentities | Não |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | labaccounts | sim |
> | users | Não |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | hostingEnvironments | sim |
> | integrationAccounts | sim |
> | integrationServiceEnvironments | sim |
> | integrationServiceEnvironments/managedApis | sim |
> | isolatedEnvironments | sim |
> | workflows | sim |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | commitmentPlans | sim |
> | webServices | sim |
> | Workspaces | sim |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | workspaces | sim |
> | workspaces/computes | Não |
> | espaços de trabalho/eventGridFilters | Não |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | Identidades | Não |
> | userAssignedIdentities | sim |

## <a name="microsoftmanagedservices"></a>Microsoft. Managedservices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | marketplaceRegistrationDefinitions | Não |
> | registrationAssignments | Não |
> | registrationDefinitions | Não |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | getEntities | Não |
> | managementGroups | Não |
> | recursos | Não |
> | startTenantBackfill | Não |
> | tenantBackfillStatus | Não |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | accounts | sim |
> | accounts/eventGridFilters | Não |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | offers | Não |
> | offerTypes | Não |
> | offerTypes/publishers | Não |
> | offerTypes/publishers/offers | Não |
> | offerTypes/publishers/offers/plans | Não |
> | offerTypes/publishers/offers/plans/agreements | Não |
> | offerTypes/publishers/offers/plans/configs | Não |
> | offerTypes/publishers/offers/plans/configs/importImage | Não |
> | privategalleryitems | Não |
> | products | Não |
> | publicado | Não |
> | editores/ofertas | Não |
> | editores/ofertas/emendas | Não |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | classicDevServices | sim |
> | updateCommunicationPreference | Não |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | Contratos | Não |
> | offertypes | Não |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | mediaservices | sim |
> | mediaservices/accountFilters | Não |
> | mediaservices/assets | Não |
> | mediaservices/assets/assetFilters | Não |
> | mediaservices/contentKeyPolicies | Não |
> | mediaservices/eventGridFilters | Não |
> | mediaservices/liveEvents | Não |
> | mediaservices/liveEvents | sim |
> | mediaservices/liveEvents/liveOutputs | Não |
> | mediaservices/liveEvents | Não |
> | mediaservices/mediaGraphs | Não |
> | mediaservices/streamingEndpoints | Não |
> | mediaservices/streamingEndpoints | sim |
> | mediaservices/streamingLocators | Não |
> | mediaservices/streamingLocators | Não |
> | mediaservices/transforms | Não |
> | mediaservices/transforms | Não |

## <a name="microsoftmicroservices4spring"></a>Microsoft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | appClusters | sim |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | assessmentProjects | sim |
> | migrateprojects | sim |
> | projects | sim |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | holographicsBroadcastAccounts | sim |
> | objectUnderstandingAccounts | sim |
> | remoteRenderingAccounts | sim |
> | spatialAnchorsAccounts | sim |
> | surfaceReconstructionAccounts | sim |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | netAppAccounts | sim |
> | netAppAccounts/backupPolicies | sim |
> | netAppAccounts/capacityPools | sim |
> | netAppAccounts/capacityPools/volumes | sim |
> | netAppAccounts/capacityPools/volumes/backups | Não |
> | netAppAccounts/capacityPools/volumes/mountTargets | sim |
> | netAppAccounts/capacityPools/volumes/instantâneos | sim |
> | netAppAccounts/cofres | Não |
## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | applicationGateways | sim |
> | applicationGatewayWebApplicationFirewallPolicies | sim |
> | applicationSecurityGroups | sim |
> | azureFirewallFqdnTags | Não |
> | azureFirewalls | sim |
> | bastionHosts | sim |
> | bgpServiceCommunities | Não |
> | connections | sim |
> | ddosCustomPolicies | sim |
> | ddosProtectionPlans | sim |
> | dnsOperationStatuses | Não |
> | dnszones | sim |
> | dnszones/A | Não |
> | dnszones/AAAA | Não |
> | dnszones/all | Não |
> | dnszones/CAA | Não |
> | dnszones/CNAME | Não |
> | dnszones/MX | Não |
> | dnszones/NS | Não |
> | dnszones/PTR | Não |
> | dnszones/recordsets | Não |
> | dnszones/SOA | Não |
> | dnszones/SRV | Não |
> | dnszones/TXT | Não |
> | expressRouteCircuits | sim |
> | expressRouteCrossConnections | sim |
> | expressRouteGateways | sim |
> | expressRoutePorts | sim |
> | expressRouteServiceProviders | Não |
> | firewallPolicies | sim |
> | frontdoors | sim |
> | frontdoorWebApplicationFirewallManagedRuleSets | Não |
> | frontdoorWebApplicationFirewallPolicies | sim |
> | getDnsResourceReference | Não |
> | internalNotify | Não |
> | loadBalancers | sim |
> | virtualNetworkGateways | sim |
> | natGateways | sim |
> | networkIntentPolicies | sim |
> | networkInterfaces | sim |
> | networkProfiles | sim |
> | networkSecurityGroups | sim |
> | networkWatchers | sim |
> | networkWatchers/connectionMonitors | sim |
> | networkWatchers/lenses | sim |
> | networkWatchers/lenses | sim |
> | p2sVpnGateways | sim |
> | privateDnsOperationStatuses | Não |
> | privateDnsZones | sim |
> | privateDnsZones/A | Não |
> | privateDnsZones/AAAA | Não |
> | privateDnsZones/tudo | Não |
> | privateDnsZones/CNAME | Não |
> | privateDnsZones/MX | Não |
> | privateDnsZones/PTR | Não |
> | privateDnsZones/SOA | Não |
> | privateDnsZones/SRV | Não |
> | privateDnsZones/TXT | Não |
> | privateDnsZones/virtualNetworkLinks | sim |
> | privateEndpoints | sim |
> | privateLinkServices | sim |
> | publicIPAddresses | sim |
> | publicIPPrefixes | sim |
> | routeFilters | sim |
> | routeTables | sim |
> | serviceEndpointPolicies | sim |
> | trafficManagerGeographicHierarchies | Não |
> | trafficmanagerprofiles | sim |
> | trafficmanagerprofiles/heatMaps | Não |
> | trafficManagerUserMetricsKeys | Não |
> | virtualWans | sim |
> | virtualNetworkGateways | sim |
> | virtualNetworks | sim |
> | virtualNetworkTaps | sim |
> | virtualWans | sim |
> | vpnGateways | sim |
> | vpnSites | sim |
> | frontdoorWebApplicationFirewallPolicies | sim |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | namespaces | sim |
> | namespaces/notificationHubs | sim |

## <a name="microsoftobjectstore"></a>Microsoft. ObjectStore

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | osNamespaces | sim |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | HyperVSites | sim |
> | ImportSites | sim |
> | ServerSites | sim |
> | VMwareSites | sim |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | clusters | sim |
> | dispositivos | Não |
> | linkTargets | Não |
> | storageInsightConfigs | Não |
> | workspaces | sim |
> | workspaces/dataSources | Não |
> | workspaces/linkedServices | Não |
> | workspaces/query | Não |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | managementassociations | Não |
> | managementconfigurations | sim |
> | solutions | sim |
> | Modos de exibição | sim |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | legacyPeerings | Não |
> | peerAsns | Não |
> | emparelhamentos | sim |
> | peeringServiceProviders | Não |
> | peeringServices | sim |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | policyEvents | Não |
> | policyMetadata | Não |
> | policyStates | Não |
> | policyTrackedResources | Não |
> | remediations | Não |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | consoles | Não |
> | dashboards | sim |
> | userSettings | Não |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | workspaceCollections | sim |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | capacities | sim |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | backupProtectedItems | Não |
> | vaults | sim |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | namespaces | sim |
> | namespaces/authorizationrules | Não |
> | namespaces/hybridconnections | Não |
> | namespaces/hybridconnections/authorizationrules | Não |
> | namespaces/wcfrelays | Não |
> | namespaces/wcfrelays/authorizationrules | Não |

## <a name="microsoftremoteapp"></a>Microsoft. RemoteApp

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | accounts | Não |
> | colec | sim |
> | coleções/aplicativos | Não |
> | coleções/SecurityPrincipals | Não |
> | templateImages | Não |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | consultas | sim |
> | resourceChangeDetails | Não |
> | resourceChanges | Não |
> | recursos | Não |
> | resourcesHistory | Não |
> | subscriptionsStatus | Não |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | availabilityStatuses | Não |
> | childAvailabilityStatuses | Não |
> | childResources | Não |
> | events | Não |
> | impactedResources | Não |
> | metadata | Não |
> | Notificações | Não |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | deployments | Não |
> | deployments/operations | Não |
> | deploymentScripts | sim |
> | deploymentScripts/logs | Não |
> | links | Não |
> | notifyResourceJobs | Não |
> | providers | Não |
> | resourceGroups | Não |
> | recursos | Não |
> | subscriptions | Não |
> | subscriptions/providers | Não |
> | subscriptions/resourceGroups | Não |
> | subscriptions/resourcegroups/resources | Não |
> | subscriptions/resources | Não |
> | subscriptions/tagnames | Não |
> | subscriptions/tagNames/tagValues | Não |
> | tenants | Não |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | de dimensionamento da Web | sim |
> | saasresources | Não |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | jobcollections | sim |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | resourceHealthMetadata | Não |
> | searchServices | sim |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | adaptiveNetworkHardenings | Não |
> | advancedThreatProtectionSettings | Não |
> | alertas | Não |
> | allowedConnections | Não |
> | applicationWhitelistings | Não |
> | assessmentMetadata | Não |
> | Avaliações | Não |
> | automações | sim |
> | AutoProvisioningSettings | Não |
> | Compliances | Não |
> | dataCollectionAgents | Não |
> | deviceSecurityGroups | Não |
> | discoveredSecuritySolutions | Não |
> | externalSecuritySolutions | Não |
> | InformationProtectionPolicies | Não |
> | iotSecuritySolutions | sim |
> | iotSecuritySolutions/analyticsModels | Não |
> | iotSecuritySolutions/analyticsModels/aggregatedAlerts | Não |
> | iotSecuritySolutions/analyticsModels/aggregatedRecommendations | Não |
> | jitNetworkAccessPolicies | Não |
> | networkData | Não |
> | playbookConfigurations | sim |
> | políticas | Não |
> | pricings | Não |
> | regulatoryComplianceStandards | Não |
> | regulatoryComplianceStandards/regulatoryComplianceControls | Não |
> | regulatoryComplianceStandards/regulatoryComplianceControls/regulatoryComplianceAssessments | Não |
> | securityContacts | Não |
> | securitySolutions | Não |
> | securitySolutionsReferenceData | Não |
> | securityStatuses | Não |
> | securityStatusesSummaries | Não |
> | serverVulnerabilityAssessments | Não |
> | Configurações | Não |
> | subavaliações | Não |
> | tarefas | Não |
> | topologies | Não |
> | workspaceSettings | Não |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | diagnosticSettings | Não |
> | diagnosticSettingsCategories | Não |

## <a name="microsoftsecurityinsights"></a>Microsoft. SecurityInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | agregações | Não |
> | alertRules | Não |
> | alertRuleTemplates | Não |
> | indicadores | Não |
> | cases | Não |
> | dataconnecters | Não |
> | entidades | Não |
> | entityQueries | Não |
> | officeConsents | Não |
> | Configurações | Não |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | namespaces | sim |
> | namespaces/authorizationrules | Não |
> | namespaces/disasterrecoveryconfigs | Não |
> | namespaces/eventgridfilters | Não |
> | namespaces/networkrulesets | Não |
> | namespaces/queues | Não |
> | namespaces/queues/authorizationrules | Não |
> | namespaces/topics | Não |
> | namespaces/topics/authorizationrules | Não |
> | namespaces/topics/subscriptions | Não |
> | namespaces/topics/subscriptions/rules | Não |
> | premiumMessagingRegions | Não |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | de dimensionamento da Web | sim |
> | clusters | sim |
> | clusters/applications | Não |
> | containerGroups | sim |
> | containerGroupSets | sim |
> | edgeclusters | sim |
> | edgeclusters/aplicativos | Não |
> | networks | sim |
> | secretstores | sim |
> | secretstores/certificados | Não |
> | secretstores/segredos | Não |
> | volumes | sim |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | de dimensionamento da Web | sim |
> | containerGroups | sim |
> | gateways | sim |
> | networks | sim |
> | segredos | sim |
> | volumes | sim |

## <a name="microsoftservices"></a>Microsoft. Services

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | providerRegistrations | Não |
> | providerRegistrations/resourceTypeRegistrations | Não |
> | rollouts | sim |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | SignalR | sim |
> | Sinalização/eventGridFilters | Não |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | SiteRecoveryVault | sim |

## <a name="microsoftsoftwareplan"></a>Microsoft. SoftwarePlan

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | hybridUseBenefits | Não |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | applicationDefinitions | sim |
> | de dimensionamento da Web | sim |
> | jitRequests | sim |

## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | managedInstances | sim |
> | managedInstances/databases | sim |
> | managedInstances/databases/backupShortTermRetentionPolicies | Não |
> | managedInstances/databases/schemas/tables/columns/sensitivityLabels | Não |
> | managedInstances/databases/vulnerabilityAssessments | Não |
> | managedInstances/databases/vulnerabilityAssessments/rules/baselines | Não |
> | managedInstances/encryptionProtector | Não |
> | managedInstances/keys | Não |
> | managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | Não |
> | managedInstances/vulnerabilityAssessments | Não |
> | servers | sim |
> | servers/administrators | Não |
> | servers/communicationLinks | Não |
> | servers/databases | sim |
> | servers/encryptionProtector | Não |
> | servers/firewallRules | Não |
> | servers/keys | Não |
> | servers/restorableDroppedDatabases | Não |
> | servers/serviceobjectives | Não |
> | servers/tdeCertificates | Não |
> | virtualClusters | Não |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | SqlVirtualMachineGroups | sim |
> | SqlVirtualMachineGroups/AvailabilityGroupListeners | Não |
> | SqlVirtualMachines | sim |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | storageAccounts | sim |
> | storageAccounts/blobServices | Não |
> | storageAccounts/blobServices | Não |
> | storageAccounts/blobServices | Não |
> | storageAccounts/services | Não |
> | storageAccounts/services/metricDefinitions | Não |
> | storageAccounts/blobServices | Não |
> | usages | Não |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | caches | sim |
> | caches/storageTargets | Não |
> | usageModels | Não |

## <a name="microsoftstoragereplication"></a>Microsoft. StorageReplication

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | replicationGroups | Não |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | storageSyncServices | sim |
> | storageSyncServices/registeredServers | Não |
> | storageSyncServices/syncGroups | Não |
> | storageSyncServices/syncGroups/cloudEndpoints | Não |
> | storageSyncServices/syncGroups/cloudEndpoints | Não |
> | storageSyncServices/workflows | Não |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | storageSyncServices | sim |
> | storageSyncServices/registeredServers | Não |
> | storageSyncServices/syncGroups | Não |
> | storageSyncServices/syncGroups/cloudEndpoints | Não |
> | storageSyncServices/syncGroups/cloudEndpoints | Não |
> | storageSyncServices/workflows | Não |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | storageSyncServices | sim |
> | storageSyncServices/registeredServers | Não |
> | storageSyncServices/syncGroups | Não |
> | storageSyncServices/syncGroups/cloudEndpoints | Não |
> | storageSyncServices/syncGroups/cloudEndpoints | Não |
> | storageSyncServices/workflows | Não |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | managers | sim |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | streamingjobs | sim |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | Cancelar | Não |
> | CreateSubscription | Não |
> | enable | Não |
> | nome | Não |
> | SubscriptionDefinitions | Não |
> | SubscriptionDefinitions | Não |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | environments | sim |
> | environments/accessPolicies | Não |
> | environments/eventsources | sim |
> | environments/referenceDataSets | sim |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | dedicatedCloudNodes | sim |
> | dedicatedCloudServices | sim |
> | virtualMachines | sim |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | apiManagementAccounts | Não |
> | apiManagementAccounts/apis/connections/connectionAcls | Não |
> | apiManagementAccounts/apis | Não |
> | apiManagementAccounts/apis/connections/connectionAcls | Não |
> | apiManagementAccounts/apis/connections/connectionAcls | Não |
> | apiManagementAccounts/apis/connections/connectionAcls | Não |
> | apiManagementAccounts/apis/connections/connectionAcls | Não |
> | apiManagementAccounts/apis/localizedDefinitions | Não |
> | apiManagementAccounts/connectionAcls | Não |
> | apiManagementAccounts/connections | Não |
> | billingMeters | Não |
> | certificates | sim |
> | connectionGateways | sim |
> | connections | sim |
> | customApis | sim |
> | deletedSites | Não |
> | funções | Não |
> | hostingEnvironments | sim |
> | hostingEnvironments/multiRolePools | Não |
> | hostingEnvironments/multiRolePools | Não |
> | publishingUsers | Não |
> | filmes | Não |
> | resourceHealthMetadata | Não |
> | runtimes | Não |
> | serverFarms | sim |
> | serverFarms/eventGridFilters | Não |
> | sites | sim |
> | sites/configuração  | Não |
> | sites/eventGridFilters | Não |
> | sites/hostNameBindings | Não |
> | sites/networkConfig | Não |
> | sites/premieraddons | sim |
> | sites/slots | sim |
> | sites/Slots/eventGridFilters | Não |
> | sites/hostNameBindings | Não |
> | sites/Slots/networkConfig | Não |
> | sourceControls | Não |
> | validade | Não |
> | verifyHostingEnvironmentVnet | Não |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | diagnosticSettings | Não |
> | diagnosticSettingsCategories | Não |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | DeviceServices | sim |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | components | Não |
> | componentsSummary | Não |
> | monitorInstances | Não |
> | monitorInstancesSummary | Não |
> | monitors | Não |
> | notificationSettings | Não |

## <a name="next-steps"></a>Próximas etapas

Para obter os mesmos dados como um arquivo de valores separados por vírgula, baixe [complete-mode-deletion.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/complete-mode-deletion.csv).
