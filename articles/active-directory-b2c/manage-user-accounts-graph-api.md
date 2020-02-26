---
title: Gerenciar usuários com a API de Microsoft Graph
titleSuffix: Azure AD B2C
description: Como gerenciar usuários em um locatário Azure AD B2C chamando a API de Microsoft Graph e usando uma identidade de aplicativo para automatizar o processo.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: b53405d199072211304e21b681de646c4e41243c
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77585621"
---
# <a name="manage-azure-ad-b2c-user-accounts-with-microsoft-graph"></a>Gerenciar Azure AD B2C contas de usuário com Microsoft Graph

Microsoft Graph permite que você gerencie contas de usuário em seu diretório Azure AD B2C fornecendo métodos Create, Read, Update e Delete na API Microsoft Graph. Você pode migrar um armazenamento de usuário existente para um locatário Azure AD B2C e executar outras operações de gerenciamento de conta de usuário chamando a API Microsoft Graph.

Nas seções a seguir, os principais aspectos do gerenciamento de usuário Azure AD B2C com a API Microsoft Graph são apresentados. As operações, os tipos e as propriedades da API Microsoft Graph apresentados aqui são um subconjunto do que aparece na documentação de referência da API do Microsoft Graph.

## <a name="register-a-management-application"></a>Registrar um aplicativo de gerenciamento

Antes que qualquer aplicativo de gerenciamento de usuário ou script que você escreve possa interagir com os recursos em seu locatário Azure AD B2C, você precisará de um registro de aplicativo que conceda as permissões para fazer isso.

Siga as etapas neste artigo de instruções para criar um registro de aplicativo que seu aplicativo de gerenciamento pode usar:

[Gerenciar Azure AD B2C com Microsoft Graph](microsoft-graph-get-started.md)

## <a name="user-management-microsoft-graph-operations"></a>Operações de Microsoft Graph de gerenciamento de usuário

