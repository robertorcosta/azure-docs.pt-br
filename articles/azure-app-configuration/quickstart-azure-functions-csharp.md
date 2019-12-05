---
title: Início Rápido da Configuração de Aplicativo do Azure com o Azure Functions | Microsoft Docs
description: Um Início Rápido para o uso da Configuração de Aplicativo do Azure com o Azure Functions.
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: Azure Functions
ms.workload: tbd
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: 6329cf0e74bbcf57164afeab5b04e2af4ee43943
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74187209"
---
# <a name="quickstart-create-an-azure-functions-app-with-azure-app-configuration"></a>Início Rápido: Criar um aplicativo do Azure Functions com a Configuração de Aplicativos do Azure

Neste guia de início rápido, você incorpora o serviço de Configuração de Aplicativos do Azure em uma aplicativo do Azure Functions para centralizar o armazenamento e o gerenciamento de configurações de aplicativo separadas do seu código.

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura do Azure - [criar uma gratuitamente](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) com a carga de trabalho de **desenvolvimento do Azure**.
- [Ferramentas do Azure Functions](../azure-functions/functions-develop-vs.md#check-your-tools-version)

## <a name="create-an-app-configuration-store"></a>Criar um repositório de Configuração de Aplicativos

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Selecione **Gerenciador de Configurações** >  **+ Criar** para adicionar os seguintes pares chave-valor:

    | Chave | Valor |
    |---|---|
    | TestApp:Settings:Message | Dados da Configuração de Aplicativo do Azure |

    Deixe **Rótulo** e **Tipo de Conteúdo** vazios por enquanto.

## <a name="create-a-functions-app"></a>Criar um aplicativo do Functions

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

## <a name="connect-to-an-app-configuration-store"></a>Conectar um repositório de Configuração de Aplicativos

1. Clique com o botão direito do mouse no projeto e selecione **Gerenciar Pacotes do NuGet**. Na guia **Navegar**, pesquise e adicione os seguintes pacotes do NuGet ao projeto. Se você não conseguir localizá-los, marque a caixa de seleção **Incluir pré-lançamento**.

    ```
    Microsoft.Extensions.Configuration.AzureAppConfiguration 2.1.0-preview-010380001-1099 or later
    ```

2. Abra *Function1.cs* e adicione os namespaces da configuração do .NET Core e o provedor de configuração da Configuração de Aplicativos.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```
3. Adicione uma propriedade `static` `Configuration` para criar uma instância singleton de `IConfiguration`. Em seguida, adicione um construtor `static` para se conectar à Configuração de Aplicativos chamando `AddAzureAppConfiguration()`. Isso carregará a configuração uma vez na inicialização do aplicativo. A mesma instância de configuração será usada para todas as chamadas do Functions posteriormente.

    ```csharp
    private static IConfiguration Configuration { set; get; }

    static Function1()
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(Environment.GetEnvironmentVariable("ConnectionString"));
        Configuration = builder.Build();
    }
    ```
4. Atualize o método `Run` para ler valores da configuração.

    ```csharp
    public static async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        string keyName = "TestApp:Settings:Message";
        string message = Configuration[keyName];
            
        return message != null
            ? (ActionResult)new OkObjectResult(message)
            : new BadRequestObjectResult($"Please create a key-value with the key '{keyName}' in App Configuration.");
    }
    ```

## <a name="test-the-function-locally"></a>Testar a função localmente

1. Defina uma variável de ambiente chamada **ConnectionString** e defina-a como a chave de acesso ao repositório de Configuração de Aplicativos. Se você usar o prompt de comando do Windows, execute o comando a seguir e reinicie o prompt de comando para permitir que a alteração entre em vigor:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Se você usa o Windows PowerShell, execute o comando a seguir:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Se você usa macOS ou Linux, execute o comando a seguir:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Para testar sua função, pressione F5. Se solicitado, aceite a solicitação do Visual Studio para baixar e instalar as ferramentas **principais (CLI) do Azure Functions**. Além disso, talvez seja necessário habilitar uma exceção de firewall para que as ferramentas possam manipular solicitações HTTP.

3. Copie a URL da sua função da saída do Azure Functions runtime.

    ![Depuração de função do Início Rápido no VS](./media/quickstarts/function-visual-studio-debugging.png)

4. Cole a URL para a solicitação HTTP na barra de endereços do navegador. A imagem a seguir mostra a resposta no navegador para a solicitação GET local retornada pela função.

    ![Inicialização local da Função do Início Rápido](./media/quickstarts/dotnet-core-function-launch-local.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou um repositório de Configuração de Aplicativos e o usou com um aplicativo do Azure Functions por meio do [Provedor de Configuração de Aplicativos](https://go.microsoft.com/fwlink/?linkid=2074664). Para saber como configurar seu aplicativo do Azure Functions para atualizar dinamicamente as configurações, passe para o próximo tutorial.

> [!div class="nextstepaction"]
> [Habilitar configuração dinâmica](./enable-dynamic-configuration-azure-functions-csharp.md)
