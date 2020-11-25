---
title: incluir arquivo
description: incluir arquivo
services: service-bus-messaging
author: clemensv
ms.service: service-bus-messaging
ms.topic: include
ms.date: 11/24/2020
ms.author: clemensv
ms.custom: include file
ms.openlocfilehash: fe205524c5aef6a43771cc09c810da217678d108
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96022126"
---
A opção de protocolo AMQP-over-WebSockets é executada pela porta TCP 443 assim como a API HTTP/REST, mas, de outra forma, funcionalmente idêntica com AMQP simples. Essa opção tem uma latência de conexão inicial um pouco maior devido a viagens de ida e volta extra de handshake e um pouco mais de sobrecarga como a compensação para compartilhar a porta HTTPS. Se esse modo estiver selecionado, a porta TCP 443 será suficiente para comunicação. As opções a seguir permitem selecionar o modo de WebSockets AMQP ou AMQP simples:

| Idioma | Opção   |
| -------- | ----- |
| .NET     | Propriedade [ServiceBusConnection. TransportType](/dotnet/api/microsoft.azure.servicebus.servicebusconnection.transporttype?view=azure-dotnet) com [TransportType. AMQP](/dotnet/api/microsoft.azure.servicebus.transporttype?view=azure-dotnet) ou [TransportType. AmqpWebSockets](/dotnet/api/microsoft.azure.servicebus.transporttype?view=azure-dotnet) |
| Java     | [com. Microsoft. Azure. ServiceBus. ClientSettings](/java/api/com.microsoft.azure.servicebus.clientsettings.clientsettings?view=azure-java-stable) com com [. Microsoft. Azure. ServiceBus. Primitives. ExportType. AMQP](/java/api/com.microsoft.azure.servicebus.primitives.transporttype?view=azure-java-stable) ou [com.Microsoft.Azure.ServiceBus.Primitives.TransportType.AMQP_WEB_SOCKETS](/java/api/com.microsoft.azure.servicebus.primitives.transporttype?view=azure-java-stable) |
| Nó  | [ServiceBusClientOptions](/javascript/api/@azure/service-bus/servicebusclientoptions?view=azure-node-latest) tem um `webSocket` argumento de construtor. |
| Python | [ServiceBusClient.transport_type](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-servicebus/latest/azure.servicebus.html#azure.servicebus.ServiceBusClient) com [TransportType. AMQP](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-servicebus/latest/azure.servicebus.html#azure.servicebus.TransportType) ou [TransportType. AmqpOverWebSocket](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-servicebus/latest/azure.servicebus.html#azure.servicebus.TransportType) |