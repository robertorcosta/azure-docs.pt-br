---
title: Suporte a marcas para recursos
description: Mostra quais tipos de recursos do Azure suportam tags. Fornece detalhes para todos os serviços do Azure.
ms.topic: conceptual
ms.date: 11/18/2019
ms.openlocfilehash: aa8736310a5c18a1f94d3920a4f88632212c7af4
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173545"
---
# <a name="tag-support-for-azure-resources"></a>Suporte a marcas para recursos do Azure
Este artigo descreve se um tipo de recurso dá suporte a [marcas](resource-group-using-tags.md). A coluna rotulada **dá suporte a marcas** indica se o tipo de recurso tem uma propriedade para a marca. A coluna rotulada como **marca no relatório de custo** indica se esse tipo de recurso passa a marca para o relatório de custo.

Para obter os mesmos dados como um arquivo de valores separados por vírgula, baixe [tag-support.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv).

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
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | DomainServices | sim | sim |
> | DomainServices/oucontainer | Não | Não |
> | DomainServices/ReplicaSets | sim | sim |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | supportProviders | Não | Não |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | aadsupportcases | Não | Não |
> | addsservices | Não | Não |
> | agentes | Não | Não |
> | anonymousapiusers | Não | Não |
> | configuração | Não | Não |
> | logs | Não | Não |
> | relatórios | Não | Não |
> | servicehealthmetrics | Não | Não |
> | services | Não | Não |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | configurações | Não | Não |
> | generateRecommendations | Não | Não |
> | metadata | Não | Não |
> | filmes | Não | Não |
> | suppressions | Não | Não |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | actionRules | sim | sim |
> | alertas | Não | Não |
> | alertsList | Não | Não |
> | alertsMetaData | Não | Não |
> | alertsSummary | Não | Não |
> | alertsSummaryList | Não | Não |
> | Receber | Não | Não |
> | smartDetectorAlertRules | sim | sim |
> | smartDetectorRuntimeEnvironments | Não | Não |
> | smartGroups | Não | Não |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | servers | sim | sim |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | reportFeedback | Não | Não |
> | serviço | sim | sim |
> | validateServiceName | Não | Não |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | configurationStores | sim | sim |
> | configurationStores/eventGridFilters | Não | Não |

## <a name="microsoftappplatform"></a>Microsoft. AppPlatform

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | Spring | sim | sim |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | attestationProviders | Não | Não |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | classicAdministrators | Não | Não |
> | Aliases de dataalias | Não | Não |
> | denyAssignments | Não | Não |
> | elevateAccess | Não | Não |
> | findOrphanRoleAssignments | Não | Não |
> | locks | Não | Não |
> | permissões | Não | Não |
> | policyAssignments | Não | Não |
> | policyDefinitions | Não | Não |
> | policySetDefinitions | Não | Não |
> | providerOperations | Não | Não |
> | roleAssignments | Não | Não |
> | roleDefinitions | Não | Não |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | automationaccounts | sim | sim |
> | automationAccounts/configurations | sim | sim |
> | automationAccounts/runbooks | Não | Não |
> | automationAccounts/runbooks | sim | sim |
> | automationAccounts/configurations | Não | Não |
> | automationAccounts/runbooks | Não | Não |

