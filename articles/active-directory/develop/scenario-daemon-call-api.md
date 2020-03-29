---
title: Chame uma API web a partir de um aplicativo daemon - plataforma de identidade da Microsoft | Azure
description: Aprenda a construir um aplicativo daemon que chama APIs da Web
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 338b638d6b33bcbbb5cf377643a96c71b0d314bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76775186"
---
# <a name="daemon-app-that-calls-web-apis---call-a-web-api-from-the-app"></a>Aplicativo Daemon que chama APIs da Web - chame uma API web a partir do aplicativo

Os aplicativos daemon .NET podem chamar uma API web. Os aplicativos daemon .NET também podem chamar várias APIs da Web pré-aprovadas.

## <a name="calling-a-web-api-from-a-daemon-application"></a>Chamando uma API web a partir de um aplicativo daemon

Veja como usar o token para chamar uma API:

# <a name="net"></a>[.NET](#tab/dotnet)

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

# <a name="python"></a>[Python](#tab/python)

```Python
endpoint = "url to the API"
http_headers = {'Authorization': 'Bearer ' + result['access_token'],
                'Accept': 'application/json',
                'Content-Type': 'application/json'}
data = requests.get(endpoint, headers=http_headers, stream=False).json()
```

# <a name="java"></a>[Java](#tab/java)

```Java
HttpURLConnection conn = (HttpURLConnection) url.openConnection();

// Set the appropriate header fields in the request header.
conn.setRequestProperty("Authorization", "Bearer " + accessToken);
conn.setRequestProperty("Accept", "application/json");

String response = HttpClientHelper.getResponseStringFromConn(conn);

int responseCode = conn.getResponseCode();
if(responseCode != HttpURLConnection.HTTP_OK) {
    throw new IOException(response);
}

JSONObject responseObject = HttpClientHelper.processResponse(responseCode, response);
```

---

## <a name="calling-several-apis"></a>Chamando várias APIs

Para aplicativos daemon, as APIs da Web que você chama precisam ser pré-aprovadas. Não há consentimento incremental com aplicativos daemon. (Não há interação do usuário.) O inquilino precisa fornecer consentimento com antecedência para a aplicação e todas as permissões de API. Se você quiser chamar várias APIs, você precisa adquirir um `AcquireTokenForClient`token para cada recurso, cada vez que chamar . A MSAL usará o cache de token do aplicativo para evitar chamadas de serviço desnecessárias.

## <a name="next-steps"></a>Próximas etapas

# <a name="net"></a>[.NET](#tab/dotnet)

> [!div class="nextstepaction"]
> [Aplicativo Daemon - passar para a produção](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=dotnet)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Aplicativo Daemon - passar para a produção](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=python)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Aplicativo Daemon - passar para a produção](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=java)

---
