---
title: Login de aplicativo de página única & de entrada - plataforma de identidade da Microsoft | Azure
description: Saiba como construir um aplicativo de página única (login)
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/11/2020
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 8dd4d1aa2423ddb48f61380a982ca256609734d6
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80419649"
---
# <a name="single-page-application-sign-in-and-sign-out"></a>Aplicativo de página única: Entrada e login

Saiba como adicionar login ao código para o seu aplicativo de página única.

Antes de obter tokens para acessar APIs em seu aplicativo, você precisa de um contexto de usuário autenticado. Você pode fazer login com os usuários do seu aplicativo no MSAL.js de duas maneiras:

* [Janela pop-up](#sign-in-with-a-pop-up-window), `loginPopup` usando o método
* [Redirecionar](#sign-in-with-redirect), usando `loginRedirect` o método

Você também pode opcionalmente passar os escopos das APIs para as quais você precisa que o usuário consentir no momento da login.

> [!NOTE]
> Se o seu aplicativo já tiver acesso a um contexto de usuário autenticado ou token de ID, você pode pular a etapa de login e adquirir diretamente tokens. Para obter detalhes, consulte [SSO sem o login do MSAL.js](msal-js-sso.md#sso-without-msaljs-login).

## <a name="choosing-between-a-pop-up-or-redirect-experience"></a>Escolhendo entre uma experiência pop-up ou redirecionamento

Você não pode usar os métodos pop-up e redirecionamento em sua aplicação. A escolha entre uma experiência pop-up ou redirecionamento depende do fluxo de aplicativos:

* Se você não quiser que os usuários se afastem da página principal do aplicativo durante a autenticação, recomendamos o método pop-up. Como o redirecionamento de autenticação acontece em uma janela pop-up, o estado da aplicação principal é preservado.

* Se os usuários tiverem restrições ou políticas de navegador onde as janelas pop-up estão desativadas, você poderá usar o método de redirecionamento. Use o método de redirecionamento com o navegador Internet Explorer, porque existem [problemas conhecidos com janelas pop-up no Internet Explorer](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser).

## <a name="sign-in-with-a-pop-up-window"></a>Faça login com uma janela pop-up

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const loginRequest = {
    scopes: ["https://graph.microsoft.com/User.ReadWrite"]
}

userAgentApplication.loginPopup(loginRequest).then(function (loginResponse) {
    //login success
    let idToken = loginResponse.idToken;
}).catch(function (error) {
    //login failure
    console.log(error);
});
```

# <a name="angular"></a>[Angular](#tab/angular)

O invólucro MSAL Angular permite que você `MsalGuard` proteja rotas específicas em seu aplicativo adicionando à definição de rota. Este guarda invocará o método para entrar quando essa rota for acessada.

```javascript
// In app-routing.module.ts
import { NgModule } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';
import { ProfileComponent } from './profile/profile.component';
import { MsalGuard } from '@azure/msal-angular';
import { HomeComponent } from './home/home.component';

const routes: Routes = [
  {
    path: 'profile',
    component: ProfileComponent,
    canActivate: [
      MsalGuard
    ]
  },
  {
    path: '',
    component: HomeComponent
  }
];

@NgModule({
  imports: [RouterModule.forRoot(routes, { useHash: false })],
  exports: [RouterModule]
})
export class AppRoutingModule { }
```

Para uma experiência de janela `popUp` pop-up, habilite a opção de configuração. Você também pode passar os escopos que requerem consentimento da seguinte forma:

```javascript
// In app.module.ts
@NgModule({
    imports: [
        MsalModule.forRoot({
            auth: {
                clientId: 'your_app_id',
            }
        }, {
            popUp: true,
            consentScopes: ["https://graph.microsoft.com/User.ReadWrite"]
        })
    ]
})
```
---

## <a name="sign-in-with-redirect"></a>Faça login com redirecionamento

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Os métodos de redirecionamento não retornam uma promessa por causa do afastamento do aplicativo principal. Para processar e acessar os tokens retornados, você precisa registrar retornos de chamadas de sucesso e erro antes de chamar os métodos de redirecionamento.

```javascript
function authCallback(error, response) {
    //handle redirect response
}

userAgentApplication.handleRedirectCallback(authCallback);

const loginRequest = {
    scopes: ["https://graph.microsoft.com/User.ReadWrite"]
}

userAgentApplication.loginRedirect(loginRequest);
```

# <a name="angular"></a>[Angular](#tab/angular)

O código aqui é o mesmo descrito anteriormente na seção sobre o login com uma janela pop-up. O fluxo padrão é redirecionado.

> [!NOTE]
> O token de ID não contém os escopos consentidos e representa apenas o usuário autenticado. Os escopos consentidos são devolvidos no token de acesso, que você adquirirá na próxima etapa.

---

## <a name="sign-out"></a>Sair

A biblioteca MSAL `logout` fornece um método que limpa o cache no armazenamento do navegador e envia uma solicitação de saída para o Azure Active Directory (Azure AD). Após a saída, a biblioteca redireciona de volta para a página inicial do aplicativo por padrão.

Você pode configurar o URI para o qual ele `postLogoutRedirectUri`deve redirecionar após a saída de saída por configuração . Este URI também deve ser registrado como uri de logout no registro do seu aplicativo.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const userAgentApplication = new UserAgentApplication(config);
userAgentApplication.logout();

```

# <a name="angular"></a>[Angular](#tab/angular)

```javascript
//In app.module.ts
@NgModule({
    imports: [
        MsalModule.forRoot({
            auth: {
                clientId: 'your_app_id',
                postLogoutRedirectUri: "your_app_logout_redirect_uri"
            }
        })
    ]
})

// In app.component.ts
this.authService.logout();
```

---

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Adquirir um token para o aplicativo](scenario-spa-acquire-token.md)
