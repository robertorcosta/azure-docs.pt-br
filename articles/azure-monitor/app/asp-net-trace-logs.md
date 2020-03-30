---
title: Explorar os logs de rastreamento do .NET no Application Insights
description: Registros de pesquisa gerados por Trace, NLog ou Log4Net.
ms.topic: conceptual
ms.date: 05/08/2019
ms.openlocfilehash: 273d5a2f4e1155541e159332312bdaa68aa175d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276264"
---
# <a name="explore-netnet-core-and-python-trace-logs-in-application-insights"></a>Explorar os logs de rastreamento .NET/.NET Core e Python no Application Insights

Envie registros de rastreamento de diagnóstico para o aplicativo ASP.NET/ASP.NET Core do ILogger, NLog, log4Net ou System.Diagnostics.Trace para [o Azure Application Insights][start]. Para aplicativos Python, envie registros de rastreamento de diagnóstico usando o AzureLogHandler no OpenCensus Python para O Monitor Do Azure. Você pode então explorá-los e revistá-los. Esses logs são mesclados com os outros arquivos de log do seu aplicativo, para que você possa identificar traços associados a cada solicitação do usuário e correlaciona-os com outros eventos e relatórios de exceção.

> [!NOTE]
> Você precisa do módulo de captura de log? É um adaptador útil para madeireiros de terceiros. Mas se você ainda não estiver usando o NLog, log4Net ou System.Diagnostics.Trace, considere apenas ligar diretamente para [**o TrackTrace do Application Insights.**](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace)
>
>
## <a name="install-logging-on-your-app"></a>Instalar o log no seu aplicativo
Instale sua estrutura de registro escolhida em seu projeto, o que deve resultar em uma entrada em app.config ou web.config.

```XML
 <configuration>
  <system.diagnostics>
    <trace>
      <listeners>
        <add name="myAppInsightsListener" type="Microsoft.ApplicationInsights.TraceListener.ApplicationInsightsTraceListener, Microsoft.ApplicationInsights.TraceListener" />
      </listeners>
    </trace>
  </system.diagnostics>
</configuration>
```

## <a name="configure-application-insights-to-collect-logs"></a>Configurar o Application Insights para coletar logs
[Adicione o Application Insights ao seu projeto](../../azure-monitor/app/asp-net.md) se ainda não tiver feito isso. Você verá uma opção para incluir o coletor de logs.

Ou clique com o botão direito do mouse no projeto no Solution Explorer para **configurar insights de aplicativos**. Selecione a opção **Configurar coleta de vestígios.**

