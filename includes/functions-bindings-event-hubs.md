---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 47e9e37676a4afa9ec29393bf970c368f3f9e5be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586114"
---
## <a name="add-to-your-functions-app"></a>Adicione ao seu aplicativo Functions

### <a name="functions-2x-and-higher"></a>Funções 2.x e superior

Trabalhar com o gatilho e as amarras requer que você faça referência ao pacote apropriado. O pacote NuGet é usado para bibliotecas de classe .NET, enquanto o pacote de extensão é usado para todos os outros tipos de aplicativos.

| Idioma                                        | Adicione por...                                   | Comentários 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Instalando o [pacote NuGet,]versão 3.x | |
| C# Script, Java, JavaScript, Python, PowerShell | Registrando o [pacote de extensão]          | [Recomenda-se que a extensão Azure Tools] use com o Visual Studio Code. |
| C# Script (somente on-line no portal Azure)         | Adicionando uma vinculação                            | Para atualizar as extensões de vinculação existentes sem ter que republicar seu aplicativo de função, consulte [Atualizar suas extensões]. |

[Pacote NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs
[core tools]: ../articles/azure-functions/functions-run-local.md
[pacote de extensão]: ../articles/azure-functions/functions-bindings-register.md#extension-bundles
[Atualize suas extensões]: ../articles/azure-functions/install-update-binding-extensions-manual.md
[Extensão do Azure Tools]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Funções 1.x

Os aplicativos Functions 1.x têm automaticamente uma referência ao pacote [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet, versão 2.x.