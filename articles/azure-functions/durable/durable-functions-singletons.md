---
title: Singletons para Funções Duráveis – Azure
description: Como usar singletons na extensão Durable Functions do Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 4eff7c4c91ed664fcf1f4fc7a8be2d43d24e5c6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76262802"
---
# <a name="singleton-orchestrators-in-durable-functions-azure-functions"></a>Orquestradores singleton em Funções Duráveis (Azure Functions)

Para trabalhos de fundo, muitas vezes você precisa garantir que apenas uma instância de um determinado orquestrador seja executada por vez. Você pode garantir esse tipo de comportamento singleton em [Funções Duráveis](durable-functions-overview.md) atribuindo um ID de instância específica a um orquestrador ao criá-lo.

## <a name="singleton-example"></a>Exemplo de singleton

O exemplo a seguir mostra uma função http-trigger que cria uma orquestração de trabalho de fundo singleton. O código garante que apenas uma instância exista para uma ID de instância especificada.

# <a name="c"></a>[C #](#tab/csharp)

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
> O código C# anterior é para Funções Duráveis 2.x. Para funções duráveis 1.x, você `OrchestrationClient` `DurableClient` deve usar atributo `DurableOrchestrationClient` em vez do `IDurableOrchestrationClient`atributo, e você deve usar o tipo de parâmetro em vez de . Para obter mais informações sobre as diferenças entre as versões, consulte o artigo [de funções duráveis.](durable-functions-versions.md)

# <a name="javascript"></a>[Javascript](#tab/javascript)

**function.json**

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

**index.js**

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

---

Por padrão, IDs de instância são GUIDs gerados aleatoriamente. No exemplo anterior, no entanto, o ID de ocorrência é passado em dados de rota da URL. O código `GetStatusAsync`chama (C#) ou `getStatus` (JavaScript) para verificar se uma instância com o ID especificado já está sendo executado. Se nenhuma dessas instâncias estiver sendo realizada, uma nova instância será criada com esse ID.

> [!NOTE]
> Há uma condição de corrida potencial neste exemplo. Se duas instâncias de **HttpStartSingle** forem executadas simultaneamente, as chamadas de função relatarão êxito, mas somente uma instância de orquestração será realmente iniciada. Dependendo dos seus requisitos, isso pode ter efeitos colaterais indesejáveis. Por esse motivo, é importante garantir que nenhum duas solicitações podem executar essa função de gatilho simultaneamente.

Os detalhes de implementação da função orquestradora não importam. Poderia ser uma função de orquestrador regular que é iniciada e concluída ou poderia ser uma que é executada eternamente (ou seja, um [Orquestração Eterna](durable-functions-eternal-orchestrations.md)). O aspecto importante é que há somente uma instância em execução por vez.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Conheça os recursos http nativos das orquestrações](durable-functions-http-features.md)
