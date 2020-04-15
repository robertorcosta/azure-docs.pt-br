---
title: Explorar registros de rastreamento .NET com o ILogger - Azure Application Insights
description: Amostras do uso do provedor ILogger do Azure Application Insights com aplicativos ASP.NET Core e Console.
ms.topic: conceptual
ms.date: 02/19/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 33effe9cfec6d766d573617ff03b58564e5b34d1
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81313650"
---
# <a name="applicationinsightsloggerprovider-for-net-core-ilogger-logs"></a>ApplicationInsightsLoggerProvedor para logs do .NET Core ILogger

ASP.NET Core suporta uma API de registro que funciona com diferentes tipos de provedores de registro incorporados e de terceiros. O registro é feito ligando para **Log()** ou uma variante dele em instâncias *do ILogger.* Este artigo demonstra como usar *o ApplicationInsightsLoggerProvider* para capturar logins do ILogger no console e ASP.NET aplicativos Core. Este artigo também descreve como o ApplicationInsightsLoggerProvider se integra com outra selemetria do Application Insights.
Para obter mais informações, consulte [Entrar no ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging).

## <a name="aspnet-core-applications"></a>ASP.NET principais aplicações

O ApplicationInsightsLoggerProvider é ativado por padrão no [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) versão 2.7.1 (e posterior) quando você ativa o monitoramento regular do Application Insights através de qualquer um dos métodos:

- Chamando o método de extensão **UseApplicationInsights** no IWebHostBuilder (agora obsoleto)
- Ao chamar o método de extensão **AddApplicationInsightsTelemetry** no IServiceCollection

O ILogger registra que as capturas do ApplicationInsightsLoggerProvider estão sujeitas à mesma configuração que qualquer outra telemetria coletada. Eles têm o mesmo conjunto de TelemetriaInicializadores e Processadores de Telemetria, usam o mesmo TelemettryChannel, e são correlacionados e amostrados da mesma forma que outros telemetria. Se você usar a versão 2.7.1 ou posterior, nenhuma ação será necessária para capturar registros do ILogger.

