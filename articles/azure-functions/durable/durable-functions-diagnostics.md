---
title: Diagnóstico nas Funções Duráveis – Azure
description: Saiba como diagnosticar problemas com a extensão de Funções Duráveis do Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 4cb832f8fe11ac2581e97d9cdcc777eaff702ee9
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231464"
---
# <a name="diagnostics-in-durable-functions-in-azure"></a>Diagnóstico no Durable Functions no Azure

Há várias opções para diagnosticar problemas com as [Funções Duráveis](durable-functions-overview.md). Algumas dessas opções são as mesmas usadas para funções regulares e algumas delas são exclusivas das Funções Duráveis.

## <a name="application-insights"></a>Percepções sobre o Aplicativo

O [Application Insights](../../azure-monitor/app/app-insights-overview.md) é a maneira recomendada de fazer diagnóstico e monitoramento no Azure Functions. O mesmo se aplica às Funções Duráveis. Para obter uma visão geral de como usar o Application Insights em seu aplicativo de funções, consulte [Monitor o Azure Functions](../functions-monitoring.md).

A Extensão Durável do Azure Functions também emite *eventos de acompanhamento*, que permitem rastrear a execução de uma orquestração de ponta a ponta. These tracking events can be found and queried using the [Application Insights Analytics](../../azure-monitor/app/analytics.md) tool in the Azure portal.

### <a name="tracking-data"></a>Acompanhamento de dados

Cada evento de ciclo de vida de uma instância de orquestração faz com que um evento de acompanhamento seja gravado na coleção de **rastreamentos** no Application Insights. Esse evento tem um conteúdo **customDimensions** com vários campos.  Todos os nomes de campo são prefixados com `prop__`.

* **hubName**: o nome do hub de tarefas no qual suas orquestrações estão em execução.
* **appName**: o nome do aplicativo de funções. This field is useful when you have multiple function apps sharing the same Application Insights instance.
* **slotName**: o [slot de implantação](../functions-deployment-slots.md) no qual o aplicativo de funções atual está sendo executado. This field is useful when you leverage deployment slots to version your orchestrations.
* **functionName**: o nome da função de orquestrador ou atividade.
* **functionType**: o tipo da função, como **Orquestrador** ou **Atividade**.
* **instanceId**: a ID exclusiva da instância de orquestração.
* **state**: o estado de execução do ciclo de vida da instância. Os valores válidos incluem:
  * **Scheduled**: a função foi agendada para execução, mas ainda não começou a ser executada.
  * **Started**: a função começou a ser executada, mas não ainda esperou ou foi concluída.
  * **Awaited**: o orquestrador agendou algum trabalho e está aguardando sua conclusão.
  * **Listening**: o orquestrador está escutando uma notificação de evento externo.
  * **Completed**: a função foi concluída com êxito.
  * **Failed**: a função falhou com um erro.
* **reason**: dados adicionais associados ao evento de acompanhamento. Por exemplo, se uma instância estiver aguardando uma notificação de evento externo, esse campo indica o nome do evento que ela está aguardando. If a function has failed, this field will contain the error details.
* **isReplay**: valor booliano que indica se o evento de acompanhamento deve ter a execução reproduzida.
* **extensionVersion**: a versão da extensão da Tarefa Durável. The version information is especially important data when reporting possible bugs in the extension. Instâncias de execução longa podem relatar várias versões se uma atualização ocorrer durante sua execução.
* **sequenceNumber**: número de sequência de execução para um evento. Combinado com o carimbo de data/hora ajuda a ordenar os eventos por tempo de execução. *Observe que esse número será redefinido para zero se o host for reiniciado enquanto a instância estiver em execução, portanto, primeiro é importante sempre classificar pelo carimbo de data/hora e, depois, sequenceNumber.*

The verbosity of tracking data emitted to Application Insights can be configured in the `logger` (Functions 1.x) or `logging` (Functions 2.0) section of the `host.json` file.

#### <a name="functions-10"></a>Functions 1.0

```json
{
    "logger": {
        "categoryFilter": {
            "categoryLevels": {
                "Host.Triggers.DurableTask": "Information"
            }
        }
    }
}
```

#### <a name="functions-20"></a>Functions 2.0

