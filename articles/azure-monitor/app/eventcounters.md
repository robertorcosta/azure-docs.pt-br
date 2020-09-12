---
title: Contadores de eventos no Application Insights | Microsoft Docs
description: Monitore o sistema e os EventCounters do .NET/.NET Core personalizados no Application Insights.
ms.topic: conceptual
ms.date: 09/20/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: f8ae36545eecbbad2a6695ca979fb7da8380e8cc
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/10/2020
ms.locfileid: "89657006"
---
# <a name="eventcounters-introduction"></a>Introdução ao EventCounters

`EventCounter` é o mecanismo do .NET/.NET Core para publicar e consumir contadores ou estatísticas. [Este](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.Tracing/documentation/EventCounterTutorial.md) documento fornece uma visão geral de `EventCounters` e exemplos sobre como publicá-los e consumi-los. Os EventCounters têm suporte em todas as plataformas de sistema operacional: Windows, Linux e macOS. Pode ser pensado como um equivalente multiplataforma para o [PerformanceCounters](/dotnet/api/system.diagnostics.performancecounter) que só tem suporte em sistemas Windows.

Embora os usuários possam publicar qualquer personalizado `EventCounters` para atender às suas necessidades, o .NET Core 3,0 e o tempo de execução mais recente publica um conjunto desses contadores por padrão. Este documento abordará as etapas necessárias para coletar e exibir `EventCounters` (definido pelo sistema ou definido pelo usuário) no insights aplicativo Azure.

## <a name="using-application-insights-to-collect-eventcounters"></a>Usar o Application Insights para coletar EventCounters

Application Insights dá suporte à coleta `EventCounters` com seu `EventCounterCollectionModule` , que faz parte do pacote NuGet recentemente lançado [Microsoft. ApplicationInsights. EventCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventCounterCollector). `EventCounterCollectionModule` é habilitado automaticamente ao usar [AspNetCore](asp-net-core.md) ou [WorkerService](worker-service.md). `EventCounterCollectionModule` coleta contadores com uma frequência de coleta não configurável de 60 segundos. Não há permissões especiais necessárias para coletar EventCounters.

## <a name="default-counters-collected"></a>Contadores padrão coletados

Para aplicativos em execução no .NET Core 3,0 ou superior, os contadores a seguir são coletados automaticamente pelo SDK. O nome dos contadores estará no formato "Categoria|Contador".

|Categoria | Contador|
|---------------|-------|
|`System.Runtime` | `cpu-usage` |
|`System.Runtime` | `working-set` |
|`System.Runtime` | `gc-heap-size` |
|`System.Runtime` | `gen-0-gc-count` |
|`System.Runtime` | `gen-1-gc-count` |
|`System.Runtime` | `gen-2-gc-count` |
|`System.Runtime` | `time-in-gc` |
|`System.Runtime` | `gen-0-size` |
|`System.Runtime` | `gen-1-size` |
|`System.Runtime` | `gen-2-size` |
|`System.Runtime` | `loh-size` |
|`System.Runtime` | `alloc-rate` |
|`System.Runtime` | `assembly-count` |
|`System.Runtime` | `exception-count` |
|`System.Runtime` | `threadpool-thread-count` |
|`System.Runtime` | `monitor-lock-contention-count` |
|`System.Runtime` | `threadpool-queue-length` |
|`System.Runtime` | `threadpool-completed-items-count` |
|`System.Runtime` | `active-timer-count` |

> [!NOTE]
> A partir da versão 2.15.0-Beta3 do SDK do [AspNetCore](asp-net-core.md) ou do [SDK do WorkerService](worker-service.md), nenhum contador é coletado por padrão. O próprio módulo está habilitado, para que os usuários possam simplesmente adicionar os contadores desejados para coletá-los.

## <a name="customizing-counters-to-be-collected"></a>Personalizar os contadores a serem coletados

