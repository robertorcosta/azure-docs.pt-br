---
title: Único sinal (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Aprenda a construir experiências de login único usando a Biblioteca de Autenticação microsoft para JavaScript (MSAL.js).
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/24/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 8080d4cf4c3f0091f7837b3fccead5474c42db55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262848"
---
# <a name="single-sign-on-with-msaljs"></a>Logon único com o MSAL.js

O SSO (Single Sign-On) permite que os usuários insiram suas credenciais uma vez para fazer login e estabelecer uma sessão que pode ser reutilizada em vários aplicativos sem exigir autenticar novamente. Isso fornece uma experiência perfeita para o usuário e reduz as solicitações repetidas de credenciais.

O Azure AD fornece recursos de SSO para aplicativos definindo um cookie de sessão quando o usuário autenticar pela primeira vez. A biblioteca MSAL.js permite que os aplicativos aproveitem isso de algumas maneiras.

## <a name="sso-between-browser-tabs"></a>SSO entre guias do navegador

Quando seu aplicativo está aberto em várias guias e você primeiro faz login no usuário em uma guia, o usuário também é conectado nas outras guias sem ser solicitado. O MSAL.js armazena o token DeI `localStorage` para o usuário no navegador e fará login do usuário no aplicativo nas outras guias abertas.

Por padrão, o MSAL.js usa `sessionStorage` o que não permite que a sessão seja compartilhada entre guias. Para obter SSO entre as guias, certifique-se de definir o `cacheLocation` em MSAL.js para `localStorage` como mostrado abaixo.

```javascript
const config = {
    auth: {
        clientId: "abcd-ef12-gh34-ikkl-ashdjhlhsdg"
    },
    cache: {
        cacheLocation: 'localStorage'
    }
}

const myMSALObj = new UserAgentApplication(config);
```

## <a name="sso-between-apps"></a>SSO entre aplicativos

Quando um usuário autentica, um cookie de sessão é definido no domínio Azure AD no navegador. O MSAL.js conta com este cookie de sessão para fornecer SSO para o usuário entre diferentes aplicativos. O MSAL.js também armazena os tokens de ID e os tokens de acesso do usuário no domínio de armazenamento do navegador por aplicativo. Como resultado, o comportamento do SSO varia para diferentes casos:  

### <a name="applications-on-the-same-domain"></a>Aplicações no mesmo domínio

Quando os aplicativos são hospedados no mesmo domínio, o usuário pode entrar em um aplicativo uma vez e, em seguida, ser autenticado para os outros aplicativos sem um prompt. O MSAL.js aproveita os tokens armazenados em cache para que o usuário no domínio forneça SSO.

### <a name="applications-on-different-domain"></a>Aplicativos em diferentes domínios

Quando os aplicativos são hospedados em diferentes domínios, os tokens armazenados em cache no domínio A não podem ser acessados por MSAL.js no domínio B.

Isso significa que, quando os usuários fizerem o registro no domínio A navegar em um aplicativo no domínio B, eles serão redirecionados ou solicitados com a página Azure AD. Como o Azure AD ainda tem o cookie de sessão do usuário, ele entrará no usuário e eles não terão que reinserir as credenciais. Se o usuário tiver várias contas de usuário em sessão com o Azure AD, o usuário será solicitado a escolher a conta relevante para fazer login.

### <a name="automatically-select-account-on-azure-ad"></a>Selecione automaticamente a conta no Azure AD

Em certos casos, o aplicativo tem acesso ao contexto de autenticação do usuário e deseja evitar o prompt de seleção de contas Azure AD quando várias contas são logadas.  Isso pode ser feito de algumas maneiras diferentes:

**Usando o ID de sessão (SID)**

ID de sessão é uma [reclamação opcional](active-directory-optional-claims.md) que pode ser configurada nos tokens de ID. Esta reclamação permite que o aplicativo identifique a sessão Azure AD do usuário independente mente do nome da conta do usuário ou nome de usuário. Você pode passar o SID nos `acquireTokenSilent` parâmetros de solicitação para a chamada. Isso permitirá que o Azure AD contorne a seleção da conta. O SID está vinculado ao cookie de sessão e não cruzará os contextos do navegador.

```javascript
var request = {
    scopes: ["user.read"],
    sid: sid
}

userAgentApplication.acquireTokenSilent(request).then(function(response) {
        const token = response.accessToken
    }
).catch(function (error) {  
        //handle error
});
```

> [!Note]
> O SID só pode ser usado `acquireTokenSilent` com solicitações de autenticação silenciosa feitas por chamada em MSAL.js.
Você pode encontrar as etapas para configurar reivindicações opcionais no manifesto do seu aplicativo [aqui](active-directory-optional-claims.md).

**Usando a dica de login**

Se você não tiver a reclamação SID configurada ou precisar ignorar o prompt de seleção de conta em `domain_hint` chamadas `extraQueryParameters` de autenticação interativas, você`loginPopup` `loginRedirect`pode `acquireTokenPopup` `acquireTokenRedirect`fazê-lo fornecendo um `login_hint` parâmetro de solicitação e, opcionalmente, um como nos métodos interativos MSAL.js ( , e ). Por exemplo: 

```javascript
var request = {
    scopes: ["user.read"],
    loginHint: preferred_username,
    extraQueryParameters: {domain_hint: 'organizations'}
}

userAgentApplication.loginRedirect(request);
```

Você pode obter os valores para login_hint e domain_hint lendo as reclamações retornadas no token id para o usuário.

* **loginHint** deve ser `preferred_username` definido como a reclamação no token ID.

* **domain_hint** só é necessário ser aprovado quando se usa a autoridade /comum. A dica de domínio é determinada por ID de inquilino(tid).  Se `tid` a reivindicação no token `9188040d-6c67-4c5b-b112-36a304b66dad` id é que são os consumidores. Caso contrário, são as organizações.

Leia [aqui](v2-oauth2-implicit-grant-flow.md) para mais informações sobre os valores para dica de login e sugestão de domínio.

> [!Note]
> Você não pode passar SID e login_hint ao mesmo tempo. Isso resultará em resposta a erros.

## <a name="sso-without-msaljs-login"></a>SSO sem login Do MSAL.js

Por design, o MSAL.js requer que um método de login seja chamado para estabelecer um contexto de usuário antes de obter tokens para APIs. Como os métodos de login são interativos, o usuário vê um prompt.

Existem certos casos em que os aplicativos têm acesso ao contexto do usuário autenticado ou token de ID através da autenticação iniciada em outro aplicativo e querem aproveitar o SSO para adquirir tokens sem antes fazer login através do MSAL.js.

Um exemplo disso é: um usuário é conectado a um aplicativo web pai que hospeda outro aplicativo JavaScript em execução como um complemento ou plugin.

A experiência do SSO neste cenário pode ser alcançada da seguinte forma:

Passe `sid` o se `login_hint` disponível `domain_hint`(ou e opcionalmente) como `acquireTokenSilent` parâmetros de solicitação para a chamada MSAL.js da seguinte forma:

```javascript
var request = {
    scopes: ["user.read"],
    loginHint: preferred_username,
    extraQueryParameters: {domain_hint: 'organizations'}
}

userAgentApplication.acquireTokenSilent(request).then(function(response) {
        const token = response.accessToken
    }
).catch(function (error) {  
        //handle error
});
```

## <a name="sso-in-adaljs-to-msaljs-update"></a>SSO em ADAL.js para atualização MSAL.js

O MSAL.js traz paridade de recursos com ADAL.js para cenários de autenticação Azure AD. Para facilitar a migração do ADAL.js para o MSAL.js e evitar que seus usuários façam login novamente, a biblioteca lê o token de ID representando a sessão do usuário no cache ADAL.js e sinaliza perfeitamente no usuário em MSAL.js.  

Para aproveitar o comportamento de assinatura única (SSO) ao atualizar do ADAL.js, você `localStorage` precisará garantir que as bibliotecas estejam usando para cachedetos. Defina `cacheLocation` `localStorage` o para dodo na configuração MSAL.js e ADAL.js na inicialização da seguinte forma:


```javascript
// In ADAL.js
window.config = {
   clientId: 'g075edef-0efa-453b-997b-de1337c29185',
   cacheLocation: 'localStorage'
};

var authContext = new AuthenticationContext(config);


// In latest MSAL.js version
const config = {
    auth: {
        clientId: "abcd-ef12-gh34-ikkl-ashdjhlhsdg"
    },
    cache: {
        cacheLocation: 'localStorage'
    }
}

const myMSALObj = new UserAgentApplication(config);
```

Uma vez configurado, o MSAL.js poderá ler o estado em cache do usuário autenticado no ADAL.js e usá-lo para fornecer SSO no MSAL.js.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre os valores [de vida útil de uma única sessão de login e token](active-directory-configurable-token-lifetimes.md) no Azure AD.
