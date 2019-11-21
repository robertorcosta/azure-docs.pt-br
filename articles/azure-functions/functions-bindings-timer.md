---
title: Gatilho de temporizador para o Azure Functions
description: Entenda como usar gatilhos de temporizador no Azure Functions.
author: craigshoemaker
ms.assetid: d2f013d1-f458-42ae-baf8-1810138118ac
ms.topic: reference
ms.date: 09/08/2018
ms.author: cshoe
ms.custom: ''
ms.openlocfilehash: ebe7916900ded20dc6555c9c28277dea889539a3
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230962"
---
# <a name="timer-trigger-for-azure-functions"></a>Gatilho de temporizador para o Azure Functions 

Este artigo explica como trabalhar com gatilhos de temporizador no Azure Functions. Um gatilho de temporizador permite executar uma função em uma agenda. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pacotes – Functions 1.x

O gatilho de timer é fornecido no [Microsoft.Azure.WebJobs.Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) pacote NuGet, versão 2. x. O código-fonte do pacote está no repositório GitHub [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Timers/).

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="packages---functions-2x"></a>Pacotes - Functions 2. x

O gatilho de timer é fornecido no [Microsoft.Azure.WebJobs.Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) pacote NuGet, versão 2. x. O código-fonte do pacote está no repositório GitHub [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/).

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="example"></a>Exemplo

Consulte o exemplo específico a um idioma:

* [C#](#c-example)
* [Script do C# (. CSx)](#c-script-example)
* [F#](#f-example)
* [Java](#java-example)
* [JavaScript](#javascript-example)
* [Python](#python-example)

### <a name="c-example"></a>Exemplo de C#

The following example shows a [C# function](functions-dotnet-class-library.md) that is executed each time the minutes have a value divisible by five (eg if the function starts at 18:57:00, the next performance will be at 19:00:00). The [`TimerInfo`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) object is passed into the function.

```cs
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
{
    if (myTimer.IsPastDue)
    {
        log.LogInformation("Timer is running late!");
    }
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
}
```

### <a name="c-script-example"></a>Exemplo 2 de C# script

O exemplo a seguir mostra uma associação de gatilho de temporizador em um arquivo *function.json* e uma [função C# script](functions-reference-csharp.md) que usa a associação. A função grava um log que indica se esta chamada de função deve-se a uma ocorrência de agendamento ausente. The [`TimerInfo`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) object is passed into the function.

Aqui estão os dados de associação no arquivo *function.json*:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Aqui está o código de script do C#:

```csharp
public static void Run(TimerInfo myTimer, ILogger log)
{
    if (myTimer.IsPastDue)
    {
        log.LogInformation("Timer is running late!");
    }
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}" );  
}
```

### <a name="f-example"></a>Exemplo de F#

O exemplo a seguir mostra uma associação de gatilho de temporizador em um arquivo *function.json* e uma [função de script F#](functions-reference-fsharp.md) que usa a associação. A função grava um log que indica se esta chamada de função deve-se a uma ocorrência de agendamento ausente. The [`TimerInfo`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) object is passed into the function.

Aqui estão os dados de associação no arquivo *function.json*:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Aqui está o código de script F#:

```fsharp
let Run(myTimer: TimerInfo, log: ILogger ) =
    if (myTimer.IsPastDue) then
        log.LogInformation("F# function is running late.")
    let now = DateTime.Now.ToLongTimeString()
    log.LogInformation(sprintf "F# function executed at %s!" now)
```

### <a name="java-example"></a>Exemplo do Java

A função de exemplo a seguir é disparada e executada a cada cinco minutos. A anotação `@TimerTrigger` na função define o agendamento usando o mesmo formato de cadeia de caracteres que as [expressões CRON](https://en.wikipedia.org/wiki/Cron#CRON_expression).

```java
@FunctionName("keepAlive")
public void keepAlive(
  @TimerTrigger(name = "keepAliveTrigger", schedule = "0 */5 * * * *") String timerInfo,
      ExecutionContext context
 ) {
     // timeInfo is a JSON string, you can deserialize it to an object using your favorite JSON library
     context.getLogger().info("Timer is triggered: " + timerInfo);
}
```

### <a name="javascript-example"></a>Exemplo de JavaScript

O exemplo a seguir mostra uma associação de gatilho de temporizador em um arquivo *function.json* e uma [função JavaScript](functions-reference-node.md) que usa a associação. A função grava um log que indica se esta chamada de função deve-se a uma ocorrência de agendamento ausente. A [timer object](#usage) is passed into the function.

Aqui estão os dados de associação no arquivo *function.json*:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Aqui está o código JavaScript:

```JavaScript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    if (myTimer.IsPastDue)
    {
        context.log('Node is running late!');
    }
    context.log('Node timer trigger function ran!', timeStamp);   

    context.done();
};
```

### <a name="python-example"></a>Exemplo de Python

The following example uses a timer trigger binding whose configuration is described in the *function.json* file. The actual [Python function](functions-reference-python.md) that uses the binding is described in the *__init__.py* file. The object passed into the function is of type [azure.functions.TimerRequest object](/python/api/azure-functions/azure.functions.timerrequest). The function logic writes to the logs indicating whether the current invocation is due to a missed schedule occurrence. 

Aqui estão os dados de associação no arquivo *function.json*:

```json
{
    "name": "mytimer",
    "type": "timerTrigger",
    "direction": "in",
    "schedule": "0 */5 * * * *"
}
```

Confira o código Python:

```python
import datetime
import logging

import azure.functions as func


def main(mytimer: func.TimerRequest) -> None:
    utc_timestamp = datetime.datetime.utcnow().replace(
        tzinfo=datetime.timezone.utc).isoformat()

    if mytimer.past_due:
        logging.info('The timer is past due!')

    logging.info('Python timer trigger function ran at %s', utc_timestamp)
```

## <a name="attributes"></a>Atributos

Em [bibliotecas de classes do C#](functions-dotnet-class-library.md), utilize o atributo [TimerTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerTriggerAttribute.cs).

O construtor do atributo usa a expressão CRON ou um `TimeSpan`. Você poderá usar `TimeSpan` apenas se o aplicativo de função estiver em execução em um Plano do Serviço de Aplicativo. O exemplo a seguir mostra uma expressão CRON:

```csharp
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
{
    if (myTimer.IsPastDue)
    {
        log.LogInformation("Timer is running late!");
    }
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
}
```

## <a name="configuration"></a>Configuração

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *function.json* e no `TimerTrigger` atributo.

|Propriedade function.json | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | N/D | Deve ser definido como "timerTrigger". Essa propriedade é definida automaticamente quando você cria o gatilho no portal do Azure.|
|**direction** | N/D | Deve ser definido como "in". Essa propriedade é definida automaticamente quando você cria o gatilho no portal do Azure. |
|**name** | N/D | O nome da variável que representa o objeto de temporizador no código de função. | 
|**schedule**|**ScheduleExpression**|Um [expressão CRON](#ncrontab-expressions) ou um valor [TimeSpan](#timespan). É possível usar um `TimeSpan` somente para um aplicativo de função executado em um Plano do Serviço de Aplicativo. Você pode colocar a expressão de agendamento em uma configuração de aplicativo e definir essa propriedade como o nome da configuração do aplicativo envolvido em sinais **%** , como neste exemplo: "%ScheduleAppSetting%". |
|**runOnStartup**|**runOnStartup**|Se `true`, a função será invocada quando o runtime for iniciado. Por exemplo, o runtime inicia quando o aplicativo de função desperta depois de ficar ocioso devido à inatividade. when the function app restarts due to function changes, and when the function app scales out. So **runOnStartup** should rarely if ever be set to `true`, especially in production. |
|**useMonitor**|**UseMonitor**|Definido como `true` ou `false` para indicar se o agendamento deve ser monitorado. Agendar o monitoramento persiste as ocorrências de agendamento para ajudar a garantir que o agendamento seja mantido corretamente mesmo quando instâncias do aplicativo de função forem reiniciadas. If not set explicitly, the default is `true` for schedules that have a recurrence interval greater than or equal to 1 minute. Para agendamentos que disparam mais de uma vez por minuto, o padrão é `false`.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!CAUTION]
> Recomendamos a configuração **runOnStartup** para `true` em produção. Usar essa configuração faz com que código seja executado em momentos altamente imprevisíveis. Em determinadas configurações de produção, essas execuções extras podem resultar em custos significativamente mais altos para aplicativos hospedados em planos de consumo. For example, with **runOnStartup** enabled the trigger is invoked whenever your function app is scaled. Verifique se você compreender totalmente o comportamento de produção de suas funções antes de habilitar **runOnStartup** em produção.   

## <a name="usage"></a>Uso

When a timer trigger function is invoked, a timer object is passed into the function. O JSON a seguir é uma representação de exemplo do objeto timer.

```json
{
    "Schedule":{
    },
    "ScheduleStatus": {
        "Last":"2016-10-04T10:15:00+00:00",
        "LastUpdated":"2016-10-04T10:16:00+00:00",
        "Next":"2016-10-04T10:20:00+00:00"
    },
    "IsPastDue":false
}
```

A propriedade `IsPastDue` é `true` quando a invocação da função atual é posterior ao agendado. Por exemplo, uma reinicialização do aplicativo de função pode causar a perda de uma invocação.

## <a name="ncrontab-expressions"></a>NCRONTAB expressions 

Azure Functions uses the [NCronTab](https://github.com/atifaziz/NCrontab) library to interpret NCRONTAB expressions. An NCRONTAB exppression is similar to a CRON expression except that it includes an additional sixth field at the beginning to use for time precision in seconds:

`{second} {minute} {hour} {day} {month} {day-of-week}`

Cada campo pode ter um dos seguintes tipos de valores:

|Type  |Exemplo  |Quando disparado  |
|---------|---------|---------|
|Um valor específico |<nobr>"0 5 * * * *"</nobr>|em hh:05:00, em que hh é cada hora (uma vez por hora)|
|Todos os valores (`*`)|<nobr>"0 * 5 * * *"</nobr>|em 5:mm: 00 diariamente, em que mm é cada minuto da hora (60 vezes por dia)|
|Um intervalo (`-` operador)|<nobr>"5-7 * * * * *"</nobr>|em hh:mm:05, hh:mm:06 e hh:mm:07, em que hh é cada minuto de cada hora (3 vezes por minuto)|
|Um conjunto de valores (`,` operador)|<nobr>"5,8,10 * * * * *"</nobr>|em hh:mm:05, hh:mm:08 e hh:mm:10, em que hh é cada minuto de cada hora (3 vezes por minuto)|
|Um valor de intervalo (`/` operador)|<nobr>"0 */5 * * * *"</nobr>|em hh:05:00, hh:10:00, hh:15:00 e assim por diante por meio de hh:55:00, em que hh é cada hora (12 vezes por hora)|

[!INCLUDE [functions-cron-expressions-months-days](../../includes/functions-cron-expressions-months-days.md)]

### <a name="ncrontab-examples"></a>NCRONTAB examples

Here are some examples of NCRONTAB expressions you can use for the timer trigger in Azure Functions.

|Exemplo|Quando disparado  |
|---------|---------|
|`"0 */5 * * * *"`|uma vez a cada cinco minutos|
|`"0 0 * * * *"`|uma vez a cada hora|
|`"0 0 */2 * * *"`|uma vez a cada duas horas|
|`"0 0 9-17 * * *"`|uma vez a cada hora entre 9h e 17h|
|`"0 30 9 * * *"`|às 9h30 todos os dias|
|`"0 30 9 * * 1-5"`|às 9h30 todo dia útil|
|`"0 30 9 * Jan Mon"`|em 9H30 toda segunda-feira em janeiro|


### <a name="ncrontab-time-zones"></a>NCRONTAB time zones

Os números em uma expressão CRON se referem a uma hora e uma data, não a um período de tempo. Por exemplo, um 5 no campo `hour` se refere a 5h, não cada cinco horas.

O fuso horário padrão usado com as expressões CRON é a Hora Universal Coordenada (UTC). Para que a expressão CRON se baseie em outro fuso horário, crie uma configuração de aplicativo para o aplicativo de funções denominada `WEBSITE_TIME_ZONE`. Defina o valor para o nome do fuso horário desejado, conforme mostrado no [Índice de fuso horário da Microsoft](https://technet.microsoft.com/library/cc749073). 

Por exemplo, a *Hora padrão da costa leste dos EUA* é UTC-05:00. To have your timer trigger fire at 10:00 AM EST every day, use the following NCRONTAB expression that accounts for UTC time zone:

```
"0 0 15 * * *"
``` 

Ou criar uma configuração de aplicativo para seu aplicativo de funções denominada `WEBSITE_TIME_ZONE` e definir o valor como **Horário padrão da costa leste dos EUA**.  Then uses the following NCRONTAB expression: 

```
"0 0 10 * * *"
``` 

Quando você usa `WEBSITE_TIME_ZONE`, o horário é ajustado para as alterações de hora no fuso horário específico, como o horário de verão. 

## <a name="timespan"></a>timespan

 É possível usar um `TimeSpan` somente para um aplicativo de função executado em um Plano do Serviço de Aplicativo.

Ao contrário de uma expressão CRON, um valor `TimeSpan` especifica o intervalo de tempo entre cada invocação de função. Quando uma função é concluída após a execução por mais tempo do que o intervalo especificado, o temporizador imediatamente chama a função novamente.

Expresso como uma cadeia de caracteres, o formato `TimeSpan` é `hh:mm:ss` quando `hh` é menor que 24. Quando os dois primeiros dígitos são 24 ou superior, o formato é `dd:hh:mm`. Estes são alguns exemplos:

|Exemplo |Quando disparado  |
|---------|---------|
|"01:00:00" | a cada hora        |
|"00:01:00"|a cada minuto         |
|"24:00:00" | every 24 hours        |
|"1.00:00:00" | every day        |

## <a name="scale-out"></a>Expansão

Se um aplicativo de funções se expandir para várias instâncias, apenas uma única instância de uma função disparada por temporizador será executada em todas as instâncias.

## <a name="function-apps-sharing-storage"></a>Armazenamento de compartilhamento de aplicativos de função

If you are sharing storage accounts across function apps that are not deployed to app service, you might need to explicitly assign host ID to each app.

| Versão do Functions | Configuração                                              |
| ----------------- | ---------------------------------------------------- |
| 2. x               | `AzureFunctionsWebHost__hostid` environment variable |
| 1.x               | `id` in *host.json*                                  |

You can omit the identifying value or manually set each function app's identifying configuration to a different value.

The timer trigger uses a storage lock to ensure that there is only one timer instance when a function app scales out to multiple instances. If two function apps share the same identifying configuration and each uses a timer trigger, only one timer runs.

## <a name="retry-behavior"></a>Tentar comportamento novamente

Ao contrário do gatilho de fila, o gatilho de temporizador não tenta novamente após a falha de uma função. Quando uma função falha, ele não é chamado novamente até a próxima vez na agenda.

## <a name="troubleshooting"></a>Solução de Problemas

Para obter informações sobre o que fazer quando o gatilho de timer não funcionar conforme o esperado, confira [Investigar e relatar problemas com funções disparadas de timer não acionadas](https://github.com/Azure/azure-functions-host/wiki/Investigating-and-reporting-issues-with-timer-triggered-functions-not-firing).

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Vá para um guia de início rápido que use um gatilho de temporizador](functions-create-scheduled-function.md)

> [!div class="nextstepaction"]
> [Aprenda mais sobre gatilhos e de associações do Azure Functions](functions-triggers-bindings.md)
