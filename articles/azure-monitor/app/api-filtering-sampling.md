---
title: Filtrando e pré-processando no SDK do Azure Application Insights | Microsoft Docs
description: Escreva Processadores de Telemetria e Inicializadores de Telemetria para o SDK filtrar ou adicionar propriedades aos dados antes da telemetria ser enviada ao portal do Application Insights.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 38a9e454-43d5-4dba-a0f0-bd7cd75fb97b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 11/23/2016
ms.author: mbullwin
ms.openlocfilehash: cae035927217a7e2677cf6ebfcce1b53782e4c01
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72248740"
---
# <a name="filtering-and-preprocessing-telemetry-in-the-application-insights-sdk"></a>Filtrando e pré-processando a telemetria no SDK do Application Insights

Você pode gravar e configurar plug-ins para o SDK do Application Insights para personalizar como a telemetria pode ser enriqueceda e processada antes de ser enviada ao serviço de Application Insights.

* [Amostragem](sampling.md) reduz o volume de telemetria sem afetar as estatísticas. Ela mantém juntos os pontos de dados relacionadas para que você possa navegar entre eles para diagnosticar um problema. No portal, as contagens totais são multiplicadas para compensar a amostragem.
* A filtragem com processadores de telemetria permite filtrar a telemetria no SDK antes que ele seja enviado ao servidor. Por exemplo, você pode reduzir o volume de telemetria excluindo as solicitações de robôs. A filtragem é uma abordagem mais básica para reduzir o tráfego do que a amostragem. Ela permite um maior controle sobre o que é transmitido, mas você precisa estar ciente de que ela afetará as estatísticas - por exemplo, se você filtrar todas as solicitações bem-sucedidas.
* [Inicializadores de telemetria adicionam ou modificam Propriedades](#add-properties) para qualquer telemetria enviada de seu aplicativo, incluindo a telemetria dos módulos padrão. Por exemplo, você pode adicionar valores calculados ou números de versão pelos qual os dados serão filtrados no portal.
* [A API do SDK](../../azure-monitor/app/api-custom-events-metrics.md) é usada para enviar eventos personalizados e métricas.

Antes de começar:

* Instale o SDK apropriado para seu aplicativo: [ASP.net](asp-net.md), [ASP.NET Core](asp-net-core.md), [não http/Worker para .net/.NET Core](worker-service.md)ou [Java](../../azure-monitor/app/java-get-started.md).

<a name="filtering"></a>

## <a name="filtering-itelemetryprocessor"></a>Filtragem: ITelemetryProcessor

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
> A adição de processador usando o `ApplicationInsights.config` ou o uso de `TelemetryConfiguration.Active` não é válida para aplicativos ASP.NET Core ou se você estiver usando o SDK Microsoft. ApplicationInsights. WorkerService.

Para aplicativos escritos usando [ASP.NET Core](asp-net-core.md#adding-telemetry-processors) ou [WorkerService](worker-service.md#adding-telemetry-processors), a adição de um novo `TelemetryProcessor` é feita usando o método de extensão `AddApplicationInsightsTelemetryProcessor` no `IServiceCollection`, conforme mostrado abaixo. Esse método é chamado no método `ConfigureServices` da sua classe `Startup.cs`.

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

## <a name="add-properties-itelemetryinitializer"></a>Adicionar propriedades: ITelemetryInitializer

Use inicializadores de telemetria para enriquecer a telemetria com informações adicionais e/ou para substituir as propriedades de telemetria definidas pelos módulos de telemetria padrão.

Por exemplo, a Application Insights para o pacote da Web coleta a telemetria sobre solicitações HTTP. Por padrão, ele sinaliza como qualquer solicitação com um código de resposta de falha > = 400. Mas se desejar tratar 400 como êxito, você pode fornecer um inicializador de telemetria que define a propriedade Sucess.

Se você fornecer um inicializador de telemetria, ele é chamado sempre que qualquer um dos métodos Track*() for chamado. Isso inclui os métodos `Track()` chamados pelos módulos de telemetria padrão. Por convenção, esses módulos não definem qualquer propriedade que já foi definida por um inicializador. Inicializadores de telemetria são chamados antes de chamar processadores de telemetria. Portanto, todos os aprimoramentos feitos por inicializadores são visíveis para os processadores.

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

aplicativos **ASP.NET: Carregar seu inicializador @ no__t-0

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

0ASP.NET-aplicativos de serviço principal/de trabalho do @no__t: Carregar seu inicializador @ no__t-0

> [!NOTE]
> A adição de inicializador usando `ApplicationInsights.config` ou o uso de `TelemetryConfiguration.Active` não é válida para aplicativos ASP.NET Core ou se você estiver usando o SDK Microsoft. ApplicationInsights. WorkerService.

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
