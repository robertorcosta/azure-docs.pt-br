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
ms.openlocfilehash: 992cface653bf3fe52afc7efa3f17573fcf91399
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73469651"
---
# <a name="tutorial-use-key-vault-references-in-an-aspnet-core-app"></a>Tutorial: Usar referências do Key Vault em um aplicativo ASP.NET Core

Neste tutorial, você aprenderá a usar o serviço de Configuração de Aplicativos do Azure junto com o Azure Key Vault. A Configuração de Aplicativos e o Key Vault são serviços complementares usados lado a lado na maioria das implantações de aplicativo.

A Configuração de Aplicativos ajuda você a usar os serviços juntos por meio da criação de chaves que fazem referência a valores armazenados no Key Vault. Quando a Configuração de Aplicativos cria essas chaves, ela armazena os URIs de valores do Key Vault em vez dos valores propriamente ditos.

Seu aplicativo usa o provedor do cliente da Configuração de Aplicativos para recuperar referências do Key Vault, assim como faz para quaisquer outras chaves armazenadas na Configuração de Aplicativos. Nesse caso, os valores armazenados na Configuração de Aplicativos são URIs que fazem referência aos valores no Key Vault. Eles não são valores nem credenciais do Key Vault. Já que o provedor do cliente reconhece as chaves como referências do Key Vault, ele usa o Key Vault para recuperar os valores delas.

Seu aplicativo é responsável por autenticar corretamente, tanto na Configuração de Aplicativos quanto no Key Vault. Os dois serviços não se comunicam diretamente.

Este tutorial mostra a você como implementar referências do Key Vault em seu código. Ele se baseia no aplicativo Web introduzido nos Inícios Rápidos. Antes de continuar, conclua [Criar um aplicativo ASP.NET Core com a Configuração de Aplicativo](./quickstart-aspnet-core-app.md) primeiro.

