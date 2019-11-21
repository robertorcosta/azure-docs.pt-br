---
title: Lidando com erros nas Funções Duráveis – Azure
description: Saiba como lidar com erros na extensão de Funções Duráveis do Azure Functions.
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 0900e3f3b76f4a82e06fe3c0e6d9bbe63b545f56
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231414"
---
# <a name="handling-errors-in-durable-functions-azure-functions"></a>Lidando com erros nas Funções Duráveis (Azure Functions)

Durable Function orchestrations are implemented in code and can use the programming language's built-in error-handling features. There really aren't any new concepts you need to learn to add error handling and compensation into your orchestrations. No entanto, há alguns comportamentos a que você deve estar atento.

## <a name="errors-in-activity-functions"></a>Erros em funções de atividade

Any exception that is thrown in an activity function is marshaled back to the orchestrator function and thrown as a `FunctionFailedException`. Você pode escrever o código de compensação e tratamento de erro que atenda às suas necessidades na função de orquestrador.

Por exemplo, considere a seguinte função de orquestrador, que transfere fundos de uma conta para outra:

### <a name="precompiled-c"></a>C# pré-compilado

```csharp
[FunctionName("TransferFunds")]
public static async Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var transferDetails = ctx.GetInput<TransferOperation>();

    await context.CallActivityAsync("DebitAccount",
        new
        {
            Account = transferDetails.SourceAccount,
            Amount = transferDetails.Amount
        });

    try
    {
        await context.CallActivityAsync("CreditAccount",
            new
            {
                Account = transferDetails.DestinationAccount,
                Amount = transferDetails.Amount
            });
    }
    catch (Exception)
    {
        // Refund the source account.
        // Another try/catch could be used here based on the needs of the application.
        await context.CallActivityAsync("CreditAccount",
            new
            {
                Account = transferDetails.SourceAccount,
                Amount = transferDetails.Amount
            });
    }
}
```

### <a name="c-script"></a>Script do C#

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

public static async Task Run(IDurableOrchestrationContext context)
{
    var transferDetails = ctx.GetInput<TransferOperation>();

    await context.CallActivityAsync("DebitAccount",
        new
        {
            Account = transferDetails.SourceAccount,
            Amount = transferDetails.Amount
        });

    try
    {
        await context.CallActivityAsync("CreditAccount",
            new
            {
                Account = transferDetails.DestinationAccount,
                Amount = transferDetails.Amount
            });
    }
    catch (Exception)
    {
        // Refund the source account.
        // Another try/catch could be used here based on the needs of the application.
        await context.CallActivityAsync("CreditAccount",
            new
            {
                Account = transferDetails.SourceAccount,
                Amount = transferDetails.Amount
            });
    }
}
```

> [!NOTE]
> The previous C# examples are for Durable Functions 2.x. For Durable Functions 1.x, you must use `DurableOrchestrationContext` instead of `IDurableOrchestrationContext`. For more information about the differences between versions, see the [Durable Functions versions](durable-functions-versions.md) article.

### <a name="javascript-functions-20-only"></a>Javascript (somente funções 2.0)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const transferDetails = context.df.getInput();

    yield context.df.callActivity("DebitAccount",
        {
            account = transferDetails.sourceAccount,
            amount = transferDetails.amount,
        }
    );

    try {
        yield context.df.callActivity("CreditAccount",
            {
                account = transferDetails.destinationAccount,
                amount = transferDetails.amount,
            }
        );
    }
    catch (error) {
        // Refund the source account.
        // Another try/catch could be used here based on the needs of the application.
        yield context.df.callActivity("CreditAccount",
            {
                account = transferDetails.sourceAccount,
                amount = transferDetails.amount,
            }
        );
    }
});
```

If the first **CreditAccount** function call fails, the orchestrator function compensates by crediting the funds back to the source account.

## <a name="automatic-retry-on-failure"></a>Repetição automática em caso de falha

Quando chama funções de atividade ou funções de suborquestração, você pode especificar uma política de repetição automática. O exemplo a seguir tenta chamar uma função até três vezes e espera cinco segundos entre cada repetição:

### <a name="precompiled-c"></a>C# pré-compilado

```csharp
[FunctionName("TimerOrchestratorWithRetry")]
public static async Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var retryOptions = new RetryOptions(
        firstRetryInterval: TimeSpan.FromSeconds(5),
        maxNumberOfAttempts: 3);

    await ctx.CallActivityWithRetryAsync("FlakyFunction", retryOptions, null);

    // ...
}
```

### <a name="c-script"></a>Script do C#

