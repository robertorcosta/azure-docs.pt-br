---
title: Diagnosticar com fluxo de métricas ao vivo - Insights do aplicativo Azure
description: Monitore seu aplicativo Web em tempo real usando métrica personalizada e diagnostique problemas com um feed em tempo real de falhas, rastreamentos e eventos.
ms.topic: conceptual
ms.date: 04/22/2019
ms.reviewer: sdash
ms.openlocfilehash: ea0d786d0b8b96941d791bcc8e92fad9a869c5f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670093"
---
# <a name="live-metrics-stream-monitor--diagnose-with-1-second-latency"></a>Live Metrics Stream: monitorar e diagnosticar com latência de um segundo

Teste a pulsação do aplicativo Web ao vivo em produção usando o Live Metrics Stream do [Application Insights](../../azure-monitor/app/app-insights-overview.md). Selecione e filtre os contadores de desempenho e as métricas para observar em tempo real, sem qualquer perturbação para o serviço. Inspecione os rastreamentos de pilha de exceções e solicitações de amostra com falha. Juntamente com [Profiler](../../azure-monitor/app/profiler.md), [Snapshot depurador](../../azure-monitor/app/snapshot-debugger.md). O Live Metrics Stream fornece uma poderosa e não invasiva ferramenta de diagnóstico para o seu site ao vivo.

Com o Live Metrics Stream, você pode:

* Valide uma correção enquanto ela é liberado, observando as contagens de falha e desempenho.
* Observe o efeito de cargas de teste e diagnostique problemas em tempo real.
* Concentre-se em sessões de teste específicas ou filtre os problemas conhecidos, selecionando e filtrando as métricas que você deseja inspecionar.
* Obter rastreamentos de exceção quando eles ocorrerem.
* Experimente os filtros para localizar os KPIs mais relevantes.
* Monitore qualquer contador de desempenho do Windows em tempo real.
* Identifique com facilidade um servidor que está apresentando problemas e filtre todo o KPI/feed em tempo real para apenas aquele servidor.

