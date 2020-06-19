---
title: Fluxos de autenticação da MSAL | Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre os fluxos de autenticação e as concessões usadas pela MSAL (Biblioteca de Autenticação da Microsoft).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/18/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 47978317b1ae914e952b764def854d8a011293e0
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2020
ms.locfileid: "84266603"
---
# <a name="authentication-flows"></a>Fluxos de autenticação

Neste artigo, você verá uma descrição dos diferentes fluxos de autenticação disponíveis na MSAL (Biblioteca de Autenticação da Microsoft).  Esses fluxos podem ser usados em uma variedade de cenários de aplicativos diferentes.

| Flow | Descrição | Usado em|  
| ---- | ----------- | ------- | 
| [Interativo](#interactive) | Obtém o token por meio de um processo interativo que solicita as credenciais ao usuário por meio de um navegador ou janela pop-up. | [Aplicativos da área de trabalho](scenario-desktop-overview.md), [Aplicativos móveis](scenario-mobile-overview.md) |
| [Concessão implícita](#implicit-grant) | Permite que o aplicativo obtenha tokens sem executar uma troca de credenciais de servidor back-end. Assim, o aplicativo pode conectar o usuário, manter a sessão e obter tokens para outras APIs Web, tudo dentro do código JavaScript do cliente.| [SPA (aplicativos de página única)](scenario-spa-overview.md) |
| [Código de autorização](#authorization-code) | Usado em aplicativos instalados em um dispositivo para obter acesso a recursos protegidos, como APIs Web. Isso permite adicionar credenciais e acesso à API a aplicativos móveis e de área de trabalho. | [Aplicativos da área de trabalho](scenario-desktop-overview.md), [Aplicativos móveis](scenario-mobile-overview.md), [Aplicativos Web](scenario-web-app-call-api-overview.md) | 
| [On-behalf-of](#on-behalf-of) | Um aplicativo invoca um serviço ou uma API Web que, por sua vez, precisa chamar outro serviço ou API Web. A ideia é propagar as permissões e identidade de usuário delegado por meio da cadeia de solicitações. | [APIs da Web](scenario-web-api-call-api-overview.md) |
| [Credenciais do cliente](#client-credentials) | Permite acessar recursos hospedados na Web com a identidade de um aplicativo. Normalmente é usado para interações de servidor para servidor que devem ser executadas em segundo plano, sem interação imediata com um usuário. | [Aplicativos daemon](scenario-daemon-overview.md) |
| [Código do dispositivo](#device-code) | Permite que os usuários se conectem a dispositivos com restrição de entrada, como uma smart TV, um dispositivo IoT ou uma impressora. | [Aplicativos de área de trabalho/móveis](scenario-desktop-acquire-token.md#command-line-tool-without-a-web-browser) |
| [Autenticação Integrada do Windows](scenario-desktop-acquire-token.md#integrated-windows-authentication) | Permite que os aplicativos em computadores ingressados no domínio ou no Azure AD (Azure Active Directory) adquiram um token silenciosamente (sem qualquer interação com a interface do usuário).| [Aplicativos de área de trabalho/móveis](scenario-desktop-acquire-token.md#integrated-windows-authentication) |
| [Nome de usuário/senha](scenario-desktop-acquire-token.md#username-and-password) | Permite que um aplicativo conecte o usuário, processando diretamente a senha dele. Esse fluxo não é recomendado. | [Aplicativos de área de trabalho/móveis](scenario-desktop-acquire-token.md#username-and-password) |

## <a name="how-each-flow-emits-tokens-and-codes"></a>Como cada fluxo emite tokens e códigos
 
Dependendo de como o cliente é criado, ele pode usar um (ou vários) dos fluxos de autenticação compatíveis com a plataforma de identidade da Microsoft.  Esses fluxos podem produzir uma variedade de tokens (id_tokens, tokens de atualização, tokens de acesso), bem como códigos de autorização. Além disso, podem exigir tokens diferentes para fazê-los funcionar. Veja uma visão geral neste gráfico:
 
|Flow | Exige | id_token | o token de acesso | token de atualização | código de autorização | 
|-----|----------|----------|--------------|---------------|--------------------|
|[Fluxo do código de autorização](v2-oauth2-auth-code-flow.md) | | x | x | x | x|  
|[Fluxo implícito](v2-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[Fluxo de OIDC híbrido](v2-protocols-oidc.md#protocol-diagram-access-token-acquisition)| | x  | |          |            x   |
|[Resgate de token de atualização](v2-oauth2-auth-code-flow.md#refresh-the-access-token) | token de atualização | x | x | x| |
|[Fluxo em-nome-de](v2-oauth2-on-behalf-of-flow.md) | o token de acesso| x| x| x| |
|[Fluxo de código do dispositivo](v2-oauth2-device-code.md) | | x| x| x| |
|[Credenciais do cliente](v2-oauth2-client-creds-grant-flow.md) | | | x (somente de aplicativo)| | |
 
Tokens emitidos no modo implícito têm uma limitação de comprimento, porque são passados de volta para o navegador por meio da URL (em que `response_mode` é `query` ou `fragment`).  Alguns navegadores têm um limite de tamanho da URL que pode ser digitada na barra do navegador e falham quando ela é muito longa.  Portanto, esses tokens não têm declarações de `groups` ou `wids`.

## <a name="interactive"></a>Interativo

A MSAL é compatível com a capacidade de solicitar interativamente as credenciais do usuário para entrar e obter um token usando essas credenciais.

![Diagrama de fluxo interativo](media/msal-authentication-flows/interactive.png)

Para saber mais sobre como usar a MSAL.NET para adquirir tokens de modo interativo em plataformas específicas, confira:
- [Xamarin Android](msal-net-xamarin-android-considerations.md)
- [Xamarin iOS](msal-net-xamarin-ios-considerations.md)
- [Plataforma Universal do Windows](msal-net-uwp-considerations.md)

Para saber mais sobre as chamadas interativas na MSAL.js, confira [Comportamento de solicitação nas solicitações interativas do MSAL.js](msal-js-prompt-behavior.md).

## <a name="implicit-grant"></a>Concessão implícita

A MSAL é compatível com o [fluxo de concessão implícita do OAuth 2](v2-oauth2-implicit-grant-flow.md), que permite ao aplicativo obter tokens da plataforma de identidade da Microsoft sem realizar uma troca de credenciais do servidor back-end. Assim, o aplicativo pode conectar o usuário, manter a sessão e obter tokens para outras APIs Web, tudo dentro do código JavaScript do cliente.

![Diagrama de fluxo de concessão implícita](media/msal-authentication-flows/implicit-grant.svg)

Muitos aplicativos Web modernos são criados como aplicativos de página única do lado do cliente e escritos com JavaScript ou uma estrutura SPA, como Angular, Vue.js e React.js. Esses aplicativos são executados em um navegador da Web e têm características de autenticação diferentes dos aplicativos Web do lado do servidor tradicionais. A plataforma de identidade da Microsoft permite que aplicativos de página única conectem usuários e obtenham tokens para acessar serviços de back-end ou APIs Web usando o fluxo de concessão implícita. O fluxo implícito permite que o aplicativo obtenha tokens de ID para representar o usuário autenticado e também os tokens de acesso necessários para chamar APIs protegidas.

Esse fluxo de autenticação não inclui cenários de aplicativos que usam estruturas JavaScript multiplataforma, como Electron e React-Native, porque exigem mais recursos para interação com as plataformas nativas.

## <a name="authorization-code"></a>Código de autorização

A MSAL é compatível com a [concessão de código de autorização do OAuth 2](v2-oauth2-auth-code-flow.md). Essa concessão pode ser usada em aplicativos instalados em um dispositivo para obter acesso a recursos protegidos, como APIs Web. Isso permite adicionar credenciais e acesso à API a aplicativos móveis e de área de trabalho. 

Quando os usuários entram em aplicativos Web (sites), este recebe um código de autorização.  O código de autorização é resgatado para adquirir um token para chamar APIs Web. Em aplicativos Web ASP.NET e ASP.NET Core, a única meta de `AcquireTokenByAuthorizationCode` é adicionar um token ao cache de tokens. O token pode ser usado pelo aplicativo (geralmente nos controladores, que apenas obtêm um token para uma API usando `AcquireTokenSilent`).

![Diagrama de fluxo de código de autorização](media/msal-authentication-flows/authorization-code.png)

No diagrama anterior, o aplicativo:

1. Solicita um código de autorização, que é resgatado para um token de acesso.
2. Usa o token de acesso para chamar a API Web.

### <a name="considerations"></a>Considerações

- É possível usar o código de autorização apenas uma vez para resgatar um token. Não tente adquirir um token várias vezes com o mesmo código de autorização (ele é explicitamente proibido pela especificação de protocolo padrão). Se você resgatar o código várias vezes intencionalmente ou porque não está ciente de que uma estrutura também faz isso para você, receberá o seguinte erro: `AADSTS70002: Error validating credentials. AADSTS54005: OAuth2 Authorization code was already redeemed, please retry with a new valid code or use an existing refresh token.`

- Ao escrever um aplicativo ASP.NET ou ASP.NET Core, isso poderá acontecer se você não informar à estrutura que já resgatou o código de autorização. Para isso, você precisa chamar o método `context.HandleCodeRedemption()` do manipulador de eventos `AuthorizationCodeReceived`.

- Evite compartilhar o token de acesso com ASP.NET, o que pode impedir que o consentimento incremental ocorra corretamente. Para saber mais, confira o [problema 693](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/693).

## <a name="on-behalf-of"></a>On-behalf-of

A MSAL é compatível com o [fluxo de autenticação em nome do usuário do OAuth 2](v2-oauth2-on-behalf-of-flow.md).  Esse fluxo é usado quando um aplicativo invoca um serviço ou API Web que, por sua vez, precisa chamar outro serviço ou API Web. A ideia é propagar as permissões e identidade de usuário delegado por meio da cadeia de solicitações. Para o serviço de camada intermediária fazer solicitações autenticadas para o serviço downstream, ele precisa proteger um token de acesso da plataforma de identidade da Microsoft em nome do usuário.

![Diagrama do fluxo On-behalf-of](media/msal-authentication-flows/on-behalf-of.png)

No diagrama anterior:

1. O aplicativo adquire um token de acesso para a API Web.
2. Um cliente (Web, área de trabalho, móvel ou aplicativo de página única) chama uma API Web protegida, adicionando o token de acesso como um token de portador ao cabeçalho de autenticação da solicitação HTTP. A API Web autentica o usuário.
3. Quando o cliente chama a API Web, ela solicita outro token em nome do usuário.  
4. A API Web protegida usa esse token para chamar uma API Web downstream em nome do usuário.  A API Web também pode solicitar tokens para outras APIs downstream (mas ainda em nome do mesmo usuário).

## <a name="client-credentials"></a>Credenciais do cliente

A MSAL é compatível com o [fluxo de credenciais de cliente do OAuth 2](v2-oauth2-client-creds-grant-flow.md). Esse fluxo permite acessar recursos hospedados na Web com a identidade de um aplicativo. Esse tipo de concessão normalmente é usado para interações de servidor para servidor que devem ser executadas em segundo plano, sem interação imediata com um usuário. Esses tipos de aplicativo normalmente são chamados de daemons ou contas de serviço. 

O fluxo de concessão de credenciais do cliente permite que um serviço Web (um cliente confidencial) use suas próprias credenciais, em vez de representar um usuário, para autenticar ao chamar outro serviço Web. Nesse cenário, o cliente é geralmente um serviço Web de camada intermediária, um serviço daemon ou um site. Para um nível mais alto de garantia, a plataforma de identidade da Microsoft também permite que o serviço de chamada use um certificado (em vez de um segredo compartilhado) como uma credencial.

> [!NOTE]
> O fluxo do cliente confidencial não está disponível nas plataformas móveis (UWP, Xamarin.iOS e Xamarin.Android), porque só são compatíveis com aplicativos cliente públicos. Aplicativos cliente públicos não sabem como comprovar a identidade do aplicativo para o provedor de identidade. Uma conexão segura pode ser obtida em back-ends de aplicativo Web ou de API Web com a implantação de um certificado.

A MSAL.NET é compatível com dois tipos de credenciais de cliente. Essas credenciais de cliente precisam ser registradas no Azure AD. As credenciais são passadas para os construtores do aplicativo cliente confidencial em seu código.

### <a name="application-secrets"></a>Segredos do aplicativo

![Diagrama de cliente confidencial com senha](media/msal-authentication-flows/confidential-client-password.png)

No diagrama anterior, o aplicativo:

1. Adquire um token usando credenciais de senha ou de segredo do aplicativo.
2. Usa o token para fazer solicitações do recurso.

### <a name="certificates"></a>Certificados

![Diagrama de cliente confidencial com certificado](media/msal-authentication-flows/confidential-client-certificate.png)

No diagrama anterior, o aplicativo:

1. Adquire um token usando credenciais de certificado.
2. Usa o token para fazer solicitações do recurso.

Essas credenciais de cliente precisam ser:
- Registradas no Azure AD.
- Passadas na construção do aplicativo cliente confidencial em seu código.

## <a name="device-code"></a>Código do dispositivo

A MSAL é compatível com o [fluxo de código do dispositivo do OAuth 2](v2-oauth2-device-code.md), que permite aos usuários se conectar a dispositivos com restrição de entrada, como uma smart TV, um dispositivo IoT ou uma impressora. A autenticação interativa com o Azure AD requer um navegador da Web. O fluxo de código do dispositivo permite que o usuário use outro dispositivo (por exemplo, outro computador ou um celular) para entrar interativamente quando o dispositivo ou o sistema operacional não fornece um navegador da Web.

Com o fluxo de código do dispositivo, o aplicativo obtém tokens por meio de um processo de duas etapas especialmente projetado para esses dispositivos ou sistemas operacionais. Aplicativos em execução em dispositivos IoT ou CLIs (ferramentas de linha de comando) são exemplos. 

![Diagrama do fluxo de código do dispositivo](media/msal-authentication-flows/device-code.png)

No diagrama anterior:

1. Sempre que a autenticação do usuário é necessária, o aplicativo fornece um código e solicita que o usuário use outro dispositivo (como um smartphone conectado à Internet) para acessar uma URL (por exemplo, `https://microsoft.com/devicelogin`). Em seguida, o usuário deve inserir o código e prosseguir com uma experiência de autenticação normal, incluindo solicitações de consentimento e [autenticação multifator](../authentication/concept-mfa-howitworks.md), se necessário.

2. Após a autenticação bem-sucedida, o aplicativo de linha de comando recebe os tokens necessários por meio de um canal de apoio e os usa para executar as chamadas à API Web necessárias.

### <a name="constraints"></a>Restrições

- O fluxo de código do dispositivo só está disponível em aplicativos cliente públicos.
- A autoridade passada na construção do aplicativo cliente público deve ser uma das seguintes:
  - Com locatários (do formulário `https://login.microsoftonline.com/{tenant}/` em que `{tenant}` é o GUID que representa a ID do locatário ou um domínio associado ao locatário).
  - Para contas corporativas ou de estudante (`https://login.microsoftonline.com/organizations/`).
- As contas pessoais da Microsoft ainda não são compatíveis com o ponto de extremidade do Azure AD v2.0. Não é possível usar os locatários `/common` ou `/consumers`.

## <a name="integrated-windows-authentication"></a>Autenticação Integrada do Windows

A MSAL é compatível com a IWA (Autenticação Integrada do Windows) para aplicativos móveis ou de área de trabalho executados em um computador Windows ingressado no domínio ou no Azure AD. Por meio da IWA, esses aplicativos podem adquirir um token silenciosamente (sem nenhuma interação com a interface do usuário). 

![Diagrama da Autenticação Integrada do Windows](media/msal-authentication-flows/integrated-windows-authentication.png)

No diagrama anterior, o aplicativo:

1. Adquire um token usando a Autenticação Integrada do Windows.
2. Usa o token para fazer solicitações do recurso.

### <a name="constraints"></a>Restrições

A IWA é compatível somente com usuários federados, ou seja, os usuários criados no Active Directory e apoiados pelo Azure AD. Os usuários criados diretamente no Azure AD, sem apoio do Active Directory (usuários gerenciados) não podem usar esse fluxo de autenticação. Essa limitação não afeta o [fluxo de nome de usuário/senha](#usernamepassword).

A IWA é para aplicativos escritos para as plataformas .NET Framework, .NET Core e Plataforma Universal do Windows.

A IWA não ignora a autenticação multifator. Se a autenticação multifator estiver configurada, a IWA poderá falhar se um desafio de autenticação multifator for exigido. A autenticação multifator exige interação do usuário.

Você não controla o momento em que o provedor de identidade exige que a autenticação de dois fatores seja realizada. Mas o administrador de locatário controla. Normalmente, a autenticação de dois fatores é necessária quando você entra em um país/região diferente, quando você não está conectado via VPN a uma rede corporativa e, às vezes, até quando você está conectado via VPN. O Azure AD usa IA para aprender continuamente se a autenticação de dois fatores é necessária. Se a IWA falhar, você deverá retornar a um [prompt de usuário interativo] (#interactive).

A autoridade passada na construção do aplicativo cliente público deve ser uma das seguintes:
- Com locatários (do formulário `https://login.microsoftonline.com/{tenant}/` em que `tenant` é o GUID que representa a ID do locatário ou um domínio associado ao locatário).
- Para contas corporativas ou de estudante (`https://login.microsoftonline.com/organizations/`). Não há compatibilidade com as contas pessoais da Microsoft (não é possível usar os locatários `/common` ou `/consumers`).

Como a IWA é um fluxo silencioso, uma das seguintes afirmações deve ser verdadeira:
- O usuário do seu aplicativo deve ter consentido o uso do aplicativo anteriormente. 
- O administrador de locatários deve ter consentido o uso do aplicativo anteriormente para todos os usuários no locatário.

Isso significa que uma das afirmações a seguir é verdadeira:
- Você, como desenvolvedor, selecionou **Conceder** no portal do Azure por conta própria.
- Um administrador de locatários selecionou **Conceder/revogar consentimento do administrador para {domínio de locatário}** na guia **Permissões de API** do registro do aplicativo. Confira [Adicionar permissões para acessar APIs Web](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).
- Você forneceu uma forma de os usuários consentirem com o aplicativo (confira [Solicitar consentimento do usuário individual](v2-permissions-and-consent.md#requesting-individual-user-consent)).
- Você forneceu uma forma de o administrador do locatário consentir com o aplicativo (confira [Consentimento do administrador](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)).

O fluxo da IWA está habilitado para aplicativos de área de trabalho do .NET, o .NET Core e a Plataforma Universal do Windows. No .NET Core, é preciso fornecer o nome de usuário para a IWA, pois ele não pode obter nomes de usuários do sistema operacional.
  
Para saber mais sobre o consentimento, confira [Permissões e consentimento v2.0](v2-permissions-and-consent.md).

## <a name="usernamepassword"></a>Nome de usuário/senha

A MSAL é compatível com a [concessão de credenciais de senha de proprietário do recurso do OAuth 2](v2-oauth-ropc.md), que permite a um aplicativo conectar o usuário manipulando diretamente a senha dele. Em seu aplicativo de área de trabalho, é possível usar o fluxo de nome de usuário/senha para adquirir um token silenciosamente. Não é necessária uma interface do usuário ao usar o aplicativo.

![Diagrama do fluxo de nome de usuário/senha](media/msal-authentication-flows/username-password.png)

No diagrama anterior, o aplicativo:

1. Adquire um token ao enviar o nome de usuário e a senha ao provedor de identidade.
2. Chama a API Web usando o token.

> [!WARNING]
> Esse fluxo não é recomendado. Ele requer um alto grau de confiança e exposição do usuário.  Use esse fluxo somente quando outros mais seguros não puderem ser usados. Para saber mais, confira [Qual é a solução para o problema crescente das senhas?](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/). 

O fluxo preferencial para adquirir um token silenciosamente em computadores Windows ingressados no domínio é a [Autenticação Integrada do Windows](#integrated-windows-authentication). Caso contrário, também é possível usar o [Fluxo de código do dispositivo](#device-code).

Embora isso seja útil em alguns casos (cenários de DevOps), se você quiser usar o nome de usuário/senha em cenários interativos em que você disponibiliza sua própria interface do usuário, tente evitá-lo. Ao usar o nome de usuário/senha:
- Os usuários que precisam realizar a autenticação multifator não poderão entrar (pois não há interação).
- Os usuários não poderão fazer logon único.

### <a name="constraints"></a>Restrições

Além das [restrições da Autenticação Integrada do Windows](#integrated-windows-authentication), as restrições a seguir também se aplicam:

- O fluxo de nome de usuário/senha não é compatível com o Acesso Condicional e a autenticação multifator. Como consequência, se o aplicativo for executado em um locatário do Azure AD em que o administrador de locatários exige autenticação multifator, você não poderá usar esse fluxo. Muitas organizações fazem isso.
- Mas só funciona para contas corporativas e de estudante (e não contas Microsoft).
- O fluxo está disponível no desktop do .NET e no .NET Core, mas não na Plataforma Universal do Windows.

### <a name="azure-ad-b2c-specifics"></a>Especificações do Azure AD B2C

Para saber mais sobre como usar o ROPC na MSAL.NET e no Azure AD B2C, confira [Usar o ROPC com o Azure AD B2C](msal-net-aad-b2c-considerations.md#resource-owner-password-credentials-ropc).
