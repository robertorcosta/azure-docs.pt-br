---
title: correlação de telemetria no Azure Application Insights | Microsoft Docs
description: Correlação no Application Insights Telemetry
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 06/07/2019
ms.reviewer: sergkanz
ms.custom: devx-track-python, devx-track-csharp
ms.openlocfilehash: fd9299d49f42eb021d64ae25447fd13e7378ff3f
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91447865"
---
# <a name="telemetry-correlation-in-application-insights"></a>Correlação de telemetria no Application Insights

No mundo dos microsserviços, cada operação lógica requer que o trabalho seja feito em vários componentes do serviço. Você pode monitorar cada um desses componentes separadamente usando [Application insights](../../azure-monitor/app/app-insights-overview.md). O Application Insights é compatível com a correlação de telemetria distribuída, que você pode usar para detectar qual componente é responsável por falhas ou degradação do desempenho.

Este artigo explica o modelo de dados usado pelo Application Insights para correlacionar a telemetria enviada por vários componentes. Ele aborda protocolos e técnicas de propagação de contexto. Ele também aborda a implementação de táticas de correlação em diferentes linguagens e plataformas.

## <a name="data-model-for-telemetry-correlation"></a>Modelo de dados para correlação de telemetria

O Application Insights define um [modelo de dados](../../azure-monitor/app/data-model.md) para correlação de telemetria distribuída. Para associar a telemetria a uma operação lógica, cada item de telemetria tem um campo de contexto chamado `operation_Id` . Esse identificador é compartilhado por todos os itens de telemetria no rastreamento distribuído. Portanto, mesmo se você perder a telemetria de uma única camada, ainda poderá associar a telemetria relatada por outros componentes.

Uma operação lógica distribuída normalmente consiste em um conjunto de operações menores que são solicitações processadas por um dos componentes. Essas operações são definidas por [telemetria de solicitação](../../azure-monitor/app/data-model-request-telemetry.md). Cada item de telemetria de solicitação tem seu próprio `id` que o identifica de forma exclusiva e global. E todos os itens de telemetria (como rastreamentos e exceções) associados à solicitação devem definir o `operation_parentId` como o valor da solicitação `id` .

Cada operação de saída, como uma chamada HTTP para outro componente, é representada pela [telemetria de dependência](../../azure-monitor/app/data-model-dependency-telemetry.md). A telemetria de dependência também define seu próprio `id` que é globalmente exclusivo. A telemetria de solicitação, iniciada por essa chamada de dependência, usa este `id` como `operation_parentId`.

Você pode compilar um modo de exibição de operação lógica distribuída usando `operation_Id`, `operation_parentId` e `request.id` com `dependency.id`. Esses campos também definem a ordem de causalidade das chamadas de telemetria.

Em um ambiente de microsserviços, os rastreamentos de componentes podem ir para itens de armazenamento diferentes. Todo componente pode ter sua própria chave de instrumentação no Application Insights. Para obter a telemetria para a operação lógica, o Application Insights consulta dados de cada item de armazenamento. Quando o número de itens de armazenamento for grande, você precisará de uma dica sobre onde procurar em seguida. O modelo de dados do Application Insights define dois campos para resolver esse problema: `request.source` e `dependency.target`. O primeiro campo identifica o componente que iniciou a solicitação de dependência. O segundo campo identifica qual componente retornou a resposta da chamada de dependência.

## <a name="example"></a>Exemplo

Vamos examinar um exemplo. Um aplicativo chamado preços de ações mostra o preço atual do mercado de um estoque usando uma API externa chamada stock. O aplicativo de preços de ações tem uma página chamada Stock Page que o navegador da Web do cliente abre usando o `GET /Home/Stock` . O aplicativo consulta a API de estoque usando a chamada HTTP `GET /api/stock/value` .

Você pode analisar a telemetria resultante executando uma consulta:

