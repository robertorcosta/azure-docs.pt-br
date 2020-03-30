---
title: Ligue para uma API web a partir de um aplicativo móvel | Azure
titleSuffix: Microsoft identity platform
description: Aprenda a criar um aplicativo móvel que chama APIs da Web. (Chame uma API web.)
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
ms.reviwer: brandwe
ms.custom: aaddev
ms.openlocfilehash: bd848fa6f74f049f97956ef1736ac2b08f3a6148
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77160144"
---
# <a name="call-a-web-api-from-a-mobile-app"></a>Chame uma API web a partir de um aplicativo móvel

Depois que seu aplicativo faz sinais em um usuário e recebe tokens, a Microsoft Authentication Library (MSAL) expõe informações sobre o usuário, o ambiente do usuário e os tokens emitidos. Seu aplicativo pode usar esses valores para chamar uma API web ou exibir uma mensagem de boas-vindas ao usuário.

Neste artigo, vamos primeiro olhar para o resultado do MSAL. Em seguida, vamos ver como usar um `AuthenticationResult` `result` token de acesso ou para chamar uma API da Web protegida.

## <a name="msal-result"></a>Resultado do MSAL
A MSAL fornece os seguintes valores: 

- `AccessToken`chama APIs da Web protegidas em uma solicitação http portador.
- `IdToken`contém informações úteis sobre o usuário inscrito. Essas informações incluem o nome do usuário, o inquilino da casa e um identificador exclusivo para armazenamento.
- `ExpiresOn`é o tempo de expiração do token. A MSAL lida com a atualização automática de um aplicativo.
- `TenantId`é o identificador do inquilino onde o usuário se inscreveu. Para usuários convidados no Azure Active Directory (Azure AD) B2B, esse valor identifica o inquilino onde o usuário entrou. O valor não identifica o inquilino da casa do usuário.  
- `Scopes`indica os escopos que foram concedidos com seu token. Os escopos concedidos podem ser um subconjunto dos escopos que você solicitou.

A MSAL também fornece uma `Account` abstração por um valor. Um `Account` valor representa a conta de entrada do usuário atual:

- `HomeAccountIdentifier`identifica o inquilino da casa do usuário.
- `UserName`é o nome de usuário preferido do usuário. Esse valor pode estar vazio para usuários do Azure AD B2C.
- `AccountIdentifier`identifica o usuário inscrito. Na maioria dos casos, esse `HomeAccountIdentifier` valor é o mesmo que o valor, a menos que o usuário seja um hóspede em outro inquilino.

## <a name="call-an-api"></a>Ligue para uma API

Depois de ter o token de acesso, você pode chamar uma API web. Seu aplicativo usará o token para construir uma solicitação HTTP e, em seguida, executará a solicitação.

### <a name="android"></a>Android

```Java
        RequestQueue queue = Volley.newRequestQueue(this);
        JSONObject parameters = new JSONObject();

        try {
            parameters.put("key", "value");
        } catch (Exception e) {
            // Error when constructing.
        }
        JsonObjectRequest request = new JsonObjectRequest(Request.Method.GET, MSGRAPH_URL,
                parameters,new Response.Listener<JSONObject>() {
            @Override
            public void onResponse(JSONObject response) {
                // Successfully called Graph. Process data and send to UI.
            }
        }, new Response.ErrorListener() {
            @Override
            public void onErrorResponse(VolleyError error) {
                // Error.
            }
        }) {
            @Override
            public Map<String, String> getHeaders() throws AuthFailureError {
                Map<String, String> headers = new HashMap<>();
                
                // Put access token in HTTP request.
                headers.put("Authorization", "Bearer " + authResult.getAccessToken());
                return headers;
            }
        };

        request.setRetryPolicy(new DefaultRetryPolicy(
                3000,
                DefaultRetryPolicy.DEFAULT_MAX_RETRIES,
                DefaultRetryPolicy.DEFAULT_BACKOFF_MULT));
        queue.add(request);
```

### <a name="msal-for-ios-and-macos"></a>MSAL para iOS e macOS

Os métodos para adquirir tokens retornam um `MSALResult` objeto. `MSALResult`expõe uma `accessToken` propriedade. Você pode `accessToken` usar para chamar uma API web. Adicione esta propriedade ao cabeçalho de autorização HTTP antes de ligar para acessar a API da Web protegida.

```objc
NSMutableURLRequest *urlRequest = [NSMutableURLRequest new];
urlRequest.URL = [NSURL URLWithString:"https://contoso.api.com"];
urlRequest.HTTPMethod = @"GET";
urlRequest.allHTTPHeaderFields = @{ @"Authorization" : [NSString stringWithFormat:@"Bearer %@", accessToken] };
        
NSURLSessionDataTask *task =
[[NSURLSession sharedSession] dataTaskWithRequest:urlRequest
     completionHandler:^(NSData * _Nullable data, NSURLResponse * _Nullable response, NSError * _Nullable error) {}];
[task resume];
```

```swift
let urlRequest = NSMutableURLRequest()
urlRequest.url = URL(string: "https://contoso.api.com")!
urlRequest.httpMethod = "GET"
urlRequest.allHTTPHeaderFields = [ "Authorization" : "Bearer \(accessToken)" ]
     
let task = URLSession.shared.dataTask(with: urlRequest as URLRequest) { (data: Data?, response: URLResponse?, error: Error?) in }
task.resume()
```

### <a name="xamarin"></a>Xamarin

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

## <a name="make-several-api-requests"></a>Faça várias solicitações de API

Se você precisar ligar para a mesma API várias vezes ou se precisar ligar para várias APIs, considere os seguintes assuntos ao criar seu aplicativo:

- **Consentimento incremental**: A plataforma de identidade da Microsoft permite que os aplicativos obtenham o consentimento do usuário quando as permissões são necessárias, em vez de todas no início. Cada vez que seu aplicativo estiver pronto para chamar uma API, ele deve solicitar apenas os escopos que ele precisa.

- **Acesso condicional**: Quando você faz várias solicitações de API, em certos cenários você pode ter que atender a requisitos adicionais de acesso condicional. Os requisitos podem aumentar desta forma se a primeira solicitação não tiver políticas de acesso condicional e seu aplicativo tentar acessar silenciosamente uma nova API que exija acesso condicional. Para lidar com esse problema, certifique-se de pegar erros de solicitações silenciosas e esteja preparado para fazer uma solicitação interativa.  Para obter mais informações, consulte [Orientação para acesso condicional](../azuread-dev/conditional-access-dev-guide.md).

## <a name="call-several-apis-by-using-incremental-consent-and-conditional-access"></a>Ligue para várias APIs usando consentimento incremental e acesso condicional

Se você precisar ligar para várias APIs para o mesmo usuário, depois de adquirir um token para `AcquireTokenSilent` o usuário, você pode evitar repetidamente pedir credenciais ao usuário ligando posteriormente para obter um token:

```csharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
```

A interação é necessária quando:

- O usuário consentiu com a primeira API, mas agora precisa consentir com mais escopos. Neste caso, você usa consentimento incremental.
- A primeira API não requer autenticação de múltiplos fatores, mas a próxima API requer.

```csharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

try
{
 result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
 result = await app.AcquireTokenInteractive("scopeApi2")
                  .WithClaims(ex.Claims)
                  .ExecuteAsync();
}
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Mover para ambiente de produção](scenario-mobile-production.md)