> [!NOTE]
> Não consegue ver o menu do Application Insights nem a opção de coletor de logs? Experimente [Solucionar problemas](#troubleshooting).

## <a name="manual-installation"></a>Instalação manual
Use este método se o tipo de projeto não tiver suporte no instalador do Application Insights (por exemplo, um projeto de Área de Trabalho do Windows).

1. Se você planeja usar o log4Net ou NLog, instale-o em seu projeto.
2. No Solution Explorer, clique com o botão direito do mouse no projeto e **selecione Gerenciar pacotes NuGet**.
3. Procure por "Insights de aplicativos".
4. Selecione um dos seguintes pacotes:

   - Para ILogger: [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.Extensions.Logging.ApplicationInsights.svg)](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
   - Para o NLog: [Microsoft.ApplicationInsights.NLogTarget](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.NLogTarget.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
   - Para Log4Net: [Microsoft.ApplicationInsights.Log4NetAppender](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.Log4NetAppender.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
   - Para System.Diagnostics: [Microsoft.ApplicationInsights.TraceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.TraceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
   - [Microsoft.ApplicationInsights.DiagnosticSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.DiagnosticSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
   - [Microsoft.ApplicationInsights.EtwCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EtwCollector.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
   - [Microsoft.ApplicationInsights.EventSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EventSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)

O pacote NuGet instala os conjuntos necessários e modifica web.config ou app.config, se isso for aplicável.

## <a name="ilogger"></a>ILogger

Para obter exemplos de uso da implementação do ILogger do Application Insights com aplicativos de console e ASP.NET Core, consulte [ApplicationInsightsLoggerProvider para logs do .NET Core ILogger](ilogger.md).

## <a name="insert-diagnostic-log-calls"></a>Inserir chamadas de log de diagnóstico
Se você usa System.Diagnostics.Trace, uma chamada típica é semelhante a:

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

Se preferir log4net ou NLog, use:

    logger.Warn("Slow response - database01");

## <a name="use-eventsource-events"></a>Use eventos EventSource
É possível configurar eventos [System.Diagnostics.Tracing.EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) para que eles sejam enviados para o Application Insights como rastreamentos. Primeiro, instale o pacote NuGet `Microsoft.ApplicationInsights.EventSourceListener`. Depois, edite a seção `TelemetryModules` do arquivo [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md).

```xml
    <Add Type="Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule, Microsoft.ApplicationInsights.EventSourceListener">
      <Sources>
        <Add Name="MyCompany" Level="Verbose" />
      </Sources>
    </Add>
```

Para cada fonte, você pode definir os seguintes parâmetros:
 * **O nome** especifica o nome do EventSource a ser coletado.
 * **O nível** especifica o nível de registro a ser coletado: *Crítico,* *Erro,* *Informativo,* *LogAlways,* *Verbose*ou *Aviso*.
 * **Palavras-chave** (opcional) especificam o valor inteiro das combinações de palavras-chave a serem usadas.

## <a name="use-diagnosticsource-events"></a>Use eventos DiagnosticSource
É possível configurar eventos [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) para que eles sejam enviados para o Application Insights como rastreamentos. Primeiro, instale o [`Microsoft.ApplicationInsights.DiagnosticSourceListener`](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener) pacote NuGet. Em seguida, edite a seção "TelemetriaModules" do arquivo [ApplicationInsights.config.](../../azure-monitor/app/configuration-with-applicationinsights-config.md)

```xml
    <Add Type="Microsoft.ApplicationInsights.DiagnosticSourceListener.DiagnosticSourceTelemetryModule, Microsoft.ApplicationInsights.DiagnosticSourceListener">
      <Sources>
        <Add Name="MyDiagnosticSourceName" />
      </Sources>
    </Add>
```

Para cada DiagnosticSource que você deseja rastrear, adicione uma entrada com o atributo **Nome** definido no nome do seu DiagnosticSource.

## <a name="use-etw-events"></a>Use eventos ETW
Você pode configurar eventos de Rastreamento de Eventos para Windows (ETW) a serem enviados ao Application Insights como rastreamentos. Primeiro, instale o pacote NuGet `Microsoft.ApplicationInsights.EtwCollector`. Em seguida, edite a seção "TelemetriaModules" do arquivo [ApplicationInsights.config.](../../azure-monitor/app/configuration-with-applicationinsights-config.md)

> [!NOTE] 
> Os eventos ETW só podem ser coletados se o processo que hospeda o SDK for executado uma identidade que é membro dos Usuários ou Administradores do Registro de Desempenho.

```xml
    <Add Type="Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule, Microsoft.ApplicationInsights.EtwCollector">
      <Sources>
        <Add ProviderName="MyCompanyEventSourceName" Level="Verbose" />
      </Sources>
    </Add>
```

Para cada fonte, você pode definir os seguintes parâmetros:
 * **ProviderName** é o nome do provedor ETW a ser coletado.
 * **O ProviderGuid** especifica o GUID do provedor ETW para coletar. Pode ser usado `ProviderName`em vez de .
 * **O nível** define o nível de registro para coletar. Pode ser *Crítico,* *Erro,* *Informativo,* *LogAlways,* *Verbose*ou *Aviso.*
 * **Palavras-chave** (opcional) definem o valor inteiro das combinações de palavras-chave para usar.

## <a name="use-the-trace-api-directly"></a>Use a API Trace diretamente
Você pode chamar a API de rastreamento do Application Insights diretamente. Os adaptadores de log usam essa API.

Por exemplo: 

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");

Uma vantagem de TrackTrace é que você pode colocar dados relativamente compridos na mensagem. Por exemplo, você pode codificar dados POST.

Você também pode adicionar um nível de gravidade à sua mensagem. E, como outras telemetrias, você pode adicionar valores de propriedade para ajudar a filtrar ou procurar diferentes conjuntos de traços. Por exemplo: 

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow database response",
                   SeverityLevel.Warning,
                   new Dictionary<string,string> { {"database", db.ID} });

Isso permitiria que você filtrasse facilmente no [Pesquisar][diagnostic] todas as mensagens de um determinado nível de gravidade que se relacionam com um determinado banco de dados.

## <a name="azureloghandler-for-opencensus-python"></a>AzureLogHandler para OpenCensus Python
O Azure Monitor Log Handler permite exportar logs Python para o Monitor Azure.

Ateste sua aplicação com o [OpenCensus Python SDK](../../azure-monitor/app/opencensus-python.md) para Monitor Azure.

Este exemplo mostra como enviar um registro de nível de aviso para o Azure Monitor.

```python
import logging

from opencensus.ext.azure.log_exporter import AzureLogHandler

logger = logging.getLogger(__name__)
logger.addHandler(AzureLogHandler(connection_string='InstrumentationKey=<your-instrumentation_key-here>'))
logger.warning('Hello, World!')
```

## <a name="explore-your-logs"></a>Explorar seus logs
Execute seu aplicativo no modo de depuração ou implante-o ao vivo.

No painel de visão geral do seu aplicativo no [portal Application Insights,][portal]selecione [Pesquisa][diagnostic].

Por exemplo, você pode:

* Filtre em vestígios de registro ou em itens com propriedades específicas.
* Inspecionar um item específico em detalhes.
* Encontre outros dados de registro do sistema relacionados com a mesma solicitação de usuário (tem o mesmo OperationId).
* Salve a configuração de uma página como favorita.

> [!NOTE]
>Se o seu aplicativo enviar muitos dados e você estiver usando o Application Insights SDK para ASP.NET versão 2.0.0-beta3 ou posterior, o recurso *de amostragem adaptativa* pode operar e enviar apenas uma parte da sua telemetria. [Saiba mais sobre amostragem.](../../azure-monitor/app/sampling.md)
>

## <a name="troubleshooting"></a>Solução de problemas
### <a name="how-do-i-do-this-for-java"></a>Como faço isso no Java?
Use os [adaptadores de log Java](../../azure-monitor/app/java-trace-logs.md).

### <a name="theres-no-application-insights-option-on-the-project-context-menu"></a>Não há nenhuma opção do Application Insights no menu de contexto do projeto
* Certifique-se de que as Ferramentas de Análise do Desenvolvedor estão instaladas na máquina de desenvolvimento. No Visual Studio **Tools** > **Extensions and Updates**, procure **ferramentas de análise de desenvolvedores.** Se não estiver na guia **Instalado,** abra a guia **Online** e instale-a.
* Este pode ser um tipo de projeto que o Devloper Analytics Tools não suporta. Use a [instalação manual](#manual-installation).

### <a name="theres-no-log-adapter-option-in-the-configuration-tool"></a>Não há opção de adaptador de log na ferramenta de configuração
* Instale a estrutura de registro primeiro.
* Se você estiver usando System.Diagnostics.Trace, certifique-se de tê-lo [configurado em *web.config*](https://msdn.microsoft.com/library/system.diagnostics.eventlogtracelistener.aspx).
* Certifique-se de que você tem a versão mais recente do Application Insights. No Visual Studio, vá para **Tools** > **Extensions and Updates**, e abra a guia **Atualizações.** Se **as ferramentas de análise do desenvolvedor** estiverm lá, selecione-a para atualizá-la.

### <a name="i-get-the-instrumentation-key-cannot-be-empty-error-message"></a><a name="emptykey"></a>Eu recebo a mensagem de erro "A tecla de instrumentação não pode estar vazia"
Você provavelmente instalou o pacote Nuget do adaptador de registro sem instalar o Application Insights. No Solution Explorer, clique com o botão direito do mouse *ApplicationInsights.config*e selecione **Update Application Insights**. Você será solicitado a fazer login no Azure e criar um recurso do Application Insights ou reutilizar um já existente. Isso deve resolver o problema.

### <a name="i-can-see-traces-but-not-other-events-in-diagnostic-search"></a>Eu posso ver traços, mas não outros eventos na busca de diagnóstico
Pode levar um tempo para todos os eventos e pedidos para passar pelo oleoduto.

### <a name="how-much-data-is-retained"></a><a name="limits"></a>Que quantidade de dados é mantida?
Vários fatores afetam a quantidade de dados retidos. Para obter mais informações, consulte a seção [limites](../../azure-monitor/app/api-custom-events-metrics.md#limits) da página de métricas de eventos do cliente.

### <a name="i-dont-see-some-log-entries-that-i-expected"></a>Eu não vejo algumas entradas de registro que eu esperava
Se o seu aplicativo enviar quantidades volumosas de dados e você estiver usando o Application Insights SDK para ASP.NET versão 2.0.0-beta3 ou posterior, o recurso de amostragem adaptativa pode operar e enviar apenas uma parte de sua telemetria. [Saiba mais sobre amostragem.](../../azure-monitor/app/sampling.md)

## <a name="next-steps"></a><a name="add"></a>Próximas etapas

* [Diagnosticar falhas e exceções no ASP.NET][exceptions]
* [Saiba mais sobre o Search][diagnostic]
* [Configurar testes de disponibilidade e capacidade de resposta][availability]
* [Solução de problemas][qna]

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[exceptions]: asp-net-exceptions.md
[portal]: https://portal.azure.com/
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[start]: ../../azure-monitor/app/app-insights-overview.md