[![Vídeo de transmissão de métricas ao vivo](./media/live-stream/youtube.png)](https://www.youtube.com/watch?v=zqfHf1Oi5PY)

Atualmente, as Métricas ao Vivo são suportadas para aplicativos ASP.NET, ASP.NET Core, Azure Functions, Java e Node.js.

## <a name="get-started"></a>Introdução

1. Se você ainda precisa [instalar o Application Insights](../../azure-monitor/azure-monitor-app-hub.yml) em seu aplicativo web, faça isso agora.
2. Além dos pacotes padrão do Application Insights, [Microsoft.ApplicationInsights.PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) é necessário para habilitar o Live Metrics Stream.
3. **Atualização para a última versão** do pacote do Application Insights. No Visual Studio, clique com o botão direito do mouse em seu projeto e escolha **Gerenciar pacotes Nuget**. Abra a guia **Atualizações** e selecione todos os pacotes Microsoft.ApplicationInsights.*.

    Reimplante o aplicativo.

3. No [Portal do Azure](https://portal.azure.com), abra o recurso Application Insights para o aplicativo e abra o Live Stream.

4. [Proteja o canal de controle](#secure-the-control-channel) se você puder usar dados confidenciais, como nomes de clientes, em seus filtros.

### <a name="no-data-check-your-server-firewall"></a>Não há dados? Verificar o firewall de servidor

Verifique se as [portas de saída para o Live Metrics Stream](../../azure-monitor/app/ip-addresses.md#outgoing-ports) estão abertas no firewall dos servidores.

## <a name="how-does-live-metrics-stream-differ-from-metrics-explorer-and-analytics"></a>Como o Live Metrics Stream difere do Metrics Explorer e Analytics?

| |Live Stream | Metrics Explorer e Analytics |
|---|---|---|
|Latency|Dados exibidos em um segundo|Agregado ao longo de minutos|
|Nenhuma retenção|Os dados persistem enquanto estão no gráfico e depois são descartados|[Dados retidos por 90 dias](../../azure-monitor/app/data-retention-privacy.md#how-long-is-the-data-kept)|
|Sob demanda|Os dados são transmitidos enquanto você abre o Live Metrics|Os dados são enviados sempre que o SDK está instalado e habilitado|
|Grátis|Não há nenhum custo para dados do Live Stream|Sujeito a [preços](../../azure-monitor/app/pricing.md)
|amostragem|Todas as métricas e os contadores selecionados são transmitidos. Há amostras de falhas e rastreamentos de pilha. TelemetryProcessors não são aplicados.|Os eventos podem ter [amostras](../../azure-monitor/app/api-filtering-sampling.md)|
|Canal de controle|Os sinais de controle de filtro são enviados ao SDK. Recomendamos que você proteja este canal.|Comunicação é uma maneira, para o portal|

## <a name="select-and-filter-your-metrics"></a>Selecionar e filtrar suas métricas

(Disponível com o ASP.NET, ASP.NET Core e Azure Functions (v2).)

Você pode monitorar o KPI personalizado em tempo real aplicando filtros arbitrários a qualquer Application Insights Telemetry no portal. Clique no controle de filtro que é exibido quando você passa o mouse sobre qualquer um dos gráficos. O gráfico a seguir plota um KPI personalizado de contagem de solicitações com filtros nos atributos de URL e a duração. Valide seus filtros com a seção Versão Prévia do Fluxo, que mostra um feed em tempo real da telemetria que corresponde aos critérios que você especificou em qualquer ponto no tempo.

![KPI de solicitação personalizado](./media/live-stream/live-stream-filteredMetric.png)

Você pode monitorar um valor diferente da Contagem. As opções dependem do tipo de fluxo, que poderia ser qualquer Application Insights Telemetry: solicitações, dependências, exceções, rastreamentos, eventos ou métricas. Ela pode ser sua própria [medida personalizada](../../azure-monitor/app/api-custom-events-metrics.md#properties):

![Opções de valor](./media/live-stream/live-stream-valueoptions.png)

Além da Application Insights Telemetry, você também pode monitorar qualquer contador de desempenho do Windows selecionando entre as opções de fluxo e fornecendo o nome do contador de desempenho.

Métricas em tempo real são agregadas em dois pontos: localmente em cada servidor e, em seguida, em todos os servidores. Você pode alterar o padrão de ambos selecionando outras opções nos respectivos menus suspensos.

## <a name="sample-telemetry-custom-live-diagnostic-events"></a>Telemetria de exemplo: eventos de diagnóstico em tempo real personalizados
Por padrão, o feed de eventos em tempo real mostra exemplos de solicitações com falha e chamadas de dependência, exceções, eventos e rastreamentos. Clique no ícone do filtro para ver os critérios aplicados em qualquer ponto no tempo. 

![Feed em tempo real padrão](./media/live-stream/live-stream-eventsdefault.png)

Assim como acontece com as métricas, você pode especificar qualquer critério arbitrário para qualquer um dos tipos de Application Insights Telemetry. Neste exemplo, estamos selecionando falhas, rastreamentos e eventos de solicitação específicos. Também estamos selecionando todas as exceções e falhas de dependência.

![Feed em tempo real personalizado](./media/live-stream/live-stream-events.png)

Observação: no momento, para critérios baseados em mensagens de exceção, use a mensagem de exceção mais externa. No exemplo anterior, para filtrar a exceção benigna com a mensagem de exceção interna (segue o delimitador "< –") "O cliente se desconectou.", use um critério de que a mensagem não contém "Erro ao ler o conteúdo da solicitação".

Consulte os detalhes de um item no feed em tempo real clicando nele. Você pode pausar o feed clicando em **Pausar** ou simplesmente rolando para baixo ou clicando em um item. O feed em tempo real será retomado quando você rolar de volta para o início ou clicando no contador de itens coletados enquanto ele estava em pausa.

![Amostra de falhas em tempo real](./media/live-stream/live-metrics-eventdetail.png)

## <a name="filter-by-server-instance"></a>Filtrar por instância do servidor

Se você quiser monitorar uma instância de função de servidor específico, filtre por servidor.

![Amostra de falhas em tempo real](./media/live-stream/live-stream-filter.png)

## <a name="secure-the-control-channel"></a>Proteger o canal de controle
Os critérios de filtro personalizados especificados são enviados para o componente de Métricas em tempo real no SDK do Application Insights. Os filtros podem conter informações confidenciais, como customerIDs. Você pode proteger o canal com uma chave de API secreta além da chave de instrumentação.
### <a name="create-an-api-key"></a>Criar uma chave de API

![Criar chave de API](./media/live-stream/live-metrics-apikeycreate.png)

### <a name="add-api-key-to-configuration"></a>Adicionar chave de API à configuração

### <a name="classic-aspnet"></a>ASP.NET clássico

No arquivo applicationinsights.config, adicione AuthenticationApiKey a QuickPulseTelemetryModule:
``` XML

<Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <AuthenticationApiKey>YOUR-API-KEY-HERE</AuthenticationApiKey>
</Add>

```
Ou, no código, defina-a no QuickPulseTelemetryModule:

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
using Microsoft.ApplicationInsights.Extensibility;

             TelemetryConfiguration configuration = new TelemetryConfiguration();
            configuration.InstrumentationKey = "YOUR-IKEY-HERE";

            QuickPulseTelemetryProcessor processor = null;

            configuration.TelemetryProcessorChainBuilder
                .Use((next) =>
                {
                    processor = new QuickPulseTelemetryProcessor(next);
                    return processor;
                })
                        .Build();

            var QuickPulse = new QuickPulseTelemetryModule()
            {

                AuthenticationApiKey = "YOUR-API-KEY"
            };
            QuickPulse.Initialize(configuration);
            QuickPulse.RegisterTelemetryProcessor(processor);
            foreach (var telemetryProcessor in configuration.TelemetryProcessors)
                {
                if (telemetryProcessor is ITelemetryModule telemetryModule)
                    {
                    telemetryModule.Initialize(configuration);
                    }
                }

```

### <a name="azure-function-apps"></a>Aplicativos de Funções do Azure

Para aplicativos de função azure (v2), a fixação do canal com uma chave aPI pode ser realizada com uma variável de ambiente.

Crie uma chave de API de dentro de seu recurso do Application Insights e vá para **Configurações do aplicativo** de seu Aplicativo de funções. Selecione **adicionar nova configuração** e insira um nome de `APPINSIGHTS_QUICKPULSEAUTHAPIKEY` e um valor que corresponda à sua chave de API.

### <a name="aspnet-core-requires-application-insights-aspnet-core-sdk-230-or-greater"></a>ASP.NET Core (requer insights de aplicativos ASP.NET O SDK Principal 2.3.0 ou superior)

Modifique o arquivo startup.cs da seguinte forma:

Primeiro adicione

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
```

No método ConfigureServices, adicione:

```csharp
services.ConfigureTelemetryModule<QuickPulseTelemetryModule> ((module, o) => module.AuthenticationApiKey = "YOUR-API-KEY-HERE");
```

No entanto, caso reconheça e confie em todos os servidores conectados, você pode testar os filtros personalizados sem o canal autenticado. Essa opção está disponível por seis meses. Essa substituição é necessária uma vez a cada nova sessão ou quando um novo servidor ficar online.

![Opções de autenticação das métricas em tempo real](./media/live-stream/live-stream-auth.png)

>[!NOTE]
>É altamente recomendável que você configure o canal autenticado antes de inserir informações potencialmente confidenciais, como CustomerIDs, nos critérios de filtro.
>

## <a name="supported-features-table"></a>Tabela de recursos suportados

| Idioma                         | Métricas Básicas       | Métricas de desempenho | Filtragem personalizada    | Telemetria de amostra    | CPU dividida por processo |
|----------------------------------|:--------------------|:--------------------|:--------------------|:--------------------|:---------------------|
| .NET                             | Suportado (V2.7.2+) | Suportado (V2.7.2+) | Suportado (V2.7.2+) | Suportado (V2.7.2+) | Suportado (V2.7.2+)  |
| .NET Core (target=.NET Framework)| Suportado (V2.4.1+) | Suportado (V2.4.1+) | Suportado (V2.4.1+) | Suportado (V2.4.1+) | Suportado (V2.4.1+)  |
| Núcleo .NET (target=.NET Core)     | Suportado (V2.4.1+) | Com suporte*          | Suportado (V2.4.1+) | Suportado (V2.4.1+) | **Não suportado**    |
| Azure Functions v2               | Com suporte           | Com suporte           | Com suporte           | Com suporte           | **Não suportado**    |
| Java                             | Suportado (V2.0.0+) | Suportado (V2.0.0+) | **Não suportado**   | **Não suportado**   | **Não suportado**    |
| Node.js                          | Suportado (V1.3.0+) | Suportado (V1.3.0+) | **Não suportado**   | Suportado (V1.3.0+) | **Não suportado**    |

As métricas básicas incluem solicitação, dependência e taxa de exceção. As métricas de desempenho (contadores de desempenho) incluem memória e CPU. A telemetria de amostra mostra um fluxo de informações detalhadas para solicitações e dependências falhadas, exceções, eventos e vestígios.

 \*O suporte ao PerfCounters varia ligeiramente entre as versões do .NET Core que não têm como alvo o Quadro .NET:

- As métricas do PerfCounters são suportadas ao serem executados no Azure App Service for Windows. (AspNetCore SDK Versão 2.4.1 ou superior)
- Os PerfCounters são suportados quando o aplicativo está sendo executado em qualquer máquina windows (VM ou Cloud Service ou On-prem etc.) (AspNetCore SDK Versão 2.7.1 ou superior), mas para aplicativos direcionados ao .NET Core 2.0 ou superior.
- Os PerfCounters são suportados quando o aplicativo está em execução em QUALQUER LUGAR (Linux, Windows, serviço de aplicativo para Linux, contêineres, etc.) na versão beta mais recente (ou seja, AspNetCore SDK Versão 2.8.0-beta1 ou superior), mas para aplicativos direcionados ao .NET Core 2.0 ou superior.

Por padrão, o Live Metrics é desativado no Node.js SDK. Para habilitar métricas `setSendLiveMetrics(true)` ao vivo, adicione aos seus [métodos de configuração](https://github.com/Microsoft/ApplicationInsights-node.js#configuration) à medida que você inicializa o SDK.

## <a name="troubleshooting"></a>Solução de problemas

Não há dados? Se o aplicativo estiver em uma rede protegida: o Live Metrics Stream usa diferentes endereços IP do que outros dispositivos de telemetria do Application Insights. Certifique-se de que [esses endereços IP](../../azure-monitor/app/ip-addresses.md) estejam abertos em seu firewall.

## <a name="next-steps"></a>Próximas etapas
* [Monitorando o uso com o Application Insights](../../azure-monitor/app/usage-overview.md)
* [Usando a Pesquisa de diagnóstico](../../azure-monitor/app/diagnostic-search.md)
* [Profiler](../../azure-monitor/app/profiler.md)
* [Depurador de instantâneos](../../azure-monitor/app/snapshot-debugger.md)
