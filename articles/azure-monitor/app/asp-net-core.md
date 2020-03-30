---
title: Insights do aplicativo Azure para aplicações ASP.NET Core | Microsoft Docs
description: Monitorar aplicativos web ASP.NET Core de disponibilidade, desempenho e uso.
ms.topic: conceptual
ms.date: 05/22/2019
ms.openlocfilehash: d6a0e507022452f1491e71651ba3bc8db3d1c090
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80284782"
---
# <a name="application-insights-for-aspnet-core-applications"></a>Insights de aplicativos para aplicativos ASP.NET Principais

Este artigo descreve como ativar o Application Insights para um aplicativo [ASP.NET Core.](https://docs.microsoft.com/aspnet/core) Quando você completar as instruções deste artigo, o Application Insights coletará solicitações, dependências, exceções, contadores de desempenho, batimentos cardíacos e logs do aplicativo ASP.NET Core.

O exemplo que usaremos aqui é um `netcoreapp2.2` [aplicativo MVC](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app) que tem como alvo . Você pode aplicar estas instruções a todas as aplicações ASP.NET Core.

## <a name="supported-scenarios"></a>Cenários com suporte

O [Application Insights SDK para ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) pode monitorar seus aplicativos, não importa onde ou como eles são executados. Se o aplicativo estiver em execução e tiver conectividade de rede com o Azure, a telemetria pode ser coletada. O monitoramento do Application Insights é suportado em todos os lugares que o .NET Core é suportado. Coberturas de suporte:
* **Sistema operacional**: Windows, Linux ou Mac.
* **Método de hospedagem**: Em processo ou fora de processo.
* **Método de implantação**: Quadro dependente ou autônomo.
* **Servidor web**: IIS (Internet Information Server) ou Kestrel.
* **Plataforma de hospedagem**: O recurso Web Apps do Azure App Service, Azure VM, Docker, Azure Kubernetes Service (AKS), e assim por diante.
* **Versão do .NET Core Runtime**: 1.XX, 2.XX ou 3.XX
* **IDE**: Visual Studio, VS Code ou linha de comando.

> [!NOTE]
> Se você estiver usando ASP.NET Core 3.X juntamente com o Application Insights, use a versão [2.8.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/2.8.0) ou superior. Esta é a única versão que suporta ASP.NET Core 3.X.

## <a name="prerequisites"></a>Pré-requisitos

- Um aplicativo ASP.NET Core em funcionamento. Se você precisar criar um aplicativo ASP.NET Core, siga este [tutorial ASP.NET Core](https://docs.microsoft.com/aspnet/core/getting-started/).
- Uma chave de instrumentação de Insights de aplicativo válida. Essa chave é necessária para enviar qualquer telemetria para o Application Insights. Se você precisar criar um novo recurso do Application Insights para obter uma chave de instrumentação, consulte [Criar um recurso do Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource).

## <a name="enable-application-insights-server-side-telemetry-visual-studio"></a>Habilitar a telemetria do lado do servidor do Application Insights (Visual Studio)

1. Abra o projeto no Visual Studio.

    > [!TIP]
    > Se você quiser, você pode configurar o controle de origem para o seu projeto para que você possa acompanhar todas as alterações que o Application Insights faz. Para habilitar o controle de origem, selecione **Adicionar arquivo** > **ao controle de origem**.

2. Selecione **a** > **telemetria**de insights do aplicativo .

3. Selecione **Começar**. O texto desta seleção pode variar, dependendo da sua versão do Visual Studio. Algumas versões anteriores usam um botão **Start Free** em vez disso.

4. Selecione sua assinatura. Em seguida, selecione **Registro de** > **recursos**.

5. Depois de adicionar insights de aplicativos ao seu projeto, verifique se você está usando a versão estável mais recente do SDK. Vá para **o Projeto** > **Gerenciar Pacotes** > NuGet**Microsoft.ApplicationInsights.AspNetCore**. Se você precisar, escolha **Atualizar**.

     ![Captura de tela mostrando onde selecionar o pacote Descriteres de Aplicativos para atualização](./media/asp-net-core/update-nuget-package.png)

6. Se você seguiu a dica opcional e adicionou seu projeto ao controle de origem, vá para **Exibir** > **alterações do Explorador de** > **equipe**. Em seguida, selecione cada arquivo para ver uma visão difusa das alterações feitas pela telemetria Application Insights.

## <a name="enable-application-insights-server-side-telemetry-no-visual-studio"></a>Habilitar a telemetria do lado do servidor do Application Insights (sem Visual Studio)

1. Instale o [pacote SDK NuGet](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)do Application Insights para ASP.NET Core . Recomendamos que você use sempre a versão estável mais recente. Encontre notas de versão completas para o SDK no [repo gitHub de código aberto](https://github.com/Microsoft/ApplicationInsights-aspnetcore/releases).

    A amostra de código a seguir mostra as `.csproj` alterações a serem adicionadas ao arquivo do seu projeto.

    ```xml
        <ItemGroup>
          <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.13.1" />
        </ItemGroup>
    ```

2. Adicione `services.AddApplicationInsightsTelemetry();` ao `ConfigureServices()` método `Startup` em sua classe, como neste exemplo:

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

    Embora você possa fornecer a chave `AddApplicationInsightsTelemetry`de instrumentação como argumento para , recomendamos que você especifique a chave de instrumentação na configuração. A amostra de código a seguir mostra `appsettings.json`como especificar uma chave de instrumentação em . Certifique-se de que `appsettings.json` está copiado para a pasta raiz do aplicativo durante a publicação.

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

    Alternativamente, especifique a chave de instrumentação em qualquer uma das seguintes variáveis de ambiente:

    * `APPINSIGHTS_INSTRUMENTATIONKEY`

    * `ApplicationInsights:InstrumentationKey`

    Por exemplo: 

    * `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`

    * `SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

    Normalmente, `APPINSIGHTS_INSTRUMENTATIONKEY` especifica a chave de instrumentação para aplicativos implantados no Azure Web Apps.

    > [!NOTE]
    > Uma chave de instrumentação especificada no `APPINSIGHTS_INSTRUMENTATIONKEY`código ganha sobre a variável ambiente , o que ganha sobre outras opções.

## <a name="run-your-application"></a>Execute seu aplicativo.

Execute sua inscrição e faça solicitações a ele. A telemetria agora deve fluir para o Application Insights. O Application Insights SDK coleta automaticamente solicitações web recebidas para o seu aplicativo, juntamente com a seguinte telemetria também.

### <a name="live-metrics"></a>Métricas ao vivo

[Métricas ao vivo](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream) podem ser usadas para verificar rapidamente se o monitoramento do Application Insights está configurado corretamente. Embora possa levar alguns minutos até que a telemetria comece a aparecer no portal e análises, o Live Metrics mostraria o uso da CPU do processo de execução em quase tempo real. Ele também pode mostrar outras telemetrias como Solicitações, Dependências, Traços, etc.

### <a name="ilogger-logs"></a>Registros iLogger

Os registros emitidos por `ILogger` `Warning` gravidade ou superior são automaticamente capturados. Siga [os docs do ILogger](ilogger.md#control-logging-level) para personalizar quais níveis de log são capturados pelo Application Insights.

### <a name="dependencies"></a>Dependências

A coleta de dependência é ativada por padrão. [Este](asp-net-dependencies.md#automatically-tracked-dependencies) artigo explica as dependências que são coletadas automaticamente, e também contém etapas para fazer o rastreamento manual.

### <a name="performance-counters"></a>Contadores de desempenho

O suporte para [contadores de desempenho](https://azure.microsoft.com/documentation/articles/app-insights-web-monitor-performance/) no ASP.NET Core é limitado:

* As versões 2.4.1 do SDK e posteriormente coletam contadores de desempenho se o aplicativo estiver sendo executado no Azure Web Apps (Windows).
* As versões 2.7.1 do SDK e posteriormente coletam `NETSTANDARD2.0` contadores de desempenho se o aplicativo estiver em execução no Windows e alvos ou posteriores.
* Para aplicativos direcionados ao .NET Framework, todas as versões dos contadores de desempenho de suporte do SDK.
* As versões 2.8.0 do SDK e posteriormente suportam o contador de cpu/memória no Linux. Nenhum outro contador é suportado no Linux. A maneira recomendada de obter contadores de sistema no Linux (e outros ambientes não-Windows) é usando [EventCounters](#eventcounter)

### <a name="eventcounter"></a>Contador de Eventos

`EventCounterCollectionModule`é ativado por padrão e coletará um conjunto padrão de contadores de aplicativos .NET Core 3.X. O tutorial [EventCounter](eventcounters.md) lista o conjunto padrão de contadores coletados. Ele também tem instruções sobre a personalização da lista.

## <a name="enable-client-side-telemetry-for-web-applications"></a>Habilite a telemetria do lado do cliente para aplicativos web

As etapas anteriores são suficientes para ajudá-lo a começar a coletar telemetria do lado do servidor. Se o aplicativo tiver componentes do lado do cliente, siga os próximos passos para começar a coletar [telemetria de uso](https://docs.microsoft.com/azure/azure-monitor/app/usage-overview).

1. In, `_ViewImports.cshtml`adicionar injeção:

    ```cshtml
        @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
    ```

2. `_Layout.cshtml`Insira `HtmlHelper` no final `<head>` da seção, mas antes de qualquer outro script. Se você quiser relatar qualquer telemetria JavaScript personalizada da página, injete-a após este trecho:

    ```cshtml
        @Html.Raw(JavaScriptSnippet.FullScript)
        </head>
    ```
    
Alternativamente, o `FullScript` `ScriptBody` uso do está disponível a partir de SDK v2.14. Use isso se precisar `<script>` controlar a tag para definir uma Política de Segurança de Conteúdo:

    ```cshtml
        <script> // apply custom changes to this script tag.
            @Html.Raw(JavaScriptSnippet.ScriptBody)
        </script>
    ```

Os `.cshtml` nomes dos arquivos mencionados anteriormente são de um modelo de aplicativo MVC padrão. Em última análise, se você quiser habilitar adequadamente o monitoramento do lado do `<head>` cliente para o seu aplicativo, o trecho JavaScript deve aparecer na seção de cada página do seu aplicativo que você deseja monitorar. Você pode alcançar esse objetivo para este modelo de `_Layout.cshtml`aplicativo adicionando o trecho JavaScript para . 

Se o seu projeto `_Layout.cshtml`não incluir, você ainda pode adicionar [monitoramento do lado do cliente](https://docs.microsoft.com/azure/azure-monitor/app/website-monitoring). Você pode fazer isso adicionando o trecho JavaScript a `<head>` um arquivo equivalente que controla todas as páginas dentro do seu aplicativo. Ou você pode adicionar o trecho a várias páginas, mas esta solução é difícil de manter e geralmente não recomendamos isso.

## <a name="configure-the-application-insights-sdk"></a>Configure o Application Insights SDK

Você pode personalizar o Application Insights SDK para ASP.NET Core para alterar a configuração padrão. Os usuários do Application Insights ASP.NET SDK podem `ApplicationInsights.config` estar familiarizados com a alteração da configuração usando ou modificando `TelemetryConfiguration.Active`. Você altera a configuração de forma diferente para ASP.NET Core. Adicione o ASP.NET Core SDK ao aplicativo e configure-o usando ASP.NET injeção de [dependência](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)incorporada do Core . Faça quase todas as `ConfigureServices()` alterações `Startup.cs` de configuração no método da sua classe, a menos que você seja direcionado para o contrário. As seções a seguir oferecem mais informações.

> [!NOTE]
> Em ASP.NET aplicativos Core, a `TelemetryConfiguration.Active` alteração da configuração modificando não é suportada.

### <a name="using-applicationinsightsserviceoptions"></a>Usando opções de serviço do ApplicationInsights

Você pode modificar algumas configurações `ApplicationInsightsServiceOptions` `AddApplicationInsightsTelemetry`comuns passando para , como neste exemplo:

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
|EnablePerformanceCountercollection  | Ativar/desativar`PerformanceCounterCollectionModule` | true
|HabilitarRequestTrackingTelemetryModule   | Ativar/desativar`RequestTrackingTelemetryModule` | true
|HabilitarEventCountercollection   | Ativar/desativar`EventCounterCollectionModule` | true
|AplicativoDependencyTrackingTelemetryModule   | Ativar/desativar`DependencyTrackingTelemetryModule` | true
|Módulo de visualização do EnableAppServicesHeartbeatTelemettry  |  Ativar/desativar`AppServicesHeartbeatTelemetryModule` | true
|HabilitarAzureInstanceMetadataTelemettryModule   |  Ativar/desativar`AzureInstanceMetadataTelemetryModule` | true
|Habilite o QuickPulseMetricStream | Ativar/desativar o recurso LiveMetrics | true
|HabilitaraaaaAmostragem Adaptativa | Ativar/desativar amostragem adaptativa | true
|Habilitarheartbeat | Habilitar/desativar o recurso Heartbeats, que periodicamente (15 min padrão) envia uma métrica personalizada chamada 'HeartBeatState' com informações sobre o tempo de execução como .NET Version, Azure Environment information, se aplicável, etc. | true
|Adicionarautométrico coletado automático | Ativar/desativar o extrator AutoCollectedMetrics, que é um processador de telemetria que envia métricas pré-agregadas sobre Solicitações/Dependências antes que a amostragem ocorra. | true
|RequestCollectionOptions.TrackExceptions | Habilitar/desativar o relatório de rastreamento de exceção não manipulado pelo módulo de coleta de solicitações. | falso no NETSTANDARD2.0 (porque as exceções são rastreadas com o ApplicationInsightsLoggerProvider), verdade de outra forma.

Consulte as [ `ApplicationInsightsServiceOptions` configurações configuráveis](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/NETCORE/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs) para obter a lista mais atualizada.

### <a name="sampling"></a>amostragem

O Application Insights SDK for ASP.NET Core suporta amostragem de taxa fixa e adaptativa. A amostragem adaptativa é ativada por padrão. 

Para obter mais informações, consulte [Configure amostragem adaptativa para aplicações ASP.NET Core](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications).

### <a name="adding-telemetryinitializers"></a>Adicionando telemetriainicializador

Use [iniciadores de telemetria](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#addmodify-properties-itelemetryinitializer) quando quiser definir propriedades globais que são enviadas com toda a telemetria.

Adicione qualquer `TelemetryInitializer` novidade `DependencyInjection` ao recipiente, conforme mostrado no código a seguir. O SDK pega automaticamente `TelemetryInitializer` qualquer um que `DependencyInjection` for adicionado ao recipiente.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
}
```

### <a name="removing-telemetryinitializers"></a>Removendo iniciadores de telemetria

Iniciadores de telemetria estão presentes por padrão. Para remover todos ou iniciadores específicos de telemetria, `AddApplicationInsightsTelemetry()`use o seguinte código de amostra *após* a chamada .

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

Você pode adicionar processadores de `TelemetryConfiguration` telemetria `AddApplicationInsightsTelemetryProcessor` personalizados usando o método de extensão em `IServiceCollection`. Você usa processadores de telemetria em [cenários avançados de filtragem](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#itelemetryprocessor-and-itelemetryinitializer). Use o exemplo a seguir.

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

### <a name="configuring-or-removing-default-telemetrymodules"></a>Configuração ou remoção de módulos de telemetria padrão

O Application Insights usa módulos de telemetria para coletar automaticamente telemetria útil sobre cargas de trabalho específicas sem exigir acompanhamento manual pelo usuário.

Os seguintes módulos de coleta automática são habilitados por padrão. Esses módulos são responsáveis pela coleta automática da telemetria. Você pode desabilitá-los ou configurá-los para alterar seu comportamento padrão.

* `RequestTrackingTelemetryModule`- Coleta solicitaçãoTelemetria a partir de solicitações web recebidas.
* `DependencyTrackingTelemetryModule`- Coleta dependênciaTelemetria de chamadas http e chamadas sql.
* `PerformanceCollectorModule`- Coleta o Desempenho do WindowsCounters.
* `QuickPulseTelemetryModule`- Coleta telemetria para exibição no portal Métricas Ao Vivo.
* `AppServicesHeartbeatTelemetryModule`- Coleta batimentos cardíacos (que são enviados como métricas personalizadas), sobre o ambiente azure App Service, onde o aplicativo está hospedado.
* `AzureInstanceMetadataTelemetryModule`- Coleta batimentos cardíacos (que são enviados como métricas personalizadas), sobre o ambiente Azure VM onde o aplicativo está hospedado.
* `EventCounterCollectionModule`- Coleciona [Contadores de Eventos.](eventcounters.md) Este módulo é um novo recurso e está disponível na versão 2.8.0 do SDK ou superior.

Para configurar qualquer `TelemetryModule`padrão, use `ConfigureTelemetryModule<T>` `IServiceCollection`o método de extensão ligado, conforme mostrado no exemplo a seguir.

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

A partir da versão 2.12.2, [`ApplicationInsightsServiceOptions`](#using-applicationinsightsserviceoptions) contém opção fácil para desativar qualquer um dos módulos padrão.

### <a name="configuring-a-telemetry-channel"></a>Configurando um canal de telemetria

O canal `ServerTelemetryChannel`padrão é . Você pode substituí-lo como o exemplo a seguir mostra.

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

### <a name="disable-telemetry-dynamically"></a>Desativar a telemetria dinamicamente

Se você quiser desativar a telemetria de forma `TelemetryConfiguration` condicional e dinâmica, você pode resolver a `DisableTelemetry` instância com ASP.NET recipiente de injeção de dependência core em qualquer lugar do seu código e definir o sinalizador nele.

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

O acima não impede que nenhum módulo de coleta automática seja coletado de telemetria. Apenas o envio de telemetria para o Application Insights é desativado com a abordagem acima. Se um módulo de coleta de automóveis em particular não for desejado, é melhor [remover o módulo de telemetria](#configuring-or-removing-default-telemetrymodules)

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="does-application-insights-support-aspnet-core-3x"></a>O Application Insights suporta ASP.NET Core 3.X?

Sim. Atualize para [o Application Insights SDK para ASP.NET versão Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 2.8.0 ou superior. As versões mais antigas do SDK não suportam ASP.NET Core 3.X.

Além disso, se você estiver usando instruções baseadas no Visual Studio [daqui,](#enable-application-insights-server-side-telemetry-visual-studio)atualize para a versão mais recente do Visual Studio 2019 (16.3.0) a bordo. As versões anteriores do Visual Studio não suportam o onboarding automático para ASP.NET aplicativos Core 3.X.

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>Como posso rastrear a telemetria que não é coletada automaticamente?

Obtenha uma `TelemetryClient` instância de usando injeção de `TrackXXX()` construtor, e chame o método necessário sobre ele. Não recomendamos a `TelemetryClient` criação de novas instâncias em um aplicativo ASP.NET Core. Uma instância `TelemetryClient` de singleton já `DependencyInjection` está registrada `TelemetryConfiguration` no contêiner, que compartilha com o resto da telemetria. A criação `TelemetryClient` de uma nova instância só é recomendada se ela precisar de uma configuração separada do resto da telemetria.

O exemplo a seguir mostra como rastrear telemetria adicional de um controlador.

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

Para obter mais informações sobre relatórios de dados personalizados no Application Insights, consulte [a referência de API de métricas personalizadas do Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics/).

### <a name="some-visual-studio-templates-used-the-useapplicationinsights-extension-method-on-iwebhostbuilder-to-enable-application-insights-is-this-usage-still-valid"></a>Alguns modelos do Visual Studio usaram o método de extensão UseApplicationInsights() no IWebHostBuilder para habilitar o Application Insights. Esse uso ainda é válido?

Embora o `UseApplicationInsights()` método de extensão ainda seja suportado, ele é marcado como obsoleto na versão 2.8.0 do Application Insights SDK em diante. Ele será removido na próxima versão principal do SDK. A maneira recomendada de ativar a telemetria `AddApplicationInsightsTelemetry()` application Insights é usando porque fornece sobrecargas para controlar alguma configuração. Além disso, em ASP.NET aplicativos `services.AddApplicationInsightsTelemetry()` Core 3.X, é a única maneira de habilitar insights de aplicativos.

### <a name="im-deploying-my-aspnet-core-application-to-web-apps-should-i-still-enable-the-application-insights-extension-from-web-apps"></a>Estou implantando meu aplicativo ASP.NET Core para Web Apps. Devo ainda habilitar a extensão Application Insights a partir de Aplicativos Web?

Se o SDK estiver instalado no tempo de compilação, como mostrado neste artigo, você não precisará habilitar a [extensão do Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps) a partir do portal App Service. Mesmo que a extensão esteja instalada, ela recuará quando detectar que o SDK já está adicionado ao aplicativo. Se você habilitar o Application Insights a partir da extensão, não será preciso instalar e atualizar o SDK. Mas se você habilitar o Application Insights seguindo instruções neste artigo, você terá mais flexibilidade porque:

   * A telemetria do Application Insights continuará funcionando em:
       * Todos os sistemas operacionais, incluindo Windows, Linux e Mac.
       * Todos os modos de publicação, incluindo autônomos ou dependentes de quadros.
       * Todos os frameworks de destino, incluindo o Framework .NET completo.
       * Todas as opções de hospedagem, incluindo Aplicativos Web, VMs, Linux, contêineres, Serviço Azure Kubernetes e hospedagem não-Azure.
       * Todas as versões do .NET Core, incluindo versões de pré-visualização.
   * Você pode ver a telemetria localmente quando estiver depurando do Visual Studio.
   * Você pode rastrear telemetria personalizada `TrackXXX()` adicional usando a API.
   * Você tem controle total sobre a configuração.

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>Posso habilitar o monitoramento do Application Insights usando ferramentas como o Monitor de Status?

Não. [O Monitor de Status](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now) e [o Monitor de Status v2](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview) atualmente suportam apenas ASP.NET 4.x.

### <a name="is-application-insights-automatically-enabled-for-my-aspnet-core-20-application"></a>O Application Insights está automaticamente habilitado para o meu aplicativo ASP.NET Core 2.0?

O `Microsoft.AspNetCore.All` metapacote 2.0 incluiu o Application Insights SDK (versão 2.1.0). Se você executar o aplicativo em Visual Studio depurador, o Visual Studio habilita o Application Insights e mostra a telemetria localmente no próprio IDE. A telemetria não foi enviada ao serviço Application Insights a menos que uma chave de instrumentação tenha sido especificada. Recomendamos seguir as instruções deste artigo para habilitar o Application Insights, mesmo para aplicativos 2.0.

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>Se eu executar meu aplicativo no Linux, todos os recursos são suportados?

Sim. O suporte a recursos para o SDK é o mesmo em todas as plataformas, com as seguintes exceções:

* O SDK coleta contadores [de eventos](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters) no Linux porque contadores [de desempenho](https://docs.microsoft.com/azure/azure-monitor/app/performance-counters) são suportados apenas no Windows. A maioria das métricas são as mesmas.
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
        services.AddApplicationInsightsTelemetry();
    }
```

### <a name="is-this-sdk-supported-for-the-new-net-core-3x-worker-service-template-applications"></a>Este SDK é suportado para os novos aplicativos de modelo de serviço do trabalhador .NET Core 3.X?

Este SDK `HttpContext`requer e, portanto, não funciona em nenhum aplicativo não-HTTP, incluindo os aplicativos de serviço ao trabalhador .NET Core 3.X. Consulte [este](worker-service.md) documento para habilitar insights de aplicativos em tais aplicativos, usando o recém-lançado Microsoft.ApplicationInsights.WorkerService SDK.

## <a name="open-source-sdk"></a>SDK do código-fonte aberto

[Leia e contribua para o código.](https://github.com/microsoft/ApplicationInsights-dotnet#recent-updates)

## <a name="video"></a>Vídeo

- Confira este vídeo passo a passo externo para configurar o [Application Insights com o .NET Core e o Visual Studio](https://www.youtube.com/watch?v=NoS9UhcR4gA&t) do zero.
- Confira este vídeo passo-a-passo externo para [configurar o Application Insights com .NET Core e Visual Studio Code](https://youtu.be/ygGt84GDync) do zero.

## <a name="next-steps"></a>Próximas etapas

* [Explore os fluxos do usuário](../../azure-monitor/app/usage-flows.md) para entender como os usuários navegam através do seu aplicativo.
* [Configure uma coleção de instantâneos](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger) para ver o estado do código-fonte e variáveis no momento em que uma exceção é lançada.
* [Use a API](../../azure-monitor/app/api-custom-events-metrics.md) para enviar seus próprios eventos e métricas para uma visão detalhada do desempenho e uso do seu aplicativo.
* Use os [Testes de disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md) para verificar seu aplicativo constante em todo o mundo.
* [Injeção de dependência no núcleo ASP.NET](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)
