---
title: Gerenciar usuários com a API de Microsoft Graph
titleSuffix: Azure AD B2C
description: Como gerenciar usuários em um locatário Azure AD B2C chamando a API de Microsoft Graph e usando uma identidade de aplicativo para automatizar o processo.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/13/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ff3cd858de86d21637f4a7a9ab9d9a83c7022f5a
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/13/2021
ms.locfileid: "98178867"
---
# <a name="manage-azure-ad-b2c-user-accounts-with-microsoft-graph"></a>Gerenciar Azure AD B2C contas de usuário com Microsoft Graph

Microsoft Graph permite que você gerencie contas de usuário em seu diretório Azure AD B2C fornecendo métodos Create, Read, Update e Delete na API Microsoft Graph. Você pode migrar um armazenamento de usuário existente para um locatário Azure AD B2C e executar outras operações de gerenciamento de conta de usuário chamando a API Microsoft Graph.

Nas seções a seguir, os principais aspectos do gerenciamento de usuário Azure AD B2C com a API Microsoft Graph são apresentados. As operações, os tipos e as propriedades da API Microsoft Graph apresentados aqui são um subconjunto do que aparece na documentação de referência da API do Microsoft Graph.

## <a name="register-a-management-application"></a>Registrar um aplicativo de gerenciamento

Antes que qualquer aplicativo de gerenciamento de usuário ou script que você escreve possa interagir com os recursos em seu locatário Azure AD B2C, você precisará de um registro de aplicativo que conceda as permissões para fazer isso.

Siga as etapas neste artigo de instruções para criar um registro de aplicativo que seu aplicativo de gerenciamento pode usar:

[Gerenciar Azure AD B2C com Microsoft Graph](microsoft-graph-get-started.md)

## <a name="user-management-microsoft-graph-operations"></a>Operações de Microsoft Graph de gerenciamento de usuário

As seguintes operações de gerenciamento de usuário estão disponíveis na [API de Microsoft Graph](/graph/api/resources/user):

- [Obter uma lista de usuários](/graph/api/user-list)
- [Criar um usuário](/graph/api/user-post-users)
- [Obter um usuário](/graph/api/user-get)
- [Atualizar um usuário](/graph/api/user-update)
- [Excluir um usuário](/graph/api/user-delete)


## <a name="code-sample-how-to-programmatically-manage-user-accounts"></a>Exemplo de código: como gerenciar programaticamente contas de usuário

Este exemplo de código é um aplicativo de console .NET Core que usa o [SDK Microsoft Graph](/graph/sdks/sdks-overview) para interagir com Microsoft Graph API. Seu código demonstra como chamar a API para gerenciar programaticamente os usuários em um locatário Azure AD B2C.
Você pode [baixar o arquivo morto de exemplo](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management/archive/master.zip) (*. zip), [procurar o repositório](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management) no GitHub ou clonar o repositório:

```cmd
git clone https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management.git
```

Depois de obter o exemplo de código, configure-o para o seu ambiente e, em seguida, compile o projeto:

1. Abra o projeto no [Visual Studio](https://visualstudio.microsoft.com) ou [Visual Studio Code](https://code.visualstudio.com).
1. Abra `src/appsettings.json`.
1. Na `appSettings` seção, substitua `your-b2c-tenant` pelo nome do seu locatário e `Application (client) ID` `Client secret` com os valores para o registro do aplicativo de gerenciamento (consulte a seção [registrar um aplicativo de gerenciamento](#register-a-management-application) deste artigo).
1. Abra uma janela de console no seu clone local do repositório, alterne para o `src` diretório e, em seguida, compile o projeto:
    ```console
    cd src
    dotnet build
    ```
1. Execute o aplicativo com o comando `dotnet`:

    ```console
    dotnet bin/Debug/netcoreapp3.1/b2c-ms-graph.dll
    ```

O aplicativo exibe uma lista de comandos que você pode executar. Por exemplo, obter todos os usuários, obter um único usuário, excluir um usuário, atualizar a senha de um usuário e importação em massa.

### <a name="code-discussion"></a>Discussão de código

O código de exemplo usa o [SDK do Microsoft Graph](/graph/sdks/sdks-overview), que foi projetado para simplificar a criação de aplicativos de alta qualidade, eficientes e resilientes que acessam Microsoft Graph.

Qualquer solicitação para a API de Microsoft Graph requer um token de acesso para autenticação. A solução utiliza o pacote NuGet [Microsoft. Graph. auth](https://www.nuget.org/packages/Microsoft.Graph.Auth/) que fornece um wrapper baseado em cenário de autenticação da MSAL (biblioteca de autenticação da Microsoft) para uso com o SDK do Microsoft Graph.

O `RunAsync` método no arquivo _Program.cs_ :

1. Lê as configurações do aplicativo do _appsettings.jsno_ arquivo
1. Inicializa o provedor de autenticação usando o fluxo de [concessão de credenciais de cliente do OAuth 2,0](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md) . Com o fluxo de concessão de credenciais de cliente, o aplicativo é capaz de obter um token de acesso para chamar a API de Microsoft Graph.
1. Configura o cliente do serviço de Microsoft Graph com o provedor de autenticação:

    ```csharp
    // Read application settings from appsettings.json (tenant ID, app ID, client secret, etc.)
    AppSettings config = AppSettingsFile.ReadFromJsonFile();

    // Initialize the client credential auth provider
    IConfidentialClientApplication confidentialClientApplication = ConfidentialClientApplicationBuilder
        .Create(config.AppId)
        .WithTenantId(config.TenantId)
        .WithClientSecret(config.ClientSecret)
        .Build();
    ClientCredentialProvider authProvider = new ClientCredentialProvider(confidentialClientApplication);

    // Set up the Microsoft Graph service client with client credentials
    GraphServiceClient graphClient = new GraphServiceClient(authProvider);
    ```

O *GraphServiceClient* inicializado é usado em _UserService.cs_ para executar as operações de gerenciamento de usuário. Por exemplo, obter uma lista das contas de usuário no locatário:

```csharp
public static async Task ListUsers(GraphServiceClient graphClient)
{
    Console.WriteLine("Getting list of users...");

    // Get all users (one page)
    var result = await graphClient.Users
        .Request()
        .Select(e => new
        {
            e.DisplayName,
            e.Id,
            e.Identities
        })
        .GetAsync();

    foreach (var user in result.CurrentPage)
    {
        Console.WriteLine(JsonConvert.SerializeObject(user));
    }
}
```

[Faça chamadas à API usando os SDKs de Microsoft Graph](/graph/sdks/create-requests) inclui informações sobre como ler e gravar informações de Microsoft Graph, usar `$select` para controlar as propriedades retornadas, fornecer parâmetros de consulta personalizados e usar os `$filter` parâmetros de `$orderBy` consulta e.

## <a name="next-steps"></a>Próximas etapas

Para obter um índice completo das operações de API de Microsoft Graph com suporte para recursos de Azure AD B2C, consulte [Microsoft Graph operações disponíveis para Azure ad B2C](microsoft-graph-operations.md).

<!-- LINK -->

[graph-objectIdentity]: /graph/api/resources/objectidentity
[graph-user]: (https://docs.microsoft.com/graph/api/resources/user)