Apenas os logs *de aviso* ou iLogger superior (de todas as categorias) são [enviados](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-3.1#log-category)ao Application Insights por padrão. Mas você pode [aplicar filtros para modificar esse comportamento.](#control-logging-level) Etapas adicionais são necessárias para capturar registros ILogger de **Program.cs** ou **Startup.cs**. (Consulte [capturar registros do ILogger de Startup.cs e Program.cs em ASP.NET aplicativos Core](#capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps).)

Se você usar uma versão anterior do Microsoft.ApplicationInsights.AspNet SDK ou quiser usar apenas o ApplicationInsightsLoggerProvider sem qualquer outro monitoramento do Application Insights, use o seguinte procedimento:

1. Instale o pacote NuGet:

   ```xml
       <ItemGroup>
         <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />  
       </ItemGroup>
   ```

1. Modifique **Program.cs** como mostrado aqui:

   ```csharp
   using Microsoft.AspNetCore;
   using Microsoft.AspNetCore.Hosting;
   using Microsoft.Extensions.Logging;

   public class Program
   {
       public static void Main(string[] args)
       {
           CreateWebHostBuilder(args).Build().Run();
       }

       public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
         WebHost.CreateDefaultBuilder(args)
           .UseStartup<Startup>()
         .ConfigureLogging(
               builder =>
               {
                   // Providing an instrumentation key here is required if you're using
                   // standalone package Microsoft.Extensions.Logging.ApplicationInsights
                   // or if you want to capture logs from early in the application startup
                   // pipeline from Startup.cs or Program.cs itself.
                   builder.AddApplicationInsights("ikey");

                   // Optional: Apply filters to control what logs are sent to Application Insights.
                   // The following configures LogLevel Information or above to be sent to
                   // Application Insights for all categories.
                   builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                                    ("", LogLevel.Information);
               }
           );
   }
   ```

O código na etapa `ApplicationInsightsLoggerProvider`2 configura . O código a seguir mostra uma `ILogger` classe controladora de exemplo, que usa para enviar logs. Os registros são capturados pelo Application Insights.

```csharp
public class ValuesController : ControllerBase
{
    private readonly ILogger _logger;

    public ValuesController(ILogger<ValuesController> logger)
    {
        _logger = logger;
    }

    // GET api/values
    [HttpGet]
    public ActionResult<IEnumerable<string>> Get()
    {
        // All the following logs will be picked up by Application Insights.
        // and all of them will have ("MyKey", "MyValue") in Properties.
        using (_logger.BeginScope(new Dictionary<string, object> { { "MyKey", "MyValue" } }))
            {
                _logger.LogWarning("An example of a Warning trace..");
                _logger.LogError("An example of an Error level message");
            }
        return new string[] { "value1", "value2" };
    }
}
```

### <a name="capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps"></a>Capture os logs do ILogger de Startup.cs e Program.cs em aplicativos ASP.NET Core

> [!NOTE]
> Em ASP.NET Núcleo 3.0 e posterior, não `ILogger` é mais possível injetar em Startup.cs e Program.cs. Consulte https://github.com/aspnet/Announcements/issues/353 para obter mais detalhes.

O novo ApplicationInsightsLoggerProvider pode capturar logs desde o início do pipeline de inicialização de aplicativos. Embora o ApplicationInsightsLoggerProvider esteja automaticamente habilitado no Application Insights (começando pela versão 2.7.1), ele não tem uma chave de instrumentação configurada até mais tarde no pipeline. Assim, somente os logs do **Controller**/outras classes serão capturados. Para capturar cada registro que começa com **Program.cs** e **Startup.cs** si, você deve ativar explicitamente uma chave de instrumentação para ApplicationInsightsLoggerProvider. Além disso, *a configuração de telemetria* não está totalmente configurada quando você faz log a partir de **Program.cs** ou **Startup.cs** si. Assim, esses logs terão uma configuração mínima que usa InMemoryChannel, sem amostragem e sem iniciadores ou processadores de telemetria padrão.

Os exemplos a seguir demonstram essa capacidade com **Program.cs** e **Startup.cs**.

#### <a name="example-programcs"></a>Exemplo Program.cs

```csharp
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        var host = CreateWebHostBuilder(args).Build();
        var logger = host.Services.GetRequiredService<ILogger<Program>>();
        // This will be picked up by AI
        logger.LogInformation("From Program. Running the host now..");
        host.Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureLogging(
        builder =>
            {
            // Providing an instrumentation key here is required if you're using
            // standalone package Microsoft.Extensions.Logging.ApplicationInsights
            // or if you want to capture logs from early in the application startup 
            // pipeline from Startup.cs or Program.cs itself.
            builder.AddApplicationInsights("ikey");

            // Adding the filter below to ensure logs of all severity from Program.cs
            // is sent to ApplicationInsights.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             (typeof(Program).FullName, LogLevel.Trace);

            // Adding the filter below to ensure logs of all severity from Startup.cs
            // is sent to ApplicationInsights.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             (typeof(Startup).FullName, LogLevel.Trace);
            }
        );
}
```

#### <a name="example-startupcs"></a>Exemplo Startup.cs

```csharp
public class Startup
{
    private readonly ILogger _logger;

    public Startup(IConfiguration configuration, ILogger<Startup> logger)
    {
        Configuration = configuration;
        _logger = logger;
    }

    public IConfiguration Configuration { get; }

    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // The following will be picked up by Application Insights.
        _logger.LogInformation("Logging from ConfigureServices.");
    }

    // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            // The following will be picked up by Application Insights.
            _logger.LogInformation("Configuring for Development environment");
            app.UseDeveloperExceptionPage();
        }
        else
        {
            // The following will be picked up by Application Insights.
            _logger.LogInformation("Configuring for Production environment");
        }

        app.UseMvc();
    }
}
```

## <a name="migrate-from-the-old-applicationinsightsloggerprovider"></a>Migrar do antigo ApplicationInsightsLoggerProvider

As versões do Microsoft.ApplicationInsights.AspNet SDK antes do 2.7.1 suportavam um provedor de registro que agora está obsoleto. Este provedor foi habilitado através do método de extensão **AddApplicationInsights()** do ILoggerFactory. Recomendamos que você migre para o novo provedor, que envolve duas etapas:

1. Remova a chamada *ILoggerFactory.AddApplicationInsights()* do método **Startup.Configure()** para evitar o registro duplo.
2. Reaplique todas as regras de filtragem em código, pois elas não serão respeitadas pelo novo provedor. Sobrecargas do *ILoggerFactory.AddApplicationInsights()* tiveram funções mínimas de LogLevel ou filtro. Com o novo provedor, a filtragem faz parte da própria estrutura de registro. Não é feito pelo provedor application Insights. Assim, quaisquer filtros fornecidos via *sobrecargas ILoggerFactory.AddApplicationInsights()* devem ser removidos. E as regras de filtragem devem ser fornecidas seguindo as instruções [de nível de registro de controle.](#control-logging-level) Se você usar *appsettings.json* para filtrar o registro, ele continuará a funcionar com o novo provedor, porque ambos usam o mesmo alias do provedor, *ApplicationInsights*.

Você ainda pode usar o antigo provedor. (Ele será removido apenas em uma grande mudança de versão para 3. *xx*.) Mas recomendamos que você migre para o novo provedor pelas seguintes razões:

- O provedor anterior não tem suporte para [escopos de log](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-scopes). No novo provedor, as propriedades do escopo são automaticamente adicionadas como propriedades personalizadas à telemetria coletada.
- Os logs agora podem ser capturados muito mais cedo no pipeline de inicialização de aplicativos. Os registros das aulas de **Programa** e **Startup** já podem ser capturados.
- Com o novo provedor, a filtragem é feita no nível de estrutura em si. Você pode filtrar logs para o provedor Application Insights da mesma forma que para outros provedores, incluindo provedores incorporados como Console, Debug e assim por diante. Você também pode aplicar os mesmos filtros a vários provedores.
- Em ASP.NET Core (2.0 e posteriores), a maneira recomendada de [habilitar provedores de registro](https://github.com/aspnet/Announcements/issues/255) é usando métodos de extensão no ILoggingBuilder em **Program.cs** si.

> [!Note]
> O novo provedor está disponível para aplicativos que visam o NETSTANDARD2.0 ou posterior. A partir do [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) versão 2.14.0 em diante, o novo provedor também está disponível para aplicativos que visam o .NET Framework NET461 ou posterior. Se o aplicativo tiver como alvo versões mais antigas do .NET Core, como .NET Core 1.1, ou se tiver como alvo o .NET Framework menos que o NET46, continue a usar o provedor antigo.

## <a name="console-application"></a>Aplicativo de console

> [!NOTE]
> Há um novo Application Insights SDK chamado [Microsoft.ApplicationInsights.WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) que pode ser usado para habilitar insights de aplicativos (ILogger e outros insights de aplicativos) para quaisquer aplicativos de console. Recomenda-se usar este pacote e instruções associadas [daqui.](../../azure-monitor/app/worker-service.md)

O código a seguir mostra um aplicativo de console de exemplo configurado para enviar rastreamentos do ILogger para o Application Insights.

Pacotes instalados:

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Extensions.DependencyInjection" Version="2.1.0" />  
  <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />
  <PackageReference Include="Microsoft.Extensions.Logging.Console" Version="2.1.0" />
</ItemGroup>
```

```csharp
class Program
{
    static void Main(string[] args)
    {
        // Create the DI container.
        IServiceCollection services = new ServiceCollection();

        // Channel is explicitly configured to do flush on it later.
        var channel = new InMemoryChannel();
        services.Configure<TelemetryConfiguration>(
            (config) =>
            {
                config.TelemetryChannel = channel;
            }
        );

        // Add the logging pipelines to use. We are using Application Insights only here.
        services.AddLogging(builder =>
        {
            // Optional: Apply filters to configure LogLevel Trace or above is sent to
            // Application Insights for all categories.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             ("", LogLevel.Trace);
            builder.AddApplicationInsights("--YourAIKeyHere--");
        });

        // Build ServiceProvider.
        IServiceProvider serviceProvider = services.BuildServiceProvider();

        ILogger<Program> logger = serviceProvider.GetRequiredService<ILogger<Program>>();

        // Begin a new scope. This is optional.
        using (logger.BeginScope(new Dictionary<string, object> { { "Method", nameof(Main) } }))
        {
            logger.LogInformation("Logger is working"); // this will be captured by Application Insights.
        }

        // Explicitly call Flush() followed by sleep is required in Console Apps.
        // This is to ensure that even if application terminates, telemetry is sent to the back-end.
        channel.Flush();
        Thread.Sleep(1000);
    }
}
```

Este exemplo usa o `Microsoft.Extensions.Logging.ApplicationInsights`pacote autônomo . Por padrão, essa configuração usa a configuração de telemetria "mínima nua" para enviar dados para o Application Insights. Mínimo significa que InMemoryChannel é o canal usado. Não há amostragem nem iniciais de telemetria padrão. Esse comportamento pode ser substituído por um aplicativo de console, como mostra o exemplo a seguir.

Instale esse pacote adicional:

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

A seção a seguir mostra como substituir a Configuração de Telemetria padrão usando os **serviços. Configurar\<telemetriaConfiguração>()** método. Este exemplo `ServerTelemetryChannel` configura e amostragem. Ele adiciona um ITelemetriaInicialr personalizado à Configuração de Telemetria.

```csharp
    // Create the DI container.
    IServiceCollection services = new ServiceCollection();
    var serverChannel = new ServerTelemetryChannel();
    services.Configure<TelemetryConfiguration>(
        (config) =>
        {
            config.TelemetryChannel = serverChannel;
            config.TelemetryInitializers.Add(new MyTelemetryInitalizer());
            config.DefaultTelemetrySink.TelemetryProcessorChainBuilder.UseSampling(5);
            serverChannel.Initialize(config);
        }
    );

    // Add the logging pipelines to use. We are adding Application Insights only.
    services.AddLogging(loggingBuilder =>
    {
        loggingBuilder.AddApplicationInsights();
    });

    ........
    ........

    // Explicitly calling Flush() followed by sleep is required in Console Apps.
    // This is to ensure that even if the application terminates, telemetry is sent to the back end.
    serverChannel.Flush();
    Thread.Sleep(1000);
```

## <a name="control-logging-level"></a>Nível de registro de controle

O ASP.NET infra *Core ILogger* possui um mecanismo embutido para aplicar a [filtragem de log](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-filtering). Isso permite controlar os registros enviados a cada provedor registrado, incluindo o provedor Application Insights. A filtragem pode ser feita tanto na configuração (normalmente usando um arquivo *appsettings.json)* quanto em código. Esta instalação é fornecida pela própria estrutura. Não é específico para o provedor de Insights de Aplicativos.

Os exemplos a seguir aplicam regras de filtro ao ApplicationInsightsLoggerProvider.

### <a name="create-filter-rules-in-configuration-with-appsettingsjson"></a>Crie regras de filtro na configuração com appsettings.json

Para ApplicationInsightsLoggerProvider, o alias do provedor é `ApplicationInsights`. A seção a seguir de *appsettings.json* instrui os provedores de registro geralmente a registrar em nível *de aviso* e acima. Em seguida, `ApplicationInsightsLoggerProvider` ele substitui as categorias de log que começam com "Microsoft" no nível *Error* e acima.

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    },
    "ApplicationInsights": {
      "LogLevel": {
        "Microsoft": "Error"
      }
    }
  }
}
```

### <a name="create-filter-rules-in-code"></a>Criar regras de filtro em código

O trecho de código a seguir configura logs para *Aviso* e acima de todas as categorias e para `ApplicationInsightsLoggerProvider` *Erro* e acima das categorias que começam com "Microsoft" a ser enviado para . Esta configuração é a mesma da seção anterior em *appsettings.json*.

```csharp
    WebHost.CreateDefaultBuilder(args)
    .UseStartup<Startup>()
    .ConfigureLogging(logging =>
      logging.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("", LogLevel.Warning)
             .AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("Microsoft", LogLevel.Error);
```

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="what-are-the-old-and-new-versions-of-applicationinsightsloggerprovider"></a>Quais são as versões antigas e novas do ApplicationInsightsLoggerProvider?

[Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) incluiu um aplicativo incorporadoInsightsLoggerProvider (Microsoft.ApplicationInsights.AspNetCore.Logging.ApplicationInsightsLoggerProvider), que foi ativado através de métodos de extensão **ILoggerFactory.** Este provedor está marcado como obsoleto a partir da versão 2.7.1. Ele será removido completamente na próxima grande mudança de versão. O pacote [Microsoft.ApplicationInsights.AspNetCore 2.6.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) em si não é obsoleto. É necessário para permitir o monitoramento de solicitações, dependências, e assim por diante.

A alternativa sugerida é o novo pacote autônomo [Microsoft.Extensions.Logging.ApplicationInsights,](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights)que contém um aplicativo melhoradoInsightsLoggerProvider (Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsights.ApplicationInsightsLoggerProvider) e métodos de extensão no ILoggerBuilder para habilitá-lo.

[Microsoft.ApplicationInsights.AspNet A](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) versão 2.7.1 tem uma dependência do novo pacote e permite a captura do ILogger automaticamente.

### <a name="why-are-some-ilogger-logs-shown-twice-in-application-insights"></a>Por que alguns registros do ILogger são mostrados duas vezes no Application Insights?

A duplicação pode ocorrer se você tiver a versão mais antiga (agora `AddApplicationInsights` `ILoggerFactory`obsoleta) do ApplicationInsightsLoggerProvider ativada por chamada . Verifique se o método **Configurar** tem o seguinte e remova-o:

```csharp
 public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
 {
     loggerFactory.AddApplicationInsights(app.ApplicationServices, LogLevel.Warning);
     // ..other code.
 }
```

Se você tiver o duplo registro quando `EnableDebugLogger` fizer a depuração do Visual Studio, defina-se como *falso* no código que habilita o Application Insights, da seguinte forma. Essa duplicação e correção só é relevante quando você está depurando o aplicativo.

```csharp
 public void ConfigureServices(IServiceCollection services)
 {
     ApplicationInsightsServiceOptions options = new ApplicationInsightsServiceOptions();
     options.EnableDebugLogger = false;
     services.AddApplicationInsightsTelemetry(options);
     // ..other code.
 }
```

### <a name="i-updated-to-microsoftapplicationinsightsaspnet-sdk-version-271-and-logs-from-ilogger-are-captured-automatically-how-do-i-turn-off-this-feature-completely"></a>Atualizei para [microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) versão 2.7.1, e os logs do ILogger são capturados automaticamente. Como desligo completamente esse recurso?

Consulte a seção [Controle de nível de registro](../../azure-monitor/app/ilogger.md#control-logging-level) para ver como filtrar logs em geral. Para desativar o ApplicationInsightsLoggerProvider, use `LogLevel.None`:

**No código:**

```csharp
    builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                      ("", LogLevel.None);
```

**Na configuração:**

```json
{
  "Logging": {
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "None"
      }
}
```

### <a name="why-do-some-ilogger-logs-not-have-the-same-properties-as-others"></a>Por que alguns registros iLogger não têm as mesmas propriedades que outros?

O Application Insights captura e envia registros do ILogger usando a mesma configuração de telemetria que é usada para todas as outras telemetrias. Mas há uma exceção. Por padrão, a configuração de telemetria não está totalmente configurada quando você faz loga do **Program.cs** ou **Startup.cs**. Os logs desses lugares não terão a configuração padrão, portanto, eles não estarão executando todos os processadores de telemetria e processadores de telemetria.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-i-want-to-log-some-additional-custom-telemetry-manually-how-should-i-do-that"></a>Estou usando o pacote autônomo Microsoft.Extensions.Logging.ApplicationInsights, e quero registrar alguma telemetria personalizada adicional manualmente. Como eu deveria fazer isso?

Quando você usa o `TelemetryClient` pacote autônomo, não é injetado no contêiner DI, `TelemetryClient` então você precisa criar uma nova instância e usar a mesma configuração que o provedor de logger usa, como mostra o código a seguir. Isso garante que a mesma configuração seja usada para toda telemetria personalizada, bem como telemetria do ILogger.

```csharp
public class MyController : ApiController
{
   // This telemetryclient can be used to track additional telemetry using TrackXXX() api.
   private readonly TelemetryClient _telemetryClient;
   private readonly ILogger _logger;

   public MyController(IOptions<TelemetryConfiguration> options, ILogger<MyController> logger)
   {
        _telemetryClient = new TelemetryClient(options.Value);
        _logger = logger;
   }  
}
```

> [!NOTE]
> Se você usar o pacote Microsoft.ApplicationInsights.AspNetCore para habilitar `TelemetryClient` o Application Insights, modifique esse código para entrar diretamente no construtor. Por exemplo, veja [esta FAQ](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core#frequently-asked-questions).


### <a name="what-application-insights-telemetry-type-is-produced-from-ilogger-logs-or-where-can-i-see-ilogger-logs-in-application-insights"></a>Qual tipo de telemetria do Application Insights é produzido a partir de registros iLogger? Ou onde posso ver os logs do ILogger no Application Insights?

O ApplicationInsightsLoggerProvider captura registros do ILogger e cria traceTelemetria a partir deles. Se um objeto Exceção for passado para o método **Log()** no ILogger, *a Telemetria de Exceção* será criada em vez de TraceTelemettria. Esses itens de telemetria podem ser encontrados nos mesmos lugares que qualquer outro TraceTelemettria ou ExceptionTelemetria para Insights de Aplicativos, incluindo portal, análise ou depurador local do Visual Studio.

Se preferir sempre enviar TraceTelemetria, use este trecho:```builder.AddApplicationInsights((opt) => opt.TrackExceptionsAsExceptionTelemetry = false);```

### <a name="i-dont-have-the-sdk-installed-and-i-use-the-azure-web-apps-extension-to-enable-application-insights-for-my-aspnet-core-applications-how-do-i-use-the-new-provider"></a>Não tenho o SDK instalado, e uso a extensão Azure Web Apps para habilitar o Application Insights para meus aplicativos ASP.NET Core. Como faço para usar o novo provedor? 

A extensão Application Insights no Azure Web Apps usa o novo provedor. Você pode modificar as regras de filtragem no arquivo *appsettings.json* para sua aplicação.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-enabling-application-insights-provider-by-calling-builderaddapplicationinsightsikey-is-there-an-option-to-get-an-instrumentation-key-from-configuration"></a>Estou usando o pacote autônomo Microsoft.Extensions.Logging.ApplicationInsights e habilitando o provedor application Insights ligando para **o construtor. Adicionar ApplicationInsights ("ikey")**. Existe uma opção para obter uma chave de instrumentação a partir da configuração?


Modifique Program.cs e appsettings.json da seguinte forma:

   ```csharp
   public class Program
   {
       public static void Main(string[] args)
       {
           CreateWebHostBuilder(args).Build().Run();
       }

       public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
           WebHost.CreateDefaultBuilder(args)
               .UseStartup<Startup>()
               .ConfigureLogging((hostingContext, logging) =>
               {
                   // hostingContext.HostingEnvironment can be used to determine environments as well.
                   var appInsightKey = hostingContext.Configuration["myikeyfromconfig"];
                   logging.AddApplicationInsights(appInsightKey);
               });
   }
   ```

   Seção `appsettings.json`relevante de:

   ```json
   {
     "myikeyfromconfig": "putrealikeyhere"
   }
   ```

Este código só é necessário quando você usa um provedor de registro autônomo. Para monitoramento regular do Application Insights, a chave de instrumentação é carregada automaticamente a partir do caminho de configuração *ApplicationInsights: Instrumentationkey*. Appsettings.json deve ficar assim:

   ```json
   {
     "ApplicationInsights":
       {
           "Instrumentationkey":"putrealikeyhere"
       }
   }
   ```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre:

* [Registro em log no ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging)
* [Registros de rastreamento .NET no Application Insights](../../azure-monitor/app/asp-net-trace-logs.md)
