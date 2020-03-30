---
title: Insights de aplicativos para aplicativos de serviço ao trabalhador (aplicativos não-HTTP)
description: Monitoramento de aplicativos .NET Core/.NET Framework não-HTTP com o Azure Monitor Application Insights.
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: 34a64ffa67b1a43a77391e0d50ddf1bfad0f73ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501166"
---
# <a name="application-insights-for-worker-service-applications-non-http-applications"></a>Insights de aplicativos para aplicativos de serviço ao trabalhador (aplicativos não HTTP)

O Application Insights está lançando um `Microsoft.ApplicationInsights.WorkerService`novo SDK, chamado , que é mais adequado para cargas de trabalho não-HTTP, como mensagens, tarefas em segundo plano, aplicativos de console etc. Esses tipos de aplicativos não têm a noção de uma solicitação HTTP recebida como um aplicativo Web tradicional ASP.NET/ASP.NET e, portanto, o uso de pacotes de Insights de aplicativos para [aplicativos ASP.NET](asp-net.md) ou [ASP.NET Core](asp-net-core.md) não é suportado.

O novo SDK não faz nenhuma coleção de telemetria por si só. Em vez disso, ele traz outros colecionadores de automóveis conhecidos do Application Insights, como [DependencyCollector,](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) [PerfCounterCollector,](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) [ApplicationInsightsLoggingProvider](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) etc. Este SDK expõe métodos `IServiceCollection` de extensão para ativar e configurar a coleta de telemetria.

## <a name="supported-scenarios"></a>Cenários com suporte

