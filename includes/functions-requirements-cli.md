---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: a84f0a92703d1b626710cfc4dcfa2820bc58bda6
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673168"
---
## <a name="configure-your-local-environment"></a>Configurar o ambiente local

Antes de começar, você deverá ter o seguinte:

+ Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell"  
+ O [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md#v2) versão 2.7.1846 ou uma versão 2.x posterior.
::: zone-end  
::: zone pivot="programming-language-python"
+ As versões 3.6 e 3.7 do Python requerem o [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md#v2) versão 2.7.1846 ou uma versão 2.x posterior. O Python 3.8 requer a [versão 3.x](../articles/azure-functions/functions-run-local.md#v2) do Core Tools.
::: zone-end

+ A [CLI do Azure](/cli/azure/install-azure-cli) versão 2.0.76 ou posterior. 
::: zone pivot="programming-language-javascript,programming-language-typescript"
+ [Node.js](https://nodejs.org/), versões Active LTS e Maintenance LTS (8.11.1 e 10.14.1 recomendadas).
::: zone-end

::: zone pivot="programming-language-python"
+ [Python 3.8 (64 bits)](https://www.python.org/downloads/release/python-382/), [Python 3.7 (64 bits)](https://www.python.org/downloads/release/python-375/), [Python 3.6 (64 bits)](https://www.python.org/downloads/release/python-368/), compatíveis com o Azure Functions. 
::: zone-end
::: zone pivot="programming-language-powershell"
+ [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows)

+ O [SDK do .NET Core 2.2+](https://www.microsoft.com/net/download)
::: zone-end
::: zone pivot="programming-language-java"  
+ O [Java Developer Kit](https://aka.ms/azure-jdks), versão 8.

+ [Apache Maven](https://maven.apache.org), versão 3.0 ou posterior.

> [!IMPORTANT]
> A variável de ambiente JAVA_HOME deve ser definida como o local de instalação do JDK para concluir este guia de início rápido.
::: zone-end