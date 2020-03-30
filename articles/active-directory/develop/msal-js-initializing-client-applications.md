---
title: Inicialize aplicativos clientes MSAL.js | Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre a inicialização de aplicativos clientes usando a Biblioteca de Autenticação microsoft para JavaScript (MSAL.js).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/12/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: a0a2c5fc971c3f1f3283d95c5617bdf1e88a6a58
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084031"
---
# <a name="initialize-client-applications-using-msaljs"></a>Inicialize aplicativos clientes usando MSAL.js
Este artigo descreve a inicialização da Biblioteca de Autenticação do Microsoft para JavaScript (MSAL.js) com uma instância de um aplicativo de agente de usuário. O aplicativo usuário-agente é uma forma de aplicativo cliente público no qual o código do cliente é executado em um usuário-agente, como um navegador da Web. Esses clientes não armazenam segredos, já que o contexto do navegador é abertamente acessível. Para saber mais sobre os tipos de aplicativos clientes e as opções de configuração do aplicativo, leia a [visão geral](msal-client-applications.md).

## <a name="prerequisites"></a>Pré-requisitos
Antes de inicializar um aplicativo, primeiro você precisa [registrá-lo no portal do Azure](scenario-spa-app-registration.md) para que seu aplicativo possa ser integrado à plataforma de identidade da Microsoft. Após o cadastro, você pode precisar das seguintes informações (que podem ser encontradas no portal azure):

- O ID do cliente (uma string representando um GUID para sua aplicação)
- O URL do provedor de identidade (chamado de instância) e o público de login do seu aplicativo. Esses dois parâmetros são coletivamente conhecidos como autoridade.
- O ID do inquilino se você estiver escrevendo um aplicativo de linha de negócios exclusivamente para sua organização (também chamado de aplicativo de inquilino único).
- Para aplicativos web, você também terá que definir o redirecionamentoUri onde o provedor de identidade retornará ao seu aplicativo com os tokens de segurança.

## <a name="initializing-applications"></a>Inicialização de aplicações

Você pode usar O MSAL.js da seguinte forma em um aplicativo JavaScript/Typescript simples. Inicialize o contexto de autenticação `UserAgentApplication` MSAL instanciando com um objeto de configuração. A configuração mínima necessária para inicializar o MSAL.js é o clientID do seu aplicativo que você deve obter no portal de registro de aplicativos.

Para métodos de autenticação`loginRedirect` com `acquireTokenRedirect`fluxos de redirecionamento (e), você `handleRedirectCallback()` precisará registrar explicitamente um retorno de chamada para sucesso ou erro através do método. Isso é necessário, uma vez que os fluxos de redirecionamento não retornam promessas como os métodos com uma experiência pop-up fazem.

```javascript
// Configuration object constructed
const config = {
    auth: {
        clientId: "abcd-ef12-gh34-ikkl-ashdjhlhsdg"
    }
}

// create UserAgentApplication instance
const myMSALObj = new UserAgentApplication(config);

function authCallback(error, response) {
    //handle redirect response
}

// (optional when using redirect methods) register redirect call back for Success or Error
myMSALObj.handleRedirectCallback(authCallback);
```

O MSAL.js foi projetado para ter `UserAgentApplication` uma única instância e configuração do para representar um único contexto de autenticação. Várias instâncias não são recomendadas, pois causam entradas de cache conflitantes e comportamento no navegador.

## <a name="configuration-options"></a>Opções de configuração

O MSAL.js tem um objeto de configuração mostrado abaixo que fornece `UserAgentApplication`um agrupamento de opções configuráveis disponíveis para criar uma instância de .

```javascript
type storage = "localStorage" | "sessionStorage";

// Protocol Support
export type AuthOptions = {
    clientId: string;
    authority?: string;
    validateAuthority?: boolean;
    redirectUri?: string | (() => string);
    postLogoutRedirectUri?: string | (() => string);
    navigateToLoginRequestUrl?: boolean;
};

// Cache Support
export type CacheOptions = {
    cacheLocation?: CacheLocation;
    storeAuthStateInCookie?: boolean;
};

// Library support
export type SystemOptions = {
    logger?: Logger;
    loadFrameTimeout?: number;
    tokenRenewalOffsetSeconds?: number;
    navigateFrameWait?: number;
};

// Developer App Environment Support
export type FrameworkOptions = {
    isAngular?: boolean;
    unprotectedResources?: Array<string>;
    protectedResourceMap?: Map<string, Array<string>>;
};

// Configuration Object
export type Configuration = {
    auth: AuthOptions,
    cache?: CacheOptions,
    system?: SystemOptions,
    framework?: FrameworkOptions
};
```

Abaixo está o conjunto total de opções configuráveis que são suportadas atualmente no objeto config:

- **clientID**: Obrigatório. O clienteID do seu aplicativo, você deve obtê-lo no portal de registro de aplicativos.

