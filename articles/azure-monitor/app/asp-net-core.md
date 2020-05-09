---
title: Aplicativo Azure insights para aplicativos ASP.NET Core | Microsoft Docs
description: Monitorar aplicativos web ASP.NET Core de disponibilidade, desempenho e uso.
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: 9c7c2e22d2befb503a388df1fa8a42c3d6eb07c5
ms.sourcegitcommit: d662eda7c8eec2a5e131935d16c80f1cf298cb6b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82652783"
---
# <a name="application-insights-for-aspnet-core-applications"></a>Application Insights para aplicativos ASP.NET Core

Este artigo descreve como habilitar Application Insights para um aplicativo [ASP.NET Core](https://docs.microsoft.com/aspnet/core) . Quando você concluir as instruções neste artigo, Application Insights coletará solicitações, dependências, exceções, contadores de desempenho, pulsações e logs do seu aplicativo ASP.NET Core.

O exemplo que usaremos aqui é um [aplicativo MVC](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app) que tem como `netcoreapp3.0`destino. Você pode aplicar essas instruções a todos os ASP.NET Core aplicativos.

## <a name="supported-scenarios"></a>Cenários com suporte

O [SDK do Application insights para ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) pode monitorar seus aplicativos, independentemente de onde ou como eles são executados. Se seu aplicativo estiver em execução e tiver conectividade de rede com o Azure, a telemetria poderá ser coletada. Há suporte para o monitoramento de Application Insights em todos os lugares do .NET Core. O suporte abrange:
* **Sistema operacional**: Windows, Linux ou Mac.
* **Método de hospedagem**: em processo ou fora do processo.
* **Método de implantação**: dependente da estrutura ou independente.
* **Servidor Web**: IIS (servidor de informações da Internet) ou Kestrel.
* **Plataforma de hospedagem**: o recurso de aplicativos Web do serviço de Azure app, VM do Azure, Docker, serviço kubernetes do Azure (AKs) e assim por diante.
* **Versão de tempo de execução do .NET Core**: 1. XX, 2. XX ou 3. XX
* **IDE**: Visual Studio, vs Code ou linha de comando.

> [!NOTE]
> ASP.NET Core 3. X requer [Application insights 2.8.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/2.8.0) ou posterior.

## <a name="prerequisites"></a>Pré-requisitos

- Um aplicativo ASP.NET Core funcionando. Se você precisar criar um aplicativo ASP.NET Core, siga este [tutorial de ASP.NET Core](https://docs.microsoft.com/aspnet/core/getting-started/).
- Uma chave de instrumentação de Application Insights válida. Essa chave é necessária para enviar qualquer telemetria para Application Insights. Se você precisar criar um novo recurso de Application Insights para obter uma chave de instrumentação, consulte [criar um recurso de Application insights](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource).

## <a name="enable-application-insights-server-side-telemetry-visual-studio"></a>Habilitar Application Insights telemetria do lado do servidor (Visual Studio)

1. Abra o projeto no Visual Studio.

    > [!TIP]
    > Se desejar, você pode configurar o controle do código-fonte para o seu projeto para poder acompanhar todas as alterações que Application Insights faz. Para habilitar o controle do código-fonte, selecione **arquivo** > **Adicionar ao controle do código-fonte**.

2. Selecione **projeto** > **Adicionar Application insights Telemetry**.

3. Selecione **introdução**. O texto dessa seleção pode variar, dependendo da sua versão do Visual Studio. Em vez disso, algumas versões anteriores usam um botão **Iniciar gratuito** .

4. Selecione sua assinatura. Em seguida, selecione**registro**de **recursos** > .

5. Depois de adicionar Application Insights ao seu projeto, verifique se você está usando a versão estável mais recente do SDK. Vá para **projeto** > **gerenciar pacotes** > NuGet**Microsoft. ApplicationInsights. AspNetCore**. Se necessário, escolha **Atualizar**.

     ![Captura de tela mostrando onde selecionar o pacote de Application Insights para atualização](./media/asp-net-core/update-nuget-package.png)

6. Se você seguiu a dica opcional e adicionou o projeto ao controle do código-fonte, acesse **Exibir** > **Team Explorer** > **alterações**. Em seguida, selecione cada arquivo para ver uma exibição de comparação das alterações feitas por Application Insights telemetria.

## <a name="enable-application-insights-server-side-telemetry-no-visual-studio"></a>Habilitar Application Insights telemetria do lado do servidor (sem o Visual Studio)

1. Instale o [pacote NuGet do SDK Application insights para ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore). Recomendamos que você sempre use a versão estável mais recente. Encontre notas de versão completas para o SDK no [repositório GitHub de código-fonte aberto](https://github.com/Microsoft/ApplicationInsights-aspnetcore/releases).

    O exemplo de código a seguir mostra as alterações a serem adicionadas ao `.csproj` arquivo do seu projeto.

    ```xml
        <ItemGroup>
          <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.13.1" />
        </ItemGroup>
    ```

2. Adicione `services.AddApplicationInsightsTelemetry();` ao `ConfigureServices()` método em sua `Startup` classe, como neste exemplo:

    ```csharp
        // This method gets called by the runtime. Use this method to add services to the container.
        public void ConfigureServices(IServiceCollection services)
        {
            // The following line enables Application Insights telemetry collection.
            services.AddApplicationInsightsTelemetry();
    
            // This code adds other services for your application.
            services.AddMvc();
        }
    ```

3. Configure a chave de instrumentação.

    Embora seja possível fornecer a chave de instrumentação como um argumento `AddApplicationInsightsTelemetry`para, recomendamos que você especifique a chave de instrumentação na configuração. O exemplo de código a seguir mostra como especificar uma chave de instrumentação no `appsettings.json`. Certifique- `appsettings.json` se de que o seja copiado para a pasta raiz do aplicativo durante a publicação.

    ```json
        {
          "ApplicationInsights": {
            "InstrumentationKey": "putinstrumentationkeyhere"
          },
          "Logging": {
            "LogLevel": {
              "Default": "Warning"
            }
          }
        }
    ```

    Como alternativa, especifique a chave de instrumentação em qualquer uma das seguintes variáveis de ambiente:

    * `APPINSIGHTS_INSTRUMENTATIONKEY`

    * `ApplicationInsights:InstrumentationKey`

    Por exemplo: 

    * `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`

    * `SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

    * `APPINSIGHTS_INSTRUMENTATIONKEY`normalmente é usado em [aplicativos Web do Azure](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps?tabs=net), mas também pode ser usado em todos os locais em que há suporte para esse SDK. (Se você estiver fazendo um monitoramento de aplicativo Web sem código, esse formato será necessário se você não estiver usando cadeias de conexão.)

    Em vez de definir as chaves de instrumentação, agora você também pode usar [cadeias de conexão](https://docs.microsoft.com/azure/azure-monitor/app/sdk-connection-string?tabs=net).

    > [!NOTE]
    > Uma chave de instrumentação especificada no código vence sobre a variável `APPINSIGHTS_INSTRUMENTATIONKEY`de ambiente, que vence em outras opções.

## <a name="run-your-application"></a>Execute seu aplicativo.

Execute seu aplicativo e faça solicitações a ele. A telemetria agora deve fluir para Application Insights. O SDK do Application Insights coleta automaticamente as solicitações da Web de entrada para seu aplicativo, juntamente com a telemetria a seguir também.

### <a name="live-metrics"></a>Métricas ao vivo

As [métricas dinâmicas](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream) podem ser usadas para verificar rapidamente se Application insights monitoramento está configurado corretamente. Embora possa levar alguns minutos antes que a telemetria comece a aparecer no portal e na análise, as métricas ao vivo mostrarão o uso da CPU do processo em execução quase em tempo real. Ele também pode mostrar outras telemetrias, como solicitações, dependências, rastreamentos, etc.

### <a name="ilogger-logs"></a>Logs do ILogger

Os logs emitidos `ILogger` por severidade `Warning` ou mais são capturados automaticamente. Siga [ILogger docs](ilogger.md#control-logging-level) para personalizar quais níveis de log são capturados por Application insights.

### <a name="dependencies"></a>Dependências

A coleção de dependências é habilitada por padrão. [Este](asp-net-dependencies.md#automatically-tracked-dependencies) artigo explica as dependências coletadas automaticamente e também contém etapas para fazer o acompanhamento manual.

### <a name="performance-counters"></a>Contadores de desempenho

O suporte para [contadores de desempenho](https://azure.microsoft.com/documentation/articles/app-insights-web-monitor-performance/) no ASP.NET Core é limitado:

* As versões do SDK 2.4.1 e posteriores coletam contadores de desempenho se o aplicativo estiver em execução nos aplicativos Web do Azure (Windows).
* As versões do SDK 2.7.1 e posteriores coletam contadores de desempenho se o aplicativo estiver `NETSTANDARD2.0` em execução no Windows e nos destinos ou posterior.
* Para aplicativos direcionados à .NET Framework, todas as versões do SDK dão suporte a contadores de desempenho.
* As versões do SDK 2.8.0 e posteriores dão suporte ao contador de CPU/memória no Linux. Nenhum outro contador tem suporte no Linux. A maneira recomendada para obter contadores do sistema no Linux (e em outros ambientes não Windows) é usando [EventCounters](#eventcounter)

### <a name="eventcounter"></a>EventCounter

`EventCounterCollectionModule`é habilitado por padrão e coletará um conjunto padrão de contadores de aplicativos .NET Core 3. X. O tutorial [EventCounter](eventcounters.md) lista o conjunto padrão de contadores coletados. Ele também tem instruções sobre como personalizar a lista.

## <a name="enable-client-side-telemetry-for-web-applications"></a>Habilitar telemetria do lado do cliente para aplicativos Web

As etapas anteriores são suficientes para ajudá-lo a começar a coletar a telemetria do lado do servidor. Se seu aplicativo tiver componentes do lado do cliente, siga as próximas etapas para começar a coletar a [telemetria de uso](https://docs.microsoft.com/azure/azure-monitor/app/usage-overview).

1. No `_ViewImports.cshtml`, adicione injeção:

    ```cshtml
        @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
    ```

2. No `_Layout.cshtml`, insira `HtmlHelper` no final da `<head>` seção, mas antes de qualquer outro script. Se você quiser relatar qualquer telemetria personalizada do JavaScript da página, insira-a após este trecho:

    ```cshtml
        @Html.Raw(JavaScriptSnippet.FullScript)
        </head>
    ```
    
Como alternativa, o uso `FullScript` do `ScriptBody` está disponível a partir do SDK v 2.14. Use isso se você precisar controlar a `<script>` marca para definir uma política de segurança de conteúdo:

```cshtml
 <script> // apply custom changes to this script tag.
     @Html.Raw(JavaScriptSnippet.ScriptBody)
 </script>
```

Os `.cshtml` nomes de arquivo referenciados anteriormente são de um modelo de aplicativo MVC padrão. Por fim, se você quiser habilitar corretamente o monitoramento do lado do cliente para seu aplicativo, o trecho de código JavaScript `<head>` deverá aparecer na seção de cada página do aplicativo que você deseja monitorar. Você pode realizar essa meta para esse modelo de aplicativo adicionando o trecho de JavaScript `_Layout.cshtml`ao. 

Se o projeto não incluir `_Layout.cshtml`, você ainda poderá adicionar o [monitoramento do lado do cliente](https://docs.microsoft.com/azure/azure-monitor/app/website-monitoring). Você pode fazer isso adicionando o trecho de JavaScript a um arquivo equivalente que controla o `<head>` de todas as páginas em seu aplicativo. Ou você pode adicionar o trecho a várias páginas, mas essa solução é difícil de manter e, em geral, não é recomendável.

## <a name="configure-the-application-insights-sdk"></a>Configurar o SDK do Application Insights

Você pode personalizar o SDK do Application Insights para ASP.NET Core para alterar a configuração padrão. Os usuários do Application Insights SDK do ASP.NET podem estar familiarizados com a alteração `ApplicationInsights.config` da configuração usando `TelemetryConfiguration.Active`ou modificando. Você altera a configuração de forma diferente para ASP.NET Core. Adicione o SDK do ASP.NET Core ao aplicativo e configure-o usando ASP.NET Core [injeção de dependência](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)interna. Faça quase todas as alterações de configuração `ConfigureServices()` no método da `Startup.cs` sua classe, a menos que você seja direcionado de outra forma. As seções a seguir oferecem mais informações.

> [!NOTE]
> Em ASP.NET Core aplicativos, não há suporte para `TelemetryConfiguration.Active` alterar a configuração modificando.

### <a name="using-applicationinsightsserviceoptions"></a>Usando ApplicationInsightsServiceOptions

Você pode modificar algumas configurações comuns passando `ApplicationInsightsServiceOptions` para `AddApplicationInsightsTelemetry`, como neste exemplo:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions aiOptions
                = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
    // Disables adaptive sampling.
    aiOptions.EnableAdaptiveSampling = false;

    // Disables QuickPulse (Live Metrics stream).
    aiOptions.EnableQuickPulseMetricStream = false;
    services.AddApplicationInsightsTelemetry(aiOptions);
}
```

Lista completa de configurações em`ApplicationInsightsServiceOptions`

|Configuração | Descrição | Padrão
|---------------|-------|-------
|EnablePerformanceCounterCollectionModule  | Habilitar/desabilitar`PerformanceCounterCollectionModule` | true
|EnableRequestTrackingTelemetryModule   | Habilitar/desabilitar`RequestTrackingTelemetryModule` | true
|EnableEventCounterCollectionModule   | Habilitar/desabilitar`EventCounterCollectionModule` | true
|EnableDependencyTrackingTelemetryModule   | Habilitar/desabilitar`DependencyTrackingTelemetryModule` | true
|EnableAppServicesHeartbeatTelemetryModule  |  Habilitar/desabilitar`AppServicesHeartbeatTelemetryModule` | true
|EnableAzureInstanceMetadataTelemetryModule   |  Habilitar/desabilitar`AzureInstanceMetadataTelemetryModule` | true
|EnableQuickPulseMetricStream | Habilitar/desabilitar o recurso LiveMetrics | true
|EnableAdaptiveSampling | Habilitar/desabilitar amostragem adaptável | true
|EnableHeartbeat | Habilitar/desabilitar o recurso de pulsações, que periodicamente (padrão de 15 min) envia uma métrica personalizada chamada ' Heartbeatstate ' com informações sobre o tempo de execução como versão do .NET, informações de ambiente do Azure, se aplicável, etc. | true
|AddAutoCollectedMetricExtractor | Habilitar/desabilitar o extrator AutoCollectedMetrics, que é um TelemetryProcessor que envia métricas previamente agregadas sobre solicitações/dependências antes que a amostragem ocorra. | true
|RequestCollectionOptions.TrackExceptions | Habilitar/desabilitar o relatório de acompanhamento de exceção sem tratamento pelo módulo coleção de solicitações. | false no netstandard 2.0 (como as exceções são rastreadas com ApplicationInsightsLoggerProvider), caso contrário, true.

Consulte as [configurações configuráveis `ApplicationInsightsServiceOptions` no](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/NETCORE/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs) para obter a lista mais atualizada.

### <a name="sampling"></a>amostragem

O SDK do Application Insights para ASP.NET Core dá suporte à amostragem de taxa fixa e adaptável. A amostragem adaptável é habilitada por padrão. 

Para obter mais informações, consulte [Configurar a amostragem adaptável para aplicativos ASP.NET Core](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications).

### <a name="adding-telemetryinitializers"></a>Adicionando TelemetryInitializers

Use [inicializadores de telemetria](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#addmodify-properties-itelemetryinitializer) quando desejar definir propriedades globais que são enviadas com toda a telemetria.

Adicione qualquer novo `TelemetryInitializer` ao `DependencyInjection` contêiner, conforme mostrado no código a seguir. O SDK seleciona automaticamente qualquer `TelemetryInitializer` que seja adicionado ao `DependencyInjection` contêiner.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
}
```

### <a name="removing-telemetryinitializers"></a>Removendo TelemetryInitializers

Os inicializadores de telemetria estão presentes por padrão. Para remover todos os inicializadores de telemetria específicos, use o código *after* de exemplo a `AddApplicationInsightsTelemetry()`seguir depois de chamar.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry();

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

Você pode adicionar processadores de telemetria `TelemetryConfiguration` personalizados ao usando o método `AddApplicationInsightsTelemetryProcessor` de `IServiceCollection`extensão no. Você usa processadores de telemetria em [cenários de filtragem avançada](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#itelemetryprocessor-and-itelemetryinitializer). Use o exemplo a seguir.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ...
    services.AddApplicationInsightsTelemetry();
    services.AddApplicationInsightsTelemetryProcessor<MyFirstCustomTelemetryProcessor>();

    // If you have more processors:
    services.AddApplicationInsightsTelemetryProcessor<MySecondCustomTelemetryProcessor>();
}
```

### <a name="configuring-or-removing-default-telemetrymodules"></a>Configurando ou removendo TelemetryModules padrão

Application Insights usa módulos de telemetria para coletar automaticamente telemetria útil sobre cargas de trabalho específicas sem a necessidade de acompanhamento manual pelo usuário.

Os seguintes módulos de coleta automática estão habilitados por padrão. Esses módulos são responsáveis por coletar automaticamente a telemetria. Você pode desabilitá-los ou configurá-los para alterar seu comportamento padrão.

* `RequestTrackingTelemetryModule`-Coleta RequestTelemetry de solicitações de entrada da Web.
* `DependencyTrackingTelemetryModule`-Coleta DependencyTelemetry de chamadas http de saída e chamadas SQL.
* `PerformanceCollectorModule`-Coleta os PerformanceCounters do Windows.
* `QuickPulseTelemetryModule`-Coleta a telemetria para exibição no portal de métricas ao vivo.
* `AppServicesHeartbeatTelemetryModule`-Coleta os batidas no coração (que são enviados como métricas personalizadas), sobre o ambiente de serviço Azure App em que o aplicativo está hospedado.
* `AzureInstanceMetadataTelemetryModule`-Coleta os batidas no coração (que são enviados como métricas personalizadas), sobre o ambiente de VM do Azure em que o aplicativo está hospedado.
* `EventCounterCollectionModule`-Coleta [EventCounters.](eventcounters.md) Este módulo é um novo recurso e está disponível no SDK versão 2.8.0 e superior.

Para configurar qualquer padrão `TelemetryModule`, use o método `ConfigureTelemetryModule<T>` de extensão `IServiceCollection`em, conforme mostrado no exemplo a seguir.

```csharp
using Microsoft.ApplicationInsights.DependencyCollector;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector;

public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry();

    // The following configures DependencyTrackingTelemetryModule.
    // Similarly, any other default modules can be configured.
    services.ConfigureTelemetryModule<DependencyTrackingTelemetryModule>((module, o) =>
            {
                module.EnableW3CHeadersInjection = true;
            });

    // The following removes all default counters from EventCounterCollectionModule, and adds a single one.
    services.ConfigureTelemetryModule<EventCounterCollectionModule>(
            (module, o) =>
            {
                module.Counters.Clear();
                module.Counters.Add(new EventCounterCollectionRequest("System.Runtime", "gen-0-size"));
            }
        );

    // The following removes PerformanceCollectorModule to disable perf-counter collection.
    // Similarly, any other default modules can be removed.
    var performanceCounterService = services.FirstOrDefault<ServiceDescriptor>(t => t.ImplementationType == typeof(PerformanceCollectorModule));
    if (performanceCounterService != null)
    {
        services.Remove(performanceCounterService);
    }
}
```

A partir da versão 2.12.2 [`ApplicationInsightsServiceOptions`](#using-applicationinsightsserviceoptions) , o contém uma opção fácil para desabilitar qualquer um dos módulos padrão.

### <a name="configuring-a-telemetry-channel"></a>Configurando um canal de telemetria

O canal padrão é `ServerTelemetryChannel`. Você pode substituí-lo como mostra o exemplo a seguir.

```csharp
using Microsoft.ApplicationInsights.Channel;

    public void ConfigureServices(IServiceCollection services)
    {
        // Use the following to replace the default channel with InMemoryChannel.
        // This can also be applied to ServerTelemetryChannel.
        services.AddSingleton(typeof(ITelemetryChannel), new InMemoryChannel() {MaxTelemetryBufferCapacity = 19898 });

        services.AddApplicationInsightsTelemetry();
    }
```

### <a name="disable-telemetry-dynamically"></a>Desabilitar telemetria dinamicamente

Se você quiser desabilitar a telemetria condicional e dinamicamente, poderá resolver `TelemetryConfiguration` a instância com ASP.NET Core contêiner de injeção de dependência em qualquer lugar em `DisableTelemetry` seu código e definir o sinalizador nela.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env, TelemetryConfiguration configuration)
    {
        configuration.DisableTelemetry = true;
        ...
    }
```

O acima não impede que nenhum módulo de coleção automática colete telemetria. Somente o envio de telemetria para Application Insights é desabilitado com a abordagem acima. Se um módulo de coleção automática específico não for desejado, é melhor [remover o módulo de telemetria](#configuring-or-removing-default-telemetrymodules)

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="does-application-insights-support-aspnet-core-3x"></a>Application Insights oferece suporte ao ASP.NET Core 3. X?

Sim. Atualize para [Application insights SDK para ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) versão 2.8.0 ou superior. As versões mais antigas do SDK não dão suporte ao ASP.NET Core 3. X.

Além disso, se você estiver usando as instruções baseadas no Visual Studio [aqui](#enable-application-insights-server-side-telemetry-visual-studio), atualize para a versão mais recente do Visual Studio 2019 (16.3.0) para carregar. As versões anteriores do Visual Studio não dão suporte à integração automática para aplicativos ASP.NET Core 3. X.

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>Como posso rastrear a telemetria que não é coletada automaticamente?

Obtenha uma instância do `TelemetryClient` usando injeção de construtor e chame o método necessário `TrackXXX()` nele. Não recomendamos a criação `TelemetryClient` de novas instâncias em um aplicativo ASP.NET Core. Uma instância singleton do `TelemetryClient` já está registrada no `DependencyInjection` contêiner, que compartilha `TelemetryConfiguration` com o restante da telemetria. A criação de `TelemetryClient` uma nova instância é recomendada apenas se precisar de uma configuração separada do restante da telemetria.

O exemplo a seguir mostra como acompanhar a telemetria adicional de um controlador.

```csharp
using Microsoft.ApplicationInsights;

public class HomeController : Controller
{
    private TelemetryClient telemetry;

    // Use constructor injection to get a TelemetryClient instance.
    public HomeController(TelemetryClient telemetry)
    {
        this.telemetry = telemetry;
    }

    public IActionResult Index()
    {
        // Call the required TrackXXX method.
        this.telemetry.TrackEvent("HomePageRequested");
        return View();
    }
```

Para obter mais informações sobre relatórios de dados personalizados em Application Insights, consulte [Application insights referência de API de métricas personalizadas](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics/).

### <a name="some-visual-studio-templates-used-the-useapplicationinsights-extension-method-on-iwebhostbuilder-to-enable-application-insights-is-this-usage-still-valid"></a>Alguns modelos do Visual Studio usaram o método de extensão UseApplicationInsights () em IWebHostBuilder para habilitar Application Insights. Esse uso ainda é válido?

Embora o método `UseApplicationInsights()` de extensão ainda tenha suporte, ele é marcado como obsoleto no Application insights SDK versão 2.8.0 em diante. Ele será removido na próxima versão principal do SDK. A maneira recomendada para habilitar a telemetria de Application Insights `AddApplicationInsightsTelemetry()` é usando o porque ela fornece sobrecargas para controlar algumas configurações. Além disso, em ASP.NET Core aplicativos 3. X `services.AddApplicationInsightsTelemetry()` , é a única maneira de habilitar o Application insights.

### <a name="im-deploying-my-aspnet-core-application-to-web-apps-should-i-still-enable-the-application-insights-extension-from-web-apps"></a>Estou implantando meu aplicativo de ASP.NET Core em aplicativos Web. Ainda devo habilitar a extensão de Application Insights de aplicativos Web?

Se o SDK estiver instalado no momento da compilação, conforme mostrado neste artigo, você não precisará habilitar a [extensão de Application insights](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps) do portal do serviço de aplicativo. Mesmo que a extensão esteja instalada, ela será retirada quando detectar que o SDK já foi adicionado ao aplicativo. Se você habilitar Application Insights da extensão, não precisará instalar e atualizar o SDK. Porém, se você habilitar Application Insights seguindo as instruções neste artigo, terá mais flexibilidade porque:

   * Application Insights telemetria continuará a funcionar em:
       * Todos os sistemas operacionais, incluindo Windows, Linux e Mac.
       * Todos os modos de publicação, incluindo dependentes da estrutura ou independentes.
       * Todas as estruturas de destino, incluindo o .NET Framework completo.
       * Todas as opções de hospedagem, incluindo aplicativos Web, VMs, Linux, contêineres, serviço kubernetes do Azure e hospedagem não Azure.
       * Todas as versões do .NET Core, incluindo versões de visualização.
   * Você pode ver a telemetria localmente quando estiver Depurando do Visual Studio.
   * Você pode acompanhar a telemetria personalizada adicional usando `TrackXXX()` a API.
   * Você tem controle total sobre a configuração.

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>Posso habilitar o monitoramento de Application Insights usando ferramentas como Status Monitor?

Não. [Status monitor](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now) e [status monitor v2](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview) atualmente dão suporte apenas a ASP.NET 4. x.

### <a name="is-application-insights-automatically-enabled-for-my-aspnet-core-20-application"></a>O é Application Insights habilitado automaticamente para meu aplicativo ASP.NET Core 2,0?

O `Microsoft.AspNetCore.All` metapacote 2,0 incluiu o SDK do Application insights (versão 2.1.0). Se você executar o aplicativo no depurador do Visual Studio, o Visual Studio permitirá Application Insights e mostrará a telemetria localmente no próprio IDE. A telemetria não foi enviada para o serviço de Application Insights, a menos que uma chave de instrumentação tenha sido especificada. É recomendável seguir as instruções neste artigo para habilitar Application Insights, mesmo para aplicativos 2,0.

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>Se eu executar meu aplicativo no Linux, todos os recursos têm suporte?

Sim. O suporte a recursos para o SDK é o mesmo em todas as plataformas, com as seguintes exceções:

* O SDK coleta [contadores de eventos](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters) no Linux porque há suporte para os [contadores de desempenho](https://docs.microsoft.com/azure/azure-monitor/app/performance-counters) apenas no Windows. A maioria das métricas são as mesmas.
* Embora `ServerTelemetryChannel` o esteja habilitado por padrão, se o aplicativo estiver sendo executado no Linux ou no MacOS, o canal não criará automaticamente uma pasta de armazenamento local para manter a telemetria temporariamente se houver problemas de rede. Devido a essa limitação, a telemetria é perdida quando há problemas temporários de rede ou servidor. Para contornar esse problema, configure uma pasta local para o canal:

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
        services.AddApplicationInsightsTelemetry();
    }
```

### <a name="is-this-sdk-supported-for-the-new-net-core-3x-worker-service-template-applications"></a>Há suporte para este SDK para os novos aplicativos de modelo de serviço de trabalho do .NET Core 3. X?

Esse SDK requer `HttpContext`e, portanto, não funciona em nenhum aplicativo não http, incluindo os aplicativos do serviço de trabalho do .NET Core 3. X. Consulte [este](worker-service.md) documento para habilitar o Application insights em tais aplicativos, usando o SDK Microsoft. ApplicationInsights. WorkerService recentemente lançado.

## <a name="open-source-sdk"></a>SDK do código-fonte aberto

[Leia e colabore para o código](https://github.com/microsoft/ApplicationInsights-dotnet#recent-updates).

## <a name="next-steps"></a>Próximas etapas

* [Explore os fluxos de usuário](../../azure-monitor/app/usage-flows.md) para entender como os usuários navegam pelo aplicativo.
* [Configure uma coleção de instantâneos](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger) para ver o estado do código-fonte e as variáveis no momento em que uma exceção é lançada.
* [Use a API](../../azure-monitor/app/api-custom-events-metrics.md) para enviar seus próprios eventos e métricas para uma exibição detalhada do desempenho e do uso do seu aplicativo.
* Use os [Testes de disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md) para verificar seu aplicativo constante em todo o mundo.
* [Injeção de dependência no ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)
