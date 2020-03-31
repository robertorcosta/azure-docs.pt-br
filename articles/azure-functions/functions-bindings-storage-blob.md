---
title: Gatilho de armazenamento Do Zure Blob e vinculações para funções do Azure
description: Aprenda a usar o gatilho de armazenamento Azure Blob e as amarras em Funções Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: 6950f1dd81ef2c70f2c45fb4c547ed7676067790
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277226"
---
# <a name="azure-blob-storage-bindings-for-azure-functions-overview"></a>Vinculações de armazenamento do Azure Blob para visão geral das funções do Azure

As funções do Azure [integram-se ao Armazenamento Azure](https://docs.microsoft.com/azure/storage/) através [de gatilhos e vinculações](./functions-triggers-bindings.md). A integração com o armazenamento Blob permite que você construa funções que reagem a alterações nos dados blob, bem como valores de leitura e gravação.

| Ação | Type |
|---------|---------|
| Execute uma função como alterações de dados de armazenamento blob | [Gatilho](./functions-bindings-storage-blob-trigger.md) |
| Leia os dados de armazenamento blob em uma função | [Vinculação de entrada](./functions-bindings-storage-blob-input.md) |
| Permitir uma função para gravar dados de armazenamento blob |[Vinculação de saída](./functions-bindings-storage-blob-output.md) |

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
[Pacote NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage
[Atualize suas extensões]: ./install-update-binding-extensions-manual.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Funções 1.x

Os aplicativos Functions 1.x têm automaticamente uma referência ao pacote [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet, versão 2.x.

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="next-steps"></a>Próximas etapas

- [Execute uma função quando os dados de armazenamento blob mudarem](./functions-bindings-storage-blob-trigger.md)
- [Leia os dados de armazenamento blob quando uma função é executada](./functions-bindings-storage-blob-input.md)
- [Escreva dados de armazenamento blob de uma função](./functions-bindings-storage-blob-output.md)
