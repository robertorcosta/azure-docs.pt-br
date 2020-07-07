---
title: Tutorial para o uso da configuração dinâmica da Configuração de Aplicativos do Azure em um aplicativo Azure Functions | Microsoft Docs
description: Neste tutorial, você aprenderá a atualizar dinamicamente os dados de configuração de aplicativos Azure Functions
services: azure-app-configuration
documentationcenter: ''
author: zhenlan
manager: qingye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 11/17/2019
ms.author: zhenlwa
ms.custom: azure-functions
ms.tgt_pltfrm: Azure Functions
ms.openlocfilehash: e8f5b21189007d2b15c2ff31b778144d9a672318
ms.sourcegitcommit: 9b5c20fb5e904684dc6dd9059d62429b52cb39bc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85856481"
---
# <a name="tutorial-use-dynamic-configuration-in-an-azure-functions-app"></a>Tutorial: Usar a configuração dinâmica no aplicativo Azure Functions

O provedor de configuração do .NET Standard para Configuração de Aplicativos dá suporte ao cache e à atualização dinâmica da configuração controlada pela atividade do aplicativo. Este tutorial mostra como você pode implementar atualizações de configuração dinâmica no código. Ele se baseia no aplicativo Azure Functions introduzido nos inícios rápidos. Antes de continuar, conclua [Criar um aplicativo Azure Functions com a Configuração de Aplicativos do Azure](./quickstart-azure-functions-csharp.md).

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Configure seu aplicativo Azure Functions para atualizar a configuração em resposta a alterações em um repositório de Configuração de Aplicativos.
> * Insira a configuração mais recente para suas chamadas do Azure Functions.

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura do Azure - [criar uma gratuitamente](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) com a carga de trabalho de **desenvolvimento do Azure**
- [Ferramentas do Azure Functions](../azure-functions/functions-develop-vs.md#check-your-tools-version)
- Conclua o início rápido [Criar um aplicativo do Azure Functions com a Configuração de Aplicativos do Azure](./quickstart-azure-functions-csharp.md)

## <a name="reload-data-from-app-configuration"></a>Recarregar os dados da Configuração de Aplicativo

1. Abra *Function1.cs*. Além da propriedade `static` `Configuration`, adicione uma nova propriedade `static` `ConfigurationRefresher` para manter uma instância singleton de `IConfigurationRefresher` que será usada para sinalizar atualizações da configuração em chamadas do Functions posteriormente.

    ```csharp
    private static IConfiguration Configuration { set; get; }
    private static IConfigurationRefresher ConfigurationRefresher { set; get; }
    ```

2. Atualize o construtor e use o método `ConfigureRefresh` para especificar a configuração a ser atualizada no repositório de Configuração de Aplicativos. Uma instância de `IConfigurationRefresher` é recuperada usando o método `GetRefresher`. Opcionalmente, também alteramos a janela de tempo de expiração do cache de configuração para 1 minuto em vez do padrão de 30 segundos.

    ```csharp
    static Function1()
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(options =>
        {
            options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                   .ConfigureRefresh(refreshOptions =>
                        refreshOptions.Register("TestApp:Settings:Message")
                                      .SetCacheExpiration(TimeSpan.FromSeconds(60))
            );
            ConfigurationRefresher = options.GetRefresher();
        });
        Configuration = builder.Build();
    }
    ```

3. Atualize o método `Run` e sinalize para atualizar a configuração usando o método `TryRefreshAsync` no início da chamada do Functions. Ele não estará operacional se a janela de tempo de expiração do cache não for atingida. Remova o operador `await` se preferir que a configuração seja atualizada sem bloqueios.

    ```csharp
    public static async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        await ConfigurationRefresher.TryRefreshAsync(); 

        string keyName = "TestApp:Settings:Message";
        string message = Configuration[keyName];
            
        return message != null
            ? (ActionResult)new OkObjectResult(message)
            : new BadRequestObjectResult($"Please create a key-value with the key '{keyName}' in App Configuration.");
    }
    ```

## <a name="test-the-function-locally"></a>Testar a função localmente

1. Defina uma variável de ambiente chamada **ConnectionString** e defina-a como a chave de acesso ao repositório de configurações de aplicativo. Se você usar o prompt de comando do Windows, execute o comando a seguir e reinicie o prompt de comando para permitir que a alteração entre em vigor:

    ```console
    setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Se você usa o Windows PowerShell, execute o comando a seguir:

    ```powershell
    $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    Se você usa macOS ou Linux, execute o comando a seguir:

    ```console
    export ConnectionString='connection-string-of-your-app-configuration-store'
    ```

2. Para testar sua função, pressione F5. Se solicitado, aceite a solicitação do Visual Studio para baixar e instalar as ferramentas **principais (CLI) do Azure Functions**. Além disso, talvez seja necessário habilitar uma exceção de firewall para que as ferramentas possam manipular solicitações HTTP.

3. Copie a URL da sua função da saída do Azure Functions runtime.

    ![Depuração de função do Início Rápido no VS](./media/quickstarts/function-visual-studio-debugging.png)

4. Cole a URL para a solicitação HTTP na barra de endereços do navegador. A imagem a seguir mostra a resposta no navegador para a solicitação GET local retornada pela função.

    ![Inicialização local da Função do Início Rápido](./media/quickstarts/dotnet-core-function-launch-local.png)

5. Entre no [portal do Azure](https://portal.azure.com). Escolha **Todos os recursos** e escolha a instância do repositório de Configuração de Aplicativos que você criou no início rápido.

6. Selecione **Gerenciador de Configurações** e atualize os valores da seguinte chave:

    | Chave | Valor |
    |---|---|
    | TestApp:Settings:Message | Dados da Configuração de Aplicativos do Azure – Atualizados |

7. Atualize o navegador algumas vezes. Quando a configuração armazenada em cache expirar após um minuto, a página mostrará a resposta da chamada do Functions com o valor atualizado.

    ![Início rápido da atualização local da função](./media/quickstarts/dotnet-core-function-refresh-local.png)

O código de exemplo usado neste tutorial pode ser baixado de [Repositório do GitHub da Configuração de Aplicativos](https://github.com/Azure/AppConfiguration/tree/master/examples/DotNetCore/AzureFunction)

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você habilitou seu aplicativo Azure Functions a atualizar dinamicamente as configurações da Configuração de Aplicativos. Para saber como usar uma identidade gerenciada pelo Azure para simplificar o acesso à Configuração de Aplicativos, passe para o próximo tutorial.

> [!div class="nextstepaction"]
> [Integração de identidade gerenciada](./howto-integrate-azure-managed-service-identity.md)
