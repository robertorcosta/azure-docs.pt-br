---
title: Gerencie usuários com a API do Microsoft Graph
titleSuffix: Azure AD B2C
description: Como gerenciar usuários em um inquilino AD B2C do Azure ligando para a API do Microsoft Graph e usando uma identidade de aplicativo para automatizar o processo.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3bd166572cea23fbb710cd053c28f51e76ba534a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476664"
---
# <a name="manage-azure-ad-b2c-user-accounts-with-microsoft-graph"></a>Gerenciar contas de usuários Azure AD B2C com o Microsoft Graph

O Microsoft Graph permite que você gerencie contas de usuário em seu diretório Azure AD B2C, fornecendo métodos de criação, leitura, atualização e exclusão na API do Microsoft Graph. Você pode migrar uma loja de usuário existente para um inquilino Azure AD B2C e executar outras operações de gerenciamento de contas de usuário, chamando a API do Microsoft Graph.

Nas seções a seguir, são apresentados os principais aspectos do gerenciamento de usuários Do Azure AD B2C com a API do Microsoft Graph. As operações, tipos e propriedades da API do Microsoft Graph apresentadas aqui são um subconjunto do que aparece na documentação de referência da API do Microsoft Graph.

## <a name="register-a-management-application"></a>Registre um aplicativo de gerenciamento

Antes que qualquer aplicativo de gerenciamento de usuário ou script que você escreva possa interagir com os recursos do seu inquilino Azure AD B2C, você precisa de um registro de aplicativo que conceda as permissões para fazê-lo.

Siga as etapas deste artigo de como fazer para criar um registro de aplicativo que seu aplicativo de gerenciamento possa usar:

[Gerenciar a azure AD B2C com gráfico microsoft](microsoft-graph-get-started.md)

## <a name="user-management-microsoft-graph-operations"></a>Gerenciamento de usuários Operações do Microsoft Graph

