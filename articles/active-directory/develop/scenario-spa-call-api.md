---
title: Construa um aplicativo de página única chamando uma API web - plataforma de identidade da Microsoft | Azure
description: Saiba como criar um aplicativo de uma página única que chama uma API web
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/06/2019
ms.author: ryanwi
ms.custom: aaddev
ms.openlocfilehash: ccece6c840033913ec6d96b446dcb98c4befb32f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77159991"
---
# <a name="single-page-application-call-a-web-api"></a>Aplicativo de uma página: Chame uma API web

Recomendamos que você `acquireTokenSilent` ligue para o método para adquirir ou renovar um token de acesso antes de chamar uma API web. Depois de ter um token, você pode chamar uma API da Web protegida.

## <a name="call-a-web-api"></a>Chamar uma API da Web

# <a name="javascript"></a>[Javascript](#tab/javascript)

Use o token de acesso adquirido como portador em uma solicitação HTTP para chamar qualquer API web, como a API do Microsoft Graph. Por exemplo: 

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

O invólucro MSAL Angular aproveita o interceptor HTTP para adquirir automaticamente tokens de acesso silenciosamente e anexá-los às solicitações HTTP às APIs. Para obter mais informações, consulte [Obter um token para chamar uma API](scenario-spa-acquire-token.md).

---

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Mover para ambiente de produção](scenario-spa-production.md)
