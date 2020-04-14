---
title: Amostragem de telemetria no Azure Application Insights | Microsoft Docs
description: Como manter o volume de telemetria sob controle.
ms.topic: conceptual
ms.date: 01/17/2020
ms.reviewer: vitalyg
ms.custom: fasttrack-edit
ms.openlocfilehash: 5e888e0606b7a9bcd9a7a94c28455d705c5f1bec
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255474"
---
# <a name="sampling-in-application-insights"></a>Amostragem no Application Insights

A amostragem é um recurso do [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md). É a maneira recomendada de reduzir o tráfego de telemetria, os custos de dados e os custos de armazenamento, preservando uma análise estatisticamente correta dos dados do aplicativo. A amostragem também ajuda a evitar que os Insights do aplicativo estrangulem sua telemetria. O filtro de amostragem seleciona itens relacionados, para que você possa navegar entre itens quando estiver fazendo investigações de diagnóstico.

Quando as contagens métricas são apresentadas no portal, elas são renormalizadas para levar em conta a amostragem. Fazer isso minimiza qualquer efeito nas estatísticas.

## <a name="brief-summary"></a>Breve resumo

* Existem três tipos diferentes de amostragem: amostragem adaptativa, amostragem de taxa fixa e amostragem de ingestão.
* A amostragem adaptativa é habilitada por padrão em todas as versões mais recentes do Application Insights ASP.NET e ASP.NET Core Software Development Kits (SDKs). Também é usado pelo [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview).
* A amostragem de taxa fixa está disponível em versões recentes dos SDKs Application Insights para ASP.NET, ASP.NET Core, Java (tanto o agente quanto o SDK) e Python.
* A amostragem de ingestão funciona no ponto final do serviço Application Insights. Só se aplica quando nenhuma outra amostragem está em vigor. Se o SDK coletar sua telemetria, a amostragem de ingestão será desativada.
* Para aplicações web, se você registrar eventos personalizados e precisar garantir que um conjunto de eventos `OperationId` seja retido ou descartado em conjunto, os eventos devem ter o mesmo valor.
* Se você escrever consultas de Análise, deverá [levar em conta a amostragem](../../azure-monitor/log-query/aggregations.md). Em particular, em vez de simplesmente contar registros, você deve usar `summarize sum(itemCount)`.
* Alguns tipos de telemetria, incluindo métricas de desempenho e métricas personalizadas, são sempre mantidos independentemente de a amostragem estar ativada ou não.

A tabela a seguir resume os tipos de amostragem disponíveis para cada SDK e o tipo de aplicação:

