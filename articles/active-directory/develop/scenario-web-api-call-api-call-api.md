---
title: API Web que chama APIs da Web-plataforma de identidade da Microsoft | Azure
description: Saiba como criar uma API Web que chama APIs da Web.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: b1582af2bbd97579852ead0d4462f80f3a50fe6a
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91257139"
---
# <a name="a-web-api-that-calls-web-apis-call-an-api"></a>Uma API Web que chama APIs da Web: chamar uma API

Depois de ter um token, você pode chamar uma API Web protegida. Normalmente, você chama as APIs downstream do controlador ou das páginas da sua API Web.

## <a name="controller-code"></a>Código do controlador

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Ao usar *o Microsoft. Identity. Web*, você tem três cenários de uso:

- [Chamar o Microsoft Graph](#call-microsoft-graph)
- [Chamar uma API Web diferente de Microsoft Graph](#call-web-api-other-than-microsoft-graph)
- [Adquirir um token manualmente](#acquire-a-token-manually)

#### <a name="call-microsoft-graph"></a>Chamar o Microsoft Graph

Nesse cenário, você adicionou `.AddMicrosoftGraph()` o *Startup.cs* conforme especificado na configuração de [código](scenario-web-api-call-api-app-configuration.md#option-1-call-microsoft-graph)e pode injetar diretamente o `GraphServiceClient` no seu controlador ou construtor de página para uso nas ações. A página Razor de exemplo a seguir exibe a foto do usuário conectado.

```CSharp
 [Authorize]
 [AuthorizeForScopes(Scopes = new[] { "user.read" })]
 public class IndexModel : PageModel
 {
     private readonly GraphServiceClient _graphServiceClient;

     public IndexModel(GraphServiceClient graphServiceClient)
     {
         _graphServiceClient = graphServiceClient;
     }

     public async Task OnGet()
     {
         var user = await _graphServiceClient.Me.Request().GetAsync();
         try
         {
             using (var photoStream = await _graphServiceClient.Me.Photo.Content.Request().GetAsync())
             {
                 byte[] photoByte = ((MemoryStream)photoStream).ToArray();
                 ViewData["photo"] = Convert.ToBase64String(photoByte);
             }
             ViewData["name"] = user.DisplayName;
         }
         catch (Exception)
         {
             ViewData["photo"] = null;
         }
     }
 }
```

#### <a name="call-web-api-other-than-microsoft-graph"></a>Chamar API Web diferente de Microsoft Graph

Nesse cenário, você adicionou o `.AddDownstreamWebApi()` *Startup.cs* conforme especificado na configuração de [código](scenario-web-api-call-api-app-configuration.md#option-2-call-a-downstream-web-api-other-than-microsoft-graph), e você pode injetar um `IDownstreamWebApi` serviço diretamente no seu controlador ou construtor de página e usá-lo nas ações:

```CSharp
 [Authorize]
 [AuthorizeForScopes(ScopeKeySection = "TodoList:Scopes")]
 public class TodoListController : Controller
 {
     private IDownstreamWebApi _downstreamWebApi;
     private const string ServiceName = "TodoList";

     public TodoListController(IDownstreamWebApi downstreamWebApi)
     {
         _downstreamWebApi = downstreamWebApi;
     }

     public async Task<ActionResult> Details(int id)
     {
         var value = await _downstreamWebApi.CallWebApiForUserAsync(
             ServiceName,
             options =>
             {
                 options.RelativePath = $"me";
             });
         return View(value);
     }
```

O `CallWebApiForUserAsync` método também tem substituições genéricas fortemente tipadas que permitem que você receba diretamente um objeto. Por exemplo, o método a seguir recebeu uma `Todo` instância, que é uma representação fortemente tipada do JSON retornado pela API da Web.

```CSharp
 // GET: TodoList/Details/5
 public async Task<ActionResult> Details(int id)
 {
     var value = await _downstreamWebApi.CallWebApiForUserAsync<object, Todo>(
         ServiceName,
         null,
         options =>
         {
             options.HttpMethod = HttpMethod.Get;
             options.RelativePath = $"api/todolist/{id}";
         });
     return View(value);
 }
```

#### <a name="acquire-a-token-manually"></a>Adquirir um token manualmente

Se você decidiu adquirir um token manualmente usando o `ITokenAcquisition` serviço, agora precisará usar o token. Nesse caso, o código a seguir continua o código de exemplo mostrado em [uma API Web que chama APIs da Web: adquirir um token para o aplicativo](scenario-web-api-call-api-acquire-token.md). O código é chamado nas ações dos controladores de API. Ele chama uma API downstream chamada *ToDoList*.

 Depois de adquirir o token, use-o como um token de portador para chamar a API downstream.

```csharp
 private async Task CallTodoListService(string accessToken)
 {
  // After the token has been returned by Microsoft.Identity.Web, add it to the HTTP authorization header before making the call to access the todolist service.
 _httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

 // Call the todolist service.
 HttpResponseMessage response = await _httpClient.GetAsync(TodoListBaseAddress + "/api/todolist");
 // ...
 }
 ```

# <a name="java"></a>[Java](#tab/java)

O código a seguir continua o código de exemplo mostrado em [uma API Web que chama APIs da Web: adquirir um token para o aplicativo](scenario-web-api-call-api-acquire-token.md). O código é chamado nas ações dos controladores de API. Ele chama o MS Graph da API downstream.

Depois de adquirir o token, use-o como um token de portador para chamar a API downstream.

```Java
private String callMicrosoftGraphMeEndpoint(String accessToken){
    RestTemplate restTemplate = new RestTemplate();

    HttpHeaders headers = new HttpHeaders();
    headers.setContentType(MediaType.APPLICATION_JSON);

    headers.set("Authorization", "Bearer " + accessToken);

    HttpEntity<String> entity = new HttpEntity<>(null, headers);

    String result = restTemplate.exchange("https://graph.microsoft.com/v1.0/me", HttpMethod.GET,
            entity, String.class).getBody();

    return result;
}
```

# <a name="python"></a>[Python](#tab/python)
Um exemplo que demonstra esse fluxo com o MSAL Python ainda não está disponível.

---

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Uma API Web que chama APIs da Web: mover para produção](scenario-web-api-call-api-production.md)