O [Application Insights SDK for Worker Service](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) é o mais adequado para aplicativos não-HTTP, não importa onde ou como eles são executados. Se o aplicativo estiver em execução e tiver conectividade de rede com o Azure, a telemetria pode ser coletada. O monitoramento do Application Insights é suportado em todos os lugares que o .NET Core é suportado. Este pacote pode ser usado no recém-introduzido [.NET Core 3.0 Worker Service](https://devblogs.microsoft.com/aspnet/dotnet-core-workers-in-azure-container-instances), [tarefas em segundo plano em Asp.Net Core 2.1/2.2,](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2)aplicativos de console (.NET Core/ .NET Framework), etc.

## <a name="prerequisites"></a>Pré-requisitos

Uma chave de instrumentação de Insights de aplicativo válida. Essa chave é necessária para enviar qualquer telemetria para o Application Insights. Se você precisar criar um novo recurso do Application Insights para obter uma chave de instrumentação, consulte [Criar um recurso do Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource).

## <a name="using-application-insights-sdk-for-worker-services"></a>Usando o SDK de insights de aplicativos para serviços ao trabalhador

1. Instale o pacote [Microsoft.ApplicationInsights.WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) no aplicativo.
   O trecho a seguir mostra as alterações que precisam `.csproj` ser adicionadas ao arquivo do seu projeto.

```xml
    <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.WorkerService" Version="2.13.1" />
    </ItemGroup>
```

1. Método `AddApplicationInsightsTelemetryWorkerService(string instrumentationKey)` de `IServiceCollection`extensão de chamada ligado, fornecendo a chave de instrumentação. Este método deve ser chamado no início da aplicação. A localização exata depende do tipo de aplicação.

1. Recupere `ILogger` uma `TelemetryClient` instância ou instância do recipiente DI `serviceProvider.GetRequiredService<TelemetryClient>();` (Dependency Injection) ligando ou usando a Injeção de Construtor. Esta etapa desencadeará `TelemetryConfiguration` a configuração de módulos de coleta automática.

Instruções específicas para cada tipo de aplicação são descritas nas seguintes seções.

## <a name="net-core-30-worker-service-application"></a>.NET Core 3.0 aplicativo de serviço ao trabalhador

O exemplo completo é compartilhado [aqui](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/WorkerServiceSampleWithApplicationInsights)

1. Baixe e instale [o .NET Core 3.0](https://dotnet.microsoft.com/download/dotnet-core/3.0)
2. Crie um novo projeto de Serviço ao Trabalhador usando o novo modelo de projeto do Visual Studio ou a linha de comando`dotnet new worker`
3. Instale o pacote [Microsoft.ApplicationInsights.WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) no aplicativo.

4. Adicione `services.AddApplicationInsightsTelemetryWorkerService();` ao `CreateHostBuilder()` método `Program.cs` em sua classe, como neste exemplo:

```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
                services.AddHostedService<Worker>();
                services.AddApplicationInsightsTelemetryWorkerService();
            });
```

5. Modifique `Worker.cs` o seu conforme o exemplo abaixo.

```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.DataContracts;

    public class Worker : BackgroundService
    {
        private readonly ILogger<Worker> _logger;
        private TelemetryClient _telemetryClient;
        private static HttpClient _httpClient = new HttpClient();

        public Worker(ILogger<Worker> logger, TelemetryClient tc)
        {
            _logger = logger;
            _telemetryClient = tc;
        }

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {
            while (!stoppingToken.IsCancellationRequested)
            {
                _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);

                using (_telemetryClient.StartOperation<RequestTelemetry>("operation"))
                {
                    _logger.LogWarning("A sample warning message. By default, logs with severity Warning or higher is captured by Application Insights");
                    _logger.LogInformation("Calling bing.com");
                    var res = await _httpClient.GetAsync("https://bing.com");
                    _logger.LogInformation("Calling bing completed with status:" + res.StatusCode);
                    _telemetryClient.TrackEvent("Bing call event completed");
                }

                await Task.Delay(1000, stoppingToken);
            }
        }
    }
```

6. Configure a chave de instrumentação.

    Embora você possa fornecer a chave `AddApplicationInsightsTelemetryWorkerService`de instrumentação como argumento para , recomendamos que você especifique a chave de instrumentação na configuração. A amostra de código a seguir mostra `appsettings.json`como especificar uma chave de instrumentação em . Certifique-se de que `appsettings.json` está copiado para a pasta raiz do aplicativo durante a publicação.

```json
    {
        "ApplicationInsights":
            {
            "InstrumentationKey": "putinstrumentationkeyhere"
            },
        "Logging":
        {
            "LogLevel":
            {
                "Default": "Warning"
            }
        }
    }
```

Alternativamente, especifique a chave de instrumentação em qualquer uma das variáveis do ambiente a seguir.
`APPINSIGHTS_INSTRUMENTATIONKEY` ou `ApplicationInsights:InstrumentationKey`

Por exemplo: `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`
Ou`SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

Normalmente, `APPINSIGHTS_INSTRUMENTATIONKEY` especifica a chave de instrumentação para aplicativos implantados em Web Apps como Web Jobs.

> [!NOTE]
> Uma chave de instrumentação especificada no `APPINSIGHTS_INSTRUMENTATIONKEY`código ganha sobre a variável ambiente , o que ganha sobre outras opções.

## <a name="aspnet-core-background-tasks-with-hosted-services"></a>ASP.NET tarefas de fundo do Core com serviços hospedados

[Este](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2&tabs=visual-studio) documento descreve como criar tarefas em segundo plano no aplicativo ASP.NET Core 2.1/2.2.

O exemplo completo é compartilhado [aqui](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/BackgroundTasksWithHostedService)

1. Instale o Microsoft.ApplicationInsights.WorkerService (pacotehttps://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) para o aplicativo.
2. Adicione `services.AddApplicationInsightsTelemetryWorkerService();` ao `ConfigureServices()` método, como neste exemplo:

```csharp
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .ConfigureAppConfiguration((hostContext, config) =>
            {
                config.AddJsonFile("appsettings.json", optional: true);
            })
            .ConfigureServices((hostContext, services) =>
            {
                services.AddLogging();
                services.AddHostedService<TimedHostedService>();

                // instrumentation key is read automatically from appsettings.json
                services.AddApplicationInsightsTelemetryWorkerService();
            })
            .UseConsoleLifetime()
            .Build();

        using (host)
        {
            // Start the host
            await host.StartAsync();

            // Wait for the host to shutdown
            await host.WaitForShutdownAsync();
        }
    }
```

A seguir está `TimedHostedService` o código para onde reside a lógica da tarefa de fundo.

```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.DataContracts;

    public class TimedHostedService : IHostedService, IDisposable
    {
        private readonly ILogger _logger;
        private Timer _timer;
        private TelemetryClient _telemetryClient;
        private static HttpClient httpClient = new HttpClient();

        public TimedHostedService(ILogger<TimedHostedService> logger, TelemetryClient tc)
        {
            _logger = logger;
            this._telemetryClient = tc;
        }

        public Task StartAsync(CancellationToken cancellationToken)
        {
            _logger.LogInformation("Timed Background Service is starting.");

            _timer = new Timer(DoWork, null, TimeSpan.Zero,
                TimeSpan.FromSeconds(1));

            return Task.CompletedTask;
        }

        private void DoWork(object state)
        {
            _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);

            using (_telemetryClient.StartOperation<RequestTelemetry>("operation"))
            {
                _logger.LogWarning("A sample warning message. By default, logs with severity Warning or higher is captured by Application Insights");
                _logger.LogInformation("Calling bing.com");
                var res = await httpClient.GetAsync("https://bing.com");
                _logger.LogInformation("Calling bing completed with status:" + res.StatusCode);
                telemetryClient.TrackEvent("Bing call event completed");
            }
        }
    }
