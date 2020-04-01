---
title: Adquira um token para chamar uma API web (aplicativos de página única) - plataforma de identidade da Microsoft | Azure
description: Saiba como criar um aplicativo de página única (adquira um token para chamar uma API)
services: active-directory
documentationcenter: dev-center-name
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/20/2019
ms.author: negoe
ms.custom: aaddev
ms.openlocfilehash: 393c3a06a2366a7d6947faf8bbfe038d6c5982fc
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80419654"
---
# <a name="single-page-application-acquire-a-token-to-call-an-api"></a>Aplicativo de página única: Adquira um token para chamar uma API

O padrão para adquirir tokens para APIs com MSAL.js é primeiro `acquireTokenSilent` tentar uma solicitação de token silencioso usando o método. Quando esse método é chamado, a biblioteca primeiro verifica o cache no armazenamento do navegador para ver se existe um token válido e o devolve. Quando nenhum token válido está no cache, ele envia uma solicitação de token silenciosa para o Azure Active Directory (Azure AD) a partir de um iframe oculto. Este método também permite que a biblioteca renove tokens. Para obter mais informações sobre a sessão de login único e os valores de vida útil do token no Azure AD, consulte [Token lifetimes](active-directory-configurable-token-lifetimes.md).

As solicitações silenciosas de token ao Azure AD podem falhar por razões como uma sessão ad do Azure vencida ou uma alteração de senha. Nesse caso, você pode invocar um dos métodos interativos (que solicitará ao usuário) para adquirir tokens:

* [Janela pop-up,](#acquire-a-token-with-a-pop-up-window)usando`acquireTokenPopup`
* [Redirecionar](#acquire-a-token-with-a-redirect), usando`acquireTokenRedirect`

## <a name="choose-between-a-pop-up-or-redirect-experience"></a>Escolha entre uma experiência pop-up ou redirecionamento

 Você não pode usar os métodos pop-up e redirecionamento em sua aplicação. A escolha entre uma experiência pop-up ou redirecionamento depende do fluxo de aplicativos:

* Se você não quiser que os usuários se afastem da página principal do aplicativo durante a autenticação, recomendamos o método pop-up. Como o redirecionamento de autenticação acontece em uma janela pop-up, o estado da aplicação principal é preservado.

* Se os usuários tiverem restrições ou políticas de navegador onde as janelas pop-ups estão desativadas, você poderá usar o método de redirecionamento. Use o método de redirecionamento com o navegador Internet Explorer, porque existem [problemas conhecidos com janelas pop-up no Internet Explorer](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser).

Você pode definir os escopos de API que deseja que o token de acesso inclua quando estiver construindo a solicitação de token de acesso. Observe que todos os escopos solicitados podem não ser concedidos no token de acesso. Isso depende do consentimento do usuário.

## <a name="acquire-a-token-with-a-pop-up-window"></a>Adquira um token com uma janela pop-up

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O código a seguir combina o padrão descrito anteriormente com os métodos para uma experiência pop-up:

```javascript
const accessTokenRequest = {
    scopes: ["user.read"]
}

userAgentApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    // Call API with token
    let accessToken = accessTokenResponse.accessToken;
}).catch(function (error) {
    //Acquire token silent failure, and send an interactive request
    if (error.errorMessage.indexOf("interaction_required") !== -1) {
        userAgentApplication.acquireTokenPopup(accessTokenRequest).then(function(accessTokenResponse) {
            // Acquire token interactive success
        }).catch(function(error) {
            // Acquire token interactive failure
            console.log(error);
        });
    }
    console.log(error);
});
```

# <a name="angular"></a>[Angular](#tab/angular)

O invólucro MSAL Angular fornece o interceptador HTTP, que automaticamente adquirirá tokens de acesso silenciosamente e os anexará às solicitações HTTP às APIs.

Você pode especificar os escopos `protectedResourceMap` para APIs na opção de configuração. `MsalInterceptor`solicitará esses escopos ao adquirir automaticamente tokens.

```javascript
// app.module.ts
@NgModule({
  declarations: [
    // ...
  ],
  imports: [
    // ...
    MsalModule.forRoot({
      auth: {
        clientId: 'Enter_the_Application_Id_Here',
      }
    },
    {
      popUp: !isIE,
      consentScopes: [
        'user.read',
        'openid',
        'profile',
      ],
      protectedResourceMap: [
        ['https://graph.microsoft.com/v1.0/me', ['user.read']]
      ]
    })
  ],
  providers: [
    {
      provide: HTTP_INTERCEPTORS,
      useClass: MsalInterceptor,
      multi: true
    }
  ],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

Para o sucesso e o fracasso da aquisição silenciosa do token, a MSAL Angular fornece retornos de chamada aos que você pode assinar. Também é importante lembrar de cancelar a inscrição.

```javascript
// In app.component.ts
 ngOnInit() {
    this.subscription=  this.broadcastService.subscribe("msal:acquireTokenFailure", (payload) => {
    });
}

ngOnDestroy() {
   this.broadcastService.getMSALSubject().next(1);
   if (this.subscription) {
     this.subscription.unsubscribe();
   }
 }
```

Alternativamente, você pode adquirir explicitamente tokens usando os métodos de aquisição-token conforme descrito na biblioteca msal.js principal.

---

## <a name="acquire-a-token-with-a-redirect"></a>Adquira um token com um redirecionamento

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O padrão a seguir é descrito anteriormente, mas mostrado com um método de redirecionamento para adquirir tokens interativamente. Você precisará registrar o retorno de redirecionamento como mencionado anteriormente.

```javascript
function authCallback(error, response) {
    // Handle redirect response
}

userAgentApplication.handleRedirectCallback(authCallback);

const accessTokenRequest: AuthenticationParameters = {
    scopes: ["user.read"]
}

userAgentApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    // Call API with token
    let accessToken = accessTokenResponse.accessToken;
}).catch(function (error) {
    //Acquire token silent failure, and send an interactive request
    console.log(error);
    if (error.errorMessage.indexOf("interaction_required") !== -1) {
        userAgentApplication.acquireTokenRedirect(accessTokenRequest);
    }
});
```

## <a name="request-optional-claims"></a>Solicitar reclamações opcionais

Você pode usar reivindicações opcionais para os seguintes propósitos:

- Inclua reivindicações adicionais em tokens para sua aplicação.
- Altere o comportamento de determinadas declarações que o Azure AD retorna em tokens.
- Adicione e acesse as declarações personalizadas para o aplicativo.

Para solicitar reclamações `IdToken`opcionais, você pode enviar `claimsRequest` um `AuthenticationParameters.ts` objeto de reclamações stringified para o campo da classe.

```javascript
"optionalClaims":
   {
      "idToken": [
            {
                  "name": "auth_time",
                  "essential": true
             }
      ],

var request = {
    scopes: ["user.read"],
    claimsRequest: JSON.stringify(claims)
};

myMSALObj.acquireTokenPopup(request);
```

Para saber mais, consulte [Reivindicações opcionais](active-directory-optional-claims.md).

# <a name="angular"></a>[Angular](#tab/angular)

Este código é o mesmo descrito anteriormente.

---

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Chamar uma API Web](scenario-spa-call-api.md)
