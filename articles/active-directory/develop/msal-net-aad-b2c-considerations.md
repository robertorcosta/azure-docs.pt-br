---
title: Azure AD B2C (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre considerações específicas ao usar o Azure AD B2C com a Biblioteca de Autenticação microsoft para .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/29/2019
ms.author: jeferrie
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: d31cf3a4e024dc59b865d096cbd0829d50f61a1a
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81533948"
---
# <a name="use-msalnet-to-sign-in-users-with-social-identities"></a>Use MSAL.NET para fazer login usuários com identidades sociais

Você pode usar MSAL.NET para fazer login usuários com identidades sociais usando [o Azure Active Directory B2C (Azure AD B2C)](https://aka.ms/aadb2c). O Azure AD B2C é construído em torno da noção de políticas. Em MSAL.NET, especificar uma política se traduz em fornecer uma autoridade.

- Quando você instancia o aplicativo do cliente público, você precisa especificar a política em autoridade.
- Quando você deseja aplicar uma política, você precisa `AcquireTokenInteractive` chamar `authority` uma substituição de conter um parâmetro.

Esta página é para MSAL 3.x. Se você estiver interessado no MSAL 2.x, consulte [as especificidades do Azure AD B2C no MSAL 2.x](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-Specifics-MSAL-2.x).

## <a name="authority-for-a-azure-ad-b2c-tenant-and-policy"></a>Autoridade para um inquilino e política Azure AD B2C

A autoridade para `https://{azureADB2CHostname}/tfp/{tenant}/{policyName}` usar é onde:

- `azureADB2CHostname`é o nome do inquilino Azure AD B2C `{your-tenant-name}.b2clogin.com`mais o host (por exemplo),
- `tenant`é o nome completo do inquilino Azure AD `{your-tenant-name}.onmicrosoft.com`B2C (por exemplo, ) ou o GUID para o inquilino,
- `policyName`o nome da política ou fluxo de usuário para solicitar (por exemplo, "b2c_1_susi" para a inscrição/login).

Para obter mais informações sobre as autoridades Azure AD B2C, consulte esta [documentação](/azure/active-directory-b2c/b2clogin).

## <a name="instantiating-the-application"></a>Instantivando o aplicativo

Ao construir o aplicativo, você precisa fornecer a autoridade.

```csharp
// Azure AD B2C Coordinates
public static string Tenant = "fabrikamb2c.onmicrosoft.com";
public static string AzureADB2CHostname = "fabrikamb2c.b2clogin.com";
public static string ClientID = "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6";
public static string PolicySignUpSignIn = "b2c_1_susi";
public static string PolicyEditProfile = "b2c_1_edit_profile";
public static string PolicyResetPassword = "b2c_1_reset";

public static string AuthorityBase = $"https://{AzureADB2CHostname}/tfp/{Tenant}/";
public static string Authority = $"{AuthorityBase}{PolicySignUpSignIn}";
public static string AuthorityEditProfile = $"{AuthorityBase}{PolicyEditProfile}";
public static string AuthorityPasswordReset = $"{AuthorityBase}{PolicyResetPassword}";

application = PublicClientApplicationBuilder.Create(ClientID)
               .WithB2CAuthority(Authority)
               .Build();
```

## <a name="acquire-a-token-to-apply-a-policy"></a>Adquira um token para aplicar uma política

A aquisição de um token para uma API protegida AD B2C do Azure em um aplicativo de cliente público exige que você use as substituições com uma autoridade:

```csharp
IEnumerable<IAccount> accounts = await application.GetAccountsAsync();
AuthenticationResult ar = await application .AcquireTokenInteractive(scopes)
                                            .WithAccount(GetAccountByPolicy(accounts, policy))
                                            .WithParentActivityOrWindow(ParentActivityOrWindow)
                                            .ExecuteAsync();
```

por:

- `policy`sendo uma das strings anteriores (por exemplo). `PolicySignUpSignIn`
- `ParentActivityOrWindow`é necessário para Android (a Atividade) e opcional para outras plataformas que suportam a iu principal, como windows no Windows e UIViewController no iOS. Veja mais informações [aqui no diálogo UI](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Acquiring-tokens-interactively#withparentactivityorwindow).
- `GetAccountByPolicy(IEnumerable<IAccount>, string)`é um método que encontra uma conta para uma determinada política. Por exemplo:

  ```csharp
  private IAccount GetAccountByPolicy(IEnumerable<IAccount> accounts, string policy)
  {
   foreach (var account in accounts)
   {
    string userIdentifier = account.HomeAccountId.ObjectId.Split('.')[0];
    if (userIdentifier.EndsWith(policy.ToLower()))
     return account;
   }
   return null;
  }
  ```

A aplicação de uma política ou fluxo de usuário (por exemplo, permitir que o `AcquireTokenInteractive`usuário final edite seu perfil ou redefinir sua senha) é feito atualmente por chamada . No caso dessas duas políticas, você não usa o resultado de token/autenticação retornado.

## <a name="special-case-of-editprofile-and-resetpassword-policies"></a>Caso especial de políticas EditProfile e ResetPassword

Quando você deseja fornecer uma experiência onde seus usuários finais fazem login com uma identidade social e, em seguida, editam seu perfil, você deseja aplicar a política de perfil de edição do Azure AD B2C. A maneira de fazer `AcquireTokenInteractive` isso é ligando com a autoridade `Prompt.NoPrompt` específica para essa política e um prompt definido para impedir que a caixa de diálogo de seleção da conta seja exibida (já que o usuário já está conectado e tem uma sessão de cookies ativa).

```csharp
private async void EditProfileButton_Click(object sender, RoutedEventArgs e)
{
 IEnumerable<IAccount> accounts = await app.GetAccountsAsync();
 try
 {
  var authResult = await app.AcquireToken(scopes:App.ApiScopes)
                               .WithAccount(GetUserByPolicy(accounts, App.PolicyEditProfile)),
                               .WithPrompt(Prompt.NoPrompt),
                               .WithB2CAuthority(App.AuthorityEditProfile)
                               .ExecuteAsync();
  DisplayBasicTokenInfo(authResult);
 }
 catch
 {
  . . .
 }
}
```
## <a name="resource-owner-password-credentials-ropc-with-azure-ad-b2c"></a>Credenciais de senha do proprietário de recursos (ROPC) com Azure AD B2C
Para obter mais detalhes sobre o fluxo ropc, consulte esta [documentação](v2-oauth-ropc.md).

Esse fluxo não é **recomendado** porque seu aplicativo pedindo a senha de um usuário não é seguro. Para obter mais informações sobre este problema, consulte [este artigo](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/).

Ao usar nome de usuário/senha, você está abrindo mão de uma série de coisas:
- Princípios centrais da identidade moderna: senha é pescada, repetida. Porque temos esse conceito de um segredo de compartilhamento que pode ser interceptado. Isso é incompatível com sem senha.
- Os usuários que precisam fazer MFA não poderão fazer login (já que não há interação).
- Os usuários não poderão fazer um único login.

### <a name="configure-the-ropc-flow-in-azure-ad-b2c"></a>Configure o fluxo ROPC no Azure AD B2C
No seu inquilino Azure AD B2C, crie um novo fluxo de usuário e selecione **Entrar usando ROPC**. Isso permitirá a política de ROPC para o seu inquilino. Consulte [Configurar o fluxo de credenciais de senha do proprietário do recurso](/azure/active-directory-b2c/configure-ropc) para obter mais detalhes.

`IPublicClientApplication`contém um método:
```csharp
AcquireTokenByUsernamePassword(
            IEnumerable<string> scopes,
            string username,
            SecureString password)
```

Este método toma como parâmetros:
- Os *escopos* para solicitar um token de acesso para.
- Um *nome de usuário*.
- Uma *senha* do SecureString para o usuário.

Lembre-se de usar a autoridade que contém a política ROPC.

### <a name="limitations-of-the-ropc-flow"></a>Limitações do fluxo de ROPC
 - O fluxo de ROPC **só funciona para contas locais** (onde você se registra no Azure AD B2C usando um e-mail ou nome de usuário). Esse fluxo não funciona se federar a qualquer um dos provedores de identidade suportados pelo Azure AD B2C (Facebook, Google, etc.).

## <a name="google-auth-and-embedded-webview"></a>Google Auth e Webview incorporado

Se você é um desenvolvedor Ad B2C do Azure usando o Google como provedor de identidade, remanejos você usa o navegador do sistema, pois o Google não permite [autenticação a partir de webviews incorporadas](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html). Atualmente, `login.microsoftonline.com` é uma autoridade confiável com o Google. O uso dessa autoridade funcionará com webview incorporado. No `b2clogin.com` entanto, o uso não é uma autoridade confiável com o Google, portanto, os usuários não poderão autenticar.

Forneceremos uma atualização para este [problema](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/688) se as coisas mudarem.

## <a name="caching-with-azure-ad-b2c-in-msalnet"></a>Cache com Azure AD B2C em MSAL.Net

### <a name="known-issue-with-azure-ad-b2c"></a>Problema conhecido com Azure AD B2C

MSAL.Net suporta um [cache de token](/dotnet/api/microsoft.identity.client.tokencache?view=azure-dotnet). A chave de cache de token é baseada nas reivindicações devolvidas pelo Provedor de Identidade. Atualmente, MSAL.Net precisa de duas reivindicações para construir uma chave de cache de token:
- `tid`que é o Azure AD Tenant ID, e
- `preferred_username`

Ambas as reivindicações estão faltando em muitos dos cenários Azure AD B2C.

O impacto do cliente é que ao tentar exibir o campo de nome de usuário, você está recebendo "Ausente da resposta do token" como o valor? Se assim for, isso é porque o Azure AD B2C não retorna um valor no IdToken para o preferred_username devido a limitações com as contas sociais e provedores de identidade externa (IdPs). O Azure AD retorna um valor para preferred_username porque ele sabe quem é o usuário, mas para o Azure AD B2C, porque o usuário pode fazer login com uma conta local, Facebook, Google, GitHub, etc. não há um valor consistente para o Azure AD B2C usar para preferred_username. Para desbloquear o MSAL de implementar a compatibilidade de cache com o ADAL, decidimos usar "Faltando à resposta do token" em nosso final ao lidar com as contas Azure AD B2C quando o IdToken não retorna nada para preferred_username. A MSAL deve devolver um valor para preferred_username para manter a compatibilidade de cache entre bibliotecas.

### <a name="workarounds"></a>Soluções alternativas

#### <a name="mitigation-for-the-missing-tenant-id"></a>Atenuação para o id inquilino desaparecido

A solução sugerida é usar o [Cache por Política](#acquire-a-token-to-apply-a-policy)

Alternativamente, você pode `tid` usar a reclamação, se estiver usando as [políticas personalizadas B2C,](https://aka.ms/ief)porque ela fornece a capacidade de retornar reivindicações adicionais ao aplicativo. Para saber mais sobre [transformação de sinistros](/azure/active-directory-b2c/claims-transformation-technical-profile)

#### <a name="mitigation-for-missing-from-the-token-response"></a>Atenuação para "Ausente da resposta do token"
Uma opção é usar a reivindicação "nome" como nome de usuário preferido. O processo é mencionado nesta [> doc B2C](../../active-directory-b2c/user-flow-overview.md) "Na coluna de reivindicação De devolução, escolha as reivindicações que deseja retornadas nos tokens de autorização enviados de volta ao seu aplicativo após uma experiência de edição de perfil bem-sucedida. Por exemplo, selecione Nome de exibição, Código Postal."

## <a name="next-steps"></a>Próximas etapas

Mais detalhes sobre a aquisição de tokens interativamente com MSAL.NET para aplicativos Azure AD B2C são fornecidos na amostra a seguir.

| Amostra | Plataforma | Descrição|
|------ | -------- | -----------|
|[ativo-diretório-b2c-xamarin-nativo](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) | Xamarin iOS, Xamarin Android, UWP | Um aplicativo xamarin forms simples mostrando como usar MSAL.NET para autenticar usuários via Azure AD B2C, e acessar uma API web com os tokens resultantes.|
