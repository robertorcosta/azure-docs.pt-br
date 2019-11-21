---
title: Controle de Versão nas Funções Duráveis – Azure
description: Saiba como implementar o Controle de Versão na extensão de Funções Duráveis do Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 87cbb94dbab241630dc7585bdf4314d858d5b4da
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232749"
---
# <a name="versioning-in-durable-functions-azure-functions"></a>Controle de Versão nas Funções Duráveis (Azure Functions)

É inevitável que funções sejam adicionadas, removidas e alteradas ao longo do tempo de vida de um aplicativo. As [Funções Duráveis](durable-functions-overview.md) permitem encadear funções de maneiras que não eram possíveis anteriormente e esse encadeamento afeta a forma como você pode tratar o controle de versão.

## <a name="how-to-handle-breaking-changes"></a>Como lidar com as alterações que causam interrupção

Há vários exemplos de alterações que causam interrupção a serem considerados. Este artigo discute os mais comuns. O tema principal por trás delas é que tanto orquestrações de função novas quando as já existentes são afetadas por alterações no código da função.

### <a name="changing-activity-or-entity-function-signatures"></a>Changing activity or entity function signatures

Uma alteração de assinatura é uma alteração no nome, na entrada ou na saída de uma função. If this kind of change is made to an activity or entity function, it could break any orchestrator function that depends on it. Se atualizar a função de orquestrador para acomodar essa alteração, você poderá interromper instâncias existentes em curso.

As an example, suppose we have the following orchestrator function.

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Essa função simples obtém os resultados de **Foo** e os transmite para **Bar**. Digamos que precisemos alterar o valor retornado de **Foo** de `bool` para `int` para dar suporte a uma variedade maior de valores de resultado. O resultado é semelhante a:

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    int result = await context.CallActivityAsync<int>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

> [!NOTE]
> The previous C# examples target Durable Functions 2.x. For Durable Functions 1.x, you must use `DurableOrchestrationContext` instead of `IDurableOrchestrationContext`. For more information about the differences between versions, see the [Durable Functions versions](durable-functions-versions.md) article.

Essa alteração funciona bem para todas as novas instâncias da função de orquestrador, mas interrompe todas as instâncias em curso. For example, consider the case where an orchestration instance calls a function named `Foo`, gets back a boolean value, and then checkpoints. Se a alteração da assinatura for implantada nesse ponto, a instância que passou pela verificação falhará imediatamente quando for retomada e reproduzir a chamada para `context.CallActivityAsync<int>("Foo")`. This failure happens because the result in the history table is `bool` but the new code tries to deserialize it into `int`.

This example is just one of many different ways that a signature change can break existing instances. De modo geral, se um orquestrador precisar alterar a forma como chama uma função, provavelmente a alteração será um problema.

### <a name="changing-orchestrator-logic"></a>Alterando a lógica do orquestrador

O outro tipo de problema de controle de versão é consequência de alterar o código da função de orquestrador de forma a confundir a lógica de reprodução para instâncias em curso.

