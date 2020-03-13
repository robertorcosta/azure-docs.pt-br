---
title: Gatilho HTTP Azure Functions
description: Saiba como chamar uma função do Azure via HTTP.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 045f3ccdc8dc09bf657ab39ce15a0d0524c73fcb
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79277590"
---
# <a name="azure-functions-http-trigger"></a>Gatilho HTTP Azure Functions

Um gatilho de HTTP permite invocar uma função com uma solicitação HTTP. Você pode usar um gatilho de HTTP para criar APIs sem servidor e responder a webhooks.

O valor de retorno padrão para uma função disparada por HTTP é:

- `HTTP 204 No Content` com um corpo vazio no functions 2. x e superior
- `HTTP 200 OK` com um corpo vazio nas funções 1. x

Para modificar a resposta HTTP, configure uma [Associação de saída](./functions-bindings-http-webhook-output.md).

Para obter mais informações sobre associações HTTP, consulte [visão geral](./functions-bindings-http-webhook.md) e [referência de associação de saída](./functions-bindings-http-webhook-output.md).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="example"></a>Exemplo

# <a name="c"></a>[C#](#tab/csharp)

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que procura por um parâmetro `name` na cadeia de caracteres de consulta ou no corpo da solicitação HTTP. Observe que o valor de retorno é usado para a associação de saída, mas um atributo de valor de retorno não é necessário.

```cs
[FunctionName("HttpTriggerCSharp")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] 
    HttpRequest req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;

    return name != null
        ? (ActionResult)new OkObjectResult($"Hello, {name}")
        : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
}
```

# <a name="c-script"></a>[C#Prescritiva](#tab/csharp-script)

