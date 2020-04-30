---
title: Associações do Barramento de Serviço para o Azure Functions
description: Saiba como enviar gatilhos e associações do barramento de serviço do Azure no Azure Functions.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.openlocfilehash: 44e4114b328701d5de363e91f5562f1daad351c6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79277408"
---
# <a name="azure-service-bus-bindings-for-azure-functions"></a>Associações do Barramento de Serviço para o Azure Functions

O Azure Functions integra-se com o [barramento de serviço do Azure](https://azure.microsoft.com/services/service-bus) por meio [de gatilhos e associações](./functions-triggers-bindings.md). A integração com o barramento de serviço permite que você crie funções que reajam e enviem mensagens de tópico ou de fila.

| Ação | Type |
|---------|---------|
| Executar uma função quando uma fila ou mensagem de tópico do barramento de serviço for criada | [Gatilho](./functions-bindings-service-bus-trigger.md) |
| Enviar mensagens do barramento de serviço do Azure |[Associação de saída](./functions-bindings-service-bus-output.md) |

## <a name="add-to-your-functions-app"></a>Adicionar ao seu aplicativo de funções

### <a name="functions-2x-and-higher"></a>Funções 2. x e posteriores

Trabalhar com o gatilho e as associações exige que você referencie o pacote apropriado. O pacote NuGet é usado para bibliotecas de classes do .NET enquanto o pacote de extensão é usado para todos os outros tipos de aplicativos.

| Linguagem                                        | Adicionar por...                                   | Comentários 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Instalando o [pacote NuGet], versão 4. x | |
| Script C#, Java, JavaScript, Python, PowerShell | Registrando o [pacote de extensão]          | A [extensão de ferramentas do Azure] é recomendada para uso com Visual Studio Code. |
| Script C# (somente online em portal do Azure)         | Adicionando uma associação                            | Para atualizar as extensões de associação existentes sem precisar republicar seu aplicativo de funções, consulte [atualizar suas extensões]. |

[Pacote NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus/
[core tools]: ./functions-run-local.md
[pacote de extensão]: ./functions-bindings-register.md#extension-bundles
[Atualizar suas extensões]: ./install-update-binding-extensions-manual.md
[Extensão de ferramentas do Azure]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Funções 1.x

Os aplicativos do Functions 1. x têm automaticamente uma referência ao pacote NuGet [Microsoft. Azure. webjobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) , versão 2. x.

## <a name="next-steps"></a>Próximas etapas

- [Executar uma função quando uma fila ou mensagem de tópico do barramento de serviço é criada (gatilho)](./functions-bindings-service-bus-trigger.md)
- [Enviar mensagens do barramento de serviço do Azure de Azure Functions (Associação de saída)](./functions-bindings-service-bus-output.md)
