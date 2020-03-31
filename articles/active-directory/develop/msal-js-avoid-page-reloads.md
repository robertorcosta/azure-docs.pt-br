---
title: Evitar recargas de página (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Saiba como evitar recargas de página ao adquirir e renovar tokens silenciosamente usando a Biblioteca de Autenticação da Microsoft para JavaScript (MSAL.js).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/29/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 63944a5a9af34c2d4cf98eeb870a730df49654e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084964"
---
# <a name="avoid-page-reloads-when-acquiring-and-renewing-tokens-silently-using-msaljs"></a>Evite recargas de página ao adquirir e renovar tokens silenciosamente usando MSAL.js
A Microsoft Authentication Library for JavaScript (MSAL.js) usa elementos ocultos `iframe` para adquirir e renovar tokens silenciosamente em segundo plano. O Azure AD retorna o token de volta ao redirect_uri registrado especificado na solicitação de token (por padrão, esta é a página raiz do aplicativo). Uma vez que a resposta é um 302, resulta no HTML correspondente ao `redirect_uri` ser carregado no `iframe`. Normalmente, o `redirect_uri` aplicativo é a página raiz e isso faz com que ele seja recarregado.

Em outros casos, se navegar até a página raiz do aplicativo requer `iframe` autenticação, pode levar a elementos aninhados ou `X-Frame-Options: deny` erro.

Uma vez que o MSAL.js não pode descartar o 302 emitido pelo Azure AD e é obrigado a processar o token devolvido, ele não pode impedir que o `redirect_uri` carregamento seja carregado no `iframe`.

Para evitar que todo o aplicativo recarrege novamente ou outros erros causados por isso, siga estas soluçãos.

## <a name="specify-different-html-for-the-iframe"></a>Especificar HTML diferente para o iframe

Defina `redirect_uri` a propriedade em configuração para uma página simples, que não exija autenticação. Você tem que ter certeza `redirect_uri` de que ele corresponde com o registrado no portal Azure. Isso não afetará a experiência de login do usuário, pois o MSAL salva a página inicial quando o usuário inicia o processo de login e redireciona de volta para o local exato após o login ser concluído.

## <a name="initialization-in-your-main-app-file"></a>Inicialização em seu arquivo principal do aplicativo

Se o seu aplicativo estiver estruturado de tal forma que exista um arquivo Javascript central que define a inicialização, roteamento e outras coisas do aplicativo, você pode carregar condicionalmente seus módulos de aplicativo com base em se o aplicativo está carregando em um `iframe` ou não. Por exemplo: 

Em AngularJS: app.js

```javascript
// Check that the window is an iframe and not popup
if (window !== window.parent && !window.opener) {
angular.module('todoApp', ['ui.router', 'MsalAngular'])
    .config(['$httpProvider', 'msalAuthenticationServiceProvider','$locationProvider', function ($httpProvider, msalProvider,$locationProvider) {
        msalProvider.init(
            // msal configuration
        );

        $locationProvider.html5Mode(false).hashPrefix('');
    }]);
}
else {
    angular.module('todoApp', ['ui.router', 'MsalAngular'])
        .config(['$stateProvider', '$httpProvider', 'msalAuthenticationServiceProvider', '$locationProvider', function ($stateProvider, $httpProvider, msalProvider, $locationProvider) {
            $stateProvider.state("Home", {
                url: '/Home',
                controller: "homeCtrl",
                templateUrl: "/App/Views/Home.html",
            }).state("TodoList", {
                url: '/TodoList',
                controller: "todoListCtrl",
                templateUrl: "/App/Views/TodoList.html",
                requireLogin: true
            })

            $locationProvider.html5Mode(false).hashPrefix('');

            msalProvider.init(
                // msal configuration
            );
        }]);
}
```

Em Angular: app.module.ts

```javascript
// Imports...
@NgModule({
  declarations: [
    AppComponent,
    MsalComponent,
    MainMenuComponent,
    AccountMenuComponent,
    OsNavComponent
  ],
  imports: [
    BrowserModule,
    AppRoutingModule,
    HttpClientModule,
    ServiceWorkerModule.register('ngsw-worker.js', { enabled: environment.production }),
    MsalModule.forRoot(environment.MsalConfig),
    SuiModule,
    PagesModule
  ],
  providers: [
    HttpServiceHelper,
    {provide: HTTP_INTERCEPTORS, useClass: MsalInterceptor, multi: true},
    AuthService
  ],
  entryComponents: [
    AppComponent,
    MsalComponent
  ]
})
export class AppModule {
  constructor() {
    console.log('APP Module Constructor!');
  }

  ngDoBootstrap(ref: ApplicationRef) {
    if (window !== window.parent && !window.opener)
    {
      console.log("Bootstrap: MSAL");
      ref.bootstrap(MsalComponent);
    }
    else
    {
    //this.router.resetConfig(RouterModule);
      console.log("Bootstrap: App");
      ref.bootstrap(AppComponent);
    }
  }
}
```

Componente Msal:

```javascript
import { Component} from '@angular/core';
import { MsalService } from '@azure/msal-angular';

// This component is used only to avoid Angular reload
// when doing acquireTokenSilent()

@Component({
  selector: 'app-root',
  template: '',
})
export class MsalComponent {
  constructor(private Msal: MsalService) {
  }
}
```

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre [a construção de um aplicativo de uma página única (SPA)](scenario-spa-overview.md) usando o MSAL.js.