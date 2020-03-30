---
title: Associações do Azure Cosmos DB para Functions 2.x
description: Entenda como usar gatilhos e associações do Azure Cosmos DB no Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2017
ms.author: cshoe
ms.openlocfilehash: f258a7aff52796a53540706bc8413575d63c9e7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77605763"
---
# <a name="azure-cosmos-db-trigger-and-bindings-for-azure-functions-2x-overview"></a>Azure Cosmos DB gatilho e vinculações para a visão geral das funções do Azure 2.x

> [!div class="op_single_selector" title1="Selecione a versão do tempo de execução das funções do Azure que você está usando: "]
> * [Versão 1](functions-bindings-cosmosdb.md)
> * [Versão 2](functions-bindings-cosmosdb-v2.md)

Este conjunto de artigos explica como trabalhar com as vinculações [do Azure Cosmos DB](../cosmos-db/serverless-computing-database.md) nas Funções Azure 2.x. O Azure Functions dá suporte a associações de gatilho, entrada e saída para o Azure Cosmos DB.

| Ação | Type |
|---------|---------|
| Execute uma função quando um documento Azure Cosmos DB for criado ou modificado | [Gatilho](./functions-bindings-cosmosdb-v2-trigger.md) |
| Leia um documento do Azure Cosmos DB | [Vinculação de entrada](./functions-bindings-cosmosdb-v2-input.md) |
| Salvar alterações em um documento DB do Azure Cosmos  |[Vinculação de saída](./functions-bindings-cosmosdb-v2-output.md) |

> [!NOTE]
> Esta referência é para [a versão 2.x do Azure Functions](functions-versions.md).  Para obter informações sobre como usar essas associações em Functions 1. x, consulte [Associações do Azure Cosmos DB para Azure Functions 1. x](functions-bindings-cosmosdb.md).
>
> Essa associação era originalmente denominada DocumentDB. No Functions versão 2.x, o gatilho, associações e pacote são chamados de Cosmos DB.

## <a name="supported-apis"></a>APIs com suporte

[!INCLUDE [SQL API support only](../../includes/functions-cosmosdb-sqlapi-note.md)]

## <a name="add-to-your-functions-app"></a>Adicione ao seu aplicativo Functions

### <a name="functions-2x-and-higher"></a>Funções 2.x e superior

Trabalhar com o gatilho e as amarras requer que você faça referência ao pacote apropriado. O pacote NuGet é usado para bibliotecas de classe .NET, enquanto o pacote de extensão é usado para todos os outros tipos de aplicativos.

| Idioma                                        | Adicione por...                                   | Comentários 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Instalando o [pacote NuGet,]versão 3.x | |
| C# Script, Java, JavaScript, Python, PowerShell | Registrando o [pacote de extensão]          | [Recomenda-se que a extensão Azure Tools] use com o Visual Studio Code. |
| C# Script (somente on-line no portal Azure)         | Adicionando uma vinculação                            | Para atualizar as extensões de vinculação existentes sem ter que republicar seu aplicativo de função, consulte [Atualizar suas extensões]. |

[Pacote NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB
[core tools]: ./functions-run-local.md
[pacote de extensão]: ./functions-bindings-register.md#extension-bundles
[Atualize suas extensões]: ./install-update-binding-extensions-manual.md
[Extensão do Azure Tools]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Funções 1.x

Os aplicativos Functions 1.x têm automaticamente uma referência ao pacote [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet, versão 2.x.

## <a name="next-steps"></a>Próximas etapas

- [Execute uma função quando um documento Azure Cosmos DB for criado ou modificado (Trigger)](./functions-bindings-cosmosdb-v2-trigger.md)
- [Leia um documento Azure Cosmos DB (vinculação de entrada)](./functions-bindings-cosmosdb-v2-input.md)
- [Salvar alterações em um documento DB do Azure Cosmos (vinculação de saída)](./functions-bindings-cosmosdb-v2-output.md)
