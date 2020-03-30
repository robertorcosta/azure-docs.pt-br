---
title: Fluxos de autenticação MSAL | Azure
titleSuffix: Microsoft identity platform
description: Conheça os fluxos de autenticação e subsídios usados pela Microsoft Authentication Library (MSAL).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/30/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 25c219bedbbbec9fbc0c5617c7bd9fc482faf49a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050505"
---
# <a name="authentication-flows"></a>Fluxos de autenticação

Este artigo descreve os diferentes fluxos de autenticação fornecidos pela Microsoft Authentication Library (MSAL).  Esses fluxos podem ser usados em uma variedade de diferentes cenários de aplicação.

| Flow | Descrição | Usado em|  
| ---- | ----------- | ------- | 
| [Interativo](#interactive) | Obtém o token através de um processo interativo que solicita ao usuário credenciais através de um navegador ou janela pop-up. | [Aplicativos de desktop,](scenario-desktop-overview.md) [aplicativos móveis](scenario-mobile-overview.md) |
| [Concessão implícita](#implicit-grant) | Permite que o aplicativo obtenha tokens sem realizar uma troca de credenciais de servidor back-end. Isso permite que o aplicativo faça login no usuário, mantenha a sessão e obtenha tokens para outras APIs da Web, tudo dentro do código JavaScript do cliente.| [SPA (aplicativos de página única)](scenario-spa-overview.md) |
| [Código de autorização](#authorization-code) | Usado em aplicativos que são instalados em um dispositivo para obter acesso a recursos protegidos, como APIs web. Isso permite que você adicione acesso de login e API aos seus aplicativos móveis e desktop. | [Aplicativos de desktop,](scenario-desktop-overview.md) [aplicativos móveis,](scenario-mobile-overview.md) [aplicativos web](scenario-web-app-call-api-overview.md) | 
| [Em nome de](#on-behalf-of) | Um aplicativo invoca um serviço ou Uma API web, que por sua vez precisa chamar outro serviço ou API web. A ideia é propagar as permissões e identidade de usuário delegado por meio da cadeia de solicitações. | [APIs da Web](scenario-web-api-call-api-overview.md) |
| [Credenciais do cliente](#client-credentials) | Permite que você acesse recursos hospedados na Web usando a identidade de um aplicativo. Comumente usado para interações de servidor para servidor que deve ser executado em segundo plano, sem interação imediata com um usuário. | [Aplicativos Daemon](scenario-daemon-overview.md) |
| [Código do dispositivo](#device-code) | Permite que os usuários entrem em dispositivos com restrição de entrada, como uma smart TV, dispositivo IoT ou impressora. | [Aplicativos desktop/mobile](scenario-desktop-acquire-token.md#command-line-tool-without-a-web-browser) |
| [Autenticação Integrada do Windows](scenario-desktop-acquire-token.md#integrated-windows-authentication) | Permite que aplicativos no domínio ou Azure Active Directory (Azure AD) se juntem aos computadores para adquirir um token silenciosamente (sem qualquer interação de Interface do Usuário).| [Aplicativos desktop/mobile](scenario-desktop-acquire-token.md#integrated-windows-authentication) |
| [Nome de usuário/senha](scenario-desktop-acquire-token.md#username-and-password) | Permite que um aplicativo faça login no usuário manuseando diretamente sua senha. Esse fluxo não é recomendado. | [Aplicativos desktop/mobile](scenario-desktop-acquire-token.md#username-and-password) |

## <a name="how-each-flow-emits-tokens-and-codes"></a>Como cada fluxo emite tokens e códigos
 
Dependendo de como seu cliente é construído, ele pode usar um (ou vários) dos fluxos de autenticação suportados pela plataforma de identidade Microsoft.  Esses fluxos podem produzir uma variedade de tokens (id_tokens, tokens de atualização, tokens de acesso) bem como códigos de autorização, e exigem diferentes tokens para fazê-los funcionar. Este gráfico fornece uma visão geral:
 
|Flow | Requer | id_token | o token de acesso | token de atualização | código de autorização | 
|-----|----------|----------|--------------|---------------|--------------------|
|[Fluxo de código de autorização](v2-oauth2-auth-code-flow.md) | | x | x | x | x|  
|[Fluxo implícito](v2-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[Fluxo oidc híbrido](v2-protocols-oidc.md#get-access-tokens)| | x  | |          |            x   |
|[Atualizar resgate de tokens](v2-oauth2-auth-code-flow.md#refresh-the-access-token) | token de atualização | x | x | x| |
|[Fluxo em nome de](v2-oauth2-on-behalf-of-flow.md) | o token de acesso| x| x| x| |
|[Fluxo de código do dispositivo](v2-oauth2-device-code.md) | | x| x| x| |
|[Credenciais do cliente](v2-oauth2-client-creds-grant-flow.md) | | | x (somente para aplicativos)| | |
 
Os tokens emitidos através do modo implícito têm uma limitação de `response_mode` `query` comprimento `fragment`devido ao ser passado de volta para o navegador através da URL (onde está ou ).  Alguns navegadores têm um limite no tamanho da URL que pode ser colocado na barra do navegador e falhar quando for muito longo.  Assim, esses tokens `groups` não `wids` têm ou reivindicações.

## <a name="interactive"></a>Interativo

A MSAL suporta a capacidade de solicitar interativamente ao usuário que suas credenciais entrem e obtenham um token usando essas credenciais.

![Diagrama de fluxo interativo](media/msal-authentication-flows/interactive.png)

Para obter mais informações sobre como usar MSAL.NET para adquirir tokens interativamente em plataformas específicas, consulte:
- [Xamarin Android](msal-net-xamarin-android-considerations.md)
- [Xamarin iOS](msal-net-xamarin-ios-considerations.md)
- [Plataforma Universal do Windows](msal-net-uwp-considerations.md)

Para obter mais informações sobre chamadas interativas no MSAL.js, consulte [O comportamento imediato em solicitações interativas do MSAL.js](msal-js-prompt-behavior.md).

## <a name="implicit-grant"></a>Concessão implícita

A MSAL suporta o [fluxo de concessão implícito OAuth 2](v2-oauth2-implicit-grant-flow.md), que permite que o aplicativo obtenha tokens da plataforma de identidade microsoft sem realizar uma troca de credenciais de servidor back-end. Isso permite que o aplicativo faça login no usuário, mantenha a sessão e obtenha tokens para outras APIs da Web, tudo dentro do código JavaScript do cliente.

![Diagrama do fluxo implícito de subvenção](media/msal-authentication-flows/implicit-grant.svg)

Muitos aplicativos web modernos são construídos como aplicativos de página única do lado do cliente, escritos usando JavaScript ou uma estrutura SPA como Angular, Vue.js e React.js. Esses aplicativos são executados em um navegador da Web e têm características de autenticação diferentes dos aplicativos web tradicionais do lado do servidor. A plataforma de identidade da Microsoft permite que aplicativos de página única entrem nos usuários e obtenham tokens para acessar serviços back-end ou APIs da Web, usando o fluxo de subvenção implícito. O fluxo implícito permite que o aplicativo obtenha tokens de ID para representar o usuário autenticado, e também acessar tokens necessários para chamar APIs protegidas.

Esse fluxo de autenticação não inclui cenários de aplicativos que usam frameworks JavaScript multiplataforma, como Electron e React-Native, porque exigem recursos adicionais para interação com as plataformas nativas.

## <a name="authorization-code"></a>Código de autorização

A MSAL suporta a concessão do [código de autorização OAuth 2](v2-oauth2-auth-code-flow.md). Essa concessão pode ser usada em aplicativos instalados em um dispositivo para obter acesso a recursos protegidos, como APIs da Web. Isso permite que você adicione acesso de login e API aos seus aplicativos móveis e desktop. 

Quando os usuários fazem login em aplicativos web (sites), o aplicativo web recebe um código de autorização.  O código de autorização é resgatado para adquirir um token para chamar APIs da Web. Em ASP.NET e ASP.NET aplicativos web `AcquireTokenByAuthorizationCode` Core, o único objetivo é adicionar um token ao cache de tokens. O token pode então ser usado pelo aplicativo (geralmente nos controladores, que apenas `AcquireTokenSilent`recebem um token para uma API usando ).

![Diagrama do fluxo de código de autorização](media/msal-authentication-flows/authorization-code.png)

No diagrama anterior, a aplicação:

1. Solicita um código de autorização, que é resgatado por um token de acesso.
2. Usa o token de acesso para chamar uma API web.

### <a name="considerations"></a>Considerações

- Você pode usar o código de autorização apenas uma vez para resgatar um token. Não tente adquirir um token várias vezes com o mesmo código de autorização (é explicitamente proibido pela especificação padrão do protocolo). Se você resgatar o código várias vezes intencionalmente, ou porque você não está ciente de que uma estrutura também o faz para você, você terá o seguinte erro:`AADSTS70002: Error validating credentials. AADSTS54005: OAuth2 Authorization code was already redeemed, please retry with a new valid code or use an existing refresh token.`

- Se você estiver escrevendo um aplicativo ASP.NET ou ASP.NET Core, isso pode acontecer se você não disser à estrutura que já resgatou o código de autorização. Para isso, você precisa `context.HandleCodeRedemption()` chamar `AuthorizationCodeReceived` o método do manipulador de eventos.

- Evite compartilhar o token de acesso com ASP.NET, o que pode impedir que o consentimento incremental aconteça corretamente. Para obter mais informações, consulte [#693 de problemas](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/693).

## <a name="on-behalf-of"></a>Em nome de

A MSAL suporta o [fluxo de autenticação OAuth 2 em nome do .](v2-oauth2-on-behalf-of-flow.md)  Esse fluxo é usado quando um aplicativo invoca um serviço ou Uma API web, que por sua vez precisa chamar outro serviço ou API web. A ideia é propagar as permissões e identidade de usuário delegado por meio da cadeia de solicitações. Para que o serviço intermediário faça solicitações autenticadas ao serviço downstream, ele precisa garantir um token de acesso da plataforma de identidade Microsoft, em nome do usuário.

![Diagrama de fluxo em nome](media/msal-authentication-flows/on-behalf-of.png)

No diagrama anterior:

1. O aplicativo adquire um token de acesso para a API web.
2. Um cliente (aplicativo web, desktop, móvel ou de uma página única) chama uma API da Web protegida, adicionando o token de acesso como um token portador no cabeçalho de autenticação da solicitação HTTP. A API web autentica o usuário.
3. Quando o cliente chama a API web, a API web solicita outro token em nome do usuário.  
4. A API web protegida usa esse token para chamar uma API web downstream em nome do usuário.  A API da Web também pode solicitar tokens posteriores para outras APIs downstream (mas ainda em nome do mesmo usuário).

## <a name="client-credentials"></a>Credenciais do cliente

A MSAL suporta o [fluxo de credenciais de cliente OAuth 2](v2-oauth2-client-creds-grant-flow.md). Esse fluxo permite que você acesse recursos hospedados na Web usando a identidade de um aplicativo. Esse tipo de concessão normalmente é usado para interações de servidor para servidor que devem ser executadas em segundo plano, sem interação imediata com um usuário. Esses tipos de aplicativo normalmente são referidos como daemons ou contas de serviço. 

As credenciais do cliente concedem fluxo permite que um serviço web (um cliente confidencial) use suas próprias credenciais, em vez de se passar por um usuário, para autenticar ao chamar outro serviço web. Neste cenário, o cliente é tipicamente um serviço web de nível intermediário, um serviço daemon ou um site. Para um nível mais alto de garantia, a plataforma de identidade da Microsoft também permite que o serviço de chamada use um certificado (em vez de um segredo compartilhado) como uma credencial.

> [!NOTE]
> O fluxo de clientes confidenciais não está disponível nas plataformas móveis (UWP, Xamarin.iOS e Xamarin.Android), porque estes só suportam aplicativos públicos de clientes. Os aplicativos de clientes públicos não sabem como provar a identidade do aplicativo para o Provedor de Identidade. Uma conexão segura pode ser alcançada no aplicativo web ou nas extremidades da API da Web, implantando um certificado.

MSAL.NET suporta dois tipos de credenciais de cliente. Essas credenciais de cliente precisam ser registradas no Azure AD. As credenciais são passadas para os construtores do aplicativo cliente confidencial em seu código.

### <a name="application-secrets"></a>Segredos do aplicativo

![Diagrama de cliente confidencial com senha](media/msal-authentication-flows/confidential-client-password.png)

No diagrama anterior, a aplicação:

1. Adquire um token usando credenciais secretas de aplicativo ou senha.
2. Usa o token para fazer solicitações do recurso.

### <a name="certificates"></a>Certificados

![Diagrama de cliente confidencial com cert](media/msal-authentication-flows/confidential-client-certificate.png)

No diagrama anterior, a aplicação:

1. Adquire um token usando credenciais de certificado.
2. Usa o token para fazer solicitações do recurso.

Essas credenciais de cliente precisam ser:
- Registrado no Azure AD.
- Passou na construção do aplicativo de cliente confidencial em seu código.

## <a name="device-code"></a>Código do dispositivo

A MSAL suporta o fluxo de código do [dispositivo OAuth 2,](v2-oauth2-device-code.md)que permite que os usuários entrem em dispositivos com restrição de entrada, como uma smart TV, dispositivo IoT ou impressora. A autenticação interativa com o Azure AD requer um navegador da Web. O fluxo de código do dispositivo permite que o usuário use outro dispositivo (por exemplo, outro computador ou um celular) para fazer login de forma interativa, onde o dispositivo ou sistema operacional não fornece um navegador da Web.

Usando o fluxo de código do dispositivo, o aplicativo obtém tokens através de um processo de duas etapas especialmente projetado para esses dispositivos ou sistemas operacionais. Exemplos desses aplicativos incluem aqueles executados em dispositivos IoT ou ferramentas de linha de comando (CLI). 

![Diagrama do fluxo de código do dispositivo](media/msal-authentication-flows/device-code.png)

No diagrama anterior:

1. Sempre que a autenticação do usuário é necessária, o aplicativo fornece um código e pede ao usuário que use `https://microsoft.com/devicelogin`outro dispositivo (como um smartphone conectado à internet) para ir a uma URL (por exemplo, ). Em seguida, o usuário é solicitado a inserir o código e prossegue através de uma experiência normal de autenticação, incluindo solicitações de consentimento e autenticação multifatorial, se necessário.

2. Após a autenticação bem-sucedida, o aplicativo de linha de comando recebe os tokens necessários através de um canal de trás e os usa para executar as chamadas da API da Web de que precisa.

### <a name="constraints"></a>Restrições

- O fluxo de código do dispositivo só está disponível em aplicativos de clientes públicos.
- A autoridade aprovada na construção do pedido público de cliente deve ser uma das seguintes:
  - Inquilino (do formulário `https://login.microsoftonline.com/{tenant}/` `{tenant}` onde está o GUID representando o ID do inquilino ou um domínio associado ao inquilino).
  - Para qualquer trabalho e`https://login.microsoftonline.com/organizations/`contas escolares ( ).
- As contas pessoais da Microsoft ainda não são suportadas pelo ponto final do Azure `/consumers` AD v2.0 (você não pode usar o ou os `/common` inquilinos).

## <a name="integrated-windows-authentication"></a>Autenticação Integrada do Windows

O MSAL suporta IWA (Integrated Windows Authentication, autenticação integrada do Windows) para aplicativos desktop ou móveis que são executados em um domínio unido ou o Azure AD se juntou ao computador Windows. Usando iWA, esses aplicativos podem adquirir um token silenciosamente (sem qualquer interação de interface do usuário). 

![Diagrama de Autenticação Integrada do Windows](media/msal-authentication-flows/integrated-windows-authentication.png)

No diagrama anterior, a aplicação:

1. Adquire um token usando autenticação integrada do Windows.
2. Usa o token para fazer solicitações do recurso.

### <a name="constraints"></a>Restrições

O IWA suporta apenas usuários federados, o que significa que os usuários criados no Active Directory e apoiados pelo Azure AD. Os usuários criados diretamente no Azure AD, sem o backup do Active Directory (usuários gerenciados) não podem usar esse fluxo de autenticação. Essa limitação não afeta o [fluxo de nome de usuário/senha](#usernamepassword).

O IWA é para aplicativos escritos para plataformas .NET Framework, .NET Core e Universal Windows Platform.

O IWA não contorna a autenticação multifatorial. Se a autenticação multifatorial estiver configurada, o IWA pode falhar se for necessário um desafio de autenticação de vários fatores. A autenticação multifatorial requer interação do usuário.

Você não controla quando o provedor de identidade solicita a autenticação de dois fatores a ser realizada. O inquilino do governo sim. Normalmente, a autenticação de dois fatores é necessária quando você faz login em um país diferente, quando você não está conectado via VPN a uma rede corporativa e, às vezes, mesmo quando você está conectado via VPN. O Azure AD usa a IA para aprender continuamente se a autenticação de dois fatores é necessária. Se o IWA falhar, você deve voltar para um [prompt de usuário interativo] (#interactive).

A autoridade aprovada na construção do pedido público de cliente deve ser uma das seguintes:
- Inquilino (do formulário `https://login.microsoftonline.com/{tenant}/` `tenant` onde está o guid representando o ID do inquilino ou um domínio associado ao inquilino).
- Para qualquer trabalho e`https://login.microsoftonline.com/organizations/`contas escolares ( ). As contas pessoais da Microsoft não são `/common` `/consumers` suportadas (você não pode usar ou inquilinos).

Como o IWA é um fluxo silencioso, um dos seguintes deve ser verdade:
- O usuário do seu aplicativo deve ter consentido anteriormente em usar o aplicativo. 
- O inquilino deve ter consentido previamente com todos os usuários do inquilino para usar o aplicativo.

Isso significa que um dos seguintes é verdadeiro:
- Você, como desenvolvedor, selecionou **Grant** no portal Azure para si mesmo.
- Um admin inquilino selecionou **o consentimento de grant/revoke admin para {domínio de inquilino}** na guia de **permissões** de API do registro para o aplicativo (consulte [Adicionar permissões para acessar APIs da Web](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)).
- Você forneceu uma maneira de os usuários consentirem com o aplicativo (ver [Solicitando consentimento individual do usuário](v2-permissions-and-consent.md#requesting-individual-user-consent)).
- Você forneceu uma maneira para o inquilino admin consentir para o pedido (ver [consentimento de admin](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)).

O fluxo IWA está habilitado para aplicativos de desktop .NET, .NET Core e Windows Universal Platform. No .NET Core, você deve fornecer o nome de usuário para IWA, porque o .NET Core não pode obter nomes de usuário do sistema operacional.
  
Para obter mais informações sobre consentimento, consulte [permissões v2.0 e consentimento](v2-permissions-and-consent.md).

## <a name="usernamepassword"></a>Nome de usuário/senha

A MSAL suporta a concessão de [credenciais de senha do proprietário de recursos OAuth 2,](v2-oauth-ropc.md)que permite que um aplicativo faça login no usuário manipulando diretamente sua senha. Em seu aplicativo de desktop, você pode usar o fluxo de nome de usuário/senha para adquirir um token silenciosamente. Nenhuma interface do ui é necessária ao usar o aplicativo.

![Diagrama do fluxo de nome de usuário/senha](media/msal-authentication-flows/username-password.png)

No diagrama anterior, a aplicação:

1. Adquire um token enviando o nome de usuário e senha para o provedor de identidade.
2. Chama uma API web usando o token.

> [!WARNING]
> Esse fluxo não é recomendado. Requer um alto grau de confiança e exposição do usuário.  Você só deve usar este fluxo quando outros fluxos mais seguros não podem ser usados. Para obter mais informações, [veja qual é a solução para o crescente problema das senhas?](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/). 

O fluxo preferido para adquirir um token silenciosamente em máquinas com domínio do Windows é [a Autenticação Integrada do Windows](#integrated-windows-authentication). Caso contrário, você também pode usar [o fluxo de código do dispositivo](#device-code).

Embora isso seja útil em alguns casos (cenários DevOps), se você quiser usar nome de usuário/senha em cenários interativos onde você fornece sua própria uI, tente evitá-la. Usando nome de usuário/senha:
- Os usuários que precisam fazer autenticação multifatorial não poderão fazer login (já que não há interação).
- Os usuários não poderão fazer um único login.

### <a name="constraints"></a>Restrições

Além das [restrições integradas de autenticação do Windows,](#integrated-windows-authentication)as seguintes restrições também se aplicam:

- O fluxo de nome de usuário/senha não é compatível com acesso condicional e autenticação multifatorial. Como conseqüência, se o seu aplicativo for executado em um inquilino Azure AD onde o administrador do inquilino requer autenticação multifatorial, você não poderá usar esse fluxo. Muitas organizações fazem isso.
- Funciona apenas para contas de trabalho e escola (não contas microsoft).
- O fluxo está disponível na área de trabalho .NET e no .NET Core, mas não na Universal Windows Platform.

### <a name="azure-ad-b2c-specifics"></a>Especificidades do Azure AD B2C

Para obter mais informações sobre como usar MSAL.NET e Azure AD B2C, consulte [Usando ROPC com Azure AD B2C (MSAL.NET)](msal-net-aad-b2c-considerations.md#resource-owner-password-credentials-ropc-with-azure-ad-b2c).
