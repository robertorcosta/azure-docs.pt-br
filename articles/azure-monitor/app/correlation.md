---
title: correlação de telemetria no Azure Application Insights | Microsoft Docs
description: Correlação no Application Insights Telemetry
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 06/07/2019
ms.reviewer: sergkanz
ms.openlocfilehash: c68b83726371d346019d18d0b066173f93196e6d
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80982048"
---
# <a name="telemetry-correlation-in-application-insights"></a>Correlação de telemetria no Application Insights

No mundo dos microsserviços, cada operação lógica requer que o trabalho seja feito em vários componentes do serviço. Você pode monitorar cada um desses componentes separadamente usando [o Application Insights](../../azure-monitor/app/app-insights-overview.md). O Application Insights é compatível com a correlação de telemetria distribuída, que você pode usar para detectar qual componente é responsável por falhas ou degradação do desempenho.

Este artigo explica o modelo de dados usado pelo Application Insights para correlacionar a telemetria enviada por vários componentes. Ele aborda protocolos e técnicas de propagação de contexto. Também abrange a implementação de táticas de correlação em diferentes idiomas e plataformas.

## <a name="data-model-for-telemetry-correlation"></a>Modelo de dados para correlação de telemetria

O Application Insights define um [modelo de dados](../../azure-monitor/app/data-model.md) para correlação de telemetria distribuída. Para associar a telemetria a uma operação lógica, cada item de telemetria tem um campo de contexto chamado `operation_Id`. Esse identificador é compartilhado por todos os itens de telemetria no rastreamento distribuído. Assim, mesmo se você perder a telemetria de uma única camada, você ainda pode associar a telemetria relatada por outros componentes.

Uma operação lógica distribuída normalmente consiste em um conjunto de operações menores que são solicitações processadas por um dos componentes. Essas operações são definidas por [telemetria de solicitação](../../azure-monitor/app/data-model-request-telemetry.md). Cada item de telemetria `id` de solicitação tem o seu próprio que o identifica de forma única e global. E todos os itens de telemetria (como traços e exceções) `operation_parentId` associados à solicitação `id`devem definir o valor da solicitação .

Toda operação de saída, como uma chamada HTTP para outro componente, é representada pela [telemetria de dependência](../../azure-monitor/app/data-model-dependency-telemetry.md). A telemetria de dependência `id` também define a sua própria que é globalmente única. A telemetria de solicitação, iniciada por essa chamada de dependência, usa este `id` como `operation_parentId`.

Você pode compilar um modo de exibição de operação lógica distribuída usando `operation_Id`, `operation_parentId` e `request.id` com `dependency.id`. Esses campos também definem a ordem de causalidade das chamadas de telemetria.

Em um ambiente de microsserviços, os rastreamentos de componentes podem ir para itens de armazenamento diferentes. Todo componente pode ter sua própria chave de instrumentação no Application Insights. Para obter telemetria para a operação lógica, o Application Insights consulta dados de cada item de armazenamento. Quando o número de itens de armazenamento for grande, você precisará de uma dica sobre onde procurar a seguir. O modelo de dados do Application Insights define dois campos para resolver esse problema: `request.source` e `dependency.target`. O primeiro campo identifica o componente que iniciou a solicitação de dependência. O segundo campo identifica qual componente retornou a resposta da chamada de dependência.

## <a name="example"></a>Exemplo

Vamos examinar um exemplo. Um aplicativo chamado Stock Prices mostra o preço de mercado atual de uma ação usando uma API externa chamada Stock. O aplicativo Preços de Ações tem uma página chamada `GET /Home/Stock`Stock page que o navegador web cliente abre usando . O aplicativo consulta a API de `GET /api/stock/value`ações usando a chamada HTTP .

Você pode analisar a telemetria resultante executando uma consulta:

```kusto
(requests | union dependencies | union pageViews)
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

Nos resultados, observe que todos os itens de telemetria compartilham a raiz `operation_Id`. Quando uma chamada do Ajax é feita a`qJSXU`partir da página, um novo ID exclusivo () é atribuído à `operation_ParentId`telemetria de dependência, e o ID da páginaView é usado como . A solicitação do servidor, em seguida, usa a ID do Ajax como `operation_ParentId`.

| itemType   | name                      | ID           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| pageView   | Página de ações                |              | STYz               | STYz         |
| dependência | GET /Home/Stock           | qJSXU        | STYz               | STYz         |
| solicitação    | GET Home/Stock            | KqKwlrSt9PA= | qJSXU              | STYz         |
| dependência | GET /api/stock/value      | bBrf2L7mm2g= | KqKwlrSt9PA=       | STYz         |

Quando a `GET /api/stock/value` chamada é feita para um serviço externo, você precisa saber `dependency.target` a identidade desse servidor para que você possa definir o campo adequadamente. Quando o serviço externo não oferece suporte a monitoramento, `target` é definido como o nome do host do serviço (por exemplo, `stock-prices-api.com`). Mas se o serviço se identificar retornando um `target` cabeçalho HTTP predefinido, contenha a identidade de serviço que permite que o Application Insights construa um trace distribuído consultando a telemetria a partir desse serviço.

## <a name="correlation-headers"></a>Cabeçalhos de correlação

O Application Insights está em transição para [o W3C Trace-Context](https://w3c.github.io/trace-context/), que define:

- `traceparent`: Carrega o ID de operação globalmente único e identificador exclusivo da chamada.
- `tracestate`: Carrega contexto de rastreamento específico do sistema.

A versão mais recente do Application Insights SDK suporta o protocolo Trace-Context, mas talvez você precise optar por ele. (A compatibilidade retrógrada com o protocolo de correlação anterior suportado pelo Application Insights SDK será mantida.)

O [protocolo HTTP de correlação, também chamado request-id,](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md)está sendo preterido. Este protocolo define dois cabeçalhos:

- `Request-Id`: Carrega a identificação globalmente única da chamada.
- `Correlation-Context`: Carrega a coleção de pares de valor de nome das propriedades de rastreamento distribuídas.

O Application Insights também define a [extensão](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md) para o protocolo HTTP de correlação. Ele usa pares nome-valor de `Request-Context` para propagar a coleção de propriedades usadas pelo computador chamado ou chamador imediato. O Application Insights SDK usa `dependency.target` este `request.source` cabeçalho para definir os campos e.

### <a name="enable-w3c-distributed-tracing-support-for-classic-aspnet-apps"></a>Habilitar o suporte de rastreamento distribuído do W3C para aplicativos clássicos do ASP.NET
 
  > [!NOTE]
  >  Começando `Microsoft.ApplicationInsights.Web` com `Microsoft.ApplicationInsights.DependencyCollector`e , nenhuma configuração é necessária.

O suporte ao Rastreamento-Contexto W3C é implementado de forma compatível com o atraso. Espera-se que a correlação funcione com aplicações que são instrumentadas com versões anteriores do SDK (sem suporte ao W3C).

Se você quiser continuar `Request-Id` usando o protocolo legado, você pode desativar o Trace-Context usando esta configuração:

```csharp
  Activity.DefaultIdFormat = ActivityIdFormat.Hierarchical;
  Activity.ForceDefaultIdFormat = true;
```

Se você executar uma versão mais antiga do SDK, recomendamos que você atualize-o ou aplique a seguinte configuração para ativar o Trace-Context.
Este recurso está `Microsoft.ApplicationInsights.Web` disponível nos pacotes, `Microsoft.ApplicationInsights.DependencyCollector` começando com a versão 2.8.0-beta1.
Isso está desabilitado por padrão. Para habilitá-lo, `ApplicationInsights.config`faça essas alterações em:

- Em `RequestTrackingTelemetryModule`, `EnableW3CHeadersExtraction` adicione o elemento `true`e defina seu valor para .
- Em `DependencyTrackingTelemetryModule`, `EnableW3CHeadersInjection` adicione o elemento `true`e defina seu valor para .
- Adicione `W3COperationCorrelationTelemetryInitializer` `TelemetryInitializers`em . Será semelhante a este exemplo:

```xml
<TelemetryInitializers>
  <Add Type="Microsoft.ApplicationInsights.Extensibility.W3C.W3COperationCorrelationTelemetryInitializer, Microsoft.ApplicationInsights"/>
   ...
