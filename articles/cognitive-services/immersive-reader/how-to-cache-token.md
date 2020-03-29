---
title: Armazenar o token de autenticação em cache
titleSuffix: Azure Cognitive Services
description: Este artigo mostrará como armazenar o token de autenticação.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: metan
ms.openlocfilehash: e652aa29b1c1935fcc4887dbe13ef9b683a8bd05
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "75946161"
---
# <a name="how-to-cache-the-authentication-token"></a>Como armazenar o token de autenticação

Este artigo demonstra como armazenar o token de autenticação para melhorar o desempenho do seu aplicativo.

## <a name="using-aspnet"></a>Usando ASP.NET

Importe o pacote **Microsoft.IdentityModel.Clients.ActiveDirectory** NuGet, que é usado para adquirir um token. Em seguida, use o `AuthenticationResult`código a seguir para adquirir um , usando os valores de autenticação que você obteve quando [criou o recurso Immersive Reader](./how-to-create-immersive-reader.md).

```csharp
private async Task<AuthenticationResult> GetTokenAsync()
{
    AuthenticationContext authContext = new AuthenticationContext($"https://login.windows.net/{TENANT_ID}");
    ClientCredential clientCredential = new ClientCredential(CLIENT_ID, CLIENT_SECRET);
    AuthenticationResult authResult = await authContext.AcquireTokenAsync("https://cognitiveservices.azure.com/", clientCredential);
    return authResult;
}
```

O `AuthenticationResult` objeto `AccessToken` tem uma propriedade que é o token real que você usará ao iniciar o Immersive Reader usando o SDK. Ele também `ExpiresOn` tem uma propriedade que denota quando o token expirará. Antes de iniciar o Immersive Reader, você pode verificar se o token expirou e adquirir um novo token somente se ele tiver expirado.

## <a name="using-nodejs"></a>Usando node.JS

Adicione o pacote npm [**de solicitação**](https://www.npmjs.com/package/request) ao seu projeto. Use o código a seguir para adquirir um token, usando os valores de autenticação que você obteve quando [criou o recurso Immersive Reader](./how-to-create-immersive-reader.md).

```javascript
router.get('/token', function(req, res) {
    request.post(
        {
            headers: { 'content-type': 'application/x-www-form-urlencoded' },
            url: `https://login.windows.net/${TENANT_ID}/oauth2/token`,
            form: {
                grant_type: 'client_credentials',
                client_id: CLIENT_ID,
                client_secret: CLIENT_SECRET,
                resource: 'https://cognitiveservices.azure.com/'
            }
        },
        function(err, resp, json) {
            const result = JSON.parse(json);
            return res.send({
                access_token: result.access_token,
                expires_on: result.expires_on
            });
        }
    );
});
```

A `expires_on` propriedade é a data e a hora em que o token expira, expresso como o número de segundos desde 1 de janeiro de 1970 UTC. Use este valor para determinar se seu token expirou antes de tentar adquirir um novo.

```javascript
async function getToken() {
    if (Date.now() / 1000 > CREDENTIALS.expires_on) {
        CREDENTIALS = await refreshCredentials();
    }
    return CREDENTIALS.access_token;
}
```

## <a name="next-steps"></a>Próximas etapas

* Explorar a [Referência do SDK da Leitura Avançada](./reference.md)