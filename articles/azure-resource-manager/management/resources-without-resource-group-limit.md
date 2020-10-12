---
title: Recursos sem limite de contagem de 800
description: Lista os tipos de recursos do Azure que podem ter mais de 800 instâncias em um grupo de recursos.
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: e7a15607b144f1d7916f310948f15dc28d76a205
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91330750"
---
# <a name="resources-not-limited-to-800-instances-per-resource-group"></a>Recursos não limitados a 800 instâncias por grupo de recursos

Por padrão, você pode implantar até 800 instâncias de um tipo de recurso em cada grupo de recursos. No entanto, alguns tipos de recursos são isentos do limite da instância 800. Este artigo lista os tipos de recursos do Azure que podem ter mais de 800 instâncias em um grupo de recursos. Todos os outros tipos de recursos são limitados a 800 instâncias.

Para alguns tipos de recursos, você precisa entrar em contato com o suporte para que o limite da instância 800 seja removido. Esses tipos de recursos são indicados neste artigo.


## <a name="microsoftautomation"></a>Microsoft.Automation

* automationaccounts

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

* edgeSubscriptions
* linkedSubscriptions
* registrations
* registrations/customerSubscriptions
* registrations/products

## <a name="microsoftbotservice"></a>Microsoft.BotService

* botServices-por padrão, limitado a 800 instâncias. Esse limite pode ser aumentado entrando em contato com o suporte.

## <a name="microsoftcompute"></a>Microsoft.Compute

* disks
* galleries
* galleries/images
* galleries/images/versions
* images
* snapshots
* virtualMachineScaleSets-por padrão, limitado a 800 instâncias. Esse limite pode ser aumentado entrando em contato com o suporte.
* virtualMachines

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

* containerGroups

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

* registries/buildTasks
* registros/buildTasks/listSourceRepositoryProperties
* registries/buildTasks/steps
* registros/buildTasks/etapas/listBuildArguments
* registries/eventGridFilters
* registries/replications
* registries/tasks
* registries/webhooks

## <a name="microsoftd365customerinsights"></a>Microsoft. D365CustomerInsights

* instances

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

* servers

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

* flexibleServers
* servers

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

* flexibleServers
* serverGroups
* servers
* serversv2

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

* schedules

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

* services

## <a name="microsofteventhub"></a>Microsoft.EventHub

* clusters
* namespaces

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

* experimentWorkspaces

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

* autoManagedVmConfigurationProfiles
* configurationProfileAssignments
* guestConfigurationAssignments
* software
* softwareUpdateProfile
* softwareUpdates

## <a name="microsoftinsights"></a>microsoft.insights

* metricalerts

## <a name="microsoftlogic"></a>Microsoft.Logic

* integrationAccounts
* workflows

## <a name="microsoftmedia"></a>Microsoft.Media

* mediaservices/liveEvents

## <a name="microsoftnetapp"></a>Microsoft.NetApp

* netAppAccounts
* netAppAccounts/capacityPools
* netAppAccounts/capacityPools/volumes
* netAppAccounts/capacityPools/volumes/mountTargets
* netAppAccounts/capacityPools/volumes/instantâneos

## <a name="microsoftnetwork"></a>Microsoft.Network

* applicationGatewayWebApplicationFirewallPolicies
* applicationSecurityGroups
* bastionHosts
* ddosProtectionPlans
* dnszones
* dnszones/A
* dnszones/AAAA
* dnszones/CAA
* dnszones/CNAME
* dnszones/MX
* dnszones/NS
* dnszones/PTR
* dnszones/SOA
* dnszones/SRV
* dnszones/TXT
* dnszones/all
* dnszones/recordsets
* networkIntentPolicies
* networkInterfaces
* privateDnsZones
* privateDnsZones/A
* privateDnsZones/AAAA
* privateDnsZones/CNAME
* privateDnsZones/MX
* privateDnsZones/PTR
* privateDnsZones/SOA
* privateDnsZones/SRV
* privateDnsZones/TXT
* privateDnsZones/tudo
* privateDnsZones/virtualNetworkLinks
* privateEndpoints
* privateLinkServices
* publicIPAddresses-por padrão, limitado a 800 instâncias. Esse limite pode ser aumentado entrando em contato com o suporte.
* serviceEndpointPolicies
* trafficmanagerprofiles
* virtualNetworkTaps

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

* rootResources

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

* workspaceCollections-por padrão, limitado a 800 instâncias. Esse limite pode ser aumentado entrando em contato com o suporte.

## <a name="microsoftrelay"></a>Microsoft.Relay

* namespaces

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

* jobcollections

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

* namespaces

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

* de dimensionamento da Web
* containerGroups
* gateways
* networks
* segredos
* volumes

## <a name="microsoftstorage"></a>Microsoft.Storage

* storageAccounts

## <a name="microsoftweb"></a>Microsoft.Web

* apiManagementAccounts/apis
* sites

## <a name="next-steps"></a>Próximas etapas

Para obter uma lista completa de cotas e limites, confira [assinatura do Azure e limites de serviço, cotas e restrições](azure-subscription-service-limits.md).