O exemplo a seguir mostra uma associação de gatilho em um arquivo *function.json* e uma [função de script de C#](functions-reference-csharp.md) que usa a associação. A função procura um parâmetro `name` na cadeia de consulta ou no corpo da solicitação HTTP.

Aqui está o arquivo *function.json*:

```json
{
    "disabled": false,
    "bindings": [
        {
            "authLevel": "function",
            "name": "req",
            "type": "httpTrigger",
            "direction": "in",
            "methods": [
                "get",
                "post"
            ]
        },
        {
            "name": "$return",
            "type": "http",
            "direction": "out"
        }
    ]
}
```

A seção [configuração](#configuration) explica essas propriedades.

Aqui está o código de script C# que associa a um `HttpRequest`:

```cs
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Newtonsoft.Json;

public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;

    return name != null
        ? (ActionResult)new OkObjectResult($"Hello, {name}")
        : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
}
```

Você pode vincular a um objeto personalizado em vez de `HttpRequest`. Esse objeto é criado a partir do corpo da solicitação e analisado como JSON. Da mesma forma, um tipo pode ser passado para a associação de saída de resposta HTTP e retornado como o corpo da resposta, juntamente com um `200` código de status.

```csharp
using System.Net;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static string Run(Person person, ILogger log)
{   
    return person.Name != null
        ? (ActionResult)new OkObjectResult($"Hello, {person.Name}")
        : new BadRequestObjectResult("Please pass an instance of Person.");
}

public class Person {
     public string Name {get; set;}
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O exemplo a seguir mostra uma associação de gatilho em um arquivo *function.json* e uma [função JavaScript](functions-reference-node.md) que usa a associação. A função procura um parâmetro `name` na cadeia de consulta ou no corpo da solicitação HTTP.

Aqui está o arquivo *function.json*:

```json
{
    "disabled": false,    
    "bindings": [
        {
            "authLevel": "function",
            "type": "httpTrigger",
            "direction": "in",
            "name": "req"
        },
        {
            "type": "http",
            "direction": "out",
            "name": "res"
        }
    ]
}
```

A seção [configuração](#configuration) explica essas propriedades.

Aqui está o código JavaScript:

```javascript
module.exports = function(context, req) {
    context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);

    if (req.query.name || (req.body && req.body.name)) {
        context.res = {
            // status defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

O exemplo a seguir mostra uma associação de gatilho em um arquivo *function.json* e uma [função Python](functions-reference-python.md) que usa a associação. A função procura um parâmetro `name` na cadeia de consulta ou no corpo da solicitação HTTP.

Aqui está o arquivo *function.json*:

```json
{
    "scriptFile": "__init__.py",
    "disabled": false,    
    "bindings": [
        {
            "authLevel": "function",
            "type": "httpTrigger",
            "direction": "in",
            "name": "req"
        },
        {
            "type": "http",
            "direction": "out",
            "name": "res"
        }
    ]
}
```

A seção [configuração](#configuration) explica essas propriedades.

Aqui está o código Python:

```python
import logging
import azure.functions as func


def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
            "Please pass a name on the query string or in the request body",
            status_code=400
        )
```

# <a name="java"></a>[Java](#tab/java)

* [Ler o parâmetro da cadeia de consulta](#read-parameter-from-the-query-string)
* [Ler o corpo de uma solicitação POST](#read-body-from-a-post-request)
* [Ler o parâmetro de uma rota](#read-parameter-from-a-route)
* [Ler o corpo de POJO de uma solicitação POST](#read-pojo-body-from-a-post-request)

Os exemplos a seguir mostram a associação de gatilho HTTP.

#### <a name="read-parameter-from-the-query-string"></a>Ler parâmetro da cadeia de caracteres de consulta

Este exemplo lê um parâmetro denominado `id` da cadeia de caracteres de consulta e o utiliza para criar um documento JSON retornado ao cliente, com o tipo de conteúdo `application/json`.

```java
@FunctionName("TriggerStringGet")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", 
            methods = {HttpMethod.GET}, 
            authLevel = AuthorizationLevel.ANONYMOUS)
        HttpRequestMessage<Optional<String>> request,
        final ExecutionContext context) {
    
    // Item list
    context.getLogger().info("GET parameters are: " + request.getQueryParameters());

    // Get named parameter
    String id = request.getQueryParameters().getOrDefault("id", "");

    // Convert and display
    if (id.isEmpty()) {
        return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                        .body("Document not found.")
                        .build();
    } 
    else {
        // return JSON from to the client
        // Generate document
        final String name = "fake_name";
        final String jsonDocument = "{\"id\":\"" + id + "\", " + 
                                        "\"description\": \"" + name + "\"}";
        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(jsonDocument)
                        .build();
    }
}
```

#### <a name="read-body-from-a-post-request"></a>Ler o corpo de uma solicitação POST

Este exemplo lê o corpo de uma solicitação POST, como uma `String`, e o utiliza para criar um documento JSON retornado ao cliente, com o tipo de conteúdo `application/json`.

```java
    @FunctionName("TriggerStringPost")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", 
              methods = {HttpMethod.POST}, 
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            final ExecutionContext context) {
        
        // Item list
        context.getLogger().info("Request body is: " + request.getBody().orElse(""));

        // Check request body
        if (!request.getBody().isPresent()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        } 
        else {
            // return JSON from to the client
            // Generate document
            final String body = request.getBody().get();
            final String jsonDocument = "{\"id\":\"123456\", " + 
                                         "\"description\": \"" + body + "\"}";
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(jsonDocument)
                          .build();
        }
    }
```

#### <a name="read-parameter-from-a-route"></a>Ler parâmetro de uma rota

Este exemplo lê um parâmetro obrigatório, denominado `id`, e um parâmetro opcional `name` do caminho da rota e os utiliza para criar um documento JSON retornado ao cliente, com o tipo de conteúdo `application/json`. T

```java
@FunctionName("TriggerStringRoute")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", 
            methods = {HttpMethod.GET}, 
            authLevel = AuthorizationLevel.ANONYMOUS,
            route = "trigger/{id}/{name=EMPTY}") // name is optional and defaults to EMPTY
        HttpRequestMessage<Optional<String>> request,
        @BindingName("id") String id,
        @BindingName("name") String name,
        final ExecutionContext context) {
    
    // Item list
    context.getLogger().info("Route parameters are: " + id);

    // Convert and display
    if (id == null) {
        return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                        .body("Document not found.")
                        .build();
    } 
    else {
        // return JSON from to the client
        // Generate document
        final String jsonDocument = "{\"id\":\"" + id + "\", " + 
                                        "\"description\": \"" + name + "\"}";
        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(jsonDocument)
                        .build();
    }
}
```

#### <a name="read-pojo-body-from-a-post-request"></a>Ler o corpo do POJO de uma solicitação POST

Este é o código para a classe `ToDoItem`, referenciada neste exemplo:

```java

public class ToDoItem {

  private String id;
  private String description;  

  public ToDoItem(String id, String description) {
    this.id = id;
    this.description = description;
  }

  public String getId() {
    return id;
  }

  public String getDescription() {
    return description;
  }
  
  @Override
  public String toString() {
    return "ToDoItem={id=" + id + ",description=" + description + "}";
  }
}

```

Este exemplo lê o corpo de uma solicitação POST. O corpo da solicitação é desserializado automaticamente em um objeto `ToDoItem` e é retornado ao cliente, com o tipo de conteúdo `application/json`. O parâmetro `ToDoItem` é serializado pelo runtime do Functions quando é atribuído para a propriedade `body` da classe `HttpMessageResponse.Builder`.

```java
@FunctionName("TriggerPojoPost")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", 
            methods = {HttpMethod.POST}, 
            authLevel = AuthorizationLevel.ANONYMOUS)
        HttpRequestMessage<Optional<ToDoItem>> request,
        final ExecutionContext context) {
    
    // Item list
    context.getLogger().info("Request body is: " + request.getBody().orElse(null));

    // Check request body
    if (!request.getBody().isPresent()) {
        return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                        .body("Document not found.")
                        .build();
    } 
    else {
        // return JSON from to the client
        // Generate document
        final ToDoItem body = request.getBody().get();
        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(body)
                        .build();
    }
}
```

---

## <a name="attributes-and-annotations"></a>Atributos e anotações

Em [ C# bibliotecas de classes](functions-dotnet-class-library.md) e Java, o atributo `HttpTrigger` está disponível para configurar a função.

Você pode definir o nível de autorização e os métodos HTTP permitidos em parâmetros de construtor de atributo, tipo de webhook e um modelo de rota. Para obter mais informações sobre essas configurações, consulte [configuração](#configuration).

# <a name="c"></a>[C#](#tab/csharp)

Este exemplo demonstra como usar o atributo [HttpTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions.Http/HttpTriggerAttribute.cs) .

```csharp
[FunctionName("HttpTriggerCSharp")]
public static Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous)] HttpRequest req)
{
    ...
}
```

Para obter um exemplo completo, consulte o [exemplo de gatilho](#example).

# <a name="c-script"></a>[C#Prescritiva](#tab/csharp-script)

O script não dá suporte C# a atributos.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Não há suporte para atributos pelo JavaScript.

# <a name="python"></a>[Python](#tab/python)

Não há suporte para atributos no Python.

# <a name="java"></a>[Java](#tab/java)

Este exemplo demonstra como usar o atributo [HttpTrigger](https://github.com/Azure/azure-functions-java-library/blob/dev/src/main/java/com/microsoft/azure/functions/annotation/HttpTrigger.java) .

```java
@FunctionName("HttpTriggerJava")
public HttpResponseMessage<String> HttpTrigger(
        @HttpTrigger(name = "req",
                     methods = {"get"},
                     authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<String> request,
        final ExecutionContext context) {

    ...
}
```

Para obter um exemplo completo, consulte o [exemplo de gatilho](#example).

---

## <a name="configuration"></a>Configuração

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *function.json* e no atributo `HttpTrigger`.

|Propriedade function.json | Propriedade de atributo |DESCRIÇÃO|
|---------|---------|----------------------|
| **tipo** | n/d| Obrigatório – deve ser definido como `httpTrigger`. |
| **direction** | n/d| Obrigatório – deve ser definido como `in`. |
| **name** | n/d| Obrigatório – o nome da variável usado no código da função da solicitação ou do corpo da solicitação. |
| <a name="http-auth"></a>**authLevel** |  **AuthLevel** |Determina quais chaves, se houver, precisam estar presentes na solicitação para invocar a função. O nível de autorização pode ser um dos seguintes valores: <ul><li><code>anonymous</code>&mdash;Nenhuma chave API é obrigatória.</li><li><code>function</code>&mdash;Uma chave de API específica de função é obrigatória. Esse será o valor padrão se nenhum for fornecido.</li><li><code>admin</code>&mdash;A chave mestra é obrigatória.</li></ul> Para saber mais informações, veja a seção sobre [chaves de autorização](#authorization-keys). |
| **methods** |**Métodos** | Uma matriz dos métodos HTTP para a qual a função responde. Se não for especificada, a função responderá a todos os métodos HTTP. Consulte [Personalizar o ponto de extremidade http](#customize-the-http-endpoint). |
| **route** | **Route** | Define o modelo da rota, controlando para quais URLs de solicitação sua função responde. O valor padrão se nenhum for fornecido será `<functionname>`. Para obter mais informações, consulte [Personalizar o ponto de extremidade http](#customize-the-http-endpoint). |
| **webHookType** | **WebHookType** | _Com suporte apenas na versão 1.x do runtime._<br/><br/>Configure o gatilho HTTP para atuar como um receptor de [webhook](https://en.wikipedia.org/wiki/Webhook) para o provedor especificado. Não defina a propriedade `methods` se você definir essa propriedade. O tipo de webhook pode ser um dos seguintes valores:<ul><li><code>genericJson</code>&mdash;Um ponto de extremidade de webhook de finalidade geral sem lógica para um provedor específico. Essa configuração restringe as solicitações àquelas que usam HTTP POST e com o tipo de conteúdo `application/json`.</li><li><code>github</code>&mdash;A função responde a [webhooks do GitHub](https://developer.github.com/webhooks/). Não use a propriedade _authLevel_ com os webhooks do GitHub. Para saber mais informações, consulte a seção sobre webhooks do GitHub posteriormente neste artigo.</li><li><code>slack</code>&mdash;A função responde a [webhooks do Slack](https://api.slack.com/outgoing-webhooks). Não use a propriedade _authLevel_ com os webhooks do Slack. Para saber mais informações, consulte a seção sobre webhooks do Slack posteriormente neste artigo.</li></ul>|

## <a name="payload"></a>Carga útil

O tipo de entrada do gatilho é declarado como `HttpRequest` ou um tipo personalizado. Ao escolher `HttpRequest`, você obterá acesso completo ao objeto de solicitação. Para um tipo personalizado, o runtime tenta analisar o corpo da solicitação JSON para definir as propriedades do objeto.

## <a name="customize-the-http-endpoint"></a>Personalização do ponto de extremidade HTTP

Por padrão quando você cria uma função para um gatilho HTTP, a função é endereçável com uma rota do formulário:

    http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>

Você pode personalizar essa rota usando a propriedade `route` opcional na associação de entrada do gatilho HTTP. Por exemplo, o seguinte arquivo *function.json* define uma propriedade `route` para um gatilho HTTP:

```json
{
    "bindings": [
    {
        "type": "httpTrigger",
        "name": "req",
        "direction": "in",
        "methods": [ "get" ],
        "route": "products/{category:alpha}/{id:int?}"
    },
    {
        "type": "http",
        "name": "res",
        "direction": "out"
    }
    ]
}
```

Usando esta configuração, a função é agora endereçável com a seguinte rota em vez da rota original.

```
http://<APP_NAME>.azurewebsites.net/api/products/electronics/357
```

Essa configuração permite que o código de função dê suporte a dois parâmetros no endereço, na _categoria_ e na _ID_.

# <a name="c"></a>[C#](#tab/csharp)

Você pode usar qualquer [Restrição de rota de API Web](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints) com seus parâmetros. O seguinte código de função em C# faz uso de ambos os parâmetros.

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;

public static IActionResult Run(HttpRequest req, string category, int? id, ILogger log)
{
    var message = String.Format($"Category: {category}, ID: {id}");
    return (ActionResult)new OkObjectResult(message);
}
```

# <a name="c-script"></a>[C#Prescritiva](#tab/csharp-script)

Você pode usar qualquer [Restrição de rota de API Web](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints) com seus parâmetros. O seguinte código de função em C# faz uso de ambos os parâmetros.

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;

public static IActionResult Run(HttpRequest req, string category, int? id, ILogger log)
{
    var message = String.Format($"Category: {category}, ID: {id}");
    return (ActionResult)new OkObjectResult(message);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

No nó, o tempo de execução do Functions fornece o corpo da solicitação do objeto `context`. Para saber mais informações, consulte o [exemplo de gatilho do JavaScript](#example).

O exemplo a seguir mostra como ler parâmetros de rota de `context.bindingData`.

```javascript
module.exports = function (context, req) {

    var category = context.bindingData.category;
    var id = context.bindingData.id;
    var message = `Category: ${category}, ID: ${id}`;

    context.res = {
        body: message;
    }

    context.done();
}
```

# <a name="python"></a>[Python](#tab/python)

O contexto de execução da função é exposto por meio de um parâmetro declarado como `func.HttpRequest`. Essa instância permite que uma função acesse parâmetros de rota de dados, valores de cadeia de caracteres de consulta e métodos que permitem retornar respostas HTTP.

Uma vez definidas, os parâmetros de rota estão disponíveis para a função chamando o método `route_params`.

```python
import logging

import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    category = req.route_params.get('category')
    id = req.route_params.get('id')
    message = f"Category: {category}, ID: {id}"

    return func.HttpResponse(message)
```

# <a name="java"></a>[Java](#tab/java)

O contexto de execução da função é propriedades como declaradas no atributo `HttpTrigger`. O atributo permite que você defina parâmetros de rota, níveis de autorização, verbos HTTP e a instância de solicitação de entrada.

Os parâmetros de rota são definidos por meio do atributo `HttpTrigger`.

```java
package com.function;

import java.util.*;
import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;

public class HttpTriggerJava {
    public HttpResponseMessage<String> HttpTrigger(
            @HttpTrigger(name = "req",
                         methods = {"get"},
                         authLevel = AuthorizationLevel.FUNCTION,
                         route = "products/{category:alpha}/{id:int}") HttpRequestMessage<String> request,
            @BindingName("category") String category,
            @BindingName("id") int id,
            final ExecutionContext context) {

        String message = String.format("Category  %s, ID: %d", category, id);
        return request.createResponseBuilder(HttpStatus.OK).body(message).build();
    }
}
```

---

Por padrão, todas as rotas de função são prefixadas com *api*. Você também pode personalizar ou remover o prefixo usando a propriedade `http.routePrefix` em seu arquivo [host.json](functions-host-json.md). O exemplo a seguir remove o prefixo de rota *api* usando uma cadeia de caracteres vazia para o prefixo no arquivo *host.json*.

```json
{
    "http": {
    "routePrefix": ""
    }
}
```

## <a name="using-route-parameters"></a>Usando parâmetros de rota

Os parâmetros de rota que definiram o padrão de `route` de uma função estão disponíveis para cada associação. Por exemplo, se você tiver uma rota definida como `"route": "products/{id}"`, uma associação de armazenamento de tabela poderá usar o valor do parâmetro `{id}` na configuração de associação.

A configuração a seguir mostra como o parâmetro `{id}` é passado para a `rowKey`da associação.

```json
{
    "type": "table",
    "direction": "in",
    "name": "product",
    "partitionKey": "products",
    "tableName": "products",
    "rowKey": "{id}"
}
```

## <a name="working-with-client-identities"></a>Trabalhando com identidades de clientes

Se seu aplicativo de função estiver usando [Serviço de Aplicativo de Autenticação / Autorização](../app-service/overview-authentication-authorization.md), você poderá visualizar informações sobre clientes autenticados a partir de seu código. Essas informações estão disponíveis como [headers de solicitação injetados pela plataforma](../app-service/app-service-authentication-how-to.md#access-user-claims). 

Você também pode ler essas informações a partir de dados vinculados. Esse recurso está disponível somente para o tempo de execução do Functions no 2. x e superior. Atualmente, também está disponível apenas para idiomas .NET.

# <a name="c"></a>[C#](#tab/csharp)

As informações sobre clientes autenticados estão disponíveis como um [ClaimsPrincipal](https://docs.microsoft.com/dotnet/api/system.security.claims.claimsprincipal). O ClaimsPrincipal está disponível como parte do contexto da solicitação, conforme mostrado no exemplo a seguir:

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using System.Security.Claims;

public static IActionResult Run(HttpRequest req, ILogger log)
{
    ClaimsPrincipal identities = req.HttpContext.User;
    // ...
    return new OkObjectResult();
}
```

Como alternativa, o ClaimsPrincipal pode ser simplesmente incluído como um parâmetro adicional na assinatura da função:

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using System.Security.Claims;
using Newtonsoft.Json.Linq;

public static void Run(JObject input, ClaimsPrincipal principal, ILogger log)
{
    // ...
    return;
}
```

# <a name="c-script"></a>[C#Prescritiva](#tab/csharp-script)

As informações sobre clientes autenticados estão disponíveis como um [ClaimsPrincipal](https://docs.microsoft.com/dotnet/api/system.security.claims.claimsprincipal). O ClaimsPrincipal está disponível como parte do contexto da solicitação, conforme mostrado no exemplo a seguir:

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using System.Security.Claims;

public static IActionResult Run(HttpRequest req, ILogger log)
{
    ClaimsPrincipal identities = req.HttpContext.User;
    // ...
    return new OkObjectResult();
}
```

Como alternativa, o ClaimsPrincipal pode ser simplesmente incluído como um parâmetro adicional na assinatura da função:

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using System.Security.Claims;
using Newtonsoft.Json.Linq;

public static void Run(JObject input, ClaimsPrincipal principal, ILogger log)
{
    // ...
    return;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O usuário autenticado está disponível por meio de [cabeçalhos HTTP](../app-service/app-service-authentication-how-to.md#access-user-claims).

# <a name="python"></a>[Python](#tab/python)

O usuário autenticado está disponível por meio de [cabeçalhos HTTP](../app-service/app-service-authentication-how-to.md#access-user-claims).

# <a name="java"></a>[Java](#tab/java)

O usuário autenticado está disponível por meio de [cabeçalhos HTTP](../app-service/app-service-authentication-how-to.md#access-user-claims).

---

## <a name="authorization-keys"></a>Chaves de autorização

As funções permitem o uso de chaves para dificultar o acesso aos pontos de extremidade de função HTTP durante o desenvolvimento.  A menos que o nível de autorização HTTP em uma função disparada por HTTP seja definido como `anonymous`, as solicitações devem incluir uma chave de API na solicitação. 

> [!IMPORTANT]
> Embora as chaves possam ajudar o ofuscar seus pontos de extremidade HTTP durante o desenvolvimento, elas não foram projetadas como uma maneira de proteger um gatilho HTTP em produção. Para obter mais informações, confira [Proteger um ponto de extremidade HTTP em produção](#secure-an-http-endpoint-in-production).

> [!NOTE]
> No runtime 1.x do Functions, os provedores de webhook podem usar chaves para autorizar solicitações de várias maneiras, dependendo do suporte do provedor. Isso é abordado em [Webhooks e chaves](#webhooks-and-keys). O tempo de execução do Functions na versão 2. x e superior não inclui suporte interno para provedores de webhook.

#### <a name="authorization-scopes-function-level"></a>Escopos de autorização (nível de função)

Há dois escopos de autorização para chaves de nível de função:

* **Função**: essas chaves se aplicam somente às funções específicas sob as quais elas são definidas. Quando usadas como uma chave de API, elas permitem acesso apenas às funções em questão.

* **Host**: as chaves com um escopo de host podem ser usadas para acessar todas as funções no aplicativo de funções. Quando usadas como uma chave de API, elas permitem acesso a qualquer função no aplicativo de funções. 

Cada chave é nomeada para referência e há uma chave padrão (chamada "default") no nível de função e de host. As chaves de função têm precedência sobre as chaves de host. Quando duas chaves forem definidas com o mesmo nome, a chave de função sempre será usada.

#### <a name="master-key-admin-level"></a>Chave mestra (nível de administrador) 

Cada aplicativo de funções também tem uma chave de host de nível de administrador chamada `_master`. Além de fornecer acesso em nível de host a todas as funções no aplicativo, a chave mestra também fornece acesso administrativo às APIs REST de tempo de execução. Não é possível revogar essa chave. Quando você define o nível de autorização como `admin`, as solicitações precisam usar a chave mestra. Nesse caso, o uso de outra chave resulta em falha na autorização.

> [!CAUTION]  
> Devido às permissões elevadas no aplicativo de funções concedidas pela chave mestra, você não deve compartilhar essa chave com terceiros nem distribuí-la em aplicativos cliente nativos. Tenha cuidado ao escolher o nível de autorização do administrador.

## <a name="obtaining-keys"></a>Obtendo chaves

As chaves são armazenadas como parte do seu aplicativo de funções no Azure e criptografadas em repouso. Para exibir suas chaves, criar chaves ou gerar novos valores para as chaves, navegue até uma das funções disparadas por HTTP no [portal do Azure](https://portal.azure.com) e selecione **Gerenciar**.

![Gerencie as chaves de função no portal.](./media/functions-bindings-http-webhook/manage-function-keys.png)

Você pode obter as chaves de função programaticamente usando [APIs de gerenciamento de chaves](https://github.com/Azure/azure-functions-host/wiki/Key-management-API).

## <a name="api-key-authorization"></a>Autorização da chave de API

A maioria dos modelos de gatilho HTTP exigem uma chave de API na solicitação. Portanto, a solicitação HTTP geralmente será como a seguinte URL:

    https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?code=<API_KEY>

A chave pode ser incluída em uma variável de cadeia de consulta chamada `code`, como acima. Ela também pode ser incluída em um cabeçalho HTTP `x-functions-key`. O valor da chave pode ser qualquer chave de função definida para a função ou qualquer chave de host.

Você pode permitir solicitações anônimas, o que não exige chaves. Você também pode exigir que a chave mestra seja usada. Altere o nível de autorização padrão usando a propriedade `authLevel` no JSON de associação. Para saber mais informações, consulte [Gatilho - configuração](#configuration).

> [!NOTE]
> Ao executar funções localmente, a autorização é desabilitada independentemente da configuração do nível de autorização especificado. Após a publicação no Azure, a configuração `authLevel` no gatilho é imposta. As chaves ainda são necessárias ao executar [localmente em um contêiner](functions-create-function-linux-custom-image.md#build-the-container-image-and-test-locally).


## <a name="secure-an-http-endpoint-in-production"></a>Proteger um ponto de extremidade HTTP em produção

Para proteger totalmente os pontos de extremidade de função na produção, considere a implementação de uma das seguintes opções de segurança no nível do aplicativo de funções:

* Ativar a autenticação/autorização do Serviço de Aplicativo para o aplicativo de funções. A plataforma do serviço de aplicativo permite que você use Azure Active Directory (AAD) e vários provedores de identidade de terceiros para autenticar clientes. Você pode usar essa estratégia para implementar regras de autorização personalizadas para suas funções e pode trabalhar com informações de usuário do seu código de função. Para saber mais, consulte [Autenticação e autorização no Serviço de Aplicativo do Azure](../app-service/overview-authentication-authorization.md) e [Trabalhando com identidades do cliente](#working-with-client-identities).

* Use o APIM (Gerenciamento de API do Azure) para autenticar solicitações. O APIM fornece uma variedade de opções de segurança de API para solicitações de entrada. Para saber mais, confira [Políticas de autenticação do Gerenciamento de API](../api-management/api-management-authentication-policies.md). Com o APIM em vigor, você pode configurar o aplicativo de funções para aceitar solicitações somente do endereço IP da sua instância do APIM. Para saber mais, confira [Restrições de endereço IP](ip-addresses.md#ip-address-restrictions).

* Implante seu aplicativo de funções em um ASE (Ambiente do Serviço de Aplicativo) do Azure. O ASE fornece um ambiente de hospedagem dedicado para que você execute suas funções. O ASE permite configurar um único gateway de front-end que você pode usar para autenticar todas as solicitações de entrada. Para obter mais informações, confira [Configurando um WAF (firewall do aplicativo Web) para o Ambiente do Serviço de Aplicativo](../app-service/environment/app-service-app-service-environment-web-application-firewall.md).

Ao usar um desses métodos de segurança de nível de aplicativo de função, você deve definir o nível de autorização da função disparada por HTTP como `anonymous`.

## <a name="webhooks"></a>Webhooks

> [!NOTE]
> O modo de webhook só está disponível para a versão 1.x do runtime do Functions. Essa alteração foi feita para melhorar o desempenho de gatilhos HTTP na versão 2. x e superior.

Na versão 1.x, os modelos de webhook fornecem validação adicional para conteúdo de webhook. Na versão 2. x e superior, o gatilho HTTP base ainda funciona e é a abordagem recomendada para WebHooks. 

### <a name="github-webhooks"></a>Webhooks do GitHub

Para responder a webhooks do GitHub, primeiramente crie sua função com um Gatilho HTTP e defina a propriedade **webHookType** como `github`. Em seguida, copie a URL e a chave de API na página **Adicionar webhook** do seu repositório GitHub. 

![](./media/functions-bindings-http-webhook/github-add-webhook.png)

### <a name="slack-webhooks"></a>Webhooks do Slack

O webhook do Slack gera um token em vez de permitir que você o especifique, de modo que é preciso configurar uma chave específica de função com o token do Slack. Consulte [Chaves de autorização](#authorization-keys).

## <a name="webhooks-and-keys"></a>Webhooks e chaves

A autorização de webhook é tratada pelo componente receptor do webhook, parte do gatilho HTTP e o mecanismo varia com base no tipo de webhook. Cada mecanismo conta com uma chave. Por padrão, a chave de função chamada "default" será usada. Para usar uma chave diferente, configure o provedor do webhook para enviar o nome da chave com a solicitação de uma das seguintes maneiras:

* **Cadeia de caracteres de consulta**: o provedor passa o nome da chave no parâmetro de cadeia de caracteres de consulta `clientid`, como `https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?clientid=<KEY_NAME>`.
* **Cabeçalho da solicitação**: o provedor passa o nome da chave no cabeçalho `x-functions-clientid`.

## <a name="limits"></a>limites

O tamanho da solicitação HTTP é limitado a 100 MB (104.857.600 bytes) e o tamanho da URL é limitado a 4 KB (4.096 bytes). Esses limites são especificados pelo `httpRuntime` elemento do [arquivo Web.config](https://github.com/Azure/azure-functions-host/blob/3.x/src/WebJobs.Script.WebHost/web.config) do runtime.

Se uma função que usa o gatilho HTTP não for concluída dentro de 230 segundos, o [Azure Load Balancer](../app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds) atingirá o tempo limite e retornará um erro http 502. A função continuará em execução, mas não poderá retornar uma resposta HTTP. Para funções de longa execução, é recomendável que você siga os padrões async e retorna um local onde você pode executar ping do status da solicitação. Para obter informações sobre o tempo que uma função pode executar, consulte [Dimensionamento e hospedagem - planejar o consumo](functions-scale.md#timeout).


## <a name="next-steps"></a>Próximas etapas

- [Retornar uma resposta HTTP de uma função](./functions-bindings-http-webhook-output.md)