```

3. Configure a chave de instrumentação.
   Use o `appsettings.json` mesmo no exemplo do .NET Core 3.0 Worker Service acima.

## <a name="net-corenet-framework-console-application"></a>Aplicativo do console .NET Core/.NET Framework

Como mencionado no início deste artigo, o novo pacote pode ser usado para habilitar a telemetria de insights de aplicativos até mesmo de um aplicativo de console regular. Este pacote [`NetStandard2.0`](https://docs.microsoft.com/dotnet/standard/net-standard)tem como alvo , e, portanto, pode ser usado para aplicativos de console em .NET Core 2.0 ou superior, e .NET Framework 4.7.2 ou superior.

O exemplo completo é compartilhado [aqui](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/ConsoleAppWithApplicationInsights)

1. Instale o Microsoft.ApplicationInsights.WorkerService (pacotehttps://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) para o aplicativo.

2. Modifique Program.cs como exemplo abaixo.

```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.DataContracts;
    using Microsoft.Extensions.DependencyInjection;
    using Microsoft.Extensions.Logging;
    using System;
    using System.Net.Http;
    using System.Threading.Tasks;

    namespace WorkerSDKOnConsole
    {
        class Program
        {
            static async Task Main(string[] args)
            {
                // Create the DI container.
                IServiceCollection services = new ServiceCollection();

                // Being a regular console app, there is no appsettings.json or configuration providers enabled by default.
                // Hence instrumentation key and any changes to default logging level must be specified here.
                services.AddLogging(loggingBuilder => loggingBuilder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>("Category", LogLevel.Information));
                services.AddApplicationInsightsTelemetryWorkerService("instrumentationkeyhere");

                // Build ServiceProvider.
                IServiceProvider serviceProvider = services.BuildServiceProvider();

                // Obtain logger instance from DI.
                ILogger<Program> logger = serviceProvider.GetRequiredService<ILogger<Program>>();

                // Obtain TelemetryClient instance from DI, for additional manual tracking or to flush.
                var telemetryClient = serviceProvider.GetRequiredService<TelemetryClient>();

                var httpClient = new HttpClient();

                while (true) // This app runs indefinitely. replace with actual application termination logic.
                {
                    logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);

                    // Replace with a name which makes sense for this operation.
                    using (telemetryClient.StartOperation<RequestTelemetry>("operation"))
                    {
                        logger.LogWarning("A sample warning message. By default, logs with severity Warning or higher is captured by Application Insights");
                        logger.LogInformation("Calling bing.com");                    
                        var res = await httpClient.GetAsync("https://bing.com");
                        logger.LogInformation("Calling bing completed with status:" + res.StatusCode);
                        telemetryClient.TrackEvent("Bing call event completed");
                    }

                    await Task.Delay(1000);
                }

                // Explicitly call Flush() followed by sleep is required in Console Apps.
                // This is to ensure that even if application terminates, telemetry is sent to the back-end.
                telemetryClient.Flush();
                Task.Delay(5000).Wait();
            }
        }
    }
