---
title: Obtenha um token para uma API web que chama APIs da Web | Azure
titleSuffix: Microsoft identity platform
description: Aprenda a construir uma API web que chama APIs da Web que requerem a aquisição de um token para o aplicativo.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 2721837459af24f39bb15ee17d394345cbb37eb1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76834103"
---
# <a name="a-web-api-that-calls-web-apis-acquire-a-token-for-the-app"></a>Uma API web que chama APIs da Web: Adquira um token para o aplicativo

Depois de construir um objeto de aplicativo cliente, use-o para adquirir um token que você pode usar para chamar uma API web.

## <a name="code-in-the-controller"></a>Código no controlador

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Aqui está um exemplo de código que é chamado nas ações dos controladores de API. Ele chama uma API a jusante chamada *todolist*.

```csharp
private async Task GetTodoList(bool isAppStarting)
{
 ...
 //
 // Get an access token to call the To Do service.
 //
 AuthenticationResult result = null;
 try
 {
  app = BuildConfidentialClient(HttpContext, HttpContext.User);
  result = await app.AcquireTokenSilent(Scopes, account)
                     .ExecuteAsync()
                     .ConfigureAwait(false);
 }
...
}
```

`BuildConfidentialClient()`é semelhante ao cenário em [uma API web que chama APIs da Web: configuração do aplicativo](scenario-web-api-call-api-app-configuration.md). `BuildConfidentialClient()``IConfidentialClientApplication` instancia-se com um cache que contém informações para apenas uma conta. A conta é fornecida `GetAccountIdentifier` pelo método.

O `GetAccountIdentifier` método usa as alegações associadas à identidade do usuário para quem a API web recebeu o JSON Web Token (JWT):

```csharp
public static string GetMsalAccountId(this ClaimsPrincipal claimsPrincipal)
{
 string userObjectId = GetObjectId(claimsPrincipal);
 string tenantId = GetTenantId(claimsPrincipal);

 if (    !string.IsNullOrWhiteSpace(userObjectId)
      && !string.IsNullOrWhiteSpace(tenantId))
 {
  return $"{userObjectId}.{tenantId}";
 }

 return null;
}
```

# <a name="java"></a>[Java](#tab/java)
Aqui está um exemplo de código que é chamado nas ações dos controladores de API. Ele chama a API downstream - Microsoft Graph.

```java
@RestController
public class ApiController {

    @Autowired
    MsalAuthHelper msalAuthHelper;

    @RequestMapping("/graphMeApi")
    public String graphMeApi() throws MalformedURLException {

        String oboAccessToken = msalAuthHelper.getOboToken("https://graph.microsoft.com/.default");

        return callMicrosoftGraphMeEndpoint(oboAccessToken);
    }

}
```

# <a name="python"></a>[Python](#tab/python)

Uma API web Python precisará usar alguns middleware para validar o token do portador recebido do cliente. A API da Web pode então obter o token de acesso [`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) para API a jusante usando a biblioteca MSAL Python chamando o método. Uma amostra demonstrando esse fluxo com o MSAL Python ainda não está disponível.

---

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Uma API web que chama APIs da Web: Chame uma API](scenario-web-api-call-api-call-api.md)
