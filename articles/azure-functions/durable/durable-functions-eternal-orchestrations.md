---
title: Orquestrações eternas nas Funções Duráveis – Azure
description: Saiba como implementar orquestrações eternas usando a extensão de Funções Duráveis do Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: d55e08fecbd1338284607ac59fe354c6fa8cb1ea
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478809"
---
# <a name="eternal-orchestrations-in-durable-functions-azure-functions"></a>Orquestrações eternas nas Funções Duráveis (Azure Functions)

*Orquestrações eternas* são funções de orquestração que nunca chegam ao fim. Elas são úteis quando você deseja usar [Funções Duráveis](durable-functions-overview.md) para agregadores e para qualquer cenário que exige um loop infinito.

## <a name="orchestration-history"></a>Histórico de orquestração

Como explicado no tópico história da [orquestração,](durable-functions-orchestrations.md#orchestration-history) o Quadro de Tarefas Duráveis acompanha a história de cada orquestração de funções. Esse histórico cresce continuamente, desde que a função de orquestrador continue agendando novos trabalhos. Se a função de orquestrador entrar em um loop infinito e agendar trabalho continuamente, esse histórico poderá ficar muito grande e causar problemas de desempenho significativos. O conceito de *orquestração eterna* foi criado para mitigar esse tipo de problema para aplicativos que precisam de loops infinitos.

## <a name="resetting-and-restarting"></a>Redefinir e reiniciar

Em vez de usar loops infinitos, as funções do orquestrador redefinem seu estado chamando o `ContinueAsNew` método (.NET) ou `continueAsNew` (JavaScript) da vinculação do gatilho de [orquestração](durable-functions-bindings.md#orchestration-trigger). Esse método usa um único parâmetro serializável em JSON, que se torna a nova entrada para geração da próxima função de orquestrador.

Quando `ContinueAsNew` é chamado, a instância enfileira uma mensagem para si mesma antes de sair. A mensagem reinicia a instância com o novo valor de entrada. A mesma ID de instância é mantida, mas o histórico da função de orquestrador efetivamente é truncado.

> [!NOTE]
> O Framework de Tarefa Durável mantém a mesma ID de instância, mas internamente cria uma nova *ID de execução* para a função de orquestrador que é redefinida por `ContinueAsNew`. A ID dessa execução geralmente não é exposta externamente, mas pode ser útil conhecê-la ao depurar a execução da orquestração.

## <a name="periodic-work-example"></a>Exemplo de trabalho periódico

Um caso de uso das orquestrações eternas é o código que precisa realizar trabalho periódico indefinidamente.

# <a name="c"></a>[C #](#tab/csharp)

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
> O exemplo c# anterior é para Funções Duráveis 2.x. Para funções duráveis 1.x, você deve usar `DurableOrchestrationContext` em vez de `IDurableOrchestrationContext`. Para obter mais informações sobre as diferenças entre as versões, consulte o artigo [de funções duráveis.](durable-functions-versions.md)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

---

A diferença entre esse exemplo e uma função disparada por temporizador é que os tempos de gatilho de limpeza aqui não se baseiam em uma agenda. Por exemplo, uma agenda CRON que executa uma função a cada hora a executará às 1:00, 2:00, 3:00 etc. e, potencialmente, poderia se deparar com problemas de sobreposição. Neste exemplo, no entanto, se a limpeza levar 30 minutos, ela será agendada às 1:00, 2:30, 4:00 etc. e não haverá chance de sobreposição.

## <a name="starting-an-eternal-orchestration"></a>Iniciando uma orquestração eterna

Use `StartNewAsync` o método (.NET) ou `startNew` (JavaScript) para iniciar uma orquestração eterna, assim como qualquer outra função de orquestração.  

> [!NOTE]
> Se você precisa garantir que uma orquestração eterna singleton esteja em `id` execução, é importante manter a mesma instância ao iniciar a orquestração. Para obter mais informações, consulte [Gerenciamento de Instâncias](durable-functions-instance-management.md).

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("Trigger_Eternal_Orchestration")]
public static async Task<HttpResponseMessage> OrchestrationTrigger(
    [HttpTrigger(AuthorizationLevel.Function, "post", Route = null)] HttpRequestMessage request,
    [DurableClient] IDurableOrchestrationClient client)
{
    string instanceId = "StaticId";

    await client.StartNewAsync("Periodic_Cleanup_Loop", instanceId); 
    return client.CreateCheckStatusResponse(request, instanceId);
}
```

> [!NOTE]
> O código anterior é para Funções Duráveis 2.x. Para funções duráveis 1.x, você `OrchestrationClient` `DurableClient` deve usar atributo `DurableOrchestrationClient` em vez do `IDurableOrchestrationClient`atributo, e você deve usar o tipo de parâmetro em vez de . Para obter mais informações sobre as diferenças entre as versões, consulte o artigo [de funções duráveis.](durable-functions-versions.md)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context, req) {
    const client = df.getClient(context);
    const instanceId = "StaticId";
    
    // null is used as the input, since there is no input in "Periodic_Cleanup_Loop".
    await client.startNew("Periodic_Cleanup_Loop", instanceId, null);

    context.log(`Started orchestration with ID = '${instanceId}'.`);
    return client.createCheckStatusResponse(context.bindingData.req, instanceId);
};
```

---

## <a name="exit-from-an-eternal-orchestration"></a>Sair de uma orquestração eterna

Se uma função de orquestrador precisar ser concluída, tudo que você precisa fazer é *não* chamar `ContinueAsNew` e permitir que a função saia.

Se uma função orquestradora estiver em um loop `TerminateAsync` infinito e precisar `terminate` ser interrompida, use o método (.NET) ou (JavaScript) do cliente de [orquestração para](durable-functions-bindings.md#orchestration-client) pará-lo. Para obter mais informações, consulte [Gerenciamento de Instâncias](durable-functions-instance-management.md).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba como implementar orquestrações singleton](durable-functions-singletons.md)
