---
title: Singletons para Funções Duráveis – Azure
description: Como usar singletons na extensão Durable Functions do Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: azfuncdf
ms.openlocfilehash: deb64cf8128fd548cb74c064ab9fd6f169db5300
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87041930"
---
# <a name="singleton-orchestrators-in-durable-functions-azure-functions"></a>Orquestradores singleton em Funções Duráveis (Azure Functions)

Para trabalhos em segundo plano, você geralmente precisa garantir que apenas uma instância de um orquestrador específico seja executada por vez. Você pode garantir esse tipo de comportamento singleton no [Durable Functions](durable-functions-overview.md) atribuindo uma ID de instância específica a um orquestrador ao criá-lo.

## <a name="singleton-example"></a>Exemplo de singleton

O exemplo a seguir mostra uma função HTTP-Trigger que cria uma orquestração de trabalho em segundo plano singleton. O código garante que apenas uma instância exista para uma ID de instância especificada.

# <a name="c"></a>[C#](#tab/csharp)

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
> O código C# anterior é para Durable Functions 2. x. Para Durable Functions 1. x, você deve usar `OrchestrationClient` o atributo em vez do `DurableClient` atributo, e deve usar o `DurableOrchestrationClient` tipo de parâmetro em vez de `IDurableOrchestrationClient` . Para obter mais informações sobre as diferenças entre versões, consulte o artigo [Durable Functions versões](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

# <a name="python"></a>[Python](#tab/python)

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

**__init__. py**

```python
import logging
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)
    instance_id = req.route_params['instanceId']
    function_name = req.route_params['functionName']

    existing_instance = await client.get_status(instance_id)

    if existing_instance != None:
        event_data = req.get_body()
        instance_id = await client.start_new(function_name, instance_id, event_data)
        logging.info(f"Started orchestration with ID = '{instance_id}'.")
        return client.create_check_status_response(req, instance_id)
    else:
        return {
            'status': 409,
            'body': f"An instance with ID '${instance_id}' already exists"
        }

```

---

Por padrão, IDs de instância são GUIDs gerados aleatoriamente. No exemplo anterior, no entanto, a ID da instância é passada em dados de rota da URL. O código chama `GetStatusAsync` (C#), `getStatus` (JavaScript) ou `get_status` (Python) para verificar se uma instância com a ID especificada já está em execução. Se nenhuma instância desse tipo estiver em execução, uma nova instância será criada com essa ID.

> [!NOTE]
> Há uma condição de corrida potencial neste exemplo. Se duas instâncias de **HttpStartSingle** forem executadas simultaneamente, as chamadas de função relatarão êxito, mas somente uma instância de orquestração será realmente iniciada. Dependendo dos seus requisitos, isso pode ter efeitos colaterais indesejáveis. Por esse motivo, é importante garantir que nenhum duas solicitações podem executar essa função de gatilho simultaneamente.

Os detalhes de implementação da função de orquestrador não são realmente importantes. Poderia ser uma função de orquestrador regular que é iniciada e concluída ou poderia ser uma que é executada eternamente (ou seja, um [Orquestração Eterna](durable-functions-eternal-orchestrations.md)). O aspecto importante é que há somente uma instância em execução por vez.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba mais sobre os recursos HTTP nativos de orquestrações](durable-functions-http-features.md)