```

Este aplicativo de console `TelemetryConfiguration`também usa o mesmo padrão , e pode ser personalizado da mesma forma que os exemplos na seção anterior.

## <a name="run-your-application"></a>Execute seu aplicativo.

Execute seu aplicativo. O exemplo de trabalhadores de todos os acima acima faz uma chamada http a cada segundo para bing.com, e também emite poucos logs usando o ILogger. Essas linhas são `StartOperation` embrulhadas dentro da chamada , `TelemetryClient`que `RequestTelemetry` é usada para criar uma operação (neste exemplo chamada "operação"). Os Insights do aplicativo coletarão esses registros do ILogger (aviso ou acima por `RequestTelemetry` padrão) e dependências, e eles estarão correlacionados com a relação pai-filho. A correlação também funciona no limite transversal do processo/rede. Por exemplo, se a chamada foi feita para outro componente monitorado, então ela também será correlacionada com este pai.

Esta operação `RequestTelemetry` personalizada pode ser considerada como o equivalente a uma solicitação web recebida em um aplicativo web típico. Embora não seja necessário usar uma Operação, ela se encaixa melhor `RequestTelemetry` com o modelo de dados de [correlação Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/correlation) - com a atuação como operação pai, e toda telemetria gerada dentro da iteração do trabalhador sendo tratada como logicamente pertencente à mesma operação. Essa abordagem também garante que toda a telemetria `operation_id`gerada (automática e manual) terá a mesma . Como a amostragem é baseada em `operation_id`, algoritmo de amostragem mantém ou descarta toda a telemetria de uma única iteração.

A seguir, lista a telemetria completa coletada automaticamente pelo Application Insights.

### <a name="live-metrics"></a>Métricas ao vivo

[Métricas ao vivo](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream) podem ser usadas para verificar rapidamente se o monitoramento do Application Insights está configurado corretamente. Embora possa levar alguns minutos até que a telemetria comece a aparecer no portal e análises, o Live Metrics mostraria o uso da CPU do processo de execução em quase tempo real. Ele também pode mostrar outras telemetrias como Solicitações, Dependências, Traços etc.

### <a name="ilogger-logs"></a>Registros iLogger

Os registros emitidos por `ILogger` `Warning` gravidade ou superior são automaticamente capturados. Siga [os docs do ILogger](ilogger.md#control-logging-level) para personalizar quais níveis de log são capturados pelo Application Insights.

### <a name="dependencies"></a>Dependências

A coleta de dependência é ativada por padrão. [Este](asp-net-dependencies.md#automatically-tracked-dependencies) artigo explica as dependências que são coletadas automaticamente, e também contém etapas para fazer o rastreamento manual.

### <a name="eventcounter"></a>Contador de Eventos

`EventCounterCollectionModule`é ativado por padrão e coletará um conjunto padrão de contadores de aplicativos .NET Core 3.0. O tutorial [EventCounter](eventcounters.md) lista o conjunto padrão de contadores coletados. Ele também tem instruções sobre a personalização da lista.

### <a name="manually-tracking-additional-telemetry"></a>Rastreamento manual de telemetria adicional

Enquanto o SDK coleta automaticamente a telemetria, como explicado acima, na maioria dos casos o usuário precisará enviar telemetria adicional para o serviço Application Insights. A maneira recomendada de rastrear telemetria adicional `TelemetryClient` é obtendo uma instância de Injeção `TrackXXX()` de Dependência e, em seguida, chamando um dos métodos [de API](api-custom-events-metrics.md) suportados sobre ele. Outro caso típico de uso é [o rastreamento personalizado das operações.](custom-operations-tracking.md) Essa abordagem é demonstrada nos exemplos do Trabalhador acima.

## <a name="configure-the-application-insights-sdk"></a>Configure o Application Insights SDK

O `TelemetryConfiguration` padrão usado pelo SDK de serviço do trabalhador é semelhante à configuração automática usada em um aplicativo ASP.NET ou `HttpContext`ASP.NET Core, menos os TelemetriaInicializers usados para enriquecer a telemetria de .

Você pode personalizar o Application Insights SDK for Worker Service para alterar a configuração padrão. Os usuários do Application Insights ASP.NET O SDK Central pode estar familiarizado com a mudança de configuração usando ASP.NET injeção de [dependência](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)incorporada do Core . O WorkerService SDK também é baseado em princípios semelhantes. Faça quase todas as `ConfigureServices()` alterações de configuração na seção ligando para métodos `IServiceCollection`apropriados, conforme detalhado abaixo.

> [!NOTE]
> Ao usar este SDK, a `TelemetryConfiguration.Active` alteração da configuração modificando não é suportada e as alterações não serão refletidas.

### <a name="using-applicationinsightsserviceoptions"></a>Usando opções de serviço do ApplicationInsights

Você pode modificar algumas configurações `ApplicationInsightsServiceOptions` `AddApplicationInsightsTelemetryWorkerService`comuns passando para , como neste exemplo:

```csharp
    using Microsoft.ApplicationInsights.WorkerService;

    public void ConfigureServices(IServiceCollection services)
    {
        Microsoft.ApplicationInsights.WorkerService.ApplicationInsightsServiceOptions aiOptions
                    = new Microsoft.ApplicationInsights.WorkerService.ApplicationInsightsServiceOptions();
        // Disables adaptive sampling.
        aiOptions.EnableAdaptiveSampling = false;

        // Disables QuickPulse (Live Metrics stream).
        aiOptions.EnableQuickPulseMetricStream = false;
        services.AddApplicationInsightsTelemetryWorkerService(aiOptions);
    }
