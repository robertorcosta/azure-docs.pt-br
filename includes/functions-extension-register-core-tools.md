---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 08/07/2020
ms.author: glenga
ms.openlocfilehash: 2936d22eacef73daef4433b3fd296dd4757fa410
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88031109"
---
Se não for possível usar os pacotes de extensão, você poderá usar Azure Functions Core Tools localmente para instalar os pacotes de extensão específicos exigidos pelo seu projeto. 

> [!NOTE]
> Para instalar manualmente as extensões usando as ferramentas básicas, você deve ter o SDK do .NET Core 2. x instalado.

Quando você instala extensões explicitamente, um arquivo de projeto do .NET chamado Extensions. csproj é adicionado à raiz do seu projeto. Esse arquivo define o conjunto de pacotes NuGet exigidos por suas funções. Embora você possa trabalhar com as [referências do pacote NuGet](/nuget/consume-packages/package-references-in-project-files) neste arquivo, as ferramentas principais permitem que você instale extensões sem precisar editar manualmente o arquivo.

Há várias maneiras de usar as ferramentas básicas para instalar as extensões necessárias em seu projeto local. 

#### <a name="install-all-extensions"></a>Instalar todas as extensões 

Use o seguinte comando para adicionar automaticamente todos os pacotes de extensão usados pelas associações em seu projeto local:

```dotnetcli
func extensions install
```
O comando lê o *function.jsno* arquivo para ver quais pacotes são necessários, instala-os e recria o projeto de extensões (Extensions. csproj). Ele adiciona as novas associações na versão atual, mas não atualiza as associações existentes. Use a opção `--force` para atualizar as associações existentes para a versão mais recente nas novas instalações.

#### <a name="install-a-specific-extension"></a>Instalar uma extensão específica

Use o comando a seguir para instalar um pacote de extensão específico em uma versão específica, neste caso, a extensão de armazenamento:

```dotnetcli
func extensions install --package Microsoft.Azure.WebJobs.Extensions.Storage --version 4.0.2
```