## <a name="microsoftazconfig"></a>Microsoft. Azconfig

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | configurationStores | sim | sim |
> | configurationStores/eventGridFilters | Não | Não |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | environments | Não | Não |
> | environments/accounts | Não | Não |
> | environments/accounts/namespaces | Não | Não |
> | environments/accounts/namespaces/configurations | Não | Não |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | b2cDirectories | sim | Não |
> | b2ctenants | Não | Não |

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | hybridDataManagers | sim | sim |
> | postgresInstances | sim | sim |
> | sqlBigDataClusters | sim | sim |
> | SQLInstances | sim | sim |
> | sqlServerRegistrations | sim | sim |
> | sqlServerRegistrations/sqlservers | Não | Não |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | registrations | sim | sim |
> | registrations/customerSubscriptions | Não | Não |
> | registrations/products | Não | Não |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | batchAccounts | sim | sim |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | billingAccounts | Não | Não |
> | billingAccounts/contratos | Não | Não |
> | billingAccounts/billingPermissions | Não | Não |
> | billingAccounts/billingProfiles | Não | Não |
> | billingAccounts/billingProfiles/billingPermissions | Não | Não |
> | billingAccounts/billingProfiles/billingRoleAssignments | Não | Não |
> | billingAccounts/billingProfiles/billingRoleDefinitions | Não | Não |
> | billingAccounts/billingProfiles/billingSubscriptions | Não | Não |
> | billingAccounts/billingProfiles/createBillingRoleAssignment | Não | Não |
> | billingAccounts/billingProfiles/clientes | Não | Não |
> | billingAccounts/billingProfiles/invoices | Não | Não |
> | billingAccounts/billingProfiles/invoices/pricesheet | Não | Não |
> | billingAccounts/billingProfiles/invoiceSections | Não | Não |
> | billingAccounts/billingProfiles/invoiceSections/billingPermissions | Não | Não |
> | billingAccounts/billingProfiles/invoiceSections/billingRoleAssignments | Não | Não |
> | billingAccounts/billingProfiles/invoiceSections/billingRoleDefinitions | Não | Não |
> | billingAccounts/billingProfiles/invoiceSections/billingSubscriptions | Não | Não |
> | billingAccounts/billingProfiles/invoiceSections/createBillingRoleAssignment | Não | Não |
> | billingAccounts/billingProfiles/invoiceSections/initiateTransfer | Não | Não |
> | billingAccounts/billingProfiles/invoiceSections/produtos | Não | Não |
> | billingAccounts/billingProfiles/invoiceSections/produtos/transferência | Não | Não |
> | billingAccounts/billingProfiles/invoiceSections/Products/updateAutoRenew | Não | Não |
> | billingAccounts/billingProfiles/invoiceSections/transações | Não | Não |
> | billingAccounts/billingProfiles/invoiceSections/transferências | Não | Não |
> | billingAccounts/BillingProfiles/patchOperations | Não | Não |
> | billingAccounts/billingProfiles/paymentMethods | Não | Não |
> | billingAccounts/billingProfiles/policies | Não | Não |
> | billingAccounts/billingProfiles/pricesheet | Não | Não |
> | billingAccounts/billingProfiles/pricesheetDownloadOperations | Não | Não |
> | billingAccounts/billingProfiles/products | Não | Não |
> | billingAccounts/billingProfiles/transactions | Não | Não |
> | billingAccounts/billingRoleAssignments | Não | Não |
> | billingAccounts/billingRoleDefinitions | Não | Não |
> | billingAccounts/billingSubscriptions | Não | Não |
> | billingAccounts/createBillingRoleAssignment | Não | Não |
> | billingAccounts/createInvoiceSectionOperations | Não | Não |
> | billingAccounts/clientes | Não | Não |
> | billingAccounts/clientes/billingPermissions | Não | Não |
> | billingAccounts/clientes/billingSubscriptions | Não | Não |
> | billingAccounts/clientes/initiateTransfer | Não | Não |
> | billingAccounts/clientes/políticas | Não | Não |
> | billingAccounts/clientes/produtos | Não | Não |
> | billingAccounts/clientes/transações | Não | Não |
> | billingAccounts/clientes/transferências | Não | Não |
> | billingAccounts/departments | Não | Não |
> | billingAccounts/enrollmentAccounts | Não | Não |
> | billingAccounts/invoices | Não | Não |
> | billingAccounts/invoiceSections | Não | Não |
> | billingAccounts/invoiceSections/billingSubscriptionMoveOperations | Não | Não |
> | billingAccounts/invoiceSections/billingSubscriptions | Não | Não |
> | billingAccounts/invoiceSections/billingSubscriptions/transfer | Não | Não |
> | billingAccounts/invoiceSections/Elevate | Não | Não |
> | billingAccounts/invoiceSections/initiateTransfer | Não | Não |
> | billingAccounts/invoiceSections/patchOperations | Não | Não |
> | billingAccounts/invoiceSections/productMoveOperations | Não | Não |
> | billingAccounts/invoiceSections/products | Não | Não |
> | billingAccounts/invoiceSections/produtos/transferência | Não | Não |
> | billingAccounts/invoiceSections/Products/updateAutoRenew | Não | Não |
> | billingAccounts/invoiceSections/transações | Não | Não |
> | billingAccounts/invoiceSections/transfers | Não | Não |
> | billingAccounts/lineOfCredit | Não | Não |
> | billingAccounts/patchOperations | Não | Não |
> | billingAccounts/paymentMethods | Não | Não |
> | billingAccounts/products | Não | Não |
> | billingAccounts/transactions | Não | Não |
> | billingPeriods | Não | Não |
> | billingPermissions | Não | Não |
> | billingProperty | Não | Não |
> | billingRoleAssignments | Não | Não |
> | billingRoleDefinitions | Não | Não |
> | createBillingRoleAssignment | Não | Não |
> | departments | Não | Não |
> | enrollmentAccounts | Não | Não |
> | invoices | Não | Não |
> | transfers | Não | Não |
> | transfers/acceptTransfer | Não | Não |
> | transfers/declineTransfer | Não | Não |
> | transfers/operationStatus | Não | Não |
> | transferências/validateTransfer | Não | Não |
> | validateAddress | Não | Não |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | mapApis | sim | sim |
> | updateCommunicationPreference | Não | Não |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | blockchainMembers | sim | sim |
> | cordaMembers | sim | sim |
> | espectadores | sim | sim |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | blueprintAssignments | Não | Não |
> | blueprintAssignments/assignmentOperations | Não | Não |
> | blueprintAssignments/operations | Não | Não |
> | blueprints | Não | Não |
> | blueprints/artifacts | Não | Não |
> | blueprints/versions | Não | Não |
> | blueprints/versions/artifacts | Não | Não |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | botServices | sim | sim |
> | botServices/channels | Não | Não |
> | botServices/connections | Não | Não |
> | idiomas | Não | Não |
> | modelos | Não | Não |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | Redis | sim | sim |
> | RedisConfigDefinition | Não | Não |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | appliedReservations | Não | Não |
> | calculateExchange | Não | Não |
> | calculatePrice | Não | Não |
> | calculatePurchasePrice | Não | Não |
> | catalogs | Não | Não |
> | commercialReservationOrders | Não | Não |
> | câmbio | Não | Não |
> | placePurchaseOrder | Não | Não |
> | reservationOrders | Não | Não |
> | reservationOrders/calculateRefund | Não | Não |
> | reservationOrders/merge | Não | Não |
> | reservationOrders/reservations | Não | Não |
> | reservationOrders/reservations/revisions | Não | Não |
> | reservationOrders/return | Não | Não |
> | reservationOrders/split | Não | Não |
> | reservationOrders/swap | Não | Não |
> | reservations | Não | Não |
> | recursos | Não | Não |
> | validateReservationOrder | Não | Não |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | Não | Não |
> | CdnWebApplicationFirewallPolicies | sim | sim |
> | edgenodes | Não | Não |
> | perfis | sim | sim |
> | profiles/endpoints | sim | sim |
> | profiles/endpoints/origins | Não | Não |
> | profiles/endpoints/origins | Não | Não |
> | validateProbe | Não | Não |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | certificateOrders | sim | sim |
> | certificateOrders/certificates | Não | Não |
> | validateCertificateRegistrationInformation | Não | Não |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | recursos | Não | Não |
> | domainNames | Não | Não |
> | domainNames/capabilities | Não | Não |
> | domainNames/internalLoadBalancers | Não | Não |
> | domainNames/serviceCertificates | Não | Não |
> | domainNames/slots | Não | Não |
> | domainNames/slots | Não | Não |
> | nome_do_domínio/Slots/funções/metricDefinitions | Não | Não |
> | DomainNames/Slots/funções/métricas | Não | Não |
> | moveSubscriptionResources | Não | Não |
> | operatingSystemFamilies | Não | Não |
> | operatingSystems | Não | Não |
> | quotas | Não | Não |
> | resourceTypes | Não | Não |
> | validateSubscriptionMoveAvailability | Não | Não |
> | virtualMachines | Não | Não |
> | virtualMachines/diagnosticSettings | Não | Não |
> | virtualMachines/metricDefinitions | Não | Não |
> | virtualMachines/metrics | Não | Não |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | Migração de infraestrutura clássica | Não | Não |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | recursos | Não | Não |
> | expressRouteCrossConnections | Não | Não |
> | expressRouteCrossConnections/peerings | Não | Não |
> | gatewaySupportedDevices | Não | Não |
> | networkSecurityGroups | Não | Não |
> | quotas | Não | Não |
> | reservedIps | Não | Não |
> | virtualNetworks | Não | Não |
> | virtualNetworks/virtualNetworkPeerings | Não | Não |
> | virtualNetworks/virtualNetworkPeerings | Não | Não |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | recursos | Não | Não |
> | disks | Não | Não |
> | images | Não | Não |
> | osImages | Não | Não |
> | osPlatformImages | Não | Não |
> | publicImages | Não | Não |
> | quotas | Não | Não |
> | storageAccounts | Não | Não |
> | storageAccounts/blobServices | Não | Não |
> | storageAccounts/blobServices | Não | Não |
> | storageAccounts/metricDefinitions | Não | Não |
> | storageAccounts/métricas | Não | Não |
> | storageAccounts/blobServices | Não | Não |
> | storageAccounts/services | Não | Não |
> | storageAccounts/services/diagnosticSettings | Não | Não |
> | storageAccounts/services/metricDefinitions | Não | Não |
> | storageAccounts/serviços/métricas | Não | Não |
> | storageAccounts/blobServices | Não | Não |
> | storageAccounts/vmImages | Não | Não |
> | vmImages | Não | Não |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | accounts | sim | sim |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | RateCard | Não | Não |
> | UsageAggregates | Não | Não |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | availabilitySets | sim | sim |
> | diskEncryptionSets | sim | sim |
> | disks | sim | sim |
> | galleries | sim | sim |
> | galerias/aplicativos | Não | Não |
> | galerias/aplicativos/versões | Não | Não |
> | galleries/images | Não | Não |
> | galleries/images/versions | Não | Não |
> | Hosts | sim | sim |
> | Hosts/hosts | sim | sim |
> | images | sim | sim |
> | proximityPlacementGroups | sim | sim |
> | restorePointCollections | sim | sim |
> | restorePointCollections/restorePoints | Não | Não |
> | sharedVMExtensions | sim | sim |
> | sharedVMExtensions/versões | Não | Não |
> | sharedVMImages | sim | sim |
> | sharedVMImages/versions | Não | Não |
> | snapshots | sim | sim |
> | virtualMachines | sim | sim |
> | virtualMachines/extensions | sim | sim |
> | virtualMachines/metricDefinitions | Não | Não |
> | virtualMachineScaleSets | sim | sim |
> | virtualMachines/extensions | Não | Não |
> | virtualMachineScaleSets/networkInterfaces | Não | Não |
> | virtualMachineScaleSets/publicIPAddresses | Não | Não |
> | virtualMachineScaleSets/virtualMachines | Não | Não |
> | virtualMachineScaleSets/networkInterfaces | Não | Não |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | AggregatedCost | Não | Não |
> | Saldos | Não | Não |
> | Orçamentos | Não | Não |
> | Charges | Não | Não |
> | CostTags | Não | Não |
> | credits | Não | Não |
> | events | Não | Não |
> | Previsões | Não | Não |
> | lots | Não | Não |
> | Marketplaces | Não | Não |
> | Pricesheets | Não | Não |
> | products | Não | Não |
> | ReservationDetails | Não | Não |
> | ReservationRecommendations | Não | Não |
> | ReservationSummaries | Não | Não |
> | ReservationTransactions | Não | Não |
> | Marcas | Não | Não |
> | tenants | Não | Não |
> | Termos | Não | Não |
> | UsageDetails | Não | Não |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | containerGroups | sim | sim |
> | serviceAssociationLinks | Não | Não |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | registries | sim | sim |
> | registries/builds | Não | Não |
> | registries/builds/cancel | Não | Não |
> | registries/builds/getLogLink | Não | Não |
> | registries/buildTasks | sim | sim |
> | registries/buildTasks/steps | Não | Não |
> | registries/eventGridFilters | Não | Não |
> | registros/generateCredentials | Não | Não |
> | registries/getBuildSourceUploadUrl | Não | Não |
> | registries/GetCredentials | Não | Não |
> | registries/importImage | Não | Não |
> | registries/queueBuild | Não | Não |
> | registries/regenerateCredential | Não | Não |
> | registries/regenerateCredentials | Não | Não |
> | registries/replications | sim | sim |
> | registries/runs | Não | Não |
> | registries/runs/cancel | Não | Não |
> | registries/scheduleRun | Não | Não |
> | registros/scopeMaps | Não | Não |
> | registries/tasks | sim | sim |
> | registros/tokens | Não | Não |
> | registries/updatePolicies | Não | Não |
> | registries/webhooks | sim | sim |
> | registries/webhooks/getCallbackConfig | Não | Não |
> | registries/webhooks/ping | Não | Não |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | containerServices | sim | sim |
> | managedClusters | sim | sim |
> | openShiftManagedClusters | sim | sim |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | accounts | sim | sim |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | Alertas | Não | Não |
> | BillingAccounts | Não | Não |
> | Orçamentos | Não | Não |
> | CloudConnectors | Não | Não |
> | Conectores | sim | sim |
> | Departments | Não | Não |
> | Dimensões | Não | Não |
> | EnrollmentAccounts | Não | Não |
> | Exportações | Não | Não |
> | ExternalBillingAccounts | Não | Não |
> | ExternalBillingAccounts/alertas | Não | Não |
> | ExternalBillingAccounts/dimensões | Não | Não |
> | ExternalBillingAccounts/previsão | Não | Não |
> | ExternalBillingAccounts/consulta | Não | Não |
> | ExternalSubscriptions | Não | Não |
> | ExternalSubscriptions/alertas | Não | Não |
> | ExternalSubscriptions/dimensões | Não | Não |
> | ExternalSubscriptions/previsão | Não | Não |
> | ExternalSubscriptions/consulta | Não | Não |
> | Visto | Não | Não |
> | Consultar | Não | Não |
> | register | Não | Não |
> | Reportconfigs | Não | Não |
> | Relatórios | Não | Não |
> | Configurações | Não | Não |
> | showbackRules | Não | Não |
> | Modos de exibição | Não | Não |