```

Observe `ApplicationInsightsServiceOptions` que neste SDK está `Microsoft.ApplicationInsights.WorkerService` no namespace ao contrário `Microsoft.ApplicationInsights.AspNetCore.Extensions` do ASP.NET Core SDK.

Configurações comumente usadas em`ApplicationInsightsServiceOptions`

|Configuração | Descrição | Padrão
|---------------|-------|-------
|Habilite o QuickPulseMetricStream | Ativar/desativar o recurso LiveMetrics | true
|HabilitaraaaaAmostragem Adaptativa | Ativar/desativar amostragem adaptativa | true
|Habilitarheartbeat | Habilitar/desativar o recurso Heartbeats, que periodicamente (15 min padrão) envia uma métrica personalizada chamada 'HeartBeatState' com informações sobre o tempo de execução como .NET Version, Azure Environment information, se aplicável, etc. | true
|Adicionarautométrico coletado automático | Ativar/desativar o extrator AutoCollectedMetrics, que é um processador de telemetria que envia métricas pré-agregadas sobre Solicitações/Dependências antes que a amostragem ocorra. | true

Consulte as [ `ApplicationInsightsServiceOptions` configurações configuráveis](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/NETCORE/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs) para obter a lista mais atualizada.

### <a name="sampling"></a>amostragem

O Application Insights SDK for Worker Service suporta amostragem de taxa fixa e adaptativa. A amostragem adaptativa é ativada por padrão. A configuração da amostragem para o Serviço do Trabalhador é feita da mesma forma que para [ASP.NET Aplicativos Principais](https://docs.microsoft.com/azure/azure-monitor/app/sampling#configuring-adaptive-sampling-for-aspnet-core-applications).

### <a name="adding-telemetryinitializers"></a>Adicionando telemetriainicializador

Use [iniciadores de telemetria](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#addmodify-properties-itelemetryinitializer) quando quiser definir propriedades enviadas com toda a telemetria.

Adicione qualquer `TelemetryInitializer` novidade `DependencyInjection` ao recipiente e o SDK `TelemetryConfiguration`irá adicioná-los automaticamente ao .

```csharp
    using Microsoft.ApplicationInsights.Extensibility;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
        services.AddApplicationInsightsTelemetryWorkerService();
    }
