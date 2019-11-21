---
title: Orquestrações eternas nas Funções Duráveis – Azure
description: Saiba como implementar orquestrações eternas usando a extensão de Funções Duráveis do Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 8d28ae18c44c434dba053b23a60eb78728f8d8e0
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232901"
---
# <a name="eternal-orchestrations-in-durable-functions-azure-functions"></a>Orquestrações eternas nas Funções Duráveis (Azure Functions)

*Orquestrações eternas* são funções de orquestração que nunca chegam ao fim. Elas são úteis quando você deseja usar [Funções Duráveis](durable-functions-overview.md) para agregadores e para qualquer cenário que exige um loop infinito.

## <a name="orchestration-history"></a>Histórico de orquestração

As explained in the [orchestration history](durable-functions-orchestrations.md#orchestration-history) topic, the Durable Task Framework keeps track of the history of each function orchestration. Esse histórico cresce continuamente, desde que a função de orquestrador continue agendando novos trabalhos. Se a função de orquestrador entrar em um loop infinito e agendar trabalho continuamente, esse histórico poderá ficar muito grande e causar problemas de desempenho significativos. O conceito de *orquestração eterna* foi criado para mitigar esse tipo de problema para aplicativos que precisam de loops infinitos.

## <a name="resetting-and-restarting"></a>Redefinir e reiniciar

Instead of using infinite loops, orchestrator functions reset their state by calling the `ContinueAsNew` (.NET) or `continueAsNew` (JavaScript) method of the [orchestration trigger binding](durable-functions-bindings.md#orchestration-trigger). Esse método usa um único parâmetro serializável em JSON, que se torna a nova entrada para geração da próxima função de orquestrador.

Quando `ContinueAsNew` é chamado, a instância enfileira uma mensagem para si mesma antes de sair. A mensagem reinicia a instância com o novo valor de entrada. A mesma ID de instância é mantida, mas o histórico da função de orquestrador efetivamente é truncado.

> [!NOTE]
> O Framework de Tarefa Durável mantém a mesma ID de instância, mas internamente cria uma nova *ID de execução* para a função de orquestrador que é redefinida por `ContinueAsNew`. A ID dessa execução geralmente não é exposta externamente, mas pode ser útil conhecê-la ao depurar a execução da orquestração.

## <a name="periodic-work-example"></a>Exemplo de trabalho periódico

Um caso de uso das orquestrações eternas é o código que precisa realizar trabalho periódico indefinidamente.

### <a name="c"></a>C#

```csharp
[FunctionName("Periodic_Cleanup_Loop")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    await context.CallActivityAsync("DoCleanup", null);

    // sleep for one hour between cleanups
    DateTime nextCleanup = context.CurrentUtcDateTime.AddHours(1);
    await context.CreateTimer(nextCleanup, CancellationToken.None);

    context.ContinueAsNew(null);
}
```

> [!NOTE]
> The previous C# example is for Durable Functions 2.x. For Durable Functions 1.x, you must use `DurableOrchestrationContext` instead of `IDurableOrchestrationContext`. For more information about the differences between versions, see the [Durable Functions versions](durable-functions-versions.md) article.

### <a name="javascript-functions-20-only"></a>Javascript (somente funções 2.0)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    yield context.df.callActivity("DoCleanup");

    // sleep for one hour between cleanups
    const nextCleanup = moment.utc(context.df.currentUtcDateTime).add(1, "h");
    yield context.df.createTimer(nextCleanup.toDate());

    context.df.continueAsNew(undefined);
});
```

A diferença entre esse exemplo e uma função disparada por temporizador é que os tempos de gatilho de limpeza aqui não se baseiam em uma agenda. Por exemplo, uma agenda CRON que executa uma função a cada hora a executará às 1:00, 2:00, 3:00 etc. e, potencialmente, poderia se deparar com problemas de sobreposição. Neste exemplo, no entanto, se a limpeza levar 30 minutos, ela será agendada às 1:00, 2:30, 4:00 etc. e não haverá chance de sobreposição.

## <a name="starting-an-eternal-orchestration"></a>Starting an eternal orchestration

Use the `StartNewAsync` (.NET) or the `startNew` (JavaScript) method to start an eternal orchestration, just like you would any other orchestration function.  

> [!NOTE]
> If you need to ensure a singleton eternal orchestration is running, it's important to maintain the same instance `id` when starting the orchestration. Para obter mais informações, consulte [Gerenciamento de Instâncias](durable-functions-instance-management.md).

```csharp
[FunctionName("Trigger_Eternal_Orchestration")]
public static async Task<HttpResponseMessage> OrchestrationTrigger(
    [HttpTrigger(AuthorizationLevel.Function, "post", Route = null)] HttpRequestMessage request,
    [DurableClient] IDurableOrchestrationClient client)
{
    string instanceId = "StaticId";
    // Null is used as the input, since there is no input in "Periodic_Cleanup_Loop".
    await client.StartNewAsync("Periodic_Cleanup_Loop", instanceId, null); 
    return client.CreateCheckStatusResponse(request, instanceId);
}
```

> [!NOTE]
> The previous code is for Durable Functions 2.x. For Durable Functions 1.x, you must use `OrchestrationClient` attribute instead of the `DurableClient` attribute, and you must use the `DurableOrchestrationClient` parameter type instead of `IDurableOrchestrationClient`. For more information about the differences between versions, see the [Durable Functions versions](durable-functions-versions.md) article.

## <a name="exit-from-an-eternal-orchestration"></a>Sair de uma orquestração eterna

Se uma função de orquestrador precisar ser concluída, tudo que você precisa fazer é *não* chamar `ContinueAsNew` e permitir que a função saia.

If an orchestrator function is in an infinite loop and needs to be stopped, use the `TerminateAsync` (.NET) or `terminate` (JavaScript) method of the [orchestration client binding](durable-functions-bindings.md#orchestration-client) to stop it. Para obter mais informações, consulte [Gerenciamento de Instâncias](durable-functions-instance-management.md).

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Saiba como implementar orquestrações singleton](durable-functions-singletons.md)