```kusto
(requests | union dependencies | union pageViews)
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

Nos resultados, observe que todos os itens de telemetria compartilham a raiz `operation_Id`. Quando uma chamada AJAX é feita da página, uma nova ID exclusiva ( `qJSXU` ) é atribuída à telemetria de dependência e a ID de pageView é usada como `operation_ParentId` . A solicitação do servidor, em seguida, usa a ID do Ajax como `operation_ParentId`.

| itemType   | name                      | ID           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| pageView   | Página de ações                |              | STYz               | STYz         |
| dependência | GET /Home/Stock           | qJSXU        | STYz               | STYz         |
| solicitação    | GET Home/Stock            | KqKwlrSt9PA= | qJSXU              | STYz         |
| dependência | GET /api/stock/value      | bBrf2L7mm2g= | KqKwlrSt9PA=       | STYz         |

Quando a chamada `GET /api/stock/value` é feita a um serviço externo, você precisa saber a identidade desse servidor para poder definir o `dependency.target` campo adequadamente. Quando o serviço externo não oferece suporte a monitoramento, `target` é definido como o nome do host do serviço (por exemplo, `stock-prices-api.com`). Mas se o serviço se identificar retornando um cabeçalho HTTP predefinido, `target` contém a identidade do serviço que permite ao Application insights criar um rastreamento distribuído consultando a telemetria desse serviço.

## <a name="correlation-headers-using-w3c-tracecontext"></a>Cabeçalhos de correlação usando o W3C TraceContext

Application Insights está fazendo a transição para o [contexto de rastreamento W3C](https://w3c.github.io/trace-context/), que define:

- `traceparent`: Transporta a ID da operação globalmente exclusiva e o identificador exclusivo da chamada.
- `tracestate`: Transporta o contexto de rastreamento específico do sistema.

A versão mais recente do SDK do Application Insights dá suporte ao protocolo Trace-Context, mas talvez seja necessário aceitá-lo. (A compatibilidade com versões anteriores com o protocolo de correlação anterior com suporte pelo SDK do Application Insights será mantida.)

O [protocolo http de correlação, também chamado Request-ID](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md), está sendo preterido. Esse protocolo define dois cabeçalhos:

- `Request-Id`: Transporta a ID global exclusiva da chamada.
- `Correlation-Context`: Transporta a coleção de pares nome-valor das propriedades de rastreamento distribuído.

Application Insights também define a [extensão](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md) para o protocolo http de correlação. Ele usa pares nome-valor de `Request-Context` para propagar a coleção de propriedades usadas pelo computador chamado ou chamador imediato. O SDK do Application Insights usa esse cabeçalho para definir `dependency.target` os `request.source` campos e.

Os modelos de dados do [W3C Trace-Context](https://w3c.github.io/trace-context/) e Application insights data são mapeados da seguinte maneira:

| Application Insights                   | TraceContext do W3C                                      |
|------------------------------------    |-------------------------------------------------    |
| `Request`, `PageView`                  | `SpanKind` é servidor se síncrono; `SpanKind` é consumidor se assíncrono                    |
| `Dependency`                           | `SpanKind` é cliente se síncrono; `SpanKind` é produtor se assíncrono                   |
| `Id` de `Request` e `Dependency`     | `SpanId`                                            |
| `Operation_Id`                         | `TraceId`                                           |
| `Operation_ParentId`                   | `SpanId` da extensão pai deste span. Se esse for um Span raiz, esse campo deverá estar vazio.     |

Para obter mais informações, consulte [Application insights modelo de dados de telemetria](../../azure-monitor/app/data-model.md).

### <a name="enable-w3c-distributed-tracing-support-for-classic-aspnet-apps"></a>Habilitar o suporte de rastreamento distribuído do W3C para aplicativos clássicos do ASP.NET
 
  > [!NOTE]
  >  A partir do `Microsoft.ApplicationInsights.Web` e do `Microsoft.ApplicationInsights.DependencyCollector` , nenhuma configuração é necessária.

O suporte ao contexto de rastreamento W3C é implementado de forma compatível com versões anteriores. Espera-se que a correlação funcione com aplicativos que são instrumentados com versões anteriores do SDK (sem suporte W3C).

Se você quiser continuar usando o protocolo herdado `Request-Id` , poderá desabilitar o Trace-Context usando essa configuração:

```csharp
  Activity.DefaultIdFormat = ActivityIdFormat.Hierarchical;
  Activity.ForceDefaultIdFormat = true;
