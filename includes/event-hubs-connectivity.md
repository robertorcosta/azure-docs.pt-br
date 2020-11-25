---
title: incluir arquivo
description: incluir arquivo
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 11/19/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: dac82692c76d9d36b1f25d7b93b5c3a2e2400672
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002777"
---
### <a name="what-ports-do-i-need-to-open-on-the-firewall"></a>Quais portas preciso abrir no firewall? 
Você pode usar os seguintes protocolos com os hubs de eventos do Azure para enviar e receber eventos:

- Advanced Message Queuing Protocol 1,0 (AMQP)
- Protocolo de transferência de hipertexto 1,1 com TLS (HTTPS)
- Apache Kafka

Consulte a tabela a seguir para as portas de saída que você precisa abrir para usar esses protocolos para se comunicar com os Hubs de Eventos do Azure. 

| Protocolo | Portas | Detalhes | 
| -------- | ----- | ------- | 
| AMQP | 5671 e 5672 | Consulte [Guia do protocolo AMQP](../articles/service-bus-messaging/service-bus-amqp-protocol-guide.md) | 
| HTTPS | 443 | Essa porta é usada para a API HTTP/REST e para AMQP-over-WebSockets. |
| Kafka | 9093 | Consulte [Usar Hubs de Eventos de aplicativos Kafka](../articles/event-hubs/event-hubs-for-kafka-ecosystem-overview.md)

A porta HTTPS é necessária para a comunicação de saída também quando AMQP é usado pela porta 5671, porque várias operações de gerenciamento executadas pelos SDKs do cliente e pela aquisição de tokens de Azure Active Directory (quando usado) são executadas por HTTPS. 

Os SDKs oficiais do Azure geralmente usam o protocolo AMQP para enviar e receber eventos dos hubs de eventos. A opção de protocolo AMQP-over-WebSockets é executada pela porta TCP 443 assim como a API HTTP, mas, de outra forma, funcionalmente idêntica com AMQP simples. Essa opção tem uma latência de conexão inicial mais alta devido a viagens de ida e volta extras de handshake e um pouco mais de sobrecarga como uma compensação para compartilhar a porta HTTPS. Se esse modo estiver selecionado, a porta TCP 443 será suficiente para comunicação. As opções a seguir permitem selecionar o modo de WebSockets AMQP ou AMQP simples:

| Idioma | Opção   |
| -------- | ----- |
| .NET     | Propriedade [EventHubConnectionOptions. TransportType](/dotnet/api/azure.messaging.eventhubs.eventhubconnectionoptions.transporttype?view=azure-dotnet&preserve-view=true) com [EventHubsTransportType. AmqpTcp](/dotnet/api/azure.messaging.eventhubs.eventhubstransporttype?view=azure-dotnet&preserve-view=true) ou [EventHubsTransportType. AmqpWebSockets](/dotnet/api/azure.messaging.eventhubs.eventhubstransporttype?view=azure-dotnet&preserve-view=true) |
| Java     | [com. Microsoft. Azure. Eventhubs. EventProcessorClientBuilder. TransportType](/java/api/com.azure.messaging.eventhubs.eventprocessorclientbuilder.transporttype?view=azure-java-stable&preserve-view=true) com [AmqpTransportType. AMQP](/java/api/com.azure.core.amqp.amqptransporttype?view=azure-java-stable&preserve-view=true) ou [AmqpTransportType.AMQP_WEB_SOCKETS](/java/api/com.azure.core.amqp.amqptransporttype?view=azure-java-stable&preserve-view=true) |
| Nó  | [EventHubConsumerClientOptions](/javascript/api/@azure/event-hubs/eventhubconsumerclientoptions?view=azure-node-latest&preserve-view=true) tem uma `webSocketOptions` propriedade. |
| Python | [EventHubConsumerClient.transport_type](/python/api/azure-eventhub/azure.eventhub.eventhubconsumerclient?view=azure-python&preserve-view=true) com [TransportType. AMQP](/python/api/azure-eventhub/azure.eventhub.transporttype?view=azure-python) ou [TransportType. AmqpOverWebSocket](/python/api/azure-eventhub/azure.eventhub.transporttype?view=azure-python&preserve-view=true) |

