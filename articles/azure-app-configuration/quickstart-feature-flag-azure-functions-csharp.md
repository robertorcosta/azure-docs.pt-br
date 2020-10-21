---
title: Início rápido para adição de sinalizadores de recurso a Azure Functions | Microsoft Docs
description: Neste início rápido, use Azure Functions com sinalizadores de recurso da Configuração de Aplicativos do Azure e teste a função localmente.
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
ms.topic: quickstart
ms.date: 8/26/2020
ms.author: alkemper
ms.openlocfilehash: d1dc843ff676429f202c0b9077057d067294f738
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92076157"
---
# <a name="quickstart-add-feature-flags-to-an-azure-functions-app"></a>Início Rápido: Adicionar sinalizadores de recurso a um aplicativo Azure Functions

Neste guia de início rápido, você cria uma implementação do gerenciamento de recursos em um aplicativo Azure Functions usando a Configuração de Aplicativos do Azure. Você usará o serviço de Configuração de Aplicativos para armazenar de maneira centralizada todos os sinalizadores de recurso e controlar os estados deles. 

As bibliotecas do Gerenciamento de Recursos do .NET estendem a estrutura com suporte a sinalizadores de recursos. Essas bibliotecas se baseiam no sistema de configuração do .NET. Elas são integradas à Configuração de Aplicativos por meio do provedor de configuração do .NET.

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura do Azure - [criar uma gratuitamente](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) com a carga de trabalho de **desenvolvimento do Azure**.
- [Ferramentas do Azure Functions](../azure-functions/functions-develop-vs.md#check-your-tools-version)

## <a name="create-an-app-configuration-store"></a>Criar um repositório de Configuração de Aplicativos

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. Selecione **Gerenciador de Recursos** >  **+Adicionar** para adicionar um sinalizador de recurso chamado `Beta`.

    > [!div class="mx-imgBorder"]
    > ![Habilitar o sinalizador de recurso chamado Beta](media/add-beta-feature-flag.png)

    Deixe `label` e `Description` indefinidos por enquanto.

8. Selecione **Aplicar** para salvar o novo sinalizador de recurso.

## <a name="create-a-functions-app"></a>Criar um aplicativo do Functions

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

## <a name="connect-to-an-app-configuration-store"></a>Conectar um repositório de Configuração de Aplicativos

1. Clique com o botão direito do mouse no projeto e selecione **Gerenciar Pacotes do NuGet**. Na guia **Navegar**, pesquise e adicione os seguintes pacotes do NuGet ao projeto. Verifique `Microsoft.Extensions.DependencyInjection` se você está no build estável mais recente. 

    ```
    Microsoft.Extensions.DependencyInjection
    Microsoft.Extensions.Configuration
    Microsoft.FeatureManagement
    ```


1. Abra *Function1.cs* e adicione os namespaces desses pacotes.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.FeatureManagement;
    using Microsoft.Extensions.DependencyInjection;
    ```

1. Adicione o construtor estático `Function1` abaixo para inicializar o provedor de Configuração de Aplicativos do Azure. Em seguida, adicione dois membros `static`, um campo chamado `ServiceProvider` para criar uma instância singleton de `ServiceProvider` e uma propriedade abaixo `Function1` chamada `FeatureManager` para criar uma instância singleton de `IFeatureManager`. Em seguida, conecte-se à Configuração de Aplicativos no `Function1` chamando `AddAzureAppConfiguration()`. Esse processo carregará a configuração na inicialização do aplicativo. A mesma instância de configuração será usada para todas as chamadas do Functions posteriormente. 

    ```csharp
        // Implements IDisposable, cached for life time of function
        private static ServiceProvider ServiceProvider; 

        static Function1()
        {
            IConfigurationRoot configuration = new ConfigurationBuilder()
                .AddAzureAppConfiguration(options =>
                {
                    options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                           .UseFeatureFlags();
                }).Build();

            var services = new ServiceCollection();                                                                             
            services.AddSingleton<IConfiguration>(configuration).AddFeatureManagement();

            ServiceProvider = services.BuildServiceProvider(); 
        }

        private static IFeatureManager FeatureManager => ServiceProvider.GetRequiredService<IFeatureManager>();
    ```

1. Atualize o método `Run` para alterar o valor da mensagem exibida dependendo do estado do sinalizador de recurso.

    ```csharp
        [FunctionName("Function1")]
        public static async Task<IActionResult> Run(
                [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
                ILogger log)
            {
                string message = await FeatureManager.IsEnabledAsync("Beta")
                     ? "The Feature Flag 'Beta' is turned ON"
                     : "The Feature Flag 'Beta' is turned OFF";
                
                return (ActionResult)new OkObjectResult(message); 
            }
    ```

## <a name="test-the-function-locally"></a>Testar a função localmente

1. Defina uma variável de ambiente chamada **ConnectionString**, em que o valor é a chave de acesso que você recuperou anteriormente em seu repositório de Configuração de Aplicativos em **Chaves de Acesso**. Se você usar o prompt de comando do Windows, execute o comando a seguir e reinicie o prompt de comando para permitir que a alteração entre em vigor:

    ```cmd
        setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Se você usa o Windows PowerShell, execute o comando a seguir:

    ```azurepowershell
        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    Se você usa macOS ou Linux, execute o comando a seguir:

    ```bash
        export ConnectionString='connection-string-of-your-app-configuration-store'
    ```

1. Pressione F5 para testar sua função. Se solicitado, aceite a solicitação do Visual Studio para baixar e instalar as ferramentas **principais (CLI) do Azure Functions**. Além disso, talvez seja necessário habilitar uma exceção de firewall para que as ferramentas possam manipular solicitações HTTP.

1. Copie a URL da sua função da saída do Azure Functions runtime.

    ![Depuração de função do Início Rápido no VS](./media/quickstarts/function-visual-studio-debugging.png)

1. Cole a URL para a solicitação HTTP na barra de endereços do navegador. A imagem a seguir mostra a resposta indicando que o sinalizador de recurso `Beta` está desabilitado. 

    ![Sinalizador de recurso de função de início rápido desabilitado](./media/quickstarts/functions-launch-ff-disabled.png)

1. Entre no [portal do Azure](https://portal.azure.com). Escolha **Todos os recursos** e escolha a instância do repositório de Configuração de Aplicativos que você criou.

1. Escolha **Gerenciador de Recursos** e altere o estado da chave **Beta** para **Ativado**.

1. Retorne ao prompt de comando e cancele o processo em execução pressionando `Ctrl-C`.  Reinicie o aplicativo pressionando F5. 

1. Copie a URL de sua função da saída do Azure Functions Runtime usando o mesmo processo que na etapa 3. Cole a URL para a solicitação HTTP na barra de endereços do navegador. A resposta do navegador deve ter sido alterada para indicar que o sinalizador de recurso `Beta` está ativado, conforme mostrado na imagem abaixo.
 
    ![Sinalizador de recurso de função de início rápido habilitado](./media/quickstarts/functions-launch-ff-enabled.png)

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou um sinalizador de recurso e o usou com um aplicativo do Azure Functions por meio do [Provedor de Configuração de Aplicativos](/dotnet/api/Microsoft.Extensions.Configuration.AzureAppConfiguration).

- Saiba mais sobre o [gerenciamento de recursos](./concept-feature-management.md).
- [Gerenciar sinalizadores de recursos](./manage-feature-flags.md).
- [Usar a configuração dinâmica no aplicativo Azure Functions](./enable-dynamic-configuration-azure-functions-csharp.md)