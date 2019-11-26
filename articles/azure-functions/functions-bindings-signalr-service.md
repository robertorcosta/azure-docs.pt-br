---
title: Associações do Serviço SignalR do Azure Functions
description: Entenda como usar associações do Serviço SignalR com o Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/28/2019
ms.author: cshoe
ms.openlocfilehash: 4c7d5d4d8777fee445585b43b58ceb261176b7f4
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231014"
---
# <a name="signalr-service-bindings-for-azure-functions"></a>Associações do Serviço SignalR para o Azure Functions

Este artigo explica como autenticar e enviar mensagens em tempo real para clientes conectados ao [Serviço do Azure SignalR](https://azure.microsoft.com/services/signalr-service/) usando associações do Serviço SignalR no Azure Functions. O Azure Functions dá suporte a associações de entrada e saída para o Serviço SignalR.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x"></a>Pacotes - Functions 2. x

As associações de serviço Signalr são fornecidas no pacote NuGet [Microsoft. Azure. webjobs. Extensions. SignalRService](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService) , versão 1. *. O código-fonte do pacote está no repositório GitHub [azure-functions-signalrservice-extension](https://github.com/Azure/azure-functions-signalrservice-extension).

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2-manual-portal.md)]


### <a name="java-annotations"></a>Anotações de Java

Para usar as anotações do serviço Signalr em funções Java, você precisa adicionar uma dependência ao artefato do *Azure-Functions-Java-library-signalr* (versão 1,0 ou superior) para o pom. xml.

```xml
<dependency>
    <groupId>com.microsoft.azure.functions</groupId>
    <artifactId>azure-functions-java-library-signalr</artifactId>
    <version>1.0.0</version>
</dependency>
```

> [!NOTE]
> Para usar as associações do Serviço do SignalR em Java, verifique se você está usando a versão 2.4.419 ou posterior do Azure Functions Core Tools (versão do host 2.0.12332).

## <a name="using-signalr-service-with-azure-functions"></a>Usando o serviço de sinalização com Azure Functions

Para obter detalhes sobre como configurar e usar o serviço de sinalização e Azure Functions juntos, consulte [Azure Functions desenvolvimento e configuração com o serviço de signaler do Azure](../azure-signalr/signalr-concept-serverless-development-config.md).

## <a name="signalr-connection-info-input-binding"></a>Associação de entrada de informações da conexão do SignalR

Antes que um cliente possa se conectar ao serviço do Azure SignalR, ele deve recuperar a URL do ponto de extremidade de serviço e um token de acesso válido. A associação de entrada *SignalRConnectionInfo* produz a URL do ponto de extremidade de Serviço SignalR e um token válido que são usados para se conectar ao serviço. Como o token é limitada pelo tempo e pode ser usado para autenticar um usuário específico para uma conexão, você não deve armazenar em cache o token nem o compartilhar entre clientes. Um gatilho HTTP que use essa associação pode ser usado pelos clientes para recuperar as informações de conexão.

Consulte o exemplo específico a um idioma:

* [2.x C#](#2x-c-input-examples)
* [2.x JavaScript](#2x-javascript-input-examples)
* [2. x Java](#2x-java-input-examples)

Para obter mais informações sobre como essa associação é usada para criar uma função "Negotiate" que pode ser consumida por um SDK de cliente do Signalr, consulte o [artigo Azure Functions desenvolvimento e configuração](../azure-signalr/signalr-concept-serverless-development-config.md) na documentação conceitos do serviço signalr.

### <a name="2x-c-input-examples"></a>exemplos de entrada C# 2. x

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

#### <a name="authenticated-tokens"></a>Tokens autenticados

Se a função for disparada por um cliente autenticado, você poderá adicionar uma declaração de ID de usuário ao token gerado. Você pode adicionar facilmente a autenticação a um aplicativo de funções usando a [autenticação do serviço de aplicativo](../app-service/overview-authentication-authorization.md).

Autenticação do Serviço de Aplicativo define os cabeçalhos HTTP denominados `x-ms-client-principal-id` e `x-ms-client-principal-name` que contêm a ID e o nome da entidade de segurança do cliente do usuário autenticado, respectivamente. Você pode definir a propriedade `UserId` da associação como o valor do cabeçalho usando uma [expressão de associação](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` ou `{headers.x-ms-client-principal-name}`. 

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

### <a name="2x-javascript-input-examples"></a>2. x exemplos de entrada JavaScript

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

#### <a name="authenticated-tokens"></a>Tokens autenticados

Se a função for disparada por um cliente autenticado, você poderá adicionar uma declaração de ID de usuário ao token gerado. Você pode adicionar facilmente a autenticação a um aplicativo de funções usando a [autenticação do serviço de aplicativo](../app-service/overview-authentication-authorization.md).

Autenticação do Serviço de Aplicativo define os cabeçalhos HTTP denominados `x-ms-client-principal-id` e `x-ms-client-principal-name` que contêm a ID e o nome da entidade de segurança do cliente do usuário autenticado, respectivamente. Você pode definir a propriedade `userId` da associação como o valor do cabeçalho usando uma [expressão de associação](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` ou `{headers.x-ms-client-principal-name}`. 

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

### <a name="2x-java-input-examples"></a>2. x exemplos de entrada Java

O exemplo a seguir mostra uma [função Java](functions-reference-java.md) que adquire informações de conexão do signalr usando a associação de entrada e a retorna sobre http.

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

#### <a name="authenticated-tokens"></a>Tokens autenticados

Se a função for disparada por um cliente autenticado, você poderá adicionar uma declaração de ID de usuário ao token gerado. Você pode adicionar facilmente a autenticação a um aplicativo de funções usando a [autenticação do serviço de aplicativo](../app-service/overview-authentication-authorization.md).

Autenticação do Serviço de Aplicativo define os cabeçalhos HTTP denominados `x-ms-client-principal-id` e `x-ms-client-principal-name` que contêm a ID e o nome da entidade de segurança do cliente do usuário autenticado, respectivamente. Você pode definir a propriedade `UserId` da associação como o valor do cabeçalho usando uma [expressão de associação](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` ou `{headers.x-ms-client-principal-name}`.

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

## <a name="signalr-output-binding"></a>Associação de saída do SignalR

Use a associação de saída do *SignalR* para enviar uma ou mais mensagens usando o Serviço Azure SignalR. Você pode difundir uma mensagem a todos os clientes conectados, ou pode difundi-la somente para clientes conectados que foram autenticados em um determinado usuário.

Você também pode usá-lo para gerenciar os grupos aos quais um usuário pertence.

Consulte o exemplo específico a um idioma:

* [2.x C#](#2x-c-send-message-output-examples)
* [2.x JavaScript](#2x-javascript-send-message-output-examples)
* [2. x Java](#2x-java-send-message-output-examples)

### <a name="2x-c-send-message-output-examples"></a>2. x C# exemplos de saída de mensagem de envio

#### <a name="broadcast-to-all-clients"></a>Difundir para todos os clientes

A exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que envia uma mensagem usando a associação de saída para todos os clientes conectados. O `Target` é o nome do método a ser invocado em cada cliente. A propriedade `Arguments` é uma matriz de zero ou mais objetos a serem passados para o método do cliente.

```cs
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message, 
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage 
        {
            Target = "newMessage", 
            Arguments = new [] { message } 
        });
}
```

#### <a name="send-to-a-user"></a>Enviar para um usuário

Você pode enviar uma mensagem apenas para conexões autenticadas para um usuário configurando a propriedade `UserId` da mensagem do SignalR.

```cs
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message, 
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage 
        {
            // the message will only be sent to this user ID
            UserId = "userId1",
            Target = "newMessage",
            Arguments = new [] { message }
        });
}
```

#### <a name="send-to-a-group"></a>Enviar para um grupo

Você pode enviar uma mensagem somente para conexões que foram adicionadas a um grupo, definindo a propriedade `GroupName` da mensagem do Signalr.

```cs
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message,
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage
        {
            // the message will be sent to the group with this name
            GroupName = "myGroup",
            Target = "newMessage",
            Arguments = new [] { message }
        });
}
```

### <a name="2x-c-group-management-output-examples"></a>exemplos de saída C# de gerenciamento de grupo 2. x

O serviço signalr permite que os usuários sejam adicionados a grupos. As mensagens podem ser enviadas a um grupo. Você pode usar a classe `SignalRGroupAction` com a associação de saída `SignalR` para gerenciar a associação de grupo de um usuário.

#### <a name="add-user-to-a-group"></a>Adicionar usuário a um grupo

O exemplo a seguir adiciona um usuário a um grupo.

```csharp
[FunctionName("addToGroup")]
public static Task AddToGroup(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]HttpRequest req,
    ClaimsPrincipal claimsPrincipal,
    [SignalR(HubName = "chat")]
        IAsyncCollector<SignalRGroupAction> signalRGroupActions)
{
    var userIdClaim = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier);
    return signalRGroupActions.AddAsync(
        new SignalRGroupAction
        {
            UserId = userIdClaim.Value,
            GroupName = "myGroup",
            Action = GroupAction.Add
        });
}
```

#### <a name="remove-user-from-a-group"></a>Remover usuário de um grupo

O exemplo a seguir remove um usuário de um grupo.

```csharp
[FunctionName("removeFromGroup")]
public static Task RemoveFromGroup(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]HttpRequest req,
    ClaimsPrincipal claimsPrincipal,
    [SignalR(HubName = "chat")]
        IAsyncCollector<SignalRGroupAction> signalRGroupActions)
{
    var userIdClaim = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier);
    return signalRGroupActions.AddAsync(
        new SignalRGroupAction
        {
            UserId = userIdClaim.Value,
            GroupName = "myGroup",
            Action = GroupAction.Remove
        });
}
```

> [!NOTE]
> Para obter o `ClaimsPrincipal` corretamente associado, você deve ter definido as configurações de autenticação no Azure Functions.

### <a name="2x-javascript-send-message-output-examples"></a>2. x exemplos de saída de mensagem de envio JavaScript

#### <a name="broadcast-to-all-clients"></a>Difundir para todos os clientes

O exemplo a seguir mostra uma associação de saída do SignalR em um arquivo *function.json* e uma [função JavaScript](functions-reference-node.md) que usa a associação para enviar uma mensagem com o Serviço do Azure SignalR. Defina a associação de saída para uma matriz de uma ou mais mensagens do SignalR. Uma mensagem do SignalR consiste em uma propriedade `target` que especifica o nome do método a ser invocado em cada cliente, e uma propriedade `arguments` que é uma matriz de objetos a serem passados para o método de cliente como argumentos.

Aqui estão os dados de associação no arquivo *function.json*:

function.json de exemplo:

```json
{
  "type": "signalR",
  "name": "signalRMessages",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

Aqui está o código JavaScript:

```javascript
module.exports = async function (context, req) {
    context.bindings.signalRMessages = [{
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
};
```

#### <a name="send-to-a-user"></a>Enviar para um usuário

Você pode enviar uma mensagem apenas para conexões autenticadas para um usuário configurando a propriedade `userId` da mensagem do SignalR.

*function.json* permanece igual. Aqui está o código JavaScript:

```javascript
module.exports = async function (context, req) {
    context.bindings.signalRMessages = [{
        // message will only be sent to this user ID
        "userId": "userId1",
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
};
```

#### <a name="send-to-a-group"></a>Enviar para um grupo

Você pode enviar uma mensagem somente para conexões que foram adicionadas a um grupo, definindo a propriedade `groupName` da mensagem do Signalr.

*function.json* permanece igual. Aqui está o código JavaScript:

```javascript
module.exports = async function (context, req) {
    context.bindings.signalRMessages = [{
        // message will only be sent to this group
        "groupName": "myGroup",
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
};
```

### <a name="2x-javascript-group-management-output-examples"></a>2. x exemplos de saída de gerenciamento de grupo JavaScript

O serviço signalr permite que os usuários sejam adicionados a grupos. As mensagens podem ser enviadas a um grupo. Você pode usar a associação de saída `SignalR` para gerenciar a associação de grupo de um usuário.

#### <a name="add-user-to-a-group"></a>Adicionar usuário a um grupo

O exemplo a seguir adiciona um usuário a um grupo.

*function.json*

```json
{
  "disabled": false,
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    },
    {
      "type": "signalR",
      "name": "signalRGroupActions",
      "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
      "hubName": "chat",
      "direction": "out"
    }
  ]
}
```

*index.js*

```javascript
module.exports = async function (context, req) {
  context.bindings.signalRGroupActions = [{
    "userId": req.query.userId,
    "groupName": "myGroup",
    "action": "add"
  }];
};
```

#### <a name="remove-user-from-a-group"></a>Remover usuário de um grupo

O exemplo a seguir remove um usuário de um grupo.

*function.json*

```json
{
  "disabled": false,
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    },
    {
      "type": "signalR",
      "name": "signalRGroupActions",
      "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
      "hubName": "chat",
      "direction": "out"
    }
  ]
}
```

*index.js*

```javascript
module.exports = async function (context, req) {
  context.bindings.signalRGroupActions = [{
    "userId": req.query.userId,
    "groupName": "myGroup",
    "action": "remove"
  }];
};
```

### <a name="2x-java-send-message-output-examples"></a>2. x exemplos de saída de mensagem de envio Java

#### <a name="broadcast-to-all-clients"></a>Difundir para todos os clientes

O exemplo a seguir mostra uma [função Java](functions-reference-java.md) que envia uma mensagem usando a associação de saída para todos os clientes conectados. O `target` é o nome do método a ser invocado em cada cliente. A propriedade `arguments` é uma matriz de zero ou mais objetos a serem passados para o método do cliente.

```java
@FunctionName("sendMessage")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRMessage sendMessage(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req) {

    SignalRMessage message = new SignalRMessage();
    message.target = "newMessage";
    message.arguments.add(req.getBody());
    return message;
}
```

#### <a name="send-to-a-user"></a>Enviar para um usuário

Você pode enviar uma mensagem apenas para conexões autenticadas para um usuário configurando a propriedade `userId` da mensagem do SignalR.

```java
@FunctionName("sendMessage")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRMessage sendMessage(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req) {

    SignalRMessage message = new SignalRMessage();
    message.userId = "userId1";
    message.target = "newMessage";
    message.arguments.add(req.getBody());
    return message;
}
```

#### <a name="send-to-a-group"></a>Enviar para um grupo

Você pode enviar uma mensagem somente para conexões que foram adicionadas a um grupo, definindo a propriedade `groupName` da mensagem do Signalr.

```java
@FunctionName("sendMessage")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRMessage sendMessage(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req) {

    SignalRMessage message = new SignalRMessage();
    message.groupName = "myGroup";
    message.target = "newMessage";
    message.arguments.add(req.getBody());
    return message;
}
```

### <a name="2x-java-group-management-output-examples"></a>2. x exemplos de saída de gerenciamento de grupo Java

O serviço signalr permite que os usuários sejam adicionados a grupos. As mensagens podem ser enviadas a um grupo. Você pode usar a classe `SignalRGroupAction` com a associação de saída `SignalROutput` para gerenciar a associação de grupo de um usuário.

#### <a name="add-user-to-a-group"></a>Adicionar usuário a um grupo

O exemplo a seguir adiciona um usuário a um grupo.

```java
@FunctionName("addToGroup")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRGroupAction addToGroup(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req,
        @BindingName("userId") String userId) {

    SignalRGroupAction groupAction = new SignalRGroupAction();
    groupAction.action = "add";
    groupAction.userId = userId;
    groupAction.groupName = "myGroup";
    return action;
}
```

#### <a name="remove-user-from-a-group"></a>Remover usuário de um grupo

O exemplo a seguir remove um usuário de um grupo.

```java
@FunctionName("removeFromGroup")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRGroupAction removeFromGroup(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req,
        @BindingName("userId") String userId) {

    SignalRGroupAction groupAction = new SignalRGroupAction();
    groupAction.action = "remove";
    groupAction.userId = userId;
    groupAction.groupName = "myGroup";
    return action;
}
```

## <a name="configuration"></a>Configuração

### <a name="signalrconnectioninfo"></a>SignalRConnectionInfo

A tabela a seguir explica as propriedades de configuração de associação que você definir no arquivo *function.json* e o `SignalRConnectionInfo` atributo.

|Propriedade function.json | Propriedade de atributo |DESCRIÇÃO|
|---------|---------|----------------------|
|**tipo**|| Deve ser definido como `signalRConnectionInfo`.|
|**direction**|| Deve ser definido como `in`.|
|**name**|| Nome da variável usada no código de função para o objeto de informações de conexão. |
|**hubName**|**HubName**| Esse valor deve ser definido como o nome do hub SignalR para o qual as informações de conexão são geradas.|
|**userId**|**UserId**| Opcional: o valor da declaração do identificador de usuário a ser definida no token de chave de acesso. |
|**connectionStringSetting**|**ConnectionStringSetting**| O nome da configuração do aplicativo que contém a cadeia de conexão do SignalR Service (o padrão é "AzureSignalRConnectionString") |

### <a name="signalr"></a>SignalR

A tabela a seguir explica as propriedades de configuração de associação que você definir no arquivo *function.json* e o `SignalR` atributo.

|Propriedade function.json | Propriedade de atributo |DESCRIÇÃO|
|---------|---------|----------------------|
|**tipo**|| Deve ser definido como `signalR`.|
|**direction**|| Deve ser definido como `out`.|
|**name**|| Nome da variável usada no código de função para o objeto de informações de conexão. |
|**hubName**|**HubName**| Esse valor deve ser definido como o nome do hub SignalR para o qual as informações de conexão são geradas.|
|**connectionStringSetting**|**ConnectionStringSetting**| O nome da configuração do aplicativo que contém a cadeia de conexão do SignalR Service (o padrão é "AzureSignalRConnectionString") |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Aprenda mais sobre gatilhos e de associações do Azure Functions](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Desenvolvimento de funções do Azure e a configuração com o serviço do Azure SignalR](../azure-signalr/signalr-concept-serverless-development-config.md)
