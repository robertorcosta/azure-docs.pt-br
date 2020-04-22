---
title: Acompanhamento de dependência no Azure Application Insights | Microsoft Docs
description: Monitore chamadas de dependência de seus locais ou do aplicativo web Microsoft Azure com o Application Insights.
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: 1e30d8036c1fc624d39f027f38e314c6c57360f6
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731501"
---
# <a name="dependency-tracking-in-azure-application-insights"></a>Rastreamento de dependência em insights de aplicativos do Azure 

Uma *dependência* é um componente externo que é chamado pelo seu aplicativo. Normalmente, ele é um serviço chamado usando HTTP, um banco de dados ou um sistema de arquivos. [O Application Insights](../../azure-monitor/app/app-insights-overview.md) mede a duração das chamadas de dependência, falhando ou não, juntamente com informações adicionais como nome de dependência e assim por diante. Você pode investigar chamadas de dependência específicas e correlacioná-las com solicitações e exceções.

## <a name="automatically-tracked-dependencies"></a>Dependências rastreadas automaticamente

O Application Insights SDKs para naves `DependencyTrackingTelemetryModule` .NET e .NET Core com as quais é um Módulo de Telemetria que coleta automaticamente dependências. Essa coleção de dependência é habilitada automaticamente para [aplicativos ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) e [ASP.NET Core,](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) quando configurada de acordo com os documentos oficiais vinculados. `DependencyTrackingTelemetryModule` é enviado como [este](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) pacote NuGet, e é trazido automaticamente ao `Microsoft.ApplicationInsights.Web` `Microsoft.ApplicationInsights.AspNetCore`usar qualquer um dos pacotes NuGet ou .

 `DependencyTrackingTelemetryModule`atualmente, rastreia as seguintes dependências automaticamente:

|Dependências |Detalhes|
|---------------|-------|
|Http/Https | Chamadas locais ou remotas http/https |
|Chamadas wcf| Somente rastreado automaticamente se as ligações baseadas em Http forem usadas.|
|SQL | Chamadas feitas `SqlClient`com . Consulte [isso](#advanced-sql-tracking-to-get-full-sql-query) para capturar consulta SQL.  |
|[Armazenamento azure (Blob, Table, Fila)](https://www.nuget.org/packages/WindowsAzure.Storage/) | Chamadas feitas com o Cliente de Armazenamento Azure. |
|[Cliente EventHub SDK](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | Versão 1.1.0 ou superior. |
|[SDK do Cliente do Barramento de Serviço](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| Versão 3.0.0 e superior. |
|Azure Cosmos DB | Somente rastreado automaticamente se HTTP/HTTPS for usado. O modo TCP não será capturado pelo Application Insights. |

Se você estiver perdendo uma dependência, ou usando um SDK diferente, certifique-se de que está na lista de [dependências coletadas automaticamente](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies). Se a dependência não for coletada automaticamente, você ainda pode rastreá-la manualmente com uma [chamada de dependência de faixa](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency).

## <a name="setup-automatic-dependency-tracking-in-console-apps"></a>Configurar o rastreamento automático de dependência em aplicativos de console

Para rastrear automaticamente as dependências dos aplicativos de `Microsoft.ApplicationInsights.DependencyCollector`console .NET, instale o pacote Nuget e inicialize `DependencyTrackingTelemetryModule` da seguinte forma:

```csharp
    DependencyTrackingTelemetryModule depModule = new DependencyTrackingTelemetryModule();
    depModule.Initialize(TelemetryConfiguration.Active);
```

Para aplicativos de console .NET Core, TelemettryConfiguration.Active é obsoleto. Consulte a orientação na [documentação](https://docs.microsoft.com/azure/azure-monitor/app/worker-service) do serviço do trabalhador e a [documentação de monitoramento do Núcleo ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)

### <a name="how-automatic-dependency-monitoring-works"></a>Como funciona o monitoramento automático da dependência?

As dependências são coletadas automaticamente usando uma das seguintes técnicas:

* Usando instrumentação de código de byte em torno de métodos selecionados. (InstrumentationEngine, do StatusMonitor ou do Azure Web App Extension)
* Retornos de chamada eventSource
* Retornos de chamada diagnosticSource (nos SDKs do núcleo .NET/.NET mais recentes)

## <a name="manually-tracking-dependencies"></a>Rastreamento manual de dependências

A seguir, alguns exemplos de dependências, que não são coletados automaticamente e, portanto, requerem rastreamento manual.

* O Azure Cosmos DB é acompanhado automaticamente somente se [HTTP/HTTPS](../../cosmos-db/performance-tips.md#networking) é usado. O modo TCP não será capturado pelo Application Insights.
* Redis

Para essas dependências não coletadas automaticamente pelo SDK, você pode rastreá-las manualmente usando a [API TrackDependency](api-custom-events-metrics.md#trackdependency) que é usada pelos módulos de coleta automática padrão.

Por exemplo, se você criar seu código com um assembly que não escreveu, poderá determinar o tempo de todas as chamadas nele, para descobrir qual sua contribuição aos tempos de resposta. Para que esses dados sejam exibidos nos gráficos de dependência no Application Insights, envie-os usando `TrackDependency`.

```csharp

    var startTime = DateTime.UtcNow;
    var timer = System.Diagnostics.Stopwatch.StartNew();
    try
    {
        // making dependency call
        success = dependency.Call();
    }
    finally
    {
        timer.Stop();
        telemetryClient.TrackDependency("myDependencyType", "myDependencyCall", "myDependencyData",  startTime, timer.Elapsed, success);
    }
```

Alternativamente, `TelemetryClient` fornece métodos `StopOperation` de extensão e que podem ser usados `StartOperation` para rastrear manualmente dependências, como mostrado [aqui](custom-operations-tracking.md#outgoing-dependencies-tracking)

Se você quiser desligar o módulo de rastreamento de dependência padrão, remova a referência ao DependencyTrackingTelemetryModule no [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) para aplicativos ASP.NET. Para ASP.NET aplicações Core, siga as instruções [aqui](asp-net-core.md#configuring-or-removing-default-telemetrymodules).

## <a name="tracking-ajax-calls-from-web-pages"></a>Rastreamento de chamadas AJAX de páginas da Web

Para páginas da Web, o Application Insights JavaScript SDK coleta automaticamente chamadas AJAX como dependências.

## <a name="advanced-sql-tracking-to-get-full-sql-query"></a>Rastreamento avançado de SQL para obter consulta SQL completa

Para chamadas SQL, o nome do servidor e do banco `DependencyTelemetry`de dados é sempre coletado e armazenado como nome do coletado . Há um campo adicional chamado 'dados', que pode conter o texto completo de consulta SQL.

Para ASP.NET aplicativos Core, não há nenhuma etapa adicional necessária para obter a consulta SQL completa.

Para aplicações ASP.NET, a consulta SQL completa é coletada com a ajuda da instrumentação de código byte, que requer o mecanismo de instrumentação. Etapas adicionais específicas da plataforma, conforme descrito abaixo, são necessárias.

| Plataforma | Etapa(s) Necessária para obter consulta SQL completa |
| --- | --- |
| Aplicativo Web do Azure |No painel de controle do aplicativo web, [abra a lâmina Application Insights](../../azure-monitor/app/azure-web-apps.md) e habilite os comandos SQL em .NET |
| Servidor IIS (Azure VM, on-prem, e assim por diante.) | Use o Módulo PowerShell do Monitor de Status para [instalar o Mecanismo de Instrumentação](../../azure-monitor/app/status-monitor-v2-api-reference.md) e reiniciar o IIS. |
| Serviço de Nuvem do Azure | Adicionar [tarefa de inicialização para instalar o StatusMonitor](../../azure-monitor/app/cloudservices.md#set-up-status-monitor-to-collect-full-sql-queries-optional) <br> Seu aplicativo deve estar a bordo do ApplicationInsights SDK no momento da compilação, instalando pacotes NuGet para [aplicativos ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) ou [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) |
| IIS Express | Sem suporte

Nos casos acima, a maneira correta de validar que o mecanismo de instrumentação está corretamente `DependencyTelemetry` instalado é validando que a versão SDK do coletado é 'rddp'. 'rdddsd' ou 'rddf' indica que as dependências são coletadas via DiagnosticSource ou EventSource callbacks e, portanto, a consulta completa do SQL não será capturada.

## <a name="where-to-find-dependency-data"></a>Onde encontrar dados de dependência

* O [Mapa do Aplicativo](app-map.md) visualiza as dependências entre seu aplicativo e os componentes de vizinhança.
* [O Transaction Diagnostics](transaction-diagnostics.md) mostra dados de servidor unificados e correlacionados.
* [A guia navegadores](javascript.md) mostra chamadas AJAX dos navegadores de seus usuários.
* Clique pelas solicitações com falha ou lentas para verificar a dependência de chamadas.
* O [Analytics](#logs-analytics) pode ser usado para consultar dados de dependência.

## <a name="diagnose-slow-requests"></a><a name="diagnosis"></a> Diagnosticar solicitações lentas

Cada evento de solicitação está associado às chamadas de dependência, exceções e outros eventos que são rastreados enquanto seu aplicativo está processando a solicitação. Então, se alguns pedidos estão indo mal, você pode descobrir se é por causa de respostas lentas de uma dependência.

### <a name="tracing-from-requests-to-dependencies"></a>Rastreamento de solicitações de dependências

Abra a guia **Desempenho** e navegue até a guia **Dependências** na parte superior ao lado das operações.

Clique em um **nome de dependência** em geral. Depois de selecionar uma dependência, um gráfico da distribuição de durações dessa dependência aparecerá à direita.

![Na guia desempenho clique na guia Dependência na parte superior e depois um nome de dependência no gráfico](./media/asp-net-dependencies/2-perf-dependencies.png)

Clique no botão azul **Amostras** no canto inferior direito e, em seguida, em uma amostra para ver os detalhes da transação de ponta a ponta.

![Clique em uma amostra para ver os detalhes da transação de ponta a ponta](./media/asp-net-dependencies/3-end-to-end.png)

### <a name="profile-your-live-site"></a>Perfil de seu site ativo

Não sabe para onde o tempo vai? O [perfil do Application Insights](../../azure-monitor/app/profiler.md) rastreia chamadas HTTP para o seu site ao vivo e mostra as funções em seu código que levaram mais tempo.

## <a name="failed-requests"></a>Solicitações falhas

As solicitações com falha também podem ser associadas a chamadas com falha para as dependências.

Podemos ir para a guia **Falhas** à esquerda e, em seguida, clicar na guia **dependências** na parte superior.

![Clique no gráfico de solicitações com falha](./media/asp-net-dependencies/4-fail.png)

Aqui você poderá ver a contagem de dependência sumida. Para obter mais detalhes sobre uma ocorrência falha tentando clicar em um nome de dependência na tabela inferior. Você pode clicar no botão **independências** azuis no canto inferior direito para obter os detalhes de transação de ponta a ponta.

## <a name="logs-analytics"></a>Logs (Análise)

Você pode rastrear dependências na [linguagem de consulta Kusto](/azure/kusto/query/). Veja alguns exemplos.

* Localize todas as chamadas com falha de dependência:

``` Kusto

    dependencies | where success != "True" | take 10
```

* Localize as chamadas AJAX:

``` Kusto

    dependencies | where client_Type == "Browser" | take 10
```

* Localize as chamadas de dependência associadas a solicitações:

``` Kusto

    dependencies
    | where timestamp > ago(1d) and  client_Type != "Browser"
    | join (requests | where timestamp > ago(1d))
      on operation_Id  
```


* Localize as chamadas do AJAX associadas a exibições de página:

``` Kusto 

    dependencies
    | where timestamp > ago(1d) and  client_Type == "Browser"
    | join (browserTimings | where timestamp > ago(1d))
      on operation_Id
```

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="how-does-automatic-dependency-collector-report-failed-calls-to-dependencies"></a>*Como o coletor automático de dependência relata chamadas falhadas para dependências?*

* Chamadas de dependência com falha terão campo de 'sucesso' definido como False. `DependencyTrackingTelemetryModule`não relata `ExceptionTelemetry`. O modelo completo de dados para dependência é descrito [aqui](data-model-dependency-telemetry.md).

## <a name="open-source-sdk"></a>SDK do código-fonte aberto
Como todo SDK do Application Insights, o módulo de coleta de dependência também é de código aberto. Leia e contribua para o código ou reporte problemas [no repo oficial do GitHub](https://github.com/Microsoft/ApplicationInsights-dotnet-server).

## <a name="next-steps"></a>Próximas etapas

* [Exceções](../../azure-monitor/app/asp-net-exceptions.md)
* [Dados do usuário e da página](../../azure-monitor/app/javascript.md)
* [Disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md)
