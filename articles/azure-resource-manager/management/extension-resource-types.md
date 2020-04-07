---
title: Tipos de recursos de extensão
description: Listas dos tipos de recursos do Azure são usadas para ampliar os recursos de outros tipos de recursos.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 3a3fbc531750bec4b16e38f1fe79f613c1b94f5e
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754860"
---
# <a name="resource-types-that-extend-capabilities-of-other-resources"></a>Tipos de recursos que ampliam recursos de outros recursos

Um recurso de extensão é um recurso que adiciona aos recursos de outro recurso. Por exemplo, o bloqueio de recursos é um recurso de extensão. Você aplica um bloqueio de recursos a outro recurso para evitar que ele seja excluído ou modificado. Não faz sentido criar um bloqueio de recursos sozinho. Um recurso de extensão é sempre aplicado a outro recurso.

## <a name="extension-resource-types"></a>Tipos de recursos de extensão

- Microsoft.Advisor/configurações
- Microsoft.Advisor/recomendações
- Microsoft.Advisor/supressões
- Microsoft.AlertsGerenciamento/alertas
- Microsoft.AlertsGerenciamento/alertasResumo de sumário
- Microsoft.Authorization/checkAccess
- Microsoft.Authorization/denyAssignments
- Microsoft.Authorization/findOrphanRoleAssignments
- Microsoft.Authorization/locks
- Microsoft.Autorização/permissões
- Microsoft.Authorization/policyAtribuições
- Microsoft.Authorization/policyDefinições
- Microsoft.Authorization/policySetDefinições
- Microsoft.Authorization/roleAssignments
- Microsoft.Authorization/roleAssignmentSUseMetrics
- Microsoft.Authorization/roleDefinições
- Microsoft.Billing/billingPeriods
- Microsoft.Billing/billingPermissões
- Microsoft.Billing/billingRoleAssignments
- Microsoft.Billing/billingRoleDefinições
- Microsoft.Billing/createBillingRoleAssignment
- Microsoft.Blueprint/blueprintAtribuições
- Microsoft.Blueprint/blueprints
- Microsoft.Consumption/Custo Agregado
- Microsoft.Consumo/Saldos
- Microsoft.Consumo/Orçamentos
- Microsoft.Consumption/Charges
- Microsoft.Consumption/CostTags
- Microsoft.Consumption/Forecasts
- Microsoft.Consumption/Marketplaces
- Microsoft.Consumo/OperaçãoResultados
- Microsoft.Consumo/OperaçãoStatus
- Microsoft.Consumption/Pricesheets
- Microsoft.Consumo/ReservasDetalhes
- Microsoft.Consumo/Recomendações de reserva
- Microsoft.Consumo/ReservasResumos
- Microsoft.Consumption/ReservationTransactions
- Microsoft.Consumption/Tags
- Microsoft.Consumption/Termos
- Microsoft.Consumo/UsoDetalhes
- Microsoft.Consumo/créditos
- Microsoft.Consumo/eventos
- Microsoft.Consumo/lotes
- Microsoft.Consumo/produtos
- Microsoft.Consumo/inquilinos
- Microsoft.ContainerInstance/serviceAssociationLinks
- Microsoft.CostManagement/Alertas
- Microsoft.CostManagement/Orçamentos
- Microsoft.CostManagement/Dimensões
- Microsoft.CostManagement/Exportações
- Microsoft.CostManagement/Assinaturas externas
- Microsoft.CostManagement/Forecast
- Microsoft.CostManagement/Consulta
- Microsoft.CostManagement/Reportconfigs
- Microsoft.CostManagement/Reports
- Microsoft.CostManagement/Views
- Microsoft.CostManagement/showbackRules
- Microsoft.CustomProviders/associations
- Microsoft.EventGrid/eventSubscriptions
- Microsoft.EventGrid/extensionTopics
- Microsoft.GuestConfiguração/configuraçãoPerfildeperfil
- Atribuição de configuração/convidado do Microsoft.GuestConfiguration
- Microsoft.GuestConfiguration/software
- Microsoft.GuestConfiguration/softwareUpdateProfile
- Microsoft.GuestConfiguration/softwareAtualizações
- microsoft.insights/linha de base
- microsoft.insights/calculatebaseline
- microsoft.insights/dataCollectionRuleAssociations
- microsoft.insights/diagnosticSettings
- microsoft.insights/diagnosticSettingsCategorias
- microsoft.insights/eventtypes
- microsoft.insights/extendedDiagnosticSettings
- microsoft.insights/guestDiagnosticSettingsAssociation
- microsoft.insights/logDefinições
- microsoft.insights/logs
- microsoft.insights/metricDefinições
- microsoft.insights/metricNamespaces
- microsoft.insights/metricbaselines
- microsoft.insights/métricas
- microsoft.insights/myWorkbooks
- microsoft.insights/topologia
- microsoft.insights/transações
- microsoft.insights/vmInsightsOnboardingStatuses
- Microsoft.KubernetesConfiguração/origemConfigurações de controle
- Microsoft.Manutenção/aplicarAtualizações
- Microsoft.Manutenção/configuraçãoAtribuições
- Microsoft.Manutenção/atualizações
- Microsoft.ManagedIdentidade/Identidades
- Microsoft.ManagedServices/registrationAtribuições
- Microsoft.ManagedServices/registrationDefinições
- Microsoft.OperationalInsights/storageInsightConfigs
- Microsoft.OperationsCos de gerenciamento/gerenciamento
- Microsoft.PolicyInsights/policyEventos
- Microsoft.PolicyInsights/policyEstados
- Microsoft.PolicyInsights/policyTrackedResources
- Microsoft.PolicyInsights/remediações
- Microsoft.RecoveryServices/backupItens protegidos
- Microsoft.RecoveryServices/replicationElegibilidadeResultados de elegibilidade    
- Microsoft.ResourceHealth/disponibilidadeStatuss
- Microsoft.ResourceHealth/childAvailabilityStatuses
- Microsoft.ResourceHealth/childResources
- Microsoft.ResourceHealth/eventos
- Microsoft.ResourceHealth/impactedResources
- Microsoft.ResourceHealth/notificações
- Microsoft.Resources/links
- Microsoft.Resources/tags
- Microsoft.Security/Compliances
- Microsoft.Security/InformationProtectionPolicies
- Microsoft.Security/adaptiveNetworkHardenings
- Microsoft.Security/advancedThreatProtectionConfigurações
- Microsoft.Security/assessmentMetadata
- Microsoft.Security/avaliações
- Microsoft.Security/complianceResultados
- Microsoft.Security/dataCollectionAgents
- Microsoft.Security/deviceSecurityGroups
- Microsoft.Security/networkData
- Microsoft.Security/serverAvaliações de vulnerabilidade
- Microsoft.SecurityInsights/agregações
- Microsoft.SecurityInsights/alertRuleTemplates
- Microsoft.SecurityInsights/alertRules
- Microsoft.SecurityInsights/marcadores
- Microsoft.SecurityInsights/cases
- Microsoft.SecurityInsights/dataConnectors
- Microsoft.SecurityInsights/dataConectoresVerificamrequisitos
- Microsoft.SecurityInsights/entidades
- Microsoft.SecurityInsights/entityQueries
- Microsoft.SecurityInsights/incidentes
- Microsoft.SecurityInsights/officeConsents
- Microsoft.SecurityInsights/configurações
- Microsoft.SoftwarePlan/híbridoUsebenefícios
- Microsoft.Subscription/CreateSubscription
- microsoft.support/supporttickets
- Microsoft.WorkloadMonitor/componentes
- Microsoft.WorkloadMonitor/monitorInstâncias
- Microsoft.WorkloadMonitor/monitores
- Microsoft.WorkloadMonitor/notificationConfigurações

## <a name="next-steps"></a>Próximas etapas

- Para obter o ID de recurso de recurso de extensão em um modelo do Azure Resource Manager, use a [extensãoResourceId](../templates/template-functions-resource.md#extensionresourceid).
- Para um exemplo de criação de um recurso de extensão em um modelo, consulte Assinaturas de [eventos da Grade de Eventos](/azure/templates/microsoft.eventgrid/2019-06-01/eventsubscriptions).