- **autoridade**: Opcional. Uma URL indicando um diretório do que o MSAL pode solicitar tokens. O valor padrão é: `https://login.microsoftonline.com/common`.
    * No Azure AD, é da&lt;&gt;/&lt;forma&gt;https:// &lt;&gt; audiência de instância , onde `https://login.microsoftonline.com`a &lt;&gt; instância é o domínio do provedor de identidade (por exemplo, ) e o público é um identificador representando o público-login. Estes podem ser os seguintes valores:
        * `https://login.microsoftonline.com/<tenant>`- inquilino é um domínio associado ao inquilino, como contoso.onmicrosoft.com, ou o GUID representando a `TenantID` propriedade do diretório usado apenas para assinar em usuários de uma organização específica.
        * `https://login.microsoftonline.com/common`- Costumava fazer login em usuários com contas de trabalho e escola ou uma conta pessoal da Microsoft.
        * `https://login.microsoftonline.com/organizations/`- Costumava fazer login em usuários com contas de trabalho e escola.
        * `https://login.microsoftonline.com/consumers/`- Costumava fazer login em usuários com apenas conta pessoal da Microsoft (ao vivo).
    * No Azure AD B2C, é `https://<instance>/tfp/<tenant>/<policyName>/`do formulário , onde a instância é o domínio Azure AD B2C ou seja. {seu inquilino-nome}.b2clogin.com, inquilino é o nome do inquilino Azure AD B2C i.e. {your-tenant-name}.onmicrosoft.com, policyName é o nome da política B2C a ser aplicada.


- **validarAutoridade**: Opcional.  Valide o emissor de tokens. O padrão é `true`. Para aplicações B2C, uma vez que o valor da autoridade é conhecido e pode ser `false`diferente por política, a validação da autoridade não funcionará e deve ser definida como .

- **redirecionamentoUri**: Opcional.  O URI de redirecionamento do seu aplicativo, onde as respostas de autenticação podem ser enviadas e recebidas pelo aplicativo. Ele deve corresponder exatamente a uma das URIs de redirecionamento que você registrou no portal. Usa `window.location.href` como padrão.

- **postLogoutRedirectUri**: Opcional.  Redireciona o usuário `postLogoutRedirectUri` para depois de sair. O padrão `redirectUri`é .

- **navegarToLoginRequestUrl**: Opcional. Capacidade de desativar a navegação padrão para iniciar a página após o login. O padrão é true. Isto é usado apenas para redirecionar fluxos.

- **cacheLocalização**: Opcional.  Define o armazenamento `localStorage` `sessionStorage`do navegador para ou . O padrão é `sessionStorage`.

- **storeAuthStateInCookie**: Opcional.  Este sinalizador foi introduzido no MSAL.js v0.2.2 como uma correção para os problemas de [loop de autenticação](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#1-issues-due-to-security-zones) no Microsoft Internet Explorer e no Microsoft Edge. Habilite `storeAuthStateInCookie` a bandeira para realtrue para tirar proveito desta correção. Quando isso estiver ativado, o MSAL.js armazenará o estado de solicitação de auth necessário para validação dos fluxos auth nos cookies do navegador. Por padrão, este `false`sinalizador é definido como .

- **madeireiro**: Opcional.  Um objeto Logger com uma instância de retorno de chamada que pode ser fornecido pelo desenvolvedor para consumir e publicar logs de forma personalizada. Para obter detalhes sobre a passagem do objeto logger, consulte [o registro com msal.js](msal-logging.md).

- **loadFrameTimeout**: Opcional.  O número de milissegundos de inatividade antes de uma resposta de renovação de token do Azure AD deve ser considerado esgotado. O padrão é 6 segundos.

- **tokenRenewalOffsetSeconds**: Opcional. O número de milissegundos que define a janela de deslocamento necessária para renovar o token antes do vencimento. O padrão é de 300 milissegundos.

- **navegarFrameWait**: Opcional. O número de milissegundos que define o tempo de espera antes que os iframes ocultos naveguem até o seu destino. O padrão é de 500 milissegundos.

Estes só são aplicáveis a serem passados a partir da biblioteca de invólucros MSAL Angular:
- **recursos desprotegidos**: Opcional.  Matriz de URIs que são recursos desprotegidos. A MSAL não anexará um token a solicitações de saída que tenham esses URI. Usa `null` como padrão.

- **protectedResourceMap**: Opcional.  Este é o mapeamento dos recursos para escopos usados pela MSAL para anexar automaticamente tokens de acesso em chamadas de API da Web. Um único token de acesso é obtido para o recurso. Assim, você pode mapear um caminho dehttps://graph.microsoft.com/v1.0/merecurso específico da seguinte forma: {" "," ["user.read"]}, ou a URL do aplicativo do recurso como: {"https://graph.microsoft.com/", ["user.read", "mail.send"]}. Isso é necessário para chamadas do CORS. Usa `null` como padrão.
