---
title: Vinculação de entrada do Azure Functions SignalR Service
description: Aprenda a retornar uma URL de ponto final de serviço SignalR e o token de acesso em Funções Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/20/2020
ms.author: cshoe
ms.openlocfilehash: 53d336aff3177a76c5e02266ffb8484bd9945119
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77530257"
---
# <a name="signalr-service-input-binding-for-azure-functions"></a>Vinculação de entrada do Serviço SignalR para funções do Azure

Antes que um cliente possa se conectar ao serviço do Azure SignalR, ele deve recuperar a URL do ponto de extremidade de serviço e um token de acesso válido. A associação de entrada *SignalRConnectionInfo* produz a URL do ponto de extremidade de Serviço SignalR e um token válido que são usados para se conectar ao serviço. Como o token é limitada pelo tempo e pode ser usado para autenticar um usuário específico para uma conexão, você não deve armazenar em cache o token nem o compartilhar entre clientes. Um gatilho HTTP que use essa associação pode ser usado pelos clientes para recuperar as informações de conexão.

Para obter mais informações sobre como essa vinculação é usada para criar uma função de "negociação" que pode ser consumida por um Cliente SignalR SDK, consulte o [artigo de desenvolvimento e configuração de funções do Azure](../azure-signalr/signalr-concept-serverless-development-config.md) na documentação de conceitos do SignalR Service.

Para obter informações sobre detalhes de configuração e configuração, consulte a [visão geral](functions-bindings-signalr-service.md).

## <a name="example"></a>Exemplo

# <a name="c"></a>[C #](#tab/csharp)

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que adquire as informações de conexão do SignalR usando a associação de entrada e retorna-a via HTTP.

```cs
[FunctionName("negotiate")]
public static SignalRConnectionInfo Negotiate(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req,
    [SignalRConnectionInfo(HubName = "chat")]SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

O exemplo a seguir mostra uma vinculação de entrada de informações de conexão SignalR em um arquivo *function.json* e uma [função C# Script](functions-reference-csharp.md) que usa a vinculação para retornar as informações de conexão.

Aqui estão os dados de associação no arquivo *function.json*:

function.json de exemplo:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Aqui está o código de script C#

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static SignalRConnectionInfo Run(HttpRequest req, SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

O exemplo a seguir mostra uma associação de entrada de informações de conexão do SignalR em um arquivo *function.json* e uma [função JavaScript](functions-reference-node.md) que usa a associação para retornar as informações de conexão.

Aqui estão os dados de associação no arquivo *function.json*:

function.json de exemplo:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Aqui está o código JavaScript:

```javascript
module.exports = async function (context, req, connectionInfo) {
    context.res.body = connectionInfo;
};
```

# <a name="python"></a>[Python](#tab/python)

O exemplo a seguir mostra uma vinculação de entrada de informações de conexão SignalR em um arquivo *function.json* e uma [função Python](functions-reference-python.md) que usa a vinculação para retornar as informações de conexão.

Aqui estão os dados de associação no arquivo *function.json*:

function.json de exemplo:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Aqui está o código Python:

```python
def main(req: func.HttpRequest, connectionInfoJson: str) -> func.HttpResponse:
    return func.HttpResponse(
        connectionInfoJson,
        status_code=200,
        headers={
            'Content-type': 'application/json'
        }
    )