### <a name="what-ip-addresses-do-i-need-to-allow"></a>Quais endereços IP preciso permitir?
Quando você estiver trabalhando com o Azure, às vezes terá que permitir intervalos de endereços IP específicos ou URLs em seu firewall corporativo ou proxy para acessar todos os serviços do Azure que você está usando ou tentando usar. Verifique se o tráfego é permitido em endereços IP usados pelos hubs de eventos. Para endereços IP usados pelos hubs de eventos do Azure: consulte [intervalos de IP e marcas de serviço do Azure – nuvem pública](https://www.microsoft.com/download/details.aspx?id=56519).

Além disso, verifique se o endereço IP do seu namespace é permitido. Para localizar os endereços IP corretos para permitir suas conexões, siga estas etapas:

1. Execute o seguinte comando de um prompt de comando: 

    ```
    nslookup <YourNamespaceName>.servicebus.windows.net
    ```
2. Anote o endereço IP retornado em `Non-authoritative answer`. 

Se você usar a **redundância de zona** para seu namespace, precisará executar algumas etapas adicionais: 

1. Primeiro, execute nslookup no namespace.

    ```
    nslookup <yournamespace>.servicebus.windows.net
    ```
2. Anote o nome na seção **resposta não autoritativa**, que está em um dos seguintes formatos: 

    ```
    <name>-s1.cloudapp.net
    <name>-s2.cloudapp.net
    <name>-s3.cloudapp.net
    ```
3. Execute nslookup para cada um com sufixos s1, s2 e s3 para obter os endereços IP de todas as três instâncias em execução em três zonas de disponibilidade, 

    > [!NOTE]
    > O endereço IP retornado pelo `nslookup` comando não é um endereço IP estático. No entanto, ela permanece constante até que a implantação subjacente seja excluída ou movida para um cluster diferente.

### <a name="where-can-i-find-client-ip-sending-or-receiving-messages-to-my-namespace"></a>Onde posso encontrar o IP do cliente enviando ou recebendo mensagens para o namespace?
Primeiro, habilite a [filtragem de IP](../articles/event-hubs/event-hubs-ip-filtering.md) no namespace. 

Em seguida, habilite os logs de diagnóstico para [eventos de conexão de rede virtual dos hubs de eventos](../articles/event-hubs/event-hubs-diagnostic-logs.md#event-hubs-virtual-network-connection-event-schema) seguindo as instruções em [Habilitar logs de diagnóstico](../articles/event-hubs/event-hubs-diagnostic-logs.md#enable-diagnostic-logs). Você verá o endereço IP para o qual a conexão é negada.

```json
{
    "SubscriptionId": "0000000-0000-0000-0000-000000000000",
    "NamespaceName": "namespace-name",
    "IPAddress": "1.2.3.4",
    "Action": "Deny Connection",
    "Reason": "IPAddress doesn't belong to a subnet with Service Endpoint enabled.",
    "Count": "65",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name",
    "Category": "EventHubVNetConnectionEvent"
}
```

> [!IMPORTANT]
> Os logs de rede virtual serão gerados somente se o namespace permitir o acesso de **endereços IP específicos** (regras de filtro IP). Se você não quiser restringir o acesso ao seu namespace usando esses recursos e ainda quiser obter logs de rede virtual para rastrear endereços IP de clientes que se conectam ao namespace de hubs de eventos, você pode usar a seguinte solução alternativa: habilitar a filtragem de IP e adicionar o intervalo de IPv4 endereçável total (1.0.0.0/1-255.0.0.0/1). Os hubs de eventos não dão suporte a intervalos de endereços IPv6. 
