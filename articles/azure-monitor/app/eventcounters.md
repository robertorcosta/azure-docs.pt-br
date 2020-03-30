---
title: Contadores de eventos em Insights de Aplicativos | Microsoft Docs
description: Monitore o sistema e os eventos principais .NET/.NET personalizados no Application Insights.
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: 2094c012e86131073fc66be4f2ac2fb2e81ef4c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663582"
---
# <a name="eventcounters-introduction"></a>Introdução eventCounters

`EventCounter`é o mecanismo .NET/.NET Core para publicar e consumir contadores ou estatísticas. [Este](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.Tracing/documentation/EventCounterTutorial.md) documento dá uma `EventCounters` visão geral e exemplos de como publicá-los e consumi-los. EventCounters são suportados em todas as plataformas de SO - Windows, Linux e macOS. Ele pode ser considerado como um equivalente multiplataforma para os [Contadores de Desempenho](https://docs.microsoft.com/dotnet/api/system.diagnostics.performancecounter) que só é suportado em sistemas Windows.

Embora os usuários `EventCounters` possam publicar qualquer costume para atender às suas necessidades, o tempo de execução do .NET Core 3.0 publica um conjunto desses contadores por padrão. O documento percorrerá as etapas `EventCounters` necessárias para coletar e visualizar (sistema definido ou definido pelo usuário) no Azure Application Insights.

## <a name="using-application-insights-to-collect-eventcounters"></a>Usando insights de aplicativos para coletar contadores de eventos

O Application Insights `EventCounters` suporta `EventCounterCollectionModule`a coleta com o seu , que faz parte do recém-lançado pacote nuget [Microsoft.ApplicationInsights.EventCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventCounterCollector). `EventCounterCollectionModule`é ativado automaticamente ao usar [o AspNetCore](asp-net-core.md) ou [o WorkerService](worker-service.md). `EventCounterCollectionModule`coleta contadores com uma frequência de coleta não configurável de 60 segundos. Não há permissões especiais necessárias para coletar EventCounters.

## <a name="default-counters-collected"></a>Contadores padrão coletados

Para aplicativos em execução no .NET Core 3.0, os seguintes contadores são coletados automaticamente pelo SDK. O nome dos contadores será da forma "Categoria| Contador".

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
|`Microsoft.AspNetCore.Hosting` | `requests-per-second` |
|`Microsoft.AspNetCore.Hosting` | `total-requests` |
|`Microsoft.AspNetCore.Hosting` | `current-requests` |
|`Microsoft.AspNetCore.Hosting` | `failed-requests` |

> [!NOTE]
> Contadores da categoria Microsoft.AspNetCore.Hosting só são adicionados em ASP.NET Principais Aplicativos.

## <a name="customizing-counters-to-be-collected"></a>Personalização de contadores a serem coletados

O exemplo a seguir mostra como adicionar/remover contadores. Essa personalização seria feita `ConfigureServices` no método do seu aplicativo depois que a coleta `AddApplicationInsightsTelemetry()` `AddApplicationInsightsWorkerService()`de telemetria do Application Insights estiver ativada usando ou . A seguir está um código de exemplo de um aplicativo ASP.NET Core. Para outros tipos de aplicações, consulte [este](worker-service.md#configuring-or-removing-default-telemetrymodules) documento.

```csharp
    using Microsoft.ApplicationInsights.Extensibility.EventCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        //... other code...

        // The following code shows several customizations done to EventCounterCollectionModule.
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

        // The following code removes EventCounterCollectionModule to disable the module completely.
        var eventCounterModule = services.FirstOrDefault<ServiceDescriptor>
                    (t => t.ImplementationType == typeof(EventCounterCollectionModule));
        if (eventCounterModule != null)
        {
            services.Remove(eventCounterModule);
        }
    }
```

## <a name="event-counters-in-metric-explorer"></a>Contadores de eventos no Metric Explorer

Para exibir as métricas eventCounter no [Metric Explorer,](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts)selecione o recurso Application Insights e escolha métricas baseadas em Log como espaço de nome métrico. Em seguida, as métricas EventCounter são exibidas na categoria Personalizado.

> [!div class="mx-imgBorder"]
> ![Contadores de eventos relatados no Application Insights](./media/event-counters/metrics-explorer-counter-list.png)

## <a name="event-counters-in-analytics"></a>Contadores de eventos em Analytics

Você também pode pesquisar e exibir relatórios de contador de eventos no [Analytics,](../../azure-monitor/app/analytics.md)na tabela **customMetrics.**

Por exemplo, execute a seguinte consulta para ver quais contadores são coletados e disponíveis para consulta:

```Kusto
customMetrics | summarize avg(value) by name
```

> [!div class="mx-imgBorder"]
> ![Contadores de eventos relatados no Application Insights](./media/event-counters/analytics-event-counters.png)

Para obter um gráfico de um `ThreadPool Completed Work Item Count`contador específico (por exemplo: ) durante o período recente, execute a seguinte consulta.

```Kusto
customMetrics 
| where name contains "System.Runtime|ThreadPool Completed Work Item Count"
| where timestamp >= ago(1h)
| summarize  avg(value) by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```
> [!div class="mx-imgBorder"]
> ![Chat de um único contador no Application Insights](./media/event-counters/analytics-completeditems-counters.png)

Como outras telemetrias, a `cloud_RoleInstance` **customMetrics** também tem uma coluna que indica a identidade da instância do servidor host em que seu aplicativo está sendo executado. A consulta acima mostra o contravalor por instância e pode ser usada para comparar o desempenho de diferentes instâncias do servidor.

## <a name="alerts"></a>Alertas
Como outras métricas, você pode [definir um alerta](../../azure-monitor/app/alerts.md) para avisá-lo se um contador de eventos estiver fora de um limite especificado. Abra o painel Alertas e clique em Adicionar Alerta.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="can-i-see-eventcounters-in-live-metrics"></a>Posso ver EventCounters em Métricas ao vivo?

Métricas ao vivo não mostram EventCounters a partir de hoje. Use O Metric Explorer ou o Analytics para ver a telemetria.

### <a name="which-platforms-can-i-see-the-default-list-of-net-core-30-counters"></a>Quais plataformas posso ver a lista padrão de contadores .NET Core 3.0?

EventCounter não requer permissões especiais e é suportado em todas as plataformas .NET Core 3.0 é suportado. Isso inclui:

* **Sistema operacional**: Windows, Linux ou macOS.
* **Método de hospedagem**: Em processo ou fora de processo.
* **Método de implantação**: Quadro dependente ou autônomo.
* **Servidor web**: IIS (Internet Information Server) ou Kestrel.
* **Plataforma de hospedagem**: O recurso Web Apps do Azure App Service, Azure VM, Docker, Azure Kubernetes Service (AKS), e assim por diante.

### <a name="i-have-enabled-application-insights-from-azure-web-app-portal-but-i-cant-see-eventcounters"></a>Habilitei o Application Insights do Portal do Aplicativo Web do Azure. Mas eu não posso ver EventCounters.?

 [A extensão do Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps) para ASP.NET Core ainda não suporta esse recurso. Este documento será atualizado quando esse recurso for suportado.

## <a name="next-steps"></a><a name="next"></a>Próximas etapas

* [Acompanhamento de dependência](../../azure-monitor/app/asp-net-dependencies.md)