```

### <a name="removing-telemetryinitializers"></a>Removendo iniciadores de telemetria

Iniciadores de telemetria estão presentes por padrão. Para remover todos ou iniciadores específicos de telemetria, `AddApplicationInsightsTelemetryWorkerService()`use o seguinte código de amostra *após* a chamada .

```csharp
   public void ConfigureServices(IServiceCollection services)
   {
        services.AddApplicationInsightsTelemetryWorkerService();
        // Remove a specific built-in telemetry initializer
        var tiToRemove = services.FirstOrDefault<ServiceDescriptor>
                            (t => t.ImplementationType == typeof(AspNetCoreEnvironmentTelemetryInitializer));
        if (tiToRemove != null)
        {
            services.Remove(tiToRemove);
        }

        // Remove all initializers
        // This requires importing namespace by using Microsoft.Extensions.DependencyInjection.Extensions;
        services.RemoveAll(typeof(ITelemetryInitializer));
   }
```

### <a name="adding-telemetry-processors"></a>Adicionando processadores de telemetria

Você pode adicionar processadores de `TelemetryConfiguration` telemetria `AddApplicationInsightsTelemetryProcessor` personalizados usando o método de extensão em `IServiceCollection`. Você usa processadores de telemetria em [cenários avançados de filtragem](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#itelemetryprocessor-and-itelemetryinitializer) para permitir um controle mais direto sobre o que está incluído ou excluído da telemetria enviada ao serviço Application Insights. Use o exemplo a seguir.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetryWorkerService();
        services.AddApplicationInsightsTelemetryProcessor<MyFirstCustomTelemetryProcessor>();
        // If you have more processors:
        services.AddApplicationInsightsTelemetryProcessor<MySecondCustomTelemetryProcessor>();
    }
```

### <a name="configuring-or-removing-default-telemetrymodules"></a>Configuração ou remoção de módulos de telemetria padrão

O Application Insights usa módulos de telemetria para coletar automaticamente a telemetria sobre cargas de trabalho específicas sem exigir rastreamento manual.

Os seguintes módulos de coleta automática são habilitados por padrão. Esses módulos são responsáveis pela coleta automática da telemetria. Você pode desabilitá-los ou configurá-los para alterar seu comportamento padrão.

* `DependencyTrackingTelemetryModule`
* `PerformanceCollectorModule`
* `QuickPulseTelemetryModule`
* `AppServicesHeartbeatTelemetryModule`
* `AzureInstanceMetadataTelemetryModule`

Para configurar qualquer `TelemetryModule`padrão, use `ConfigureTelemetryModule<T>` `IServiceCollection`o método de extensão ligado, conforme mostrado no exemplo a seguir.

```csharp
    using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
    using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetryWorkerService();

            // The following configures QuickPulseTelemetryModule.
            // Similarly, any other default modules can be configured.
            services.ConfigureTelemetryModule<QuickPulseTelemetryModule>((module, o) =>
            {
                module.AuthenticationApiKey = "keyhere";
            });

            // The following removes PerformanceCollectorModule to disable perf-counter collection.
            // Similarly, any other default modules can be removed.
            var performanceCounterService = services.FirstOrDefault<ServiceDescriptor>
                                        (t => t.ImplementationType == typeof(PerformanceCollectorModule));
            if (performanceCounterService != null)
            {
                services.Remove(performanceCounterService);
            }
    }
```

### <a name="configuring-telemetry-channel"></a>Configuração do canal de telemetria

O canal `ServerTelemetryChannel`padrão é . Você pode substituí-lo como o exemplo a seguir mostra.

```csharp
using Microsoft.ApplicationInsights.Channel;

    public void ConfigureServices(IServiceCollection services)
    {
        // Use the following to replace the default channel with InMemoryChannel.
        // This can also be applied to ServerTelemetryChannel.
        services.AddSingleton(typeof(ITelemetryChannel), new InMemoryChannel() {MaxTelemetryBufferCapacity = 19898 });

        services.AddApplicationInsightsTelemetryWorkerService();
    }
```

### <a name="disable-telemetry-dynamically"></a>Desativar a telemetria dinamicamente

Se você quiser desativar a telemetria de forma `TelemetryConfiguration` condicional e dinâmica, você pode resolver a `DisableTelemetry` instância com ASP.NET recipiente de injeção de dependência core em qualquer lugar do seu código e definir o sinalizador nele.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetryWorkerService();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env, TelemetryConfiguration configuration)
    {
        configuration.DisableTelemetry = true;
        ...
    }
