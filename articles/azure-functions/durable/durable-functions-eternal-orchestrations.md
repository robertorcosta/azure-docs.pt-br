---
title: Orquestrações eternas nas Funções Duráveis – Azure
description: Saiba como implementar orquestrações eternas usando a extensão de Funções Duráveis do Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: d96229bb5e3d288915b64e5a7ce29a8651f2a181
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177373"
---
# <a name="eternal-orchestrations-in-durable-functions-azure-functions"></a>Orquestrações eternas nas Funções Duráveis (Azure Functions)

*Orquestrações eternas* são funções de orquestração que nunca chegam ao fim. Elas são úteis quando você deseja usar [Funções Duráveis](durable-functions-overview.md) para agregadores e para qualquer cenário que exige um loop infinito.

## <a name="orchestration-history"></a>Histórico de orquestração

Conforme explicado no tópico do [histórico de orquestração](durable-functions-orchestrations.md#orchestration-history) , o Framework de tarefa durável controla o histórico de cada orquestração de função. Esse histórico cresce continuamente, desde que a função de orquestrador continue agendando novos trabalhos. Se a função de orquestrador entrar em um loop infinito e agendar trabalho continuamente, esse histórico poderá ficar muito grande e causar problemas de desempenho significativos. O conceito de *orquestração eterna* foi criado para mitigar esse tipo de problema para aplicativos que precisam de loops infinitos.

## <a name="resetting-and-restarting"></a>Redefinir e reiniciar

Em vez de usar loops infinitos, as funções de orquestrador redefinem seu estado chamando o método [ContinueAsNew](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_ContinueAsNew_). Esse método usa um único parâmetro serializável em JSON, que se torna a nova entrada para geração da próxima função de orquestrador.

Quando `ContinueAsNew` é chamado, a instância enfileira uma mensagem para si mesma antes de sair. A mensagem reinicia a instância com o novo valor de entrada. A mesma ID de instância é mantida, mas o histórico da função de orquestrador efetivamente é truncado.

> [!NOTE]
> O Framework de Tarefa Durável mantém a mesma ID de instância, mas internamente cria uma nova *ID de execução* para a função de orquestrador que é redefinida por `ContinueAsNew`. A ID dessa execução geralmente não é exposta externamente, mas pode ser útil conhecê-la ao depurar a execução da orquestração.

## <a name="periodic-work-example"></a>Exemplo de trabalho periódico

Um caso de uso das orquestrações eternas é o código que precisa realizar trabalho periódico indefinidamente.

### <a name="c"></a>C#

```csharp
[FunctionName("Periodic_Cleanup_Loop")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    await context.CallActivityAsync("DoCleanup", null);

    // sleep for one hour between cleanups
    DateTime nextCleanup = context.CurrentUtcDateTime.AddHours(1);
    await context.CreateTimer(nextCleanup, CancellationToken.None);

    context.ContinueAsNew(null);
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (apenas Funções 2.x)

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

## <a name="starting-an-eternal-orchestration"></a>Iniciando uma orquestração de eternas
Use o método [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) para iniciar uma orquestração de eternas. Isso não é diferente de disparar qualquer outra função de orquestração.  

> [!NOTE]
> Se você precisar garantir que uma orquestração singleton eternas esteja em execução, é importante manter a mesma instância `id` ao iniciar a orquestração. Para obter mais informações, consulte [Gerenciamento de Instâncias](durable-functions-instance-management.md).

```csharp
[FunctionName("Trigger_Eternal_Orchestration")]
public static async Task<HttpResponseMessage> OrchestrationTrigger(
    [HttpTrigger(AuthorizationLevel.Function, "post", Route = null)] HttpRequestMessage request,
    [OrchestrationClient] DurableOrchestrationClientBase client)
{
    string instanceId = "StaticId";
    // Null is used as the input, since there is no input in "Periodic_Cleanup_Loop".
    await client.StartNewAsync("Periodic_Cleanup_Loop", instanceId, null); 
    return client.CreateCheckStatusResponse(request, instanceId);
}
```

## <a name="exit-from-an-eternal-orchestration"></a>Sair de uma orquestração eterna

Se uma função de orquestrador precisar ser concluída, tudo que você precisa fazer é *não* chamar `ContinueAsNew` e permitir que a função saia.

Se uma função de orquestrador for um loop infinito e precisar ser interrompido, use o método [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_) para pará-la. Para obter mais informações, consulte [Gerenciamento de Instâncias](durable-functions-instance-management.md).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba como implementar orquestrações singleton](durable-functions-singletons.md)
