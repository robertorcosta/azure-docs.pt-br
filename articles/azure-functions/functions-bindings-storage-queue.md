---
title: Gatilhos e associações do armazenamento de filas do Azure para Azure Functions visão geral
description: Entenda como usar o gatilho do armazenamento de fila do Azure e a associação de saída no Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 71321fa631bafde5b1dbaac2d165cb68fd8116d4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87055802"
---
# <a name="azure-queue-storage-trigger-and-bindings-for-azure-functions-overview"></a>Gatilhos e associações do armazenamento de filas do Azure para Azure Functions visão geral

Azure Functions pode executar como novas mensagens de armazenamento de fila do Azure são criadas e podem gravar mensagens de fila em uma função.

| Ação | Type |
|---------|---------|
| Executar uma função como armazenamento de filas alterações de dados | [Gatilho](./functions-bindings-storage-queue-trigger.md) |
| Gravar mensagens de armazenamento de fila |[Associação de saída](./functions-bindings-storage-queue-output.md) |

## <a name="add-to-your-functions-app"></a>Adicionar ao seu aplicativo de funções

### <a name="functions-2x-and-higher"></a>Funções 2.x e posteriores

Trabalhar com o gatilho e as associações exige que você referencie o pacote apropriado. O pacote NuGet é usado para bibliotecas de classes do .NET enquanto o pacote de extensão é usado para todos os outros tipos de aplicativos.

| Linguagem                                        | Adicionar por...                                   | Comentários 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Instalando o [pacote NuGet], versão 3. x | |
| Script C#, Java, JavaScript, Python, PowerShell | Registrando o [pacote de extensão]          | A [extensão de ferramentas do Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) é recomendada para uso com Visual Studio Code. |
| Script C# (somente online em portal do Azure)         | Adicionando uma associação                            | Para atualizar as extensões de associação existentes sem precisar republicar seu aplicativo de funções, consulte [atualizar suas extensões]. |

[core tools]: ./functions-run-local.md
[pacote de extensão]: ./functions-bindings-register.md#extension-bundles
[Pacote NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage
[Atualizar suas extensões]: ./install-update-binding-extensions-manual.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Funções 1.x

Os aplicativos do Functions 1. x têm automaticamente uma referência ao pacote NuGet [Microsoft. Azure. webjobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) , versão 2. x.

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="next-steps"></a>Próximas etapas

- [Executar uma função como armazenamento de fila alterações de dados (gatilho)](./functions-bindings-storage-queue-trigger.md)
- [Gravar mensagens de armazenamento de fila (Associação de saída)](./functions-bindings-storage-queue-output.md)