As seguintes operações de gerenciamento de usuários estão disponíveis na [API do Microsoft Graph](https://docs.microsoft.com/graph/api/resources/user):

- [Obtenha uma lista de usuários](https://docs.microsoft.com/graph/api/user-list)
- [Criar um usuário](https://docs.microsoft.com/graph/api/user-post-users)
- [Obter um usuário](https://docs.microsoft.com/graph/api/user-get)
- [Atualize um usuário](https://docs.microsoft.com/graph/api/user-update)
- [Excluir um usuário](https://docs.microsoft.com/graph/api/user-delete)

## <a name="user-properties"></a>Propriedades do usuário

### <a name="display-name-property"></a>Propriedade do nome de exibição

O `displayName` é o nome a ser exibido no gerenciamento de usuário do portal Azure para o usuário, e no token de acesso Azure AD B2C retorna ao aplicativo. Essa propriedade é obrigatória.

### <a name="identities-property"></a>Propriedade de identidades

Uma conta de cliente, que pode ser um consumidor, parceiro ou cidadão, pode estar associada a esses tipos de identidade:

- **Identidade local** - O nome de usuário e a senha são armazenados localmente no diretório Ad B2C do Azure. Muitas vezes nos referimos a essas identidades como "contas locais".
- **Identidade federada** - Também conhecida como contas *sociais* ou *corporativas,* a identidade do usuário é gerenciada por um provedor de identidade federado como Facebook, Microsoft, ADFS ou Salesforce.

Um usuário com uma conta de cliente pode fazer login com várias identidades. Por exemplo, nome de usuário, e-mail, ID do funcionário, ID do governo e outros. Uma única conta pode ter várias identidades, tanto locais quanto sociais, com a mesma senha.

Na API do Microsoft Graph, as identidades locais `identities` e federadas são armazenadas no atributo do usuário, que é do [tipo objectIdentity][graph-objectIdentity]. A `identities` coleção representa um conjunto de identidades usadas para fazer login em uma conta de usuário. Essa coleção permite que o usuário faça login na conta do usuário com qualquer uma de suas identidades associadas.

| Propriedade   | Type |Descrição|
|:---------------|:--------|:----------|
|signInType|string| Especifica os tipos de login do usuário em seu diretório. Para conta `emailAddress`local: `emailAddress2` `emailAddress3`, `userName` `emailAddress1`, , , , ou qualquer outro tipo que você gosta. A conta social `federated`deve ser definida como .|
|emissor|string|Especifica o emissor da identidade. Para contas locais (quando **signInType** não `federated`estiver), esta propriedade é o `contoso.onmicrosoft.com`nome de domínio padrão do inquilino B2C local, por exemplo . Para a identidade social (onde **signInType** é `federated`) o valor é o nome do emissor, por exemplo`facebook.com`|
|emissorAssignedId|string|Especifica o identificador exclusivo atribuído ao usuário pelo emissor. A combinação de **emissor** e **emissorAssignedId** deve ser única dentro do seu inquilino. Para conta local, quando **o signInType** é definido ou `emailAddress` `userName`, ele representa o nome de login para o usuário.<br>Quando **o signInType** é definido como: <ul><li>`emailAddress`(ou começa `emailAddress` `emailAddress1`com como ) **emissorAssignedId** deve ser um endereço de e-mail válido</li><li>`userName`(ou qualquer outro valor), **o emissorAssignedId** deve ser uma parte local válida [de um endereço de e-mail](https://tools.ietf.org/html/rfc3696#section-3)</li><li>`federated`, **emissorAssignedId** representa o identificador único da conta federada</li></ul>|

A seguinte propriedade **identidades,** com uma identidade de conta local com um nome de login, um endereço de e-mail como login e com uma identidade social. 

 ```JSON
 "identities": [
     {
       "signInType": "userName",
       "issuer": "contoso.onmicrosoft.com",
       "issuerAssignedId": "johnsmith"
     },
     {
       "signInType": "emailAddress",
       "issuer": "contoso.onmicrosoft.com",
       "issuerAssignedId": "jsmith@yahoo.com"
     },
     {
       "signInType": "federated",
       "issuer": "facebook.com",
       "issuerAssignedId": "5eecb0cd"
     }
   ]
 ```

Para identidades federadas, dependendo do provedor de identidade, o **emissorAssignedId** é um valor único para um determinado usuário por aplicativo ou conta de desenvolvimento. Configure a diretiva Azure AD B2C com o mesmo ID de aplicativo que foi anteriormente atribuído pelo provedor social ou outro aplicativo dentro da mesma conta de desenvolvimento.

### <a name="password-profile-property"></a>Propriedade de perfil de senha

Para uma identidade local, a propriedade **passwordProfile** é necessária e contém a senha do usuário. A `forceChangePasswordNextSignIn` propriedade deve `false`ser definida como.

Para uma identidade federada (social), a propriedade **passwordProfile** não é necessária.

```JSON
"passwordProfile" : {
    "password": "password-value",
    "forceChangePasswordNextSignIn": false
  }
```

### <a name="password-policy-property"></a>Propriedade da política de senhas

A política de senha do Azure AD B2C (para contas locais) é baseada na política de [força de senha forte](../active-directory/authentication/concept-sspr-policy.md) do Azure Active Directory. As políticas de login ou redefinição de senha do Azure AD B2C exigem essa forte força de senha e não expiram senhas.

Em cenários de migração de usuários, se as contas que você deseja migrar tiverem uma força de senha mais fraca do que a [forte força de senha](../active-directory/authentication/concept-sspr-policy.md) imposta pelo Azure AD B2C, você pode desativar o forte requisito de senha. Para alterar a política de senha padrão, defina a propriedade `passwordPolicies` como `DisableStrongPassword`. Por exemplo, você pode modificar a solicitação de criação de usuário da seguinte maneira:

```JSON
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

### <a name="extension-properties"></a>Propriedades de extensão

Cada aplicativo voltado para o cliente tem requisitos exclusivos para que as informações sejam coletadas. O inquilino Azure AD B2C vem com um conjunto integrado de informações armazenadas em propriedades, como Nome dado, Sobrenome, Cidade e Código Postal. Com o Azure AD B2C, você pode estender o conjunto de propriedades armazenadas em cada conta do cliente. Para obter mais informações sobre a definição de atributos personalizados, consulte [atributos personalizados (fluxos de usuário)](user-flow-custom-attributes.md) e [atributos personalizados (políticas personalizadas).](custom-policy-custom-attributes.md)

A API do Microsoft Graph suporta a criação e atualização de um usuário com atributos de extensão. Atributos de extensão na API do Graph são nomeados usando a convenção `extension_ApplicationObjectID_attributename`. Por exemplo: 

```JSON
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyNumber": "212342"
```

## <a name="code-sample"></a>Exemplo de código

Esta amostra de código é um aplicativo de console .NET Core que usa o [Microsoft Graph SDK](https://docs.microsoft.com/graph/sdks/sdks-overview) para interagir com a API do Microsoft Graph. Seu código demonstra como chamar a API para gerenciar programáticamente os usuários em um inquilino Azure AD B2C.
Você pode [baixar o arquivo de exemplo](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management/archive/master.zip) (*.zip), navegar no [repositório](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management) no GitHub ou clonar o repositório:

```cmd
git clone https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management.git
```

Depois de obter a amostra de código, configure-a para o ambiente e, em seguida, construa o projeto:

1. Abra o projeto no [Visual Studio](https://visualstudio.microsoft.com) ou Visual [Studio Code](https://code.visualstudio.com).
1. Abra o `src/appsettings.json`.
1. Na `appSettings` seção, `your-b2c-tenant` substitua pelo nome `Application (client) ID` do `Client secret` seu inquilino e com os valores para o registro do seu aplicativo de gestão (consulte a seção [Registrar uma](#register-a-management-application) seção de aplicação de gestão deste artigo).
1. Abra uma janela de console dentro do clone `src` local do repo, mude para o diretório e construa o projeto:
    ```console
    cd src
    dotnet build
    ```
1. Execute o aplicativo com o comando `dotnet`:

    ```console
    dotnet bin/Debug/netcoreapp3.0/b2c-ms-graph.dll
    ```

O aplicativo exibe uma lista de comandos que você pode executar. Por exemplo, obtenha todos os usuários, obtenha um único usuário, exclua um usuário, atualize a senha de um usuário e importe em massa.

### <a name="code-discussion"></a>Discussão de código

O código de exemplo usa o [Microsoft Graph SDK](https://docs.microsoft.com/graph/sdks/sdks-overview), que foi projetado para simplificar a construção de aplicativos de alta qualidade, eficientes e resistentes que acessam o Microsoft Graph. Então, você não precisa fazer uma aPI direta do Microsoft Graph.

Qualquer solicitação à API do Microsoft Graph requer um token de acesso para autenticação. A solução faz uso do pacote [Microsoft.Graph.Auth](https://www.nuget.org/packages/Microsoft.Graph.Auth/) NuGet que fornece um invólucro baseado em cenário de autenticação da Microsoft Authentication Library (MSAL) para uso com o Microsoft Graph SDK.

O `RunAsync` método no arquivo _Program.cs:_

1. Lê as configurações do aplicativo a partir do arquivo _appsettings.json_
1. Inicializa o provedor auth usando o fluxo [de concessão de credenciais do cliente OAuth 2.0.](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md) Com o fluxo de concessão de credenciais do cliente, o aplicativo é capaz de obter um token de acesso para chamar a API do Microsoft Graph.
1. Configura o cliente de serviço do Microsoft Graph com o provedor auth:

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

O *GraphServiceClient* inicializado é então usado em _UserService.cs_ para executar as operações de gerenciamento de usuários. Por exemplo, obter uma lista das contas de usuário no inquilino:

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

[Faça chamadas de API usando os SDKs do Microsoft Graph](https://docs.microsoft.com/graph/sdks/create-requests) inclui informações `$select` sobre como ler e gravar informações do Microsoft `$filter` `$orderBy` Graph, usar para controlar as propriedades retornadas, fornecer parâmetros de consulta personalizados e usar os parâmetros e consulta.

## <a name="next-steps"></a>Próximas etapas

Para obter um índice completo das operações de API do Microsoft Graph suportadas para recursos Azure AD B2C, consulte [as operações do Microsoft Graph disponíveis para o Azure AD B2C](microsoft-graph-operations.md).

<!-- LINK -->

[graph-objectIdentity]: https://docs.microsoft.com/graph/api/resources/objectidentity
[graph-user]: (https://docs.microsoft.com/graph/api/resources/user)
