---
title: Lidando com eventos externos nas Funções Duráveis – Azure
description: Saiba como lidar com eventos externos na extensão de Funções Duráveis do Azure Functions.
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 7d7fcc725d78b24a93b09cb9c76cf7dc0231cac2
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232889"
---
# <a name="handling-external-events-in-durable-functions-azure-functions"></a>Lidando com eventos externos nas Funções Duráveis (Azure Functions)

Funções de orquestrador têm a capacidade de aguardar e escutar eventos externos. Frequentemente, esse recurso das [Funções Duráveis](durable-functions-overview.md) é útil para lidar com interação humana ou com outros gatilhos externos.

> [!NOTE]
> Eventos externos são operações assíncronas unidirecionais. Não são adequados para situações em que o cliente que envia o evento precisa de uma resposta síncrona da função de orquestrador.

## <a name="wait-for-events"></a>Aguardar eventos

The `WaitForExternalEvent` (.NET) and `waitForExternalEvent` (JavaScript) methods of the [orchestration trigger binding](durable-functions-bindings.md#orchestration-trigger) allows an orchestrator function to asynchronously wait and listen for an external event. A função do orquestrador que está escutando declara o *nome* do evento e a *forma dos dados* que espera receber.

### <a name="c"></a>C#

```csharp
[FunctionName("BudgetApproval")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool approved = await context.WaitForExternalEvent<bool>("Approval");
    if (approved)
    {
        // approval granted - do the approved action
    }
    else
    {
        // approval denied - send a notification
    }
}
```

> [!NOTE]
> The previous C# code is for Durable Functions 2.x. For Durable Functions 1.x, you must use `DurableOrchestrationContext` instead of `IDurableOrchestrationContext`. For more information about the differences between versions, see the [Durable Functions versions](durable-functions-versions.md) article.

### <a name="javascript-functions-20-only"></a>Javascript (somente funções 2.0)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const approved = yield context.df.waitForExternalEvent("Approval");
    if (approved) {
        // approval granted - do the approved action
    } else {
        // approval denied - send a notification
    }
});
```

O exemplo anterior escuta um único evento específico e entra em ação quando ele é recebido.

Você pode escutar vários eventos ao mesmo tempo, como no exemplo a seguir, que aguarda uma de três notificações de evento possíveis.

#### <a name="c"></a>C#

```csharp
[FunctionName("Select")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var event1 = context.WaitForExternalEvent<float>("Event1");
    var event2 = context.WaitForExternalEvent<bool>("Event2");
    var event3 = context.WaitForExternalEvent<int>("Event3");

    var winner = await Task.WhenAny(event1, event2, event3);
    if (winner == event1)
    {
        // ...
    }
    else if (winner == event2)
    {
        // ...
    }
    else if (winner == event3)
    {
        // ...
    }
}
```

> [!NOTE]
> The previous C# code is for Durable Functions 2.x. For Durable Functions 1.x, you must use `DurableOrchestrationContext` instead of `IDurableOrchestrationContext`. For more information about the differences between versions, see the [Durable Functions versions](durable-functions-versions.md) article.

#### <a name="javascript-functions-20-only"></a>Javascript (somente funções 2.0)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const event1 = context.df.waitForExternalEvent("Event1");
    const event2 = context.df.waitForExternalEvent("Event2");
    const event3 = context.df.waitForExternalEvent("Event3");

    const winner = yield context.df.Task.any([event1, event2, event3]);
    if (winner === event1) {
        // ...
    } else if (winner === event2) {
        // ...
    } else if (winner === event3) {
        // ...
    }
});
```

O exemplo anterior escuta *qualquer* um de vários eventos. Também é possível aguardar *todos* os eventos.

#### <a name="c"></a>C#

```csharp
[FunctionName("NewBuildingPermit")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string applicationId = context.GetInput<string>();

    var gate1 = context.WaitForExternalEvent("CityPlanningApproval");
    var gate2 = context.WaitForExternalEvent("FireDeptApproval");
    var gate3 = context.WaitForExternalEvent("BuildingDeptApproval");

    // all three departments must grant approval before a permit can be issued
    await Task.WhenAll(gate1, gate2, gate3);

    await context.CallActivityAsync("IssueBuildingPermit", applicationId);
}
```