Considere a seguinte função de orquestrador:

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Agora, vamos supor que você queira fazer uma alteração aparentemente inocente para adicionar outra chamada de função.

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    if (result)
    {
        await context.CallActivityAsync("SendNotification");
    }

    await context.CallActivityAsync("Bar", result);
}
```

> [!NOTE]
> The previous C# examples target Durable Functions 2.x. For Durable Functions 1.x, you must use `DurableOrchestrationContext` instead of `IDurableOrchestrationContext`. For more information about the differences between versions, see the [Durable Functions versions](durable-functions-versions.md) article.

Essa alteração adiciona uma nova chamada de função a **SendNotification**, entre **Foo** e **Bar**. Não há nenhuma alteração de assinatura. O problema surge quando uma instância existente é retomada da chamada para **Bar**. During replay, if the original call to **Foo** returned `true`, then the orchestrator replay will call into **SendNotification**, which is not in its execution history. Como resultado, o Framework de Tarefa Durável falhará com um `NonDeterministicOrchestrationException`, porque encontrou uma chamada para **SendNotification** quando esperava encontrar uma chamada para **Bar**. The same type of problem can occur when adding any calls to "durable" APIs, including `CreateTimer`, `WaitForExternalEvent`, etc.

## <a name="mitigation-strategies"></a>Estratégias de mitigação

Veja algumas das estratégias para lidar com desafios de controle de versão:

* Não fazer nada
* Parar todas as instâncias em curso
* Implantações lado a lado

### <a name="do-nothing"></a>Não fazer nada

A maneira mais fácil de lidar com uma alteração que causa interrupção é deixar que as instâncias de orquestração em curso falhem. Novas instâncias executarão com êxito o código alterado.

Whether this kind of failure is a problem depends on the importance of your in-flight instances. Se você estiver em um desenvolvimento ativo e não se preocupar com instâncias em curso, isso pode ser suficiente. However, you'll need to deal with exceptions and errors in your diagnostics pipeline. Se quiser evitar essas coisas, considere as outras opções de controle de versão.

### <a name="stop-all-in-flight-instances"></a>Parar todas as instâncias em curso

Outra opção é parar todas as instâncias em curso. Stopping all instances can be done by clearing the contents of the internal **control-queue** and **workitem-queue** queues. The instances will be forever stuck where they are, but they will not clutter your logs with failure messages. This approach is ideal in rapid prototype development.

> [!WARNING]
> Os detalhes dessas filas podem mudar ao longo do tempo, portanto, não use essa técnica para cargas de trabalho de produção.

### <a name="side-by-side-deployments"></a>Implantações lado a lado

A maneira mais infalível de garantir que alterações que causam interrupção sejam implantadas com segurança é implantá-las lado a lado com as versões mais antigas. Isso pode ser feito usando qualquer uma das seguintes técnicas:

* Deploy all the updates as entirely new functions, leaving existing functions as-is. This can be tricky because the callers of the new function versions must be updated as well following the same guidelines.
* Implantar todas as atualizações como um novo aplicativo de funções com uma conta de armazenamento diferente.
* Deploy a new copy of the function app with the same storage account but with an updated `taskHub` name. Side-by-side deployments is the recommended technique.

### <a name="how-to-change-task-hub-name"></a>Como alterar o nome do hub de tarefas

O hub de tarefas pode ser configurado no arquivo *host.json* da seguinte forma:

#### <a name="functions-1x"></a>Funções 1.x

```json
{
    "durableTask": {
        "hubName": "MyTaskHubV2"
    }
}
```

#### <a name="functions-20"></a>Functions 2.0

```json
{
    "extensions": {
        "durableTask": {
            "hubName": "MyTaskHubV2"
        }
    }
}
```

The default value for Durable Functions v1.x is `DurableFunctionsHub`. Starting in Durable Functions v2.0, the default task hub name is the same as the function app name in Azure, or `TestHubName` if running outside of Azure.

Todas as entidades do Armazenamento do Azure são nomeadas com base no valor de configuração `hubName`. Fornecendo um novo nome ao hub de tarefas, você garante que filas e uma tabela de histórico diferentes sejam criadas para a nova versão de seu aplicativo. The function app, however, will stop processing events for orchestrations or entities created under the previous task hub name.

É recomendável implantar a nova versão do aplicativo de funções em um novo [Slot de implantação](../functions-deployment-slots.md). Os slots de implantação permitem que você execute várias cópias de seu aplicativo de funções lado a lado, com apenas uma delas como o slot de *produção* ativo. Quando você estiver pronto para expor a nova lógica de orquestração para sua infraestrutura existente, isso pode ser tão simples quanto colocar a nova versão no slot de produção.

> [!NOTE]
> Essa estratégia funciona melhor quando você usa gatilhos de webhook e HTTP para funções de orquestrador. For non-HTTP triggers, such as queues or Event Hubs, the trigger definition should [derive from an app setting](../functions-bindings-expressions-patterns.md#binding-expressions---app-settings) that gets updated as part of the swap operation.

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Saiba como lidar com problemas de desempenho e escala](durable-functions-perf-and-scale.md)