```

Se você executar uma versão mais antiga do SDK, recomendamos atualizá-la ou aplicar a configuração a seguir para habilitar o Trace-Context.
Esse recurso está disponível nos `Microsoft.ApplicationInsights.Web` pacotes e `Microsoft.ApplicationInsights.DependencyCollector` , começando com a versão 2.8.0-beta1.
Isso está desabilitado por padrão. Para habilitá-lo, faça essas alterações em `ApplicationInsights.config` :

- Em `RequestTrackingTelemetryModule` , adicione o `EnableW3CHeadersExtraction` elemento e defina seu valor como `true` .
- Em `DependencyTrackingTelemetryModule` , adicione o `EnableW3CHeadersInjection` elemento e defina seu valor como `true` .
- Adicione `W3COperationCorrelationTelemetryInitializer` em `TelemetryInitializers` . Ele será semelhante a este exemplo:

```xml
<TelemetryInitializers>
  <Add Type="Microsoft.ApplicationInsights.Extensibility.W3C.W3COperationCorrelationTelemetryInitializer, Microsoft.ApplicationInsights"/>
   ...
</TelemetryInitializers>
```

### <a name="enable-w3c-distributed-tracing-support-for-aspnet-core-apps"></a>Habilitar o suporte de rastreamento distribuído do W3C para aplicativos ASP.NET Core

 > [!NOTE]
  > A partir da `Microsoft.ApplicationInsights.AspNetCore` versão 2.8.0, nenhuma configuração é necessária.
 
O suporte ao contexto de rastreamento W3C é implementado de forma compatível com versões anteriores. Espera-se que a correlação funcione com aplicativos que são instrumentados com versões anteriores do SDK (sem suporte W3C).

Se você quiser continuar usando o protocolo herdado `Request-Id` , poderá desabilitar o Trace-Context usando essa configuração:

```csharp
  Activity.DefaultIdFormat = ActivityIdFormat.Hierarchical;
  Activity.ForceDefaultIdFormat = true;
```

Se você executar uma versão mais antiga do SDK, recomendamos atualizá-la ou aplicar a configuração a seguir para habilitar o Trace-Context.

Esse recurso está no `Microsoft.ApplicationInsights.AspNetCore` versão 2.5.0-beta1 e no `Microsoft.ApplicationInsights.DependencyCollector` versão 2.8.0-beta1.
Isso está desabilitado por padrão. Para habilitar, configure `ApplicationInsightsServiceOptions.RequestCollectionOptions.EnableW3CDistributedTracing` como `true`:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry(o => 
        o.RequestCollectionOptions.EnableW3CDistributedTracing = true );
    // ....
}
```

### <a name="enable-w3c-distributed-tracing-support-for-java-apps"></a>Habilitar suporte a rastreamento distribuído do W3C para aplicativos Java

#### <a name="java-30-agent"></a>Agente do Java 3,0

  O agente do Java 3,0 dá suporte ao W3C pronto para uso e nenhuma configuração adicional é necessária. 

#### <a name="java-sdk"></a>Java SDK
- **Configuração de entrada**

  - Para aplicativos Java EE, adicione o seguinte à `<TelemetryModules>` marca em ApplicationInsights.xml:

    ```xml
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule>
       <Param name = "W3CEnabled" value ="true"/>
       <Param name ="enableW3CBackCompat" value = "true" />
    </Add>
    ```
    
  - Para aplicativos Spring boot, adicione estas propriedades:

    - `azure.application-insights.web.enable-W3C=true`
    - `azure.application-insights.web.enable-W3C-backcompat-mode=true`

- **Configuração de saída**

  Adicione o seguinte ao AI-Agent.xml:

  ```xml
  <Instrumentation>
    <BuiltIn enabled="true">
      <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
    </BuiltIn>
  </Instrumentation>
  ```

  > [!NOTE]
  > o modo de compatibilidade com versões anteriores está habilitado por padrão e o parâmetro `enableW3CBackCompat` é opcional. Use-o somente quando quiser desativar a compatibilidade com versões anteriores.
  >
  > O ideal seria desativá-lo quando todos os seus serviços forem atualizados para versões mais recentes dos SDKs compatíveis com o protocolo W3C. É altamente recomendável que você mude para os SDKs mais novos assim que possível.