```

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>Como posso rastrear a telemetria que não é coletada automaticamente?

Obtenha uma `TelemetryClient` instância de usando injeção de `TrackXXX()` construtor, e chame o método necessário sobre ele. Não recomendamos a `TelemetryClient` criação de novas instâncias. Uma instância `TelemetryClient` de singleton já `DependencyInjection` está registrada `TelemetryConfiguration` no contêiner, que compartilha com o resto da telemetria. A criação `TelemetryClient` de uma nova instância só é recomendada se ela precisar de uma configuração separada do resto da telemetria.

### <a name="can-i-use-visual-studio-ide-to-onboard-application-insights-to-a-worker-service-project"></a>Posso usar o Visual Studio IDE para abordar insights de aplicativos para um projeto de serviço do trabalhador?

O visual Studio IDE onboarding atualmente é suportado apenas para ASP.NET/ASP.NET Principais Aplicativos. Este documento será atualizado quando o Visual Studio envia suporte para aplicações de serviço onboarding Worker.

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>Posso habilitar o monitoramento do Application Insights usando ferramentas como o Monitor de Status?

Não. [O Monitor de Status](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now) e [o Monitor de Status v2](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview) atualmente suportam apenas ASP.NET 4.x.

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>Se eu executar meu aplicativo no Linux, todos os recursos são suportados?

Sim. O suporte a recursos para este SDK é o mesmo em todas as plataformas, com as seguintes exceções:

* Os contadores de desempenho são suportados apenas no Windows, com exceção da CPU/memória do processo mostrada em Métricas ao vivo.
* Embora `ServerTelemetryChannel` seja ativado por padrão, se o aplicativo estiver sendo executado no Linux ou MacOS, o canal não cria automaticamente uma pasta de armazenamento local para manter a telemetria temporariamente se houver problemas de rede. Devido a essa limitação, a telemetria é perdida quando há problemas temporários de rede ou servidor. Para contornar esse problema, configure uma pasta local para o canal:

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

    public void ConfigureServices(IServiceCollection services)
    {
        // The following will configure the channel to use the given folder to temporarily
        // store telemetry items during network or Application Insights server issues.
        // User should ensure that the given folder already exists
        // and that the application has read/write permissions.
        services.AddSingleton(typeof(ITelemetryChannel),
                                new ServerTelemetryChannel () {StorageFolder = "/tmp/myfolder"});
        services.AddApplicationInsightsTelemetryWorkerService();
    }
```

## <a name="sample-applications"></a>Aplicativos de exemplo

[.NET Core Console Application](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/ConsoleAppWithApplicationInsights) Use esta amostra se estiver usando um aplicativo de console escrito em .NET Core (2.0 ou superior) ou .NET Framework (4.7.2 ou superior)

[ASP .NET Core tarefas em plano de fundo com HostedServices](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/BackgroundTasksWithHostedService) Use esta amostra se estiver em Asp.Net Núcleo 2.1/2.2 e criando tarefas em segundo plano conforme orientação oficial [aqui](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2)

[.NET Core 3.0 Serviço de Trabalhadores](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/WorkerServiceSampleWithApplicationInsights) Use esta amostra se você tiver um aplicativo de serviço ao trabalhador .NET Core 3.0 conforme orientação oficial [aqui](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-3.0&tabs=visual-studio#worker-service-template)

## <a name="open-source-sdk"></a>SDK do código-fonte aberto

[Leia e contribua para o código.](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates)

## <a name="next-steps"></a>Próximas etapas

* [Use a API](../../azure-monitor/app/api-custom-events-metrics.md) para enviar seus próprios eventos e métricas para uma visão detalhada do desempenho e uso do seu aplicativo.
* [Rastreie dependências adicionais não rastreadas automaticamente](../../azure-monitor/app/auto-collect-dependencies.md).
* [Enriquecer ou filtrar a telemetria coletada automaticamente](../../azure-monitor/app/api-filtering-sampling.md).
* [Injeção de dependência no núcleo ASP.NET](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection).
