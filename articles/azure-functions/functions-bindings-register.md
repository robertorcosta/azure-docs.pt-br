---
title: Registre extensões de vinculação de funções do Azure
description: Aprenda a registrar uma extensão de vinculação funções do Azure com base no seu ambiente.
author: craigshoemaker
ms.topic: reference
ms.date: 07/08/2019
ms.author: cshoe
ms.openlocfilehash: 1688fe848beb62731391bf4399a0dabec5265320
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277512"
---
# <a name="register-azure-functions-binding-extensions"></a>Registre extensões de vinculação de funções do Azure

Na versão 2.x funções do Azure, [as vinculações](./functions-triggers-bindings.md) estão disponíveis como pacotes separados do tempo de execução das funções. Enquanto as funções .NET acessam vinculações através de pacotes NuGet, os pacotes de extensão permitem que outras funções acessem todas as vinculações através de uma configuração.

Considere os seguintes itens relacionados a extensões de vinculação:

- As extensões de vinculação não estão explicitamente registradas nas Funções 1.x, exceto ao [criar uma biblioteca de classe C# usando o Visual Studio](#local-csharp).

- Os gatilhos HTTP e temporizador são suportados por padrão e não requerem uma extensão.

A tabela a seguir indica quando e como você registra as vinculações.

| Ambiente de desenvolvimento |Registro<br/> em funções de 1. x  |Registro<br/> em funções 2. x  |
|-------------------------|------------------------------------|------------------------------------|
|Portal do Azure|Automático|Automático|
|Non-.NET idiomas ou o desenvolvimento local de Ferramentas Core do Azure|Automático|[Use ferramentas principais de funções do Azure e pacotes de extensão](#extension-bundles)|
|Biblioteca de classe C# usando o Visual Studio|[Usar as ferramentas do NuGet](#vs)|[Usar as ferramentas do NuGet](#vs)|
|Biblioteca de classes C# usando o código do Visual Studio|N/D|[Use o .NET Core CLI](#vs-code)|

## <a name="extension-bundles-for-local-development"></a><a name="extension-bundles"></a>Pacotes de extensão para desenvolvimento local

Os pacotes de extensão são uma tecnologia de implantação que permite adicionar um conjunto compatível de extensões de vinculação de funções ao seu aplicativo de função. Um conjunto predefinido de extensões são adicionados quando você constrói seu aplicativo. Os pacotes de extensão definidos em um pacote são compatíveis entre si, o que ajuda a evitar conflitos entre pacotes. Você habilita pacotes de extensão no arquivo host.json do aplicativo.  

Você pode usar pacotes de extensão com versões 2.x e posteriores do tempo de execução functions. Ao desenvolver localmente, certifique-se de que você está usando a versão mais recente do [Azure Functions Core Tools](functions-run-local.md#v2).

Use pacotes de extensão para o desenvolvimento local usando as ferramentas principais do Azure Functions, visual studio code e quando você for construído remotamente.

Se você não usar pacotes de extensão, você deve instalar o .NET Core 2.x SDK no computador local antes de instalar quaisquer extensões de vinculação. Os pacotes de extensão removem essa exigência para o desenvolvimento local. 

Para usar pacotes de extensão, atualize o arquivo `extensionBundle` *host.json* para incluir a seguinte entrada para :
 
[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

<a name="local-csharp"></a>

## <a name="c-class-library-with-visual-studio"></a><a name="vs"></a>Biblioteca\# de classe C com Visual Studio

No **Visual Studio,** você pode instalar pacotes do Console do Gerenciador de Pacotes usando o comando [Install-Package,](https://docs.microsoft.com/nuget/tools/ps-ref-install-package) como mostrado no exemplo a seguir:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

O nome do pacote utilizado para uma determinada vinculação está previsto no artigo de referência para essa vinculação. Para obter um exemplo, consulte o [pacotes seção do artigo de referência de associação do barramento de serviço](functions-bindings-service-bus.md#functions-1x).

Substituir `<TARGET_VERSION>` no exemplo com uma versão específica do pacote, como `3.0.0-beta5`. As versões válidas estão listadas nas páginas de pacotes individuais em [NuGet.org](https://nuget.org). As principais versões que correspondem ao tempo de execução de Funções 1.x ou 2.x são especificadas no artigo de referência para a vinculação.

Se você `Install-Package` usar para referenciar uma vinculação, você não precisa usar [pacotes de extensão](#extension-bundles). Esta abordagem é específica para bibliotecas de classe construídas no Visual Studio.

## <a name="c-class-library-with-visual-studio-code"></a><a name="vs-code"></a>Biblioteca de classe C# com Código Visual Studio

No **Visual Studio Code,** instale pacotes para um projeto de biblioteca de classe C# a partir do prompt de comando usando o comando [dotnet add package](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) no .NET Core CLI. O exemplo a seguir demonstra como você adiciona uma vinculação:

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.<BINDING_TYPE_NAME> --version <TARGET_VERSION>
```

O .NET Core CLI só pode ser usado para o desenvolvimento do Azure Functions 2.x.

Substitua pelo `<BINDING_TYPE_NAME>` nome do pacote que contém a vinculação necessária. Você pode encontrar o artigo de referência vinculante desejado na [lista de vinculações suportadas](./functions-triggers-bindings.md#supported-bindings).

Substituir `<TARGET_VERSION>` no exemplo com uma versão específica do pacote, como `3.0.0-beta5`. As versões válidas estão listadas nas páginas de pacotes individuais em [NuGet.org](https://nuget.org). As principais versões que correspondem ao tempo de execução de Funções 1.x ou 2.x são especificadas no artigo de referência para a vinculação.

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Gatilho de função azure e exemplo de vinculação](./functions-bindings-example.md)
