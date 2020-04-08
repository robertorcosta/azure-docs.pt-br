---
title: Suporte de marcação para recursos
description: Mostra quais tipos de recursos do Azure suportam tags. Fornece detalhes para todos os serviços do Azure.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: e012126995136bec15dc360be5e91007b6f69f09
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/07/2020
ms.locfileid: "80802485"
---
# <a name="tag-support-for-azure-resources"></a>Suporte a marcas para recursos do Azure
Este artigo descreve se um tipo de recurso dá suporte a [marcas](tag-resources.md). A coluna **'Suportes' indica** se o tipo de recurso tem uma propriedade para a tag. A coluna rotulada **Tag in cost report** indica se esse tipo de recurso passa a tag para o relatório de custos. Você pode visualizar os custos por tags na análise de [custos de Gerenciamento de Custos](../../cost-management-billing/costs/quick-acm-cost-analysis.md#understanding-grouping-and-filtering-options) e na fatura de faturamento do [Azure e dados de uso diário.](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md)

Para obter os mesmos dados como um arquivo de valores separados por vírgula, baixe [tag-support.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv).

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
> - [Microsoft.BlockchainTokens](#microsoftblockchaintokens)
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
> - [Microsoft.Falcon](#microsoftfalcon)
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
> - [Microsoft.Kubernetes](#microsoftkubernetes)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.Maintenance](#microsoftmaintenance)
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
> - [Microsoft.Notebooks](#microsoftnotebooks)
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
> - [Microsoft.Quantum](#microsoftquantum)
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
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | DomainServices | Sim | Sim |
> | DomainServices / oucontainer | Não | Não |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | supportProviders | Não | Não |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
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
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | configurações | Não | Não |
> | generateRecommendations | Não | Não |
> | metadata | Não | Não |
> | filmes | Não | Não |
> | suppressions | Não | Não |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | actionRules | Sim | Sim |
> | alertas | Não | Não |
> | alertsList | Não | Não |
> | alertasMetaData | Não | Não |
> | alertsSummary | Não | Não |
> | alertsSummaryList | Não | Não |
> | smartDetectorAlertRules | Sim | Sim |
> | smartGroups | Não | Não |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | servers | Sim | Sim |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | reportFeedback | Não | Não |
> | serviço | Sim | Sim |
> | validateServiceName | Não | Não |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | configuraçãoLojas | Sim | Sim |
> | configuraçãoLojas / eventGridFilters | Não | Não |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | Spring | Sim | Sim |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | attestationProviders | Não | Não |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | classicAdministrators | Não | Não |
> | dataAliases | Não | Não |
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
> | atribuições de funçãoUseMetrics | Não | Não |
> | roleDefinitions | Não | Não |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | automationaccounts | Sim | Sim |
> | automaçãoContas/configurações | Sim | Sim |
> | automaçãoContas / empregos | Não | Não |
> | automaçãoContas / privateEndpointConnectionProxies | Não | Não |
> | automaçãoContas / privateEndpointConnections | Não | Não |
> | automaçãoContas / privateLinkResources | Não | Não |
> | automaçãoContas / runbooks | Sim | Sim |
> | automaçãoContas / softwareAtualizaçãoconfiguraçõesde configurações | Não | Não |
> | automaçãoContas / webhooks | Não | Não |

## <a name="microsoftazconfig"></a>Microsoft.Azconfig

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | configuraçãoLojas | Sim | Sim |
> | configuraçãoLojas / eventGridFilters | Não | Não |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | environments | Não | Não |
> | ambientes / contas | Não | Não |
> | ambientes / contas / namespaces | Não | Não |
> | ambientes / contas / namespaces / configurações | Não | Não |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | b2cDirectories | Sim | Não |
> | b2ctenants | Não | Não |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | híbridoDataManagers | Sim | Sim |
> | postgresInstâncias | Sim | Sim |
> | sqlInstances | Sim | Sim |
> | Sqlserverregistrations | Sim | Sim |
> | sqlServerInscrições / sqlServers | Não | Não |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | cloudManifestFiles | Não | Não |
> | registrations | Sim | Sim |
> | registros / assinaturas de clientes | Não | Não |
> | registros /produtos | Não | Não |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | batchAccounts | Sim | Sim |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | billingAccounts | Não | Não |
> | faturamentoContas/contratos | Não | Não |
> | contas de faturamento / billingPermissões | Não | Não |
> | faturamentoContas/faturamentoPerfis | Não | Não |
> | billingContas / faturamentoPerfis / billingPermissões | Não | Não |
> | faturamentoContas / faturamentoPerfis / faturamentoRoleAssignments | Não | Não |
> | faturamentoContas / faturamentoPerfis / faturamentoRoleDefinições | Não | Não |
> | faturamentoContas / faturamentoPerfis / faturamentoAssinaturas | Não | Não |
> | faturamentoContas / faturamentoPerfis / criarBillingRoleAssignment | Não | Não |
> | faturamentoContas / faturamentoPerfis /clientes | Não | Não |
> | faturamentoContas / faturamentoPerfis / instruções | Não | Não |
> | faturamentoContas/faturamentoPerfis/faturas | Não | Não |
> | faturamentoContas / faturamentoPerfis / faturas / tabela de preços | Não | Não |
> | faturamentoContas / faturamentoPerfis/ faturas / transações | Não | Não |
> | faturamentoContas / faturamentoPerfis /faturaSeções | Não | Não |
> | billingContas / faturamentoPerfis / faturaSeções / billingPermissões | Não | Não |
> | faturamentoContas / faturamentoPerfis / faturaSeções / faturamentoRoleAssignments | Não | Não |
> | faturamentoContas / faturamentoPerfis / faturaSeções / faturamentoRoleDefinições | Não | Não |
> | faturamentoContas / faturamentoPerfis / faturaSeções / faturamentoAssinaturas | Não | Não |
> | faturamentoContas / faturamentoPerfis / faturaSeções / createBillingRoleAssignment | Não | Não |
> | billingContas / faturamentoPerfis / faturaSeções / iniciarTransfer | Não | Não |
> | faturamentoContas / faturamentoPerfis / faturaSeções / produtos | Não | Não |
> | faturamentoContas / faturamentoPerfis / faturaSeções / produtos / transferência | Não | Não |
> | faturamentoContas / faturamentoPerfis / faturaSes / produtos / atualizaçãoAutoRenew | Não | Não |
> | faturamentoContas / faturamentoPerfis / faturaSeções / transações | Não | Não |
> | faturamentoContas / faturamentoPerfis / faturaSeções / transferências | Não | Não |
> | billingContas / FaturamentoPerfis / patchOperações | Não | Não |
> | faturamentoContas / faturamentoPerfis /pagamentosMétodos | Não | Não |
> | faturamentoContas / faturamentoPerfis/políticas | Não | Não |
> | faturamentoContas / faturamentoPerfis / tabela de preços | Não | Não |
> | faturamentoContas / faturamentoPerfis / tabela de preçosDownloadOperações | Não | Não |
> | faturamentoContas / faturamentoPerfis/produtos | Não | Não |
> | faturamentoContas/ faturamentoPerfis/transações | Não | Não |
> | faturamentoContas / cobrançaFunçãos | Não | Não |
> | faturamentoContas / cobrançaRoleDefinições | Não | Não |
> | faturamentoContas/faturamentoAssinaturas | Não | Não |
> | faturamentoContas/ faturamentoAssinaturas/faturas | Não | Não |
> | faturamentoContas / createBillingRoleAssignment | Não | Não |
> | faturamentoContas / criarFaturaSeseOperações | Não | Não |
> | faturamentoContas/clientes | Não | Não |
> | faturamentoContas / clientes / billingPermissões | Não | Não |
> | faturamentoContas / clientes / faturamentoAssinaturas | Não | Não |
> | faturamentoContas / clientes / iniciarTransfer | Não | Não |
> | faturamentoContas / clientes / políticas | Não | Não |
> | faturamentoContas / clientes / produtos | Não | Não |
> | faturamentoContas / clientes / transações | Não | Não |
> | faturamentoContas / clientes / transferências | Não | Não |
> | faturamentoContas/departamentos | Não | Não |
> | contas de faturamento / matrículaContas | Não | Não |
> | faturamentoContas/faturas | Não | Não |
> | contas de faturamento / faturaSeções | Não | Não |
> | billingContas / faturaSeções / faturamentoSubscriptionMoveOperations | Não | Não |
> | faturamentoContas / faturaSeções / faturamentoAssinaturas | Não | Não |
> | faturamentoContas / faturaSeções / faturamentoAssinaturas / transferência | Não | Não |
> | contas de faturamento / faturaSeções / elevar | Não | Não |
> | billingContas / faturaSeções / iniciarTransfer | Não | Não |
> | billingContas / faturaSeções / patchOperations | Não | Não |
> | billingContas / faturaSeções / produtoMovimentaOperações | Não | Não |
> | faturamentoContas / faturaSeções / produtos | Não | Não |
> | faturamentoContas / faturaSeções / produtos / transferência | Não | Não |
> | faturamentoContas / faturaSeções / produtos / atualizaçãoAutoRenew | Não | Não |
> | contas de faturamento / faturaSeções / transações | Não | Não |
> | contas de faturamento / faturaSeções / transferências | Não | Não |
> | contas de faturamento / linhaOfCredit | Não | Não |
> | contas de faturamento / operações de correção | Não | Não |
> | faturamentoContas / pagamentosMétodos | Não | Não |
> | faturamentoContas/produtos | Não | Não |
> | faturamentoContas/transações | Não | Não |
> | billingPeriods | Não | Não |
> | billingPermissões | Não | Não |
> | billingProperty | Não | Não |
> | cobrançaRoleAtribuições | Não | Não |
> | faturamentoRoleDefinições | Não | Não |
> | criarAtribuição de billing | Não | Não |
> | departments | Não | Não |
> | enrollmentAccounts | Não | Não |
> | invoices | Não | Não |
> | transfers | Não | Não |
> | transferências / aceitatransfer | Não | Não |
> | transferências / declínioTransfer | Não | Não |
> | transferências / operaçãoStatus | Não | Não |
> | transferências / validarTransfer | Não | Não |
> | validarEndereço | Não | Não |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | mapApis | Sim | Sim |
> | updateCommunicationPreference | Não | Não |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | membros do blockchain | Sim | Sim |
> | membros corda | Sim | Sim |
> | Observadores | Sim | Sim |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | TokenServices | Sim | Sim |
> | TokenServices / BlockchainNetworks | Não | Não |
> | TokenServices / Grupos | Não | Não |
> | TokenServices / Grupos / Contas | Não | Não |
> | TokenServices / TokenTemplates | Não | Não |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | blueprintAssignments | Não | Não |
> | projetoAtribuições / atribuiçãoOperações | Não | Não |
> | projetoAtribuições/operações | Não | Não |
> | blueprints | Não | Não |
> | plantas / artefatos | Não | Não |
> | plantas / versões | Não | Não |
> | plantas / versões / artefatos | Não | Não |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | botServices | Sim | Sim |
> | botServices / canais | Não | Não |
> | botServices / conexões | Não | Não |
> | idiomas | Não | Não |
> | modelos | Não | Não |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | Redis | Sim | Sim |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | appliedReservations | Não | Não |
> | aumento automático de quotas | Não | Não |
> | calculeExchange | Não | Não |
> | calculatePrice | Não | Não |
> | calcularBuyPrice | Não | Não |
> | catalogs | Não | Não |
> | commercialReservationOrders | Não | Não |
> | câmbio | Não | Não |
> | placePurchaseOrder | Não | Não |
> | reservationOrders | Não | Não |
> | reservasOrdens / cálculoReembolso | Não | Não |
> | reservaOrders/mesclagem | Não | Não |
> | reservasOrdens /reservas | Não | Não |
> | reservasPedidos / reservas / revisões | Não | Não |
> | reservasOrdens/devolução | Não | Não |
> | reservasOrders / split | Não | Não |
> | reservasOrdens/swap | Não | Não |
> | reservations | Não | Não |
> | provedores de recursos | Não | Não |
> | recursos | Não | Não |
> | validateReservationOrder | Não | Não |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | CdnWebApplicationFirewallGerenciamentoderegras gerenciadas | Não | Não |
> | CdnWebApplicationFirewallPolicies | Sim | Sim |
> | edgenodes | Não | Não |
> | perfis | Sim | Sim |
> | perfis / pontos finais | Sim | Sim |
> | perfis / endpoints / domínios personalizados | Não | Não |
> | perfis / endpoints / grupos de origem | Não | Não |
> | perfis / endpoints / origens | Não | Não |
> | validateProbe | Não | Não |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | certificateOrders | Sim | Sim |
> | certificadoS/certificados | Não | Não |
> | validateCertificateRegistrationInformation | Não | Não |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | funcionalidades | Não | Não |
> | domainNames | Não | Não |
> | domínionomes / recursos | Não | Não |
> | domínioNomes / internalLoadBalancers | Não | Não |
> | domínionomes / serviceCertificados | Não | Não |
> | domínionomes/slots | Não | Não |
> | domínioNomes / slots / funções | Não | Não |
> | domínioNomes / slots / funções / métricaDefinições | Não | Não |
> | domínioNomes / slots / funções / métricas | Não | Não |
> | moveSubscriptionResources | Não | Não |
> | operatingSystemFamilies | Não | Não |
> | operatingSystems | Não | Não |
> | quotas | Não | Não |
> | resourceTypes | Não | Não |
> | validateSubscriptionMoveAvailability | Não | Não |
> | virtualMachines | Não | Não |
> | máquinas virtuais / diagnósticoConfigurações | Não | Não |
> | máquinas virtuais / métricaDefinições | Não | Não |
> | máquinas virtuais / métricas | Não | Não |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | Migração de infraestrutura clássica | Não | Não |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | funcionalidades | Não | Não |
> | expressRouteCrossConnections | Não | Não |
> | expressRouteCrossConexões / peerings | Não | Não |
> | gatewaySupportedDevices | Não | Não |
> | networkSecurityGroups | Não | Não |
> | quotas | Não | Não |
> | reservedIps | Não | Não |
> | virtualNetworks | Não | Não |
> | redes virtuais / remotaRede virtualPeeringProxies | Não | Não |
> | redes virtuais / virtualNetworkPeerings | Não | Não |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | funcionalidades | Não | Não |
> | disks | Não | Não |
> | images | Não | Não |
> | osImages | Não | Não |
> | osPlatformImages | Não | Não |
> | publicImages | Não | Não |
> | quotas | Não | Não |
> | storageAccounts | Não | Não |
> | armazenamentoContas / blobServices | Não | Não |
> | armazenamentoContas / arquivosServiços | Não | Não |
> | armazenamentoContas / métricaDefinições | Não | Não |
> | armazenamentoContas/métricas | Não | Não |
> | armazenamentoContas / filaServiços | Não | Não |
> | armazenamentoContas/serviços | Não | Não |
> | armazenamentoContas / serviços / diagnósticoConfigurações | Não | Não |
> | armazenamentoContas/ serviços / métricaDefinições | Não | Não |
> | armazenamentoContas/ serviços / métricas | Não | Não |
> | armazenamentoContas / tabelaServiços | Não | Não |
> | armazenamentoContas / vmImages | Não | Não |
> | vmImages | Não | Não |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | accounts | Sim | Sim |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | RateCard | Não | Não |
> | UsageAggregates | Não | Não |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | availabilitySets | Sim | Sim |
> | discoConfiguraçãodescriptografia | Sim | Sim |
> | disks | Sim | Sim |
> | galleries | Sim | Sim |
> | galerias / aplicações | Não | Não |
> | galerias / aplicações / versões | Não | Não |
> | galerias / imagens | Não | Não |
> | galerias / imagens / versões | Não | Não |
> | hostGroups | Sim | Sim |
> | hostGroups / hosts | Sim | Sim |
> | images | Sim | Sim |
> | proximidadeGrupos de colocação | Sim | Sim |
> | restorePointCollections | Sim | Sim |
> | restaurarPointCollections / restauraçãoPontos | Não | Não |
> | extensões compartilhadasVM | Sim | Sim |
> | compartilhadoVMExtensões/ versões | Não | Não |
> | sharedVMImages | Sim | Sim |
> | compartilhadoVMImages / versões | Não | Não |
> | snapshots | Sim | Sim |
> | sshPublicKeys | Sim | Sim |
> | virtualMachines | Sim | Sim |
> | Máquinas virtuais / extensões | Sim | Sim |
> | máquinas virtuais / métricaDefinições | Não | Não |
> | virtualMachineScaleSets | Sim | Sim |
> | virtualMachineScaleSets / extensões | Não | Não |
> | virtualMachineScaleSets / interfaces de rede | Não | Não |
> | virtualMachineScaleSets / publicIPAddresses | Não | Não |
> | virtualMachineScaleSets / virtualMachines | Não | Não |
> | virtualMachineScaleSets / virtualMachines / networkInterfaces | Não | Não |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
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
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | containerGroups | Sim | Sim |
> | serviceAssociationLinks | Não | Não |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | registries | Sim | Sim |
> | registros / agentPools | Sim | Sim |
> | registros / construções | Não | Não |
> | registros / construções / cancelamento | Não | Não |
> | registros / builds / getLogLink | Não | Não |
> | registros / buildTasks | Sim | Sim |
> | registros / buildTasks / steps | Não | Não |
> | registros / eventGridFilters | Não | Não |
> | registros / gerarcredenciais | Não | Não |
> | registros / getBuildSourceUploadUrl | Não | Não |
> | registros / GetCredentials | Não | Não |
> | registros / importaçãoImagem | Não | Não |
> | registros / privateEndpointConnectionProxies | Não | Não |
> | registros / privateEndpointConnectionProxies / validam | Não | Não |
> | registros / privateEndpointConnections | Não | Não |
> | registros / privateLinkResources | Não | Não |
> | registros / filaBuild | Não | Não |
> | registros / regeneração Credencial | Não | Não |
> | registros / regeneraçõesCredenciais | Não | Não |
> | registros / replicação | Sim | Sim |
> | registros / corridas | Não | Não |
> | registros / corridas / cancelamento | Não | Não |
> | registros / cronogramaRun | Não | Não |
> | registros / escopoMapas | Não | Não |
> | registros / tarefaS | Sim | Sim |
> | registros / tarefas | Sim | Sim |
> | registros / tokens | Não | Não |
> | registros / atualizaçãoPolíticas | Não | Não |
> | registros / webhooks | Sim | Sim |
> | registros / webhooks / getCallbackConfig | Não | Não |
> | registros / webhooks / ping | Não | Não |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | containerServices | Sim | Sim |
> | managedClusters | Sim | Sim |
> | openShiftClusters gerenciados | Sim | Sim |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | Alertas | Não | Não |
> | BillingAccounts | Não | Não |
> | Orçamentos | Não | Não |
> | CloudConnectors | Não | Não |
> | Conectores | Sim | Sim |
> | Departments | Não | Não |
> | Dimensões | Não | Não |
> | EnrollmentAccounts | Não | Não |
> | Exportações | Não | Não |
> | Contas de faturamento externas | Não | Não |
> | Contas/alertas de faturamento externo | Não | Não |
> | Contas/dimensões de faturamento externo | Não | Não |
> | Contas /Previsão de Faturamento Externo | Não | Não |
> | Contas/consulta de contas externas | Não | Não |
> | Assinaturas externas | Não | Não |
> | Assinaturas externas / alertas | Não | Não |
> | Assinaturas externas / Dimensões | Não | Não |
> | Assinaturas externas / Previsão | Não | Não |
> | Assinaturas externas / Consulta | Não | Não |
> | Previsão | Não | Não |
> | Consulta | Não | Não |
> | register | Não | Não |
> | Reportconfigs | Não | Não |
> | Relatórios | Não | Não |
> | Configurações | Não | Não |
> | showbackRegras | Não | Não |
> | Exibições | Não | Não |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | solicitações | Não | Não |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | associações | Não | Não |
> | provedores de recursos | Sim | Sim |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | jobs | Sim | Sim |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | DataBoxEdgeDevices | Sim | Sim |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | workspaces | Sim | Não |
> | espaços de trabalho / dbEspaços de trabalho | Não | Não |
> | espaços de trabalho / armazenamentoCriptografia | Não | Não |
> | espaços de trabalho / virtualNetworkPeerings | Não | Não |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | catalogs | Sim | Sim |
> | catálogos de dados | Sim | Sim |
> | catálogos de dados/ datasources | Não | Não |
> | catálogos de dados / datasources / scans | Não | Não |
> | catálogos de dados / datasources / scans / datasets | Não | Não |
> | datacatalogs / datasources / scans / triggers | Não | Não |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | dataFactories | Sim | Não |
> | dataFábricas / diagnósticoConfigurações | Não | Não |
> | dataFábricas / métricaDefinições | Não | Não |
> | dataFactorySchema | Não | Não |
> | factories | Sim | Não |
> | fábricas / integraçãoRuntimes | Não | Não |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | accounts | Sim | Sim |
> | contas / dadosLakeStoreContas | Não | Não |
> | contas / armazenamentoContas | Não | Não |
> | contas / armazenamentoContas / contêineres | Não | Não |
> | contas / transferAnalyticsUnits | Não | Não |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | accounts | Sim | Sim |
> | contas / eventGridFilters | Não | Não |
> | contas / firewallRegras | Não | Não |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | services | Não | Não |
> | serviços/projetos | Não | Não |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | accounts | Sim | Sim |
> | contas / ações | Não | Não |
> | contas / compartilhamentos / conjuntos de dados | Não | Não |
> | contas / ações / convites | Não | Não |
> | contas / ações / assinaturas de compartilhamento de provedores | Não | Não |
> | contas / compartilhamentos / sincronizaçãoConfigurações | Não | Não |
> | contas / assinaturas de compartilhamento | Não | Não |
> | contas / assinaturas de compartilhamento / consumidorSourceDataSets | Não | Não |
> | contas / assinaturas de compartilhamento / mapeamentos de conjuntos de dados | Não | Não |
> | contas / assinaturas de compartilhamento / gatilhos | Não | Não |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | servers | Sim | Sim |
> | servidores/conselheiros | Não | Não |
> | servidores /chaves | Não | Não |
> | servidores / privateEndpointConnectionProxies | Não | Não |
> | servidores / privateEndpointConnections | Não | Não |
> | servidores / privateLinkResources | Não | Não |
> | servidores / consultaTextos | Não | Não |
> | servidores / servidores recuperáveisServidores | Não | Não |
> | servidores / topQueryStatistics | Não | Não |
> | servidores / virtualNetworkRules | Não | Não |
> | servidores / esperaEstatísticas | Não | Não |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | servers | Sim | Sim |
> | servidores/conselheiros | Não | Não |
> | servidores /chaves | Não | Não |
> | servidores / privateEndpointConnectionProxies | Não | Não |
> | servidores / privateEndpointConnections | Não | Não |
> | servidores / privateLinkResources | Não | Não |
> | servidores / consultaTextos | Não | Não |
> | servidores / servidores recuperáveisServidores | Não | Não |
> | servidores / topQueryStatistics | Não | Não |
> | servidores / virtualNetworkRules | Não | Não |
> | servidores / esperaEstatísticas | Não | Não |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | grupos de servidores | Sim | Sim |
> | servers | Sim | Sim |
> | servidores/conselheiros | Não | Não |
> | servidores /chaves | Não | Não |
> | servidores / privateEndpointConnectionProxies | Não | Não |
> | servidores / privateEndpointConnections | Não | Não |
> | servidores / privateLinkResources | Não | Não |
> | servidores / consultaTextos | Não | Não |
> | servidores / servidores recuperáveisServidores | Não | Não |
> | servidores / topQueryStatistics | Não | Não |
> | servidores / virtualNetworkRules | Não | Não |
> | servidores / esperaEstatísticas | Não | Não |
> | servidoresv2 | Sim | Sim |
> | servidores únicos | Sim | Sim |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | artefatoFontes | Sim | Sim |
> | rollouts | Sim | Sim |
> | serviçoTopologies | Sim | Sim |
> | serviçosTopologies/serviços | Sim | Sim |
> | serviçosTopologies / serviços / serviceUnits | Sim | Sim |
> | etapas | Sim | Sim |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualização

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | grupos de aplicativos | Sim | Sim |
> | grupos de aplicativos / aplicativos | Não | Não |
> | grupos de aplicativos / desktops | Não | Não |
> | grupos de aplicativos / startmenuitems | Não | Não |
> | hostpools | Sim | Sim |
> | hostpools / hosts | Não | Não |
> | hostpools / sessionhosts / usersessions | Não | Não |
> | hostpools / sessões de usuário | Não | Não |
> | workspaces | Sim | Sim |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | Elásticos | Sim | Sim |
> | Elásticos / IotHubTenants | Sim | Sim |
> | ElasticPools / IotHubTenants / securitySettings | Não | Não |
> | IotHubs | Sim | Sim |
> | IotHubs / eventGridFilters | Não | Não |
> | IotHubs / segurançaConfigurações | Não | Não |
> | ProvisioningServices | Sim | Sim |
> | usages | Não | Não |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | Pipelines | Sim | Sim |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | controladores | Sim | Sim |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | labcenters | Sim | Sim |
> | labs | Sim | Sim |
> | laboratórios / ambientes | Sim | Sim |
> | laboratórios / serviceRunners | Sim | Sim |
> | laboratórios / virtualMáquinas | Sim | Sim |
> | schedules | Sim | Sim |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | databaseAccounts | Não | Não |
> | databaseAccounts | Sim | Sim |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | domains | Sim | Sim |
> | domínios / domínioSProprietáriosde propriedade | Não | Não |
> | generateSsoRequest | Não | Não |
> | topLevelDomains | Não | Não |
> | validateDomainRegistrationInformation | Não | Não |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | lcsprojects | Não | Não |
> | projetos de lcs / implantações em nuvem | Não | Não |
> | lcsprojects / conectores | Não | Não |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | services | Sim | Sim |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | domains | Sim | Sim |
> | domínios / tópicos | Não | Não |
> | eventSubscriptions | Não | Não |
> | extensionTopics | Não | Não |
> | partnerNamespaces | Sim | Sim |
> | partnerNamespaces / eventChannels | Não | Não |
> | registros de parceiros | Sim | Sim |
> | partnerTopics | Sim | Sim |
> | partnerTopics / eventAssinaturas | Não | Não |
> | systemTopics | Sim | Sim |
> | systemTopics / eventosAssinaturas | Não | Não |
> | topics | Sim | Sim |
> | topicTypes | Não | Não |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | clusters | Sim | Sim |
> | namespaces | Sim | Sim |
> | namespaces / regras de autorização | Não | Não |
> | namespaces / disasterrecoveryconfigs | Não | Não |
> | namespaces / eventhubs | Não | Não |
> | namespaces / eventhubs / autorizações | Não | Não |
> | namespaces / eventhubs / grupos de consumidores | Não | Não |
> | namespaces / conjuntos de regras de rede | Não | Não |

## <a name="microsoftfalcon"></a>Microsoft.Falcon

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | namespaces | Sim | Sim |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | provedores de recursos | Não | Não |
> | recursos | Não | Não |
> | providers | Não | Não |
> | assinaturaRegistros de características | Não | Não |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | enroll | Não | Não |
> | galleryitems | Não | Não |
> | generateartifactaccessuri | Não | Não |
> | myareas | Não | Não |
> | myareas / áreas | Não | Não |
> | myareas / áreas / áreas | Não | Não |
> | myareas / áreas / áreas / itens de galeria | Não | Não |
> | myareas / áreas / itens de galeria | Não | Não |
> | myareas / itens de galeria | Não | Não |
> | register | Não | Não |
> | recursos | Não | Não |
> | retrieveresourcesbyid | Não | Não |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | accounts | Sim | Sim |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | Contas gerenciadas automaticamente | Sim | Sim |
> | perfis de configuração gerenciados automaticamente | Sim | Sim |
> | configuraçãoAtribuições de perfil | Não | Não |
> | guestConfigurationAssignments | Não | Não |
> | software | Não | Não |
> | softwareUpdateProfile | Não | Não |
> | atualizações de software | Não | Não |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | hanaInstances | Sim | Sim |
> | sapMonitors | Sim | Sim |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | HSMs dedicados | Sim | Sim |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | clusters | Sim | Sim |
> | clusters/aplicações | Não | Não |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | services | Sim | Sim |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | Máquinas | Sim | Sim |
> | máquinas / extensões | Sim | Sim |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | gestores de dados | Sim | Sim |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | components | Sim | Sim |
> | escopos de rede | Sim | Sim |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | jobs | Sim | Sim |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Não | Não |
> | diagnosticSettingsCategories | Não | Não |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | aplicativosTemplates | Não | Não |
> | IoTApps | Sim | Sim |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | Grafo | Sim | Sim |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | deletedVaults | Não | Não |
> | hsmPools | Sim | Sim |
> | vaults | Sim | Sim |
> | cofres / accessPolicies | Não | Não |
> | cofres / eventGridFiltros | Não | Não |
> | cofres / segredos | Não | Não |

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | clusters conectados | Sim | Sim |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | clusters | Sim | Sim |
> | clusters / configurações de banco de dados anexados | Não | Não |
> | clusters /bancos de dados | Não | Não |
> | clusters / bancos de dados / conexões de dados | Não | Não |
> | clusters / bancos de dados / conexões eventhub | Não | Não |
> | clusters / bancos de dados / principaisatribuições | Não | Não |
> | clusters / conexões de dados | Não | Não |
> | clusters / principaisatribuições | Não | Não |
> | clusters / identidades compartilhadas | Não | Não |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | labaccounts | Sim | Sim |
> | users | Não | Não |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | hostingEnvironments | Sim | Sim |
> | integrationAccounts | Sim | Sim |
> | integraçãoServiceEnvironments | Sim | Sim |
> | integraçãoServiceEnvironments/managedApis | Sim | Sim |
> | ambientes isolados | Sim | Sim |
> | workflows | Sim | Sim |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | commitmentPlans | Sim | Sim |
> | webServices | Sim | Sim |
> | Workspaces | Sim | Sim |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | workspaces | Sim | Sim |
> | espaços de trabalho / computação | Não | Não |
> | espaços de trabalho / eventGridFilters | Não | Não |

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | aplicarAtualizações | Não | Não |
> | configuraçõesAtribuições | Não | Não |
> | configurações de manutenção | Sim | Sim |
> | atualizações | Não | Não |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | Identidades | Não | Não |
> | userAssignedIdentities | Sim | Sim |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | marketplaceRegistrationDefinições | Não | Não |
> | inscriçõesAtribuições | Não | Não |
> | registroDefinições | Não | Não |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | getEntities | Não | Não |
> | managementGroups | Não | Não |
> | managementGroups /configurações | Não | Não |
> | recursos | Não | Não |
> | startTenantBackfill | Não | Não |
> | tenantBackfillStatus | Não | Não |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | accounts | Sim | Sim |
> | contas / eventGridFilters | Não | Não |
> | contas / privateAtlases | Sim | Sim |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | offers | Não | Não |
> | offerTypes | Não | Não |
> | offerTypes / editores | Não | Não |
> | offerTipos / editores / ofertas | Não | Não |
> | offerTipos / editores / ofertas / planos | Não | Não |
> | offerTipos / editores / ofertas / planos / acordos | Não | Não |
> | offerTipos / editores / ofertas / planos / configs | Não | Não |
> | offerTipos / editores / ofertas / planos / configs / importaçãoImagem | Não | Não |
> | privategalleryitems | Não | Não |
> | privateStoreClient | Não | Não |
> | privateStores | Não | Não |
> | privateStores / ofertas | Não | Não |
> | products | Não | Não |
> | publishers | Não | Não |
> | editores / ofertas | Não | Não |
> | editores / ofertas / alterações | Não | Não |
> | register | Não | Não |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | classicDevServices | Sim | Sim |
> | updateCommunicationPreference | Não | Não |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | Contratos | Não | Não |
> | offertypes | Não | Não |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | mediaservices | Sim | Sim |
> | mediaservices / contaFiltros | Não | Não |
> | mediaservices / ativos | Não | Não |
> | mediaservices / ativos / assetFiltros | Não | Não |
> | mediaservices / contentKeyPolicies | Não | Não |
> | mediaservices / eventGridFilters | Não | Não |
> | mediaservices / liveEventOperations | Não | Não |
> | mediaservices / liveEvents | Sim | Sim |
> | mediaservices / liveEvents / liveOutputs | Não | Não |
> | mediaservices / liveOutputOperations | Não | Não |
> | mediaservices / mediaGraphs | Não | Não |
> | mediaservices / streamingEndpointOperations | Não | Não |
> | mediaservices / streamingEndpoints | Sim | Sim |
> | mediaservices / streamingLocators | Não | Não |
> | mediaservices / streamingPolicies | Não | Não |
> | mediaservices / transformações | Não | Não |
> | mediaservices / transformas / empregos | Não | Não |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | appClusters | Sim | Sim |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | AvaliaçãoProjetos | Sim | Sim |
> | migrateprojects | Sim | Sim |
> | moveCollections | Sim | Sim |
> | projects | Sim | Sim |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | holographicsBroadcastContas | Sim | Sim |
> | objectCompreensãodecontas | Sim | Sim |
> | controle remotoRenderizandocontas | Sim | Sim |
> | espacialAnchorsContas | Sim | Sim |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | netAppContas | Sim | Não |
> | netAppContas / contaBackups | Não | Não |
> | netAppContas / capacityPools | Sim | Não |
> | netAppContas / capacitaçãoPools / volumes | Sim | Não |
> | netAppContas / capacityPools / volumes / snapshots | Não | Não |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | applicationGateways | Sim | Sim |
> | aplicativoGatewayWebAplicativoDe aplicativosFirewall | Sim | Sim |
> | applicationSecurityGroups | Sim | Sim |
> | azureFirewallFqdnTags | Não | Não |
> | azureFirewalls | Sim | Não |
> | bastiõesanfitriões | Sim | Sim |
> | bgpServiceCommunities | Não | Não |
> | connections | Sim | Sim |
> | ddosCustomPolicies | Sim | Sim |
> | ddosProtectionPlans | Sim | Sim |
> | dnsOperationStatuses | Não | Não |
> | dnszones | Sim | Sim |
> | dnszones / A | Não | Não |
> | dnszones / AAAA | Não | Não |
> | dnszones / todos | Não | Não |
> | dnszones / CAA | Não | Não |
> | dnszones / CNAME | Não | Não |
> | dnszones / MX | Não | Não |
> | dnszones / NS | Não | Não |
> | dnszones / PTR | Não | Não |
> | dnszones / conjuntos de registros | Não | Não |
> | dnszones / SOA | Não | Não |
> | dnszones / SRV | Não | Não |
> | dnszones / TXT | Não | Não |
> | expressRouteCircuits | Sim | Sim |
> | expressRouteCrossConnections | Sim | Sim |
> | expressRouteGateways | Sim | Sim |
> | expressRoutePorts | Sim | Sim |
> | expressRouteServiceProviders | Não | Não |
> | firewallPolíticas | Sim | Sim |
> | frontdoors | Sim, mas limitado (veja [nota abaixo)](#frontdoor) | Sim |
> | frontdoorWebAplicativoDeAplicativosGerenciamentoderegrasgerenciadasConjuntos de regras | Sim, mas limitado (veja [nota abaixo)](#frontdoor) | Não |
> | frontdoorWebApplicationFirewallPolicies | Sim, mas limitado (veja [nota abaixo)](#frontdoor) | Sim |
> | getDnsResourceReference | Não | Não |
> | internalNotify | Não | Não |
> | loadBalancers | Sim | Não |
> | virtualNetworkGateways | Sim | Sim |
> | natGateways | Sim | Sim |
> | networkIntentPolicies | Sim | Sim |
> | networkInterfaces | Sim | Sim |
> | networkProfiles | Sim | Sim |
> | networkSecurityGroups | Sim | Sim |
> | networkWatchers | Sim | Não |
> | observadores de rede / monitores de conexão | Sim | Não |
> | networkWatchers / lentes | Sim | Não |
> | networkWatchers / pingMeshes | Sim | Não |
> | p2sVpnGateways | Sim | Sim |
> | privateDnsOperationStatuses | Não | Não |
> | privateDnsZones | Sim | Sim |
> | privateDnsZones / A | Não | Não |
> | privateDnsZones / AAAA | Não | Não |
> | privateDnsZones / todos | Não | Não |
> | privateDnsZones / CNAME | Não | Não |
> | privateDnsZones / MX | Não | Não |
> | privateDnsZones / PTR | Não | Não |
> | privateDnsZones / SOA | Não | Não |
> | privateDnsZones / SRV | Não | Não |
> | privateDnsZones / TXT | Não | Não |
> | privateDnsZones / virtualNetworkLinks | Sim | Sim |
> | privateEndpoints | Sim | Sim |
> | privateLinkServices | Sim | Sim |
> | publicIPAddresses | Sim | Sim |
> | publicIPPrefixes | Sim | Sim |
> | routeFilters | Sim | Sim |
> | routeTables | Sim | Sim |
> | serviceEndpointPolicies | Sim | Sim |
> | trafficManagerGeographicHierarchies | Não | Não |
> | trafficmanagerprofiles | Sim | Sim |
> | trafficmanagerprofiles/heatMaps | Não | Não |
> | tráfegoManagerUserMetricsKeys | Não | Não |
> | virtualWans | Sim | Sim |
> | virtualNetworkGateways | Sim | Sim |
> | virtualNetworks | Sim | Sim |
> | virtualNetworkTaps | Sim | Sim |
> | virtualWans | Sim | Sim |
> | vpnGateways | Sim | Não |
> | vpnSites | Sim | Sim |
> | frontdoorWebApplicationFirewallPolicies | Sim | Sim |

<a id="frontdoor" />

> [!NOTE]
> Para o Azure Front Door Service, você pode aplicar tags ao criar o recurso, mas atualizar ou adicionar tags não é suportado no momento.


## <a name="microsoftnotebooks"></a>Microsoft.Notebooks

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | NotebookProxies | Não | Não |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | namespaces | Sim | Não |
> | namespaces / notificationHubs | Sim | Não |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | osNamespaces | Sim | Sim |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | Hipervsites | Sim | Sim |
> | ImportaçãoSites | Sim | Sim |
> | ServidoresSites | Sim | Sim |
> | VMwareSites | Sim | Sim |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | clusters | Sim | Sim |
> | linkTargets | Não | Não |
> | storageInsightConfigs | Não | Não |
> | workspaces | Sim | Sim |
> | espaços de trabalho / dadosExporta | Não | Não |
> | espaços de trabalho / dataSources | Não | Não |
> | espaços de trabalho / linkedServices | Não | Não |
> | espaços de trabalho / linkedArmazenamentoContas | Não | Não |
> | espaços de trabalho/ consulta | Não | Não |
> | espaços de trabalho / escopoPrivateLinkProxies | Não | Não |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | managementassociations | Não | Não |
> | managementconfigurations | Sim | Sim |
> | solutions | Sim | Sim |
> | Modos de exibição | Sim | Sim |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | legadoPeerings | Não | Não |
> | peerAsns | Não | Não |
> | peerings | Sim | Sim |
> | peeringServiceCountries | Não | Não |
> | peeringServiceProviders | Não | Não |
> | peeringServices | Sim | Sim |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | policyEvents | Não | Não |
> | policyMetadata | Não | Não |
> | policyStates | Não | Não |
> | policyTrackedResources | Não | Não |
> | remediations | Não | Não |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | consoles | Não | Não |
> | dashboards | Sim | Sim |
> | userSettings | Não | Não |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | workspaceCollections | Sim | Sim |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | capacities | Sim | Sim |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | accounts | Sim | Sim |

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | Workspaces | Sim | Sim |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | backupProtectedItems | Não | Não |
> | vaults | Sim | Sim |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | namespaces | Sim | Sim |
> | namespaces / regras de autorização | Não | Não |
> | namespaces / conexões híbridas | Não | Não |
> | namespaces / conexões híbridas / regras de autorização | Não | Não |
> | namespaces / wcfrelays | Não | Não |
> | namespaces / wcfrelays / regras de autorização | Não | Não |

## <a name="microsoftremoteapp"></a>Microsoft.RemoteApp

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | accounts | Não | Não |
> | coleções | Sim | Sim |
> | coleções / aplicações | Não | Não |
> | coleções / seguranças principais | Não | Não |
> | modeloImagens | Não | Não |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | consultas | Sim | Sim |
> | recursoChangeDetails | Não | Não |
> | recursosAlterações | Não | Não |
> | recursos | Não | Não |
> | recursosHistóricohistórico | Não | Não |
> | subscriptionsStatus | Não | Não |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | availabilityStatuses | Não | Não |
> | childAvailabilityStatuses | Não | Não |
> | childResources | Não | Não |
> | questões emergentes | Não | Não |
> | events | Não | Não |
> | impactedResources | Não | Não |
> | metadata | Não | Não |
> | Notificações | Não | Não |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | deployments | Sim | Não |
> | implantações /operações | Não | Não |
> | implantaçãoScripts | Sim | Sim |
> | implantaçãoScripts/ logs | Não | Não |
> | links | Não | Não |
> | notifyResourceJobs | Não | Não |
> | providers | Não | Não |
> | resourceGroups | Sim | Não |
> | subscriptions | Sim | Não |
> | tenants | Não | Não |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | de dimensionamento da Web | Sim | Sim |
> | saasresources | Não | Não |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | resourceHealthMetadata | Não | Não |
> | searchServices | Sim | Sim |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | adaptiveNetworkHardenings | Não | Não |
> | advancedThreatProtectionSettings | Não | Não |
> | alertas | Não | Não |
> | allowedConnections | Não | Não |
> | applicationWhitelistings | Não | Não |
> | avaliaçãoMetadados | Não | Não |
> | avaliações | Não | Não |
> | AutoDismissAlertsRules | Não | Não |
> | Automações | Sim | Sim |
> | AutoProvisioningSettings | Não | Não |
> | Compliances | Não | Não |
> | dataCollectionAgents | Não | Não |
> | deviceSecurityGroups | Não | Não |
> | discoveredSecuritySolutions | Não | Não |
> | externalSecuritySolutions | Não | Não |
> | InformationProtectionPolicies | Não | Não |
> | iotSecuritySolutions | Sim | Sim |
> | iotSecuritySolutions / analyticsModelos | Não | Não |
> | iotSecuritySolutions / analyticsModels / agregadosAlertas | Não | Não |
> | iotSecuritySolutions / analyticsModels / agregadosRecomendações | Não | Não |
> | jitNetworkAccessPolicies | Não | Não |
> | networkData | Não | Não |
> | políticas | Não | Não |
> | pricings | Não | Não |
> | normas de conformidade regulamentar | Não | Não |
> | normas de conformidade/ regulamentaçãoConformidadeControls | Não | Não |
> | normas de conformidade/ regulamentaçãoConformidadeS/regulamentaçãoConformidades | Não | Não |
> | secureScoreControlDefinições | Não | Não |
> | secureScoreControls | Não | Não |
> | secureScores | Não | Não |
> | secureScores / secureScoreControls | Não | Não |
> | securityContacts | Não | Não |
> | securitySolutions | Não | Não |
> | securitySolutionsReferenceData | Não | Não |
> | securityStatuses | Não | Não |
> | securityStatusesSummaries | Não | Não |
> | avaliações de vulnerabilidade do servidor | Não | Não |
> | configurações | Não | Não |
> | subAvaliações | Não | Não |
> | tarefas | Não | Não |
> | topologies | Não | Não |
> | workspaceSettings | Não | Não |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Não | Não |
> | diagnosticSettingsCategories | Não | Não |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | agregações | Não | Não |
> | alertaRegras | Não | Não |
> | alertRuleTemplates | Não | Não |
> | indicadores | Não | Não |
> | cases | Não | Não |
> | dataConnectors | Não | Não |
> | dataConectoresVerificandorequisitos | Não | Não |
> | entidades | Não | Não |
> | entityQueries | Não | Não |
> | Incidentes | Não | Não |
> | officeConsents | Não | Não |
> | configurações | Não | Não |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | namespaces | Sim | Não |
> | namespaces / regras de autorização | Não | Não |
> | namespaces / disasterrecoveryconfigs | Não | Não |
> | namespaces / eventgridfilters | Não | Não |
> | namespaces / conjuntos de regras de rede | Não | Não |
> | namespaces / filas | Não | Não |
> | namespaces / filas / regras de autorização | Não | Não |
> | namespaces / tópicos | Não | Não |
> | namespaces / tópicos / regras de autorização | Não | Não |
> | namespaces / tópicos / assinaturas | Não | Não |
> | namespaces / tópicos / assinaturas / regras | Não | Não |
> | premiumMessagingRegions | Não | Não |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | de dimensionamento da Web | Sim | Sim |
> | clusters | Sim | Sim |
> | clusters/aplicações | Não | Não |
> | containerGroups | Sim | Sim |
> | contêinerGroupSets | Sim | Sim |
> | edgeclusters | Sim | Sim |
> | clusters de borda / aplicativos | Não | Não |
> | managedclusters | Sim | Sim |
> | clusters/tipos de nó | Não | Não |
> | networks | Sim | Sim |
> | secretstores | Sim | Sim |
> | lojas secretas / certificados | Não | Não |
> | lojas secretas / segredos | Não | Não |
> | volumes | Sim | Sim |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | de dimensionamento da Web | Sim | Sim |
> | containerGroups | Sim | Sim |
> | gateways | Sim | Sim |
> | networks | Sim | Sim |
> | segredos | Sim | Sim |
> | volumes | Sim | Sim |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | providerRegistrations | Não | Não |
> | provedorInscrições/recursosTypeInscrições | Não | Não |
> | rollouts | Sim | Sim |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | SignalR | Sim | Sim |
> | SignalR / eventGridFilters | Não | Não |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | SiteRecoveryVault | Sim | Sim |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | híbridoUseBenefícios | Não | Não |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | applicationDefinitions | Sim | Sim |
> | de dimensionamento da Web | Sim | Sim |
> | jitRequests | Sim | Sim |

## <a name="microsoftspoolservice"></a>Microsoft.SpoolService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | Assinaturas registradas | Não | Não |
> | Carretéis | Sim | Sim |


## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | managedInstances | Sim | Sim |
> | gerenciadoInstâncias/bancos de dados | Sim (veja [nota abaixo)](#sqlnote) | Sim |
> | gerenciadoInstâncias / bancos de dados / backupDecurtoprazoPolíticas de retenção | Não | Não |
> | gerenciadoInstâncias / bancos de dados / schemas / tabelas / colunas / sensibilidadeRótulos | Não | Não |
> | gerenciadoInstâncias/ bancos de dados / vulnerabilidadeAvaliações | Não | Não |
> | gerenciadoInstâncias / bancos de dados / vulnerabilidadeAvaliações / regras / linhas de base | Não | Não |
> | gerenciadoInstâncias / encryptionProtector | Não | Não |
> | gerenciadoInstâncias / chaves | Não | Não |
> | gerenciadoSInstâncias/ restaurávelDroppedDatabases / backupDecurtoprazoPolíticas de retenção | Não | Não |
> | gerenciadoInstâncias /vulnerabilidadeAvaliações | Não | Não |
> | servers | Sim | Sim |
> | servidores /administradores | Não | Não |
> | servidores / links de comunicação | Não | Não |
> | servidores /bancos de dados | Sim (veja [nota abaixo)](#sqlnote) | Sim |
> | servidores / encryptionProtector | Não | Não |
> | servidores / firewallRegras | Não | Não |
> | servidores /chaves | Não | Não |
> | servidores / restauráveisDroppedDatabases | Não | Não |
> | servidores /objetivos de serviço | Não | Não |
> | servidores / tdeCertificados | Não | Não |
> | clusters virtuais | Não | Não |

<a id="sqlnote" />

> [!NOTE]
> O banco de dados Mestre não oferece suporte a marcas, mas outros bancos de dados, incluindo os bancos de dados do SQL Data Warehouse do Azure, sim. Os bancos de dados do SQL Data Warehouse do Azure devem estar no estado Ativo (não Pausado).

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | SqlVirtualMachineGroups | Sim | Sim |
> | SqlVirtualMachineGroups / AvailabilityGroupListeners | Não | Não |
> | SqlVirtualMachines | Sim | Sim |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | storageAccounts | Sim | Sim |
> | armazenamentoContas / blobServices | Não | Não |
> | armazenamentoContas / arquivosServiços | Não | Não |
> | armazenamentoContas / filaServiços | Não | Não |
> | armazenamentoContas/serviços | Não | Não |
> | armazenamentoContas/ serviços / métricaDefinições | Não | Não |
> | armazenamentoContas / tabelaServiços | Não | Não |
> | usages | Não | Não |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | Caches | Sim | Sim |
> | caches/ armazenamentoAlvos | Não | Não |
> | useModels | Não | Não |

## <a name="microsoftstoragereplication"></a>Microsoft.StorageReplication

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | replicaçãoGrupos | Não | Não |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Sim | Sim |
> | armazenamentoSyncServices / servidores registrados | Não | Não |
> | armazenamentoSyncServices / syncGroups | Não | Não |
> | armazenamentoSyncServices / syncGroups / cloudEndpoints | Não | Não |
> | armazenamentoSyncServices / syncGroups / serverEndpoints | Não | Não |
> | armazenamentoSyncServices / fluxos de trabalho | Não | Não |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Sim | Sim |
> | armazenamentoSyncServices / servidores registrados | Não | Não |
> | armazenamentoSyncServices / syncGroups | Não | Não |
> | armazenamentoSyncServices / syncGroups / cloudEndpoints | Não | Não |
> | armazenamentoSyncServices / syncGroups / serverEndpoints | Não | Não |
> | armazenamentoSyncServices / fluxos de trabalho | Não | Não |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Sim | Sim |
> | armazenamentoSyncServices / servidores registrados | Não | Não |
> | armazenamentoSyncServices / syncGroups | Não | Não |
> | armazenamentoSyncServices / syncGroups / cloudEndpoints | Não | Não |
> | armazenamentoSyncServices / syncGroups / serverEndpoints | Não | Não |
> | armazenamentoSyncServices / fluxos de trabalho | Não | Não |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | managers | Sim | Sim |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | streamingjobs | Sim (veja a observação a seguir) | Sim |

> [!NOTE]
> Você não pode adicionar uma marca quando streamingjobs estiver em execução. Pare o recurso para adicionar uma marca.

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | cancel | Não | Não |
> | CreateSubscription | Não | Não |
> | enable | Não | Não |
> | renomear | Não | Não |
> | SubscriptionDefinitions | Não | Não |
> | SubscriptionDefinitions | Não | Não |
> | subscriptions | Não | Não |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | environments | Sim | Não |
> | ambientes / políticas de acesso | Não | Não |
> | ambientes / fontes de eventos | Sim | Não |
> | ambientes / referênciaConjuntos de dados | Sim | Não |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | dedicadoCloudNodes | Sim | Sim |
> | dedicadoCloudServices | Sim | Sim |
> | virtualMachines | Sim | Sim |

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | dispositivos | Sim | Sim |
> | Assinaturas registradas | Não | Não |
> | Fornecedores | Não | Não |
> | fornecedores / skus | Não | Não |
> | fornecedores / vnfs | Não | Não |
> | virtualNetworkFunctionSkus | Não | Não |
> | vnfs | Sim | Sim |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | apiManagementAccounts | Não | Não |
> | apiManagementContas / apiAcls | Não | Não |
> | apiManagementContas / apis | Não | Não |
> | apiManagementContas / apis / apiAcls | Não | Não |
> | apiManagementContas / apis / conexãoAcls | Não | Não |
> | apiManagementContas / apis / conexões | Não | Não |
> | apiManagementContas / apis / conexões / conexõesAcls | Não | Não |
> | apiManagementContas / apis / localizadoDefinições | Não | Não |
> | apiManagementContas / conexãoAcls | Não | Não |
> | apiManagementContas / conexões | Não | Não |
> | billingMeters | Não | Não |
> | certificates | Sim | Sim |
> | connectionGateways | Sim | Sim |
> | connections | Sim | Sim |
> | customApis | Sim | Sim |
> | deletedSites | Não | Não |
> | hostingEnvironments | Sim | Sim |
> | hostingAmbientes / eventGridFilters | Não | Não |
> | hostingAmbientes / multiRolePools | Não | Não |
> | hospedagemAmbientes / workerPools | Não | Não |
> | kubeAmbientes | Sim | Sim |
> | publishingUsers | Não | Não |
> | filmes | Não | Não |
> | resourceHealthMetadata | Não | Não |
> | runtimes | Não | Não |
> | serverFarms | Sim | Sim |
> | serverFarms / eventGridFilters | Não | Não |
> | sites | Sim | Sim |
> | sites / config  | Não | Não |
> | sites / eventGridFilters | Não | Não |
> | sites / hostNameVinculações | Não | Não |
> | sites / redeConfig | Não | Não |
> | sites / premieraddons | Sim | Sim |
> | sites / slots | Sim | Sim |
> | sites / slots / eventGridFilters | Não | Não |
> | sites / slots / hostNameVinculções | Não | Não |
> | sites / slots / redeConfig | Não | Não |
> | sourceControls | Não | Não |
> | staticSites | Sim | Sim |
> | validade | Não | Não |
> | verifyHostingEnvironmentVnet | Não | Não |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Não | Não |
> | diagnosticSettingsCategories | Não | Não |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | DeviceServices | Sim | Sim |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Tag in relatório de custos |
> | ------------- | ----------- | ----------- |
> | components | Não | Não |
> | componentsSummary | Não | Não |
> | monitorInstances | Não | Não |
> | monitorInstancesSummary | Não | Não |
> | monitors | Não | Não |
> | notificationSettings | Não | Não |

## <a name="next-steps"></a>Próximas etapas

Para saber como aplicar tags a recursos, consulte [Use tags para organizar seus recursos do Azure](tag-resources.md).