## <a name="microsoftcustomerlockbox"></a>Microsoft. CustomerLockbox

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | solicitações | Não | Não |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | SAS | Não | Não |
> | resourceProviders | sim | sim |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | jobs | sim | sim |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | DataBoxEdgeDevices | sim | sim |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | workspaces | sim | Não |
> | workspaces/virtualNetworkPeerings | Não | Não |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | catalogs | sim | sim |
> | catálogos de | sim | sim |
> | datacatalogs/fontes de fonte | Não | Não |
> | datacatalogs/fontes de pesquisa/verificações | Não | Não |
> | datacatalogs/fontes/exames/conjuntos de valores | Não | Não |
> | datacatalogs/fontes/exames/gatilhos | Não | Não |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | dataFactories | sim | Não |
> | dataFactories/diagnosticSettings | Não | Não |
> | dataFactories/metricDefinitions | Não | Não |
> | dataFactorySchema | Não | Não |
> | factories | sim | Não |
> | factories/integrationRuntimes | Não | Não |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | accounts | sim | sim |
> | accounts/dataLakeStoreAccounts | Não | Não |
> | accounts/storageAccounts | Não | Não |
> | accounts/storageAccounts/containers | Não | Não |
> | contas/transferAnalyticsUnits | Não | Não |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | accounts | sim | sim |
> | accounts/eventGridFilters | Não | Não |
> | accounts/firewallRules | Não | Não |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | services | Não | Não |
> | services/projects | Não | Não |

