---
title: Compilar aplicativo de página única chamando uma API Web-plataforma de identidade da Microsoft | Azure
description: Saiba como criar um aplicativo de página única que chama uma API da Web
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/06/2019
ms.author: ryanwi
ms.custom: aaddev
ms.openlocfilehash: 5b70b109f43e80fc3ec68f52aef2dba6823033bb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "80882124"
---
# <a name="single-page-application-call-a-web-api"></a>Aplicativo de página única: chamar uma API da Web

Recomendamos que você chame o `acquireTokenSilent` método para adquirir ou renovar um token de acesso antes de chamar uma API da Web. Depois de ter um token, você pode chamar uma API Web protegida.

## <a name="call-a-web-api"></a>Chamar uma API da Web

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Use o token de acesso adquirido como um portador em uma solicitação HTTP para chamar qualquer API da Web, como Microsoft Graph API. Por exemplo:

```javascript
    var headers = new Headers();
    var bearer = "Bearer " + access_token;
    headers.append("Authorization", bearer);
    var options = {
         method: "GET",
         headers: headers
    };
    var graphEndpoint = "https://graph.microsoft.com/v1.0/me";

    fetch(graphEndpoint, options)
        .then(function (response) {
             //do something with response
        }
```

# <a name="angular"></a>[Angular](#tab/angular)

O wrapper angular MSAL aproveita o interceptador HTTP para adquirir automaticamente tokens de acesso silenciosamente e anexá-los às solicitações HTTP para APIs. Para obter mais informações, consulte [adquirir um token para chamar uma API](scenario-spa-acquire-token.md).

---

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Mover para ambiente de produção](scenario-spa-production.md)
