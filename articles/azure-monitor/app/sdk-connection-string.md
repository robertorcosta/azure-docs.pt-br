---
title: Strings de conexão no Azure Application Insights | Microsoft Docs
description: Como usar strings de conexão.
ms.topic: conceptual
author: timothymothra
ms.author: tilee
ms.date: 01/17/2020
ms.reviewer: mbullwin
ms.openlocfilehash: 25eda0ae2b0d873fe9850e5b886489a5f2590e69
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80410623"
---
# <a name="connection-strings"></a>Cadeias de conexão

## <a name="overview"></a>Visão geral

As seqüências de conexão fornecem aos usuários do Application Insight uma única configuração, eliminando a necessidade de várias configurações de proxy. Altamente útil para servidores web intranet, ambientes de nuvem soberanos ou híbridos que procuram enviar dados para o serviço de monitoramento.

Os pares de valor-chave fornecem uma maneira fácil para os usuários definirem uma combinação de sufixo prefixo para cada serviço/produto de Application Insights (AI).

> [!IMPORTANT]
> Não recomendamos a configuração da tecla Connection String e Instrumentation. No caso de um usuário definir ambos, o que foi definido por último terá precedência. 


## <a name="scenario-overview"></a>Visão geral do cenário 

Cenários de clientes onde visualizamos isso tendo mais impacto:

- Exceções de firewall ou redirecionamentos de proxy 

    Nos casos em que o monitoramento para servidor web intranet é necessário, nossa solução anterior pediu aos clientes para adicionar pontos finais de serviço individuais à sua configuração. Para mais informações, consulte [aqui](https://docs.microsoft.com/azure/azure-monitor/app/troubleshoot-faq#can-i-monitor-an-intranet-web-server). 
    As strings de conexão oferecem uma alternativa melhor reduzindo esse esforço para uma única configuração. Um simples prefixo, a alteração do sufixo permite a população automática e o redirecionamento de todos os pontos finais para os serviços certos. 

- Ambientes de nuvem soberanos ou híbridos

    Os usuários podem enviar dados para uma [região de governo azure](https://docs.microsoft.com/azure/azure-government/documentation-government-services-monitoringandmanagement#application-insights)definida.
    As strings de conexão permitem definir as configurações de ponto final para seus servidores de intranet ou configurações de nuvem híbrida. 

## <a name="getting-started"></a>Introdução

### <a name="finding-my-connection-string"></a>Encontrando minha corda de conexão?

A seqüência de conexões é exibida na lâmina visão geral do recurso Application Insights.

![string de conexão na lâmina de visão geral](media/overview-dashboard/overview-connection-string.png)

### <a name="schema"></a>Esquema

#### <a name="max-length"></a>Comprimento máximo

A conexão tem um comprimento máximo suportado de 4096 caracteres.

#### <a name="key-value-pairs"></a>Pares chave-valor

A seqüência de conexões consiste em uma lista de configurações representadas como pares de valor-chave separados por ponto e vírgula:`key1=value1;key2=value2;key3=value3`

#### <a name="syntax"></a>Sintaxe

- `InstrumentationKey`(ex: 00000000-0000-0000-0000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000  A seqüência de conexões é um campo **obrigatório.**
- `Authorization`(ex: ikey) (Esta configuração é opcional porque hoje nós apenas suportamos a autorização ikey.)
- `EndpointSuffix`(ex: applicationinsights.azure.cn) A configuração do sufixo de ponto final instruirá o SDK a que a nuvem do Azure se conectará. O SDK reunirá o resto do ponto final para serviços individuais.
- Pontos finais explícitos.
  Qualquer serviço pode ser explicitamente substituído na seqüência de conexões.
   - `IngestionEndpoint`(ex:https://dc.applicationinsights.azure.com)
   - `LiveEndpoint`(ex:https://live.applicationinsights.azure.com)
   - `ProfilerEndpoint`(ex:https://profiler.applicationinsights.azure.com)
   - `SnapshotEndpoint`(ex:https://snapshot.applicationinsights.azure.com)

#### <a name="endpoint-schema"></a>Esquema de ponto final

`<prefix>.<suffix>`
- Prefixo: Define um serviço. 
- Sufixo: Define o nome de domínio comum.

##### <a name="valid-suffixes"></a>Sufixos válidos

Aqui está uma lista de sufixos válidos 
- applicationinsights.azure.cn
- applicationinsights.us


Consulte também: https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification


##### <a name="valid-prefixes"></a>Prefixos válidos

- [Ingestão de Telemetria:](./app-insights-overview.md)`dc`
- [Métricas ao vivo](./live-stream.md):`live`
- [Perfil:](./profiler-overview.md)`profiler`
- [Instantâneo:](./snapshot-debugger.md)`snapshot`



## <a name="connection-string-examples"></a>Exemplos de seqüência de conexão


### <a name="minimal-valid-connection-string"></a>Seqüência de conexão válida mínima

`InstrumentationKey=00000000-0000-0000-0000-000000000000;`

Neste exemplo, apenas a chave de instrumentação foi definida.

- O esquema de autorização é padrão para "ikey" 
- Chave de instrumentação: 0000000-0000-0000-0000000000000000000000
- Os URIs de serviço regional são baseados nos padrões do [SDK](https://github.com/microsoft/ApplicationInsights-dotnet/blob/e50d569cebf485e72e98f4a08a0bc0e30cdf42bc/BASE/src/Microsoft.ApplicationInsights/Extensibility/Implementation/Endpoints/Constants.cs#L6) e se conectarão ao Azure global público:
   - Ingestão:https://dc.services.visualstudio.com/
   - Métricas ao vivo:https://rt.services.visualstudio.com/
   - Profiler:https://agent.azureserviceprofiler.net/
   - Depurador:https://agent.azureserviceprofiler.net/  



### <a name="connection-string-with-endpoint-suffix"></a>String de conexão com sufixo de ponto final

`InstrumentationKey=00000000-0000-0000-0000-000000000000;EndpointSuffix=ai.contoso.com;`

Neste exemplo, essa seqüência de conexões especifica o sufixo de ponto final e o SDK construirá pontos finais de serviço.

- O esquema de autorização é padrão para "ikey" 
- Chave de instrumentação: 0000000-0000-0000-0000000000000000000000
- Os URIs de serviço regionais são baseados no sufixo de ponto final fornecido: 
   - Ingestão:https://dc.ai.contoso.com
   - Métricas ao vivo:https://live.ai.contoso.com
   - Profiler:https://profiler.ai.contoso.com 
   - Depurador:https://snapshot.ai.contoso.com   



### <a name="connection-string-with-explicit-endpoint-overrides"></a>String de conexão com substituições explícitas de ponto final 

`InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://custom.com:111/;LiveEndpoint=https://custom.com:222/;ProfilerEndpoint=https://custom.com:333/;SnapshotEndpoint=https://custom.com:444/;`

Neste exemplo, essa seqüência de conexões especifica substituições explícitas para cada serviço. O SDK usará os pontos finais exatos fornecidos sem modificação.

- O esquema de autorização é padrão para "ikey" 
- Chave de instrumentação: 0000000-0000-0000-0000000000000000000000
- Os URIs de serviço regionais baseiam-se nos valores de substituição explícitos: 
   - Ingestão: https:\//custom.com:111/
   - Métricas ao vivo: https:\//custom.com:222/
   - Profiler: https:\//custom.com:333/ 
   - Depurador: https:\//custom.com:444/   


## <a name="how-to-set-a-connection-string"></a>Como definir uma seqüência de conexão

As strings de conexão são suportadas nas seguintes versões do SDK:
- .NET e .NET Core v2.12.0
- Java v2.5.1 e Java 3.0
- Javascript v2.3.0
- NodeJS v1.5.0
- Python v1.0.0

Uma seqüência de conexão pode ser definida por código, variável de ambiente ou arquivo de configuração.



### <a name="environment-variable"></a>Variável de ambiente

- Cadeia de conexão: `APPLICATIONINSIGHTS_CONNECTION_STRING`

# <a name="netnetcore"></a>[.NET/.NetCore](#tab/net)

TelemetriaConfiguração.ConexãoString:https://github.com/microsoft/ApplicationInsights-dotnet/blob/add45ceed35a817dc7202ec07d3df1672d1f610d/BASE/src/Microsoft.ApplicationInsights/Extensibility/TelemetryConfiguration.cs#L271-L274

.Net Explicitamente definido:
```csharp
var configuration = new TelemetryConfiguration
{
    ConnectionString = "InstrumentationKey=00000000-0000-0000-0000-000000000000;"
};
```

Arquivo .Net Config:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
    <ConnectionString>InstrumentationKey=00000000-0000-0000-0000-000000000000</ConnectionString>
</ApplicationInsights>
```


NetCore config.json: 

```json
{
  "ApplicationInsights": {
    "ConnectionString" : "InstrumentationKey=00000000-0000-0000-0000-000000000000;"
    }
  }
```


# <a name="java"></a>[Java](#tab/java)


Java (v2.5.x) Explicitamente definido:
```java
TelemetryConfiguration.getActive().setConnectionString("InstrumentationKey=00000000-0000-0000-0000-000000000000");
```

ApplicationInsights.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
    <ConnectionString>InstrumentationKey=00000000-0000-0000-0000-000000000000;</ConnectionString>
</ApplicationInsights>
```

# <a name="javascript"></a>[JavaScript](#tab/js)

Importante: Javascript não suporta o uso de Variáveis de Ambiente.

Usando o trecho:

```javascript
<script type="text/javascript">
var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("addTelemetryInitializer"),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),t.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4},!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t}(
{
  connectionString:"InstrumentationKey=00000000-0000-0000-0000-000000000000;"
}
);window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```


Configuração manual:
```javascript
import { ApplicationInsights } from '@microsoft/applicationinsights-web'

const appInsights = new ApplicationInsights({ config: {
  connectionString: 'InstrumentationKey=00000000-0000-0000-0000-000000000000;'
  /* ...Other Configuration Options... */
} });
appInsights.loadAppInsights();
appInsights.trackPageView();
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("InstrumentationKey=00000000-0000-0000-0000-000000000000;");
appInsights.start();
```

# <a name="python"></a>[Python](#tab/python)

Recomendamos que os usuários definam a variável ambiente.

Para definir explicitamente a seqüência de conexões:

```python
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

tracer = Tracer(exporter=AzureExporter(connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000'), sampler=ProbabilitySampler(1.0))
```


## <a name="next-steps"></a>Próximas etapas

Introdução ao runtime com:

* [Aplicativos hospedados no IIS na VM do Azure e no conjunto de dimensionamento de máquinas virtuais do Azure](../../azure-monitor/app/azure-vm-vmss-apps.md)
* [Servidor IIS](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Aplicativos Web do Azure ](../../azure-monitor/app/azure-web-apps.md)

Introdução ao tempo de desenvolvimento com:

* [ASP.NET](../../azure-monitor/app/asp-net.md)
* [ASP.NET Core](../../azure-monitor/app/asp-net-core.md)
* [Java](../../azure-monitor/app/java-get-started.md)
* [Node.js](../../azure-monitor/app/nodejs.md)
* [Python](../../azure-monitor/app/opencensus-python.md)
