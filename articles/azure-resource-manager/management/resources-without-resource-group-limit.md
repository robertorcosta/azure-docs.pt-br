---
title: Recursos sem limite de contagem de 800
description: Lista os tipos de recursos do Azure que podem ter mais de 800 instâncias em um grupo de recursos.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 8644bec1a68acebff18cf83d17acb014784dc964
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804768"
---
# <a name="resources-not-limited-to-800-instances-per-resource-group"></a>Recursos não limitados a 800 instâncias por grupo de recursos

Por padrão, você pode implantar até 800 instâncias de um tipo de recurso em cada grupo de recursos. No entanto, alguns tipos de recursos estão isentos do limite de 800 instâncias. Este artigo lista os tipos de recursos do Azure que podem ter mais de 800 instâncias em um grupo de recursos. Todos os outros tipos de recursos estão limitados a 800 instâncias.

Para alguns tipos de recursos, você precisa entrar em contato com o suporte para ter o limite de 800 instâncias removido. Esses tipos de recursos são observados neste artigo.


## <a name="microsoftautomation"></a>Microsoft.Automation

* automationaccounts

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

* registrations
* registrations/customerSubscriptions
* registrations/products

## <a name="microsoftbotservice"></a>Microsoft.BotService

* botServices - Por padrão, limitado a 800 instâncias. Esse limite pode ser aumentado entrando em contato com o suporte.

## <a name="microsoftcompute"></a>Microsoft.Compute

* disks
* galleries
* galleries/images
* galleries/images/versions
* images
* snapshots
* virtualMachines

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

* containerGroups

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

* registries/buildTasks
* registros/compilaçãoTarefas/listSourceRepositoryProperties
* registries/buildTasks/steps
* registros/compilaçãoTarefas/etapas/listBuildArguments
* registries/eventGridFilters
* registries/replications
* registries/tasks
* registries/webhooks

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

* servers

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

* servers

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

* grupos de servidores
* servers
* servidoresv2
* servidores únicos

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

* horários - Por padrão, limitado a 800 instâncias. Esse limite pode ser aumentado entrando em contato com o suporte.

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

* services

## <a name="microsofteventhub"></a>Microsoft.EventHub

* clusters
* namespaces

## <a name="microsoftexperimentation"></a>Microsoft.Experimentação

* experimentoespaços de trabalho

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

* perfis de configuração gerenciados automaticamente
* configuraçãoAtribuições de perfil
* guestConfigurationAssignments
* software
* softwareUpdateProfile
* atualizações de software

## <a name="microsoftinsights"></a>microsoft.insights

* metricalerts

## <a name="microsoftlogic"></a>Microsoft.Logic

* integrationAccounts
* workflows

## <a name="microsoftnetapp"></a>Microsoft.NetApp

* netAppContas
* netAppContas/capacityPools
* netAppContas/capacityPools/volumes
* netAppContas/capacityPools/volumes/mountTargets
* netAppContas/capacityPools/volumes/snapshots

## <a name="microsoftnetwork"></a>Microsoft.Network

* aplicativoGatewayWebAplicativoDe aplicativosFirewall
* applicationSecurityGroups
* bastiõesanfitriões
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
* publicIPAddresses - Por padrão, limitado a 800 instâncias. Esse limite pode ser aumentado entrando em contato com o suporte.
* serviceEndpointPolicies
* trafficmanagerprofiles
* virtualNetworkTaps

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

* rootResources

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

* workspaceCollections - Por padrão, limitado a 800 instâncias. Esse limite pode ser aumentado entrando em contato com o suporte.

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

Para obter uma lista completa de cotas e limites, consulte limites de assinatura e serviço do [Azure, cotas e restrições](azure-subscription-service-limits.md).
