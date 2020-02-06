---
title: Filtrando e pré-processando no SDK do Azure Application Insights | Microsoft Docs
description: Escreva Processadores de Telemetria e Inicializadores de Telemetria para o SDK filtrar ou adicionar propriedades aos dados antes da telemetria ser enviada ao portal do Application Insights.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 11/23/2016
ms.openlocfilehash: dcc71739d859fb9cf4e03e5d3540d3cdbc69ac49
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77031536"
---
# <a name="filtering-and-preprocessing-telemetry-in-the-application-insights-sdk"></a>Filtrando e pré-processando a telemetria no SDK do Application Insights

Você pode gravar e configurar plug-ins para o SDK do Application Insights para personalizar como a telemetria pode ser enriqueceda e processada antes de ser enviada ao serviço de Application Insights.

* [Amostragem](sampling.md) reduz o volume de telemetria sem afetar as estatísticas. Ela mantém juntos os pontos de dados relacionadas para que você possa navegar entre eles para diagnosticar um problema. No portal, as contagens totais são multiplicadas para compensar a amostragem.
* A filtragem com processadores de telemetria permite filtrar a telemetria no SDK antes que ele seja enviado ao servidor. Por exemplo, você pode reduzir o volume de telemetria excluindo as solicitações de robôs. A filtragem é uma abordagem mais básica para reduzir o tráfego do que a amostragem. Ela permite um maior controle sobre o que é transmitido, mas você precisa estar ciente de que ela afetará as estatísticas - por exemplo, se você filtrar todas as solicitações bem-sucedidas.
* [Inicializadores de telemetria adicionam ou modificam Propriedades](#add-properties) para qualquer telemetria enviada de seu aplicativo, incluindo a telemetria dos módulos padrão. Por exemplo, você pode adicionar valores calculados ou números de versão pelos qual os dados serão filtrados no portal.
* [A API do SDK](../../azure-monitor/app/api-custom-events-metrics.md) é usada para enviar eventos personalizados e métricas.

Antes de iniciar:

* Instale o SDK apropriado para seu aplicativo: [ASP.net](asp-net.md), [ASP.NET Core](asp-net-core.md), [não http/Worker para .net/.NET Core](worker-service.md), [Java](../../azure-monitor/app/java-get-started.md) ou [JavaScript](javascript.md)

<a name="filtering"></a>

## <a name="filtering"></a>Filtragem

Essa técnica oferece controle direto sobre o que é incluído ou excluído do fluxo de telemetria. A filtragem pode ser usada para descartar itens de telemetria de serem enviados para Application Insights. Você pode usá-la em conjunto com a Amostragem, ou separadamente.

Para filtrar a telemetria, você escreve um processador de telemetria e o registra com o `TelemetryConfiguration`. Toda a telemetria passa pelo processador e você pode optar por descartá-la do fluxo ou fornecê-la ao próximo processador na cadeia. Isso inclui a telemetria dos módulos padrão, como o coletor de solicitação HTTP e o coletor de dependências, e a telemetria que você mesmo rastreou. Por exemplo, você pode filtrar a telemetria sobre solicitações de robôs ou sobre chamadas de dependência bem-sucedidas.

> [!WARNING]
> Filtrar a telemetria enviada do SDK usando processadores pode distorcer as estatísticas que você vê no portal e dificultar o acompanhamento de itens relacionados.
>
> Em vez disso, considere usar a [amostragem](../../azure-monitor/app/sampling.md).
>
>

### <a name="create-a-telemetry-processor-c"></a>Criar um processador de telemetria (C#)

1. Para criar um filtro, implemente `ITelemetryProcessor`.

    Observe que os Processadores de Telemetria criam uma cadeia de processamento. Ao criar uma instância de um processador de telemetria, você recebe uma referência ao próximo processador na cadeia. Quando um ponto de dados de telemetria é passado para o método Process, ele faz seu trabalho e, em seguida, chama (ou não chamadas) o próximo processador de telemetria na cadeia.

    ```csharp
    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.Extensibility;

    public class SuccessfulDependencyFilter : ITelemetryProcessor
    {
        private ITelemetryProcessor Next { get; set; }

        // next will point to the next TelemetryProcessor in the chain.
        public SuccessfulDependencyFilter(ITelemetryProcessor next)
        {
            this.Next = next;
        }

        public void Process(ITelemetry item)
        {
            // To filter out an item, return without calling the next processor.
            if (!OKtoSend(item)) { return; }

            this.Next.Process(item);
        }

        // Example: replace with your own criteria.
        private bool OKtoSend (ITelemetry item)
        {
            var dependency = item as DependencyTelemetry;
            if (dependency == null) return true;

            return dependency.Success != true;
        }
    }
    ```

2. Adicione seu processador.

**Aplicativos ASP.net** Insira este trecho de código em ApplicationInsights. config:

```xml
<TelemetryProcessors>
  <Add Type="WebApplication9.SuccessfulDependencyFilter, WebApplication9">
     <!-- Set public property -->
     <MyParamFromConfigFile>2-beta</MyParamFromConfigFile>
  </Add>
</TelemetryProcessors>
```

Você pode transmitir valores de cadeia de caracteres do arquivo .config fornecendo propriedades nomeadas públicas em sua classe.

> [!WARNING]
> Fique atento para que o nome do tipo e todos os nomes de propriedade no arquivo .config correspondam aos nomes de classe e de propriedade no código. Se o arquivo .config fizer referência a um tipo ou propriedade inexistente, o SDK poderá silenciosamente falhar ao enviar qualquer telemetria.
>

**Como alternativa,** é possível inicializar o filtro no código. Em uma classe de inicialização adequada – por exemplo, AppStart em `Global.asax.cs`-Insira o processador na cadeia:

```csharp
var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
builder.Use((next) => new SuccessfulDependencyFilter(next));

// If you have more processors:
builder.Use((next) => new AnotherProcessor(next));

builder.Build();
```

Os TelemetryClients criados depois desse ponto usarão seus processadores.

**Aplicativos de serviço ASP.NET Core/Worker**

> [!NOTE]
> Adicionar o processador usando `ApplicationInsights.config` ou usando `TelemetryConfiguration.Active` não é válido para aplicativos ASP.NET Core ou se você estiver usando o SDK Microsoft. ApplicationInsights. WorkerService.

Para aplicativos escritos usando [ASP.NET Core](asp-net-core.md#adding-telemetry-processors) ou [WorkerService](worker-service.md#adding-telemetry-processors), adicionar um novo `TelemetryProcessor` é feito usando `AddApplicationInsightsTelemetryProcessor` método de extensão em `IServiceCollection`, conforme mostrado abaixo. Esse método é chamado no método `ConfigureServices` da classe `Startup.cs`.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // ...
        services.AddApplicationInsightsTelemetry();
        services.AddApplicationInsightsTelemetryProcessor<SuccessfulDependencyFilter>();

        // If you have more processors:
        services.AddApplicationInsightsTelemetryProcessor<AnotherProcessor>();
    }
```

### <a name="example-filters"></a>Filtros de exemplo

#### <a name="synthetic-requests"></a>Solicitações sintéticas

Filtre os bots e os testes Web. Embora Metrics Explorer ofereça a opção de filtrar fontes sintéticas, essa opção reduz o tráfego e o tamanho de ingestão filtrando-os no próprio SDK.

```csharp
public void Process(ITelemetry item)
{
  if (!string.IsNullOrEmpty(item.Context.Operation.SyntheticSource)) {return;}

  // Send everything else:
  this.Next.Process(item);
}
```

#### <a name="failed-authentication"></a>Autenticação com falha

Filtre as solicitações com uma resposta "401".

```csharp
public void Process(ITelemetry item)
{
    var request = item as RequestTelemetry;

    if (request != null &&
    request.ResponseCode.Equals("401", StringComparison.OrdinalIgnoreCase))
    {
        // To filter out an item, return without calling the next processor.
        return;
    }

    // Send everything else
    this.Next.Process(item);
}
```

#### <a name="filter-out-fast-remote-dependency-calls"></a>Filtrar chamadas de dependência rápidas remotas

Se desejar diagnosticar chamadas lentas, filtre as rápidas.

> [!NOTE]
> Isso distorcerá as estatísticas que você vê no portal.
>
>

```csharp
public void Process(ITelemetry item)
{
    var request = item as DependencyTelemetry;

    if (request != null && request.Duration.TotalMilliseconds < 100)
    {
        return;
    }
    this.Next.Process(item);
}
```

#### <a name="diagnose-dependency-issues"></a>Diagnosticar problemas de dependência

[Este blog](https://azure.microsoft.com/blog/implement-an-application-insights-telemetry-processor/) descreve um projeto para diagnosticar problemas de dependência ao enviar automaticamente pings regulares para as dependências.

<a name="add-properties"></a>

### <a name="javascript-web-applications"></a>Aplicativos Web do JavaScript

**Filtragem usando ITelemetryInitializer**

1. Crie uma função de retorno de chamada do inicializador de telemetria. A função de retorno de chamada usa `ITelemetryItem` como um parâmetro, que é o evento que está sendo processado. Retornar `false` deste retorno de chamada resulta no item de telemetria a ser filtrado.  

   ```JS
   var filteringFunction = (envelope) => {
     if (envelope.data.someField === 'tobefilteredout') {
        return false;
     }
  
     return true;
   };
   ```

2. Adicione o retorno de chamada do inicializador de telemetria:

   ```JS
   appInsights.addTelemetryInitializer(filteringFunction);
   ```

## <a name="addmodify-properties-itelemetryinitializer"></a>Adicionar/modificar propriedades: ITelemetryInitializer


Use inicializadores de telemetria para enriquecer a telemetria com informações adicionais e/ou para substituir as propriedades de telemetria definidas pelos módulos de telemetria padrão.

Por exemplo, a Application Insights para o pacote da Web coleta a telemetria sobre solicitações HTTP. Por padrão, ele sinaliza como qualquer solicitação com um código de resposta de falha > = 400. Mas se desejar tratar 400 como êxito, você pode fornecer um inicializador de telemetria que define a propriedade Sucess.

Se você fornecer um inicializador de telemetria, ele é chamado sempre que qualquer um dos métodos Track*() for chamado. Isso inclui `Track()` métodos chamados pelos módulos de telemetria padrão. Por convenção, esses módulos não definem qualquer propriedade que já foi definida por um inicializador. Inicializadores de telemetria são chamados antes de chamar processadores de telemetria. Portanto, todos os aprimoramentos feitos por inicializadores são visíveis para os processadores.

**Definir seu inicializador**

*C#*

```csharp
using System;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;

namespace MvcWebRole.Telemetry
{
  /*
   * Custom TelemetryInitializer that overrides the default SDK
   * behavior of treating response codes >= 400 as failed requests
   *
   */
  public class MyTelemetryInitializer : ITelemetryInitializer
  {
    public void Initialize(ITelemetry telemetry)
    {
        var requestTelemetry = telemetry as RequestTelemetry;
        // Is this a TrackRequest() ?
        if (requestTelemetry == null) return;
        int code;
        bool parsed = Int32.TryParse(requestTelemetry.ResponseCode, out code);
        if (!parsed) return;
        if (code >= 400 && code < 500)
        {
            // If we set the Success property, the SDK won't change it:
            requestTelemetry.Success = true;

            // Allow us to filter these requests in the portal:
            requestTelemetry.Properties["Overridden400s"] = "true";
        }
        // else leave the SDK to set the Success property
    }
  }
}
```

**Aplicativos ASP.NET: carregar seu inicializador**

Em ApplicationInsights.config:

```xml
<ApplicationInsights>
  <TelemetryInitializers>
    <!-- Fully qualified type name, assembly name: -->
    <Add Type="MvcWebRole.Telemetry.MyTelemetryInitializer, MvcWebRole"/>
    ...
  </TelemetryInitializers>
</ApplicationInsights>
```

*Como alternativa,* você pode instanciar o inicializador no código, por exemplo em Global.aspx.cs:

```csharp
protected void Application_Start()
{
    // ...
    TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
}
```

[Ver mais deste exemplo.](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole)

**Aplicativos de serviço ASP.NET Core/Worker: carregar seu inicializador**

> [!NOTE]
> Adicionar inicializador usando `ApplicationInsights.config` ou usando `TelemetryConfiguration.Active` não é válido para aplicativos ASP.NET Core ou se você estiver usando o SDK Microsoft. ApplicationInsights. WorkerService.

Para aplicativos escritos usando [ASP.NET Core](asp-net-core.md#adding-telemetryinitializers) ou [WorkerService](worker-service.md#adding-telemetryinitializers), adicionar um novo `TelemetryInitializer` é feito adicionando-o ao contêiner de injeção de dependência, como mostrado abaixo. Isso é feito no método `Startup.ConfigureServices`.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```

### <a name="java-telemetry-initializers"></a>Inicializadores de telemetria do Java

[Documentação do SDK do Java](https://docs.microsoft.com/java/api/com.microsoft.applicationinsights.extensibility.telemetryinitializer?view=azure-java-stable)

```Java
public interface TelemetryInitializer
{ /** Initializes properties of the specified object. * @param telemetry The {@link com.microsoft.applicationinsights.telemetry.Telemetry} to initialize. */

void initialize(Telemetry telemetry); }
```

Em seguida, registre o inicializador personalizado em seu arquivo applicationinsights. XML.

```xml
<Add type="mypackage.MyConfigurableContextInitializer">
    <Param name="some_config_property" value="some_value" />
</Add>
```

### <a name="javascript-telemetry-initializers"></a>Inicializadores de telemetria JavaScript
*JavaScript*

Insira um inicializador de telemetria logo após o código de inicialização que você obteve do portal:

```JS
<script type="text/javascript">
    // ... initialization code
    ...({
        instrumentationKey: "your instrumentation key"
    });
    window.appInsights = appInsights;


    // Adding telemetry initializer.
    // This is called whenever a new telemetry item
    // is created.

    appInsights.queue.push(function () {
        appInsights.context.addTelemetryInitializer(function (envelope) {
            var telemetryItem = envelope.data.baseData;

            // To check the telemetry items type - for example PageView:
            if (envelope.name == Microsoft.ApplicationInsights.Telemetry.PageView.envelopeType) {
                // this statement removes url from all page view documents
                telemetryItem.url = "URL CENSORED";
            }

            // To set custom properties:
            telemetryItem.properties = telemetryItem.properties || {};
            telemetryItem.properties["globalProperty"] = "boo";

            // To set custom metrics:
            telemetryItem.measurements = telemetryItem.measurements || {};
            telemetryItem.measurements["globalMetric"] = 100;
        });
    });

    // End of inserted code.

    appInsights.trackPageView();
</script>
```

Para obter um resumo das propriedades não personalizadas disponíveis em telemetryItem, veja o [Modelo de dados de exportação do Application Insights](../../azure-monitor/app/export-data-model.md).

Você pode adicionar quantos inicializadores desejar e eles são chamados na ordem em que são adicionados.

### <a name="opencensus-python-telemetry-processors"></a>Processadores de telemetria do OpenCensus Python

Os processadores de telemetria no Python OpenCensus são simplesmente funções de retorno de chamada chamadas para processar a telemetria antes de serem exportadas. A função de retorno de chamada deve aceitar um tipo de dados de [envelope](https://github.com/census-instrumentation/opencensus-python/blob/master/contrib/opencensus-ext-azure/opencensus/ext/azure/common/protocol.py#L86) como seu parâmetro. Para filtrar a telemetria de ser exportada, verifique se a função de retorno de chamada retorna `False`. Você pode ver o esquema para Azure Monitor tipos de dados nos envelopes [aqui](https://github.com/census-instrumentation/opencensus-python/blob/master/contrib/opencensus-ext-azure/opencensus/ext/azure/common/protocol.py).

```python
# Example for log exporter
import logging

from opencensus.ext.azure.log_exporter import AzureLogHandler

logger = logging.getLogger(__name__)

# Callback function to append '_hello' to each log message telemetry
def callback_function(envelope):
    envelope.data.baseData.message += '_hello'
    return True

handler = AzureLogHandler(connection_string='InstrumentationKey=<your-instrumentation_key-here>')
handler.add_telemetry_processor(callback_function)
logger.addHandler(handler)
logger.warning('Hello, World!')
```
```python
# Example for trace exporter
import requests

from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace import config_integration
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['requests'])

# Callback function to add os_type: linux to span properties
def callback_function(envelope):
    envelope.data.baseData.properties['os_type'] = 'linux'
    return True

exporter = AzureExporter(
    connection_string='InstrumentationKey=<your-instrumentation-key-here>'
)
exporter.add_telemetry_processor(callback_function)
tracer = Tracer(exporter=exporter, sampler=ProbabilitySampler(1.0))
with tracer.span(name='parent'):
response = requests.get(url='https://www.wikipedia.org/wiki/Rabbit')
```

```python
# Example for metrics exporter
import time

from opencensus.ext.azure import metrics_exporter
from opencensus.stats import aggregation as aggregation_module
from opencensus.stats import measure as measure_module
from opencensus.stats import stats as stats_module
from opencensus.stats import view as view_module
from opencensus.tags import tag_map as tag_map_module

stats = stats_module.stats
view_manager = stats.view_manager
stats_recorder = stats.stats_recorder

CARROTS_MEASURE = measure_module.MeasureInt("carrots",
                                            "number of carrots",
                                            "carrots")
CARROTS_VIEW = view_module.View("carrots_view",
                                "number of carrots",
                                [],
                                CARROTS_MEASURE,
                                aggregation_module.CountAggregation())

# Callback function to only export the metric if value is greater than 0
def callback_function(envelope):
    return envelope.data.baseData.metrics[0].value > 0

def main():
    # Enable metrics
    # Set the interval in seconds in which you want to send metrics
    exporter = metrics_exporter.new_metrics_exporter(connection_string='InstrumentationKey=<your-instrumentation-key-here>')
    exporter.add_telemetry_processor(callback_function)
    view_manager.register_exporter(exporter)

    view_manager.register_view(CARROTS_VIEW)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    mmap.measure_int_put(CARROTS_MEASURE, 1000)
    mmap.record(tmap)
    # Default export interval is every 15.0s
    # Your application should run for at least this amount
    # of time so the exporter will meet this interval
    # Sleep can fulfill this
    time.sleep(60)

    print("Done recording metrics")

if __name__ == "__main__":
    main()
```
Você pode adicionar quantos processadores desejar e eles serão chamados na ordem em que são adicionados. Se um processador deve lançar uma exceção, ele não afeta os processadores a seguir.

### <a name="example-telemetryinitializers"></a>Exemplo de TelemetryInitializers

#### <a name="add-custom-property"></a>Adicionar propriedade personalizada

O inicializador de exemplo a seguir adiciona uma propriedade personalizada a cada telemetria rastreada.

```csharp
public void Initialize(ITelemetry item)
{
  var itemProperties = item as ISupportProperties;
  if(itemProperties != null && !itemProperties.ContainsKey("customProp"))
    {
        itemProperties.Properties["customProp"] = "customValue";
    }
}
```

#### <a name="add-cloud-role-name"></a>Adicionar nome da função de nuvem

O inicializador de exemplo a seguir define o nome da função de nuvem para cada telemetria rastreada.

```csharp
public void Initialize(ITelemetry telemetry)
{
    if(string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
    {
        telemetry.Context.Cloud.RoleName = "MyCloudRoleName";
    }
}
```

## <a name="itelemetryprocessor-and-itelemetryinitializer"></a>ITelemetryProcessor e ITelemetryInitializer

Qual é a diferença entre os processadores de telemetria e inicializadores de telemetria?

* Há algumas sobreposições no que você pode fazer com elas: ambas podem ser usadas para adicionar ou modificar propriedades de telemetria, embora seja recomendável usar inicializadores para essa finalidade.
* Sempre execute TelemetryInitializers antes de TelemetryProcessors.
* TelemetryInitializers pode ser chamado mais de uma vez. Por convenção, eles não definem nenhuma propriedade que já tenha sido definida.
* TelemetryProcessors permitem que você substitua ou descarte completamente um item de telemetria.
* Todos os TelemetryInitializers registrados têm a garantia de serem chamados para cada item de telemetria. Para processadores de telemetria, o SDK garante a chamada do primeiro processador de telemetria. Se o restante dos processadores são chamados ou não, o é decidido pelos processadores de telemetria anteriores.
* Use TelemetryInitializers para enriquecer a telemetria com propriedades adicionais ou substituir uma existente. Use TelemetryProcessor para filtrar a telemetria.

## <a name="troubleshooting-applicationinsightsconfig"></a>Resolução de problemas ApplicationInsights.config

* Confirme se o nome do tipo totalmente qualificado e o nome do assembly estão corretos.
* Confirme se o arquivo applicationinsights. config está no seu diretório de saída e contém todas as alterações recentes.

## <a name="reference-docs"></a>Documentos de Referência

* [Visão geral da API](../../azure-monitor/app/api-custom-events-metrics.md)
* [Referência do ASP.NET](https://msdn.microsoft.com/library/dn817570.aspx)

## <a name="sdk-code"></a>Código do SDK

* [SDK de Núcleo do ASP.NET](https://github.com/Microsoft/ApplicationInsights-aspnetcore)
* [SDK do ASP.NET](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [SDK do JavaScript](https://github.com/Microsoft/ApplicationInsights-JS)

## <a name="next"></a>Próximas etapas
* [Pesquisar eventos e logs](../../azure-monitor/app/diagnostic-search.md)
* [Amostragem](../../azure-monitor/app/sampling.md)
* [Solução de problemas](../../azure-monitor/app/troubleshoot-faq.md)