> [!NOTE]
> The previous code is for Durable Functions 2.x. For Durable Functions 1.x, you must use `DurableOrchestrationContext` instead of `IDurableOrchestrationContext`. For more information about the differences between versions, see the [Durable Functions versions](durable-functions-versions.md) article.

#### <a name="javascript-functions-20-only"></a>Javascript (somente funções 2.0)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const applicationId = context.df.getInput();

    const gate1 = context.df.waitForExternalEvent("CityPlanningApproval");
    const gate2 = context.df.waitForExternalEvent("FireDeptApproval");
    const gate3 = context.df.waitForExternalEvent("BuildingDeptApproval");

    // all three departments must grant approval before a permit can be issued
    yield context.df.Task.all([gate1, gate2, gate3]);

    yield context.df.callActivity("IssueBuildingPermit", applicationId);
});
```

`WaitForExternalEvent` waits indefinitely for some input.  O aplicativo de funções pode ser descarregado com segurança enquanto aguarda. Se um evento chegar para esta instância de orquestração, ela será ativada automaticamente e processará imediatamente o evento.

> [!NOTE]
> Se o seu aplicativo de funções utiliza o Plano de Consumo, não são feitas cobranças enquanto uma função de orquestrador está aguardando uma tarefa de `WaitForExternalEvent` (.NET) ou `waitForExternalEvent` (JavaScript), independentemente do tempo de espera.

No .NET, se a carga do evento não puder ser convertida no `T` do tipo esperado, uma exceção será lançada.

## <a name="send-events"></a>Enviar eventos

The `RaiseEventAsync` (.NET) or `raiseEvent` (JavaScript) method of the [orchestration client binding](durable-functions-bindings.md#orchestration-client) sends the events that `WaitForExternalEvent` (.NET) or `waitForExternalEvent` (JavaScript) waits for.  O método `RaiseEventAsync` usa *eventName* e *eventData* como parâmetros. Os dados do evento devem ser serializáveis em JSON.

Abaixo, temos um exemplo de função disparada em fila que envia um evento de "Aprovação" para uma instância de função de orquestrador. A ID da instância de orquestração vem do corpo da mensagem da fila.

### <a name="c"></a>C#

```csharp
[FunctionName("ApprovalQueueProcessor")]
public static async Task Run(
    [QueueTrigger("approval-queue")] string instanceId,
    [DurableClient] IDurableOrchestrationClient client)
{
    await client.RaiseEventAsync(instanceId, "Approval", true);
}
```

> [!NOTE]
> The previous C# code is for Durable Functions 2.x. For Durable Functions 1.x, you must use `OrchestrationClient` attribute instead of the `DurableClient` attribute, and you must use the `DurableOrchestrationClient` parameter type instead of `IDurableOrchestrationClient`. For more information about the differences between versions, see the [Durable Functions versions](durable-functions-versions.md) article.

### <a name="javascript-functions-20-only"></a>Javascript (somente funções 2.0)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);
    await client.raiseEvent(instanceId, "Approval", true);
};
```

Internamente, `RaiseEventAsync` (.NET) ou `raiseEvent` (JavaScript) enfileira uma mensagem que é obtida pela função do orquestrador em espera. Se a instância não está aguardando o *nome do evento* especificado, a mensagem de evento é adicionada a uma fila na memória. Se posteriormente a instância de orquestração começa a escutar esse *nome do evento*, ela verifica a fila de mensagens de evento.

> [!NOTE]
> Se não houver nenhuma instância de orquestração com a *ID da instância* especificada, a mensagem de evento é descartada.

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Learn how to implement error handling](durable-functions-error-handling.md)

> [!div class="nextstepaction"]
> [Executar um exemplo que aguarda interação humana](durable-functions-phone-verification.md)