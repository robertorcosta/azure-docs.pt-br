---
title: Contadores de eventos no Application Insights | Microsoft Docs
description: Monitore o sistema e os EventCounters do .NET/.NET Core personalizados no Application Insights.
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: 37d0e1e741548986788be78860830f36add1f5a8
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83700440"
---
# <a name="eventcounters-introduction"></a>Introdução ao EventCounters

`EventCounter` é o mecanismo do .NET/.NET Core para publicar e consumir contadores ou estatísticas. [Este](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.Tracing/documentation/EventCounterTutorial.md) documento fornece uma visão geral de `EventCounters` e exemplos sobre como publicá-los e consumi-los. Os EventCounters têm suporte em todas as plataformas de sistema operacional: Windows, Linux e macOS. Pode ser pensado como um equivalente multiplataforma para o [PerformanceCounters](https://docs.microsoft.com/dotnet/api/system.diagnostics.performancecounter) que só tem suporte em sistemas Windows.

Embora os usuários possam publicar qualquer `EventCounters` personalizado para atender às suas necessidades, o runtime do .NET Core 3.0 publica um conjunto desses contadores por padrão. O documento percorrerá as etapas necessárias para coletar e exibir `EventCounters` (definido pelo sistema ou pelo usuário) no Azure Application Insights.

## <a name="using-application-insights-to-collect-eventcounters"></a>Usar o Application Insights para coletar EventCounters

O Application Insights é compatível com a coleta de `EventCounters` com seu `EventCounterCollectionModule`, que faz parte do pacote nuget recém-lançado [Microsoft.ApplicationInsights.EventCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventCounterCollector). `EventCounterCollectionModule` é habilitado automaticamente ao usar [AspNetCore](asp-net-core.md) ou [WorkerService](worker-service.md). `EventCounterCollectionModule` coleta contadores com uma frequência de coleta não configurável de 60 segundos. Não há permissões especiais necessárias para coletar EventCounters.

## <a name="default-counters-collected"></a>Contadores padrão coletados

Para aplicativos em execução no .NET Core 3.0, os contadores a seguir são coletados automaticamente pelo SDK. O nome dos contadores estará no formato "Categoria|Contador".

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
> Os contadores da categoria Microsoft.AspNetCore.Hosting são adicionados somente em aplicativos ASP.NET Core.

## <a name="customizing-counters-to-be-collected"></a>Personalizar os contadores a serem coletados

O exemplo a seguir mostra como adicionar/remover contadores. Essa personalização seria feita no método `ConfigureServices` do seu aplicativo depois que a coleção de telemetria do Application Insights ser habilitada usando `AddApplicationInsightsTelemetry()` ou `AddApplicationInsightsWorkerService()`. Veja a seguir um exemplo de código de um aplicativo ASP.NET Core. Para outros tipos de aplicativos, confira [este](worker-service.md#configuring-or-removing-default-telemetrymodules) documento.

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

Para exibir as métricas do EventCounter no [Metric Explorer](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts), selecione o recurso Application Insights e escolha Métricas baseadas em log como namespace da métrica. Em seguida, as métricas EventCounter são exibidas em Categoria personalizada.

> [!div class="mx-imgBorder"]
> ![Contadores de evento relatados no Application Insights](./media/event-counters/metrics-explorer-counter-list.png)

## <a name="event-counters-in-analytics"></a>Contadores de evento no Analytics

Você também pode pesquisar e exibir relatórios de contador de eventos no [Analytics](../../azure-monitor/app/analytics.md), na tabela **customMetrics**.

Por exemplo, execute a consulta a seguir para ver quais contadores são coletados e estão disponíveis para consulta:

```Kusto
customMetrics | summarize avg(value) by name
```

> [!div class="mx-imgBorder"]
> ![Contadores de evento relatados no Application Insights](./media/event-counters/analytics-event-counters.png)

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
Assim como ocorre com outras métricas, você pode [definir um alerta](../../azure-monitor/platform/alerts-log.md) para avisar se um contador de evento fica fora de um limite especificado. Abra o painel Alertas e clique em Adicionar Alerta.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="can-i-see-eventcounters-in-live-metrics"></a>Posso ver o EventCounters no Live Metrics?

O Live Metrics não mostra o EventCounters a partir de hoje. Use o Metric Explorer ou o Analytics para ver a telemetria.

### <a name="which-platforms-can-i-see-the-default-list-of-net-core-30-counters"></a>Em quais plataformas posso ver a lista padrão de contadores do .NET Core 3.0?

O EventCounter não exige nenhuma permissão especial e é compatível com todas as plataformas nas quais o .NET Core 3.0 tem suporte. Isso inclui:

* **Sistema operacional**: Windows, Linux ou macOS.
* **Método de hospedagem**: em andamento ou fora do processo.
* **Método de implantação**: dependente da estrutura ou autossuficiente.
* **Servidor Web**: IIS (Internet Information Server) ou Kestrel.
* **Plataforma de hospedagem**: o recurso de aplicativos Web do Serviço de Aplicativo do Azure, VM do Azure, Docker, AKS (Serviço Kubernetes do Azure) e assim por diante.

### <a name="i-have-enabled-application-insights-from-azure-web-app-portal-but-i-cant-see-eventcounters"></a>Habilitei o Application Insights no portal de aplicativo Web do Azure. Mas não consigo ver o EventCounters?

 A [extensão do Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps) para ASP.NET Core ainda não é compatível com esse recurso. Este documento será atualizado quando esse recurso for compatível.

## <a name="next-steps"></a><a name="next"></a>Próximas etapas

* [Acompanhamento de dependência](../../azure-monitor/app/asp-net-dependencies.md)
