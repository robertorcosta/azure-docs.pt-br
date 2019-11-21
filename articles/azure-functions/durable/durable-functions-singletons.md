---
title: Singletons para Funções Duráveis – Azure
description: Como usar singletons na extensão Durable Functions do Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 8c12e0ab854bb2b5764dd326e3f1649202f6f16b
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232812"
---
# <a name="singleton-orchestrators-in-durable-functions-azure-functions"></a>Orquestradores singleton em Funções Duráveis (Azure Functions)

For background jobs, you often need to ensure that only one instance of a particular orchestrator runs at a time. You can ensure this kind of singleton behavior in [Durable Functions](durable-functions-overview.md) by assigning a specific instance ID to an orchestrator when creating it.

## <a name="singleton-example"></a>Exemplo de singleton

The following example shows an HTTP-trigger function that creates a singleton background job orchestration. O código garante que apenas uma instância exista para uma ID de instância especificada.

### <a name="c"></a>C#

```cs
[FunctionName("HttpStartSingle")]
public static async Task<HttpResponseMessage> RunSingle(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}/{instanceId}")] HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient starter,
    string functionName,
    string instanceId,
    ILogger log)
{
    // Check if an instance with the specified ID already exists.
    var existingInstance = await starter.GetStatusAsync(instanceId);
    if (existingInstance == null)
    {
        // An instance with the specified ID doesn't exist, create one.
        dynamic eventData = await req.Content.ReadAsAsync<object>();
        await starter.StartNewAsync(functionName, instanceId, eventData);
        log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
        return starter.CreateCheckStatusResponse(req, instanceId);
    }
    else
    {
        // An instance with the specified ID exists, don't create one.
        return req.CreateErrorResponse(
            HttpStatusCode.Conflict,
            $"An instance with ID '{instanceId}' already exists.");
    }
}
```

> [!NOTE]
> The previous C# code is for Durable Functions 2.x. For Durable Functions 1.x, you must use `OrchestrationClient` attribute instead of the `DurableClient` attribute, and you must use the `DurableOrchestrationClient` parameter type instead of `IDurableOrchestrationClient`. For more information about the differences between versions, see the [Durable Functions versions](durable-functions-versions.md) article.

### <a name="javascript-functions-20-only"></a>Javascript (somente funções 2.0)

Aqui está o arquivo function.json:
```json
{
  "bindings": [
    {
      "authLevel": "function",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "route": "orchestrators/{functionName}/{instanceId}",
      "methods": ["post"]
    },
    {
      "name": "starter",
      "type": "orchestrationClient",
      "direction": "in"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ]
}
```

Aqui está o código JavaScript:
```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const instanceId = req.params.instanceId;
    const functionName = req.params.functionName;

    // Check if an instance with the specified ID already exists.
    const existingInstance = await client.getStatus(instanceId);
    if (!existingInstance) {
        // An instance with the specified ID doesn't exist, create one.
        const eventData = req.body;
        await client.startNew(functionName, instanceId, eventData);
        context.log(`Started orchestration with ID = '${instanceId}'.`);
        return client.createCheckStatusResponse(req, instanceId);
    } else {
        // An instance with the specified ID exists, don't create one.
        return {
            status: 409,
            body: `An instance with ID '${instanceId}' already exists.`,
        };
    }
};
```

Por padrão, IDs de instância são GUIDs gerados aleatoriamente. In the previous example, however, the instance ID is passed in route data from the URL. The code calls `GetStatusAsync`(C#) or `getStatus` (JavaScript) to check if an instance having the specified ID is already running. If no such instance is running, a new instance is created with that ID.

> [!NOTE]
> Há uma condição de corrida potencial neste exemplo. Se duas instâncias de **HttpStartSingle** forem executadas simultaneamente, as chamadas de função relatarão êxito, mas somente uma instância de orquestração será realmente iniciada. Dependendo dos seus requisitos, isso pode ter efeitos colaterais indesejáveis. Por esse motivo, é importante garantir que nenhum duas solicitações podem executar essa função de gatilho simultaneamente.

The implementation details of the orchestrator function don't actually matter. Poderia ser uma função de orquestrador regular que é iniciada e concluída ou poderia ser uma que é executada eternamente (ou seja, um [Orquestração Eterna](durable-functions-eternal-orchestrations.md)). O aspecto importante é que há somente uma instância em execução por vez.

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Learn about the native HTTP features of orchestrations](durable-functions-http-features.md)
