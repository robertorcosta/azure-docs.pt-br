---
title: Lidando com erros nas Funções Duráveis – Azure
description: Saiba como lidar com erros na extensão de Funções Duráveis do Azure Functions.
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 447b3dcf5040835f5a853beff68bde794ece51f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277850"
---
# <a name="handling-errors-in-durable-functions-azure-functions"></a>Lidando com erros nas Funções Duráveis (Azure Functions)

As orquestrações de função durável são implementadas em código e podem usar os recursos de manipulação de erros incorporados da linguagem de programação. Não há realmente nenhum novo conceito que você precisa aprender para adicionar manipulação de erros e compensação em suas orquestrações. No entanto, há alguns comportamentos a que você deve estar atento.

## <a name="errors-in-activity-functions"></a>Erros em funções de atividade

Qualquer exceção que seja lançada em uma função de atividade é `FunctionFailedException`recolocada de volta à função orquestradora e jogada como um . Você pode escrever o código de compensação e tratamento de erro que atenda às suas necessidades na função de orquestrador.

Por exemplo, considere a seguinte função de orquestrador, que transfere fundos de uma conta para outra:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("TransferFunds")]
public static async Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var transferDetails = context.GetInput<TransferOperation>();

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
> Os exemplos c# anteriores são para Funções Duráveis 2.x. Para funções duráveis 1.x, você deve usar `DurableOrchestrationContext` em vez de `IDurableOrchestrationContext`. Para obter mais informações sobre as diferenças entre as versões, consulte o artigo [de funções duráveis.](durable-functions-versions.md)

# <a name="javascript"></a>[Javascript](#tab/javascript)

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

---

Se a primeira chamada de função **CreditAccount** falhar, a função orquestradora compensa creditando os fundos de volta à conta de origem.

## <a name="automatic-retry-on-failure"></a>Repetição automática em caso de falha

Quando chama funções de atividade ou funções de suborquestração, você pode especificar uma política de repetição automática. O exemplo a seguir tenta chamar uma função até três vezes e espera cinco segundos entre cada repetição:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("TimerOrchestratorWithRetry")]
public static async Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var retryOptions = new RetryOptions(
        firstRetryInterval: TimeSpan.FromSeconds(5),
        maxNumberOfAttempts: 3);

    await context.CallActivityWithRetryAsync("FlakyFunction", retryOptions, null);

    // ...
}
```

> [!NOTE]
> Os exemplos c# anteriores são para Funções Duráveis 2.x. Para funções duráveis 1.x, você deve usar `DurableOrchestrationContext` em vez de `IDurableOrchestrationContext`. Para obter mais informações sobre as diferenças entre as versões, consulte o artigo [de funções duráveis.](durable-functions-versions.md)

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const firstRetryIntervalInMilliseconds = 5000;
    const maxNumberOfAttempts = 3;

    const retryOptions = 
        new df.RetryOptions(firstRetryIntervalInMilliseconds, maxNumberOfAttempts);

    yield context.df.callActivityWithRetry("FlakyFunction", retryOptions);

    // ...
});
```

---

A chamada de função de atividade no exemplo anterior toma um parâmetro para configurar uma diretiva de repetição automática. Existem várias opções para personalizar a política de retrição automática:

* **Núm. máximo de tentativas**: o número máximo de tentativas de repetição.
* **Primeiro intervalo de repetição**: o tempo de espera antes de fazer a primeira tentativa.
* **Coeficiente de retirada**: o coeficiente usado para determinar a taxa de aumento de retirada. O valor padrão é 1.
* **Intervalo máx. de repetição**: o tempo máximo de espera entre tentativas de repetição.
* **Tempo limite de repetição**: o tempo máximo a ser dedicado às novas tentativas. O comportamento padrão é repetir indefinidamente.
* **Handle**: Um retorno de chamada definido pelo usuário pode ser especificado para determinar se uma função deve ser repetida novamente.

## <a name="function-timeouts"></a>Tempos limite de função

Você pode querer abandonar uma chamada de função dentro de uma função orquestradora se estiver demorando muito para ser concluída. No momento, o modo adequado de fazer isso é criar um [temporizador durável](durable-functions-timers.md) usando `context.CreateTimer` (.NET) ou `context.df.createTimer` (JavaScript) em conjunto com `Task.WhenAny` (.NET) ou `context.df.Task.any` (JavaScript), conforme mostrado no exemplo a seguir:

# <a name="c"></a>[C #](#tab/csharp)

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

> [!NOTE]
> Os exemplos c# anteriores são para Funções Duráveis 2.x. Para funções duráveis 1.x, você deve usar `DurableOrchestrationContext` em vez de `IDurableOrchestrationContext`. Para obter mais informações sobre as diferenças entre as versões, consulte o artigo [de funções duráveis.](durable-functions-versions.md)

# <a name="javascript"></a>[Javascript](#tab/javascript)

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

---

> [!NOTE]
> Esse mecanismo não encerra a execução de funções de atividade em andamento. Em vez disso, ele simplesmente permite que a função de orquestrador ignore o resultado e prossiga. Para mais informações, confira a documentação dos [Medidores de tempo](durable-functions-timers.md#usage-for-timeout).

## <a name="unhandled-exceptions"></a>Exceções sem tratamento

Se uma função de orquestrador falhar com uma exceção sem tratamento, os detalhes da exceção serão registrados e a instância será concluída com um status `Failed`.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Conheça orquestrações eternas](durable-functions-eternal-orchestrations.md)

> [!div class="nextstepaction"]
> [Saiba como diagnosticar problemas](durable-functions-diagnostics.md)