O exemplo a seguir mostra como adicionar/remover contadores. Essa personalização seria feita no método `ConfigureServices` do seu aplicativo depois que a coleção de telemetria do Application Insights ser habilitada usando `AddApplicationInsightsTelemetry()` ou `AddApplicationInsightsWorkerService()`. Veja a seguir um exemplo de código de um aplicativo ASP.NET Core. Para outros tipos de aplicativos, confira [este](worker-service.md#configuring-or-removing-default-telemetrymodules) documento.

```csharp
    using Microsoft.ApplicationInsights.Extensibility.EventCounterCollector;
    using Microsoft.Extensions.DependencyInjection;

    public void ConfigureServices(IServiceCollection services)
    {
        //... other code...

        // The following code shows how to configure the module to collect
        // additional counters.
        services.ConfigureTelemetryModule<EventCounterCollectionModule>(
            (module, o) =>
            {
                // This removes all default counters.
                module.Counters.Clear();

                // This adds a user defined counter "MyCounter" from EventSource named "MyEventSource"
                module.Counters.Add(new EventCounterCollectionRequest("MyEventSource", "MyCounter"));

                // This adds the system counter "gen-0-size" from "System.Runtime"
                module.Counters.Add(new EventCounterCollectionRequest("System.Runtime", "gen-0-size"));
            }
        );
    }
```

## <a name="disabling-eventcounter-collection-module"></a>Desabilitando o módulo de coleta EventCounter

`EventCounterCollectionModule` pode ser desabilitado usando o `ApplicationInsightsServiceOptions` . Um exemplo ao usar o SDK do ASP.NET Core é mostrado abaixo.

```csharp
    using Microsoft.ApplicationInsights.AspNetCore.Extensions;
    using Microsoft.Extensions.DependencyInjection;

    public void ConfigureServices(IServiceCollection services)
    {
        //... other code...

        var applicationInsightsServiceOptions = new ApplicationInsightsServiceOptions();
        applicationInsightsServiceOptions.EnableEventCounterCollectionModule = false;
        services.AddApplicationInsightsTelemetry(applicationInsightsServiceOptions);
    }
```

Uma abordagem semelhante também pode ser usada para o SDK do WorkerService, mas o namespace deve ser alterado, conforme mostrado no exemplo abaixo.

```csharp
    using Microsoft.ApplicationInsights.WorkerService;
    using Microsoft.Extensions.DependencyInjection;

    var applicationInsightsServiceOptions = new ApplicationInsightsServiceOptions();
    applicationInsightsServiceOptions.EnableEventCounterCollectionModule = false;
    services.AddApplicationInsightsTelemetryWorkerService(applicationInsightsServiceOptions);
```

## <a name="event-counters-in-metric-explorer"></a>Contadores de eventos no Metric Explorer

Para exibir as métricas do EventCounter no [Metric Explorer](../platform/metrics-charts.md), selecione o recurso Application Insights e escolha Métricas baseadas em log como namespace da métrica. Em seguida, as métricas EventCounter são exibidas em Categoria personalizada.

> [!div class="mx-imgBorder"]
> ![Contadores de eventos relatados no Gerenciador de métricas Application Insights](./media/event-counters/metrics-explorer-counter-list.png)

## <a name="event-counters-in-analytics"></a>Contadores de evento no Analytics

Você também pode pesquisar e exibir relatórios de contador de eventos no [Analytics](../log-query/log-query-overview.md), na tabela **customMetrics**.

Por exemplo, execute a consulta a seguir para ver quais contadores são coletados e estão disponíveis para consulta:

```Kusto
customMetrics | summarize avg(value) by name
```

> [!div class="mx-imgBorder"]
> ![Contadores de eventos relatados na análise de Application Insights](./media/event-counters/analytics-event-counters.png)

Para obter um gráfico de um contador específico (por exemplo: `ThreadPool Completed Work Item Count`) no período recente, execute a consulta a seguir.

```Kusto
customMetrics 
| where name contains "System.Runtime|ThreadPool Completed Work Item Count"
| where timestamp >= ago(1h)
| summarize  avg(value) by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```
> [!div class="mx-imgBorder"]
> ![Chat de um único contador no Application Insights](./media/event-counters/analytics-completeditems-counters.png)

Como outras telemetrias, o **customMetrics** também tem uma coluna `cloud_RoleInstance` que indica a identidade da instância do servidor host no qual seu aplicativo está sendo executado. A consulta acima mostra o valor do contador por instância e pode ser usada para comparar o desempenho de diferentes instâncias de servidor.

## <a name="alerts"></a>Alertas
Assim como ocorre com outras métricas, você pode [definir um alerta](../platform/alerts-log.md) para avisar se um contador de evento fica fora de um limite especificado. Abra o painel Alertas e clique em Adicionar Alerta.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="can-i-see-eventcounters-in-live-metrics"></a>Posso ver o EventCounters no Live Metrics?

O Live Metrics não mostra o EventCounters a partir de hoje. Use o Metric Explorer ou o Analytics para ver a telemetria.

### <a name="i-have-enabled-application-insights-from-azure-web-app-portal-but-i-cant-see-eventcounters"></a>Habilitei o Application Insights no portal de aplicativo Web do Azure. Mas não consigo ver o EventCounters?

 A [extensão do Application Insights](./azure-web-apps.md) para ASP.NET Core ainda não é compatível com esse recurso. Este documento será atualizado quando esse recurso for compatível.

## <a name="next-steps"></a><a name="next"></a>Próximas etapas

* [Acompanhamento de dependência](./asp-net-dependencies.md)