</TelemetryInitializers>
```

### <a name="enable-w3c-distributed-tracing-support-for-aspnet-core-apps"></a>Habilitar o suporte de rastreamento distribuído do W3C para aplicativos ASP.NET Core

 > [!NOTE]
  > A `Microsoft.ApplicationInsights.AspNetCore` partir da versão 2.8.0, nenhuma configuração é necessária.
 
O suporte ao Rastreamento-Contexto W3C é implementado de forma compatível com o atraso. Espera-se que a correlação funcione com aplicações que são instrumentadas com versões anteriores do SDK (sem suporte ao W3C).

Se você quiser continuar `Request-Id` usando o protocolo legado, você pode desativar o Trace-Context usando esta configuração:

```csharp
  Activity.DefaultIdFormat = ActivityIdFormat.Hierarchical;
  Activity.ForceDefaultIdFormat = true;
```

Se você executar uma versão mais antiga do SDK, recomendamos que você atualize-o ou aplique a seguinte configuração para ativar o Trace-Context.

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

- **Configuração de entrada**

  - Para aplicativos Java EE, adicione `<TelemetryModules>` o seguinte à tag em ApplicationInsights.xml:

    ```xml
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule>
       <Param name = "W3CEnabled" value ="true"/>
       <Param name ="enableW3CBackCompat" value = "true" />
    </Add>
    ```
    
  - Para aplicativos spring boot, adicione essas propriedades:

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
> Certifique-se de que as configurações de entrada e saída são exatamente as mesmas.

### <a name="enable-w3c-distributed-tracing-support-for-web-apps"></a>Habilite o suporte de rastreamento distribuído W3C para aplicativos da Web

Este recurso `Microsoft.ApplicationInsights.JavaScript`está em . Isso está desabilitado por padrão. Para habilitá-lo, use `distributedTracingMode` a configuração. AI_AND_W3C é fornecida para compatibilidade retrógrada com quaisquer serviços legados instrumentados pelo Application Insights.

- **configuração npm (ignorar se usar configuração snippet)**

  ```javascript
  import { ApplicationInsights, DistributedTracingModes } from '@microsoft/applicationinsights-web';

  const appInsights = new ApplicationInsights({ config: {
    instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
    distributedTracingMode: DistributedTracingModes.W3C
    /* ...other configuration options... */
  } });
  appInsights.loadAppInsights();
  ```
  
- **Configuração de snippet (ignorar se usar a configuração npm)**

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

## <a name="opentracing-and-application-insights"></a>OpenTracing e Application Insights

A [especificação do modelo de dados do OpenTracing](https://opentracing.io/) e os modelos de dados do Application Insights são mapeados da seguinte maneira:

| Application Insights                   | OpenTracing                                        |
|------------------------------------    |-------------------------------------------------    |
| `Request`, `PageView`                  | `Span` com `span.kind = server`                    |
| `Dependency`                           | `Span` com `span.kind = client`                    |
| `Id` de `Request` e `Dependency`     | `SpanId`                                            |
| `Operation_Id`                         | `TraceId`                                           |
| `Operation_ParentId`                   | `Reference` do tipo `ChildOf` (o intervalo pai)     |

Para obter mais informações, consulte [o modelo de dados de telemetria application insights](../../azure-monitor/app/data-model.md).

Para definições de conceitos opentracing, consulte a [especificação](https://github.com/opentracing/specification/blob/master/specification.md) OpenTracing e [convenções semânticas](https://github.com/opentracing/specification/blob/master/semantic_conventions.md).

## <a name="telemetry-correlation-in-opencensus-python"></a>Correlação de telemetria no OpenCensus Python

OpenCensus Python segue `OpenTracing` as especificações do modelo de dados descritas anteriormente. Ele também suporta [o W3C Trace-Context](https://w3c.github.io/trace-context/) sem exigir qualquer configuração.

### <a name="incoming-request-correlation"></a>Correlação de solicitação recebida

O OpenCensus Python correlaciona os cabeçalhos do Contexto de Rastreamento W3C das solicitações recebidas para os intervalos gerados a partir das próprias solicitações. O OpenCensus fará isso automaticamente com integrações para essas estruturas de aplicativos web populares: Flask, Django e Pyramid. Você só precisa preencher os cabeçalhos de contexto de rastreamento W3C com o [formato correto](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format) e enviá-los com a solicitação. Aqui está uma amostra de aplicativo de frasco que demonstra isso:

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

Este código executa um aplicativo flask de `8080`exemplo em sua máquina local, ouvindo a porta . Para correlacionar o contexto de rastreamento, você envia uma solicitação para o ponto final. Neste exemplo, você pode `curl` usar um comando:
```
curl --header "traceparent: 00-4bf92f3577b34da6a3ce929d0e0e4736-00f067aa0ba902b7-01" localhost:8080
```
Olhando para o [formato de cabeçalho Trace-Context,](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format)você pode obter as seguintes informações:

`version`: `00`

`trace-id`: `4bf92f3577b34da6a3ce929d0e0e4736`

`parent-id/span-id`: `00f067aa0ba902b7`

`trace-flags`: `01`

Se você olhar para a entrada de solicitação enviada ao Azure Monitor, você poderá ver campos preenchidos com as informações do cabeçalho de rastreamento. Você pode encontrar esses dados em Logs (Analytics) no recurso Azure Monitor Application Insights.

![Solicitar telemetria em Logs (Analytics)](./media/opencensus-python/0011-correlation.png)

O `id` campo está `<trace-id>.<span-id>`no formato `trace-id` , onde o é retirado do cabeçalho de rastreamento que foi passado na solicitação e o `span-id` é uma matriz gerada de 8 bytes para este período.

O `operation_ParentId` campo está `<trace-id>.<parent-id>`no formato `trace-id` , `parent-id` onde tanto o e o são retirados do cabeçalho de rastreamento que foi passado na solicitação.

### <a name="log-correlation"></a>Correlação de log

O OpenCensus Python permite correlacionar logs adicionando um ID de rastreamento, um ID de extensão e um sinalizador de amostragem para registrar registros. Você adiciona esses atributos instalando a [integração de registro](https://pypi.org/project/opencensus-ext-logging/)openCensus . Os seguintes atributos `LogRecord` serão `traceId`adicionados aos objetos Python: , `spanId`e `traceSampled`. Observe que isso só tem efeito para madeireiros que são criados após a integração.

Aqui está uma aplicação de exemplo que demonstra isso:

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
Quando este código é executado, as seguintes impressões no console:
```
2019-10-17 11:25:59,382 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 Before the span
2019-10-17 11:25:59,384 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=70da28f5a4831014 In the span
2019-10-17 11:25:59,385 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 After the span
```
Note que há `spanId` um presente para a mensagem de registro que está dentro do intervalo. Este é `spanId` o mesmo que `hello`pertence ao vão chamado.

Você pode exportar os `AzureLogHandler`dados de log usando . Para obter mais informações, consulte [este artigo](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python#logs).

## <a name="telemetry-correlation-in-net"></a>Correlação de telemetria em .NET

Ao longo do tempo, .NET definiu várias maneiras de correlacionar registros de telemetria e diagnóstico:

- `System.Diagnostics.CorrelationManager`permite o rastreamento de [LogicalOperationStack e ActivityId](https://msdn.microsoft.com/library/system.diagnostics.correlationmanager.aspx).
- O `System.Diagnostics.Tracing.EventSource` e o ETW (Rastreamento de Eventos para Windows) definem o método [SetCurrentThreadActivityId](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.setcurrentthreadactivityid.aspx).
- `ILogger`usa [escopos log](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes).
- O WCF (Windows Communication Foundation) e o HTTP conectam a propagação de contexto "atual".

Mas esses métodos não permitiram suporte automático de rastreamento distribuído. `DiagnosticSource`suporta correlação automática entre máquinas. As bibliotecas `DiagnosticSource` .NET suportam e permitem a propagação automática entre máquinas do contexto de correlação através do transporte, como http.

O Guia do `DiagnosticSource` Usuário [de Atividades](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) explica o básico das atividades de rastreamento.

ASP.NET Core 2.0 suporta a extração de cabeçalhos HTTP e o início de novas atividades.

`System.Net.Http.HttpClient`, começando com a versão 4.1.0, suporta injeção automática de cabeçalhos HTTP correlação e rastreamento de chamadas HTTP como atividades.

Há um novo módulo HTTP, [Microsoft.AspNet.TelemetriaCorrelation](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation/), para ASP.NET clássica. Este módulo implementa a correlação de telemetria usando `DiagnosticSource`. Ele inicia uma atividade com base nos cabeçalhos de solicitação de entrada. Também correlaciona a telemetria das diferentes etapas do processamento de solicitações, mesmo quando cada etapa do processamento do Internet Information Services (IIS) é executada em um segmento gerenciado diferente.

O SDK do Application Insights, a partir da versão 2.4.0-beta1, usa `DiagnosticSource` e `Activity` para coletar telemetria e associá-la à atividade atual.

<a name="java-correlation"></a>
## <a name="telemetry-correlation-in-the-java"></a>Correlação de telemetria no Java

[O agente Java do Application Insights,](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent) bem como a versão 2.0.0 do [Java SDK](../../azure-monitor/app/java-get-started.md) ou posterior suporta correlação automática de telemetria. Ele preenche automaticamente `operation_id` para todas as telemetrias (como traços, exceções e eventos personalizados) emitidas no âmbito de uma solicitação. Ele também propaga os cabeçalhos de correlação (descritos anteriormente) para chamadas de serviço por serviço via HTTP, se o [agente Java SDK](../../azure-monitor/app/java-agent.md) estiver configurado.

> [!NOTE]
> O agente Java do Application Insights coleta automaticamente solicitações e dependências para JMS, Kafka, Netty/Webflux e muito mais. Para Java SDK, apenas chamadas feitas via Apache HttpClient são suportadas para o recurso de correlação. A propagação automática do contexto entre tecnologias de mensagens (como Kafka, RabbitMQ e Azure Service Bus) não é suportada no SDK. 

<a name="java-role-name"></a>
## <a name="role-name"></a>Nome da função

Você pode querer personalizar a forma como os nomes dos componentes são exibidos no [Mapa de Aplicativos](../../azure-monitor/app/app-map.md). Para isso, você pode definir `cloud_RoleName` manualmente uma das seguintes ações:

- Com o Application Insights Java SDK 2.5.0 `cloud_RoleName` e `<RoleName>` posterior, você pode especificar o adicionando ao seu arquivo ApplicationInsights.xml:

  ```XML
  <?xml version="1.0" encoding="utf-8"?>
  <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">
     <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>
     <RoleName>** Your role name **</RoleName>
     ...
  </ApplicationInsights>
  ```

- Se você usar o Spring Boot com o Inicial de Inicialização de Mola do Aplicativo Insights, você só precisa definir seu nome personalizado para o aplicativo no arquivo application.properties:

  `spring.application.name=<name-of-app>`

  O Spring Boot Starter `cloudRoleName` atribui automaticamente o valor `spring.application.name` que você digita para a propriedade.

## <a name="next-steps"></a>Próximas etapas

- Escreva [telemetria personalizada](../../azure-monitor/app/api-custom-events-metrics.md).
- Para obter cenários avançados de correlação em ASP.NET Core e ASP.NET, consulte [Acompanhar operações personalizadas](custom-operations-tracking.md).
- Saiba mais sobre [como definir cloud_RoleName](../../azure-monitor/app/app-map.md#set-cloud-role-name) para outros SDKs.
- Integrar todos os componentes do seu microsserviço ao Application Insights. Consultar as [plataformas compatíveis](../../azure-monitor/app/platforms.md).
- Consultar o [modelo de dados](../../azure-monitor/app/data-model.md) para os tipos do Application Insights.
- Saiba como [estender e filtrar a telemetria](../../azure-monitor/app/api-filtering-sampling.md).
- Revisar a [Referência de configuração do Application Insights](configuration-with-applicationinsights-config.md).
