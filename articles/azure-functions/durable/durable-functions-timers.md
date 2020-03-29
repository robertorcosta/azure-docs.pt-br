---
title: Temporizadores nas Funções Duráveis – Azure
description: Saiba como implementar temporizadores duráveis na extensão de Funções Duráveis do Azure Functions.
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 0565cc149a36baf31d8516fffcf48b194c465760
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76261476"
---
# <a name="timers-in-durable-functions-azure-functions"></a>Temporizadores nas Funções Duráveis (Azure Functions)

As [Funções Duráveis](durable-functions-overview.md) fornecem *temporizadores duráveis* para uso em funções de orquestrador para implementar atrasos ou para configurar tempos limite em ações assíncronas. Temporizadores duráveis devem ser usados em funções de orquestrador em vez de `Thread.Sleep` ou `Task.Delay` (C#) ou `setTimeout()` e `setInterval()` (JavaScript).

Você cria um temporizador durável chamando o `CreateTimer` método (.NET) ou o `createTimer` método (JavaScript) da vinculação do gatilho de [orquestração](durable-functions-bindings.md#orchestration-trigger). O método retorna uma tarefa que é concluída em uma data e hora especificadas.

## <a name="timer-limitations"></a>Limitações do temporizador

Quando você cria um temporizador que expira às 16h30, o Quadro de Tarefas Duráveis subjacente enfileira uma mensagem que se torna visível apenas às 16h30. Ao ser executado no plano de consumo de funções do Azure, a mensagem temporizador recém-visível garantirá que o aplicativo de função seja ativado em uma VM apropriada.

> [!NOTE]
> * Os temporizadores duráveis estão atualmente limitados a 7 dias. Se forem necessários atrasos mais longos, eles podem `while` ser simulados usando as APIs do temporizador em um loop.
> * Use `CurrentUtcDateTime` sempre `DateTime.UtcNow` em vez `currentUtcDateTime` de `Date.now` em `Date.UTC` .NET ou em vez de ou em JavaScript ao calcular o tempo de incêndio para temporizadores duráveis. Para obter mais informações, consulte o artigo de restrições de [código de função do orquestrador.](durable-functions-code-constraints.md)

## <a name="usage-for-delay"></a>Uso para atrasos

O exemplo a seguir ilustra como usar temporizadores duráveis para atrasar a execução. O exemplo é emitir uma notificação de cobrança todos os dias por 10 dias.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("BillingIssuer")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    for (int i = 0; i < 10; i++)
    {
        DateTime deadline = context.CurrentUtcDateTime.Add(TimeSpan.FromDays(1));
        await context.CreateTimer(deadline, CancellationToken.None);
        await context.CallActivityAsync("SendBillingEvent");
    }
}
```

> [!NOTE]
> O exemplo c# anterior tem como alvo funções duráveis 2.x. Para funções duráveis 1.x, você deve usar `DurableOrchestrationContext` em vez de `IDurableOrchestrationContext`. Para obter mais informações sobre as diferenças entre as versões, consulte o artigo [de funções duráveis.](durable-functions-versions.md)

# <a name="javascript"></a>[Javascript](#tab/javascript)

```js
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    for (let i = 0; i < 10; i++) {
        const deadline = moment.utc(context.df.currentUtcDateTime).add(1, 'd');
        yield context.df.createTimer(deadline.toDate());
        yield context.df.callActivity("SendBillingEvent");
    }
});
```

---

> [!WARNING]
> Evite loops infinitos em funções de orquestrador. Para obter informações sobre como implementar de forma segura e eficiente cenários de loop infinito, consulte [Orquestrações eternas](durable-functions-eternal-orchestrations.md).

## <a name="usage-for-timeout"></a>Uso para tempo limite

Este exemplo ilustra como usar temporizadores duráveis para implementar tempos limite.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("TryGetQuote")]
public static async Task<bool> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    TimeSpan timeout = TimeSpan.FromSeconds(30);
    DateTime deadline = context.CurrentUtcDateTime.Add(timeout);

    using (var cts = new CancellationTokenSource())
    {
        Task activityTask = context.CallActivityAsync("GetQuote");
        Task timeoutTask = context.CreateTimer(deadline, cts.Token);

        Task winner = await Task.WhenAny(activityTask, timeoutTask);
        if (winner == activityTask)
        {
            // success case
            cts.Cancel();
            return true;
        }
        else
        {
            // timeout case
            return false;
        }
    }
}
```

> [!NOTE]
> O exemplo c# anterior tem como alvo funções duráveis 2.x. Para funções duráveis 1.x, você deve usar `DurableOrchestrationContext` em vez de `IDurableOrchestrationContext`. Para obter mais informações sobre as diferenças entre as versões, consulte o artigo [de funções duráveis.](durable-functions-versions.md)

# <a name="javascript"></a>[Javascript](#tab/javascript)

```js
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const deadline = moment.utc(context.df.currentUtcDateTime).add(30, "s");

    const activityTask = context.df.callActivity("GetQuote");
    const timeoutTask = context.df.createTimer(deadline.toDate());

    const winner = yield context.df.Task.any([activityTask, timeoutTask]);
    if (winner === activityTask) {
        // success case
        timeoutTask.cancel();
        return true;
    }
    else
    {
        // timeout case
        return false;
    }
});
```

---

> [!WARNING]
> Use `CancellationTokenSource` um (.NET) `cancel()` ou ligue `TimerTask` para o retornado (JavaScript) para cancelar um temporizador durável se o seu código não esperar que ele seja concluído. O Quadro de Tarefas Duráveis não mudará o status de uma orquestração para "concluído" até que todas as tarefas pendentes sejam concluídas ou canceladas.

Este mecanismo de cancelamento não encerra a função de atividade em andamento ou execuções de suborquestração. Em vez disso, ele simplesmente permite que a função de orquestrador ignore o resultado e prossiga. Se o seu aplicativo de função usar o plano Consumo, você ainda será cobrado por qualquer tempo e memória consumido pela função de atividade abandonada. Por padrão, funções em execução pelo Plano de Consumo têm um tempo limite de cinco minutos. Se esse limite for ultrapassado, o host do Azure Functions será reciclado para interromper toda a execução e evitar uma situação de cobrança sem controle. O [tempo limite da função é configurável](../functions-host-json.md#functiontimeout).

Para obter um exemplo mais aprofundado de como implementar tempos outs em funções orquestrais, consulte o artigo [Interação Humana & Timeouts - Verificação telefônica.](durable-functions-phone-verification.md)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba como acionar e manipular eventos externos](durable-functions-external-events.md)
