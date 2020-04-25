---
title: Telemetria para análise de tráfego de pesquisa
titleSuffix: Azure Cognitive Search
description: Habilite a análise de tráfego de pesquisa para Pesquisa Cognitiva do Azure, colete telemetria e eventos iniciados pelo usuário usando Application Insights e, em seguida, analise as descobertas em um relatório Power BI.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 7c843b45b5a398aaaa1aab66f80961560477cf18
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82128101"
---
# <a name="collect-telemetry-data-for-search-traffic-analytics"></a>Coletar dados de telemetria para análise de tráfego de pesquisa

A análise de tráfego de pesquisa é um padrão para coletar telemetria sobre interações do usuário com seu aplicativo Pesquisa Cognitiva do Azure, como eventos de clique iniciado pelo usuário e entradas de teclado. Usando essas informações, você pode determinar a eficácia de sua solução de pesquisa, incluindo termos de pesquisa populares, taxa de clickthrough e quais entradas de consulta resultam em zero resultados.

Esse padrão usa uma dependência de [Application insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) (um recurso de [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/)) para coletar dados do usuário. Ele requer que você adicione instrumentação ao seu código de cliente, conforme descrito neste artigo. Por fim, você precisará de um mecanismo de relatório para analisar os dados. É recomendável Power BI, mas você pode usar o painel do aplicativo ou qualquer ferramenta que se conecte ao Application Insights.

> [!NOTE]
> O padrão descrito neste artigo é para cenários avançados e dados cliques gerados pelo código que você adiciona ao seu cliente. Por outro lado, os logs de serviço são fáceis de configurar, fornecem uma variedade de métricas e podem ser feitos no portal sem nenhum código necessário. A habilitação do registro em log é recomendada para todos os cenários. Para obter mais informações, consulte [coletar e analisar dados de log](search-monitor-logs.md).

## <a name="identify-relevant-search-data"></a>Identificar dados de pesquisa relevantes

Para ter métricas úteis para a análise de tráfego de pesquisa, é necessário registrar alguns sinais dos usuários de seu aplicativo de pesquisa. Esses sinais significam conteúdo em que os usuários estão interessados e que eles consideram relevantes. Para análise de tráfego de pesquisa, eles incluem:

+ Eventos de pesquisa gerados pelo usuário: somente consultas de pesquisa iniciadas por um usuário são interessantes. Solicitações de pesquisa usadas para preencher facetas, conteúdo adicional ou qualquer informação interna, não são importantes e distorcem e influenciam seus resultados.

+ Eventos de clique gerados pelo usuário: em uma página de resultados da pesquisa, um evento de clique geralmente significa que um documento é um resultado relevante para uma consulta de pesquisa específica.

Ao vincular a pesquisa e clicar em eventos com uma ID de correlação, você obterá uma compreensão mais profunda de como a funcionalidade de pesquisa do aplicativo está sendo executada.

## <a name="add-search-traffic-analytics"></a>Adicionar análise de tráfego de pesquisa

