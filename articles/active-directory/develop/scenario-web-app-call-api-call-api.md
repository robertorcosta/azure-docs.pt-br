---
title: Chamar uma API da Web de um aplicativo Web-plataforma de identidade da Microsoft | Azure
description: Saiba como criar um aplicativo Web que chama APIs da Web (chamando uma API Web protegida)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 84df33137566445015848655cfecb87ba67ef123
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82181674"
---
# <a name="a-web-app-that-calls-web-apis-call-a-web-api"></a>Um aplicativo Web que chama APIs da Web: chamar uma API da Web

Agora que você tem um token, você pode chamar uma API Web protegida.

## <a name="call-a-protected-web-api"></a>Chamar uma API Web protegida

Chamar uma API Web protegida depende do idioma e da estrutura de sua escolha:

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Aqui está o código simplificado para a ação `HomeController`do. Esse código obtém um token para chamar Microsoft Graph. O código foi adicionado para mostrar como chamar Microsoft Graph como uma API REST. A URL para a API de Microsoft Graph é fornecida no arquivo appSettings. JSON e é lida em uma variável chamada `webOptions`:

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    ...
  },
  ...
  "GraphApiUrl": "https://graph.microsoft.com"
}
```

```csharp
public async Task<IActionResult> Profile()
{
 // Acquire the access token.
 string[] scopes = new string[]{"user.read"};
 string accessToken = await tokenAcquisition.GetAccessTokenForUserAsync(scopes);

 // Use the access token to call a protected web API.
 HttpClient client = new HttpClient();
 client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
 
  var response = await httpClient.GetAsync($"{webOptions.GraphApiUrl}/beta/me");

  if (response.StatusCode == HttpStatusCode.OK)
  {
   var content = await response.Content.ReadAsStringAsync();

   dynamic me = JsonConvert.DeserializeObject(content);
   return me;
  }

  ViewData["Me"] = me;
  return View();
}
```

> [!NOTE]
> Você pode usar o mesmo princípio para chamar qualquer API da Web.
>
> A maioria das APIs da Web do Azure fornece um SDK que simplifica a chamada da API. Isso também é verdadeiro para Microsoft Graph. No próximo artigo, você aprenderá onde encontrar um tutorial que ilustra o uso da API.

# <a name="java"></a>[Java](#tab/java)

```Java
private String getUserInfoFromGraph(String accessToken) throws Exception {
    // Microsoft Graph user endpoint
    URL url = new URL("https://graph.microsoft.com/v1.0/me");

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
    return responseObject.toString();
}

```

# <a name="python"></a>[Python](#tab/python)

```Python
@app.route("/graphcall")
def graphcall():
    token = _get_token_from_cache(app_config.SCOPE)
    if not token:
        return redirect(url_for("login"))
    graph_data = requests.get(  # Use token to call downstream service.
        app_config.ENDPOINT,
        headers={'Authorization': 'Bearer ' + token['access_token']},
        ).json()
    return render_template('display.html', result=graph_data)
```

---

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Mover para ambiente de produção](scenario-web-app-call-api-production.md)