```

# <a name="java"></a>[Java](#tab/java)

O exemplo a seguir mostra uma [função Java](functions-reference-java.md) que adquire informações de conexão SignalR usando a vinculação de entrada e a retorna em HTTP.

```java
@FunctionName("negotiate")
public SignalRConnectionInfo negotiate(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> req,
        @SignalRConnectionInfoInput(
            name = "connectionInfo",
            hubName = "chat") SignalRConnectionInfo connectionInfo) {
    return connectionInfo;
}
```

---

## <a name="authenticated-tokens"></a>Tokens autenticados

Se a função for disparada por um cliente autenticado, você poderá adicionar uma declaração de ID de usuário ao token gerado. Você pode facilmente adicionar autenticação a um aplicativo de função usando [a Autenticação do Serviço de Aplicativo](../app-service/overview-authentication-authorization.md).

Autenticação do Serviço de Aplicativo define os cabeçalhos HTTP denominados `x-ms-client-principal-id` e `x-ms-client-principal-name` que contêm a ID e o nome da entidade de segurança do cliente do usuário autenticado, respectivamente.

# <a name="c"></a>[C #](#tab/csharp)

Você pode `UserId` definir a propriedade da vinculação ao valor `{headers.x-ms-client-principal-id}` de `{headers.x-ms-client-principal-name}`qualquer cabeçalho usando uma [expressão vinculante](./functions-bindings-expressions-patterns.md): ou .

```cs
[FunctionName("negotiate")]
public static SignalRConnectionInfo Negotiate(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req, 
    [SignalRConnectionInfo
        (HubName = "chat", UserId = "{headers.x-ms-client-principal-id}")]
        SignalRConnectionInfo connectionInfo)
{
    // connectionInfo contains an access key token with a name identifier claim set to the authenticated user
    return connectionInfo;
}
```

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

Você pode `userId` definir a propriedade da vinculação ao valor `{headers.x-ms-client-principal-id}` de `{headers.x-ms-client-principal-name}`qualquer cabeçalho usando uma [expressão vinculante](./functions-bindings-expressions-patterns.md): ou .

function.json de exemplo:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "userId": "{headers.x-ms-client-principal-id}",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Aqui está o código de script C#

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static SignalRConnectionInfo Run(HttpRequest req, SignalRConnectionInfo connectionInfo)
{
    // connectionInfo contains an access key token with a name identifier
    // claim set to the authenticated user
    return connectionInfo;
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

Você pode `userId` definir a propriedade da vinculação ao valor `{headers.x-ms-client-principal-id}` de `{headers.x-ms-client-principal-name}`qualquer cabeçalho usando uma [expressão vinculante](./functions-bindings-expressions-patterns.md): ou .

function.json de exemplo:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "userId": "{headers.x-ms-client-principal-id}",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Aqui está o código JavaScript:

```javascript
module.exports = async function (context, req, connectionInfo) {
    // connectionInfo contains an access key token with a name identifier
    // claim set to the authenticated user
    context.res.body = connectionInfo;
};
```

# <a name="python"></a>[Python](#tab/python)

Você pode `userId` definir a propriedade da vinculação ao valor `{headers.x-ms-client-principal-id}` de `{headers.x-ms-client-principal-name}`qualquer cabeçalho usando uma [expressão vinculante](./functions-bindings-expressions-patterns.md): ou .

function.json de exemplo:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "userId": "{headers.x-ms-client-principal-id}",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Aqui está o código Python:

```python
def main(req: func.HttpRequest, connectionInfoJson: str) -> func.HttpResponse:
    # connectionInfo contains an access key token with a name identifier
    # claim set to the authenticated user
    return func.HttpResponse(
        connectionInfoJson,
        status_code=200,
        headers={
            'Content-type': 'application/json'
        }
    )
```

# <a name="java"></a>[Java](#tab/java)

Você pode `userId` definir a propriedade da vinculação ao valor `{headers.x-ms-client-principal-id}` de `{headers.x-ms-client-principal-name}`qualquer cabeçalho usando uma [expressão vinculante](./functions-bindings-expressions-patterns.md): ou .

```java
@FunctionName("negotiate")
public SignalRConnectionInfo negotiate(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> req,
        @SignalRConnectionInfoInput(
            name = "connectionInfo",
            hubName = "chat",
            userId = "{headers.x-ms-client-principal-id}") SignalRConnectionInfo connectionInfo) {
    return connectionInfo;
}
```

---

## <a name="next-steps"></a>Próximas etapas

- [Enviar mensagens de serviço signalR (vinculação de saída)](./functions-bindings-signalr-service-output.md) 