## <a name="microsoftdatashare"></a>Microsoft. DataShare

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | accounts | sim | sim |
> | contas/compartilhamentos | Não | Não |
> | contas/compartilhamentos/conjuntos de valores | Não | Não |
> | contas/compartilhamentos/convites | Não | Não |
> | contas/compartilhamentos/providersharesubscriptions | Não | Não |
> | contas/compartilhamentos/synchronizationSettings | Não | Não |
> | contas/sharesubscriptions | Não | Não |
> | contas/sharesubscriptions/consumerSourceDataSets | Não | Não |
> | contas/sharesubscriptions/datasetmappings | Não | Não |
> | contas/sharesubscriptions/gatilhos | Não | Não |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | servers | sim | sim |
> | servers/advisors | Não | Não |
> | servidores/privateEndpointConnectionProxies | Não | Não |
> | servidores/privateEndpointConnections | Não | Não |
> | servidores/privateLinkResources | Não | Não |
> | servers/queryTexts | Não | Não |
> | servers/recoverableServers | Não | Não |
> | servers/topQueryStatistics | Não | Não |
> | servers/virtualNetworkRules | Não | Não |
> | servers/waitStatistics | Não | Não |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | servers | sim | sim |
> | servers/advisors | Não | Não |
> | servidores/privateEndpointConnectionProxies | Não | Não |
> | servidores/privateEndpointConnections | Não | Não |
> | servidores/privateLinkResources | Não | Não |
> | servers/queryTexts | Não | Não |
> | servers/recoverableServers | Não | Não |
> | servers/topQueryStatistics | Não | Não |
> | servers/virtualNetworkRules | Não | Não |
> | servers/waitStatistics | Não | Não |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | serverGroups | sim | sim |
> | servers | sim | sim |
> | servers/advisors | Não | Não |
> | servers/keys | Não | Não |
> | servidores/privateEndpointConnectionProxies | Não | Não |
> | servidores/privateEndpointConnections | Não | Não |
> | servidores/privateLinkResources | Não | Não |
> | servers/queryTexts | Não | Não |
> | servers/recoverableServers | Não | Não |
> | servers/topQueryStatistics | Não | Não |
> | servers/virtualNetworkRules | Não | Não |
> | servers/waitStatistics | Não | Não |
> | serversv2 | sim | sim |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | artifactSources | sim | sim |
> | rollouts | sim | sim |
> | pertopologias | sim | sim |
> | serviços e pertopologias | sim | sim |
> | pertopologias/serviços/unidades de serviço | sim | sim |
> | etapas | sim | sim |

## <a name="microsoftdesktopvirtualization"></a>Microsoft. DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | applicationgroups | sim | sim |
> | applicationgroups/aplicativos | Não | Não |
> | applicationgroups/desktops | Não | Não |
> | applicationgroups/startmenuitems | Não | Não |
> | hostpools | sim | sim |
> | hostpools/sessionhosts | Não | Não |
> | hostpools/sessionhosts/usersessions | Não | Não |
> | hostpools/usersessions | Não | Não |
> | workspaces | sim | sim |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | ElasticPools | sim | sim |
> | ElasticPools/IotHubTenants | sim | sim |
> | IotHubs | sim | sim |
> | IotHubs/eventGridFilters | Não | Não |
> | ProvisioningServices | sim | sim |
> | usages | Não | Não |

