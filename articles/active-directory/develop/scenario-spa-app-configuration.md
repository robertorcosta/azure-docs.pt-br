---
title: Configure aplicativo de página única - plataforma de identidade da Microsoft | Azure
description: Saiba como construir um aplicativo de página única (configuração de código do aplicativo)
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/11/2020
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: f159105046231ba5fb4e458cdd70d930a411a920
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80882328"
---
# <a name="single-page-application-code-configuration"></a>Aplicativo de página única: configuração de código

Saiba como configurar o código para o aplicativo de uma única página (SPA).

## <a name="msal-libraries-that-support-implicit-flow"></a>Bibliotecas MSAL que suportam fluxo implícito

A plataforma de identidade microsoft fornece as seguintes bibliotecas Microsoft Authentication Library (MSAL) para suportar fluxo implícito usando práticas de segurança recomendadas pelo setor:

| Biblioteca MSAL | Descrição |
|--------------|--------------|
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/> [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)  | Biblioteca JavaScript simples para uso em qualquer aplicativo web do lado do cliente que seja construído através de estruturas JavaScript ou SPA, como Angular, Vue.js e React.js. |
| ![MSAL Angular](media/sample-v2-code/logo_angular.png) <br/> [MSAL Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | Invólucro da biblioteca MSAL.js principal para simplificar o uso em aplicativos de página única que são construídos através da estrutura Angular. |

## <a name="application-code-configuration"></a>Configuração do código do aplicativo

Em uma biblioteca MSAL, as informações de registro do aplicativo são passadas como configuração durante a inicialização da biblioteca.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
// Configuration object constructed.
const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri" //defaults to application start page
    }
}

// create UserAgentApplication instance
const userAgentApplication = new UserAgentApplication(config);
```

Para obter mais informações sobre as opções configuráveis, consulte [Inicializando o aplicativo com MSAL.js](msal-js-initializing-client-applications.md).

# <a name="angular"></a>[Angular](#tab/angular)

```javascript
// App.module.ts
import { MsalModule } from '@azure/msal-angular';

@NgModule({
    imports: [
        MsalModule.forRoot({
            auth: {
                clientId: 'your_app_id'
            }
        })
    ]
})

export class AppModule { }
```

---

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Entrar e sair](scenario-spa-sign-in.md)