```csharp
public static async Task Run(IDurableOrchestrationContext context)
{
    var retryOptions = new RetryOptions(
        firstRetryInterval: TimeSpan.FromSeconds(5),
        maxNumberOfAttempts: 3);

    await ctx.CallActivityWithRetryAsync("FlakyFunction", retryOptions, null);

    // ...
}
```

> [!NOTE]
> The previous C# examples are for Durable Functions 2.x. For Durable Functions 1.x, you must use `DurableOrchestrationContext` instead of `IDurableOrchestrationContext`. For more information about the differences between versions, see the [Durable Functions versions](durable-functions-versions.md) article.

### <a name="javascript-functions-20-only"></a>Javascript (somente funções 2.0)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const retryOptions = new df.RetryOptions(5000, 3);

    yield context.df.callActivityWithRetry("FlakyFunction", retryOptions);

    // ...
});
```

A API `CallActivityWithRetryAsync` (.NET) ou `callActivityWithRetry` (JavaScript) usa um parâmetro `RetryOptions`. Sub-orchestration calls using the `CallSubOrchestratorWithRetryAsync` (.NET) or `callSubOrchestratorWithRetry` (JavaScript) API can use these same retry policies.

There are several options for customizing the automatic retry policy:

* **Núm. máximo de tentativas**: o número máximo de tentativas de repetição.
* **Primeiro intervalo de repetição**: o tempo de espera antes de fazer a primeira tentativa.
* **Coeficiente de retirada**: o coeficiente usado para determinar a taxa de aumento de retirada. O valor padrão é 1.
* **Intervalo máx. de repetição**: o tempo máximo de espera entre tentativas de repetição.
* **Tempo limite de repetição**: o tempo máximo a ser dedicado às novas tentativas. O comportamento padrão é repetir indefinidamente.
* **Handle**: A user-defined callback can be specified to determine whether a function should be retried.

## <a name="function-timeouts"></a>Tempos limite de função

You might want to abandon a function call within an orchestrator function if it's taking too long to complete. No momento, o modo adequado de fazer isso é criar um [temporizador durável](durable-functions-timers.md) usando `context.CreateTimer` (.NET) ou `context.df.createTimer` (JavaScript) em conjunto com `Task.WhenAny` (.NET) ou `context.df.Task.any` (JavaScript), conforme mostrado no exemplo a seguir:

### <a name="precompiled-c"></a>C# pré-compilado

```csharp
[FunctionName("TimerOrchestrator")]
public static async Task<bool> Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    TimeSpan timeout = TimeSpan.FromSeconds(30);
    DateTime deadline = context.CurrentUtcDateTime.Add(timeout);

    using (var cts = new CancellationTokenSource())
    {
        Task activityTask = context.CallActivityAsync("FlakyFunction");
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

### <a name="c-script"></a>Script do C#

```csharp
public static async Task<bool> Run(IDurableOrchestrationContext context)
{
    TimeSpan timeout = TimeSpan.FromSeconds(30);
    DateTime deadline = context.CurrentUtcDateTime.Add(timeout);

    using (var cts = new CancellationTokenSource())
    {
        Task activityTask = context.CallActivityAsync("FlakyFunction");
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
> The previous C# examples are for Durable Functions 2.x. For Durable Functions 1.x, you must use `DurableOrchestrationContext` instead of `IDurableOrchestrationContext`. For more information about the differences between versions, see the [Durable Functions versions](durable-functions-versions.md) article.

### <a name="javascript-functions-20-only"></a>Javascript (somente funções 2.0)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const deadline = moment.utc(context.df.currentUtcDateTime).add(30, "s");

    const activityTask = context.df.callActivity("FlakyFunction");
    const timeoutTask = context.df.createTimer(deadline.toDate());

    const winner = yield context.df.Task.any([activityTask, timeoutTask]);
    if (winner === activityTask) {
        // success case
        timeoutTask.cancel();
        return true;
    } else {
        // timeout case
        return false;
    }
});
```

> [!NOTE]
> Esse mecanismo não encerra a execução de funções de atividade em andamento. Em vez disso, ele simplesmente permite que a função de orquestrador ignore o resultado e prossiga. Para mais informações, confira a documentação dos [Medidores de tempo](durable-functions-timers.md#usage-for-timeout).

## <a name="unhandled-exceptions"></a>Exceções sem tratamento

Se uma função de orquestrador falhar com uma exceção sem tratamento, os detalhes da exceção serão registrados e a instância será concluída com um status `Failed`.

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Learn about eternal orchestrations](durable-functions-eternal-orchestrations.md)

> [!div class="nextstepaction"]
> [Saiba como diagnosticar problemas](durable-functions-diagnostics.md)