> [!IMPORTANT]
> Verifique se as configurações de entrada e saída são exatamente iguais.

### <a name="enable-w3c-distributed-tracing-support-for-web-apps"></a>Habilitar o suporte ao rastreamento distribuído W3C para aplicativos Web

Este recurso está em `Microsoft.ApplicationInsights.JavaScript` . Isso está desabilitado por padrão. Para habilitá-lo, use `distributedTracingMode` config. AI_AND_W3C é fornecida para compatibilidade com versões anteriores com qualquer serviço herdado instrumentado por Application Insights.

- **configuração do NPM (ignorar se estiver usando a configuração de trecho de código)**

  ```javascript
  import { ApplicationInsights, DistributedTracingModes } from '@microsoft/applicationinsights-web';

  const appInsights = new ApplicationInsights({ config: {
    instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
    distributedTracingMode: DistributedTracingModes.W3C
    /* ...other configuration options... */
  } });
  appInsights.loadAppInsights();
  ```
  
- **Configuração de trecho de código (ignorar se estiver usando a instalação do NPM)**

  ```
  <script type="text/javascript">
  var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){i[e]=function(){var n=arguments;i.queue.push(function(){i[e].apply(i,n)})}}var i={config:e};i.initialize=!0;var a=document,t=window;setTimeout(function(){var n=a.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",a.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{i.cookie=a.cookie}catch(e){}i.queue=[],i.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var o="Track"+r[0];if(n("start"+o),n("stop"+o),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var s=t[r];t[r]=function(e,n,a,t,o){var c=s&&s(e,n,a,t,o);return!0!==c&&i["_"+r]({message:e,url:n,lineNumber:a,columnNumber:t,error:o}),c},e.autoExceptionInstrumented=!0}return i}
  (
    {
      instrumentationKey:"INSTRUMENTATION_KEY",
      distributedTracingMode: 2 // DistributedTracingModes.W3C
      /* ...other configuration options... */
    }
  );
  window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
  </script>
  ```

## <a name="telemetry-correlation-in-opencensus-python"></a>Correlação de telemetria no OpenCensus Python

