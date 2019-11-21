---
title: Exclusão completa de modo
description: Mostra como os tipos de recurso lidam com a exclusão de modo completo em modelos do Azure Resource Manager.
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: 075dd5071d6c59c6cc73fff8d51a495b012665dd
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232682"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>Exclusão de recursos do Azure para implantações do modo completo

Este artigo descreve como os tipos de recurso lidam com a exclusão quando ela não ocorre em um modelo implantado no modo completo.

The resource types marked with **Yes** are deleted when the type isn't in the template deployed with complete mode.

The resource types marked with **No** aren't automatically deleted when not in the template; however, they're deleted if the parent resource is deleted. Para obter uma descrição completa do comportamento, consulte [Modos de implantação do Azure Resource Manager](deployment-modes.md).

If you deploy to [more than one resource group in a template](resource-manager-cross-resource-group-deployment.md), resources in the resource group specified in the deployment operation are eligible to be deleted. Resources in the secondary resource groups aren't deleted.

Jump to a resource provider namespace:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [Microsoft.Addons](#microsoftaddons)
> - [Microsoft.ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppPlatform](#microsoftappplatform)
> - [Microsoft.Attestation](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.Azconfig](#microsoftazconfig)
> - [Microsoft.Azure.Geneva](#microsoftazuregeneva)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.Billing](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Capacity](#microsoftcapacity)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Consumption](#microsoftconsumption)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft.CustomProviders](#microsoftcustomproviders)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
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
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.DynamicsLcs](#microsoftdynamicslcs)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Features](#microsoftfeatures)
> - [Microsoft.Gallery](#microsoftgallery)
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
> - [Microsoft.Intune](#microsoftintune)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
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
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.RemoteApp](#microsoftremoteapp)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Scheduler](#microsoftscheduler)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.SecurityGraph](#microsoftsecuritygraph)
> - [Microsoft.SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.Services](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SiteRecovery](#microsoftsiterecovery)
> - [Microsoft.SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.SQL](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageReplication](#microsoftstoragereplication)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.Subscription](#microsoftsubscription)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | DomainServices | SIM |
> | DomainServices / oucontainer | Não |

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
> | actionRules | SIM |
> | alertas | Não |
> | alertsList | Não |
> | alertsMetaData | Não |
> | alertsSummary | Não |
> | alertsSummaryList | Não |
> | feedback | Não |
> | smartDetectorAlertRules | SIM |
> | smartDetectorRuntimeEnvironments | Não |
> | smartGroups | Não |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | servidores | SIM |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | reportFeedback | Não |
> | serviço | SIM |
> | validateServiceName | Não |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | configurationStores | SIM |
> | configurationStores / eventGridFilters | Não |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | Spring | SIM |

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
> | dataAliases | Não |
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
> | automationaccounts | SIM |
> | automationAccounts / configurations | SIM |
> | automationAccounts / jobs | Não |
> | automationAccounts / runbooks | SIM |
> | automationAccounts / softwareUpdateConfigurations | Não |
> | automationAccounts / webhooks | Não |

## <a name="microsoftazconfig"></a>Microsoft.Azconfig

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | configurationStores | SIM |
> | configurationStores / eventGridFilters | Não |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | environments | Não |
> | environments / accounts | Não |
> | environments / accounts / namespaces | Não |
> | environments / accounts / namespaces / configurations | Não |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | b2cDirectories | SIM |
> | b2ctenants | Não |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | hybridDataManagers | SIM |
> | postgresInstances | SIM |
> | sqlBigDataClusters | SIM |
> | sqlInstances | SIM |
> | sqlServerRegistrations | SIM |
> | sqlServerRegistrations / sqlServers | Não |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | registrations | SIM |
> | registrations / customerSubscriptions | Não |
> | registrations / products | Não |
> | verificationKeys | Não |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | batchAccounts | SIM |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | billingAccounts | Não |
> | billingAccounts / agreements | Não |
> | billingAccounts / billingPermissions | Não |
> | billingAccounts / billingProfiles | Não |
> | billingAccounts / billingProfiles / billingPermissions | Não |
> | billingAccounts / billingProfiles / billingRoleAssignments | Não |
> | billingAccounts / billingProfiles / billingRoleDefinitions | Não |
> | billingAccounts / billingProfiles / billingSubscriptions | Não |
> | billingAccounts / billingProfiles / createBillingRoleAssignment | Não |
> | billingAccounts / billingProfiles / customers | Não |
> | billingAccounts / billingProfiles / invoices | Não |
> | billingAccounts / billingProfiles / invoices / pricesheet | Não |
> | billingAccounts / billingProfiles / invoiceSections | Não |
> | billingAccounts / billingProfiles / invoiceSections / billingPermissions | Não |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleAssignments | Não |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleDefinitions | Não |
> | billingAccounts / billingProfiles / invoiceSections / billingSubscriptions | Não |
> | billingAccounts / billingProfiles / invoiceSections / createBillingRoleAssignment | Não |
> | billingAccounts / billingProfiles / invoiceSections / initiateTransfer | Não |
> | billingAccounts / billingProfiles / invoiceSections / products | Não |
> | billingAccounts / billingProfiles / invoiceSections / products / transfer | Não |
> | billingAccounts / billingProfiles / invoiceSections / products / updateAutoRenew | Não |
> | billingAccounts / billingProfiles / invoiceSections / transactions | Não |
> | billingAccounts / billingProfiles / invoiceSections / transfers | Não |
> | billingAccounts / BillingProfiles / patchOperations | Não |
> | billingAccounts / billingProfiles / paymentMethods | Não |
> | billingAccounts / billingProfiles / policies | Não |
> | billingAccounts / billingProfiles / pricesheet | Não |
> | billingAccounts / billingProfiles / pricesheetDownloadOperations | Não |
> | billingAccounts / billingProfiles / products | Não |
> | billingAccounts / billingProfiles / transactions | Não |
> | billingAccounts / billingRoleAssignments | Não |
> | billingAccounts / billingRoleDefinitions | Não |
> | billingAccounts / billingSubscriptions | Não |
> | billingAccounts / billingSubscriptions / invoices | Não |
> | billingAccounts / createBillingRoleAssignment | Não |
> | billingAccounts / createInvoiceSectionOperations | Não |
> | billingAccounts / customers | Não |
> | billingAccounts / customers / billingPermissions | Não |
> | billingAccounts / customers / billingSubscriptions | Não |
> | billingAccounts / customers / initiateTransfer | Não |
> | billingAccounts / customers / policies | Não |
> | billingAccounts / customers / products | Não |
> | billingAccounts / customers / transactions | Não |
> | billingAccounts / customers / transfers | Não |
> | billingAccounts / departments | Não |
> | billingAccounts / enrollmentAccounts | Não |
> | billingAccounts / invoices | Não |
> | billingAccounts / invoiceSections | Não |
> | billingAccounts / invoiceSections / billingSubscriptionMoveOperations | Não |
> | billingAccounts / invoiceSections / billingSubscriptions | Não |
> | billingAccounts / invoiceSections / billingSubscriptions / transfer | Não |
> | billingAccounts / invoiceSections / elevate | Não |
> | billingAccounts / invoiceSections / initiateTransfer | Não |
> | billingAccounts / invoiceSections / patchOperations | Não |
> | billingAccounts / invoiceSections / productMoveOperations | Não |
> | billingAccounts / invoiceSections / products | Não |
> | billingAccounts / invoiceSections / products / transfer | Não |
> | billingAccounts / invoiceSections / products / updateAutoRenew | Não |
> | billingAccounts / invoiceSections / transactions | Não |
> | billingAccounts / invoiceSections / transfers | Não |
> | billingAccounts / lineOfCredit | Não |
> | billingAccounts / patchOperations | Não |
> | billingAccounts / paymentMethods | Não |
> | billingAccounts / products | Não |
> | billingAccounts / transactions | Não |
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
> | transfers / acceptTransfer | Não |
> | transfers / declineTransfer | Não |
> | transfers / operationStatus | Não |
> | transfers / validateTransfer | Não |
> | validateAddress | Não |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | mapApis | SIM |
> | updateCommunicationPreference | Não |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | blockchainMembers | SIM |
> | cordaMembers | SIM |
> | espectadores | SIM |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | blueprintAssignments | Não |
> | blueprintAssignments / assignmentOperations | Não |
> | blueprintAssignments / operations | Não |
> | blueprints | Não |
> | blueprints / artifacts | Não |
> | blueprints / versions | Não |
> | blueprints / versions / artifacts | Não |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | botServices | SIM |
> | botServices / channels | Não |
> | botServices / connections | Não |
> | idiomas | Não |
> | modelos | Não |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | Redis | SIM |
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
> | exchange | Não |
> | placePurchaseOrder | Não |
> | reservationOrders | Não |
> | reservationOrders / calculateRefund | Não |
> | reservationOrders / merge | Não |
> | reservationOrders / reservations | Não |
> | reservationOrders / reservations / revisions | Não |
> | reservationOrders / return | Não |
> | reservationOrders / split | Não |
> | reservationOrders / swap | Não |
> | reservations | Não |
> | recursos | Não |
> | validateReservationOrder | Não |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | Não |
> | CdnWebApplicationFirewallPolicies | SIM |
> | edgenodes | Não |
> | perfis | SIM |
> | profiles / endpoints | SIM |
> | profiles / endpoints / customdomains | Não |
> | profiles / endpoints / origins | Não |
> | validateProbe | Não |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | certificateOrders | SIM |
> | certificateOrders / certificates | Não |
> | validateCertificateRegistrationInformation | Não |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | recursos | Não |
> | domainNames | SIM |
> | domainNames / capabilities | Não |
> | domainNames / internalLoadBalancers | Não |
> | domainNames / serviceCertificates | Não |
> | domainNames / slots | Não |
> | domainNames / slots / roles | Não |
> | domainNames / slots / roles / metricDefinitions | Não |
> | domainNames / slots / roles / metrics | Não |
> | moveSubscriptionResources | Não |
> | operatingSystemFamilies | Não |
> | operatingSystems | Não |
> | quotas | Não |
> | resourceTypes | Não |
> | validateSubscriptionMoveAvailability | Não |
> | virtualMachines | SIM |
> | virtualMachines / diagnosticSettings | Não |
> | virtualMachines / metricDefinitions | Não |
> | virtualMachines / metrics | Não |

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
> | expressRouteCrossConnections / peerings | Não |
> | gatewaySupportedDevices | Não |
> | networkSecurityGroups | SIM |
> | quotas | Não |
> | reservedIps | SIM |
> | virtualNetworks | SIM |
> | virtualNetworks / remoteVirtualNetworkPeeringProxies | Não |
> | virtualNetworks / virtualNetworkPeerings | Não |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | recursos | Não |
> | discos | Não |
> | images | Não |
> | osImages | Não |
> | osPlatformImages | Não |
> | publicImages | Não |
> | quotas | Não |
> | storageAccounts | SIM |
> | storageAccounts / blobServices | Não |
> | storageAccounts / fileServices | Não |
> | storageAccounts / metricDefinitions | Não |
> | storageAccounts / metrics | Não |
> | storageAccounts / queueServices | Não |
> | storageAccounts / services | Não |
> | storageAccounts / services / diagnosticSettings | Não |
> | storageAccounts / services / metricDefinitions | Não |
> | storageAccounts / services / metrics | Não |
> | storageAccounts / tableServices | Não |
> | storageAccounts / vmImages | Não |
> | vmImages | Não |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | accounts | SIM |

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
> | availabilitySets | SIM |
> | diskEncryptionSets | SIM |
> | discos | SIM |
> | galleries | SIM |
> | galleries / applications | Não |
> | galleries / applications / versions | Não |
> | galleries / images | Não |
> | galleries / images / versions | Não |
> | hostGroups | SIM |
> | hostGroups / hosts | SIM |
> | images | SIM |
> | proximityPlacementGroups | SIM |
> | restorePointCollections | SIM |
> | restorePointCollections / restorePoints | Não |
> | sharedVMImages | SIM |
> | sharedVMImages / versions | Não |
> | snapshots | SIM |
> | virtualMachines | SIM |
> | virtualMachines / extensions | SIM |
> | virtualMachines / metricDefinitions | Não |
> | virtualMachineScaleSets | SIM |
> | virtualMachineScaleSets / extensions | Não |
> | virtualMachineScaleSets / networkInterfaces | Não |
> | virtualMachineScaleSets / publicIPAddresses | Não |
> | virtualMachineScaleSets / virtualMachines | Não |
> | virtualMachineScaleSets / virtualMachines / networkInterfaces | Não |

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
> | containerGroups | SIM |
> | serviceAssociationLinks | Não |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | registries | SIM |
> | registries / builds | Não |
> | registries / builds / cancel | Não |
> | registries / builds / getLogLink | Não |
> | registries / buildTasks | SIM |
> | registries / buildTasks / steps | Não |
> | registries / eventGridFilters | Não |
> | registries / generateCredentials | Não |
> | registries / getBuildSourceUploadUrl | Não |
> | registries / GetCredentials | Não |
> | registries / importImage | Não |
> | registries / queueBuild | Não |
> | registries / regenerateCredential | Não |
> | registries / regenerateCredentials | Não |
> | registries / replications | SIM |
> | registries / runs | Não |
> | registries / runs / cancel | Não |
> | registries / scheduleRun | Não |
> | registries / scopeMaps | Não |
> | registries / taskRuns | SIM |
> | registries / tasks | SIM |
> | registries / tokens | Não |
> | registries / updatePolicies | Não |
> | registries / webhooks | SIM |
> | registries / webhooks / getCallbackConfig | Não |
> | registries / webhooks / ping | Não |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | containerServices | SIM |
> | managedClusters | SIM |
> | openShiftManagedClusters | SIM |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | accounts | SIM |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | Alertas | Não |
> | BillingAccounts | Não |
> | Orçamentos | Não |
> | CloudConnectors | Não |
> | Conectores | SIM |
> | Departments | Não |
> | Dimensões | Não |
> | EnrollmentAccounts | Não |
> | Exportações | Não |
> | ExternalBillingAccounts | Não |
> | ExternalBillingAccounts / Alerts | Não |
> | ExternalBillingAccounts / Dimensions | Não |
> | ExternalBillingAccounts / Forecast | Não |
> | ExternalBillingAccounts / Query | Não |
> | ExternalSubscriptions | Não |
> | ExternalSubscriptions / Alerts | Não |
> | ExternalSubscriptions / Dimensions | Não |
> | ExternalSubscriptions / Forecast | Não |
> | ExternalSubscriptions / Query | Não |
> | Forecast | Não |
> | Consulta | Não |
> | register | Não |
> | Reportconfigs | Não |
> | Relatórios | Não |
> | Configurações | Não |
> | showbackRules | Não |
> | Exibições | Não |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | solicitações | Não |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | associations | Não |
> | resourceProviders | SIM |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | jobs | SIM |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | DataBoxEdgeDevices | SIM |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | workspaces | SIM |
> | workspaces / virtualNetworkPeerings | Não |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | catalogs | SIM |
> | datacatalogs | SIM |
> | datacatalogs / datasources | Não |
> | datacatalogs / datasources / scans | Não |
> | datacatalogs / datasources / scans / datasets | Não |
> | datacatalogs / datasources / scans / triggers | Não |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | dataFactories | SIM |
> | dataFactories / diagnosticSettings | Não |
> | dataFactories / metricDefinitions | Não |
> | dataFactorySchema | Não |
> | factories | SIM |
> | factories / integrationRuntimes | Não |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | accounts | SIM |
> | accounts / dataLakeStoreAccounts | Não |
> | accounts / storageAccounts | Não |
> | accounts / storageAccounts / containers | Não |
> | accounts / transferAnalyticsUnits | Não |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | accounts | SIM |
> | accounts / eventGridFilters | Não |
> | accounts / firewallRules | Não |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | services | SIM |
> | services / projects | SIM |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | accounts | SIM |
> | accounts / shares | Não |
> | accounts / shares / datasets | Não |
> | accounts / shares / invitations | Não |
> | accounts / shares / providersharesubscriptions | Não |
> | accounts / shares / synchronizationSettings | Não |
> | accounts / sharesubscriptions | Não |
> | accounts / sharesubscriptions / consumerSourceDataSets | Não |
> | accounts / sharesubscriptions / datasetmappings | Não |
> | accounts / sharesubscriptions / triggers | Não |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | servidores | SIM |
> | servers / advisors | Não |
> | servers / privateEndpointConnectionProxies | Não |
> | servers / privateEndpointConnections | Não |
> | servers / privateLinkResources | Não |
> | servers / queryTexts | Não |
> | servers / recoverableServers | Não |
> | servers / topQueryStatistics | Não |
> | servers / virtualNetworkRules | Não |
> | servers / waitStatistics | Não |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | servidores | SIM |
> | servers / advisors | Não |
> | servers / privateEndpointConnectionProxies | Não |
> | servers / privateEndpointConnections | Não |
> | servers / privateLinkResources | Não |
> | servers / queryTexts | Não |
> | servers / recoverableServers | Não |
> | servers / topQueryStatistics | Não |
> | servers / virtualNetworkRules | Não |
> | servers / waitStatistics | Não |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | serverGroups | SIM |
> | servidores | SIM |
> | servers / advisors | Não |
> | servers / keys | Não |
> | servers / privateEndpointConnectionProxies | Não |
> | servers / privateEndpointConnections | Não |
> | servers / privateLinkResources | Não |
> | servers / queryTexts | Não |
> | servers / recoverableServers | Não |
> | servers / topQueryStatistics | Não |
> | servers / virtualNetworkRules | Não |
> | servers / waitStatistics | Não |
> | serversv2 | SIM |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | artifactSources | SIM |
> | rollouts | SIM |
> | serviceTopologies | SIM |
> | serviceTopologies / services | SIM |
> | serviceTopologies / services / serviceUnits | SIM |
> | etapas | SIM |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | applicationgroups | SIM |
> | applicationgroups / applications | Não |
> | applicationgroups / desktops | Não |
> | applicationgroups / startmenuitems | Não |
> | hostpools | SIM |
> | hostpools / sessionhosts | Não |
> | hostpools / sessionhosts / usersessions | Não |
> | hostpools / usersessions | Não |
> | workspaces | SIM |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | ElasticPools | SIM |
> | ElasticPools / IotHubTenants | SIM |
> | IotHubs | SIM |
> | IotHubs / eventGridFilters | Não |
> | ProvisioningServices | SIM |
> | usages | Não |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | pipelines | SIM |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | controladores | SIM |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | labcenters | SIM |
> | labs | SIM |
> | labs / environments | SIM |
> | labs / serviceRunners | SIM |
> | labs / virtualMachines | SIM |
> | schedules | SIM |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | databaseAccounts | Não |
> | databaseAccounts | SIM |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | domains | SIM |
> | domains / domainOwnershipIdentifiers | Não |
> | generateSsoRequest | Não |
> | topLevelDomains | Não |
> | validateDomainRegistrationInformation | Não |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | lcsprojects | Não |
> | lcsprojects / clouddeployments | Não |
> | lcsprojects / connectors | Não |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | services | SIM |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | domains | SIM |
> | domains / topics | Não |
> | eventSubscriptions | Não |
> | extensionTopics | Não |
> | topics | SIM |
> | topicTypes | Não |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | clusters | SIM |
> | namespaces | SIM |
> | namespaces / authorizationrules | Não |
> | namespaces / disasterrecoveryconfigs | Não |
> | namespaces / eventhubs | Não |
> | namespaces / eventhubs / authorizationrules | Não |
> | namespaces / eventhubs / consumergroups | Não |
> | namespaces / networkrulesets | Não |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | elástico | Não |
> | providers | Não |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | enroll | Não |
> | galleryitems | Não |
> | generateartifactaccessuri | Não |
> | myareas | Não |
> | myareas / areas | Não |
> | myareas / areas / areas | Não |
> | myareas / areas / areas / galleryitems | Não |
> | myareas / areas / galleryitems | Não |
> | myareas / galleryitems | Não |
> | register | Não |
> | recursos | Não |
> | retrieveresourcesbyid | Não |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | accounts | SIM |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | configurationProfileAssignments | Não |
> | guestConfigurationAssignments | Não |
> | software | Não |
> | softwareUpdateProfile | Não |
> | softwareUpdates | Não |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | hanaInstances | SIM |
> | sapMonitors | SIM |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | dedicatedHSMs | SIM |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | clusters | SIM |
> | clusters / applications | Não |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | services | SIM |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | machines | SIM |
> | machines / extensions | SIM |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | dataManagers | SIM |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | components | SIM |
> | networkScopes | SIM |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | jobs | SIM |

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
> | IoTApps | SIM |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | Graph | SIM |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | deletedVaults | Não |
> | hsmPools | SIM |
> | vaults | SIM |
> | vaults / accessPolicies | Não |
> | vaults / eventGridFilters | Não |
> | vaults / secrets | Não |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | clusters | SIM |
> | clusters / attacheddatabaseconfigurations | Não |
> | clusters / databases | Não |
> | clusters / databases / dataconnections | Não |
> | clusters / databases / eventhubconnections | Não |
> | clusters / sharedidentities | Não |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | labaccounts | SIM |
> | users | Não |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | hostingEnvironments | SIM |
> | integrationAccounts | SIM |
> | integrationServiceEnvironments | SIM |
> | integrationServiceEnvironments / managedApis | SIM |
> | isolatedEnvironments | SIM |
> | workflows | SIM |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | commitmentPlans | SIM |
> | webServices | SIM |
> | Workspaces | SIM |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | workspaces | SIM |
> | workspaces / computes | Não |
> | workspaces / eventGridFilters | Não |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | Identidades | Não |
> | userAssignedIdentities | SIM |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

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
> | accounts | SIM |
> | accounts / eventGridFilters | Não |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | offers | Não |
> | offerTypes | Não |
> | offerTypes / publishers | Não |
> | offerTypes / publishers / offers | Não |
> | offerTypes / publishers / offers / plans | Não |
> | offerTypes / publishers / offers / plans / agreements | Não |
> | offerTypes / publishers / offers / plans / configs | Não |
> | offerTypes / publishers / offers / plans / configs / importImage | Não |
> | privategalleryitems | Não |
> | products | Não |
> | publishers | Não |
> | publishers / offers | Não |
> | publishers / offers / amendments | Não |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | classicDevServices | SIM |
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
> | mediaservices | SIM |
> | mediaservices / accountFilters | Não |
> | mediaservices / assets | Não |
> | mediaservices / assets / assetFilters | Não |
> | mediaservices / contentKeyPolicies | Não |
> | mediaservices / eventGridFilters | Não |
> | mediaservices / liveEventOperations | Não |
> | mediaservices / liveEvents | SIM |
> | mediaservices / liveEvents / liveOutputs | Não |
> | mediaservices / liveOutputOperations | Não |
> | mediaservices / mediaGraphs | Não |
> | mediaservices / streamingEndpointOperations | Não |
> | mediaservices / streamingEndpoints | SIM |
> | mediaservices / streamingLocators | Não |
> | mediaservices / streamingPolicies | Não |
> | mediaservices / transforms | Não |
> | mediaservices / transforms / jobs | Não |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | appClusters | SIM |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | assessmentProjects | SIM |
> | migrateprojects | SIM |
> | projects | SIM |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | holographicsBroadcastAccounts | SIM |
> | objectUnderstandingAccounts | SIM |
> | remoteRenderingAccounts | SIM |
> | spatialAnchorsAccounts | SIM |
> | surfaceReconstructionAccounts | SIM |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | netAppAccounts | SIM |
> | netAppAccounts / capacityPools | SIM |
> | netAppAccounts / capacityPools / volumes | SIM |
> | netAppAccounts / capacityPools / volumes / mountTargets | SIM |
> | netAppAccounts / capacityPools / volumes / snapshots | SIM |
## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | applicationGateways | SIM |
> | applicationGatewayWebApplicationFirewallPolicies | SIM |
> | applicationSecurityGroups | SIM |
> | azureFirewallFqdnTags | Não |
> | azureFirewalls | SIM |
> | bastionHosts | SIM |
> | bgpServiceCommunities | Não |
> | connections | SIM |
> | ddosCustomPolicies | SIM |
> | ddosProtectionPlans | SIM |
> | dnsOperationStatuses | Não |
> | dnszones | SIM |
> | dnszones / A | Não |
> | dnszones / AAAA | Não |
> | dnszones / all | Não |
> | dnszones / CAA | Não |
> | dnszones / CNAME | Não |
> | dnszones / MX | Não |
> | dnszones / NS | Não |
> | dnszones / PTR | Não |
> | dnszones / recordsets | Não |
> | dnszones / SOA | Não |
> | dnszones / SRV | Não |
> | dnszones / TXT | Não |
> | expressRouteCircuits | SIM |
> | expressRouteCrossConnections | SIM |
> | expressRouteGateways | SIM |
> | expressRoutePorts | SIM |
> | expressRouteServiceProviders | Não |
> | firewallPolicies | SIM |
> | frontdoors | SIM |
> | frontdoorWebApplicationFirewallManagedRuleSets | Não |
> | frontdoorWebApplicationFirewallPolicies | SIM |
> | getDnsResourceReference | Não |
> | internalNotify | Não |
> | loadBalancers | SIM |
> | virtualNetworkGateways | SIM |
> | natGateways | SIM |
> | networkIntentPolicies | SIM |
> | networkInterfaces | SIM |
> | networkProfiles | SIM |
> | networkSecurityGroups | SIM |
> | networkWatchers | SIM |
> | networkWatchers / connectionMonitors | SIM |
> | networkWatchers / lenses | SIM |
> | networkWatchers / pingMeshes | SIM |
> | p2sVpnGateways | SIM |
> | privateDnsOperationStatuses | Não |
> | privateDnsZones | SIM |
> | privateDnsZones / A | Não |
> | privateDnsZones / AAAA | Não |
> | privateDnsZones / all | Não |
> | privateDnsZones / CNAME | Não |
> | privateDnsZones / MX | Não |
> | privateDnsZones / PTR | Não |
> | privateDnsZones / SOA | Não |
> | privateDnsZones / SRV | Não |
> | privateDnsZones / TXT | Não |
> | privateDnsZones / virtualNetworkLinks | SIM |
> | privateEndpoints | SIM |
> | privateLinkServices | SIM |
> | publicIPAddresses | SIM |
> | publicIPPrefixes | SIM |
> | routeFilters | SIM |
> | routeTables | SIM |
> | serviceEndpointPolicies | SIM |
> | trafficManagerGeographicHierarchies | Não |
> | trafficmanagerprofiles | SIM |
> | trafficmanagerprofiles / heatMaps | Não |
> | trafficManagerUserMetricsKeys | Não |
> | virtualWans | SIM |
> | virtualNetworkGateways | SIM |
> | virtualNetworks | SIM |
> | virtualNetworkTaps | SIM |
> | virtualWans | SIM |
> | vpnGateways | SIM |
> | vpnSites | SIM |
> | frontdoorWebApplicationFirewallPolicies | SIM |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | namespaces | SIM |
> | namespaces / notificationHubs | SIM |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | osNamespaces | SIM |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | HyperVSites | SIM |
> | ImportSites | SIM |
> | ServerSites | SIM |
> | VMwareSites | SIM |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | clusters | SIM |
> | dispositivos | Não |
> | linkTargets | Não |
> | storageInsightConfigs | Não |
> | workspaces | SIM |
> | workspaces / dataSources | Não |
> | workspaces / linkedServices | Não |
> | workspaces / query | Não |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | managementassociations | Não |
> | managementconfigurations | SIM |
> | solutions | SIM |
> | Modos de exibição | SIM |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | legacyPeerings | Não |
> | peerAsns | Não |
> | peerings | SIM |
> | peeringServiceProviders | Não |
> | peeringServices | SIM |

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
> | dashboards | SIM |
> | userSettings | Não |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | workspaceCollections | SIM |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | capacities | SIM |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | backupProtectedItems | Não |
> | vaults | SIM |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | namespaces | SIM |
> | namespaces / authorizationrules | Não |
> | namespaces / hybridconnections | Não |
> | namespaces / hybridconnections / authorizationrules | Não |
> | namespaces / wcfrelays | Não |
> | namespaces / wcfrelays / authorizationrules | Não |

## <a name="microsoftremoteapp"></a>Microsoft.RemoteApp

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | accounts | Não |
> | collections | SIM |
> | collections / applications | Não |
> | collections / securityprincipals | Não |
> | templateImages | Não |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | consultas | SIM |
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
> | deployments / operations | Não |
> | deploymentScripts | SIM |
> | deploymentScripts / logs | Não |
> | links | Não |
> | notifyResourceJobs | Não |
> | providers | Não |
> | resourceGroups | Não |
> | recursos | Não |
> | subscriptions | Não |
> | subscriptions / providers | Não |
> | subscriptions / resources | Não |
> | subscriptions / tagnames | Não |
> | subscriptions / tagNames / tagValues | Não |
> | tenants | Não |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | aplicativos | SIM |
> | saasresources | Não |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | jobcollections | SIM |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | resourceHealthMetadata | Não |
> | searchServices | SIM |

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
> | assessments | Não |
> | autoDismissAlertsRules | Não |
> | automations | SIM |
> | AutoProvisioningSettings | Não |
> | Compliances | Não |
> | dataCollectionAgents | Não |
> | deviceSecurityGroups | Não |
> | discoveredSecuritySolutions | Não |
> | externalSecuritySolutions | Não |
> | InformationProtectionPolicies | Não |
> | iotSecuritySolutions | SIM |
> | iotSecuritySolutions / analyticsModels | Não |
> | iotSecuritySolutions / analyticsModels / aggregatedAlerts | Não |
> | iotSecuritySolutions / analyticsModels / aggregatedRecommendations | Não |
> | jitNetworkAccessPolicies | Não |
> | networkData | Não |
> | políticas | Não |
> | pricings | Não |
> | regulatoryComplianceStandards | Não |
> | regulatoryComplianceStandards / regulatoryComplianceControls | Não |
> | regulatoryComplianceStandards / regulatoryComplianceControls / regulatoryComplianceAssessments | Não |
> | securityContacts | Não |
> | securitySolutions | Não |
> | securitySolutionsReferenceData | Não |
> | securityStatuses | Não |
> | securityStatusesSummaries | Não |
> | serverVulnerabilityAssessments | Não |
> | configurações | Não |
> | subAssessments | Não |
> | tarefas | Não |
> | topologies | Não |
> | workspaceSettings | Não |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | diagnosticSettings | Não |
> | diagnosticSettingsCategories | Não |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | aggregations | Não |
> | alertRules | Não |
> | alertRuleTemplates | Não |
> | bookmarks | Não |
> | cases | Não |
> | dataConnectors | Não |
> | entidades | Não |
> | entityQueries | Não |
> | officeConsents | Não |
> | configurações | Não |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | namespaces | SIM |
> | namespaces / authorizationrules | Não |
> | namespaces / disasterrecoveryconfigs | Não |
> | namespaces / eventgridfilters | Não |
> | namespaces / networkrulesets | Não |
> | namespaces / queues | Não |
> | namespaces / queues / authorizationrules | Não |
> | namespaces / topics | Não |
> | namespaces / topics / authorizationrules | Não |
> | namespaces / topics / subscriptions | Não |
> | namespaces / topics / subscriptions / rules | Não |
> | premiumMessagingRegions | Não |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | aplicativos | SIM |
> | clusters | SIM |
> | clusters / applications | Não |
> | containerGroups | SIM |
> | containerGroupSets | SIM |
> | edgeclusters | SIM |
> | edgeclusters / applications | Não |
> | networks | SIM |
> | secretstores | SIM |
> | secretstores / certificates | Não |
> | secretstores / secrets | Não |
> | volumes | SIM |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | aplicativos | SIM |
> | containerGroups | SIM |
> | gateways | SIM |
> | networks | SIM |
> | segredos | SIM |
> | volumes | SIM |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | providerRegistrations | Não |
> | providerRegistrations / resourceTypeRegistrations | Não |
> | rollouts | SIM |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | SignalR | SIM |
> | SignalR / eventGridFilters | Não |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | SiteRecoveryVault | SIM |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | hybridUseBenefits | Não |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | applicationDefinitions | SIM |
> | aplicativos | SIM |
> | jitRequests | SIM |

## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | managedInstances | SIM |
> | managedInstances / databases | SIM |
> | managedInstances / databases / backupShortTermRetentionPolicies | Não |
> | managedInstances / databases / schemas / tables / columns / sensitivityLabels | Não |
> | managedInstances / databases / vulnerabilityAssessments | Não |
> | managedInstances / databases / vulnerabilityAssessments / rules / baselines | Não |
> | managedInstances / encryptionProtector | Não |
> | managedInstances / keys | Não |
> | managedInstances / restorableDroppedDatabases / backupShortTermRetentionPolicies | Não |
> | managedInstances / vulnerabilityAssessments | Não |
> | servidores | SIM |
> | servers / administrators | Não |
> | servers / communicationLinks | Não |
> | servers / databases | SIM |
> | servers / encryptionProtector | Não |
> | servers / firewallRules | Não |
> | servers / keys | Não |
> | servers / restorableDroppedDatabases | Não |
> | servers / serviceobjectives | Não |
> | servers / tdeCertificates | Não |
> | virtualClusters | Não |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | SqlVirtualMachineGroups | SIM |
> | SqlVirtualMachineGroups / AvailabilityGroupListeners | Não |
> | SqlVirtualMachines | SIM |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | storageAccounts | SIM |
> | storageAccounts / blobServices | Não |
> | storageAccounts / fileServices | Não |
> | storageAccounts / queueServices | Não |
> | storageAccounts / services | Não |
> | storageAccounts / services / metricDefinitions | Não |
> | storageAccounts / tableServices | Não |
> | usages | Não |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | caches | SIM |
> | caches / storageTargets | Não |
> | usageModels | Não |

## <a name="microsoftstoragereplication"></a>Microsoft.StorageReplication

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | replicationGroups | Não |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | storageSyncServices | SIM |
> | storageSyncServices / registeredServers | Não |
> | storageSyncServices / syncGroups | Não |
> | storageSyncServices / syncGroups / cloudEndpoints | Não |
> | storageSyncServices / syncGroups / serverEndpoints | Não |
> | storageSyncServices / workflows | Não |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | storageSyncServices | SIM |
> | storageSyncServices / registeredServers | Não |
> | storageSyncServices / syncGroups | Não |
> | storageSyncServices / syncGroups / cloudEndpoints | Não |
> | storageSyncServices / syncGroups / serverEndpoints | Não |
> | storageSyncServices / workflows | Não |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | storageSyncServices | SIM |
> | storageSyncServices / registeredServers | Não |
> | storageSyncServices / syncGroups | Não |
> | storageSyncServices / syncGroups / cloudEndpoints | Não |
> | storageSyncServices / syncGroups / serverEndpoints | Não |
> | storageSyncServices / workflows | Não |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | managers | SIM |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | streamingjobs | SIM |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | cancel | Não |
> | CreateSubscription | Não |
> | enable | Não |
> | rename | Não |
> | SubscriptionDefinitions | Não |
> | SubscriptionDefinitions | Não |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | environments | SIM |
> | environments / accessPolicies | Não |
> | environments / eventsources | SIM |
> | environments / referenceDataSets | SIM |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | dedicatedCloudNodes | SIM |
> | dedicatedCloudServices | SIM |
> | virtualMachines | SIM |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | apiManagementAccounts | Não |
> | apiManagementAccounts / apiAcls | Não |
> | apiManagementAccounts / apis | Não |
> | apiManagementAccounts / apis / apiAcls | Não |
> | apiManagementAccounts / apis / connectionAcls | Não |
> | apiManagementAccounts / apis / connections | Não |
> | apiManagementAccounts / apis / connections / connectionAcls | Não |
> | apiManagementAccounts / apis / localizedDefinitions | Não |
> | apiManagementAccounts / connectionAcls | Não |
> | apiManagementAccounts / connections | Não |
> | billingMeters | Não |
> | certificates | SIM |
> | connectionGateways | SIM |
> | connections | SIM |
> | customApis | SIM |
> | deletedSites | Não |
> | functions | Não |
> | hostingEnvironments | SIM |
> | hostingEnvironments / multiRolePools | Não |
> | hostingEnvironments / workerPools | Não |
> | publishingUsers | Não |
> | filmes | Não |
> | resourceHealthMetadata | Não |
> | runtimes | Não |
> | serverFarms | SIM |
> | serverFarms / eventGridFilters | Não |
> | sites | SIM |
> | sites/config  | Não |
> | sites / eventGridFilters | Não |
> | sites / hostNameBindings | Não |
> | sites / networkConfig | Não |
> | sites / premieraddons | SIM |
> | sites / slots | SIM |
> | sites / slots / eventGridFilters | Não |
> | sites / slots / hostNameBindings | Não |
> | sites / slots / networkConfig | Não |
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
> | DeviceServices | SIM |

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

## <a name="next-steps"></a>Próximos passos

Para obter os mesmos dados como um arquivo de valores separados por vírgula, baixe [complete-mode-deletion.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/complete-mode-deletion.csv).
