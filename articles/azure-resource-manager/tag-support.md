---
title: Suporte à marca do Azure Resource Manager para recursos
description: Mostra quais tipos de recursos do Azure suportam tags. Fornece detalhes para todos os serviços do Azure.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 10/27/2019
ms.author: tomfitz
ms.openlocfilehash: 6b64eb955476a8ab5307b7d508484c290fd7660a
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73162163"
---
# <a name="tag-support-for-azure-resources"></a>Suporte a marcas para recursos do Azure
Este artigo descreve se um tipo de recurso dá suporte a [marcas](resource-group-using-tags.md). A coluna rotulada **dá suporte a marcas** indica se o tipo de recurso tem uma propriedade para a marca. A coluna rotulada como **marca no relatório de custo** indica se esse tipo de recurso passa a marca para o relatório de custo.

Para obter os mesmos dados como um arquivo de valores separados por vírgula, baixe [tag-support.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv).

Ir para um namespace do provedor de recursos:
> [!div class="op_single_selector"]
> - [Microsoft. AAD](#microsoftaad)
> - [Microsoft. addons](#microsoftaddons)
> - [Microsoft. ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft. Advisor](#microsoftadvisor)
> - [Microsoft. AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft. AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft. AppConfiguration](#microsoftappconfiguration)
> - [Microsoft. AppPlatform](#microsoftappplatform)
> - [Microsoft. atestador](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft. Azconfig](#microsoftazconfig)
> - [Microsoft. Azure. Geneva](#microsoftazuregeneva)
> - [Microsoft. AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft. AzureData](#microsoftazuredata)
> - [Microsoft. AzureStack](#microsoftazurestack)
> - [Microsoft. batch](#microsoftbatch)
> - [Microsoft. billing](#microsoftbilling)
> - [Microsoft. BingMaps](#microsoftbingmaps)
> - [Microsoft. Blockchain](#microsoftblockchain)
> - [Microsoft. Blueprint](#microsoftblueprint)
> - [Microsoft. BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft. Capacity](#microsoftcapacity)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft. CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft. ClassicCompute](#microsoftclassiccompute)
> - [Microsoft. ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft. ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft. ClassicStorage](#microsoftclassicstorage)
> - [Microsoft. Cognitivaservices](#microsoftcognitiveservices)
> - [Microsoft. Commerce](#microsoftcommerce)
> - [Microsoft. Compute](#microsoftcompute)
> - [Microsoft. consumo](#microsoftconsumption)
> - [Microsoft. ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft. CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft. CostManagement](#microsoftcostmanagement)
> - [Microsoft. CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft. CustomProviders](#microsoftcustomproviders)
> - [Microsoft. Data Box](#microsoftdatabox)
> - [Microsoft. DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft. databricks](#microsoftdatabricks)
> - [Microsoft. datacatalog](#microsoftdatacatalog)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft. datamigration](#microsoftdatamigration)
> - [Microsoft. DataShare](#microsoftdatashare)
> - [Microsoft. DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft. DBforMySQL](#microsoftdbformysql)
> - [Microsoft. DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft. DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft. DesktopVirtualization](#microsoftdesktopvirtualization)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft. DevOps](#microsoftdevops)
> - [Microsoft. DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft. DomainRegistration](#microsoftdomainregistration)
> - [Microsoft. DynamicsLcs](#microsoftdynamicslcs)
> - [Microsoft. EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft. EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft. Features](#microsoftfeatures)
> - [Microsoft. Gallery](#microsoftgallery)
> - [Microsoft. genomas](#microsoftgenomics)
> - [Microsoft. GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft. HanaOnAzure](#microsofthanaonazure)
> - [Microsoft. HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft. HealthcareApis](#microsofthealthcareapis)
> - [Microsoft. HybridCompute](#microsofthybridcompute)
> - [Microsoft. HybridData](#microsofthybriddata)
> - [Microsoft. Hydra](#microsofthydra)
> - [Microsoft. ImportExport](#microsoftimportexport)
> - [Microsoft. Intune](#microsoftintune)
> - [Microsoft. IoTCentral](#microsoftiotcentral)
> - [Microsoft. IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft. Kusto](#microsoftkusto)
> - [Microsoft. LabServices](#microsoftlabservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft. MachineLearning](#microsoftmachinelearning)
> - [Microsoft. MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft. ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft. Managedservices](#microsoftmanagedservices)
> - [Microsoft. Management](#microsoftmanagement)
> - [Microsoft. Maps](#microsoftmaps)
> - [Microsoft. Marketplace](#microsoftmarketplace)
> - [Microsoft. MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft. MarketplaceOrdering](#microsoftmarketplaceordering)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft. Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft. migrar](#microsoftmigrate)
> - [Microsoft. MixedReality](#microsoftmixedreality)
> - [Microsoft. NetApp](#microsoftnetapp)
> - [Microsoft. Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft. ObjectStore](#microsoftobjectstore)
> - [Microsoft. OffAzure](#microsoftoffazure)
> - [Microsoft. OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft. OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft. emparelhamento](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft. Portal](#microsoftportal)
> - [Microsoft. PowerBI](#microsoftpowerbi)
> - [Microsoft. PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft. Relay](#microsoftrelay)
> - [Microsoft. RemoteApp](#microsoftremoteapp)
> - [Microsoft. ResourceGraph](#microsoftresourcegraph)
> - [Microsoft. ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft. SaaS](#microsoftsaas)
> - [Microsoft.Scheduler](#microsoftscheduler)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft. SecurityGraph](#microsoftsecuritygraph)
> - [Microsoft. SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft. ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft. Services](#microsoftservices)
> - [Microsoft. SignalRService](#microsoftsignalrservice)
> - [Microsoft. SiteRecovery](#microsoftsiterecovery)
> - [Microsoft. SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft. Solutions](#microsoftsolutions)
> - [Microsoft. SQL](#microsoftsql)
> - [Microsoft. SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft. StorageCache](#microsoftstoragecache)
> - [Microsoft. StorageReplication](#microsoftstoragereplication)
> - [Microsoft. StorageSync](#microsoftstoragesync)
> - [Microsoft. StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft. StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft. StorSimple](#microsoftstorsimple)
> - [Microsoft. StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft. Subscription](#microsoftsubscription)
> - [Microsoft. TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft. VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft. WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [Microsoft. WindowsIoT](#microsoftwindowsiot)
> - [Microsoft. WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | DomainServices | SIM | SIM |
> | DomainServices/oucontainer | Não | Não |
> | DomainServices/ReplicaSets | SIM | SIM |

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
> | actionRules | SIM | SIM |
> | alertas | Não | Não |
> | alertsList | Não | Não |
> | alertsMetaData | Não | Não |
> | alertsSummary | Não | Não |
> | alertsSummaryList | Não | Não |
> | Receber | Não | Não |
> | smartDetectorAlertRules | SIM | SIM |
> | smartDetectorRuntimeEnvironments | Não | Não |
> | smartGroups | Não | Não |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | servidores | SIM | SIM |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | reportFeedback | Não | Não |
> | serviço | SIM | SIM |
> | validateServiceName | Não | Não |

## <a name="microsoftappconfiguration"></a>Microsoft. AppConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | configurationStores | SIM | SIM |
> | configurationStores/eventGridFilters | Não | Não |

## <a name="microsoftappplatform"></a>Microsoft. AppPlatform

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | Spring | SIM | SIM |

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
> | automationaccounts | SIM | SIM |
> | automationAccounts/configurations | SIM | SIM |
> | automationAccounts/runbooks | Não | Não |
> | automationAccounts/runbooks | SIM | SIM |
> | automationAccounts/configurations | Não | Não |
> | automationAccounts/runbooks | Não | Não |

## <a name="microsoftazconfig"></a>Microsoft. Azconfig

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | configurationStores | SIM | SIM |
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
> | b2cDirectories | SIM | Não |
> | b2ctenants | Não | Não |

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | hybridDataManagers | SIM | SIM |
> | postgresInstances | SIM | SIM |
> | sqlBigDataClusters | SIM | SIM |
> | SQLInstances | SIM | SIM |
> | sqlServerRegistrations | SIM | SIM |
> | sqlServerRegistrations/sqlservers | Não | Não |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | registrations | SIM | SIM |
> | registrations/customerSubscriptions | Não | Não |
> | registrations/products | Não | Não |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | batchAccounts | SIM | SIM |

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
> | mapApis | SIM | SIM |
> | updateCommunicationPreference | Não | Não |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | blockchainMembers | SIM | SIM |
> | cordaMembers | SIM | SIM |
> | espectadores | SIM | SIM |

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
> | botServices | SIM | SIM |
> | botServices/channels | Não | Não |
> | botServices/connections | Não | Não |
> | idiomas | Não | Não |
> | modelos | Não | Não |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | Redis | SIM | SIM |
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
> | exchange | Não | Não |
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
> | CdnWebApplicationFirewallPolicies | SIM | SIM |
> | edgenodes | Não | Não |
> | perfis | SIM | SIM |
> | profiles/endpoints | SIM | SIM |
> | profiles/endpoints/origins | Não | Não |
> | profiles/endpoints/origins | Não | Não |
> | validateProbe | Não | Não |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | certificateOrders | SIM | SIM |
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
> | discos | Não | Não |
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
> | accounts | SIM | SIM |

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
> | availabilitySets | SIM | SIM |
> | diskEncryptionSets | SIM | SIM |
> | discos | SIM | SIM |
> | galleries | SIM | SIM |
> | galerias/aplicativos | Não | Não |
> | galerias/aplicativos/versões | Não | Não |
> | galleries/images | Não | Não |
> | galleries/images/versions | Não | Não |
> | Hosts | SIM | SIM |
> | Hosts/hosts | SIM | SIM |
> | images | SIM | SIM |
> | proximityPlacementGroups | SIM | SIM |
> | restorePointCollections | SIM | SIM |
> | restorePointCollections/restorePoints | Não | Não |
> | sharedVMExtensions | SIM | SIM |
> | sharedVMExtensions/versões | Não | Não |
> | sharedVMImages | SIM | SIM |
> | sharedVMImages/versions | Não | Não |
> | snapshots | SIM | SIM |
> | virtualMachines | SIM | SIM |
> | virtualMachines/extensions | SIM | SIM |
> | virtualMachines/metricDefinitions | Não | Não |
> | virtualMachineScaleSets | SIM | SIM |
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
> | containerGroups | SIM | SIM |
> | serviceAssociationLinks | Não | Não |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | registries | SIM | SIM |
> | registries/builds | Não | Não |
> | registries/builds/cancel | Não | Não |
> | registries/builds/getLogLink | Não | Não |
> | registries/buildTasks | SIM | SIM |
> | registries/buildTasks/steps | Não | Não |
> | registries/eventGridFilters | Não | Não |
> | registros/generateCredentials | Não | Não |
> | registries/getBuildSourceUploadUrl | Não | Não |
> | registries/GetCredentials | Não | Não |
> | registries/importImage | Não | Não |
> | registries/queueBuild | Não | Não |
> | registries/regenerateCredential | Não | Não |
> | registries/regenerateCredentials | Não | Não |
> | registries/replications | SIM | SIM |
> | registries/runs | Não | Não |
> | registries/runs/cancel | Não | Não |
> | registries/scheduleRun | Não | Não |
> | registros/scopeMaps | Não | Não |
> | registries/tasks | SIM | SIM |
> | registros/tokens | Não | Não |
> | registries/updatePolicies | Não | Não |
> | registries/webhooks | SIM | SIM |
> | registries/webhooks/getCallbackConfig | Não | Não |
> | registries/webhooks/ping | Não | Não |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | containerServices | SIM | SIM |
> | managedClusters | SIM | SIM |
> | openShiftManagedClusters | SIM | SIM |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | accounts | SIM | SIM |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | Alertas | Não | Não |
> | BillingAccounts | Não | Não |
> | Orçamentos | Não | Não |
> | CloudConnectors | Não | Não |
> | Conectores | SIM | SIM |
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
> | Consulta | Não | Não |
> | register | Não | Não |
> | Reportconfigs | Não | Não |
> | Relatórios | Não | Não |
> | Configurações | Não | Não |
> | showbackRules | Não | Não |
> | Exibições | Não | Não |

## <a name="microsoftcustomerlockbox"></a>Microsoft. CustomerLockbox

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | solicitações | Não | Não |

## <a name="microsoftcustomproviders"></a>Microsoft. CustomProviders

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | SAS | Não | Não |
> | resourceProviders | SIM | SIM |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | jobs | SIM | SIM |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | DataBoxEdgeDevices | SIM | SIM |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | workspaces | SIM | Não |
> | workspaces/virtualNetworkPeerings | Não | Não |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | catalogs | SIM | SIM |
> | catálogos de | SIM | SIM |
> | datacatalogs/fontes de fonte | Não | Não |
> | datacatalogs/fontes de pesquisa/verificações | Não | Não |
> | datacatalogs/fontes/exames/conjuntos de valores | Não | Não |
> | datacatalogs/fontes/exames/gatilhos | Não | Não |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | dataFactories | SIM | Não |
> | dataFactories/diagnosticSettings | Não | Não |
> | dataFactories/metricDefinitions | Não | Não |
> | dataFactorySchema | Não | Não |
> | factories | SIM | Não |
> | factories/integrationRuntimes | Não | Não |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | accounts | SIM | SIM |
> | accounts/dataLakeStoreAccounts | Não | Não |
> | accounts/storageAccounts | Não | Não |
> | accounts/storageAccounts/containers | Não | Não |
> | contas/transferAnalyticsUnits | Não | Não |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | accounts | SIM | SIM |
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
> | accounts | SIM | SIM |
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
> | servidores | SIM | SIM |
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
> | servidores | SIM | SIM |
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
> | serverGroups | SIM | SIM |
> | servidores | SIM | SIM |
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
> | serversv2 | SIM | SIM |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | artifactSources | SIM | SIM |
> | rollouts | SIM | SIM |
> | pertopologias | SIM | SIM |
> | serviços e pertopologias | SIM | SIM |
> | pertopologias/serviços/unidades de serviço | SIM | SIM |
> | etapas | SIM | SIM |

## <a name="microsoftdesktopvirtualization"></a>Microsoft. DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | applicationgroups | SIM | SIM |
> | applicationgroups/aplicativos | Não | Não |
> | applicationgroups/desktops | Não | Não |
> | applicationgroups/startmenuitems | Não | Não |
> | hostpools | SIM | SIM |
> | hostpools/sessionhosts | Não | Não |
> | hostpools/sessionhosts/usersessions | Não | Não |
> | hostpools/usersessions | Não | Não |
> | workspaces | SIM | SIM |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | ElasticPools | SIM | SIM |
> | ElasticPools/IotHubTenants | SIM | SIM |
> | IotHubs | SIM | SIM |
> | IotHubs/eventGridFilters | Não | Não |
> | ProvisioningServices | SIM | SIM |
> | usages | Não | Não |

## <a name="microsoftdevops"></a>Microsoft. DevOps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | Pipelines | SIM | SIM |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | controladores | SIM | SIM |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | labcenters | SIM | SIM |
> | labs | SIM | SIM |
> | laboratórios/ambientes | SIM | SIM |
> | labs/serviceRunners | SIM | SIM |
> | labs/virtualMachines | SIM | SIM |
> | schedules | SIM | SIM |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | databaseAccounts | Não | Não |
> | databaseAccounts | SIM | SIM |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | domains | SIM | SIM |
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

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft. EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | services | SIM | SIM |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | domains | SIM | SIM |
> | domains/topics | Não | Não |
> | eventSubscriptions | Não | Não |
> | extensionTopics | Não | Não |
> | topics | SIM | SIM |
> | topicTypes | Não | Não |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | clusters | SIM | SIM |
> | namespaces | SIM | SIM |
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
> | elástico | Não | Não |
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
> | accounts | SIM | SIM |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | autoManagedVmConfigurationProfiles | SIM | SIM |
> | configurationProfileAssignments | Não | Não |
> | guestConfigurationAssignments | Não | Não |
> | software | Não | Não |
> | softwareUpdateProfile | Não | Não |
> | softwareUpdates | Não | Não |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | hanaInstances | SIM | SIM |
> | sapMonitors | SIM | SIM |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | dedicatedHSMs | SIM | SIM |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | clusters | SIM | SIM |
> | clusters/applications | Não | Não |

## <a name="microsofthealthcareapis"></a>Microsoft. HealthcareApis

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | services | SIM | SIM |

## <a name="microsofthybridcompute"></a>Microsoft. HybridCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | maquina | SIM | SIM |
> | máquinas/extensões | SIM | SIM |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | Gerenciadores de DataManager | SIM | SIM |

## <a name="microsofthydra"></a>Microsoft. Hydra

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | components | SIM | SIM |
> | networkScopes | SIM | SIM |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | jobs | SIM | SIM |

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
> | IoTApps | SIM | SIM |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | Graph | SIM | SIM |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | deletedVaults | Não | Não |
> | hsmPools | SIM | SIM |
> | vaults | SIM | SIM |
> | vaults/accessPolicies | Não | Não |
> | cofres/eventGridFilters | Não | Não |
> | vaults/secrets | Não | Não |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | clusters | SIM | SIM |
> | clusters/attacheddatabaseconfigurations | Não | Não |
> | clusters/databases | Não | Não |
> | clusters/databases/dataconnections | Não | Não |
> | clusters/databases/eventhubconnections | Não | Não |
> | clusters/sharedidentities | Não | Não |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | labaccounts | SIM | SIM |
> | users | Não | Não |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | hostingEnvironments | SIM | SIM |
> | integrationAccounts | SIM | SIM |
> | integrationServiceEnvironments | SIM | SIM |
> | integrationServiceEnvironments/managedApis | SIM | SIM |
> | isolatedEnvironments | SIM | SIM |
> | workflows | SIM | SIM |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | commitmentPlans | SIM | SIM |
> | webServices | SIM | SIM |
> | Workspaces | SIM | SIM |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | workspaces | SIM | SIM |
> | workspaces/computes | Não | Não |
> | espaços de trabalho/eventGridFilters | Não | Não |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | Identidades | Não | Não |
> | userAssignedIdentities | SIM | SIM |

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
> | accounts | SIM | SIM |
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
> | classicDevServices | SIM | SIM |
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
> | mediaservices | SIM | SIM |
> | mediaservices/accountFilters | Não | Não |
> | mediaservices/assets | Não | Não |
> | mediaservices/assets/assetFilters | Não | Não |
> | mediaservices/contentKeyPolicies | Não | Não |
> | mediaservices/eventGridFilters | Não | Não |
> | mediaservices/liveEvents | Não | Não |
> | mediaservices/liveEvents | SIM | SIM |
> | mediaservices/liveEvents/liveOutputs | Não | Não |
> | mediaservices/liveEvents | Não | Não |
> | mediaservices/mediaGraphs | Não | Não |
> | mediaservices/streamingEndpoints | Não | Não |
> | mediaservices/streamingEndpoints | SIM | SIM |
> | mediaservices/streamingLocators | Não | Não |
> | mediaservices/streamingLocators | Não | Não |
> | mediaservices/transforms | Não | Não |
> | mediaservices/transforms | Não | Não |

## <a name="microsoftmicroservices4spring"></a>Microsoft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | appClusters | SIM | SIM |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | assessmentProjects | SIM | SIM |
> | migrateprojects | SIM | SIM |
> | projects | SIM | SIM |

## <a name="microsoftmixedreality"></a>Microsoft. MixedReality

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | holographicsBroadcastAccounts | SIM | SIM |
> | objectUnderstandingAccounts | SIM | SIM |
> | remoteRenderingAccounts | SIM | SIM |
> | spatialAnchorsAccounts | SIM | SIM |
> | surfaceReconstructionAccounts | SIM | SIM |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | netAppAccounts | SIM | SIM |
> | netAppAccounts/backupPolicies | SIM | SIM |
> | netAppAccounts/capacityPools | SIM | SIM |
> | netAppAccounts/capacityPools/volumes | SIM | SIM |
> | netAppAccounts/capacityPools/volumes/backups | Não | Não |
> | netAppAccounts/capacityPools/volumes/mountTargets | SIM | SIM |
> | netAppAccounts/capacityPools/volumes/instantâneos | SIM | SIM |
> | netAppAccounts/cofres | Não | Não |
## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | applicationGateways | SIM | SIM |
> | applicationGatewayWebApplicationFirewallPolicies | SIM | SIM |
> | applicationSecurityGroups | SIM | SIM |
> | azureFirewallFqdnTags | Não | Não |
> | azureFirewalls | SIM | Não |
> | bastionHosts | SIM | SIM |
> | bgpServiceCommunities | Não | Não |
> | connections | SIM | SIM |
> | ddosCustomPolicies | SIM | SIM |
> | ddosProtectionPlans | SIM | SIM |
> | dnsOperationStatuses | Não | Não |
> | dnszones | SIM | SIM |
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
> | expressRouteCircuits | SIM | SIM |
> | expressRouteCrossConnections | SIM | SIM |
> | expressRouteGateways | SIM | SIM |
> | expressRoutePorts | SIM | SIM |
> | expressRouteServiceProviders | Não | Não |
> | firewallPolicies | SIM | SIM |
> | frontdoors | Sim, mas limitado (veja a [Observação abaixo](#frontdoor)) | SIM |
> | frontdoorWebApplicationFirewallManagedRuleSets | Sim, mas limitado (veja a [Observação abaixo](#frontdoor)) | Não |
> | frontdoorWebApplicationFirewallPolicies | Sim, mas limitado (veja a [Observação abaixo](#frontdoor)) | SIM |
> | getDnsResourceReference | Não | Não |
> | internalNotify | Não | Não |
> | loadBalancers | SIM | Não |
> | virtualNetworkGateways | SIM | SIM |
> | natGateways | SIM | SIM |
> | networkIntentPolicies | SIM | SIM |
> | networkInterfaces | SIM | SIM |
> | networkProfiles | SIM | SIM |
> | networkSecurityGroups | SIM | SIM |
> | networkWatchers | SIM | Não |
> | networkWatchers/connectionMonitors | SIM | Não |
> | networkWatchers/lenses | SIM | Não |
> | networkWatchers/lenses | SIM | Não |
> | p2sVpnGateways | SIM | SIM |
> | privateDnsOperationStatuses | Não | Não |
> | privateDnsZones | SIM | SIM |
> | privateDnsZones/A | Não | Não |
> | privateDnsZones/AAAA | Não | Não |
> | privateDnsZones/tudo | Não | Não |
> | privateDnsZones/CNAME | Não | Não |
> | privateDnsZones/MX | Não | Não |
> | privateDnsZones/PTR | Não | Não |
> | privateDnsZones/SOA | Não | Não |
> | privateDnsZones/SRV | Não | Não |
> | privateDnsZones/TXT | Não | Não |
> | privateDnsZones/virtualNetworkLinks | SIM | SIM |
> | privateEndpoints | SIM | SIM |
> | privateLinkServices | SIM | SIM |
> | publicIPAddresses | SIM | SIM |
> | publicIPPrefixes | SIM | SIM |
> | routeFilters | SIM | SIM |
> | routeTables | SIM | SIM |
> | serviceEndpointPolicies | SIM | SIM |
> | trafficManagerGeographicHierarchies | Não | Não |
> | trafficmanagerprofiles | SIM | SIM |
> | trafficmanagerprofiles/heatMaps | Não | Não |
> | trafficManagerUserMetricsKeys | Não | Não |
> | virtualWans | SIM | SIM |
> | virtualNetworkGateways | SIM | SIM |
> | virtualNetworks | SIM | SIM |
> | virtualNetworkTaps | SIM | SIM |
> | virtualWans | SIM | SIM |
> | vpnGateways | SIM | Não |
> | vpnSites | SIM | SIM |
> | frontdoorWebApplicationFirewallPolicies | SIM | SIM |

<a id="frontdoor" />

> [!NOTE]
> Para o serviço de porta frontal do Azure, você pode aplicar marcas ao criar o recurso, mas a atualização ou adição de marcas não tem suporte no momento.


## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | namespaces | SIM | Não |
> | namespaces/notificationHubs | SIM | Não |

## <a name="microsoftobjectstore"></a>Microsoft. ObjectStore

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | osNamespaces | SIM | SIM |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | HyperVSites | SIM | SIM |
> | ImportSites | SIM | SIM |
> | ServerSites | SIM | SIM |
> | VMwareSites | SIM | SIM |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | clusters | SIM | SIM |
> | dispositivos | Não | Não |
> | linkTargets | Não | Não |
> | storageInsightConfigs | Não | Não |
> | workspaces | SIM | SIM |
> | workspaces/dataSources | Não | Não |
> | workspaces/linkedServices | Não | Não |
> | workspaces/query | Não | Não |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | managementassociations | Não | Não |
> | managementconfigurations | SIM | SIM |
> | solutions | SIM | SIM |
> | Modos de exibição | SIM | SIM |

## <a name="microsoftpeering"></a>Microsoft. emparelhamento

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | legacyPeerings | Não | Não |
> | peerAsns | Não | Não |
> | emparelhamentos | SIM | SIM |
> | peeringServiceProviders | Não | Não |
> | peeringServices | SIM | SIM |

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
> | dashboards | SIM | SIM |
> | userSettings | Não | Não |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | workspaceCollections | SIM | SIM |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | capacities | SIM | SIM |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | backupProtectedItems | Não | Não |
> | vaults | SIM | SIM |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | namespaces | SIM | SIM |
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
> | Colec | SIM | SIM |
> | coleções/aplicativos | Não | Não |
> | coleções/SecurityPrincipals | Não | Não |
> | templateImages | Não | Não |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | consultas | SIM | SIM |
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
> | deployments | SIM | Não |
> | deployments/operations | Não | Não |
> | deploymentScripts | SIM | SIM |
> | deploymentScripts/logs | Não | Não |
> | links | Não | Não |
> | notifyResourceJobs | Não | Não |
> | providers | Não | Não |
> | resourceGroups | SIM | Não |
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
> | aplicativos | SIM | SIM |
> | saasresources | Não | Não |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | jobcollections | SIM | SIM |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | resourceHealthMetadata | Não | Não |
> | searchServices | SIM | SIM |

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
> | automações | SIM | SIM |
> | AutoProvisioningSettings | Não | Não |
> | Compliances | Não | Não |
> | dataCollectionAgents | Não | Não |
> | deviceSecurityGroups | Não | Não |
> | discoveredSecuritySolutions | Não | Não |
> | externalSecuritySolutions | Não | Não |
> | InformationProtectionPolicies | Não | Não |
> | iotSecuritySolutions | SIM | SIM |
> | iotSecuritySolutions/analyticsModels | Não | Não |
> | iotSecuritySolutions/analyticsModels/aggregatedAlerts | Não | Não |
> | iotSecuritySolutions/analyticsModels/aggregatedRecommendations | Não | Não |
> | jitNetworkAccessPolicies | Não | Não |
> | networkData | Não | Não |
> | playbookConfigurations | SIM | SIM |
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
> | configurações | Não | Não |
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
> | configurações | Não | Não |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | namespaces | SIM | Não |
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
> | aplicativos | SIM | SIM |
> | clusters | SIM | SIM |
> | clusters/applications | Não | Não |
> | containerGroups | SIM | SIM |
> | containerGroupSets | SIM | SIM |
> | edgeclusters | SIM | SIM |
> | edgeclusters/aplicativos | Não | Não |
> | networks | SIM | SIM |
> | secretstores | SIM | SIM |
> | secretstores/certificados | Não | Não |
> | secretstores/segredos | Não | Não |
> | volumes | SIM | SIM |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | aplicativos | SIM | SIM |
> | containerGroups | SIM | SIM |
> | gateways | SIM | SIM |
> | networks | SIM | SIM |
> | segredos | SIM | SIM |
> | volumes | SIM | SIM |

## <a name="microsoftservices"></a>Microsoft. Services

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | providerRegistrations | Não | Não |
> | providerRegistrations/resourceTypeRegistrations | Não | Não |
> | rollouts | SIM | SIM |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | SignalR | SIM | SIM |
> | Sinalização/eventGridFilters | Não | Não |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | SiteRecoveryVault | SIM | SIM |

## <a name="microsoftsoftwareplan"></a>Microsoft. SoftwarePlan

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | hybridUseBenefits | Não | Não |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | applicationDefinitions | SIM | SIM |
> | aplicativos | SIM | SIM |
> | jitRequests | SIM | SIM |

## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | managedInstances | SIM | SIM |
> | managedInstances/databases | Sim (veja a [Observação abaixo](#sqlnote)) | SIM |
> | managedInstances/databases/backupShortTermRetentionPolicies | Não | Não |
> | managedInstances/databases/schemas/tables/columns/sensitivityLabels | Não | Não |
> | managedInstances/databases/vulnerabilityAssessments | Não | Não |
> | managedInstances/databases/vulnerabilityAssessments/rules/baselines | Não | Não |
> | managedInstances/encryptionProtector | Não | Não |
> | managedInstances/keys | Não | Não |
> | managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | Não | Não |
> | managedInstances/vulnerabilityAssessments | Não | Não |
> | servidores | SIM | SIM |
> | servers/administrators | Não | Não |
> | servers/communicationLinks | Não | Não |
> | servers/databases | Sim (veja a [Observação abaixo](#sqlnote)) | SIM |
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
> | SqlVirtualMachineGroups | SIM | SIM |
> | SqlVirtualMachineGroups/AvailabilityGroupListeners | Não | Não |
> | SqlVirtualMachines | SIM | SIM |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | storageAccounts | SIM | SIM |
> | storageAccounts/blobServices | Não | Não |
> | storageAccounts/blobServices | Não | Não |
> | storageAccounts/blobServices | Não | Não |
> | storageAccounts/services | Não | Não |
> | storageAccounts/services/metricDefinitions | Não | Não |
> | storageAccounts/blobServices | Não | Não |
> | usages | Não | Não |

## <a name="microsoftstoragecache"></a>Microsoft. StorageCache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | caches | SIM | SIM |
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
> | storageSyncServices | SIM | SIM |
> | storageSyncServices/registeredServers | Não | Não |
> | storageSyncServices/syncGroups | Não | Não |
> | storageSyncServices/syncGroups/cloudEndpoints | Não | Não |
> | storageSyncServices/syncGroups/cloudEndpoints | Não | Não |
> | storageSyncServices/workflows | Não | Não |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | SIM | SIM |
> | storageSyncServices/registeredServers | Não | Não |
> | storageSyncServices/syncGroups | Não | Não |
> | storageSyncServices/syncGroups/cloudEndpoints | Não | Não |
> | storageSyncServices/syncGroups/cloudEndpoints | Não | Não |
> | storageSyncServices/workflows | Não | Não |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | SIM | SIM |
> | storageSyncServices/registeredServers | Não | Não |
> | storageSyncServices/syncGroups | Não | Não |
> | storageSyncServices/syncGroups/cloudEndpoints | Não | Não |
> | storageSyncServices/syncGroups/cloudEndpoints | Não | Não |
> | storageSyncServices/workflows | Não | Não |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | managers | SIM | SIM |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | streamingjobs | Sim (veja a observação a seguir) | SIM |

> [!NOTE]
> Você não pode adicionar uma marca quando streamingjobs estiver em execução. Pare o recurso para adicionar uma marca.

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | Cancelar | Não | Não |
> | CreateSubscription | Não | Não |
> | enable | Não | Não |
> | Nome | Não | Não |
> | SubscriptionDefinitions | Não | Não |
> | SubscriptionDefinitions | Não | Não |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | environments | SIM | Não |
> | environments/accessPolicies | Não | Não |
> | environments/eventsources | SIM | Não |
> | environments/referenceDataSets | SIM | Não |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft. VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | dedicatedCloudNodes | SIM | SIM |
> | dedicatedCloudServices | SIM | SIM |
> | virtualMachines | SIM | SIM |

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
> | certificates | SIM | SIM |
> | connectionGateways | SIM | SIM |
> | connections | SIM | SIM |
> | customApis | SIM | SIM |
> | deletedSites | Não | Não |
> | functions | Não | Não |
> | hostingEnvironments | SIM | SIM |
> | hostingEnvironments/multiRolePools | Não | Não |
> | hostingEnvironments/multiRolePools | Não | Não |
> | publishingUsers | Não | Não |
> | filmes | Não | Não |
> | resourceHealthMetadata | Não | Não |
> | runtimes | Não | Não |
> | serverFarms | SIM | SIM |
> | serverFarms/eventGridFilters | Não | Não |
> | sites | SIM | SIM |
> | sites/configuração  | Não | Não |
> | sites/eventGridFilters | Não | Não |
> | sites/hostNameBindings | Não | Não |
> | sites/networkConfig | Não | Não |
> | sites/premieraddons | SIM | SIM |
> | sites/slots | SIM | SIM |
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
> | DeviceServices | SIM | SIM |

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

## <a name="next-steps"></a>Próximos passos

Para saber como aplicar tags a recursos, consulte [Use tags para organizar seus recursos do Azure](resource-group-using-tags.md).