```json
{
    "logging": {
        "logLevel": {
          "Host.Triggers.DurableTask": "Information",
        },
    }
}
```

Por padrão, todos os eventos de acompanhamento de não-replay são emitidos. O volume dos dados pode ser reduzido definindo `Host.Triggers.DurableTask` como `"Warning"` ou `"Error"`. Nesse caso, os eventos de acompanhamento serão emitidos somente para situações excepcionais.

Para habilitar a emitir os eventos de reprodução de orquestração detalhado, o `LogReplayEvents` pode ser definido como `true` na `host.json` do arquivo sob `durableTask` conforme mostrado:

#### <a name="functions-10"></a>Functions 1.0

```json
{
    "durableTask": {
        "logReplayEvents": true
    }
}
```

#### <a name="functions-20"></a>Functions 2.0

```javascript
{
    "extensions": {
        "durableTask": {
            "logReplayEvents": true
        }
    }
}
```

> [!NOTE]
> Por padrão, a amostragem da telemetria do Application Insights é feita segundo o Azure Functions runtime, para evitar a emissão de dados com frequência excessiva. Isso pode fazer com que informações de acompanhamento sejam perdidas quando muitos eventos de ciclo de vida ocorrerem em um curto período. O [artigo sobre Monitoramento no Azure Functions](../functions-monitoring.md#configure-sampling) explica como configurar esse comportamento.

### <a name="single-instance-query"></a>Consulta de instância única

A consulta a seguir mostra dados de acompanhamento históricos de uma única instância de orquestração da função [Sequência Hello](durable-functions-sequence.md). Ela é escrita usando a [AIQL (Linguagem de Consulta do Application Insights)](https://aka.ms/LogAnalyticsLanguageReference). Ela filtra a execução de reproduções para que somente o caminho de execução *lógico* seja mostrado. Os eventos podem ser ordenados classificando por `timestamp` e `sequenceNumber`, conforme mostrado na consulta abaixo:

```AIQL
let targetInstanceId = "ddd1aaa685034059b545eb004b15d4eb";
let start = datetime(2018-03-25T09:20:00);
traces
| where timestamp > start and timestamp < start + 30m
| where customDimensions.Category == "Host.Triggers.DurableTask"
| extend functionName = customDimensions["prop__functionName"]
| extend instanceId = customDimensions["prop__instanceId"]
| extend state = customDimensions["prop__state"]
| extend isReplay = tobool(tolower(customDimensions["prop__isReplay"]))
| extend sequenceNumber = tolong(customDimensions["prop__sequenceNumber"])
| where isReplay != true
| where instanceId == targetInstanceId
| sort by timestamp asc, sequenceNumber asc
| project timestamp, functionName, state, instanceId, sequenceNumber, appName = cloud_RoleName
```

O resultado é uma lista de eventos de rastreamento que mostra o caminho de execução da orquestração, incluindo quaisquer funções de atividade ordenadas pelo tempo de execução em ordem crescente.

![Consulta do Application Insights](./media/durable-functions-diagnostics/app-insights-single-instance-ordered-query.png)

### <a name="instance-summary-query"></a>Consulta de resumo da instância

A consulta a seguir exibe o status de todas as instâncias de orquestração que foram executadas em um intervalo de tempo especificado.

```AIQL
let start = datetime(2017-09-30T04:30:00);
traces
| where timestamp > start and timestamp < start + 1h
| where customDimensions.Category == "Host.Triggers.DurableTask"
| extend functionName = tostring(customDimensions["prop__functionName"])
| extend instanceId = tostring(customDimensions["prop__instanceId"])
| extend state = tostring(customDimensions["prop__state"])
| extend isReplay = tobool(tolower(customDimensions["prop__isReplay"]))
| extend output = tostring(customDimensions["prop__output"])
| where isReplay != true
| summarize arg_max(timestamp, *) by instanceId
| project timestamp, instanceId, functionName, state, output, appName = cloud_RoleName
| order by timestamp asc
```

O resultado é uma lista de IDs de instância e seu status de runtime atual.

![Consulta do Application Insights](./media/durable-functions-diagnostics/app-insights-single-summary-query.png)

## <a name="logging"></a>Registro em log

É importante ter em mente o comportamento de reprodução do orquestrador ao gravar logs diretamente de uma função de orquestrador. Por exemplo, considere a seguinte função de orquestrador:

### <a name="precompiled-c"></a>C# pré-compilado

```csharp
[FunctionName("FunctionChain")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context,
    ILogger log)
{
    log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

### <a name="c-script"></a>Script do C#

```csharp
public static async Task Run(
    IDurableOrchestrationContext context,
    ILogger log)
{
    log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

### <a name="javascript-functions-20-only"></a>Javascript (somente funções 2.0)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    context.log("Calling F1.");
    yield context.df.callActivity("F1");
    context.log("Calling F2.");
    yield context.df.callActivity("F2");
    context.log("Calling F3.");
    yield context.df.callActivity("F3");
    context.log("Done!");
});
```

The resulting log data is going to look something like the following example output:

```txt
Calling F1.
Calling F1.
Calling F2.
Calling F1.
Calling F2.
Calling F3.
Calling F1.
Calling F2.
Calling F3.
Done!
```

> [!NOTE]
> Lembre-se de que, embora os logs declarem estar chamando F1, F2 e F3, essas funções *realmente* são chamadas apenas na primeira vez em que são encontradas. Chamadas posteriores que ocorrem durante a reprodução são ignoradas e as saídas são reproduzidas para a lógica do orquestrador.

Se quiser registrar no log apenas a execução sem reproduções, você poderá escrever uma expressão condicional para registrar apenas se `IsReplaying` for `false`. Considere o exemplo anterior, mas desta vez com verificações de reprodução.

#### <a name="precompiled-c"></a>C# pré-compilado

```csharp
[FunctionName("FunctionChain")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context,
    ILogger log)
{
    if (!context.IsReplaying) log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    if (!context.IsReplaying) log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    if (!context.IsReplaying) log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

#### <a name="c"></a>C#

```cs
public static async Task Run(
    IDurableOrchestrationContext context,
    ILogger log)
{
    if (!context.IsReplaying) log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    if (!context.IsReplaying) log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    if (!context.IsReplaying) log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

#### <a name="javascript-functions-20-only"></a>Javascript (somente funções 2.0)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    if (!context.df.isReplaying) context.log("Calling F1.");
    yield context.df.callActivity("F1");
    if (!context.df.isReplaying) context.log("Calling F2.");
    yield context.df.callActivity("F2");
    if (!context.df.isReplaying) context.log("Calling F3.");
    yield context.df.callActivity("F3");
    context.log("Done!");
});
```

Starting in Durable Functions 2.0, .NET orchestrator functions also have the option to create an `ILogger` that automatically filters out log statements during replay. This automatic filtering is done using the `IDurableOrchestrationContext.CreateReplaySafeLogger(ILogger)` API.

```csharp
[FunctionName("FunctionChain")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context,
    ILogger log)
{
    log = context.CreateReplaySafeLogger(log);
    log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

With the previously mentioned changes, the log output is as follows:

```txt
Calling F1.
Calling F2.
Calling F3.
Done!
```

> [!NOTE]
> The previous C# examples are for Durable Functions 2.x. For Durable Functions 1.x, you must use `DurableOrchestrationContext` instead of `IDurableOrchestrationContext`. For more information about the differences between versions, see the [Durable Functions versions](durable-functions-versions.md) article.

## <a name="custom-status"></a>Status personalizados

O status de orquestração personalizado permite que você defina um valor de status personalizado para a função do orquestrador. Esse status é fornecido por meio da API de consulta de status HTTP ou da API `IDurableOrchestrationClient.GetStatusAsync`. O status de orquestração personalizado possibilita um monitoramento mais rico para funções do orquestrador. Por exemplo, o código de função do orquestrador pode incluir `IDurableOrchestrationContext.SetCustomStatus` chamadas para atualizar o progresso de uma operação demorada. Um cliente, como uma página da web ou outro sistema externo pode, em seguida, consultar periodicamente as APIs de consulta de status HTTP para informações de andamento mais ricas. Um exemplo usando `IDurableOrchestrationContext.SetCustomStatus` é fornecido abaixo:

### <a name="precompiled-c"></a>C# pré-compilado

```csharp
[FunctionName("SetStatusTest")]
public static async Task SetStatusTest([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    // ...do work...

    // update the status of the orchestration with some arbitrary data
    var customStatus = new { completionPercentage = 90.0, status = "Updating database records" };
    context.SetCustomStatus(customStatus);

    // ...do more work...
}
```

> [!NOTE]
> The previous C# example is for Durable Functions 2.x. For Durable Functions 1.x, you must use `DurableOrchestrationContext` instead of `IDurableOrchestrationContext`. For more information about the differences between versions, see the [Durable Functions versions](durable-functions-versions.md) article.

### <a name="javascript-functions-20-only"></a>Javascript (somente funções 2.0)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    // ...do work...

    // update the status of the orchestration with some arbitrary data
    const customStatus = { completionPercentage: 90.0, status: "Updating database records", };
    context.df.setCustomStatus(customStatus);

    // ...do more work...
});
```

Durante a execução da orquestração, clientes externos podem buscar este status personalizado:

```http
GET /admin/extensions/DurableTaskExtension/instances/instance123

```

O clientes terão a seguinte resposta:

```http
{
  "runtimeStatus": "Running",
  "input": null,
  "customStatus": { "completionPercentage": 90.0, "status": "Updating database records" },
  "output": null,
  "createdTime": "2017-10-06T18:30:24Z",
  "lastUpdatedTime": "2017-10-06T19:40:30Z"
}
```

> [!WARNING]
> O conteúdo do status personalizado é limitado a 16 KB de textoUTF-16 JSON porque ele precisa ser capaz de caber em uma coluna de Armazenamento de Tabelas do Azure. Você pode usar o armazenamento externo se precisar de conteúdo maior.

## <a name="debugging"></a>Depuração

O Azure Functions dá suporte à depuração do código de função diretamente e esse mesmo suporte se estende às Funções Duráveis, seja em execução no Azure ou localmente. No entanto, há alguns comportamentos a que você deve estar atento ao depurar:

* **Replay**: Orchestrator functions regularly [replay](durable-functions-orchestrations.md#reliability) when new inputs are received. This behavior means a single *logical* execution of an orchestrator function can result in hitting the same breakpoint multiple times, especially if it is set early in the function code.
* **Await**: Whenever an `await` is encountered in an orchestrator function, it yields control back to the Durable Task Framework dispatcher. If it is the first time a particular `await` has been encountered, the associated task is *never* resumed. Because the task never resumes, stepping *over* the await (F10 in Visual Studio) is not possible. Ignorar só funciona quando uma tarefa está sendo reproduzida.
* **Messaging timeouts**: Durable Functions internally uses queue messages to drive execution of orchestrator, activity, and entity functions. Em um ambiente com várias VMs, interromper a depuração por longos períodos pode fazer com que outra VM receba a mensagem, resultando em uma execução duplicada. Esse comportamento também existe para funções de gatilho de fila regulares, mas é importante ressaltar neste contexto, uma vez que as filas são um detalhe de implementação.
* **Stopping and starting**: Messages in Durable functions persist between debug sessions. If you stop debugging and terminate the local host process while a durable function is executing, that function may re-execute automatically in a future debug session. This behavior can be confusing when not expected. Clearing all messages from the [internal storage queues](durable-functions-perf-and-scale.md#internal-queue-triggers) between debug sessions is one technique to avoid this behavior.

> [!TIP]
> When setting breakpoints in orchestrator functions, if you want to only break on non-replay execution, you can set a conditional breakpoint that breaks only if `IsReplaying` is `false`.

## <a name="storage"></a>Armazenamento

Por padrão, as Funções Duráveis armazenam o estado no Armazenamento do Azure. This behavior means you can inspect the state of your orchestrations using tools such as [Microsoft Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

![Azure Storage Explorer screenshot](./media/durable-functions-diagnostics/storage-explorer.png)

Isso é útil para a depuração, pois você vê exatamente em qual estado uma orquestração pode estar. Mensagens nas filas também podem ser examinadas para saber qual trabalho está pendente (ou preso, em alguns casos).

> [!WARNING]
> Embora seja conveniente ver o histórico de execução no armazenamento de tabelas, evite depender desta tabela. Ela pode mudar à medida que a extensão de Funções Duráveis evoluir.

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Learn more about monitoring in Azure Functions](../functions-monitoring.md)