## <a name="microsoftdevops"></a>Microsoft. DevOps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | pipelines | sim | sim |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | controladores | sim | sim |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | labcenters | sim | sim |
> | labs | sim | sim |
> | laboratórios/ambientes | sim | sim |
> | labs/serviceRunners | sim | sim |
> | labs/virtualMachines | sim | sim |
> | schedules | sim | sim |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | databaseAccounts | Não | Não |
> | databaseAccounts | sim | sim |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | domains | sim | sim |
> | domains/domainOwnershipIdentifiers | Não | Não |
> | generateSsoRequest | Não | Não |
> | topLevelDomains | Não | Não |
> | validateDomainRegistrationInformation | Não | Não |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | lcsprojects | Não | Não |
> | lcsprojects/clouddeployments | Não | Não |
> | lcsprojects/connectors | Não | Não |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | services | sim | sim |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | domains | sim | sim |
> | domains/topics | Não | Não |
> | eventSubscriptions | Não | Não |
> | extensionTopics | Não | Não |
> | topics | sim | sim |
> | topicTypes | Não | Não |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | clusters | sim | sim |
> | namespaces | sim | sim |
> | namespaces/authorizationrules | Não | Não |
> | namespaces/disasterrecoveryconfigs | Não | Não |
> | namespaces/eventhubs | Não | Não |
> | namespaces/eventhubs/authorizationrules | Não | Não |
> | namespaces/eventhubs/consumergroups | Não | Não |
> | namespaces/networkrulesets | Não | Não |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | recursos | Não | Não |
> | providers | Não | Não |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | enroll | Não | Não |
> | galleryitems | Não | Não |
> | generateartifactaccessuri | Não | Não |
> | myareas | Não | Não |
> | myareas/areas | Não | Não |
> | myareas/areas/areas | Não | Não |
> | myareas/areas/areas/galleryitems | Não | Não |
> | myareas/areas/galleryitems | Não | Não |
> | myareas/galleryitems | Não | Não |
> | register | Não | Não |
> | recursos | Não | Não |
> | retrieveresourcesbyid | Não | Não |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | accounts | sim | sim |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | autoManagedVmConfigurationProfiles | sim | sim |
> | configurationProfileAssignments | Não | Não |
> | guestConfigurationAssignments | Não | Não |
> | software | Não | Não |
> | softwareUpdateProfile | Não | Não |
> | softwareUpdates | Não | Não |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | hanaInstances | sim | sim |
> | sapMonitors | sim | sim |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | dedicatedHSMs | sim | sim |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | clusters | sim | sim |
> | clusters/applications | Não | Não |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | services | sim | sim |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | maquina | sim | sim |
> | máquinas/extensões | sim | sim |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | Gerenciadores de DataManager | sim | sim |

## <a name="microsofthydra"></a>Microsoft. Hydra

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | components | sim | sim |
> | networkScopes | sim | sim |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | jobs | sim | sim |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Não | Não |
> | diagnosticSettingsCategories | Não | Não |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | appTemplates | Não | Não |
> | IoTApps | sim | sim |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | Grafo | sim | sim |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | deletedVaults | Não | Não |
> | hsmPools | sim | sim |
> | vaults | sim | sim |
> | vaults/accessPolicies | Não | Não |
> | cofres/eventGridFilters | Não | Não |
> | vaults/secrets | Não | Não |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | clusters | sim | sim |
> | clusters/attacheddatabaseconfigurations | Não | Não |
> | clusters/databases | Não | Não |
> | clusters/databases/dataconnections | Não | Não |
> | clusters/databases/eventhubconnections | Não | Não |
> | clusters/sharedidentities | Não | Não |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | labaccounts | sim | sim |
> | users | Não | Não |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | hostingEnvironments | sim | sim |
> | integrationAccounts | sim | sim |
> | integrationServiceEnvironments | sim | sim |
> | integrationServiceEnvironments/managedApis | sim | sim |
> | isolatedEnvironments | sim | sim |
> | workflows | sim | sim |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | commitmentPlans | sim | sim |
> | webServices | sim | sim |
> | Workspaces | sim | sim |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | workspaces | sim | sim |
> | workspaces/computes | Não | Não |
> | espaços de trabalho/eventGridFilters | Não | Não |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | Identidades | Não | Não |
> | userAssignedIdentities | sim | sim |

## <a name="microsoftmanagedservices"></a>Microsoft. Managedservices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | marketplaceRegistrationDefinitions | Não | Não |
> | registrationAssignments | Não | Não |
> | registrationDefinitions | Não | Não |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | getEntities | Não | Não |
> | managementGroups | Não | Não |
> | recursos | Não | Não |
> | startTenantBackfill | Não | Não |
> | tenantBackfillStatus | Não | Não |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | accounts | sim | sim |
> | accounts/eventGridFilters | Não | Não |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | offers | Não | Não |
> | offerTypes | Não | Não |
> | offerTypes/publishers | Não | Não |
> | offerTypes/publishers/offers | Não | Não |
> | offerTypes/publishers/offers/plans | Não | Não |
> | offerTypes/publishers/offers/plans/agreements | Não | Não |
> | offerTypes/publishers/offers/plans/configs | Não | Não |
> | offerTypes/publishers/offers/plans/configs/importImage | Não | Não |
> | privategalleryitems | Não | Não |
> | products | Não | Não |
> | publicado | Não | Não |
> | editores/ofertas | Não | Não |
> | editores/ofertas/emendas | Não | Não |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | classicDevServices | sim | sim |
> | updateCommunicationPreference | Não | Não |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | Contratos | Não | Não |
> | offertypes | Não | Não |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | mediaservices | sim | sim |
> | mediaservices/accountFilters | Não | Não |
> | mediaservices/assets | Não | Não |
> | mediaservices/assets/assetFilters | Não | Não |
> | mediaservices/contentKeyPolicies | Não | Não |
> | mediaservices/eventGridFilters | Não | Não |
> | mediaservices/liveEvents | Não | Não |
> | mediaservices/liveEvents | sim | sim |
> | mediaservices/liveEvents/liveOutputs | Não | Não |
> | mediaservices/liveEvents | Não | Não |
> | mediaservices/mediaGraphs | Não | Não |
> | mediaservices/streamingEndpoints | Não | Não |
> | mediaservices/streamingEndpoints | sim | sim |
> | mediaservices/streamingLocators | Não | Não |
> | mediaservices/streamingLocators | Não | Não |
> | mediaservices/transforms | Não | Não |
> | mediaservices/transforms | Não | Não |

