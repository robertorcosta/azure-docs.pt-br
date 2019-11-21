---
title: Develop and run Azure functions locally
description: Saiba como codificar e testar o Azure Functions no computador local antes de executá-las no Azure Functions.
ms.topic: conceptual
ms.date: 09/04/2018
ms.openlocfilehash: 835edcb94b294d93cab41ea51b88ac38db71d95e
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230643"
---
# <a name="code-and-test-azure-functions-locally"></a>Codificar e testar o Azure Functions localmente

Enquanto você é capaz de desenvolver e testar o Azure Functions no [Azure portal], muitos desenvolvedores preferem uma experiência de desenvolvimento local. O Functions facilita a utilização do seu editor de códigos favorito e das ferramentas de desenvolvimento para criar e testar as funções em seu computador local. Suas funções locais podem se conectar a serviços do Azure em tempo real e você pode depurá-las em seu computador local usando o runtime total do Functions.

## <a name="local-development-environments"></a>Ambientes de desenvolvimento locais

A maneira como você desenvolve funções em seu computador local depende de seu [idioma](supported-languages.md) e preferências de ferramentas. Os ambientes na tabela a seguir dão suporte a desenvolvimento local:

|Ambiente                              |Languages         |Descrição|
|-----------------------------------------|------------|---|
|[Visual Studio Code](functions-develop-vs-code.md)| [C# (class library)](functions-dotnet-class-library.md), [C# script (.csx)](functions-reference-csharp.md), [JavaScript](functions-reference-node.md), [PowerShell](functions-create-first-function-powershell.md), [Python](functions-reference-python.md) | A [extensão do Azure Functions para VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) adiciona suporte ao Functions para o VS Code. Requer as Ferramentas Essenciais. Oferece suporte ao desenvolvimento em Linux, MacOS e Windows ao usar a versão 2.x das Ferramentas Essenciais. Para saber mais, confira [Criar sua primeira função usando o Visual Studio Code](functions-create-first-function-vs-code.md). |
| [Prompt de comando ou terminal](functions-run-local.md) | [C# (class library)](functions-dotnet-class-library.md), [C# script (.csx)](functions-reference-csharp.md), [JavaScript](functions-reference-node.md), [PowerShell](functions-reference-powershell.md), [Python](functions-reference-python.md) | [Ferramentas básicas do Azure Functions] provides the core runtime and templates for creating functions, which enable local development. A versão 2.x oferece suporte a desenvolvimento em Linux, MacOS e Windows. Todos os ambientes contam com ferramentas essenciais para o runtime local do Functions. |
| [Visual Studio 2019](functions-develop-vs.md) | [C# (biblioteca de classes)](functions-dotnet-class-library.md) | The Azure Functions tools are included in the **Azure development** workload of [Visual Studio 2019](https://www.visualstudio.com/vs/) and later versions. Permite que você compile funções em uma biblioteca de classes e publique o arquivo .dll no Azure. Inclui as Ferramentas Essenciais para teste local. Para saber mais, consulte [Desenvolver Azure Functions usando o Visual Studio](functions-develop-vs.md). |
| [Maven](functions-create-first-java-maven.md) (diversos) | [Java](functions-reference-java.md) | Integra-se com as Ferramentas Essenciais para habilitar o desenvolvimento de funções Java. A versão 2.x oferece suporte a desenvolvimento em Linux, MacOS e Windows. Para saber mais, consulte [Criar sua primeira função com Java e Maven](functions-create-first-java-maven.md). Também dá suporte ao uso de desenvolvimento [Eclipse](functions-create-maven-eclipse.md) e [IntelliJ IDEA](functions-create-maven-intellij.md) |

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

Cada um desses ambientes de desenvolvimento local permite criar projetos de aplicativo de funções e usar modelos predefinidos do Functions para criar novas funções. Cada um usa as Ferramentas Essenciais para que você possa testar e depurar suas funções com relação ao runtime do Functions em seu próprio computador, assim como faria com qualquer outro aplicativo. You can also publish your function app project from any of these environments to Azure.  

## <a name="next-steps"></a>Próximos passos

+ To learn more about local development of compiled C# functions using Visual Studio 2019, see [Develop Azure Functions using Visual Studio](functions-develop-vs.md).
+ To learn more about local development of functions using VS Code on a Mac, Linux, or Windows computer, see [Deploy Azure Functions from VS Code](/azure/javascript/tutorial-vscode-serverless-node-01).
+ Para saber mais sobre como desenvolver funções do prompt de comando ou do terminal, consulte [Trabalhar com as Ferramentas Essenciais do Azure Functions](functions-run-local.md).

<!-- LINKS -->

[Ferramentas básicas do Azure Functions]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure portal]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
