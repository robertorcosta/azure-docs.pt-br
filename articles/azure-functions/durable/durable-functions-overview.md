---
title: Visão Geral das Funções Duráveis – Azure
description: Introdução à extensão de Durable Functions do Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: overview
ms.date: 08/07/2019
ms.author: cgillum
ms.reviewer: azfuncdf
ms.openlocfilehash: a917a823d47d6a072cf5a3ee5d636b432913df9a
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71299435"
---
# <a name="what-are-durable-functions"></a>O que são as Durable Functions?

As *Durable Functions* são uma extensão do [Azure Functions](../functions-overview.md) que permite escrever funções com estado em um ambiente de computação sem servidor. A extensão permite definir fluxos de trabalho com estado pela escrita de [*funções de orquestrador*](durable-functions-orchestrations.md) e entidades com estado pela escrita de [*funções de entidade*](durable-functions-entities.md) usando o modelo de programação do Azure Functions. Nos bastidores, a extensão gerencia o estado, os pontos de verificação e as reinicializações para você, permitindo que você se concentre na lógica de negócios.

## <a name="language-support"></a>Linguagens compatíveis

Atualmente, as Durable Functions dão suporte às seguintes linguagens:

* **C#** : [bibliotecas de classes pré-compiladas](../functions-dotnet-class-library.md) e [script C#](../functions-reference-csharp.md).
* **F#** : bibliotecas de classes pré-compiladas e script F#. Só há suporte para o script F# na versão 1.x do Azure Functions Runtime.
* **JavaScript**: compatível apenas com a versão 2.x do Azure Functions Runtime. Exige a versão 1.7.0 da extensão das Durable Functions ou uma versão posterior. 