As seguintes operações de gerenciamento de usuário estão disponíveis na [API de Microsoft Graph](https://docs.microsoft.com/graph/api/resources/user):

- [Obter uma lista de usuários](https://docs.microsoft.com/graph/api/user-list)
- [Criar um usuário](https://docs.microsoft.com/graph/api/user-post-users)
- [Obter um usuário](https://docs.microsoft.com/graph/api/user-get)
- [Atualizar um usuário](https://docs.microsoft.com/graph/api/user-update)
- [Excluir um usuário](https://docs.microsoft.com/graph/api/user-delete)

## <a name="user-properties"></a>Propriedades do usuário

### <a name="display-name-property"></a>Propriedade de nome de exibição

O `displayName` é o nome a ser exibido no gerenciamento de usuário portal do Azure para o usuário e, no token de acesso Azure AD B2C retorna ao aplicativo. Essa propriedade é obrigatória.

### <a name="identities-property"></a>Propriedade Identities

Uma conta de cliente, que pode ser um consumidor, parceiro ou cidadão, pode ser associada a esses tipos de identidade:

- Identidade **local** -o nome de usuário e a senha são armazenados localmente no diretório Azure ad B2C. Geralmente, nos referimos a essas identidades como "contas locais".
- Identidade **federada** – também conhecida como contas *corporativas* ou *sociais* , a identidade do usuário é gerenciada por um provedor de identidade federado, como Facebook, Microsoft, ADFS ou Salesforce.

Um usuário com uma conta de cliente pode entrar com várias identidades. Por exemplo, nome de usuário, email, ID do funcionário, ID do governo e outros. Uma única conta pode ter várias identidades, locais e sociais, com a mesma senha.

Na API Microsoft Graph, as identidades locais e federadas são armazenadas no atributo usuário `identities`, que é do tipo [objectidentity][graph-objectIdentity]. A coleção de `identities` representa um conjunto de identidades usadas para entrar em uma conta de usuário. Essa coleção permite que o usuário entre na conta de usuário com qualquer uma de suas identidades associadas.

| Propriedade   | Type |DESCRIÇÃO|
|:---------------|:--------|:----------|
|signInType|string| Especifica os tipos de entrada do usuário em seu diretório. Para a conta local: `emailAddress`, `emailAddress1`, `emailAddress2`, `emailAddress3`, `userName`ou qualquer outro tipo que desejar. A conta social deve ser definida como `federated`.|
|emissor|string|Especifica o emissor da identidade. Para contas locais (em que **signInType** não é `federated`), essa propriedade é o nome de domínio padrão do locatário B2C local, por exemplo `contoso.onmicrosoft.com`. Para a identidade social (em que **signInType** é `federated`), o valor é o nome do emissor, por exemplo `facebook.com`|
|issuerAssignedId|string|Especifica o identificador exclusivo atribuído ao usuário pelo emissor. A combinação de **emissor** e **issuerAssignedId** deve ser exclusiva em seu locatário. Para conta local, quando **signInType** é definido como `emailAddress` ou `userName`, ele representa o nome de entrada do usuário.<br>Quando **signInType** é definido como: <ul><li>`emailAddress` (ou começa com `emailAddress` como `emailAddress1`) **issuerAssignedId** deve ser um endereço de email válido</li><li>`userName` (ou qualquer outro valor), **issuerAssignedId** deve ser uma [parte local válida de um endereço de email](https://tools.ietf.org/html/rfc3696#section-3)</li><li>`federated`, **issuerAssignedId** representa o identificador exclusivo da conta federada</li></ul>|

Para identidades federadas, dependendo do provedor de identidade, o **issuerAssignedId** é um valor exclusivo para um determinado usuário por aplicativo ou conta de desenvolvimento. Configure a política de Azure AD B2C com a mesma ID de aplicativo que foi atribuída anteriormente pelo provedor social ou outro aplicativo dentro da mesma conta de desenvolvimento.

### <a name="password-profile-property"></a>Propriedade de perfil de senha

Para uma identidade local, a propriedade **passwordProfile** é necessária e contém a senha do usuário. A propriedade `forceChangePasswordNextSignIn` deve ser definida como `false`.

Para uma identidade federada (social), a propriedade **passwordProfile** não é necessária.

```JSON
"passwordProfile" : {
    "password": "password-value",
    "forceChangePasswordNextSignIn": false
  }
```

### <a name="password-policy-property"></a>Propriedade de política de senha

A política de senha de Azure AD B2C (para contas locais) é baseada na Azure Active Directory política de [força de senha forte](../active-directory/authentication/concept-sspr-policy.md) . As políticas de inscrição ou entrada e redefinição de senha do Azure AD B2C exigem essa força de senha forte e não expiram as senhas.

Em cenários de migração de usuário, se as contas que você deseja migrar tiverem uma intensidade de senha mais fraca do que a [força de senha forte](../active-directory/authentication/concept-sspr-policy.md) imposta por Azure ad B2C, você poderá desabilitar o requisito de senha forte. Para alterar a política de senha padrão, defina a propriedade `passwordPolicies` como `DisableStrongPassword`. Por exemplo, você pode modificar a solicitação de criação de usuário da seguinte maneira:

```JSON
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

### <a name="extension-properties"></a>Propriedades de extensão

Cada aplicativo voltado para o cliente tem requisitos exclusivos para que as informações sejam coletadas. Seu locatário de Azure AD B2C vem com um conjunto interno de informações armazenadas em Propriedades, como nome, sobrenome, cidade e CEP fornecidos. Com Azure AD B2C, você pode estender o conjunto de propriedades armazenadas em cada conta de cliente. Para obter mais informações sobre como definir atributos personalizados, consulte [atributos personalizados (fluxos de usuário)](user-flow-custom-attributes.md) e [atributos personalizados (políticas personalizadas)](custom-policy-custom-attributes.md).

Microsoft Graph API dá suporte à criação e atualização de um usuário com atributos de extensão. Atributos de extensão na API do Graph são nomeados usando a convenção `extension_ApplicationObjectID_attributename`. Por exemplo:

```JSON
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyNumber": "212342"
```

## <a name="code-sample"></a>Exemplo de código

Este exemplo de código é um aplicativo de console .NET Core que usa o [SDK Microsoft Graph](https://docs.microsoft.com/graph/sdks/sdks-overview) para interagir com Microsoft Graph API. Seu código demonstra como chamar a API para gerenciar programaticamente os usuários em um locatário Azure AD B2C.
Você pode [baixar o arquivo morto de exemplo](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management/archive/master.zip) (*. zip), [procurar o repositório](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management) no GitHub ou clonar o repositório:

```cmd
git clone https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management.git
```

Depois de obter o exemplo de código, configure-o para o seu ambiente e, em seguida, compile o projeto:

1. Abra o projeto no [Visual Studio](https://visualstudio.microsoft.com) ou [Visual Studio Code](https://code.visualstudio.com).
1. Abra o `src/appsettings.json`.
1. Na seção `appSettings`, substitua `your-b2c-tenant` pelo nome do seu locatário e `Application (client) ID` e `Client secret` pelos valores para o registro do aplicativo de gerenciamento (consulte a seção [registrar um aplicativo de gerenciamento](#register-a-management-application) deste artigo).
1. Abra uma janela de console no seu clone local do repositório, alterne para o diretório `src` e, em seguida, compile o projeto:
    ```console
    cd src
    dotnet build
    ```
1. Execute o aplicativo com o comando `dotnet`:

```console
dotnet bin/Debug/netcoreapp3.0/b2c-ms-graph.dll
```

O aplicativo exibe uma lista de comandos que você pode executar. Por exemplo, obter todos os usuários, obter um único usuário, excluir um usuário, atualizar a senha de um usuário e importação em massa.

### <a name="code-discussion"></a>Discussão de código

O código de exemplo usa o [SDK do Microsoft Graph](https://docs.microsoft.com/graph/sdks/sdks-overview), que foi projetado para simplificar a criação de aplicativos de alta qualidade, eficientes e resilientes que acessam Microsoft Graph. Portanto, você não precisa fazer um direcionamento de todas as Microsoft Graph API.

Qualquer solicitação para a API de Microsoft Graph requer um token de acesso para autenticação. A solução utiliza o pacote NuGet [Microsoft. Graph. auth](https://www.nuget.org/packages/Microsoft.Graph.Auth/) que fornece um wrapper baseado em cenário de autenticação da MSAL (biblioteca de autenticação da Microsoft) para uso com o SDK do Microsoft Graph.

O método `RunAsync` no arquivo _Program.cs_ :

1. Lê as configurações do aplicativo do arquivo _appSettings. JSON_
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

[Faça chamadas à API usando os SDKs de Microsoft Graph](https://docs.microsoft.com/graph/sdks/create-requests) inclui informações sobre como ler e gravar informações de Microsoft Graph, usar `$select` para controlar as propriedades retornadas, fornecer parâmetros de consulta personalizados e usar os parâmetros de consulta `$filter` e `$orderBy`.

## <a name="next-steps"></a>Próximas etapas

Para obter um índice completo das operações de API de Microsoft Graph com suporte para recursos de Azure AD B2C, consulte [Microsoft Graph operações disponíveis para Azure ad B2C](microsoft-graph-operations.md).

<!-- LINK -->

[graph-objectIdentity]: https://docs.microsoft.com/graph/api/resources/objectidentity
[graph-user]: (https://docs.microsoft.com/graph/api/resources/user)
