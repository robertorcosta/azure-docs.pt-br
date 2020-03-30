---
title: Exclusão completa de modo
description: Mostra como os tipos de recurso lidam com a exclusão de modo completo em modelos do Azure Resource Manager.
ms.topic: conceptual
ms.date: 02/26/2020
ms.openlocfilehash: 5f797974212636460306c6a17869d6b8380545ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77664399"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>Exclusão de recursos do Azure para implantações do modo completo

Este artigo descreve como os tipos de recurso lidam com a exclusão quando ela não ocorre em um modelo implantado no modo completo.

Os tipos de recursos marcados com **Sim** são excluídos quando o tipo não está no modelo implantado com modo completo.

Os tipos de recursos marcados com **Não** não são excluídos automaticamente quando não estão no modelo; no entanto, eles são excluídos se o recurso pai for excluído. Para obter uma descrição completa do comportamento, consulte [Modos de implantação do Azure Resource Manager](deployment-modes.md).

Se você implantar [em mais de um grupo de recursos em um modelo,](cross-resource-group-deployment.md)os recursos no grupo de recursos especificados na operação de implantação serão elegíveis para serem excluídos. Os recursos nos grupos de recursos secundários não são excluídos.

Pule para um namespace do provedor de recursos:
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
> - [Microsoft.DesktopVirtualização](#microsoftdesktopvirtualization)
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
> - [Microsoft.Notebooks](#microsoftnotebooks)
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
> - [Microsoft.ProjectBabylon](#microsoftprojectbabylon)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.RemoteApp](#microsoftremoteapp)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
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
> - [Microsoft.SpoolService](#microsoftspoolservice)
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
> - [Microsoft.VnfManager](#microsoftvnfmanager)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | DomainServices | Sim |
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
> | actionRules | Sim |
> | alertas | Não |
> | alertsList | Não |
> | alertasMetaData | Não |
> | alertsSummary | Não |
> | alertsSummaryList | Não |
> | comentários | Não |
> | smartDetectorAlertRules | Sim |
> | smartDetectorRuntimeEnvironments | Não |
> | smartGroups | Não |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | servers | Sim |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | reportFeedback | Não |
> | serviço | Sim |
> | validateServiceName | Não |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | configuraçãoLojas | Sim |
> | configuraçãoLojas / eventGridFilters | Não |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | Spring | Sim |

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
> | atribuições de funçãoUseMetrics | Não |
> | roleDefinitions | Não |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | automationaccounts | Sim |
> | automaçãoContas/configurações | Sim |
> | automaçãoContas / empregos | Não |
> | automaçãoContas / privateEndpointConnectionProxies | Não |
> | automaçãoContas / privateEndpointConnections | Não |
> | automaçãoContas / privateLinkResources | Não |
> | automaçãoContas / runbooks | Sim |
> | automaçãoContas / softwareAtualizaçãoconfiguraçõesde configurações | Não |
> | automaçãoContas / webhooks | Não |

## <a name="microsoftazconfig"></a>Microsoft.Azconfig

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | configuraçãoLojas | Sim |
> | configuraçãoLojas / eventGridFilters | Não |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | environments | Não |
> | ambientes / contas | Não |
> | ambientes / contas / namespaces | Não |
> | ambientes / contas / namespaces / configurações | Não |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | b2cDirectories | Sim |
> | b2ctenants | Não |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | híbridoDataManagers | Sim |
> | postgresInstâncias | Sim |
> | sqlBigDataClusters | Sim |
> | sqlInstances | Sim |
> | Sqlserverregistrations | Sim |
> | sqlServerInscrições / sqlServers | Não |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | registrations | Sim |
> | registros / assinaturas de clientes | Não |
> | registros /produtos | Não |
> | verificaçãoChaves | Não |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | batchAccounts | Sim |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | billingAccounts | Não |
> | faturamentoContas/contratos | Não |
> | contas de faturamento / billingPermissões | Não |
> | faturamentoContas/faturamentoPerfis | Não |
> | billingContas / faturamentoPerfis / billingPermissões | Não |
> | faturamentoContas / faturamentoPerfis / faturamentoRoleAssignments | Não |
> | faturamentoContas / faturamentoPerfis / faturamentoRoleDefinições | Não |
> | faturamentoContas / faturamentoPerfis / faturamentoAssinaturas | Não |
> | faturamentoContas / faturamentoPerfis / criarBillingRoleAssignment | Não |
> | faturamentoContas / faturamentoPerfis /clientes | Não |
> | faturamentoContas / faturamentoPerfis / instruções | Não |
> | faturamentoContas/faturamentoPerfis/faturas | Não |
> | faturamentoContas / faturamentoPerfis / faturas / tabela de preços | Não |
> | faturamentoContas / faturamentoPerfis/ faturas / transações | Não |
> | faturamentoContas / faturamentoPerfis /faturaSeções | Não |
> | billingContas / faturamentoPerfis / faturaSeções / billingPermissões | Não |
> | faturamentoContas / faturamentoPerfis / faturaSeções / faturamentoRoleAssignments | Não |
> | faturamentoContas / faturamentoPerfis / faturaSeções / faturamentoRoleDefinições | Não |
> | faturamentoContas / faturamentoPerfis / faturaSeções / faturamentoAssinaturas | Não |
> | faturamentoContas / faturamentoPerfis / faturaSeções / createBillingRoleAssignment | Não |
> | billingContas / faturamentoPerfis / faturaSeções / iniciarTransfer | Não |
> | faturamentoContas / faturamentoPerfis / faturaSeções / produtos | Não |
> | faturamentoContas / faturamentoPerfis / faturaSeções / produtos / transferência | Não |
> | faturamentoContas / faturamentoPerfis / faturaSes / produtos / atualizaçãoAutoRenew | Não |
> | faturamentoContas / faturamentoPerfis / faturaSeções / transações | Não |
> | faturamentoContas / faturamentoPerfis / faturaSeções / transferências | Não |
> | billingContas / FaturamentoPerfis / patchOperações | Não |
> | faturamentoContas / faturamentoPerfis /pagamentosMétodos | Não |
> | faturamentoContas / faturamentoPerfis/políticas | Não |
> | faturamentoContas / faturamentoPerfis / tabela de preços | Não |
> | faturamentoContas / faturamentoPerfis / tabela de preçosDownloadOperações | Não |
> | faturamentoContas / faturamentoPerfis/produtos | Não |
> | faturamentoContas/ faturamentoPerfis/transações | Não |
> | faturamentoContas / cobrançaFunçãos | Não |
> | faturamentoContas / cobrançaRoleDefinições | Não |
> | faturamentoContas/faturamentoAssinaturas | Não |
> | faturamentoContas/ faturamentoAssinaturas/faturas | Não |
> | faturamentoContas / createBillingRoleAssignment | Não |
> | faturamentoContas / criarFaturaSeseOperações | Não |
> | faturamentoContas/clientes | Não |
> | faturamentoContas / clientes / billingPermissões | Não |
> | faturamentoContas / clientes / faturamentoAssinaturas | Não |
> | faturamentoContas / clientes / iniciarTransfer | Não |
> | faturamentoContas / clientes / políticas | Não |
> | faturamentoContas / clientes / produtos | Não |
> | faturamentoContas / clientes / transações | Não |
> | faturamentoContas / clientes / transferências | Não |
> | faturamentoContas/departamentos | Não |
> | contas de faturamento / matrículaContas | Não |
> | faturamentoContas/faturas | Não |
> | contas de faturamento / faturaSeções | Não |
> | billingContas / faturaSeções / faturamentoSubscriptionMoveOperations | Não |
> | faturamentoContas / faturaSeções / faturamentoAssinaturas | Não |
> | faturamentoContas / faturaSeções / faturamentoAssinaturas / transferência | Não |
> | contas de faturamento / faturaSeções / elevar | Não |
> | billingContas / faturaSeções / iniciarTransfer | Não |
> | billingContas / faturaSeções / patchOperations | Não |
> | billingContas / faturaSeções / produtoMovimentaOperações | Não |
> | faturamentoContas / faturaSeções / produtos | Não |
> | faturamentoContas / faturaSeções / produtos / transferência | Não |
> | faturamentoContas / faturaSeções / produtos / atualizaçãoAutoRenew | Não |
> | contas de faturamento / faturaSeções / transações | Não |
> | contas de faturamento / faturaSeções / transferências | Não |
> | contas de faturamento / linhaOfCredit | Não |
> | contas de faturamento / operações de correção | Não |
> | faturamentoContas / pagamentosMétodos | Não |
> | faturamentoContas/produtos | Não |
> | faturamentoContas/transações | Não |
> | billingPeriods | Não |
> | billingPermissões | Não |
> | billingProperty | Não |
> | cobrançaRoleAtribuições | Não |
> | faturamentoRoleDefinições | Não |
> | criarAtribuição de billing | Não |
> | departments | Não |
> | enrollmentAccounts | Não |
> | invoices | Não |
> | transfers | Não |
> | transferências / aceitatransfer | Não |
> | transferências / declínioTransfer | Não |
> | transferências / operaçãoStatus | Não |
> | transferências / validarTransfer | Não |
> | validarEndereço | Não |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | mapApis | Sim |
> | updateCommunicationPreference | Não |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | membros do blockchain | Sim |
> | membros corda | Sim |
> | Observadores | Sim |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | blueprintAssignments | Não |
> | projetoAtribuições / atribuiçãoOperações | Não |
> | projetoAtribuições/operações | Não |
> | blueprints | Não |
> | plantas / artefatos | Não |
> | plantas / versões | Não |
> | plantas / versões / artefatos | Não |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | botServices | Sim |
> | botServices / canais | Não |
> | botServices / conexões | Não |
> | idiomas | Não |
> | modelos | Não |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | Redis | Sim |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | appliedReservations | Não |
> | aumento automático de quotas | Não |
> | calculeExchange | Não |
> | calculatePrice | Não |
> | calcularBuyPrice | Não |
> | catalogs | Não |
> | commercialReservationOrders | Não |
> | câmbio | Não |
> | placePurchaseOrder | Não |
> | reservationOrders | Não |
> | reservasOrdens / cálculoReembolso | Não |
> | reservaOrders/mesclagem | Não |
> | reservasOrdens /reservas | Não |
> | reservasPedidos / reservas / revisões | Não |
> | reservasOrdens/devolução | Não |
> | reservasOrders / split | Não |
> | reservasOrdens/swap | Não |
> | reservations | Não |
> | provedores de recursos | Não |
> | recursos | Não |
> | validateReservationOrder | Não |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | CdnWebApplicationFirewallGerenciamentoderegras gerenciadas | Não |
> | CdnWebApplicationFirewallPolicies | Sim |
> | edgenodes | Não |
> | perfis | Sim |
> | perfis / pontos finais | Sim |
> | perfis / endpoints / domínios personalizados | Não |
> | perfis / endpoints / origens | Não |
> | validateProbe | Não |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | certificateOrders | Sim |
> | certificadoS/certificados | Não |
> | validateCertificateRegistrationInformation | Não |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | funcionalidades | Não |
> | domainNames | Sim |
> | domínionomes / recursos | Não |
> | domínioNomes / internalLoadBalancers | Não |
> | domínionomes / serviceCertificados | Não |
> | domínionomes/slots | Não |
> | domínioNomes / slots / funções | Não |
> | domínioNomes / slots / funções / métricaDefinições | Não |
> | domínioNomes / slots / funções / métricas | Não |
> | moveSubscriptionResources | Não |
> | operatingSystemFamilies | Não |
> | operatingSystems | Não |
> | quotas | Não |
> | resourceTypes | Não |
> | validateSubscriptionMoveAvailability | Não |
> | virtualMachines | Sim |
> | máquinas virtuais / diagnósticoConfigurações | Não |
> | máquinas virtuais / métricaDefinições | Não |
> | máquinas virtuais / métricas | Não |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | Migração de infraestrutura clássica | Não |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | funcionalidades | Não |
> | expressRouteCrossConnections | Não |
> | expressRouteCrossConexões / peerings | Não |
> | gatewaySupportedDevices | Não |
> | networkSecurityGroups | Sim |
> | quotas | Não |
> | reservedIps | Sim |
> | virtualNetworks | Sim |
> | redes virtuais / remotaRede virtualPeeringProxies | Não |
> | redes virtuais / virtualNetworkPeerings | Não |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | funcionalidades | Não |
> | disks | Não |
> | images | Não |
> | osImages | Não |
> | osPlatformImages | Não |
> | publicImages | Não |
> | quotas | Não |
> | storageAccounts | Sim |
> | armazenamentoContas / blobServices | Não |
> | armazenamentoContas / arquivosServiços | Não |
> | armazenamentoContas / métricaDefinições | Não |
> | armazenamentoContas/métricas | Não |
> | armazenamentoContas / filaServiços | Não |
> | armazenamentoContas/serviços | Não |
> | armazenamentoContas / serviços / diagnósticoConfigurações | Não |
> | armazenamentoContas/ serviços / métricaDefinições | Não |
> | armazenamentoContas/ serviços / métricas | Não |
> | armazenamentoContas / tabelaServiços | Não |
> | armazenamentoContas / vmImages | Não |
> | vmImages | Não |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | accounts | Sim |

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
> | availabilitySets | Sim |
> | discoConfiguraçãodescriptografia | Sim |
> | disks | Sim |
> | galleries | Sim |
> | galerias / aplicações | Não |
> | galerias / aplicações / versões | Não |
> | galerias / imagens | Não |
> | galerias / imagens / versões | Não |
> | hostGroups | Sim |
> | hostGroups / hosts | Sim |
> | images | Sim |
> | proximidadeGrupos de colocação | Sim |
> | restorePointCollections | Sim |
> | restaurarPointCollections / restauraçãoPontos | Não |
> | sharedVMImages | Sim |
> | compartilhadoVMImages / versões | Não |
> | snapshots | Sim |
> | virtualMachines | Sim |
> | Máquinas virtuais / extensões | Sim |
> | máquinas virtuais / métricaDefinições | Não |
> | virtualMachineScaleSets | Sim |
> | virtualMachineScaleSets / extensões | Não |
> | virtualMachineScaleSets / interfaces de rede | Não |
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
> | containerGroups | Sim |
> | serviceAssociationLinks | Não |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | registries | Sim |
> | registros / construções | Não |
> | registros / construções / cancelamento | Não |
> | registros / builds / getLogLink | Não |
> | registros / buildTasks | Sim |
> | registros / buildTasks / steps | Não |
> | registros / eventGridFilters | Não |
> | registros / gerarcredenciais | Não |
> | registros / getBuildSourceUploadUrl | Não |
> | registros / GetCredentials | Não |
> | registros / importaçãoImagem | Não |
> | registros / privateEndpointConnectionProxies | Não |
> | registros / privateEndpointConnectionProxies / validam | Não |
> | registros / privateEndpointConnections | Não |
> | registros / privateLinkResources | Não |
> | registros / filaBuild | Não |
> | registros / regeneração Credencial | Não |
> | registros / regeneraçõesCredenciais | Não |
> | registros / replicação | Sim |
> | registros / corridas | Não |
> | registros / corridas / cancelamento | Não |
> | registros / cronogramaRun | Não |
> | registros / escopoMapas | Não |
> | registros / tarefaS | Sim |
> | registros / tarefas | Sim |
> | registros / tokens | Não |
> | registros / atualizaçãoPolíticas | Não |
> | registros / webhooks | Sim |
> | registros / webhooks / getCallbackConfig | Não |
> | registros / webhooks / ping | Não |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | containerServices | Sim |
> | managedClusters | Sim |
> | openShiftClusters gerenciados | Sim |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | accounts | Sim |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | Alertas | Não |
> | BillingAccounts | Não |
> | Orçamentos | Não |
> | CloudConnectors | Não |
> | Conectores | Sim |
> | Departments | Não |
> | Dimensões | Não |
> | EnrollmentAccounts | Não |
> | Exportações | Não |
> | Contas de faturamento externas | Não |
> | Contas/alertas de faturamento externo | Não |
> | Contas/dimensões de faturamento externo | Não |
> | Contas /Previsão de Faturamento Externo | Não |
> | Contas/consulta de contas externas | Não |
> | Assinaturas externas | Não |
> | Assinaturas externas / alertas | Não |
> | Assinaturas externas / Dimensões | Não |
> | Assinaturas externas / Previsão | Não |
> | Assinaturas externas / Consulta | Não |
> | Previsão | Não |
> | Consulta | Não |
> | register | Não |
> | Reportconfigs | Não |
> | Relatórios | Não |
> | Configurações | Não |
> | showbackRegras | Não |
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
> | associações | Não |
> | provedores de recursos | Sim |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | jobs | Sim |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | DataBoxEdgeDevices | Sim |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | workspaces | Sim |
> | espaços de trabalho / dbEspaços de trabalho | Não |
> | espaços de trabalho / armazenamentoCriptografia | Não |
> | espaços de trabalho / virtualNetworkPeerings | Não |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | catalogs | Sim |
> | catálogos de dados | Sim |
> | catálogos de dados/ datasources | Não |
> | catálogos de dados / datasources / scans | Não |
> | catálogos de dados / datasources / scans / datasets | Não |
> | datacatalogs / datasources / scans / triggers | Não |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | dataFactories | Sim |
> | dataFábricas / diagnósticoConfigurações | Não |
> | dataFábricas / métricaDefinições | Não |
> | dataFactorySchema | Não |
> | factories | Sim |
> | fábricas / integraçãoRuntimes | Não |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | accounts | Sim |
> | contas / dadosLakeStoreContas | Não |
> | contas / armazenamentoContas | Não |
> | contas / armazenamentoContas / contêineres | Não |
> | contas / transferAnalyticsUnits | Não |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | accounts | Sim |
> | contas / eventGridFilters | Não |
> | contas / firewallRegras | Não |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | services | Sim |
> | serviços/projetos | Sim |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | accounts | Sim |
> | contas / ações | Não |
> | contas / compartilhamentos / conjuntos de dados | Não |
> | contas / ações / convites | Não |
> | contas / ações / assinaturas de compartilhamento de provedores | Não |
> | contas / compartilhamentos / sincronizaçãoConfigurações | Não |
> | contas / assinaturas de compartilhamento | Não |
> | contas / assinaturas de compartilhamento / consumidorSourceDataSets | Não |
> | contas / assinaturas de compartilhamento / mapeamentos de conjuntos de dados | Não |
> | contas / assinaturas de compartilhamento / gatilhos | Não |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | servers | Sim |
> | servidores/conselheiros | Não |
> | servidores /chaves | Não |
> | servidores / privateEndpointConnectionProxies | Não |
> | servidores / privateEndpointConnections | Não |
> | servidores / privateLinkResources | Não |
> | servidores / consultaTextos | Não |
> | servidores / servidores recuperáveisServidores | Não |
> | servidores / topQueryStatistics | Não |
> | servidores / virtualNetworkRules | Não |
> | servidores / esperaEstatísticas | Não |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | servers | Sim |
> | servidores/conselheiros | Não |
> | servidores /chaves | Não |
> | servidores / privateEndpointConnectionProxies | Não |
> | servidores / privateEndpointConnections | Não |
> | servidores / privateLinkResources | Não |
> | servidores / consultaTextos | Não |
> | servidores / servidores recuperáveisServidores | Não |
> | servidores / topQueryStatistics | Não |
> | servidores / virtualNetworkRules | Não |
> | servidores / esperaEstatísticas | Não |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | grupos de servidores | Sim |
> | servers | Sim |
> | servidores/conselheiros | Não |
> | servidores /chaves | Não |
> | servidores / privateEndpointConnectionProxies | Não |
> | servidores / privateEndpointConnections | Não |
> | servidores / privateLinkResources | Não |
> | servidores / consultaTextos | Não |
> | servidores / servidores recuperáveisServidores | Não |
> | servidores / topQueryStatistics | Não |
> | servidores / virtualNetworkRules | Não |
> | servidores / esperaEstatísticas | Não |
> | servidoresv2 | Sim |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | artefatoFontes | Sim |
> | rollouts | Sim |
> | serviçoTopologies | Sim |
> | serviçosTopologies/serviços | Sim |
> | serviçosTopologies / serviços / serviceUnits | Sim |
> | etapas | Sim |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualização

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | grupos de aplicativos | Sim |
> | grupos de aplicativos / aplicativos | Não |
> | grupos de aplicativos / desktops | Não |
> | grupos de aplicativos / startmenuitems | Não |
> | hostpools | Sim |
> | hostpools / hosts | Não |
> | hostpools / sessionhosts / usersessions | Não |
> | hostpools / sessões de usuário | Não |
> | workspaces | Sim |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | Elásticos | Sim |
> | Elásticos / IotHubTenants | Sim |
> | ElasticPools / IotHubTenants / securitySettings | Não |
> | IotHubs | Sim |
> | IotHubs / eventGridFilters | Não |
> | IotHubs / segurançaConfigurações | Não |
> | ProvisioningServices | Sim |
> | usages | Não |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | Pipelines | Sim |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | controladores | Sim |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | labcenters | Sim |
> | labs | Sim |
> | laboratórios / ambientes | Sim |
> | laboratórios / serviceRunners | Sim |
> | laboratórios / virtualMáquinas | Sim |
> | schedules | Sim |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | databaseAccounts | Não |
> | databaseAccounts | Sim |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | domains | Sim |
> | domínios / domínioSProprietáriosde propriedade | Não |
> | generateSsoRequest | Não |
> | topLevelDomains | Não |
> | validateDomainRegistrationInformation | Não |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | lcsprojects | Não |
> | projetos de lcs / implantações em nuvem | Não |
> | lcsprojects / conectores | Não |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | services | Sim |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | domains | Sim |
> | domínios / tópicos | Não |
> | eventSubscriptions | Não |
> | extensionTopics | Não |
> | partnerNamespaces | Sim |
> | partnerNamespaces / eventChannels | Não |
> | registros de parceiros | Sim |
> | partnerTopics | Sim |
> | partnerTopics / eventAssinaturas | Não |
> | systemTopics | Sim |
> | systemTopics / eventosAssinaturas | Não |
> | topics | Sim |
> | topicTypes | Não |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | clusters | Sim |
> | namespaces | Sim |
> | namespaces / regras de autorização | Não |
> | namespaces / disasterrecoveryconfigs | Não |
> | namespaces / eventhubs | Não |
> | namespaces / eventhubs / autorizações | Não |
> | namespaces / eventhubs / grupos de consumidores | Não |
> | namespaces / conjuntos de regras de rede | Não |

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
> | myareas / áreas | Não |
> | myareas / áreas / áreas | Não |
> | myareas / áreas / áreas / itens de galeria | Não |
> | myareas / áreas / itens de galeria | Não |
> | myareas / itens de galeria | Não |
> | register | Não |
> | recursos | Não |
> | retrieveresourcesbyid | Não |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | accounts | Sim |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | Contas gerenciadas automaticamente | Sim |
> | perfis de configuração gerenciados automaticamente | Sim |
> | configuraçãoAtribuições de perfil | Não |
> | guestConfigurationAssignments | Não |
> | software | Não |
> | softwareUpdateProfile | Não |
> | atualizações de software | Não |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | hanaInstances | Sim |
> | sapMonitors | Sim |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | HSMs dedicados | Sim |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | clusters | Sim |
> | clusters/aplicações | Não |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | services | Sim |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | Máquinas | Sim |
> | máquinas / extensões | Sim |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | gestores de dados | Sim |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | components | Sim |
> | escopos de rede | Sim |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | jobs | Sim |

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
> | aplicativosTemplates | Não |
> | IoTApps | Sim |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | Grafo | Sim |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | deletedVaults | Não |
> | hsmPools | Sim |
> | vaults | Sim |
> | cofres / accessPolicies | Não |
> | cofres / eventGridFiltros | Não |
> | cofres / segredos | Não |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | clusters | Sim |
> | clusters / configurações de banco de dados anexados | Não |
> | clusters /bancos de dados | Não |
> | clusters / bancos de dados / conexões de dados | Não |
> | clusters / bancos de dados / conexões eventhub | Não |
> | clusters / bancos de dados / principaisatribuições | Não |
> | clusters / principaisatribuições | Não |
> | clusters / identidades compartilhadas | Não |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | labaccounts | Sim |
> | users | Não |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | hostingEnvironments | Sim |
> | integrationAccounts | Sim |
> | integraçãoServiceEnvironments | Sim |
> | integraçãoServiceEnvironments/managedApis | Sim |
> | ambientes isolados | Sim |
> | workflows | Sim |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | commitmentPlans | Sim |
> | webServices | Sim |
> | Workspaces | Sim |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | workspaces | Sim |
> | espaços de trabalho / computação | Não |
> | espaços de trabalho / eventGridFilters | Não |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | Identidades | Não |
> | userAssignedIdentities | Sim |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | marketplaceRegistrationDefinições | Não |
> | inscriçõesAtribuições | Não |
> | registroDefinições | Não |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | getEntities | Não |
> | managementGroups | Não |
> | managementGroups /configurações | Não |
> | recursos | Não |
> | startTenantBackfill | Não |
> | tenantBackfillStatus | Não |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | accounts | Sim |
> | contas / eventGridFilters | Não |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | offers | Não |
> | offerTypes | Não |
> | offerTypes / editores | Não |
> | offerTipos / editores / ofertas | Não |
> | offerTipos / editores / ofertas / planos | Não |
> | offerTipos / editores / ofertas / planos / acordos | Não |
> | offerTipos / editores / ofertas / planos / configs | Não |
> | offerTipos / editores / ofertas / planos / configs / importaçãoImagem | Não |
> | privategalleryitems | Não |
> | privateStoreClient | Não |
> | products | Não |
> | publishers | Não |
> | editores / ofertas | Não |
> | editores / ofertas / alterações | Não |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | classicDevServices | Sim |
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
> | mediaservices | Sim |
> | mediaservices / contaFiltros | Não |
> | mediaservices / ativos | Não |
> | mediaservices / ativos / assetFiltros | Não |
> | mediaservices / contentKeyPolicies | Não |
> | mediaservices / eventGridFilters | Não |
> | mediaservices / liveEventOperations | Não |
> | mediaservices / liveEvents | Sim |
> | mediaservices / liveEvents / liveOutputs | Não |
> | mediaservices / liveOutputOperations | Não |
> | mediaservices / mediaGraphs | Não |
> | mediaservices / streamingEndpointOperations | Não |
> | mediaservices / streamingEndpoints | Sim |
> | mediaservices / streamingLocators | Não |
> | mediaservices / streamingPolicies | Não |
> | mediaservices / transformações | Não |
> | mediaservices / transformas / empregos | Não |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | appClusters | Sim |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | AvaliaçãoProjetos | Sim |
> | migrateprojects | Sim |
> | moveCollections | Sim |
> | projects | Sim |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | holographicsBroadcastContas | Sim |
> | objectCompreensãodecontas | Sim |
> | controle remotoRenderizandocontas | Sim |
> | espacialAnchorsContas | Sim |
> | superfícieReconstruçãoContas | Sim |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | netAppContas | Sim |
> | netAppContas / capacityPools | Sim |
> | netAppContas / capacitaçãoPools / volumes | Sim |
> | netAppContas / capacitaçãoPools / volumes / mountTargets | Sim |
> | netAppContas / capacityPools / volumes / snapshots | Sim |

## <a name="microsoftnotebooks"></a>Microsoft.Notebooks

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | NotebookProxies | Não |
## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | applicationGateways | Sim |
> | aplicativoGatewayWebAplicativoDe aplicativosFirewall | Sim |
> | applicationSecurityGroups | Sim |
> | azureFirewallFqdnTags | Não |
> | azureFirewalls | Sim |
> | bastiõesanfitriões | Sim |
> | bgpServiceCommunities | Não |
> | connections | Sim |
> | ddosCustomPolicies | Sim |
> | ddosProtectionPlans | Sim |
> | dnsOperationStatuses | Não |
> | dnszones | Sim |
> | dnszones / A | Não |
> | dnszones / AAAA | Não |
> | dnszones / todos | Não |
> | dnszones / CAA | Não |
> | dnszones / CNAME | Não |
> | dnszones / MX | Não |
> | dnszones / NS | Não |
> | dnszones / PTR | Não |
> | dnszones / conjuntos de registros | Não |
> | dnszones / SOA | Não |
> | dnszones / SRV | Não |
> | dnszones / TXT | Não |
> | expressRouteCircuits | Sim |
> | expressRouteCrossConnections | Sim |
> | expressRouteGateways | Sim |
> | expressRoutePorts | Sim |
> | expressRouteServiceProviders | Não |
> | firewallPolíticas | Sim |
> | frontdoors | Sim |
> | frontdoorWebAplicativoDeAplicativosGerenciamentoderegrasgerenciadasConjuntos de regras | Não |
> | frontdoorWebApplicationFirewallPolicies | Sim |
> | getDnsResourceReference | Não |
> | internalNotify | Não |
> | loadBalancers | Sim |
> | virtualNetworkGateways | Sim |
> | natGateways | Sim |
> | networkIntentPolicies | Sim |
> | networkInterfaces | Sim |
> | networkProfiles | Sim |
> | networkSecurityGroups | Sim |
> | networkWatchers | Sim |
> | observadores de rede / monitores de conexão | Sim |
> | networkWatchers / lentes | Sim |
> | networkWatchers / pingMeshes | Sim |
> | p2sVpnGateways | Sim |
> | privateDnsOperationStatuses | Não |
> | privateDnsZones | Sim |
> | privateDnsZones / A | Não |
> | privateDnsZones / AAAA | Não |
> | privateDnsZones / todos | Não |
> | privateDnsZones / CNAME | Não |
> | privateDnsZones / MX | Não |
> | privateDnsZones / PTR | Não |
> | privateDnsZones / SOA | Não |
> | privateDnsZones / SRV | Não |
> | privateDnsZones / TXT | Não |
> | privateDnsZones / virtualNetworkLinks | Sim |
> | privateEndpoints | Sim |
> | privateLinkServices | Sim |
> | publicIPAddresses | Sim |
> | publicIPPrefixes | Sim |
> | routeFilters | Sim |
> | routeTables | Sim |
> | serviceEndpointPolicies | Sim |
> | trafficManagerGeographicHierarchies | Não |
> | trafficmanagerprofiles | Sim |
> | perfis de gerenciadores de tráfego / heatMaps | Não |
> | tráfegoManagerUserMetricsKeys | Não |
> | virtualWans | Sim |
> | virtualNetworkGateways | Sim |
> | virtualNetworks | Sim |
> | virtualNetworkTaps | Sim |
> | virtualWans | Sim |
> | vpnGateways | Sim |
> | vpnSites | Sim |
> | frontdoorWebApplicationFirewallPolicies | Sim |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | namespaces | Sim |
> | namespaces / notificationHubs | Sim |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | osNamespaces | Sim |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | Hipervsites | Sim |
> | ImportaçãoSites | Sim |
> | ServidoresSites | Sim |
> | VMwareSites | Sim |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | clusters | Sim |
> | linkTargets | Não |
> | storageInsightConfigs | Não |
> | workspaces | Sim |
> | espaços de trabalho / dadosExporta | Não |
> | espaços de trabalho / dataSources | Não |
> | espaços de trabalho / linkedServices | Não |
> | espaços de trabalho / privateEndpointConnectionProxies | Não |
> | espaços de trabalho / privateEndpointConnections | Não |
> | espaços de trabalho / privateLinkResources | Não |
> | espaços de trabalho/ consulta | Não |
> | espaços de trabalho / escopoPrivateLinkProxies | Não |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | managementassociations | Não |
> | managementconfigurations | Sim |
> | solutions | Sim |
> | Modos de exibição | Sim |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | legadoPeerings | Não |
> | peerAsns | Não |
> | peerings | Sim |
> | peeringServiceCountries | Não |
> | peeringServiceProviders | Não |
> | peeringServices | Sim |

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
> | dashboards | Sim |
> | userSettings | Não |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | workspaceCollections | Sim |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | capacities | Sim |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | accounts | Sim |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | backupProtectedItems | Não |
> | vaults | Sim |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | namespaces | Sim |
> | namespaces / regras de autorização | Não |
> | namespaces / conexões híbridas | Não |
> | namespaces / conexões híbridas / regras de autorização | Não |
> | namespaces / wcfrelays | Não |
> | namespaces / wcfrelays / regras de autorização | Não |

## <a name="microsoftremoteapp"></a>Microsoft.RemoteApp

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | accounts | Não |
> | coleções | Sim |
> | coleções / aplicações | Não |
> | coleções / seguranças principais | Não |
> | modeloImagens | Não |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | consultas | Sim |
> | recursoChangeDetails | Não |
> | recursosAlterações | Não |
> | recursos | Não |
> | recursosHistóricohistórico | Não |
> | subscriptionsStatus | Não |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | availabilityStatuses | Não |
> | childAvailabilityStatuses | Não |
> | childResources | Não |
> | questões emergentes | Não |
> | events | Não |
> | impactedResources | Não |
> | metadata | Não |
> | Notificações | Não |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | deployments | Não |
> | implantações /operações | Não |
> | implantaçãoScripts | Sim |
> | implantaçãoScripts/ logs | Não |
> | links | Não |
> | notifyResourceJobs | Não |
> | providers | Não |
> | resourceGroups | Não |
> | subscriptions | Não |
> | tenants | Não |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | de dimensionamento da Web | Sim |
> | saasresources | Não |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | resourceHealthMetadata | Não |
> | searchServices | Sim |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | adaptiveNetworkHardenings | Não |
> | advancedThreatProtectionSettings | Não |
> | alertas | Não |
> | allowedConnections | Não |
> | applicationWhitelistings | Não |
> | avaliaçãoMetadados | Não |
> | avaliações | Não |
> | AutoDismissAlertsRules | Não |
> | Automações | Sim |
> | AutoProvisioningSettings | Não |
> | Compliances | Não |
> | dataCollectionAgents | Não |
> | deviceSecurityGroups | Não |
> | discoveredSecuritySolutions | Não |
> | externalSecuritySolutions | Não |
> | InformationProtectionPolicies | Não |
> | iotSecuritySolutions | Sim |
> | iotSecuritySolutions / analyticsModelos | Não |
> | iotSecuritySolutions / analyticsModels / agregadosAlertas | Não |
> | iotSecuritySolutions / analyticsModels / agregadosRecomendações | Não |
> | jitNetworkAccessPolicies | Não |
> | networkData | Não |
> | políticas | Não |
> | pricings | Não |
> | normas de conformidade regulamentar | Não |
> | normas de conformidade/ regulamentaçãoConformidadeControls | Não |
> | normas de conformidade/ regulamentaçãoConformidadeS/regulamentaçãoConformidades | Não |
> | securityContacts | Não |
> | securitySolutions | Não |
> | securitySolutionsReferenceData | Não |
> | securityStatuses | Não |
> | securityStatusesSummaries | Não |
> | avaliações de vulnerabilidade do servidor | Não |
> | configurações | Não |
> | subAvaliações | Não |
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
> | agregações | Não |
> | alertaRegras | Não |
> | alertRuleTemplates | Não |
> | indicadores | Não |
> | cases | Não |
> | dataConnectors | Não |
> | dataConectoresVerificandorequisitos | Não |
> | entidades | Não |
> | entityQueries | Não |
> | Incidentes | Não |
> | officeConsents | Não |
> | configurações | Não |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | namespaces | Sim |
> | namespaces / regras de autorização | Não |
> | namespaces / disasterrecoveryconfigs | Não |
> | namespaces / eventgridfilters | Não |
> | namespaces / conjuntos de regras de rede | Não |
> | namespaces / filas | Não |
> | namespaces / filas / regras de autorização | Não |
> | namespaces / tópicos | Não |
> | namespaces / tópicos / regras de autorização | Não |
> | namespaces / tópicos / assinaturas | Não |
> | namespaces / tópicos / assinaturas / regras | Não |
> | premiumMessagingRegions | Não |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | de dimensionamento da Web | Sim |
> | clusters | Sim |
> | clusters/aplicações | Não |
> | containerGroups | Sim |
> | contêinerGroupSets | Sim |
> | edgeclusters | Sim |
> | clusters de borda / aplicativos | Não |
> | managedclusters | Sim |
> | clusters/tipos de nó | Não |
> | networks | Sim |
> | secretstores | Sim |
> | lojas secretas / certificados | Não |
> | lojas secretas / segredos | Não |
> | volumes | Sim |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | de dimensionamento da Web | Sim |
> | containerGroups | Sim |
> | gateways | Sim |
> | networks | Sim |
> | segredos | Sim |
> | volumes | Sim |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | providerRegistrations | Não |
> | provedorInscrições/recursosTypeInscrições | Não |
> | rollouts | Sim |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | SignalR | Sim |
> | SignalR / eventGridFilters | Não |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | SiteRecoveryVault | Sim |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | híbridoUseBenefícios | Não |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | applicationDefinitions | Sim |
> | de dimensionamento da Web | Sim |
> | jitRequests | Sim |

## <a name="microsoftspoolservice"></a>Microsoft.SpoolService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | Assinaturas registradas | Não |
> | Carretéis | Sim |

## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | managedInstances | Sim |
> | gerenciadoInstâncias/bancos de dados | Sim |
> | gerenciadoInstâncias / bancos de dados / backupDecurtoprazoPolíticas de retenção | Não |
> | gerenciadoInstâncias / bancos de dados / schemas / tabelas / colunas / sensibilidadeRótulos | Não |
> | gerenciadoInstâncias/ bancos de dados / vulnerabilidadeAvaliações | Não |
> | gerenciadoInstâncias / bancos de dados / vulnerabilidadeAvaliações / regras / linhas de base | Não |
> | gerenciadoInstâncias / encryptionProtector | Não |
> | gerenciadoInstâncias / chaves | Não |
> | gerenciadoSInstâncias/ restaurávelDroppedDatabases / backupDecurtoprazoPolíticas de retenção | Não |
> | gerenciadoInstâncias /vulnerabilidadeAvaliações | Não |
> | servers | Sim |
> | servidores /administradores | Não |
> | servidores / links de comunicação | Não |
> | servidores /bancos de dados | Sim |
> | servidores / encryptionProtector | Não |
> | servidores / firewallRegras | Não |
> | servidores /chaves | Não |
> | servidores / restauráveisDroppedDatabases | Não |
> | servidores /objetivos de serviço | Não |
> | servidores / tdeCertificados | Não |
> | clusters virtuais | Não |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | SqlVirtualMachineGroups | Sim |
> | SqlVirtualMachineGroups / AvailabilityGroupListeners | Não |
> | SqlVirtualMachines | Sim |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | storageAccounts | Sim |
> | armazenamentoContas / blobServices | Não |
> | armazenamentoContas / arquivosServiços | Não |
> | armazenamentoContas / filaServiços | Não |
> | armazenamentoContas/serviços | Não |
> | armazenamentoContas/ serviços / métricaDefinições | Não |
> | armazenamentoContas / tabelaServiços | Não |
> | usages | Não |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | Caches | Sim |
> | caches/ armazenamentoAlvos | Não |
> | useModels | Não |

## <a name="microsoftstoragereplication"></a>Microsoft.StorageReplication

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | replicaçãoGrupos | Não |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | storageSyncServices | Sim |
> | armazenamentoSyncServices / servidores registrados | Não |
> | armazenamentoSyncServices / syncGroups | Não |
> | armazenamentoSyncServices / syncGroups / cloudEndpoints | Não |
> | armazenamentoSyncServices / syncGroups / serverEndpoints | Não |
> | armazenamentoSyncServices / fluxos de trabalho | Não |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | storageSyncServices | Sim |
> | armazenamentoSyncServices / servidores registrados | Não |
> | armazenamentoSyncServices / syncGroups | Não |
> | armazenamentoSyncServices / syncGroups / cloudEndpoints | Não |
> | armazenamentoSyncServices / syncGroups / serverEndpoints | Não |
> | armazenamentoSyncServices / fluxos de trabalho | Não |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | storageSyncServices | Sim |
> | armazenamentoSyncServices / servidores registrados | Não |
> | armazenamentoSyncServices / syncGroups | Não |
> | armazenamentoSyncServices / syncGroups / cloudEndpoints | Não |
> | armazenamentoSyncServices / syncGroups / serverEndpoints | Não |
> | armazenamentoSyncServices / fluxos de trabalho | Não |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | managers | Sim |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | streamingjobs | Sim |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | cancel | Não |
> | CreateSubscription | Não |
> | enable | Não |
> | renomear | Não |
> | SubscriptionDefinitions | Não |
> | SubscriptionDefinitions | Não |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | environments | Sim |
> | ambientes / políticas de acesso | Não |
> | ambientes / fontes de eventos | Sim |
> | ambientes / referênciaConjuntos de dados | Sim |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | dedicadoCloudNodes | Sim |
> | dedicadoCloudServices | Sim |
> | virtualMachines | Sim |

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | dispositivos | Sim |
> | Assinaturas registradas | Não |
> | Fornecedores | Não |
> | fornecedores / skus | Não |
> | fornecedores / vnfs | Não |
> | vnfs | Sim |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | apiManagementAccounts | Não |
> | apiManagementContas / apiAcls | Não |
> | apiManagementContas / apis | Não |
> | apiManagementContas / apis / apiAcls | Não |
> | apiManagementContas / apis / conexãoAcls | Não |
> | apiManagementContas / apis / conexões | Não |
> | apiManagementContas / apis / conexões / conexõesAcls | Não |
> | apiManagementContas / apis / localizadoDefinições | Não |
> | apiManagementContas / conexãoAcls | Não |
> | apiManagementContas / conexões | Não |
> | billingMeters | Não |
> | certificates | Sim |
> | connectionGateways | Sim |
> | connections | Sim |
> | customApis | Sim |
> | deletedSites | Não |
> | hostingEnvironments | Sim |
> | hostingAmbientes / eventGridFilters | Não |
> | hostingAmbientes / multiRolePools | Não |
> | hospedagemAmbientes / workerPools | Não |
> | publishingUsers | Não |
> | filmes | Não |
> | resourceHealthMetadata | Não |
> | runtimes | Não |
> | serverFarms | Sim |
> | serverFarms / eventGridFilters | Não |
> | sites | Sim |
> | sites/config  | Não |
> | sites / eventGridFilters | Não |
> | sites / hostNameVinculações | Não |
> | sites / redeConfig | Não |
> | sites / premieraddons | Sim |
> | sites / slots | Sim |
> | sites / slots / eventGridFilters | Não |
> | sites / slots / hostNameVinculções | Não |
> | sites / slots / redeConfig | Não |
> | sourceControls | Não |
> | staticSites | Sim |
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
> | DeviceServices | Sim |

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
