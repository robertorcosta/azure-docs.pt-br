---
title: Serviços de mensagens Azure - Gerente de Serviços para Gerenciador de Recursos
description: Este artigo fornece mapeamento da API REST & Cmdlets PowerShell do Gerenciador de Recursos REST & cmdlets PowerShell.
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
ms.openlocfilehash: d263381667319b98a28ee6168e2de75c4041b58a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77589903"
---
# <a name="deprecation-of-azure-service-manager-support-for-azure-service-bus-relay-and-event-hubs"></a>Suporte ao Azure Service Manager para ônibus de serviço azure, relé e hubs de eventos

O Resource Manager, nossa pilha de infra-estrutura em nuvem de última geração, está substituindo totalmente o modelo "clássico" de Gerenciamento de Serviços do Azure (modelo clássico de implantação). Como resultado, o modelo clássico de implantação REST APIs e suporte para Service Bus, Relay e Event Hubs serão aposentados em 1 º de novembro de 2021. Essa depreciação foi anunciada pela primeira vez em um anúncio da [Microsoft Tech Community,](https://techcommunity.microsoft.com/t5/Service-Bus-blog/Deprecating-Service-Management-support-for-Azure-Service-Bus/ba-p/370909)mas recentemente decidimos estender o período de depreciação mais dois anos a partir do momento do anúncio original. Para facilitar a identificação, `management.core.windows.net` essas APIs têm em seu URI. Consulte a tabela a seguir para obter uma lista das APIs depreciadas e da versão aazure Resource Manager API que você deve usar agora.

Para continuar usando service bus, relay e event hubs, mude-se para Gerenciador de Recursos até 31 de outubro de 2021. Encorajamos todos os clientes que ainda estão usando APIs antigas a fazer a troca em breve para aproveitar os benefícios adicionais do Resource Manager, que incluem agrupamento de recursos, tags, um processo simplificado de implantação e gerenciamento e acesso com grãos finos controle usando o controle de acesso baseado em função (RBAC).

Para obter mais informações sobre o Azure Resource Manager vs Azure Service Manager, consulte o [TechNet Blog](https://blogs.technet.microsoft.com/meamcs/2016/12/22/difference-between-azure-service-manager-and-azure-resource-manager/).

Para obter mais informações sobre apIs do Service Manager e do Gerenciador de Recursos para os Hubs de Serviço saqueado, relay e event hubs do Azure, consulte nossa documentação da API REST:

- [Ônibus de serviço azure](/rest/api/servicebus/)
- [Hubs de eventos do Azure](/rest/api/eventhub/)
- [Retransmissão do Azure](/rest/api/relay/)

## <a name="service-manager-rest-api---resource-manager-rest-api"></a>Gerente de Serviços REST API - Gerenciador de recursos REST API

| APIs do Service Manager (Preteridas) | Gerente de Recursos - API de Ônibus de Serviço | Gerenciador de recursos - API do Hub de Eventos | Gerenciador de recursos - API de relé |
| --------------- | ----------------- | ----------------- | ----------------- | 
| **Namespaces-GetNamespaceAsync** <br/>[Ônibus de serviço obter namespace](/rest/api/servicebus/get-namespace)<br/>[Hub de eventos obter namespace](/rest/api/eventhub/get-event-hub)<br/>[Relé Obter Namespace](/rest/api/servicebus/get-relays)<br/> ```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [get](/rest/api/servicebus/namespaces/get) | [get](/rest/api/eventhub/namespaces/get) | [get](/rest/api/relay/namespaces/get) |
| **Detalhes de conexão-obter detalhes de conexão**<br/>Ônibus de serviço/hub de eventos/relé getconnectionDetals<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/ConnectionDetails``` | [listkeys](/rest/api/servicebus/namespaces/listkeys) | [listkeys](/rest/api/eventhub/namespaces/listkeys) | [listkeys](/rest/api/relay/namespaces/listkeys) |
| **Tópicos-GetTopicsAsync**<br/>Barramento de Serviço<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/topics? $skip={skip}&$top={top}``` | [Lista](/rest/api/servicebus/topics/listbynamespace) | &nbsp; | &nbsp; | 
| **Filas-GetQueueAsync** <br/>Barramento de Serviço<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/queues/{queueName}``` | [get](/rest/api/servicebus/queues/get) | &nbsp; | &nbsp; | 
| **Relés-GetRelaysAsync**<br/>[Obter relés](/rest/api/servicebus/get-relays)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/relays? $skip={skip}&$top={top}```| &nbsp; | &nbsp; | [Lista](/rest/api/relay/wcfrelays/listbynamespace) | 
| **NamespaceAutorizaçãoRegras-GetNameSpaceAutorizaçãoDeregraDeso**<br/>Ônibus de serviço/Hub de eventos/Relé GetNamespaceAuthRule<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/authorizationrules?``` | [obterregra de autorização](/rest/api/servicebus/namespaces/getauthorizationrule) |[obterregra de autorização](/rest/api/eventhub/namespaces/getauthorizationrule) | [obterregra de autorização](/rest/api/relay/namespaces/getauthorizationrule) |
| **Namespaces-DeleteNamespaceAsync**<br/>[Espaço de nome para exclusão de barramento de serviço](/rest/api/servicebus/delete-namespace)<br/>[Hubs de eventos excluem namespace](/rest/api/eventhub/delete-event-hub)<br/>[Relés Excluem namespace](/rest/api/servicebus/delete-namespace)<br/> ```DELETE  https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [Excluir](/rest/api/servicebus/namespaces/delete) | [Excluir](/rest/api/eventhub/namespaces/delete) | [Excluir](/rest/api/relay/namespaces/delete) | 
| **MensagensSKUPlan-GetPlanAsync**<br/>Ônibus de serviço/Hub de eventos/Relé obter namespace<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/MessagingPlan``` | [get](/rest/api/servicebus/namespaces/get) | [get](/rest/api/eventhub/namespaces/get) | [get](/rest/api/relay/namespaces/get) |
| **MensagensSKUPlan-UpdatePlanAsync**<br/>Ônibus de serviço/Hub de eventos/Relé obter namespace<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/MessagingPlan``` | [createorupdate](/rest/api/servicebus/namespaces/createorupdate) | [createorupdate](/rest/api/eventhub/namespaces/createorupdate) | [createorupdate](/rest/api/relay/namespaces/createorupdate) |
| **NamespaceAutorizaçãoRegras-AtualizaçãoNome-EspaçoCarteirade regrasRegraDescção**<br/>Ônibus de serviço/Hub de eventos/Relé obter namespace<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules/{rule name}``` | [createorupdate](/rest/api/servicebus/namespaces/createorupdate) | [criarouatualizarregra de autorização](/rest/api/eventhub/namespaces/createorupdateauthorizationrule) | [criarouatualizarregra de autorização](/rest/api/relay/namespaces/createorupdateauthorizationrule) | 
| **NamespaceAutorizaçãoRegras-CriarCriarNomeSpaceAutorizaçãoRegraDesoSoSincrono**<br/> 
Ônibus de serviço/Hub de eventos/relé<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules/{rule name}``` |[createorupdate](/rest/api/servicebus/namespaces/createorupdate) | [criarouatualizarregra de autorização](/rest/api/eventhub/namespaces/createorupdateauthorizationrule) | [criarouatualizarregra de autorização](/rest/api/relay/namespaces/createorupdateauthorizationrule) |
| **NamespacePropriedades-GetNamespacePropriedadesSAsync**<br/>[Ônibus de serviço obter namespace](/rest/api/servicebus/get-namespace)<br/>[Hubs de eventos recebem namespace](/rest/api/eventhub/get-event-hub)<br/>[Relé Obter Namespace](/rest/api/servicebus/get-relays)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [get](/rest/api/servicebus/namespaces/get) | [get](/rest/api/eventhub/namespaces/get) | [get](/rest/api/relay/namespaces/get) |
| **RegionCodes-GetRegionCodesAsync**<br/>Ônibus de serviço/EventHub/Relay Obter namespace<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [listbysku](/rest/api/servicebus/regions/listbysku) | [listbysku](/rest/api/eventhub/regions/listbysku) | &nbsp; | 
| **NamespacePropriedades-AtualizaçãoNomespacePropriedadePropriedade**<br/>Ônibus de serviço/EventHub/Relay<br/>```GET    https://management.core.windows.net/{subscription ID}/services/ServiceBus/Regions/``` | [createorupdate](/rest/api/servicebus/namespaces/createorupdate) | [createorupdate](/rest/api/eventhub/namespaces/createorupdate) | [createorupdate](/rest/api/relay/namespaces/createorupdate) |
| **EventHubsCrud-ListEventHubsAsync**<br/>[Listar hubs de eventos](/rest/api/eventhub/list-event-hubs)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/eventhubs?$skip={skip}&$top={top}``` | &nbsp; | [Lista](/rest/api/eventhub/eventhubs/listbynamespace) | &nbsp; | 
| **EventHubsCrud-GetEventHubAsync**<br/>[Obtenha hubs de eventos](/rest/api/eventhub/get-event-hub)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/eventhubs/{eventHubPath}``` | &nbsp; | [get](/rest/api/eventhub/eventhubs/get) | &nbsp; | 
| **NamespaceAutorizaçãoRegras-ExclusãoNameSpaceRuleAsync**<br/>Ônibus de serviço/Hub de eventos/relé<br/>```DELETE https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules/{rule name}``` | [excluirregra de autorização](/rest/api/servicebus/namespaces/deleteauthorizationrule) | [excluirregra de autorização](/rest/api/eventhub/namespaces/deleteauthorizationrule) | [excluirregra de autorização](/rest/api/relay/namespaces/deleteauthorizationrule) |
| **NamespaceAutorizaçãoRegras-GetNameSpaceAutorizaçãoDeautorizaçãoRegrasAsync**<br/>Ônibus de serviço/EventHub/Relay<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules``` | [regras de autorização de listas](/rest/api/servicebus/namespaces/listauthorizationrules) | [regras de autorização de listas](/rest/api/eventhub/namespaces/listauthorizationrules) | [regras de autorização de listas](/rest/api/relay/namespaces/listauthorizationrules) |
| **NamespaceDisponibilidade-IsNamespaceDisponível**<br/>[Disponibilidade de namespace do ônibus de serviço](/rest/api/servicebus/check-namespace-availability)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/CheckNamespaceAvailability/?namespace=<namespaceValue>``` | [checknameavailability](/rest/api/servicebus/namespaces/checknameavailability) | [checknameavailability](/rest/api/eventhub/namespaces/checknameavailability) | [checknameavailability](/rest/api/relay/namespaces/checknameavailability) |
| **Namespaces-CreateOrUpdateNamespaceAsync**<br/>Ônibus de serviço/Hub de eventos/relé<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [createorupdate](/rest/api/servicebus/namespaces/createorupdate) | [createorupdate](/rest/api/eventhub/namespaces/createorupdate) | [createorupdate](/rest/api/relay/namespaces/createorupdate) | 
| **Tópicos-GetTopicAsync**<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/topics/{topicPath}``` | [get](/rest/api/servicebus/topics/get) | &nbsp; | &nbsp; |

## <a name="service-manager-powershell---resource-manager-powershell"></a>Gerenciador de serviços PowerShell - Gerenciador de recursos PowerShell
| Comando PowerShell do gerenciador de serviços (preterido) | Novos comandos do gerenciador de recursos | Comando de gerenciador de recursos mais novo |
| ----- | ----- | ----- | 
| [Get-AzureSBAuthorizationRule](/powershell/module/servicemanagement/azure/get-azuresbauthorizationrule?view=azuresmps-4.0.0) | [Get-AzureRmServiceBusAuthorizationRule](/powershell/module/azurerm.servicebus/get-azurermservicebusauthorizationrule?view=azurermps-6.13.0) | [Regra de autorização do Get-AzServiceBus](/powershell/module/az.servicebus/get-azservicebusauthorizationrule?view=azps-1.6.0) |
| [Get-AzureSBLocation](/powershell/module/servicemanagement/azure/get-azuresblocation?view=azuresmps-4.0.0) | [Configuração get-AzureRmServiceBusGeoDR](/powershell/module/azurerm.servicebus/get-azurermservicebusgeodrconfiguration?view=azurermps-6.13.0) | [Configuração get-AzServiceBusGeoDR](/powershell/module/az.servicebus/get-azservicebusgeodrconfiguration?view=azps-1.6.0) |
| [Get-AzureSBNamespace](/powershell/module/servicemanagement/azure/get-azuresbnamespace?view=azuresmps-4.0.0) | [Get-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/get-azurermservicebusnamespace?view=azurermps-6.13.0) | [Get-AzServiceBusNamespace](/powershell/module/az.servicebus/get-azservicebusnamespace?view=azps-1.6.0) |
| [New-AzureSBAuthorizationRule](/powershell/module/servicemanagement/azure/new-azuresbauthorizationrule?view=azuresmps-4.0.0) | [New-AzureRmServiceBusAuthorizationRule](/powershell/module/azurerm.servicebus/new-azurermservicebusauthorizationrule?view=azurermps-6.13.0) | [New-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/new-azservicebusauthorizationrule?view=azps-1.6.0) |
| [New-AzureSBNamespace](/powershell/module/servicemanagement/azure/new-azuresbnamespace?view=azuresmps-4.0.0) | [New-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/new-azurermservicebusnamespace?view=azurermps-6.13.0) | [New-AzServiceBusNamespace](/powershell/module/az.servicebus/new-azservicebusnamespace?view=azps-1.6.0) |
| [Regra de autorização do Remove-AzureRmRelay](/powershell/module/azurerm.relay/remove-azurermrelayauthorizationrule?view=azurermps-6.13.0) | [Remove-AzureRmEventHubAuthorizationRule](/powershell/module/azurerm.eventhub/remove-azurermeventhubauthorizationrule?view=azurermps-6.13.0) | [Regra de autorização do Remove-AzServiceBus](/powershell/module/az.servicebus/remove-azservicebusauthorizationrule?view=azps-1.6.0) |
| [Remove-AzureSBNamespace](/powershell/module/servicemanagement/azure/remove-azuresbnamespace?view=azuresmps-4.0.0) | [Remove-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/remove-azurermservicebusnamespace?view=azurermps-6.13.0) | [Remove-AzServiceBusNamespace](/powershell/module/az.servicebus/remove-azservicebusnamespace?view=azps-1.6.0) |
| [Set-AzureSBAuthorizationRule](/powershell/module/servicemanagement/azure/set-azuresbauthorizationrule?view=azuresmps-4.0.0) | [Set-AzureRmServiceBusAuthorizationRule](/powershell/module/azurerm.servicebus/set-azurermservicebusauthorizationrule?view=azurermps-6.13.0) | [Regra de autorização do Set-AzServiceBus](/powershell/module/az.servicebus/set-azservicebusauthorizationrule?view=azps-1.6.0) |

## <a name="next-steps"></a>Próximas etapas
Consulte a seguinte documentação: 

- Última documentação da API REST
    - [Ônibus de serviço azure](/rest/api/servicebus/)
    - [Hubs de eventos do Azure](/rest/api/eventhub/)
    - [Retransmissão do Azure](/rest/api/relay/)
- Documentação mais recente do PowerShell
    - [Ônibus de serviço azure](/powershell/module/azurerm.servicebus/?view=azurermps-6.13.0#service_bus)
    - [Hubs de eventos do Azure](/powershell/module/azurerm.eventhub/?view=azurermps-6.13.0#event_hub)
    - [Grade de Eventos Azure](/powershell/module/azurerm.eventgrid/?view=azurermps-6.13.0#event_grid)
