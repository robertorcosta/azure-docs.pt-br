---
title: Tutorial para o uso de referências do Key Vault da Configuração de Aplicativos do Azure em um aplicativo ASP.NET Core | Microsoft Docs
description: Neste tutorial, você aprenderá a usar as referências do Key Vault da Configuração de Aplicativos do Azure de um aplicativo ASP.NET Core
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 10/07/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: a14cb3035c159c82df44f686da7f7b78ef942943
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72035811"
---
# <a name="tutorial-use-key-vault-references-in-an-aspnet-core-app"></a>Tutorial: Usar referências do Key Vault em um aplicativo ASP.NET Core

Neste tutorial, você aprenderá a usar o serviço de Configuração de Aplicativos do Azure junto com o Azure Key Vault. Esses são serviços complementares, que serão usados lado a lado na maioria das implantações de aplicativo. Para ajudar você a usá-los juntos, a Configuração de Aplicativos permite que você crie chaves que fazem referência a valores armazenados no Key Vault. Quando você faz isso, a Configuração de Aplicativos armazena o URI para o valor Key Vault, em vez do valor propriamente dito. Seu aplicativo recupera o valor dessa chave usando o provedor de cliente da Configuração de Aplicativos, assim como qualquer outra chave armazenada na Configuração de Aplicativos. O provedor do cliente o reconhece como uma referência do Key Vault e chama o Key Vault para recuperar o valor. Seu aplicativo é responsável por autenticar corretamente, tanto na Configuração de Aplicativos quanto no Key Vault. Os dois serviços não se comunicam diretamente.

Este tutorial mostra como você pode implementar referências do Key Vault no código. Ele se baseia no aplicativo Web introduzido nos Inícios Rápidos. Antes de continuar, conclua [Criar um aplicativo ASP.NET Core com a Configuração de Aplicativo](./quickstart-aspnet-core-app.md) primeiro.