As Durable Functions têm o objetivo de dar suporte a todas as [linguagens do Azure Functions](../supported-languages.md). Confira a [lista de problemas das Durable Functions](https://github.com/Azure/azure-functions-durable-extension/issues) para obter o último status do trabalho para dar suporte a linguagens adicionais.

Assim como o Azure Functions, há modelos para ajudá-lo a desenvolver as Durable Functions usando o [Visual Studio 2019](durable-functions-create-first-csharp.md), o [Visual Studio Code](quickstart-js-vscode.md) e o [portal do Azure](durable-functions-create-portal.md).

## <a name="application-patterns"></a>Padrões de aplicativo

O principal caso de uso das Durable Functions é simplificar requisitos complexos de coordenação com estado em aplicativos sem servidor. As seguintes seções descrevem padrões de aplicativo típicos que podem se beneficiar com as Durable Functions:

* [Encadeamento de funções](#chaining)
* [Fan-out/fan-in](#fan-in-out)
* [APIs HTTP assíncronas](#async-http)
* [Monitoramento](#monitoring)
* [Interação humana](#human)
* [Agregador](#aggregator)

### <a name="chaining"></a>Padrão 1: Encadeamento de funções

No padrão de encadeamento de funções, uma sequência de funções é executada em uma ordem específica. Nesse padrão, a saída de uma função é aplicada à entrada de outra função.

![Um diagrama do padrão de encadeamento de funções](./media/durable-functions-concepts/function-chaining.png)

Use as Durable Functions para implementar o padrão de encadeamento de funções de forma concisa, conforme mostrado no seguinte exemplo:

#### <a name="c"></a>C#

```csharp
[FunctionName("Chaining")]
public static async Task<object> Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    try
    {
        var x = await context.CallActivityAsync<object>("F1", null);
        var y = await context.CallActivityAsync<object>("F2", x);
        var z = await context.CallActivityAsync<object>("F3", y);
        return  await context.CallActivityAsync<object>("F4", z);
    }
    catch (Exception)
    {
        // Error handling or compensation goes here.
    }
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (apenas Funções 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const x = yield context.df.callActivity("F1");
    const y = yield context.df.callActivity("F2", x);
    const z = yield context.df.callActivity("F3", y);
    return    yield context.df.callActivity("F4", z);
});
```

Nesse exemplo, os valores `F1`, `F2`, `F3` e `F4` são os nomes de outras funções no aplicativo de funções. Implemente o fluxo de controle usando construtos de codificação imperativa normal. O código é executado de cima para baixo. Ele pode envolver a semântica do fluxo de controle da linguagem existente, como condicionais e loops. Você pode incluir a lógica de tratamento de erro em blocos `try`/`catch`/`finally`.

Use o parâmetro `context` [DurableOrchestrationContext] \(.NET\) e o objeto `context.df` (JavaScript) para invocar outras funções por nome, passar parâmetros e retornar a saída da função. Sempre que o código chama `await` (C#) ou `yield` (JavaScript), a estrutura das Durable Functions faz a verificação pontual do progresso da instância da função atual. Se o processo ou a VM for reciclada no meio da execução, a instância da função retomará na chamada `await` ou `yield` anterior. Para obter mais informações, confira a próxima seção, Padrão nº2: Fan-out/fan-in.

> [!NOTE]
> O objeto `context` no JavaScript representa o [contexto de função](../functions-reference-node.md#context-object) inteiro, não apenas o parâmetro [DurableOrchestrationContext].

### <a name="fan-in-out"></a>Padrão 2: Fan-out/fan-in

No padrão fan-out/fan-in, execute várias funções em paralelo e, em seguida, aguarde a conclusão de todas as funções. Frequentemente, algum trabalho de agregação é feito nos resultados retornados pelas funções.

![Um diagrama do padrão fan-out/fan-in](./media/durable-functions-concepts/fan-out-fan-in.png)

Com funções normais, realize fan-out fazendo com que a função envie várias mensagens para uma fila. No entanto, o processo de realizar fan-in é muito mais complexo. Para o fan-in, em uma função normal, você escreve o código a ser controlado quando as funções disparadas por fila terminam e, em seguida, armazena saídas da função.

A extensão Durable Functions cuida desse padrão com um código relativamente simples:

#### <a name="c"></a>C#

```csharp
[FunctionName("FanOutFanIn")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    var parallelTasks = new List<Task<int>>();

    // Get a list of N work items to process in parallel.
    object[] workBatch = await context.CallActivityAsync<object[]>("F1", null);
    for (int i = 0; i < workBatch.Length; i++)
    {
        Task<int> task = context.CallActivityAsync<int>("F2", workBatch[i]);
        parallelTasks.Add(task);
    }

    await Task.WhenAll(parallelTasks);

    // Aggregate all N outputs and send the result to F3.
    int sum = parallelTasks.Sum(t => t.Result);
    await context.CallActivityAsync("F3", sum);
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (apenas Funções 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const parallelTasks = [];

    // Get a list of N work items to process in parallel.
    const workBatch = yield context.df.callActivity("F1");
    for (let i = 0; i < workBatch.length; i++) {
        parallelTasks.push(context.df.callActivity("F2", workBatch[i]));
    }

    yield context.df.Task.all(parallelTasks);

    // Aggregate all N outputs and send the result to F3.
    const sum = parallelTasks.reduce((prev, curr) => prev + curr, 0);
    yield context.df.callActivity("F3", sum);
});
```

O trabalho de fan-out é distribuído para várias instâncias da função `F2`. O trabalho é acompanhado usando uma lista dinâmica de tarefas. A API `Task.WhenAll` .NET ou `context.df.Task.all` JavaScript é chamada para aguardar até que todas as funções chamadas sejam concluídas. Em seguida, as saídas da função `F2` são agregadas da lista de tarefas dinâmicas e passadas para a função `F3`.

O ponto de verificação automático que ocorre na chamada `await` ou `yield` em `Task.WhenAll` ou `context.df.Task.all` garante que uma possível falha ou reinicialização no meio do processo não exija a reinicialização de uma tarefa já concluída.

> [!NOTE]
> Em raras circunstâncias, é possível que uma falha ocorra na janela depois que uma função de atividade for concluída, mas antes de sua conclusão ser salva no histórico de orquestração. Se isso acontecer, a função de atividade será executada novamente desde o início depois que o processo for recuperado.

### <a name="async-http"></a>Padrão 3: APIs HTTP assíncronas

O padrão de API HTTP assíncrona trata do problema de coordenar o estado de operações de execução longa com clientes externos. Uma maneira comum de implementar esse padrão é fazer com que um ponto de extremidade HTTP dispare a ação de execução longa. Em seguida, redirecione o cliente para um ponto de extremidade de status que é sondado pelo cliente para saber quando a operação é concluída.

![Um diagrama do padrão de API HTTP](./media/durable-functions-concepts/async-http-api.png)

As Durable Functions fornecem **suporte interno** para esse padrão, simplificando ou, até mesmo, removendo o código que você precisa escrever para interagir com execuções de função de execução longa. Por exemplo, as amostras de início rápido das Durable Functions ([C#](durable-functions-create-first-csharp.md) e [JavaScript](quickstart-js-vscode.md)) mostram um comando REST simples que você pode usar para iniciar novas instâncias de função de orquestrador. Depois que uma instância é iniciada, a extensão expõe as APIs HTTP de webhook que consultam o status da função de orquestrador. 

O exemplo a seguir mostra os comandos REST que iniciam um orquestrador e consultam seu status. Para maior clareza, alguns detalhes do protocolo foram omitidos do exemplo.

```
> curl -X POST https://myfunc.azurewebsites.net/orchestrators/DoWork -H "Content-Length: 0" -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/b79baf67f717453ca9e86c5da21e03ec

{"id":"b79baf67f717453ca9e86c5da21e03ec", ...}

> curl https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/b79baf67f717453ca9e86c5da21e03ec

{"runtimeStatus":"Running","lastUpdatedTime":"2019-03-16T21:20:47Z", ...}

> curl https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 200 OK
Content-Length: 175
Content-Type: application/json

{"runtimeStatus":"Completed","lastUpdatedTime":"2019-03-16T21:20:57Z", ...}
```

Como o runtime das Durable Functions gerencia o estado para você, você não precisa implementar seu próprio mecanismo de acompanhamento de status.

A extensão Durable Functions expõe as APIs HTTP internas que gerenciam orquestrações de execução longa. Como alternativa, você pode implementar esse padrão por conta própria usando seus próprios gatilhos de função (como HTTP, uma fila ou os Hubs de Eventos do Azure) e a [associação de cliente de orquestração](durable-functions-bindings.md#orchestration-client). Por exemplo, você pode usar uma mensagem da fila para disparar o encerramento. Ou você pode usar um gatilho HTTP protegido por uma política de autenticação do Azure Active Directory, em vez de APIs HTTP internas que usam uma chave gerada para autenticação.

Para obter mais informações, confira o artigo [Recursos HTTP](durable-functions-http-features.md), que explica como você pode expor processos assíncronos de execução longa via HTTP usando a extensão das Durable Functions.

### <a name="monitoring"></a>Padrão 4: Monitoramento

O padrão de monitor refere-se a um processo recorrente e flexível em um fluxo de trabalho. Um exemplo é fazer uma sondagem até que condições específicas sejam atendidas. Você pode usar um [gatilho de temporizador](../functions-bindings-timer.md) normal para lidar com um cenário básico, como um trabalho de limpeza periódico, mas seu intervalo é estático e o gerenciamento do tempo de vida da instância torna-se complexo. Use as Durable Functions para criar intervalos de recorrência flexíveis, gerenciar os tempos de vida de tarefas e criar vários processos de monitor com base em uma única orquestração.

Um exemplo do padrão de monitor é reverter o cenário de API HTTP assíncrona anterior. Em vez de expor um ponto de extremidade para um cliente externo monitorar uma operação de execução longa, o monitor de execução longa consome um ponto de extremidade externo e, em seguida, aguarda uma alteração de estado.

![Um diagrama do padrão de monitor](./media/durable-functions-concepts/monitor.png)

Em poucas linhas de código, você pode usar as Durable Functions para criar vários monitores que observam pontos de extremidade arbitrários. Os monitores podem encerrar a execução quando uma condição é atendida ou o [DurableOrchestrationClient](durable-functions-instance-management.md) pode encerrar os monitores. Você pode alterar o intervalo de `wait` de um monitor de acordo com uma condição específica (por exemplo, retirada exponencial). 

O seguinte código implementa um monitor básico:

#### <a name="c"></a>C#

```csharp
[FunctionName("MonitorJobStatus")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    int jobId = context.GetInput<int>();
    int pollingInterval = GetPollingInterval();
    DateTime expiryTime = GetExpiryTime();

    while (context.CurrentUtcDateTime < expiryTime)
    {
        var jobStatus = await context.CallActivityAsync<string>("GetJobStatus", jobId);
        if (jobStatus == "Completed")
        {
            // Perform an action when a condition is met.
            await context.CallActivityAsync("SendAlert", machineId);
            break;
        }

        // Orchestration sleeps until this time.
        var nextCheck = context.CurrentUtcDateTime.AddSeconds(pollingInterval);
        await context.CreateTimer(nextCheck, CancellationToken.None);
    }

    // Perform more work here, or let the orchestration end.
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (apenas Funções 2.x)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const jobId = context.df.getInput();
    const pollingInternal = getPollingInterval();
    const expiryTime = getExpiryTime();

    while (moment.utc(context.df.currentUtcDateTime).isBefore(expiryTime)) {
        const jobStatus = yield context.df.callActivity("GetJobStatus", jobId);
        if (jobStatus === "Completed") {
            // Perform an action when a condition is met.
            yield context.df.callActivity("SendAlert", machineId);
            break;
        }

        // Orchestration sleeps until this time.
        const nextCheck = moment.utc(context.df.currentUtcDateTime).add(pollingInterval, 's');
        yield context.df.createTimer(nextCheck.toDate());
    }

    // Perform more work here, or let the orchestration end.
});
```

Quando uma solicitação é recebida, uma nova instância de orquestração é criada para essa ID do trabalho. A instância sonda um status até que uma condição seja atendida e o loop seja encerrado. Um temporizador durável controla o intervalo de sondagem. Em seguida, mais trabalho pode ser realizado ou a orquestração pode ser encerrada. Quando o `context.CurrentUtcDateTime` (.NET) ou o `context.df.currentUtcDateTime` (JavaScript) excede o valor `expiryTime`, o monitor é encerrado.

### <a name="human"></a>Padrão 5: Interação humana

Muitos processos automatizados envolvem algum tipo de interação humana. Envolver humanos em um processo automatizado é complicado, porque as pessoas não estão tão altamente disponíveis nem são tão dinâmicas quanto os serviços de nuvem. Um processo automatizado pode permitir essa interação usando tempos limite e a lógica de compensação.

Um processo de aprovação é um exemplo de um processo empresarial que envolve a interação humana. A aprovação de um gerente pode ser necessária para um relatório de despesas que exceda determinado valor em dólares. Se o gerente não aprovar o relatório de despesas em até 72 horas (talvez ele esteja de férias), um processo de escalonamento será iniciado para obter a aprovação de outra pessoa (talvez o gerente do gerente).

![Um diagrama do padrão de interação humana](./media/durable-functions-concepts/approval.png)

Você pode implementar o padrão nesse exemplo usando uma função de orquestrador. O orquestrador usa um [temporizador durável](durable-functions-timers.md) para solicitar aprovação. O orquestrador fará o escalonamento se o tempo limite se esgotar. O orquestrador aguarda um [evento externo](durable-functions-external-events.md), como uma notificação gerada por uma interação humana.

Estes exemplos criam um processo de aprovação para demonstrar o padrão de interação humana:

#### <a name="c"></a>C#

```csharp
[FunctionName("ApprovalWorkflow")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    await context.CallActivityAsync("RequestApproval", null);
    using (var timeoutCts = new CancellationTokenSource())
    {
        DateTime dueTime = context.CurrentUtcDateTime.AddHours(72);
        Task durableTimeout = context.CreateTimer(dueTime, timeoutCts.Token);

        Task<bool> approvalEvent = context.WaitForExternalEvent<bool>("ApprovalEvent");
        if (approvalEvent == await Task.WhenAny(approvalEvent, durableTimeout))
        {
            timeoutCts.Cancel();
            await context.CallActivityAsync("ProcessApproval", approvalEvent.Result);
        }
        else
        {
            await context.CallActivityAsync("Escalate", null);
        }
    }
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (apenas Funções 2.x)

```javascript
const df = require("durable-functions");
const moment = require('moment');

module.exports = df.orchestrator(function*(context) {
    yield context.df.callActivity("RequestApproval");

    const dueTime = moment.utc(context.df.currentUtcDateTime).add(72, 'h');
    const durableTimeout = context.df.createTimer(dueTime.toDate());

    const approvalEvent = context.df.waitForExternalEvent("ApprovalEvent");
    if (approvalEvent === yield context.df.Task.any([approvalEvent, durableTimeout])) {
        durableTimeout.cancel();
        yield context.df.callActivity("ProcessApproval", approvalEvent.result);
    } else {
        yield context.df.callActivity("Escalate");
    }
});
```

Para criar o temporizador durável, chame `context.CreateTimer` (.NET) ou `context.df.createTimer` (JavaScript). A notificação é recebida pelo `context.WaitForExternalEvent` (.NET) ou `context.df.waitForExternalEvent` (JavaScript). Em seguida, `Task.WhenAny` (.NET) ou `context.df.Task.any` (JavaScript) é chamado para decidir se o próximo passo é escalonar (o tempo limite ocorre primeiro) ou processar a aprovação (a aprovação é recebida antes do tempo limite).

Um cliente externo pode entregar a notificação de eventos para uma função de orquestrador em espera usando as [APIs HTTP internas](durable-functions-http-api.md#raise-event) ou a API [DurableOrchestrationClient.RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_System_String_System_String_System_Object_) em outra função:

```csharp
[FunctionName("RaiseEventToOrchestration")]
public static async Task Run(
    [HttpTrigger] string instanceId,
    [OrchestrationClient] DurableOrchestrationClient client)
{
    bool isApproved = true;
    await client.RaiseEventAsync(instanceId, "ApprovalEvent", isApproved);
}
```

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const isApproved = true;
    await client.raiseEvent(instanceId, "ApprovalEvent", isApproved);
};
```

```bash
curl -d "true" http://localhost:7071/runtime/webhooks/durabletask/instances/{instanceId}/raiseEvent/ApprovalEvent -H "Content-Type: application/json"
```

### <a name="aggregator"></a>Padrão nº 6: Agregador (versão prévia)

O sexto padrão trata da agregação de dados de evento durante um período em uma única *entidade* endereçável. Nesse padrão, os dados que estão sendo agregados podem vir de várias fontes, podem ser entregues em lotes ou podem estar distribuídos por longos períodos. O agregador pode precisar executar uma ação nos dados de evento quando eles são recebidos e os clientes externos talvez precisem consultar os dados agregados.

![Diagrama do agregador](./media/durable-functions-concepts/aggregator.png)

A complexidade de tentar implementar esse padrão com as funções normais sem estado é que o controle de simultaneidade se torna um grande desafio. Além de se preocupar com vários threads modificando os mesmos dados ao mesmo tempo, você precisa se preocupar em garantir que o agregador só seja executado em uma única VM por vez.

Usando uma [função de Entidade Durável](durable-functions-preview.md#entity-functions), é possível implementar esse padrão com facilidade como uma única função.

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    int currentValue = ctx.GetState<int>();

    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            int amount = ctx.GetInput<int>();
            currentValue += amount;
            break;
        case "reset":
            currentValue = 0;
            break;
        case "get":
            ctx.Return(currentValue);
            break;
    }

    ctx.SetState(currentValue);
}
```

As Entidades Duráveis também podem ser modeladas como classes .NET. Esse modelo pode ser útil se a lista de operações é fixa e se torna grande. O exemplo a seguir é uma implementação equivalente da entidade `Counter` usando métodos e classes .NET.

```csharp
public class Counter
{
    [JsonProperty("value")]
    public int CurrentValue { get; set; }

    public void Add(int amount) => this.CurrentValue += amount;

    public void Reset() => this.CurrentValue = 0;

    public int Get() => this.CurrentValue;

    [FunctionName(nameof(Counter))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<Counter>();
}
```

Os clientes podem enfileirar *operações* de uma função de entidade (também conhecido como "sinalização") usando a [associação do cliente de entidade](durable-functions-bindings.md#entity-client).

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static async Task Run(
    [EventHubTrigger("device-sensor-events")] EventData eventData,
    [OrchestrationClient] IDurableOrchestrationClient entityClient)
{
    var metricType = (string)eventData.Properties["metric"];
    var delta = BitConverter.ToInt32(eventData.Body, eventData.Body.Offset);

    // The "Counter/{metricType}" entity is created on-demand.
    var entityId = new EntityId("Counter", metricType);
    await entityClient.SignalEntityAsync(entityId, "add", delta);
}
```

Os proxies gerados dinamicamente também estão disponíveis para sinalizar entidades para torná-las fortemente tipadas. Além da sinalização, os clientes também podem consultar o estado de uma função de entidade usando [métodos fortemente tipados](durable-functions-bindings.md#entity-client-usage) na associação do cliente de orquestração.

> [!NOTE]
> Atualmente, as funções de entidade só estão disponíveis no .NET como parte da [versão prévia das Durable Functions 2.0](durable-functions-preview.md).

## <a name="the-technology"></a>A tecnologia

Nos bastidores, a extensão Durable Functions baseia-se na [Durable Task Framework](https://github.com/Azure/durabletask), uma biblioteca open-source no GitHub usada para a criação de fluxos de trabalho em código. Assim como o Azure Functions é a evolução sem servidor do Azure WebJobs, as Durable Functions são a evolução sem servidor da Durable Task Framework. A Microsoft e outras organizações usam a Durable Task Framework extensivamente para automatizar processos críticos. Ele é uma opção natural para o ambiente sem servidor do Azure Functions.

## <a name="code-constraints"></a>Restrições de código

Para fornecer garantias de execução confiáveis e de execução longa, as funções de orquestrador têm um conjunto de regras de codificação que precisam ser seguidas. Para obter mais informações, confira o artigo [Restrições de código na função de orquestrador](durable-functions-code-constraints.md).

## <a name="billing"></a>Cobrança

As Durable Functions são cobradas da mesma forma que o Azure Functions. Para saber mais, confira [Preços do Azure Functions](https://azure.microsoft.com/pricing/details/functions/). Ao executar funções de orquestrador no [plano de Consumo](../functions-scale.md#consumption-plan) do Azure Functions, há alguns comportamentos de cobrança para sua informação. Para obter mais informações sobre esses comportamentos, confira o artigo [Cobrança das Durable Functions](durable-functions-billing.md).

## <a name="jump-right-in"></a>Comece a usar agora

Comece a usar as Durable Functions em menos de 10 minutos concluindo um destes tutoriais de início rápido específicos a uma linguagem:

* [C# usando o Visual Studio 2019](durable-functions-create-first-csharp.md)
* [JavaScript usando o Visual Studio Code](quickstart-js-vscode.md)

Nos dois inícios rápidos, você criará e testará uma durable function "Olá, Mundo" localmente. Em seguida, você publicará o código de função no Azure. A função que você criará orquestra e encadeia chamadas para outras funções.

## <a name="learn-more"></a>Saiba mais

O seguinte vídeo destaca os benefícios das Durable Functions:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Durable-Functions-in-Azure-Functions/player] 

Para obter uma discussão mais detalhada sobre as Durable Functions e a tecnologia subjacente, confira o seguinte vídeo (ele se concentra no .NET, mas os conceitos também se aplicam a outras linguagens compatíveis):

> [!VIDEO https://channel9.msdn.com/Events/dotnetConf/2018/S204/player]

Como as Durable Functions são uma extensão avançada do [Azure Functions](../functions-overview.md), elas não são apropriadas para todos os aplicativos. Para obter uma comparação com outras tecnologias de orquestração do Azure, confira [Comparar o Azure Functions e os Aplicativos Lógicos do Azure](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-azure-functions-and-azure-logic-apps).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tipos de função e recursos das Durable Functions](durable-functions-types-features-overview.md)
