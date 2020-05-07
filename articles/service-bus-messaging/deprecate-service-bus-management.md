---
title: Serviços de mensagens do Azure-Service Manager para o Gerenciador de recursos
description: Este artigo fornece mapeamento de API REST preterida do Azure Service Manager & cmdlets do PowerShell para a API REST do Gerenciador de recursos & cmdlets do PowerShell.
services: service-bus-messaging, event-hubs, event-grid
documentationcenter: na
author: spelluru
editor: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/22/2020
ms.author: spelluru
ms.openlocfilehash: 71e64f4be44d835ad4eed0bb74177e55aef3a35a
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82792251"
---
# <a name="deprecation-of-azure-service-manager-support-for-azure-service-bus-relay-and-event-hubs"></a>Substituição do suporte de Service Manager do Azure para o barramento de serviço do Azure, retransmissão e hubs de eventos

O Gerenciador de recursos, nossa pilha de infraestrutura de nuvem de última geração, está substituindo totalmente o modelo de gerenciamento de serviços do Azure "clássico" (modelo de implantação clássico). Como resultado, as APIs REST do modelo de implantação clássica e o suporte para o barramento de serviço, retransmissão e hubs de eventos serão desativados em 1º de novembro de 2021. Essa reprovação foi anunciada primeiro em um [comunicado da comunidade técnica da Microsoft](https://techcommunity.microsoft.com/t5/Service-Bus-blog/Deprecating-Service-Management-support-for-Azure-Service-Bus/ba-p/370909), mas decidimos recentemente estender o período de reprovação mais dois anos a partir da hora do anúncio original. Para facilitar a identificação, essas APIs `management.core.windows.net` têm em seu URI. Consulte a tabela a seguir para obter uma lista das APIs preteridas e sua versão Azure Resource Manager API que você deve usar agora.

Para continuar usando o barramento de serviço, retransmissão e hubs de eventos, vá para o Resource Manager em 31 de outubro de 2021. Incentivamos todos os clientes que ainda estão usando APIs antigas para tornar o switch em breve aproveitar os benefícios adicionais do Resource Manager, que incluem agrupamento de recursos, marcas, um processo de implantação e gerenciamento simplificado e controle de acesso refinado usando o RBAC (controle de acesso baseado em função).

Para obter mais informações sobre Azure Resource Manager do Service Manager do Azure, consulte o [blog do TechNet](https://blogs.technet.microsoft.com/meamcs/2016/12/22/difference-between-azure-service-manager-and-azure-resource-manager/).

Para obter mais informações sobre as APIs do Service Manager e do Resource Manager para o barramento de serviço do Azure, retransmissão e hubs de eventos, consulte nossa documentação da API REST:

- [Barramento de Serviço do Azure](/rest/api/servicebus/)
- [Hubs de eventos do Azure](/rest/api/eventhub/)
- [Retransmissão do Azure](/rest/api/relay/)

## <a name="service-manager-rest-api---resource-manager-rest-api"></a>API REST do Service Manager – API REST do Gerenciador de recursos

| APIs de Service Manager (preteridas) | Gerenciador de recursos-API do barramento de serviço | Gerenciador de recursos-API do hub de eventos | Gerenciador de recursos-API de retransmissão |
| --------------- | ----------------- | ----------------- | ----------------- | 
| **Namespaces-GetNamespaceAsync** <br/>[Namespace Get do barramento de serviço](/rest/api/servicebus/get-namespace)<br/>[Namespace de Get do hub de eventos](/rest/api/eventhub/get-event-hub)<br/>[Namespace Get de retransmissão](/rest/api/servicebus/get-relays)<br/> ```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [get](/rest/api/servicebus/namespaces/get) | get | [get](/rest/api/relay/namespaces/get) |
| **ConnectionDetails-GetConnectionDetails**<br/>Barramento de serviço/Hub de eventos/GetConnectionDetals de retransmissão<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/ConnectionDetails``` | [listkeys](/rest/api/servicebus/namespaces/listkeys) | listkeys | [listkeys](/rest/api/relay/namespaces/listkeys) |
| **Tópicos-GetTopicsAsync**<br/>Barramento de Serviço<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/topics? $skip={skip}&$top={top}``` | [list](/rest/api/servicebus/topics/listbynamespace) | &nbsp; | &nbsp; | 
| **Filas-GetQueueAsync** <br/>Barramento de Serviço<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/queues/{queueName}``` | [get](/rest/api/servicebus/queues/get) | &nbsp; | &nbsp; | 
| **Retransmissões-GetRelaysAsync**<br/>[Obter retransmissões](/rest/api/servicebus/get-relays)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/relays? $skip={skip}&$top={top}```| &nbsp; | &nbsp; | [list](/rest/api/relay/wcfrelays/listbynamespace) | 
| **NamespaceAuthorizationRules-GetNamespaceAuthorizationRuleAsync**<br/>Barramento de serviço/Hub de eventos/GetNamespaceAuthRule de retransmissão<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/authorizationrules?``` | [getauthorizationrule](/rest/api/servicebus/namespaces/getauthorizationrule) |getauthorizationrule | [getauthorizationrule](/rest/api/relay/namespaces/getauthorizationrule) |
| **Namespaces-DeleteNamespaceAsync**<br/>[Namespace de exclusão do barramento de serviço](/rest/api/servicebus/delete-namespace)<br/>[Namespace de exclusão de hubs de eventos](/rest/api/eventhub/delete-event-hub)<br/>[Retransmite o namespace de exclusão](/rest/api/servicebus/delete-namespace)<br/> ```DELETE  https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [delete](/rest/api/servicebus/namespaces/delete) | excluir | [delete](/rest/api/relay/namespaces/delete) | 
| **MessagingSKUPlan-GetPlanAsync**<br/>Barramento de serviço/Hub de eventos/namespace Get de retransmissão<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/MessagingPlan``` | [get](/rest/api/servicebus/namespaces/get) | get | [get](/rest/api/relay/namespaces/get) |
| **MessagingSKUPlan-UpdatePlanAsync**<br/>Barramento de serviço/Hub de eventos/namespace Get de retransmissão<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/MessagingPlan``` | [createorupdate](/rest/api/servicebus/namespaces/createorupdate) | createorupdate | [createorupdate](/rest/api/relay/namespaces/createorupdate) |
| **NamespaceAuthorizationRules-UpdateNamespaceAuthorizationRuleAsync**<br/>Barramento de serviço/Hub de eventos/namespace Get de retransmissão<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules/{rule name}``` | [createorupdate](/rest/api/servicebus/namespaces/createorupdate) | createorupdateauthorizationrule | [createorupdateauthorizationrule](/rest/api/relay/namespaces/createorupdateauthorizationrule) | 
| **NamespaceAuthorizationRules-CreateNamespaceAuthorizationRuleAsync**<br/> 
Barramento de serviço/Hub de eventos/retransmissão<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules/{rule name}``` |[createorupdate](/rest/api/servicebus/namespaces/createorupdate) | createorupdateauthorizationrule | [createorupdateauthorizationrule](/rest/api/relay/namespaces/createorupdateauthorizationrule) |
| **Namespaceproperties-GetNamespacePropertiesAsync**<br/>[Namespace Get do barramento de serviço](/rest/api/servicebus/get-namespace)<br/>[Namespace Get dos hubs de eventos](/rest/api/eventhub/get-event-hub)<br/>[Namespace Get de retransmissão](/rest/api/servicebus/get-relays)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [get](/rest/api/servicebus/namespaces/get) | get | [get](/rest/api/relay/namespaces/get) |
| **RegionCodes-GetRegionCodesAsync**<br/>Namespace do barramento de serviço/EventHub/Relay Get<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [listbysku](/rest/api/servicebus/regions/listbysku) | listbysku | &nbsp; | 
| **Namespaceproperties-UpdateNamespacePropertyAsync**<br/>Barramento de serviço/EventHub/retransmissão<br/>```GET    https://management.core.windows.net/{subscription ID}/services/ServiceBus/Regions/``` | [createorupdate](/rest/api/servicebus/namespaces/createorupdate) | createorupdate | [createorupdate](/rest/api/relay/namespaces/createorupdate) |
| **EventHubsCrud-ListEventHubsAsync**<br/>[Listar hubs de eventos](/rest/api/eventhub/list-event-hubs)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/eventhubs?$skip={skip}&$top={top}``` | &nbsp; | [list](/rest/api/servicebus/eventhubs/listbynamespace) | &nbsp; | 
| **EventHubsCrud-GetEventHubAsync**<br/>[Obter hubs de eventos](/rest/api/eventhub/get-event-hub)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/eventhubs/{eventHubPath}``` | &nbsp; | [get](/rest/api/eventhub/get-event-hub) | &nbsp; | 
| **NamespaceAuthorizationRules-DeleteNamespaceAuthorizationRuleAsync**<br/>Barramento de serviço/Hub de eventos/retransmissão<br/>```DELETE https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules/{rule name}``` | [deleteauthorizationrule](/rest/api/servicebus/namespaces/deleteauthorizationrule) | deleteauthorizationrule | [deleteauthorizationrule](/rest/api/relay/namespaces/deleteauthorizationrule) |
| **NamespaceAuthorizationRules-GetNamespaceAuthorizationRulesAsync**<br/>Barramento de serviço/EventHub/retransmissão<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules``` | [listauthorizationrules](/rest/api/servicebus/namespaces/listauthorizationrules) | listauthorizationrules | [listauthorizationrules](/rest/api/relay/namespaces/listauthorizationrules) |
| **NamespaceAvailability-IsNamespaceAvailable**<br/>[Disponibilidade do namespace do barramento de serviço](/rest/api/servicebus/check-namespace-availability)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/CheckNamespaceAvailability/?namespace=<namespaceValue>``` | [checknameavailability](/rest/api/servicebus/namespaces/checknameavailability) | checknameavailability | [checknameavailability](/rest/api/relay/namespaces/checknameavailability) |
| **Namespaces-CreateOrUpdateNamespaceAsync**<br/>Barramento de serviço/Hub de eventos/retransmissão<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [createorupdate](/rest/api/servicebus/namespaces/createorupdate) | createorupdate | [createorupdate](/rest/api/relay/namespaces/createorupdate) | 
| **Tópicos-GetTopicAsync**<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/topics/{topicPath}``` | [get](/rest/api/servicebus/topics/get) | &nbsp; | &nbsp; |

## <a name="service-manager-powershell---resource-manager-powershell"></a>Service Manager PowerShell – PowerShell do Gerenciador de recursos
| Service Manager comando do PowerShell (preterido) | Novos comandos do Resource Manager | Comando mais recente do Resource Manager |
| ----- | ----- | ----- | 
| [Get-AzureSBAuthorizationRule](/powershell/module/servicemanagement/azure/get-azuresbauthorizationrule?view=azuresmps-4.0.0) | [Get-AzureRmServiceBusAuthorizationRule](/powershell/module/azurerm.servicebus/get-azurermservicebusauthorizationrule?view=azurermps-6.13.0) | [Get-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/get-azservicebusauthorizationrule?view=azps-1.6.0) |
| [Get-AzureSBLocation](/powershell/module/servicemanagement/azure/get-azuresblocation?view=azuresmps-4.0.0) | [Get-AzureRmServiceBusGeoDRConfiguration](/powershell/module/azurerm.servicebus/get-azurermservicebusgeodrconfiguration?view=azurermps-6.13.0) | [Get-AzServiceBusGeoDRConfiguration](/powershell/module/az.servicebus/get-azservicebusgeodrconfiguration?view=azps-1.6.0) |
| [Get-AzureSBNamespace](/powershell/module/servicemanagement/azure/get-azuresbnamespace?view=azuresmps-4.0.0) | [Get-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/get-azurermservicebusnamespace?view=azurermps-6.13.0) | [Get-AzServiceBusNamespace](/powershell/module/az.servicebus/get-azservicebusnamespace?view=azps-1.6.0) |
| [New-AzureSBAuthorizationRule](/powershell/module/servicemanagement/azure/new-azuresbauthorizationrule?view=azuresmps-4.0.0) | [New-AzureRmServiceBusAuthorizationRule](/powershell/module/azurerm.servicebus/new-azurermservicebusauthorizationrule?view=azurermps-6.13.0) | [New-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/new-azservicebusauthorizationrule?view=azps-1.6.0) |
| [New-AzureSBNamespace](/powershell/module/servicemanagement/azure/new-azuresbnamespace?view=azuresmps-4.0.0) | [New-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/new-azurermservicebusnamespace?view=azurermps-6.13.0) | [New-AzServiceBusNamespace](/powershell/module/az.servicebus/new-azservicebusnamespace?view=azps-1.6.0) |
| [Remove-AzureRmRelayAuthorizationRule](/powershell/module/azurerm.relay/remove-azurermrelayauthorizationrule?view=azurermps-6.13.0) | [Remove-AzureRmEventHubAuthorizationRule](/powershell/module/azurerm.eventhub/remove-azurermeventhubauthorizationrule?view=azurermps-6.13.0) | [Remove-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/remove-azservicebusauthorizationrule?view=azps-1.6.0) |
| [Remove-AzureSBNamespace](/powershell/module/servicemanagement/azure/remove-azuresbnamespace?view=azuresmps-4.0.0) | [Remove-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/remove-azurermservicebusnamespace?view=azurermps-6.13.0) | [Remove-AzServiceBusNamespace](/powershell/module/az.servicebus/remove-azservicebusnamespace?view=azps-1.6.0) |
| [Set-AzureSBAuthorizationRule](/powershell/module/servicemanagement/azure/set-azuresbauthorizationrule?view=azuresmps-4.0.0) | [Set-AzureRmServiceBusAuthorizationRule](/powershell/module/azurerm.servicebus/set-azurermservicebusauthorizationrule?view=azurermps-6.13.0) | [Set-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/set-azservicebusauthorizationrule?view=azps-1.6.0) |

## <a name="next-steps"></a>Próximas etapas
Consulte a seguinte documentação: 

- Documentação da API REST mais recente
    - [Barramento de Serviço do Azure](/rest/api/servicebus/)
    - [Hubs de eventos do Azure](/rest/api/eventhub/)
    - [Retransmissão do Azure](/rest/api/relay/)
- Documentação mais recente do PowerShell
    - [Barramento de Serviço do Azure](/powershell/module/azurerm.servicebus/?view=azurermps-6.13.0#service_bus)
    - [Hubs de eventos do Azure](/powershell/module/azurerm.eventhub/?view=azurermps-6.13.0#event_hub)
    - [Grade de Eventos do Azure](/powershell/module/azurerm.eventgrid/?view=azurermps-6.13.0#event_grid)
