---
title: Início Rápido da Configuração de Aplicativo do Azure com o .NET Core | Microsoft Docs
description: Um Início Rápido para o uso da Configuração de Aplicativo do Azure com aplicativos .NET Core
services: azure-app-configuration
author: yegu-ms
ms.service: azure-app-configuration
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: yegu
ms.openlocfilehash: 46aab7bcb9ec941bee465b0d87430b7415b2dfeb
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75413685"
---
# <a name="quickstart-create-a-net-core-app-with-app-configuration"></a>Início Rápido: Criar um aplicativo .NET Core com a Configuração de Aplicativo

Neste guia de início rápido, você incorpora a Configuração de Aplicativos do Azure em um aplicativo .NET Core para centralizar o armazenamento e o gerenciamento de configurações de aplicativo separadas do seu código.

## <a name="prerequisites"></a>Prerequisites

- Assinatura do Azure - [criar uma gratuitamente](https://azure.microsoft.com/free/)
- [SDK do .NET Core](https://dotnet.microsoft.com/download) – também disponível no [Azure Cloud Shell](https://shell.azure.com).

## <a name="create-an-app-configuration-store"></a>Criar um repositório de Configuração de Aplicativos

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Selecione **Gerenciador de Configurações** > **Criar** para adicionar os seguintes pares chave-valor:

    | Chave | Valor |
    |---|---|
    | TestApp:Settings:Message | Dados da Configuração de Aplicativo do Azure |

    Deixe **Rótulo** e **Tipo de Conteúdo** vazios por enquanto.

## <a name="create-a-net-core-console-app"></a>Criar um aplicativo de console .NET Core

Você usará a [CLI (interface de linha de comando) do .NET Core](https://docs.microsoft.com/dotnet/core/tools/) para criar um projeto de aplicativo de console .NET Core. A vantagem de usar a CLI do .NET Core em relação ao Visual Studio é que ela está disponível nas plataformas Windows, macOS e Linux.  Como alternativa, use as ferramentas pré-instaladas disponíveis no [Azure Cloud Shell](https://shell.azure.com).

1. Crie uma nova pasta para o seu projeto.

2. Na nova pasta, execute o seguinte comando para criar um novo projeto de aplicativo do console do ASP.NET Core:

    ```CLI
        dotnet new console
    ```

## <a name="connect-to-an-app-configuration-store"></a>Conectar um repositório de Configuração de Aplicativos

1. Adicione uma referência ao pacote NuGet `Microsoft.Extensions.Configuration.AzureAppConfiguration`, executando o seguinte comando:

    ```CLI
        dotnet add package Microsoft.Extensions.Configuration.AzureAppConfiguration --version 2.0.0-preview-009470001-1371
    ```

2. Execute o seguinte comando para restaurar pacotes do projeto:

    ```CLI
        dotnet restore
    ```

3. Abra *Program.cs* e adicione uma referência ao provedor da Configuração de Aplicativos do .NET Core.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

4. Atualize o método `Main` para usar a Configuração de Aplicativos chamando o método `builder.AddAzureAppConfiguration()`.

    ```csharp
    static void Main(string[] args)
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(Environment.GetEnvironmentVariable("ConnectionString"));

        var config = builder.Build();
        Console.WriteLine(config["TestApp:Settings:Message"] ?? "Hello world!");
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Compilar e executar o aplicativo localmente

1. Defina uma variável de ambiente chamada **ConnectionString** e defina-a como a chave de acesso ao repositório de Configuração de Aplicativos. Na linha de comando, execute o seguinte comando e reinicie o prompt de comando para permitir que a alteração entre em vigor:

    ```CLI
        setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Se você usa o Windows PowerShell, execute o comando a seguir:

    ```azurepowershell
        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    Se você usa macOS ou Linux, execute o comando a seguir:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Execute o seguinte comando para compilar o aplicativo de console:

    ```CLI
        dotnet build
    ```

3. Depois que a construção for concluída com êxito, execute o seguinte comando para executar o aplicativo localmente:

    ```CLI
        dotnet run
    ```

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou um repositório de Configuração de Aplicativos e o usou com um aplicativo de console .NET Core por meio do [provedor da Configuração de Aplicativos](https://go.microsoft.com/fwlink/?linkid=2074664). Para saber como configurar seu aplicativo .NET Core para atualizar dinamicamente as configurações, passe para o próximo tutorial.

> [!div class="nextstepaction"]
> [Habilitar configuração dinâmica](./enable-dynamic-configuration-dotnet-core.md)