Você pode usar qualquer editor de código para executar as etapas deste tutorial. [Visual Studio Code](https://code.visualstudio.com/) é uma opção excelente que está disponível nas plataformas Windows, macOS e Linux.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma chave da Configuração de Aplicativos que referencia um valor armazenado no Key Vault
> * Acessar o valor dessa chave por meio de um aplicativo Web ASP.NET Core

## <a name="prerequisites"></a>Pré-requisitos

Para realizar este tutorial, instale o [SDK do .NET Core](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-vault"></a>Criar um cofre

1. Selecione a opção **Criar um recurso** no canto superior esquerdo do portal do Azure

    ![Saída após a conclusão da criação do Key Vault](./media/quickstarts/search-services.png)
2. Digite **Key Vault** na caixa Pesquisar.
3. Na lista de resultados, escolha **Key Vault**.
4. Na seção Key Vault, escolha **Criar**.
5. A seção **Criar cofre de chaves** fornece as seguintes informações:
    - **Nome**: um nome exclusivo é necessário. Para este início rápido, usamos **Contoso-vault2**. 
    - **Assinatura**: Escolha uma assinatura.
    - Em **Grupo de Recursos**, escolha **Criar novo** e digite um nome para o grupo de recursos.
    - No menu suspenso **Local**, escolha um local.
    - Deixe as outras opções em seus padrões.
6. Depois de fornecer as informações acima, selecione **Criar**.

Nesse ponto, sua conta do Azure é a única autorizada a acessar esse novo cofre.

![Saída após a conclusão da criação do Key Vault](./media/quickstarts/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>Adicionar um segredo ao Key Vault

Para adicionar um segredo ao cofre, basta executar algumas etapas adicionais. Nesse caso, adicionamos uma mensagem que podemos usar para testar a recuperação do Key Vault. A mensagem é chamada de **Mensagem** e armazenamos o valor de **Olá do Key Vault** nela.

1. Na página de propriedades do Key Vault, selecione **Segredos**.
1. Clique em **Gerar/Importar**.
1. Na tela **Criar um segredo**, escolha os seguintes valores:
    - **Opções de upload**: Manual.
    - **Nome**: Mensagem
    - **Valor**: Olá de Key Vault
    - Deixe os outros valores com seus padrões. Clique em **Criar**.

## <a name="add-a-key-vault-reference-to-app-config"></a>Adicionar uma referência do Key Vault à Configuração de Aplicativos

1. Entre no [Portal do Azure](https://portal.azure.com). Selecione **Todos os recursos** e selecione a instância do repositório de configurações do aplicativo que você criou no início rápido.

1. Clique em **Gerenciador de Configurações**.

1. Clique em **+ Criar** > **Referência do Key Vault** e escolha os seguintes valores:
    - **Chave**: TestApp:Settings:KeyVaultMessage
    - **Rótulo**: Deixar em branco
    - **Assinatura**, **Grupo de recursos**, **Cofre de chaves**: Escolha as opções correspondentes ao Key Vault que você criou na seção anterior.
    - **Segredo**: Selecione o segredo chamado **Mensagem** criado na seção anterior.

## <a name="connect-to-key-vault"></a>Conectar-se ao Key Vault

1. Para este tutorial, você usará uma entidade de serviço para autenticação no Key Vault. Para criar uma entidade de serviço, use o comando [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) da CLI do Azure:

    ```azurecli
    az ad sp create-for-rbac -n "http://mySP" --sdk-auth
    ```

    Essa operação retornará uma série de pares de chave/valor. 

    ```console
    {
    "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
    "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
    "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
    "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "activeDirectoryGraphResourceId": "https://graph.windows.net/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
    }
    ```

1. Execute o comando a seguir para permitir que a entidade de serviço acesse o cofre de chaves:

        az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey

1. Adicione segredos para *clientId* e *clientSecret* para o Gerenciador de Segredos. Esses comandos devem ser executados no mesmo diretório que o arquivo *.csproj*.

        dotnet user-secrets set ConnectionStrings:KeyVaultClientId <clientId-of-your-service-principal>        
        dotnet user-secrets set ConnectionStrings:KeyVaultClientSecret <clientSecret-of-your-service-principal>

> [!NOTE]
> Essas credenciais do Key Vault são usadas somente dentro do seu aplicativo. Seu aplicativo é autenticado diretamente para o Key Vault com essas credenciais. Elas nunca são passadas para o serviço de Configuração de Aplicativos.

## <a name="update-your-code-to-use-a-key-vault-reference"></a>Atualizar o código para usar uma referência do Key Vault

1. Abra *Program.cs* e adicione referências a pacotes necessários.

    ```csharp
    using Microsoft.Azure.KeyVault;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

1. Atualize o método `CreateWebHostBuilder` para usar a Configuração de Aplicativos chamando o método `config.AddAzureAppConfiguration()`. Inclua a opção `UseAzureKeyVault`, passando uma nova referência de `KeyVaultClient` para seu Key Vault.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                KeyVaultClient kvClient = new KeyVaultClient(async (authority, resource, scope) =>
                {
                    var adCredential = new ClientCredential(settings["ConnectionStrings:KeyVaultClientId"], settings["ConnectionStrings:KeyVaultClientSecret"]);
                    var authenticationContext = new AuthenticationContext(authority, null);
                    return (await authenticationContext.AcquireTokenAsync(resource, adCredential)).AccessToken;
                });

                config.AddAzureAppConfiguration(options => {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                            .UseAzureKeyVault(kvClient); });
            })
            .UseStartup<Startup>();
    ```

1. Depois que você passar a referência de *KeyVaultClient* para o método `UseAzureKeyVault` ao inicializar a conexão com a Configuração de Aplicativos, você pode acessar os valores das referências do Key Vault da mesma maneira que acessa os valores das chaves comuns da Configuração de Aplicativos. Para ver esse processo em ação, abra *Index.cshtml* no diretório Exibições > Página Inicial. Substitua o conteúdo dele pelo código a seguir:

    ```html
    @using Microsoft.Extensions.Configuration
    @inject IConfiguration Configuration

    <style>
        body {
            background-color: @Configuration["TestApp:Settings:BackgroundColor"]
        }
        h1 {
            color: @Configuration["TestApp:Settings:FontColor"];
            font-size: @Configuration["TestApp:Settings:FontSize"];
        }
    </style>

    <h1>@Configuration["TestApp:Settings:Message"]
        and @Configuration["TestApp:Settings:KeyVaultMessage"]</h1>
    ```

    Você acessa o valor da referência do Key Vault *TestApp:Settings:KeyVaultMessage* da mesma maneira que o valor de configuração *TestApp:Settings:Message*

## <a name="build-and-run-the-app-locally"></a>Compilar e executar o aplicativo localmente

1. Para criar o aplicativo usando a CLI do .NET Core, execute o seguinte comando no shell de comando:

        dotnet build

2. Depois que a construção for concluída com êxito, execute o seguinte comando para executar o aplicativo Web localmente:

        dotnet run

3. Abra uma janela do navegador e vá para `http://localhost:5000`, que é a URL padrão do aplicativo Web hospedado localmente.

    ![Inicialização local do aplicativo do Início Rápido](./media/key-vault-reference-launch-local.png)


## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você adicionou uma identidade de serviço gerenciada do Azure para simplificar o acesso à Configuração de Aplicativo e melhorar o gerenciamento de credenciais de seu aplicativo. Para saber mais sobre como usar a Configuração de Aplicativo, continue para ver as amostras da CLI do Azure.

> [!div class="nextstepaction"]
> [Exemplos de CLI](./cli-samples.md)
