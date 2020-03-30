---
title: Vinculações da Grade de Eventos do Azure para funções do Azure
description: Entenda como manipular a Grade de Eventos no Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: 21654a3b325e8b8f0a3e49ee64b7624c8540d0d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461072"
---
# <a name="azure-event-grid-bindings-for-azure-functions"></a>Vinculações da Grade de Eventos do Azure para funções do Azure

Esta referência explica como lidar com eventos [da Event Grid](../event-grid/overview.md) em Funções Azure. Para obter detalhes sobre como lidar com as mensagens do Event Grid em um ponto final HTTP, consulte [Receber eventos em um ponto final HTTP](../event-grid/receive-events.md).

A Grade de Eventos é um serviço do Azure que envia solicitações HTTP para notificá-lo sobre eventos que acontecem nos *publicadores*. Um publicador é o serviço ou recurso que origina o evento. Por exemplo, uma conta de armazenamento de Blobs do Azure é um publicador, e [uma exclusão ou upload de blob é um evento](../storage/blobs/storage-blob-event-overview.md). Alguns [serviços do Azure têm suporte interno para publicar eventos na Grade de Eventos](../event-grid/overview.md#event-sources).

Os *manipuladores* de eventos recebem e processam eventos. O Azure Functions é um dos vários serviços do[Azure que possuem suporte interno para manipular eventos da Grande de Eventos](../event-grid/overview.md#event-handlers). Nesta referência, você aprende a usar um gatilho event grid para invocar uma função quando um evento é recebido da Event Grid e usar a vinculação de saída para enviar eventos para um [tópico personalizado da Event Grid](../event-grid/post-to-custom-topic.md).

Se preferir, você pode usar um gatilho HTTP para lidar com eventos da grade de eventos; veja [Receber eventos para um ponto final HTTP](../event-grid/receive-events.md). Atualmente, você não pode usar um gatilho do Event Grid para um aplicativo Azure Functions quando o evento é entregue no [esquema CloudEvents](../event-grid/cloudevents-schema.md#azure-functions). Em vez disso, use um gatilho HTTP.

| Ação | Type |
|---------|---------|
| Execute uma função quando um evento da Event Grid é despachado | [Gatilho](./functions-bindings-event-grid-trigger.md) |
| Envia um evento da Grade de Eventos |[Vinculação de saída](./functions-bindings-event-grid-output.md) |

O código nesta referência é padrão para a sintaxe .NET Core, usada na versão Funções 2.x e superior. Para obter informações sobre a sintaxe 1.x, consulte os [modelos de funções 1.x](https://github.com/Azure/azure-functions-templates/tree/v1.x/Functions.Templates/Templates).

## <a name="add-to-your-functions-app"></a>Adicione ao seu aplicativo Functions

### <a name="functions-2x-and-higher"></a>Funções 2.x e superior

Trabalhar com o gatilho e as amarras requer que você faça referência ao pacote apropriado. O pacote NuGet é usado para bibliotecas de classe .NET, enquanto o pacote de extensão é usado para todos os outros tipos de aplicativos.

| Idioma                                        | Adicione por...                                   | Comentários 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Instalando o [pacote NuGet,]versão 3.x | |
| C# Script, Java, JavaScript, Python, PowerShell | Registrando o [pacote de extensão]          | [Recomenda-se que a extensão Azure Tools](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) use com o Visual Studio Code. |
| C# Script (somente on-line no portal Azure)         | Adicionando uma vinculação                            | Para atualizar as extensões de vinculação existentes sem ter que republicar seu aplicativo de função, consulte [Atualizar suas extensões]. |

[core tools]: ./functions-run-local.md
[pacote de extensão]: ./functions-bindings-register.md#extension-bundles
[Pacote NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid
[Atualize suas extensões]: ./install-update-binding-extensions-manual.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Funções 1.x

Os aplicativos Functions 1.x têm automaticamente uma referência ao pacote [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet, versão 2.x.

## <a name="next-steps"></a>Próximas etapas
* [Execute uma função quando um evento da Event Grid é despachado](./functions-bindings-event-grid-trigger.md)
* [Despachar um evento da Grade de Eventos](./functions-bindings-event-grid-trigger.md)