| SDK do Application Insights | Amostragem adaptativa suportada | Amostragem de taxa fixa suportada | Amostragem de ingestão suportada |
|-|-|-|-|
| ASP.NET | [Sim (ligado por padrão)](#configuring-adaptive-sampling-for-aspnet-applications) | [Sim](#configuring-fixed-rate-sampling-for-aspnet-applications) | Só se nenhuma outra amostragem estiver em vigor |
| ASP.NET Core | [Sim (ligado por padrão)](#configuring-adaptive-sampling-for-aspnet-core-applications) | [Sim](#configuring-fixed-rate-sampling-for-aspnet-core-applications) | Só se nenhuma outra amostragem estiver em vigor |
| Funções do Azure | [Sim (ligado por padrão)](#configuring-adaptive-sampling-for-azure-functions) | Não | Só se nenhuma outra amostragem estiver em vigor |
| Java | Não | [Sim](#configuring-fixed-rate-sampling-for-java-applications) | Só se nenhuma outra amostragem estiver em vigor |
| Python | Não | [Sim](#configuring-fixed-rate-sampling-for-opencensus-python-applications) | Só se nenhuma outra amostragem estiver em vigor |
| Todos os outros | Não | Não | [Sim](#ingestion-sampling) |

> [!NOTE]
> As informações na maior parte desta página se aplicam às versões atuais dos SDKs do Application Insights. Para obter informações sobre versões mais antigas dos SDKs, [consulte a seção abaixo](#older-sdk-versions).

## <a name="types-of-sampling"></a>Tipos de amostragem

Existem três métodos de amostragem diferentes:

* **A amostragem adaptativa** ajusta automaticamente o volume da telemetria enviada do SDK em seu aplicativo ASP.NET/ASP.NET Core e a partir de Funções Azure. Esta é a amostragem padrão quando você usa o ASP.NET ou ASP.NET Core SDK. Atualmente, a amostragem adaptativa está disponível apenas para ASP.NET telemetria do lado do servidor e para funções do Azure.

* **A amostragem de taxa fixa** reduz o volume de telemetria enviada tanto do seu ASP.NET ou do servidor Core ou Java ASP.NET quanto dos navegadores de seus usuários. Você define a taxa. O cliente e o servidor sincronizarão suas amostragens para que, na Pesquisa, você possa navegar entre exibições de página e solicitações relacionadas.

* **A amostragem de ingestão** acontece no ponto final do serviço Application Insights. Ela descarta parte da telemetria que chega em seu aplicativo, na taxa de amostragem que você definir. Ela não reduz o tráfego de telemetria enviado do seu aplicativo, mas ajuda você a se manter em sua cota mensal. A principal vantagem da amostragem de ingestão é que você pode definir a taxa de amostragem sem reimplantar seu aplicativo. A amostragem de ingestão funciona uniformemente para todos os servidores e clientes, mas não se aplica quando qualquer outro tipo de amostragem está em operação.

> [!IMPORTANT]
> Se os métodos adaptativos ou de amostragem de taxa fixa estiverem em operação, a amostragem de ingestão será desativada.

## <a name="adaptive-sampling"></a>amostragem adaptável

A amostragem adaptável afeta o volume de telemetria enviado do seu aplicativo de servidor Web para o end-point do serviço Application Insights.

> [!TIP]
> A amostragem adaptativa é ativada por padrão quando você usa o SDK ASP.NET ou o ASP.NET Core SDK, e também é habilitado por padrão para funções do Azure.

O volume é ajustado automaticamente para manter dentro de uma taxa máxima `MaxTelemetryItemsPerSecond`especificada de tráfego e é controlado através da configuração . Se o aplicativo produzir uma baixa quantidade de telemetria, como quando depuração ou devido ao baixo uso, os itens `MaxTelemetryItemsPerSecond`não serão descartados pelo processador de amostragem enquanto o volume estiver abaixo . À medida que o volume da telemetria aumenta, a taxa de amostragem é ajustada de modo a atingir o volume-alvo. O ajuste é recalculado em intervalos regulares e tem base em uma média móvel da taxa de transmissão de saída.

Para atingir o volume de destino, parte da telemetria gerada é descartada. Assim como os outros tipos de amostragem, o algoritmo retém os itens de telemetria relacionados. Por exemplo, quando você estiver analisando a telemetria na Pesquisa, poderá localizar a solicitação relacionada a uma exceção específica.

As contagens de métrica, como a taxa de solicitações e a taxa de exceções são ajustadas para compensar a taxa de amostragem, para que mostrem valores aproximadamente corretos no Gerenciador de Métricas.

### <a name="configuring-adaptive-sampling-for-aspnet-applications"></a>Configuração de amostragem adaptativa para aplicações ASP.NET

> [!NOTE]
> Esta seção se aplica a aplicativos ASP.NET, não a aplicativos ASP.NET Core. [Saiba mais sobre a configuração da amostragem adaptativa para ASP.NET aplicativos Core mais tarde neste documento.](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications)

Em [`ApplicationInsights.config`](../../azure-monitor/app/configuration-with-applicationinsights-config.md), você pode ajustar `AdaptiveSamplingTelemetryProcessor` vários parâmetros no nó. Os números mostrados são os valores padrão:

* `<MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>`
  
    A taxa de destino desejada pelo algoritmo adaptável **em cada host de servidor**. Se o seu aplicativo Web for executado em muitos hosts, reduza esse valor para permanecer dentro de sua taxa de destino de tráfego no portal do Application Insights.

* `<EvaluationInterval>00:00:15</EvaluationInterval>` 
  
    O intervalo em que a taxa atual de telemetria é reavaliada. Avaliação é executada como uma média móvel. Talvez você queira reduzir esse intervalo se a sua telemetria estiver sujeita a picos repentinos.

* `<SamplingPercentageDecreaseTimeout>00:02:00</SamplingPercentageDecreaseTimeout>`
  
    Quando o valor percentual amostral muda, quanto tempo depois é permitido baixar o percentual amostral novamente para capturar menos dados?

* `<SamplingPercentageIncreaseTimeout>00:15:00</SamplingPercentageIncreaseTimeout>`
  
    Quando o valor percentual da amostragem muda, quanto tempo depois podemos aumentar o percentual amostral novamente para capturar mais dados?

* `<MinSamplingPercentage>0.1</MinSamplingPercentage>`
  
    Como a porcentagem de amostragem varia, qual é o valor mínimo que podemos definir?

* `<MaxSamplingPercentage>100.0</MaxSamplingPercentage>`
  
    Como a porcentagem de amostragem varia, qual é o valor máximo que podemos definir?

* `<MovingAverageRatio>0.25</MovingAverageRatio>` 
  
    No cálculo da média móvel, isso especifica o peso que deve ser atribuído ao valor mais recente. Use um valor menor ou igual a 1. Valores menores tornam o algoritmo menos reativo a mudanças repentinas.

* `<InitialSamplingPercentage>100</InitialSamplingPercentage>`
  
    A quantidade de telemetria para provar quando o aplicativo acabou de começar. Não reduza esse valor enquanto estiver depurando.

* `<ExcludedTypes>Trace;Exception</ExcludedTypes>`
  
    Uma lista delimitada de ponto e vírgula de tipos que você não deseja estar sujeito à amostragem. Os tipos `Dependency`reconhecidos são: `Event`, , `Exception`, `PageView` `Request`, , . `Trace` Toda a telemetria dos tipos especificados é transmitida; os tipos que não forem especificados serão amostrados.

* `<IncludedTypes>Request;Dependency</IncludedTypes>`
  
    Uma lista delimitada de ponto e vírgula de tipos que você deseja sujeitar à amostragem. Os tipos `Dependency`reconhecidos são: `Event`, , `Exception`, `PageView` `Request`, , . `Trace` Os tipos especificados serão amostrados; toda a telemetria dos outros tipos será sempre transmitida.

**Para desligar** a amostragem `AdaptiveSamplingTelemetryProcessor` adaptativa, remova `ApplicationInsights.config`os nós de .

#### <a name="alternative-configure-adaptive-sampling-in-code"></a>Alternativa: Configurar amostragem adaptativa em código

Em vez de definir o `.config` parâmetro de amostragem no arquivo, você pode definir programáticamente esses valores.

1. Remova todos `AdaptiveSamplingTelemetryProcessor` os nós do `.config` arquivo.
2. Use o seguinte trecho para configurar a amostragem adaptativa:

    ```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.Channel.Implementation;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    
    // ...

    var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
    // For older versions of the Application Insights SDK, use the following line instead:
    // var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;

    // Enable AdaptiveSampling so as to keep overall telemetry volume to 5 items per second.
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5);

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();
    ```

    ([Saiba mais sobre os processadores de telemetria](../../azure-monitor/app/api-filtering-sampling.md#filtering).)

Você também pode ajustar a taxa de amostragem para cada tipo de telemetria individualmente, ou até mesmo excluir certos tipos de serem amostrados:

```csharp
// The following configures adaptive sampling with 5 items per second, and also excludes Dependency telemetry from being subjected to sampling.
builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");
```

### <a name="configuring-adaptive-sampling-for-aspnet-core-applications"></a>Configuração de amostragem adaptativa para aplicações ASP.NET Core

Não há `ApplicationInsights.config` para ASP.NET aplicativos Core, então toda configuração é feita via código.
A amostragem adaptável está habilitada por padrão para todos os aplicativos ASP.NET Core. É possível desabilitar ou personalizar o comportamento de amostragem.

#### <a name="turning-off-adaptive-sampling"></a>Desligando a amostragem adaptativa

O recurso de amostragem padrão pode ser desativado `ConfigureServices`ao `ApplicationInsightsServiceOptions` adicionar `Startup.cs` o serviço Application Insights, no método, usando dentro do arquivo:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ...

    var aiOptions = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
    aiOptions.EnableAdaptiveSampling = false;
    services.AddApplicationInsightsTelemetry(aiOptions);

    //...
}
```

O código acima desativará a amostragem adaptativa. Siga as etapas abaixo para adicionar a amostragem com mais opções de personalização.

#### <a name="configure-sampling-settings"></a>Configurar configurações de amostragem

Use os métodos de extensão de `TelemetryProcessorChainBuilder` conforme mostrado abaixo para personalizar o comportamento de amostragem.

> [!IMPORTANT]
> Se você usar este método para configurar a `aiOptions.EnableAdaptiveSampling` amostragem, certifique-se de definir a propriedade para `false` quando ligar `AddApplicationInsightsTelemetry()`.

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, TelemetryConfiguration configuration)
{
    var builder = configuration.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
    // For older versions of the Application Insights SDK, use the following line instead:
    // var builder = configuration.TelemetryProcessorChainBuilder;

    // Using adaptive sampling
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5);

    // Alternately, the following configures adaptive sampling with 5 items per second, and also excludes DependencyTelemetry from being subject to sampling.
    // builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));
    
    builder.Build();

    // ...
}
```

### <a name="configuring-adaptive-sampling-for-azure-functions"></a>Configuração de amostragem adaptativa para funções azure

Siga as instruções [desta página](https://docs.microsoft.com/azure/azure-functions/functions-monitoring#configure-sampling) para configurar a amostragem adaptativa para aplicativos em execução em Funções Azure.

## <a name="fixed-rate-sampling"></a>Amostragem de taxa fixa

A amostragem de taxa fixa reduz o tráfego enviado do seu servidor web e navegadores da Web. Ao contrário da amostragem adaptável, ela reduz a telemetria a uma taxa fixa decidida por você. A amostragem de taxa fixa está disponível para aplicações ASP.NET, ASP.NET Core, Java e Python.

Como outras técnicas de amostragem, isso também retém itens relacionados. Ele também sincroniza a amostragem do cliente e do servidor para que os itens relacionados sejam retidos - por exemplo, quando você olha para uma exibição de página na Pesquisa, você pode encontrar suas solicitações de servidor relacionadas. 

No Metrics Explorer, as taxas como as contagens de solicitações e de exceções são multiplicadas por um fator para compensar a taxa de amostragem, para que elas sejam aproximadamente corretas.

### <a name="configuring-fixed-rate-sampling-for-aspnet-applications"></a>Configuração de amostragem de taxa fixa para aplicações ASP.NET

1. **Desabilite a amostragem adaptativa**: Em [`ApplicationInsights.config`](../../azure-monitor/app/configuration-with-applicationinsights-config.md), remover ou comentar o `AdaptiveSamplingTelemetryProcessor` nó.

    ```xml
    <TelemetryProcessors>
        <!-- Disabled adaptive sampling:
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
        </Add>
        -->
    ```

2. **Habilitar o módulo de amostragem de taxa fixa.** Adicione este trecho [`ApplicationInsights.config`](../../azure-monitor/app/configuration-with-applicationinsights-config.md)a:
   
    ```XML
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <!-- Set a percentage close to 100/N where N is an integer. -->
            <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
            <SamplingPercentage>10</SamplingPercentage>
        </Add>
    </TelemetryProcessors>
    ```

      Alternativamente, em vez de definir `ApplicationInsights.config` o parâmetro de amostragem no arquivo, você pode definir programaticamente esses valores:

    ```csharp
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

    // ...

    var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
    // For older versions of the Application Insights SDK, use the following line instead:
    // var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;

    builder.UseSampling(10.0); // percentage

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();
    ```

    ([Saiba mais sobre os processadores de telemetria](../../azure-monitor/app/api-filtering-sampling.md#filtering).)

### <a name="configuring-fixed-rate-sampling-for-aspnet-core-applications"></a>Configuração de amostragem de taxa fixa para aplicações ASP.NET Core

1. **Desativar amostragem adaptativa**: Alterações podem ser feitas no `ConfigureServices` método, usando `ApplicationInsightsServiceOptions`:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // ...

        var aiOptions = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
        aiOptions.EnableAdaptiveSampling = false;
        services.AddApplicationInsightsTelemetry(aiOptions);

        //...
    }
    ```

2. **Habilitar o módulo de amostragem de taxa fixa.** Alterações podem ser `Configure` feitas no método, conforme mostrado no trecho abaixo:

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        var configuration = app.ApplicationServices.GetService<TelemetryConfiguration>();

        var builder = configuration.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
        // For older versions of the Application Insights SDK, use the following line instead:
        // var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;

        // Using fixed rate sampling   
        double fixedSamplingPercentage = 10;
        builder.UseSampling(fixedSamplingPercentage);

        builder.Build();

        // ...
    }
    ```

### <a name="configuring-fixed-rate-sampling-for-java-applications"></a>Configuração de amostragem de taxa fixa para aplicações Java

Por padrão, nenhuma amostragem é habilitada no agente Java e no SDK. Atualmente, ele suporta apenas amostragem de taxa fixa. A amostragem adaptativa não é suportada em Java.

#### <a name="configuring-java-agent"></a>Configuração do Agente Java

1. Baixe [applicationinsights-agent-3.0.0-PREVIEW.2.jar](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.0.0-PREVIEW.2/applicationinsights-agent-3.0.0-PREVIEW.2.jar)

1. Para habilitar a amostragem, adicione o seguinte ao seu `ApplicationInsights.json` arquivo:

```json
{
  "instrumentationSettings": {
    "preview": {
      "sampling": {
        "fixedRate": {
          "percentage": 10 //this is just an example that shows you how to enable only only 10% of transaction 
        }
      }
    }
  }
}
```

#### <a name="configuring-java-sdk"></a>Configuração do Java SDK

1. Baixe e configure seu aplicativo web com o mais recente [Application Insights Java SDK](../../azure-monitor/app/java-get-started.md).

2. **Habilite o módulo de amostragem de** `ApplicationInsights.xml` taxa fixa adicionando o seguinte trecho para arquivar:

    ```XML
    <TelemetryProcessors>
        <BuiltInProcessors>
            <Processor type="FixedRateSamplingTelemetryProcessor">
                <!-- Set a percentage close to 100/N where N is an integer. -->
                <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
                <Add name="SamplingPercentage" value="50" />
            </Processor>
        </BuiltInProcessors>
    </TelemetryProcessors>
    ```

3. Você pode incluir ou excluir tipos específicos de telemetria `Processor` de `FixedRateSamplingTelemetryProcessor`amostragem usando as seguintes tags dentro da tag:
   
    ```XML
    <ExcludedTypes>
        <ExcludedType>Request</ExcludedType>
    </ExcludedTypes>

    <IncludedTypes>
        <IncludedType>Exception</IncludedType>
    </IncludedTypes>
    ```

Os tipos de telemetria que podem ser `Dependency` `Event`incluídos ou excluídos da amostragem `Exception`são: , , , `PageView` `Request`, e `Trace`.

> [!NOTE]
> Para o percentual de amostragem, escolha um percentual que esteja próximo a 100/N, em que N é um inteiro.  Atualmente, a amostragem não dá suporte a outros valores.

### <a name="configuring-fixed-rate-sampling-for-opencensus-python-applications"></a>Configuração de amostragem de taxa fixa para aplicativos OpenCensus Python

Ateste sua aplicação com os mais recentes [exportadores do OpenCensus Azure Monitor](../../azure-monitor/app/opencensus-python.md).

> [!NOTE]
> A amostragem de taxa fixa não está disponível para o exportador de métricas. Isso significa que as métricas personalizadas são os únicos tipos de telemetria onde a amostragem NÃO pode ser configurada. O exportador de métricas enviará toda a telemetria que ele rastreia.

#### <a name="fixed-rate-sampling-for-tracing"></a>Amostragem de taxa fixa para rastreamento ####
Você pode especificar um `sampler` como parte da configuração do `Tracer`. Se não for fornecido um `ProbabilitySampler` sampler explícito, o será usado por padrão. O `ProbabilitySampler` usaria uma taxa de 1/10000 por padrão, o que significa que uma em cada 10.000 solicitações será enviada para o Application Insights. Se você quiser especificar uma taxa de amostragem, veja abaixo.

Para especificar a taxa `Tracer` de amostragem, certifique-se de que você especifica um sampler com uma taxa de amostragem entre 0,0 e 1,0 inclusive. Uma taxa de amostragem de 1,0 representa 100%, o que significa que todas as suas solicitações serão enviadas como telemetria para o Application Insights.

```python
tracer = Tracer(
    exporter=AzureExporter(
        instrumentation_key='00000000-0000-0000-0000-000000000000',
    ),
    sampler=ProbabilitySampler(1.0),
)
```

#### <a name="fixed-rate-sampling-for-logs"></a>Amostragem de taxa fixa para logs ####
Você pode configurar a amostragem de taxa fixa para `AzureLogHandler` modificar o `logging_sampling_rate` argumento opcional. Se nenhum argumento for fornecido, será utilizada uma taxa amostral de 1,0. Uma taxa de amostragem de 1,0 representa 100%, o que significa que todas as suas solicitações serão enviadas como telemetria para o Application Insights.

```python
exporter = metrics_exporter.new_metrics_exporter(
    instrumentation_key='00000000-0000-0000-0000-000000000000',
    logging_sampling_rate=0.5,
)
```

### <a name="configuring-fixed-rate-sampling-for-web-pages-with-javascript"></a>Configuração de amostragem de taxa fixa para páginas da Web com JavaScript

Páginas da Web baseadas em JavaScript podem ser configuradas para usar insights de aplicativos. A telemetria é enviada do aplicativo cliente em execução no navegador do usuário, e as páginas podem ser hospedadas a partir de qualquer servidor.

Quando você [configurar suas páginas da Web baseadas em JavaScript para Insights de aplicativos,](javascript.md)modifique o trecho JavaScript que você recebe do portal Insights do aplicativo.

> [!TIP]
> Em ASP.NET aplicativos com JavaScript incluídos, o `_Layout.cshtml`trecho normalmente entra .

Insira uma linha como `samplingPercentage: 10,` antes da chave de instrumentação:

```xml
<script>
    var appInsights = // ... 
    ({ 
      // Value must be 100/N where N is an integer.
      // Valid examples: 50, 25, 20, 10, 5, 1, 0.1, ...
      samplingPercentage: 10, 

      instrumentationKey: ...
    }); 

    window.appInsights = appInsights; 
    appInsights.trackPageView(); 
</script>
```

Para o percentual de amostragem, escolha um percentual que esteja próximo a 100/N, em que N é um inteiro. Atualmente, a amostragem não dá suporte a outros valores.

#### <a name="coordinating-server-side-and-client-side-sampling"></a>Coordenação de amostragem do lado do servidor e do lado do cliente

O JavaScript SDK do lado do cliente participa da amostragem de taxa fixa em conjunto com o SDK do lado do servidor. As páginas instrumentadas enviarão apenas a telemetria do lado do cliente do mesmo usuário para o qual o SDK do lado do servidor tomou sua decisão de incluir na amostragem. Essa lógica foi projetada para manter a integridade das sessões do usuário em aplicativos do lado do cliente e do servidor. Como resultado, a partir de qualquer item específico de telemetria no Application Insights você pode encontrar todos os outros itens de telemetria para este usuário ou sessão e na Pesquisa, você pode navegar entre visualizações de página e solicitações relacionadas.

Se a telemetria do seu cliente e do lado do servidor não mostrar amostras coordenadas:

* Verifique se você habilitou a amostragem tanto no servidor quanto no cliente.
* Verifique se você definiu o mesmo percentual de amostragem no cliente e no servidor.
* Certifique-se de que a versão do SDK é 2.0 ou superior.

## <a name="ingestion-sampling"></a>amostragem de ingestão

A amostragem de ingestão opera no ponto em que a telemetria do servidor web, navegadores e dispositivos chega ao ponto final do serviço Application Insights. Embora ela não reduza o tráfego de telemetria enviado do seu aplicativo, reduz a quantidade processada e mantida (e cobrada) pelo Application Insights.

Use esse tipo de amostragem se seu aplicativo geralmente ultrapassar a cota mensal e se você não tiver a opção de usar os tipos de amostragem baseados em SDK. 

Defina a taxa de amostragem na página Uso e custos estimados:

![Na lâmina visão geral do aplicativo, clique em Configurações, Cota, Amostras e selecione uma taxa de amostragem e clique em Atualizar.](./media/sampling/data-sampling.png)

Assim como outros tipos de amostragem, o algoritmo retém os itens relacionados de telemetria. Por exemplo, quando você estiver analisando a telemetria na Pesquisa, poderá localizar a solicitação relacionada a uma exceção específica. As contagens de métrica como a taxa de solicitação e a taxa de exceções são mantidas corretamente.

Os pontos de dados que são descartados pela amostragem não estão disponíveis em nenhum recurso do Application Insights como [Exportação Contínua](../../azure-monitor/app/export-telemetry.md).

A amostragem de ingestão não funciona enquanto a amostragem adaptativa ou de taxa fixa estiver em operação. A amostragem adaptativa é habilitada por padrão quando o SDK ASP.NET ou o ASP.NET Core SDK está sendo usado, ou quando o Application Insights está ativado no [Azure App Service](azure-web-apps.md) ou usando o Status Monitor. Quando a telemetria é recebida pelo ponto final do serviço Application Insights, ela examina a telemetria e se a taxa de amostragem é relatada como inferior a 100% (o que indica que a telemetria está sendo amostrada) então a taxa de amostragem de ingestão que você define é ignorada.

> [!WARNING]
> O valor mostrado na telha do portal indica o valor definido para a amostragem de ingestão. Não representa a taxa de amostragem real se qualquer tipo de amostragem sdk (amostragem adaptativa ou de taxa fixa) estiver em operação.

## <a name="when-to-use-sampling"></a>Quando usar amostragem

Em geral, para a maioria das aplicações de pequeno e médio porte você não precisa de amostragem. As informações de diagnóstico mais úteis e as estatísticas mais precisas são obtidas por meio da coleta de dados em todas as atividades de usuário. 

As principais vantagens da amostragem são:

* O serviço application Insights derruba pontos de dados ("aceleradores") quando seu aplicativo envia uma taxa muito alta de telemetria em um curto intervalo de tempo. A amostragem reduz a probabilidade de que sua aplicação veja o estrangulamento ocorrer.
* Permanecer dentro da [cota](pricing.md) de pontos de dados para o seu tipo de preço. 
* Para reduzir o tráfego de rede da coleção de telemetria. 

### <a name="which-type-of-sampling-should-i-use"></a>Que tipo de amostragem eu devo usar?

**Use a amostragem de ingestão se:**

* Você costuma usar sua cota mensal de telemetria.
* Você está recebendo muita telemetria dos navegadores dos seus usuários.
* Você estiver usando uma versão do SDK que não oferece suporte à amostragem. Por exemplo, o ASP.NET anteriores à 2.

**Use a amostragem de taxa fixa se:**

* Você deseja uma amostragem sincronizada entre cliente e servidor para que, quando estiver investigando eventos na [Pesquisa,](../../azure-monitor/app/diagnostic-search.md)você possa navegar entre eventos relacionados no cliente e no servidor, como visualizações de página e solicitações HTTP.
* Você tiver certeza sobre a porcentagem de amostragem apropriada para seu aplicativo. Ela deve ser alta o suficiente para obter métricas precisas, mas abaixo da taxa que excede sua cota de preços e limitações.

**Use a amostragem adaptável:**

Se as condições para usar outras formas de amostragem não se aplicarem, é recomendável a amostragem adaptável. Esta configuração é habilitada por padrão no SDK ASP.NET/ASP.NET Core. Não reduzirá o tráfego até que uma certa taxa mínima seja atingida, portanto, os locais de baixo uso provavelmente não serão amostrados.

## <a name="knowing-whether-sampling-is-in-operation"></a>Saber se a amostragem está em operação

Para descobrir a taxa de amostragem real, independentemente de onde ela tiver sido aplicada, use uma [consulta do Analytics](../../azure-monitor/app/analytics.md) como esta:

```kusto
union requests,dependencies,pageViews,browserTimings,exceptions,traces
| where timestamp > ago(1d)
| summarize RetainedPercentage = 100/avg(itemCount) by bin(timestamp, 1h), itemType
```

Se você `RetainedPercentage` ver que para qualquer tipo é menor que 100, então esse tipo de telemetria está sendo amostrado.

> [!IMPORTANT]
> O Application Insights não amostra a sessão, as métricas (incluindo métricas personalizadas) ou os tipos de telemetria de contador de desempenho em qualquer uma das técnicas de amostragem. Esses tipos são sempre excluídos da amostragem, pois uma redução da precisão pode ser altamente indesejável para esses tipos de telemetria.

## <a name="how-sampling-works"></a>Como funciona a amostragem

O algoritmo de amostragem decide quais itens de telemetria podem cair e quais manter. Isso é verdade se a amostragem é feita pelo SDK ou no serviço Application Insights. A decisão de amostragem baseia-se em várias regras que visam preservar todos os pontos de dados inter-relacionados intactos, mantendo uma experiência de diagnóstico no Application Insights que seja acionável e confiável mesmo com um conjunto reduzido de dados. Por exemplo, se o aplicativo tiver uma solicitação com falha incluída em uma amostra, os itens adicionais de telemetria (como exceção e vestígios registrados para esta solicitação) serão retidos. A amostragem mantém ou deixa todos juntos. Como resultado, quando você examinar os detalhes da solicitação no Application Insights, é possível ver sempre a solicitação junto com seus itens de telemetria associados.

A decisão amostral baseia-se na ID de operação do pedido, o que significa que todos os itens de telemetria pertencentes a uma determinada operação são preservados ou descartados. Para os itens de telemetria que não possuem um conjunto de ID de operação (como itens de telemetria relatados a partir de threads assíncronos sem contexto HTTP), a amostragem simplesmente captura uma porcentagem de itens de telemetria de cada tipo.

Ao apresentar a telemetria de volta para você, o serviço Application Insights ajusta as métricas pelo mesmo percentual de amostragem usado no momento da coleta, para compensar os pontos de dados ausentes. Portanto, ao examinar a telemetria no Application Insights, os usuários veem aproximações estatisticamente corretas que estão muito próximas aos números reais.

A precisão da aproximação depende principalmente do percentual de amostragem configurado. Além disso, a precisão aumenta para os aplicativos que lidam com um grande volume de solicitações geralmente semelhantes de uma grande quantidade de usuários. Por outro lado, para aplicativos que não funcionam com uma carga significativa, a amostragem não é necessária, pois esses aplicativos geralmente podem enviar toda a sua telemetria, ao mesmo tempo que permanecem dentro da cota, sem causar perda de dados da limitação. 

## <a name="frequently-asked-questions"></a>Perguntas frequentes

*Qual é o comportamento amostral padrão nos SDKs ASP.NET e ASP.NET Core?*

* Se você estiver usando uma das versões mais recentes do SDK acima, o Adaptive Sampling é ativado por padrão com cinco itens de telemetria por segundo.
  Há dois `AdaptiveSamplingTelemetryProcessor` nós adicionados por padrão, e `Event` um inclui o tipo de `Event` amostragem, enquanto o outro exclui o tipo de amostragem. Essa configuração significa que o SDK tentará limitar os itens de telemetria a cinco itens de telemetria de `Event` tipos e cinco `Events` itens de telemetria de todos os outros tipos combinados, garantindo assim que sejam amostrados separadamente de outros tipos de telemetria. Os eventos são normalmente usados para telemetria de negócios, e provavelmente não devem ser afetados por volumes de telemetria diagnóstica.
  
  A seguir, `ApplicationInsights.config` mostra o arquivo padrão gerado. No ASP.NET Core, o mesmo comportamento padrão é ativado em código. Use os [exemplos na seção anterior desta página](#configuring-adaptive-sampling-for-aspnet-core-applications) para alterar esse comportamento padrão.

    ```xml
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
            <ExcludedTypes>Event</ExcludedTypes>
        </Add>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
            <IncludedTypes>Event</IncludedTypes>
        </Add>
    </TelemetryProcessors>
    ```

*A telemetria pode ser amostrada mais de uma vez?*

* Não. AmostragemOs processadores ignoram itens de considerações de amostragem se o item já estiver amostrado. O mesmo vale para a amostragem de ingestão também, que não aplicará amostragem aos itens já amostrados no próprio SDK.

*Por que a amostragem não se trata apenas de “coletar X% de cada tipo de telemetria”?*

* Embora essa abordagem amostral forneça um alto nível de precisão em aproximações métricas, quebraria a capacidade de correlacionar dados diagnósticos por usuário, sessão e solicitação, o que é fundamental para diagnósticos. Portanto, a amostragem funciona melhor com políticas como "coletar todos os itens de telemetria para X% dos usuários do aplicativo", ou "coletar toda a telemetria para X% das solicitações de aplicativos". Para os itens de telemetria não associados às solicitações (como processamento assíncrono de fundo), o recuo é "coletar X% de todos os itens para cada tipo de telemetria". 

*O percentual de amostragem pode ser alterado com o tempo?*

* Sim, a amostragem adaptável altera gradualmente a porcentagem de amostragem com base no volume atualmente observado da telemetria.

*Se eu usar a amostragem de taxa fixa, como saber qual percentual de amostragem funcionará melhor para o meu aplicativo?*

* Uma maneira é iniciar com a amostragem adaptável, descobrir qual taxa se adequa (consulte a pergunta anterior) e, em seguida, alternar para a amostragem de taxa fixa usando essa taxa. 
  
    Caso contrário, é preciso adivinhar. Analise o seu uso atual da telemetria na em Application Insights, observe qualquer limitação que esteja ocorrendo e estime o volume da telemetria coletada. Essas três entradas, junto com seu tipo de preço selecionado, sugere o quanto você talvez queira reduzir o volume da telemetria coletada. No entanto, um aumento no número de usuários ou alguma outra mudança no volume de telemetria pode invalidar sua estimativa.

*O que acontece se eu configurar a porcentagem de amostragem como muito baixa?*

* Percentuais amostrais excessivamente baixos causam amostragem excessivamente agressiva e reduzem a precisão das aproximações quando o Application Insights tenta compensar a visualização dos dados para a redução do volume de dados. Além disso, sua experiência de diagnóstico pode ser negativamente impactada, já que algumas das solicitações raramente falhando ou lentas podem ser amostradas.

*O que acontece se eu configurar a porcentagem de amostragem como muito alta?*

* A configuração de um percentual amostral muito alto (não agressivo o suficiente) resulta em uma redução insuficiente no volume da telemetria coletada. Ainda pode ocorrer perda de dados de telemetria relacionada à limitação, e o custo do uso do Application Insights pode ser maior do que o planejado devido a encargos excedentes.

*Em quais plataformas posso usar a amostragem?*

* A amostragem de ingestão pode ocorrer automaticamente para qualquer telemetria acima de um determinado volume, se o SDK não estiver executando a amostragem. Essa configuração funcionaria, por exemplo, se você estiver usando uma versão mais antiga do SDK ASP.NET ou Java SDK.
* Se você estiver usando os SDKs atuais ASP.NET ou ASP.NET Core (hospedados no Azure ou no seu próprio servidor), você terá uma amostragem adaptativa por padrão, mas você pode mudar para taxa fixa conforme descrito acima. Com a amostragem de taxa fixa, o SDK do navegador sincroniza automaticamente os eventos relacionados ao exemplo. 
* Se você estiver usando o agente Java `ApplicationInsights.json` atual, você pode configurar `ApplicationInsights.xml`(para Java SDK, configurar) para ativar a amostragem de taxa fixa. A amostragem é desativada por padrão. Com amostragem de taxa fixa, o SDK do navegador e o servidor sincronizam automaticamente a amostra de eventos relacionados.

*Há alguns eventos raros que sempre desejo ver. Como posso fazê-los passar pelo módulo de amostragem?*

* A melhor maneira de conseguir isso é escrever um [Telemetria Inicializador](../../azure-monitor/app/api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)personalizado, que define o `SamplingPercentage` item de telemetria que você deseja retido, como mostrado abaixo. Como os iniciadores são garantidos para serem executados antes dos processadores de telemetria (incluindo amostragem), isso garante que todas as técnicas de amostragem ignorem este item de quaisquer considerações de amostragem. Iniciadores de telemetria personalizados estão disponíveis no SDK ASP.NET, no ASP.NET Core SDK, no JavaScript SDK e no Java SDK. Por exemplo, você pode configurar um inicializador de telemetria usando o sdk ASP.NET:

    ```csharp
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            if(somecondition)
            {
                ((ISupportSampling)telemetry).SamplingPercentage = 100;
            }
        }
    }
    ```

## <a name="older-sdk-versions"></a>Versões mais antigas do SDK

A amostragem adaptativa está disponível para o Application Insights SDK para ASP.NET v2.0.0-beta3 e posteriormente, Microsoft.ApplicationInsights.AspNetCore SDK v2.2.0-beta1 e posteriormente, e é habilitado por padrão.

A amostragem de taxa fixa é uma característica do SDK em ASP.NET versões de 2.0.0 e Java SDK versão 2.0.1 e em diante.

Antes do v2.5.0-beta2 do SDK ASP.NET, e v2.2.0-beta3 do ASP.NET Core SDK, a decisão de amostragem foi baseada no hash do ID do usuário para aplicativos que definem "usuário" (ou seja, aplicativos web mais típicos). Para os tipos de aplicativos que não definiram os usuários (como serviços web) a decisão de amostragem foi baseada no ID de operação da solicitação. Versões recentes dos SDKs ASP.NET e ASP.NET Core usam o ID de operação para a decisão de amostragem.

## <a name="next-steps"></a>Próximas etapas

* [filtragem](../../azure-monitor/app/api-filtering-sampling.md) pode fornecer um controle mais restrito do que o SDK envia.
* Leia o artigo da Rede de [Desenvolvedores Otimizar telemetria com insights de aplicativos](https://msdn.microsoft.com/magazine/mt808502.aspx).