Na página do [portal](https://portal.azure.com) do serviço de pesquisa cognitiva do Azure, a página Pesquisa análise de tráfego contém uma folha de consulta para seguir esse padrão de telemetria. Nessa página, você pode selecionar ou criar um recurso de Application Insights, obter a chave de instrumentação, copiar trechos de código que podem ser adaptados para sua solução e baixar um relatório de Power BI criado sobre o esquema refletido no padrão.

![Pesquisar Análise de Tráfego página no portal](media/search-traffic-analytics/azuresearch-trafficanalytics.png "Pesquisar Análise de Tráfego página no portal")

## <a name="1---set-up-application-insights"></a>1-configurar Application Insights

Selecione um recurso de Application Insights existente ou [crie um](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) se você ainda não tiver um. Se você usar a página Pesquisar Análise de Tráfego, poderá copiar a chave de instrumentação que seu aplicativo precisa para se conectar ao Application Insights.

Depois de ter um recurso de Application Insights, você pode seguir [as instruções para linguagens e plataformas com suporte](https://docs.microsoft.com/azure/azure-monitor/app/platforms) para registrar seu aplicativo. O registro está simplesmente adicionando a chave de instrumentação de Application Insights ao seu código, que configura a associação. Você pode encontrar a chave no portal ou na página Pesquisar Análise de Tráfego ao selecionar um recurso existente.

Um atalho que funciona para alguns tipos de projeto do Visual Studio é refletido nas etapas a seguir. Ele cria um recurso e registra seu aplicativo com apenas alguns cliques.

1. Para o desenvolvimento do Visual Studio e do ASP.net, abra sua solução e selecione **projeto** > **Adicionar Application insights Telemetry**.

1. Clique em **introdução**.

1. Registre seu aplicativo fornecendo uma conta Microsoft, assinatura do Azure e um recurso de Application Insights (um novo recurso é o padrão). Clique em **Registrar**.

Neste ponto, seu aplicativo está configurado para monitoramento de aplicativos, o que significa que todas as cargas de página são controladas com métricas padrão. Para obter mais informações sobre as etapas anteriores, consulte [Enable Application insights telemetria do lado do servidor](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core#enable-application-insights-server-side-telemetry-visual-studio).

## <a name="2---add-instrumentation"></a>2 - Adicionar instrumentação

Esta etapa é onde você instrumenta seu próprio aplicativo de pesquisa, usando o recurso Application Insights criado na etapa acima. Há quatro etapas para esse processo, começando com a criação de um cliente de telemetria.

### <a name="step-1-create-a-telemetry-client"></a>Etapa 1: criar um cliente de telemetria

Crie um objeto que envia eventos para Application Insights. Você pode adicionar instrumentação ao código do aplicativo do lado do servidor ou código do lado do cliente em execução em um navegador, expresso aqui como variantes do C# e do JavaScript (para outras linguagens, consulte a lista completa de [plataformas e estruturas com suporte](https://docs.microsoft.com/azure/application-insights/app-insights-platforms). Escolha a abordagem que fornece a profundidade desejada de informações.

A telemetria do lado do servidor captura métricas na camada de aplicativo, por exemplo, em aplicativos executados como um serviço Web na nuvem ou como um aplicativo local em uma rede corporativa. A telemetria do lado do servidor captura eventos de pesquisa e de clique, a posição de um documento nos resultados e informações de consulta, mas sua coleta de dados terá o escopo de qualquer informação disponível nessa camada.

No cliente, você pode ter código adicional que manipule entradas de consulta, adiciona navegação ou inclui contexto (por exemplo, consultas iniciadas de um home page em vez de uma página de produto). Se isso descrever sua solução, você poderá optar pela Instrumentação do lado do cliente para que sua telemetria reflita os detalhes adicionais. A maneira como esse detalhe adicional é coletado vai além do escopo desse padrão, mas você pode examinar [Application insights para páginas da Web](https://docs.microsoft.com/azure/azure-monitor/app/javascript#explore-browserclient-side-data) para obter mais direção. 

**Como usar C#**

Para o C#, o **InstrumentationKey** é encontrado na configuração do aplicativo, como AppSettings. JSON se o seu projeto for ASP.net. Consulte as instruções de registro se não tiver certeza da localização da chave.

```csharp
private static TelemetryClient _telemetryClient;

// Add a constructor that accepts a telemetry client:
public HomeController(TelemetryClient telemetry)
    {
        _telemetryClient = telemetry;
    }
```

**Usar JavaScript**

```javascript
<script type="text/javascript">var appInsights=window.appInsights||function(config){function r(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},u=document,e=window,o="script",s=u.createElement(o),i,f;s.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js";u.getElementsByTagName(o)[0].parentNode.appendChild(s);try{t.cookie=u.cookie}catch(h){}for(t.queue=[],i=["Event","Exception","Metric","PageView","Trace","Dependency"];i.length;)r("track"+i.pop());return r("setAuthenticatedUserContext"),r("clearAuthenticatedUserContext"),config.disableExceptionTracking||(i="onerror",r("_"+i),f=e[i],e[i]=function(config,r,u,e,o){var s=f&&f(config,r,u,e,o);return s!==!0&&t["_"+i](config,r,u,e,o),s}),t}
({
instrumentationKey: "<YOUR INSTRUMENTATION KEY>"
});
window.appInsights=appInsights;
</script>
```

### <a name="step-2-request-a-search-id-for-correlation"></a>Etapa 2: solicitar uma ID de pesquisa para correlação

Para correlacionar as solicitações de pesquisa com cliques, é necessário ter uma ID de correlação que relaciona esses dois eventos distintos. O Azure Pesquisa Cognitiva fornece uma ID de pesquisa quando você a solicita com um cabeçalho HTTP.

Ter a ID de pesquisa permite a correlação das métricas emitidas pelo Azure Pesquisa Cognitiva para a solicitação em si, com as métricas personalizadas nas quais você está fazendo logon Application Insights.  

**Como usar C#**

```csharp
// This sample uses the .NET SDK https://www.nuget.org/packages/Microsoft.Azure.Search

var client = new SearchIndexClient(<SearchServiceName>, <IndexName>, new SearchCredentials(<QueryKey>)

// Use HTTP headers so that you can get the search ID from the response
var headers = new Dictionary<string, List<string>>() { { "x-ms-azs-return-searchid", new List<string>() { "true" } } };
var response = await client.Documents.SearchWithHttpMessagesAsync(searchText: searchText, searchParameters: parameters, customHeaders: headers);
string searchId = string.Empty;
if (response.Response.Headers.TryGetValues("x-ms-azs-searchid", out IEnumerable<string> headerValues)){
    searchId = headerValues.FirstOrDefault();
}
```

**Usar JavaScript (chamando APIs REST)**

```javascript
request.setRequestHeader("x-ms-azs-return-searchid", "true");
request.setRequestHeader("Access-Control-Expose-Headers", "x-ms-azs-searchid");
var searchId = request.getResponseHeader('x-ms-azs-searchid');
```

### <a name="step-3-log-search-events"></a>Etapa 3: eventos de pesquisa de log

Toda vez que uma solicitação de pesquisa é emitida por um usuário, você deve registrar isso como um evento de pesquisa com o esquema a seguir em um Application Insights evento personalizado. Lembre-se de registrar somente as consultas de pesquisa geradas pelo usuário.

+ **SearchServiceName**: (cadeia de caracteres) nome do serviço de pesquisa
+ **SearchId**: (GUID) identificador exclusivo da consulta de pesquisa (vem na resposta de pesquisa)
+ **IndexName**: (cadeia de caracteres) índice do serviço de pesquisa a ser consultado
+ **QueryTerms**: (cadeia de caracteres) termos de pesquisa inseridos pelo usuário
+ **ResultCount**: (int) número de documentos que foram retornados (vem na resposta de pesquisa)
+ **ScoringProfile**: (cadeia de caracteres) o nome do perfil de Pontuação usado, se houver

> [!NOTE]
> Solicite a contagem de consultas geradas pelo usuário adicionando $count = true à sua consulta de pesquisa. Para obter mais informações, consulte [Pesquisar documentos (REST)](/rest/api/searchservice/search-documents#counttrue--false).
>

**Como usar C#**

```csharp
var properties = new Dictionary <string, string> {
    {"SearchServiceName", <service name>},
    {"SearchId", <search Id>},
    {"IndexName", <index name>},
    {"QueryTerms", <search terms>},
    {"ResultCount", <results count>},
    {"ScoringProfile", <scoring profile used>}
    };
_telemetryClient.TrackEvent("Search", properties);
```

**Usar JavaScript**

```javascript
appInsights.trackEvent("Search", {
SearchServiceName: <service name>,
SearchId: <search id>,
IndexName: <index name>,
QueryTerms: <search terms>,
ResultCount: <results count>,
ScoringProfile: <scoring profile used>
});
```

### <a name="step-4-log-click-events"></a>Etapa 4: eventos de clique em log

Sempre que um usuário clica em um documento, esse é um sinal que deve ser registrado para fins de análise de pesquisa. Use os eventos personalizados do Application Insights para registrar esses eventos com o esquema a seguir:

+ **ServiceName: (** cadeia de caracteres) nome do serviço de pesquisa
+ **SearchId**: (GUID) identificador exclusivo da consulta de pesquisa relacionada
+ ID do documento **DocId**: (String)
+ **Posição**: (int) a classificação do documento na página de resultados da pesquisa

> [!NOTE]
> Position refere-se à ordem cardinal em seu aplicativo. Você tem a liberdade para definir esse número, desde que seja sempre o mesmo, para permitir a comparação.
>

**Como usar C#**

```csharp
var properties = new Dictionary <string, string> {
    {"SearchServiceName", <service name>},
    {"SearchId", <search id>},
    {"ClickedDocId", <clicked document id>},
    {"Rank", <clicked document position>}
    };
_telemetryClient.TrackEvent("Click", properties);
```

**Usar JavaScript**

```javascript
appInsights.trackEvent("Click", {
    SearchServiceName: <service name>,
    SearchId: <search id>,
    ClickedDocId: <clicked document id>,
    Rank: <clicked document position>
});
```

## <a name="3---analyze-in-power-bi"></a>3 - Analisar no Power BI

Depois de instrumentar seu aplicativo e verificar se seu aplicativo está conectado corretamente ao Application Insights, você baixa um modelo de relatório predefinido para analisar dados no Power BI desktop. O relatório contém gráficos predefinidos e tabelas úteis para analisar os dados adicionais capturados para análise de tráfego de pesquisa.

1. No painel de navegação à esquerda do painel de Pesquisa Cognitiva do Azure, em **configurações**, clique em **análise de tráfego de pesquisa**.

1. Na página **Análise de tráfego de pesquisa**, na etapa 3, clique em **Obter o Power BI Desktop** para instalar o Power BI.

   ![Obter Power BI relatórios](./media/search-traffic-analytics/get-use-power-bi.png "Obter Power BI relatórios")

1. Na mesma página, clique em **baixar Power bi relatório**.

1. O relatório é aberto no Power BI Desktop e você é solicitado a se conectar ao Application Insights e fornecer credenciais. Você pode encontrar informações de conexão nas páginas de portal do Azure para o recurso de Application Insights. Para credenciais, forneça o mesmo nome de usuário e senha que você usa para entrar no Portal.

   ![Conectar-se ao Application Insights](./media/search-traffic-analytics/connect-to-app-insights.png "Conectar-se ao Application Insights")

1. Clique em **Carregar**.

O relatório contém gráficos e tabelas que ajudam você a tomar decisões mais inteligentes a fim de melhorar o desempenho e a relevância da pesquisa.

As métricas incluem os seguintes itens:

+ Pesquisar o volume e os pares de documentos de termo mais populares: termos que resultam no mesmo documento clicado, ordenados por cliques.
+ Pesquisa sem cliques: termos das principais consultas que não registraram nenhum clique

A captura de tela a seguir mostra a aparência de um relatório interno se você tiver usado todos os elementos do esquema.

![Painel de Power BI para Pesquisa Cognitiva do Azure](./media/search-traffic-analytics/azuresearch-powerbi-dashboard.png "Painel de Power BI para Pesquisa Cognitiva do Azure")

## <a name="next-steps"></a>Próximas etapas

Instrumente seu aplicativo de pesquisa para obter dados informativos e avançados sobre o serviço de pesquisa.

Você pode encontrar mais informações sobre o [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) e visitar a [página de preços](https://azure.microsoft.com/pricing/details/application-insights/) para saber mais sobre suas diferentes camadas de serviço.

Saiba mais sobre como criar relatórios incríveis. Consulte [introdução ao Power bi desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/) para obter detalhes.