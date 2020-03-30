---
title: Associações do Barramento de Serviço para o Azure Functions
description: Aprenda a enviar gatilhos e vinculações do Azure Service Bus em Funções Azure.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.openlocfilehash: 44e4114b328701d5de363e91f5562f1daad351c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277408"
---
# <a name="azure-service-bus-bindings-for-azure-functions"></a>Associações do Barramento de Serviço para o Azure Functions

As funções do Azure [integram-se ao Azure Service Bus](https://azure.microsoft.com/services/service-bus) através [de gatilhos e vinculações](./functions-triggers-bindings.md). A integração com o Service Bus permite que você crie funções que reajam e enviem mensagens de fila ou tópico.

| Ação | Type |
|---------|---------|
| Execute uma função quando uma fila de barra de serviço ou mensagem de tópico for criada | [Gatilho](./functions-bindings-service-bus-trigger.md) |
| Envie mensagens de ônibus de serviço do Azure |[Vinculação de saída](./functions-bindings-service-bus-output.md) |

## <a name="add-to-your-functions-app"></a>Adicione ao seu aplicativo Functions

### <a name="functions-2x-and-higher"></a>Funções 2.x e superior

Trabalhar com o gatilho e as amarras requer que você faça referência ao pacote apropriado. O pacote NuGet é usado para bibliotecas de classe .NET, enquanto o pacote de extensão é usado para todos os outros tipos de aplicativos.

| Idioma                                        | Adicione por...                                   | Comentários 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Instalando o [pacote NuGet,]versão 4.x | |
| C# Script, Java, JavaScript, Python, PowerShell | Registrando o [pacote de extensão]          | [Recomenda-se que a extensão Azure Tools] use com o Visual Studio Code. |
| C# Script (somente on-line no portal Azure)         | Adicionando uma vinculação                            | Para atualizar as extensões de vinculação existentes sem ter que republicar seu aplicativo de função, consulte [Atualizar suas extensões]. |

[Pacote NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus/
[core tools]: ./functions-run-local.md
[pacote de extensão]: ./functions-bindings-register.md#extension-bundles
[Atualize suas extensões]: ./install-update-binding-extensions-manual.md
[Extensão do Azure Tools]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Funções 1.x

Os aplicativos Functions 1.x têm automaticamente uma referência ao pacote [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet, versão 2.x.

## <a name="next-steps"></a>Próximas etapas

- [Execute uma função quando uma fila de barra de serviço ou mensagem de tópico for criada (Trigger)](./functions-bindings-service-bus-trigger.md)
- [Envie mensagens de barramento de serviço do Azure das funções do Azure (vinculação de saída)](./functions-bindings-service-bus-output.md)