## <a name="microsoftmicroservices4spring"></a>Microsoft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | appClusters | sim | sim |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | assessmentProjects | sim | sim |
> | migrateprojects | sim | sim |
> | projects | sim | sim |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | holographicsBroadcastAccounts | sim | sim |
> | objectUnderstandingAccounts | sim | sim |
> | remoteRenderingAccounts | sim | sim |
> | spatialAnchorsAccounts | sim | sim |
> | surfaceReconstructionAccounts | sim | sim |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | netAppAccounts | sim | Não |
> | netAppAccounts/capacityPools | sim | Não |
> | netAppAccounts/capacityPools/volumes | sim | Não |
> | netAppAccounts/capacityPools/volumes/mountTargets | sim | Não |
> | netAppAccounts/capacityPools/volumes/instantâneos | sim | Não |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | applicationGateways | sim | sim |
> | applicationGatewayWebApplicationFirewallPolicies | sim | sim |
> | applicationSecurityGroups | sim | sim |
> | azureFirewallFqdnTags | Não | Não |
> | azureFirewalls | sim | Não |
> | bastionHosts | sim | sim |
> | bgpServiceCommunities | Não | Não |
> | connections | sim | sim |
> | ddosCustomPolicies | sim | sim |
> | ddosProtectionPlans | sim | sim |
> | dnsOperationStatuses | Não | Não |
> | dnszones | sim | sim |
> | dnszones/A | Não | Não |
> | dnszones/AAAA | Não | Não |
> | dnszones/all | Não | Não |
> | dnszones/CAA | Não | Não |
> | dnszones/CNAME | Não | Não |
> | dnszones/MX | Não | Não |
> | dnszones/NS | Não | Não |
> | dnszones/PTR | Não | Não |
> | dnszones/recordsets | Não | Não |
> | dnszones/SOA | Não | Não |
> | dnszones/SRV | Não | Não |
> | dnszones/TXT | Não | Não |
> | expressRouteCircuits | sim | sim |
> | expressRouteCrossConnections | sim | sim |
> | expressRouteGateways | sim | sim |
> | expressRoutePorts | sim | sim |
> | expressRouteServiceProviders | Não | Não |
> | firewallPolicies | sim | sim |
> | frontdoors | Sim, mas limitado (veja a [Observação abaixo](#frontdoor)) | sim |
> | frontdoorWebApplicationFirewallManagedRuleSets | Sim, mas limitado (veja a [Observação abaixo](#frontdoor)) | Não |
> | frontdoorWebApplicationFirewallPolicies | Sim, mas limitado (veja a [Observação abaixo](#frontdoor)) | sim |
> | getDnsResourceReference | Não | Não |
> | internalNotify | Não | Não |
> | loadBalancers | sim | Não |
> | virtualNetworkGateways | sim | sim |
> | natGateways | sim | sim |
> | networkIntentPolicies | sim | sim |
> | networkInterfaces | sim | sim |
> | networkProfiles | sim | sim |
> | networkSecurityGroups | sim | sim |
> | networkWatchers | sim | Não |
> | networkWatchers/connectionMonitors | sim | Não |
> | networkWatchers/lenses | sim | Não |
> | networkWatchers/lenses | sim | Não |
> | p2sVpnGateways | sim | sim |
> | privateDnsOperationStatuses | Não | Não |
> | privateDnsZones | sim | sim |
> | privateDnsZones/A | Não | Não |
> | privateDnsZones/AAAA | Não | Não |
> | privateDnsZones/tudo | Não | Não |
> | privateDnsZones/CNAME | Não | Não |
> | privateDnsZones/MX | Não | Não |
> | privateDnsZones/PTR | Não | Não |
> | privateDnsZones/SOA | Não | Não |
> | privateDnsZones/SRV | Não | Não |
> | privateDnsZones/TXT | Não | Não |
> | privateDnsZones/virtualNetworkLinks | sim | sim |
> | privateEndpoints | sim | sim |
> | privateLinkServices | sim | sim |
> | publicIPAddresses | sim | sim |
> | publicIPPrefixes | sim | sim |
> | routeFilters | sim | sim |
> | routeTables | sim | sim |
> | serviceEndpointPolicies | sim | sim |
> | trafficManagerGeographicHierarchies | Não | Não |
> | trafficmanagerprofiles | sim | sim |
> | trafficmanagerprofiles/heatMaps | Não | Não |
> | trafficManagerUserMetricsKeys | Não | Não |
> | virtualWans | sim | sim |
> | virtualNetworkGateways | sim | sim |
> | virtualNetworks | sim | sim |
> | virtualNetworkTaps | sim | sim |
> | virtualWans | sim | sim |
> | vpnGateways | sim | Não |
> | vpnSites | sim | sim |
> | frontdoorWebApplicationFirewallPolicies | sim | sim |

<a id="frontdoor" />

> [!NOTE]
> Para o serviço de porta frontal do Azure, você pode aplicar marcas ao criar o recurso, mas a atualização ou adição de marcas não tem suporte no momento.


## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | namespaces | sim | Não |
> | namespaces/notificationHubs | sim | Não |

## <a name="microsoftobjectstore"></a>Microsoft. ObjectStore

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | osNamespaces | sim | sim |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | HyperVSites | sim | sim |
> | ImportSites | sim | sim |
> | ServerSites | sim | sim |
> | VMwareSites | sim | sim |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | clusters | sim | sim |
> | dispositivos | Não | Não |
> | linkTargets | Não | Não |
> | storageInsightConfigs | Não | Não |
> | workspaces | sim | sim |
> | workspaces/dataSources | Não | Não |
> | workspaces/linkedServices | Não | Não |
> | workspaces/query | Não | Não |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | managementassociations | Não | Não |
> | managementconfigurations | sim | sim |
> | solutions | sim | sim |
> | Modos de exibição | sim | sim |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | legacyPeerings | Não | Não |
> | peerAsns | Não | Não |
> | emparelhamentos | sim | sim |
> | peeringServiceProviders | Não | Não |
> | peeringServices | sim | sim |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | policyEvents | Não | Não |
> | policyMetadata | Não | Não |
> | policyStates | Não | Não |
> | policyTrackedResources | Não | Não |
> | remediations | Não | Não |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | consoles | Não | Não |
> | dashboards | sim | sim |
> | userSettings | Não | Não |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | workspaceCollections | sim | sim |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | capacities | sim | sim |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | backupProtectedItems | Não | Não |
> | vaults | sim | sim |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | namespaces | sim | sim |
> | namespaces/authorizationrules | Não | Não |
> | namespaces/hybridconnections | Não | Não |
> | namespaces/hybridconnections/authorizationrules | Não | Não |
> | namespaces/wcfrelays | Não | Não |
> | namespaces/wcfrelays/authorizationrules | Não | Não |

## <a name="microsoftremoteapp"></a>Microsoft. RemoteApp

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | accounts | Não | Não |
> | colec | sim | sim |
> | coleções/aplicativos | Não | Não |
> | coleções/SecurityPrincipals | Não | Não |
> | templateImages | Não | Não |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | consultas | sim | sim |
> | resourceChangeDetails | Não | Não |
> | resourceChanges | Não | Não |
> | recursos | Não | Não |
> | resourcesHistory | Não | Não |
> | subscriptionsStatus | Não | Não |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | availabilityStatuses | Não | Não |
> | childAvailabilityStatuses | Não | Não |
> | childResources | Não | Não |
> | events | Não | Não |
> | impactedResources | Não | Não |
> | metadata | Não | Não |
> | Notificações | Não | Não |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | deployments | sim | Não |
> | deployments/operations | Não | Não |
> | deploymentScripts | sim | sim |
> | deploymentScripts/logs | Não | Não |
> | links | Não | Não |
> | notifyResourceJobs | Não | Não |
> | providers | Não | Não |
> | resourceGroups | sim | Não |
> | recursos | Não | Não |
> | subscriptions | Não | Não |
> | subscriptions/providers | Não | Não |
> | subscriptions/resourceGroups | Não | Não |
> | subscriptions/resourcegroups/resources | Não | Não |
> | subscriptions/resources | Não | Não |
> | subscriptions/tagnames | Não | Não |
> | subscriptions/tagNames/tagValues | Não | Não |
> | tenants | Não | Não |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | de dimensionamento da Web | sim | sim |
> | saasresources | Não | Não |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | jobcollections | sim | sim |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | resourceHealthMetadata | Não | Não |
> | searchServices | sim | sim |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | adaptiveNetworkHardenings | Não | Não |
> | advancedThreatProtectionSettings | Não | Não |
> | alertas | Não | Não |
> | allowedConnections | Não | Não |
> | applicationWhitelistings | Não | Não |
> | assessmentMetadata | Não | Não |
> | Avaliações | Não | Não |
> | automações | sim | sim |
> | AutoProvisioningSettings | Não | Não |
> | Compliances | Não | Não |
> | dataCollectionAgents | Não | Não |
> | deviceSecurityGroups | Não | Não |
> | discoveredSecuritySolutions | Não | Não |
> | externalSecuritySolutions | Não | Não |
> | InformationProtectionPolicies | Não | Não |
> | iotSecuritySolutions | sim | sim |
> | iotSecuritySolutions/analyticsModels | Não | Não |
> | iotSecuritySolutions/analyticsModels/aggregatedAlerts | Não | Não |
> | iotSecuritySolutions/analyticsModels/aggregatedRecommendations | Não | Não |
> | jitNetworkAccessPolicies | Não | Não |
> | networkData | Não | Não |
> | playbookConfigurations | sim | sim |
> | políticas | Não | Não |
> | pricings | Não | Não |
> | regulatoryComplianceStandards | Não | Não |
> | regulatoryComplianceStandards/regulatoryComplianceControls | Não | Não |
> | regulatoryComplianceStandards/regulatoryComplianceControls/regulatoryComplianceAssessments | Não | Não |
> | securityContacts | Não | Não |
> | securitySolutions | Não | Não |
> | securitySolutionsReferenceData | Não | Não |
> | securityStatuses | Não | Não |
> | securityStatusesSummaries | Não | Não |
> | serverVulnerabilityAssessments | Não | Não |
> | Configurações | Não | Não |
> | subavaliações | Não | Não |
> | tarefas | Não | Não |
> | topologies | Não | Não |
> | workspaceSettings | Não | Não |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Não | Não |
> | diagnosticSettingsCategories | Não | Não |

## <a name="microsoftsecurityinsights"></a>Microsoft. SecurityInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | agregações | Não | Não |
> | alertRules | Não | Não |
> | alertRuleTemplates | Não | Não |
> | indicadores | Não | Não |
> | cases | Não | Não |
> | dataconnecters | Não | Não |
> | entidades | Não | Não |
> | entityQueries | Não | Não |
> | officeConsents | Não | Não |
> | Configurações | Não | Não |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | namespaces | sim | Não |
> | namespaces/authorizationrules | Não | Não |
> | namespaces/disasterrecoveryconfigs | Não | Não |
> | namespaces/eventgridfilters | Não | Não |
> | namespaces/networkrulesets | Não | Não |
> | namespaces/queues | Não | Não |
> | namespaces/queues/authorizationrules | Não | Não |
> | namespaces/topics | Não | Não |
> | namespaces/topics/authorizationrules | Não | Não |
> | namespaces/topics/subscriptions | Não | Não |
> | namespaces/topics/subscriptions/rules | Não | Não |
> | premiumMessagingRegions | Não | Não |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | de dimensionamento da Web | sim | sim |
> | clusters | sim | sim |
> | clusters/applications | Não | Não |
> | containerGroups | sim | sim |
> | containerGroupSets | sim | sim |
> | edgeclusters | sim | sim |
> | edgeclusters/aplicativos | Não | Não |
> | networks | sim | sim |
> | secretstores | sim | sim |
> | secretstores/certificados | Não | Não |
> | secretstores/segredos | Não | Não |
> | volumes | sim | sim |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | de dimensionamento da Web | sim | sim |
> | containerGroups | sim | sim |
> | gateways | sim | sim |
> | networks | sim | sim |
> | segredos | sim | sim |
> | volumes | sim | sim |

## <a name="microsoftservices"></a>Microsoft. Services

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | providerRegistrations | Não | Não |
> | providerRegistrations/resourceTypeRegistrations | Não | Não |
> | rollouts | sim | sim |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | SignalR | sim | sim |
> | Sinalização/eventGridFilters | Não | Não |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | SiteRecoveryVault | sim | sim |

## <a name="microsoftsoftwareplan"></a>Microsoft. SoftwarePlan

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | hybridUseBenefits | Não | Não |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | applicationDefinitions | sim | sim |
> | de dimensionamento da Web | sim | sim |
> | jitRequests | sim | sim |

## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | managedInstances | sim | sim |
> | managedInstances/databases | Sim (veja a [Observação abaixo](#sqlnote)) | sim |
> | managedInstances/databases/backupShortTermRetentionPolicies | Não | Não |
> | managedInstances/databases/schemas/tables/columns/sensitivityLabels | Não | Não |
> | managedInstances/databases/vulnerabilityAssessments | Não | Não |
> | managedInstances/databases/vulnerabilityAssessments/rules/baselines | Não | Não |
> | managedInstances/encryptionProtector | Não | Não |
> | managedInstances/keys | Não | Não |
> | managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | Não | Não |
> | managedInstances/vulnerabilityAssessments | Não | Não |
> | servers | sim | sim |
> | servers/administrators | Não | Não |
> | servers/communicationLinks | Não | Não |
> | servers/databases | Sim (veja a [Observação abaixo](#sqlnote)) | sim |
> | servers/encryptionProtector | Não | Não |
> | servers/firewallRules | Não | Não |
> | servers/keys | Não | Não |
> | servers/restorableDroppedDatabases | Não | Não |
> | servers/serviceobjectives | Não | Não |
> | servers/tdeCertificates | Não | Não |
> | virtualClusters | Não | Não |

<a id="sqlnote" />

> [!NOTE]
> O banco de dados Mestre não oferece suporte a marcas, mas outros bancos de dados, incluindo os bancos de dados do SQL Data Warehouse do Azure, sim. Os bancos de dados do SQL Data Warehouse do Azure devem estar no estado Ativo (não Pausado).

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | SqlVirtualMachineGroups | sim | sim |
> | SqlVirtualMachineGroups/AvailabilityGroupListeners | Não | Não |
> | SqlVirtualMachines | sim | sim |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | storageAccounts | sim | sim |
> | storageAccounts/blobServices | Não | Não |
> | storageAccounts/blobServices | Não | Não |
> | storageAccounts/blobServices | Não | Não |
> | storageAccounts/services | Não | Não |
> | storageAccounts/services/metricDefinitions | Não | Não |
> | storageAccounts/blobServices | Não | Não |
> | usages | Não | Não |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | caches | sim | sim |
> | caches/storageTargets | Não | Não |
> | usageModels | Não | Não |

## <a name="microsoftstoragereplication"></a>Microsoft. StorageReplication

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | replicationGroups | Não | Não |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | sim | sim |
> | storageSyncServices/registeredServers | Não | Não |
> | storageSyncServices/syncGroups | Não | Não |
> | storageSyncServices/syncGroups/cloudEndpoints | Não | Não |
> | storageSyncServices/syncGroups/cloudEndpoints | Não | Não |
> | storageSyncServices/workflows | Não | Não |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | sim | sim |
> | storageSyncServices/registeredServers | Não | Não |
> | storageSyncServices/syncGroups | Não | Não |
> | storageSyncServices/syncGroups/cloudEndpoints | Não | Não |
> | storageSyncServices/syncGroups/cloudEndpoints | Não | Não |
> | storageSyncServices/workflows | Não | Não |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | sim | sim |
> | storageSyncServices/registeredServers | Não | Não |
> | storageSyncServices/syncGroups | Não | Não |
> | storageSyncServices/syncGroups/cloudEndpoints | Não | Não |
> | storageSyncServices/syncGroups/cloudEndpoints | Não | Não |
> | storageSyncServices/workflows | Não | Não |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | managers | sim | sim |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | streamingjobs | Sim (veja a observação a seguir) | sim |

> [!NOTE]
> Você não pode adicionar uma marca quando streamingjobs estiver em execução. Pare o recurso para adicionar uma marca.

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | Cancelar | Não | Não |
> | CreateSubscription | Não | Não |
> | enable | Não | Não |
> | nome | Não | Não |
> | SubscriptionDefinitions | Não | Não |
> | SubscriptionDefinitions | Não | Não |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | environments | sim | Não |
> | environments/accessPolicies | Não | Não |
> | environments/eventsources | sim | Não |
> | environments/referenceDataSets | sim | Não |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | dedicatedCloudNodes | sim | sim |
> | dedicatedCloudServices | sim | sim |
> | virtualMachines | sim | sim |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | apiManagementAccounts | Não | Não |
> | apiManagementAccounts/apis/connections/connectionAcls | Não | Não |
> | apiManagementAccounts/apis | Não | Não |
> | apiManagementAccounts/apis/connections/connectionAcls | Não | Não |
> | apiManagementAccounts/apis/connections/connectionAcls | Não | Não |
> | apiManagementAccounts/apis/connections/connectionAcls | Não | Não |
> | apiManagementAccounts/apis/connections/connectionAcls | Não | Não |
> | apiManagementAccounts/apis/localizedDefinitions | Não | Não |
> | apiManagementAccounts/connectionAcls | Não | Não |
> | apiManagementAccounts/connections | Não | Não |
> | billingMeters | Não | Não |
> | certificates | sim | sim |
> | connectionGateways | sim | sim |
> | connections | sim | sim |
> | customApis | sim | sim |
> | deletedSites | Não | Não |
> | funções | Não | Não |
> | hostingEnvironments | sim | sim |
> | hostingEnvironments/multiRolePools | Não | Não |
> | hostingEnvironments/multiRolePools | Não | Não |
> | publishingUsers | Não | Não |
> | filmes | Não | Não |
> | resourceHealthMetadata | Não | Não |
> | runtimes | Não | Não |
> | serverFarms | sim | sim |
> | serverFarms/eventGridFilters | Não | Não |
> | sites | sim | sim |
> | sites/configuração  | Não | Não |
> | sites/eventGridFilters | Não | Não |
> | sites/hostNameBindings | Não | Não |
> | sites/networkConfig | Não | Não |
> | sites/premieraddons | sim | sim |
> | sites/slots | sim | sim |
> | sites/Slots/eventGridFilters | Não | Não |
> | sites/hostNameBindings | Não | Não |
> | sites/Slots/networkConfig | Não | Não |
> | sourceControls | Não | Não |
> | validade | Não | Não |
> | verifyHostingEnvironmentVnet | Não | Não |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Não | Não |
> | diagnosticSettingsCategories | Não | Não |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | DeviceServices | sim | sim |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | components | Não | Não |
> | componentsSummary | Não | Não |
> | monitorInstances | Não | Não |
> | monitorInstancesSummary | Não | Não |
> | monitors | Não | Não |
> | notificationSettings | Não | Não |

## <a name="next-steps"></a>Próximas etapas

Para saber como aplicar tags a recursos, consulte [Use tags para organizar seus recursos do Azure](resource-group-using-tags.md).