O Python OpenCensus dá suporte ao [contexto de rastreamento W3C](https://w3c.github.io/trace-context/) sem a necessidade de configuração adicional.

Como referência, o modelo de dados OpenCensus pode ser encontrado [aqui](https://github.com/census-instrumentation/opencensus-specs/tree/master/trace).

### <a name="incoming-request-correlation"></a>Correlação de solicitação de entrada

OpenCensus Python correlaciona cabeçalhos de contexto de rastreamento W3C de solicitações de entrada para as extensões que são geradas a partir das solicitações. O OpenCensus fará isso automaticamente com integrações para essas estruturas de aplicativos Web populares: Flask, Django e pirâmide. Você só precisa popular os cabeçalhos de contexto de rastreamento do W3C com o [formato correto](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format) e enviá-los com a solicitação. Veja um exemplo de aplicativo Flask que demonstra isso:

```python
from flask import Flask
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.ext.flask.flask_middleware import FlaskMiddleware
from opencensus.trace.samplers import ProbabilitySampler

app = Flask(__name__)
middleware = FlaskMiddleware(
    app,
    exporter=AzureExporter(),
    sampler=ProbabilitySampler(rate=1.0),
)

@app.route('/')
def hello():
    return 'Hello World!'

if __name__ == '__main__':
    app.run(host='localhost', port=8080, threaded=True)
```

Esse código executa um aplicativo Flask de exemplo em seu computador local, ouvindo a porta `8080` . Para correlacionar o contexto de rastreamento, você envia uma solicitação ao ponto de extremidade. Neste exemplo, você pode usar um `curl` comando:
```
curl --header "traceparent: 00-4bf92f3577b34da6a3ce929d0e0e4736-00f067aa0ba902b7-01" localhost:8080
```
Ao examinar o [formato de cabeçalho do contexto de rastreamento](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format), você pode derivar as seguintes informações:

`version`: `00`

`trace-id`: `4bf92f3577b34da6a3ce929d0e0e4736`

`parent-id/span-id`: `00f067aa0ba902b7`

`trace-flags`: `01`

Se você examinar a entrada de solicitação que foi enviada para Azure Monitor, poderá ver os campos preenchidos com as informações do cabeçalho de rastreamento. Você pode encontrar esses dados em logs (análise) no recurso de Application Insights de Azure Monitor.

![Solicitar telemetria em logs (análise)](./media/opencensus-python/0011-correlation.png)

O `id` campo está no formato `<trace-id>.<span-id>` , em que o `trace-id` é tirado do cabeçalho de rastreamento que foi passado na solicitação e o `span-id` é uma matriz de 8 bytes gerada para esse intervalo.

O `operation_ParentId` campo está no formato `<trace-id>.<parent-id>` , onde `trace-id` e e `parent-id` são tirados do cabeçalho de rastreamento que foi passado na solicitação.

### <a name="log-correlation"></a>Correlação de logs

O OpenCensus Python permite que você correlacione os logs adicionando uma ID de rastreamento, uma ID de extensão e um sinalizador de amostragem aos registros de log. Você adiciona esses atributos instalando a [integração de log](https://pypi.org/project/opencensus-ext-logging/)do OpenCensus. Os seguintes atributos serão adicionados aos objetos Python `LogRecord` : `traceId` , `spanId` e `traceSampled` . Observe que isso só entra em vigor para agentes que são criados após a integração.

Aqui está um aplicativo de exemplo que demonstra isso:

```python
import logging

from opencensus.trace import config_integration
from opencensus.trace.samplers import AlwaysOnSampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['logging'])
logging.basicConfig(format='%(asctime)s traceId=%(traceId)s spanId=%(spanId)s %(message)s')
tracer = Tracer(sampler=AlwaysOnSampler())

logger = logging.getLogger(__name__)
logger.warning('Before the span')
with tracer.span(name='hello'):
    logger.warning('In the span')
logger.warning('After the span')
```
Quando esse código é executado, as seguintes impressões são impressas no console do:
```
2019-10-17 11:25:59,382 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 Before the span
2019-10-17 11:25:59,384 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=70da28f5a4831014 In the span
2019-10-17 11:25:59,385 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 After the span
```
Observe que há um `spanId` presente para a mensagem de log que está dentro do intervalo. Isso é o mesmo `spanId` que pertence ao trecho nomeado `hello` .

Você pode exportar os dados de log usando o `AzureLogHandler` . Para obter mais informações, consulte [este artigo](./opencensus-python.md#logs).

## <a name="telemetry-correlation-in-net"></a>Correlação de telemetria em .NET

Ao longo do tempo, o .NET definiu várias maneiras de correlacionar os logs de telemetria e de diagnóstico:

- `System.Diagnostics.CorrelationManager` permite o acompanhamento de [LogicalOperationStack e ActivityId](/dotnet/api/system.diagnostics.correlationmanager?view=netcore-3.1).
- O `System.Diagnostics.Tracing.EventSource` e o ETW (Rastreamento de Eventos para Windows) definem o método [SetCurrentThreadActivityId](/dotnet/api/system.diagnostics.tracing.eventsource.setcurrentthreadactivityid?view=netcore-3.1#overloads).
- `ILogger` usa [escopos de log](/aspnet/core/fundamentals/logging#log-scopes).
- O WCF (Windows Communication Foundation) e o HTTP conectam a propagação de contexto "atual".

Mas esses métodos não habilitam o suporte automático a rastreamento distribuído. `DiagnosticSource` dá suporte à correlação automática entre computadores. As bibliotecas do .NET dão suporte `DiagnosticSource` e permitem a propagação automática entre computadores do contexto de correlação por meio do transporte, como http.

O [Guia do usuário da atividade](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) no `DiagnosticSource` explica os conceitos básicos do acompanhamento de atividades.

ASP.NET Core 2,0 dá suporte à extração de cabeçalhos HTTP e à inicialização de novas atividades.

`System.Net.Http.HttpClient`, a partir da versão 4.1.0, dá suporte à injeção automática de cabeçalhos HTTP de correlação e ao acompanhamento de chamadas HTTP como atividades.

Há um novo módulo HTTP, [Microsoft. AspNet. TelemetryCorrelation](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation/), para ASP.net clássicas. Este módulo implementa a correlação de telemetria usando `DiagnosticSource`. Ele inicia uma atividade com base nos cabeçalhos de solicitação de entrada. Ele também correlaciona a telemetria de diferentes estágios de processamento de solicitação, mesmo quando cada estágio do processamento de Serviços de Informações da Internet (IIS) é executado em um thread gerenciado diferente.

O SDK do Application Insights, a partir da versão 2.4.0-beta1, usa `DiagnosticSource` e `Activity` para coletar telemetria e associá-la à atividade atual.

<a name="java-correlation"></a>
## <a name="telemetry-correlation-in-java"></a>Correlação de telemetria em Java

O [agente Java](./java-in-process-agent.md) , bem como [Java SDK](../../azure-monitor/app/java-get-started.md) versão 2.0.0 ou posterior, dá suporte à correlação automática de telemetria. Ele preenche automaticamente `operation_id` todas as telemetrias (como rastreamentos, exceções e eventos personalizados) emitidos dentro do escopo de uma solicitação. Ele também propaga os cabeçalhos de correlação (descritos anteriormente) para chamadas de serviço a serviço via HTTP, se o [agente do SDK do Java](../../azure-monitor/app/java-agent.md) estiver configurado.

> [!NOTE]
> Application Insights o agente Java coleta automaticamente solicitações e dependências para JMS, Kafka, Subnety/webfluxo e muito mais. Para o SDK do Java, somente as chamadas feitas por meio do Apache HttpClient têm suporte para o recurso de correlação. A propagação automática de contexto entre tecnologias de mensagens (como Kafka, RabbitMQ e barramento de serviço do Azure) não tem suporte no SDK. 

> [!NOTE]
> Para coletar telemetria personalizada, você precisa instrumentar o aplicativo com o SDK do Java 2,6. 

### <a name="role-names"></a>Nomes de função

Talvez você queira personalizar a maneira como os nomes de componentes são exibidos no [mapa do aplicativo](../../azure-monitor/app/app-map.md). Para fazer isso, você pode definir manualmente o `cloud_RoleName` executando uma das seguintes ações:

- Para Application Insights o agente Java 3,0, defina o nome da função de nuvem da seguinte maneira:

    ```json
    {
      "instrumentationSettings": {
        "preview": {
          "roleName": "my cloud role name"
        }
      }
    }
    ```
    Você também pode definir o nome da função de nuvem usando a variável de ambiente `APPLICATIONINSIGHTS_ROLE_NAME` .

- Com Application Insights SDK do Java 2.5.0 e posterior, você pode especificar o `cloud_RoleName` adicionando `<RoleName>` ao arquivo de ApplicationInsights.xml:

  ```XML
  <?xml version="1.0" encoding="utf-8"?>
  <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">
     <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>
     <RoleName>** Your role name **</RoleName>
     ...
  </ApplicationInsights>
  ```

- Se você usar o Spring boot com o iniciador do Spring boot Application Insights, precisará apenas definir seu nome personalizado para o aplicativo no arquivo Application. Properties:

  `spring.application.name=<name-of-app>`

  O iniciador do Spring boot atribui automaticamente `cloudRoleName` ao valor inserido para a `spring.application.name` propriedade.

## <a name="next-steps"></a>Próximas etapas

- Gravar [telemetria personalizada](../../azure-monitor/app/api-custom-events-metrics.md).
- Para cenários de correlação avançada em ASP.NET Core e ASP.NET, consulte [rastrear operações personalizadas](custom-operations-tracking.md).
- Saiba mais sobre como [configurar cloud_RoleName](./app-map.md#set-or-override-cloud-role-name) para outros SDKs.
- Integrar todos os componentes do seu microsserviço ao Application Insights. Consultar as [plataformas compatíveis](./platforms.md).
- Consultar o [modelo de dados](./data-model.md) para os tipos do Application Insights.
- Saiba como [estender e filtrar a telemetria](./api-filtering-sampling.md).
- Revisar a [Referência de configuração do Application Insights](configuration-with-applicationinsights-config.md).
