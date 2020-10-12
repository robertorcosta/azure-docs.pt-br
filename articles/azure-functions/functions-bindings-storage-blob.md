---
title: Gatilhos e associações do armazenamento de BLOBs do Azure para Azure Functions
description: Aprenda a usar o gatilho e as associações do armazenamento de BLOBs do Azure no Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: eead2d6ae2860bf06f1b878c1bb1692d74c06df6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87055975"
---
# <a name="azure-blob-storage-bindings-for-azure-functions-overview"></a>Associações de armazenamento de BLOBs do Azure para Azure Functions visão geral

O Azure Functions integra-se com o [armazenamento do Azure](../storage/index.yml) por meio [de gatilhos e associações](./functions-triggers-bindings.md). A integração com o armazenamento de BLOBs permite que você crie funções que reajam a alterações em dados de BLOB, bem como valores de leitura e gravação.

| Ação | Type |
|---------|---------|
| Executar uma função como alterações de dados do armazenamento de BLOBs | [Gatilho](./functions-bindings-storage-blob-trigger.md) |
| Ler dados do armazenamento de BLOBs em uma função | [Associação de entrada](./functions-bindings-storage-blob-input.md) |
| Permitir que uma função grave dados de armazenamento de BLOBs |[Associação de saída](./functions-bindings-storage-blob-output.md) |

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

- [Executar uma função quando os dados do armazenamento de BLOBs forem alterados](./functions-bindings-storage-blob-trigger.md)
- [Ler dados do armazenamento de BLOBs quando uma função é executada](./functions-bindings-storage-blob-input.md)
- [Gravar dados de armazenamento de blobs de uma função](./functions-bindings-storage-blob-output.md)
