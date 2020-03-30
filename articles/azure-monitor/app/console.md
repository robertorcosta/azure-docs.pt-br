---
title: Azure Application Insights para aplicativos de console | Microsoft Docs
description: Monitorar aplicativos web de disponibilidade, desempenho e uso.
ms.topic: conceptual
ms.date: 12/02/2019
ms.reviewer: lmolkova
ms.openlocfilehash: baaea0f8055eeff0314fcf5fde00729ea8091d12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77655422"
---
# <a name="application-insights-for-net-console-applications"></a>Application Insights para aplicativos do console .NET

O [Application Insights](../../azure-monitor/app/app-insights-overview.md) permite que você monitore seu aplicativo Web quanto à disponibilidade, desempenho e uso.

Você precisa de uma assinatura do [Microsoft Azure](https://azure.com). Entre com uma conta da Microsoft, que você pode ter para o Windows, Xbox Live ou outros serviços de nuvem da Microsoft. Sua equipe pode ter uma assinatura organizacional do Azure: peça ao proprietário que adicione você a ela usando sua conta da Microsoft.

> [!NOTE]
> Há um novo Application Insights SDK chamado [Microsoft.ApplicationInsights.WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) que pode ser usado para habilitar insights de aplicativos para quaisquer aplicativos de console. Recomenda-se usar este pacote e instruções associadas [daqui.](../../azure-monitor/app/worker-service.md) Este pacote [`NetStandard2.0`](https://docs.microsoft.com/dotnet/standard/net-standard)tem como alvo , e, portanto, pode ser usado em .NET Core 2.0 ou superior, e .NET Framework 4.7.2 ou superior.

## <a name="getting-started"></a>Introdução

* No [portal do Azure](https://portal.azure.com), [crie um recurso Application Insights](../../azure-monitor/app/create-new-resource.md). Para o tipo de aplicativo, escolha **Geral**.
* Faça uma cópia da chave de instrumentação. Encontre a chave na queda do **Essentials** do novo recurso que você criou.
* Instale o pacote [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) mais recente.
* Defina a chave de instrumentação em seu código antes de acompanhar qualquer telemetria (ou defina a variável de ambiente APPINSIGHTS_INSTRUMENTATIONKEY). Depois disso, você deve ser capaz de acompanhar a telemetria manualmente e vê-la no portal do Azure

```csharp
// you may use different options to create configuration as shown later in this article
TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();
configuration.InstrumentationKey = " *your key* ";
var telemetryClient = new TelemetryClient(configuration);
telemetryClient.TrackTrace("Hello World!");
```

> [!NOTE]
> A telemetria não é enviada instantaneamente. Os itens de telemetria são em lote e enviados pelo ApplicationInsights SDK. Nos aplicativos console, que sai `Track()` logo após os métodos de `Flush()` chamada, a telemetria pode não ser enviada a menos que seja `Sleep` feita antes que o aplicativo saia, como mostrado em exemplo [completo](#full-example) mais tarde neste artigo.


* Instale a versão mais recente do pacote [Microsoft.ApplicationInsights.DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) - ele acompanha automaticamente HTTP, SQL ou outras chamadas de dependência externas.

Você pode inicializar e configurar o Application Insights pelo código ou usando o arquivo `ApplicationInsights.config`. Verifique se a inicialização ocorre o mais cedo possível. 

> [!NOTE]
> As instruções referentes a **ApplicationInsights.config** são aplicáveis apenas a aplicativos direcionados ao .NET Framework e não são aplicáveis a aplicativos .NET Core.

### <a name="using-config-file"></a>Usando o arquivo de configuração

Por padrão, o SDK do Application Insights procura pelo arquivo `ApplicationInsights.config` no diretório de trabalho quando `TelemetryConfiguration` está sendo criado

```csharp
TelemetryConfiguration config = TelemetryConfiguration.Active; // Reads ApplicationInsights.config file if present
```

Você também pode especificar o caminho para o arquivo de configuração.

```csharp
using System.IO;
TelemetryConfiguration configuration = TelemetryConfiguration.CreateFromConfiguration(File.ReadAllText("C:\\ApplicationInsights.config"));
var telemetryClient = new TelemetryClient(configuration);
```

Para obter mais informações, consulte [referência do arquivo de configuração](configuration-with-applicationinsights-config.md).

Você pode obter um exemplo completo do arquivo de configuração ao instalar a versão mais recente do pacote [Microsoft.ApplicationInsights.WindowsServer](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer). Aqui está a configuração **mínima** para a coleção de dependência que é equivalente ao exemplo de código.

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <InstrumentationKey>Your Key</InstrumentationKey>
  <TelemetryInitializers>
    <Add Type="Microsoft.ApplicationInsights.DependencyCollector.HttpDependenciesParsingTelemetryInitializer, Microsoft.AI.DependencyCollector"/>
  </TelemetryInitializers>
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule, Microsoft.AI.DependencyCollector">
      <ExcludeComponentCorrelationHttpHeadersOnDomains>
        <Add>core.windows.net</Add>
        <Add>core.chinacloudapi.cn</Add>
        <Add>core.cloudapi.de</Add>
        <Add>core.usgovcloudapi.net</Add>
        <Add>localhost</Add>
        <Add>127.0.0.1</Add>
      </ExcludeComponentCorrelationHttpHeadersOnDomains>
      <IncludeDiagnosticSourceActivities>
        <Add>Microsoft.Azure.ServiceBus</Add>
        <Add>Microsoft.Azure.EventHubs</Add>
      </IncludeDiagnosticSourceActivities>
    </Add>
  </TelemetryModules>
  <TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel, Microsoft.AI.ServerTelemetryChannel"/>
</ApplicationInsights>

```

### <a name="configuring-telemetry-collection-from-code"></a>Configurando a coleta de telemetria do código
> [!NOTE]
> O arquivo de configuração de leitura não é suportado no .NET Core. Você pode considerar o uso [do Application Insights SDK para ASP.NET Core](../../azure-monitor/app/asp-net-core.md)

* Durante a inicialização do aplicativo, crie e configure a instância `DependencyTrackingTelemetryModule` - ela deve ser singleton e deve ser preservada durante o tempo de vida do aplicativo.

```csharp
var module = new DependencyTrackingTelemetryModule();

// prevent Correlation Id to be sent to certain endpoints. You may add other domains as needed.
module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
//...

// enable known dependency tracking, note that in future versions, we will extend this list. 
// please check default settings in https://github.com/Microsoft/ApplicationInsights-dotnet-server/blob/develop/Src/DependencyCollector/DependencyCollector/ApplicationInsights.config.install.xdt

module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.ServiceBus");
module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.EventHubs");
//....

// initialize the module
module.Initialize(configuration);
```

* Adicionar inicializadores de telemetria comuns

```csharp
// ensures proper DependencyTelemetry.Type is set for Azure RESTful API calls
configuration.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());
```

Se você criou a `TelemetryConfiguration()` configuração com o construtor simples, você precisa ativar o suporte à correlação adicionalmente. **Não é necessário** se você ler `TelemetryConfiguration.CreateDefault()` a `TelemetryConfiguration.Active`configuração do arquivo, usado ou .

```csharp
configuration.TelemetryInitializers.Add(new OperationCorrelationTelemetryInitializer());
```

* Você também pode querer instalar e inicializar o módulo coletor de contador de desempenho, conforme descrito [aqui](https://apmtips.com/blog/2017/02/13/enable-application-insights-live-metrics-from-code/)


#### <a name="full-example"></a>Exemplo completo

```csharp
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DependencyCollector;
using Microsoft.ApplicationInsights.Extensibility;
using System.Net.Http;
using System.Threading.Tasks;

namespace ConsoleApp
{
    class Program
    {
        static void Main(string[] args)
        {
            TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();

            configuration.InstrumentationKey = "removed";
            configuration.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());

            var telemetryClient = new TelemetryClient(configuration);
            using (InitializeDependencyTracking(configuration))
            {
                // run app...

                telemetryClient.TrackTrace("Hello World!");

                using (var httpClient = new HttpClient())
                {
                    // Http dependency is automatically tracked!
                    httpClient.GetAsync("https://microsoft.com").Wait();
                }

            }

            // before exit, flush the remaining data
            telemetryClient.Flush();

            // flush is not blocking so wait a bit
            Task.Delay(5000).Wait();

        }

        static DependencyTrackingTelemetryModule InitializeDependencyTracking(TelemetryConfiguration configuration)
        {
            var module = new DependencyTrackingTelemetryModule();

            // prevent Correlation Id to be sent to certain endpoints. You may add other domains as needed.
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.chinacloudapi.cn");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.cloudapi.de");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.usgovcloudapi.net");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("localhost");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("127.0.0.1");

            // enable known dependency tracking, note that in future versions, we will extend this list. 
            // please check default settings in https://github.com/microsoft/ApplicationInsights-dotnet-server/blob/develop/WEB/Src/DependencyCollector/DependencyCollector/ApplicationInsights.config.install.xdt

            module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.ServiceBus");
            module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.EventHubs");

            // initialize the module
            module.Initialize(configuration);

            return module;
        }
    }
}

```

## <a name="next-steps"></a>Próximas etapas
* [Monitore dependências](../../azure-monitor/app/asp-net-dependencies.md) para ver se REST, SQL ou outros recursos externos estão causando lentidão.
* [Use a API](../../azure-monitor/app/api-custom-events-metrics.md) para enviar seus próprios eventos e métricas para uma exibição mais detalhada do desempenho e do uso do aplicativo.