Você pode usar qualquer editor de código para executar as etapas deste tutorial. Por exemplo, o [Visual Studio Code](https://code.visualstudio.com/) é um editor de código multiplataforma disponível para os sistemas operacionais Windows, macOS e Linux.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma chave da Configuração de Aplicativos que referencia um valor armazenado no Key Vault.
> * Acessar o valor dessa chave por meio de um aplicativo Web ASP.NET Core.

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, instale o [SDK do .NET Core](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-vault"></a>Criar um cofre

1. Selecione a opção **Criar um recurso** no canto superior esquerdo do portal do Azure:

    ![Saída após a conclusão da criação do cofre de chaves](./media/quickstarts/search-services.png)
1. Digite **Key Vault** na caixa de pesquisa.
1. Na lista de resultados, selecione **Cofres de chaves** à esquerda.
1. Em **Cofres de chaves**, selecione **Adicionar**.
1. À direita, na seção **Criar cofre de chaves**, forneça as seguintes informações:
    - Selecione **Assinatura** para escolher uma assinatura.
    - Em **Grupo de Recursos**, selecione **Criar novo** e digite um nome para o grupo de recursos.
    - Em **Nome do cofre de chaves**, é necessário um nome exclusivo. Para este tutorial, insira **Contoso-vault2**.
    - Na lista suspensa **Região**, escolha uma localização.
1. Deixe as outras opções de **Criar cofre de chaves** com os valores padrão.
1. Selecione **Criar**.

Nesse ponto, sua conta do Azure é a única autorizada a acessar esse novo cofre.

![Saída após a conclusão da criação do cofre de chaves](./media/quickstarts/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>Adicionar um segredo ao Key Vault

Para adicionar um segredo ao cofre, basta executar algumas etapas adicionais. Nesse caso, adicione uma mensagem que você possa usar para testar a recuperação do Key Vault. A mensagem é chamada de **Mensagem** e você armazena nela o valor de "Olá do Key Vault".

1. Na página de propriedades do Key Vault, selecione **Segredos**.
1. Selecione **Gerar/Importar**.
1. No painel **Criar um segredo**, insira os seguintes valores:
    - **Opções de upload**: insira **Manual**.
    - **Nome**: insira **Mensagem**.
    - **Valor**: insira **Olá do Key Vault**.
1. Deixe as outras propriedades de **Criar um segredo** com os valores padrão.
1. Selecione **Criar**.

## <a name="add-a-key-vault-reference-to-app-configuration"></a>Adicionar uma referência do Key Vault à Configuração de Aplicativos

1. Entre no [Portal do Azure](https://portal.azure.com). Escolha **Todos os recursos** e depois escolha a instância do repositório de Configurações de Aplicativos que você criou no início rápido.

1. Selecione **Gerenciador de Configurações**.

1. Clique em **+ Criar** > **Referência do Key Vault** e especifique os seguintes valores:
    - **Chave**: selecione **TestApp:Settings:KeyVaultMessage**.
    - **Rótulo**: deixe esse valor em branco.
    - **Assinatura**, **Grupo de recursos** e **Cofre de chaves**: Insira os valores correspondentes àqueles no cofre de chaves que você criou na seção anterior.
    - **Segredo**: selecione o segredo chamado **Mensagem** criado na seção anterior.

## <a name="connect-to-key-vault"></a>Conectar-se ao Key Vault

1. Neste tutorial, você usará uma entidade de serviço para autenticação no Key Vault. Para criar uma entidade de serviço, use o comando [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) da CLI do Azure:

    ```azurecli
    az ad sp create-for-rbac -n "http://mySP" --sdk-auth
    ```

    Essa operação retorna uma série de pares de chave/valor:

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

    ```
    az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
    ```

1. Adicione segredos para *clientId* e *clientSecret* ao Gerenciador de Segredos, a ferramenta para armazenar dados confidenciais que você adicionou ao arquivo *.csproj* no [Início rápido: criar um aplicativo ASP.NET Core com a Configuração de Aplicativos do Azure](./quickstart-aspnet-core-app.md). Esses comandos devem ser executados no mesmo diretório que o arquivo *.csproj*.

    ```
    dotnet user-secrets set ConnectionStrings:KeyVaultClientId <clientId-of-your-service-principal>
    dotnet user-secrets set ConnectionStrings:KeyVaultClientSecret <clientSecret-of-your-service-principal>
    ```

> [!NOTE]
> Essas credenciais do Key Vault são usadas somente dentro do seu aplicativo. Seu aplicativo é autenticado diretamente para o Key Vault com essas credenciais. Elas nunca são passadas para o serviço de Configuração de Aplicativos.

## <a name="update-your-code-to-use-a-key-vault-reference"></a>Atualizar o código para usar uma referência do Key Vault

1. Abra *Program.cs* e adicione referências aos seguintes pacotes necessários:

    ```csharp
    using Microsoft.Azure.KeyVault;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

1. Atualize o método `CreateWebHostBuilder` para usar a Configuração de Aplicativos chamando o método `config.AddAzureAppConfiguration`. Inclua a opção `UseAzureKeyVault` a passar para uma nova referência de `KeyVaultClient` para o seu Key Vault.

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

1. Ao inicializar a conexão com a Configuração de Aplicativos, você passou a referência de `KeyVaultClient` para o método `UseAzureKeyVault`. Após a inicialização, você pode acessar os valores de referências do Key Vault da mesma maneira que acessa os valores de chaves comuns da Configuração de Aplicativos.

    Para ver esse processo em ação, abra *Index.cshtml* no diretório **Exibições** > **Página Inicial**. Substitua o conteúdo pelo código a seguir:

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

    Você acessa o valor da referência do Key Vault **TestApp:Settings:KeyVaultMessage** da mesma maneira que acessa o valor de configuração **TestApp:Settings:Message**.

## <a name="build-and-run-the-app-locally"></a>Compilar e executar o aplicativo localmente

1. Para criar o aplicativo usando a CLI do .NET Core, execute o seguinte comando no shell de comando:

    ```
    dotnet build
    ```

1. Depois que o build é concluído, use o comando a seguir para executar o aplicativo Web localmente:

    ```
    dotnet run
    ```

1. Abra uma janela do navegador e vá para `http://localhost:5000`, que é a URL padrão do aplicativo Web hospedado localmente.

    ![Inicialização de aplicativo local de início rápido](./media/key-vault-reference-launch-local.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou uma chave da Configuração de Aplicativos que referencia um valor armazenado no Key Vault. Para saber como adicionar uma identidade de serviço gerenciada pelo Azure que simplifica o acesso à Configuração de Aplicativos e ao Key Vault, prossiga para o próximo tutorial.

> [!div class="nextstepaction"]
> [Integração de identidade gerenciada](./howto-integrate-azure-managed-service-identity.md)
