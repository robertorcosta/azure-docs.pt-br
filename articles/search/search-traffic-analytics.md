---
title: Telemetria para análise de tráfego de pesquisa
titleSuffix: Azure Cognitive Search
description: Habilite a análise de tráfego de pesquisa para pesquisa cognitiva do Azure, colete telemetria e eventos iniciados pelo usuário usando o Application Insights e, em seguida, analise as descobertas em um relatório do Power BI.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: db8c1781061f038cc90310fcd00c220fa6f5d1a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80258202"
---
# <a name="collect-telemetry-data-for-search-traffic-analytics"></a>Coletar dados de telemetria para análise de tráfego de pesquisa

A análise de tráfego de pesquisa é um padrão para coletar telemetria sobre interações do usuário com seu aplicativo Azure Cognitive Search, como eventos de clique iniciados pelo usuário e entradas de teclado. Usando essas informações, você pode determinar a eficácia de sua solução de pesquisa, incluindo termos de pesquisa populares, taxa de cliques e quais entradas de consulta não produzem resultados.

Esse padrão requer uma dependência do [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) (um recurso do [Azure Monitor)](https://docs.microsoft.com/azure/azure-monitor/)para coletar dados do usuário. Requer que você adicione instrumentação ao seu código de cliente, conforme descrito neste artigo. Finalmente, você precisará de um mecanismo de emissão de relatórios para analisar os dados. Recomendamos o Power BI, mas você pode usar o Painel de Aplicativos ou qualquer ferramenta que se conecte ao Application Insights.

> [!NOTE]
> O padrão descrito neste artigo é para cenários avançados e dados de clickstream gerados pelo código que você adiciona ao seu cliente. Em contraste, os registros de serviço são fáceis de configurar, fornecem uma série de métricas e podem ser feitos no portal sem necessidade de código. Habilitar o registro de diagnóstico é recomendado para todos os cenários. Para obter mais informações, consulte [Coletar e analisar dados de log](search-monitor-logs.md).

## <a name="identify-relevant-search-data"></a>Identificar dados de pesquisa relevantes

Para ter métricas úteis para análise de tráfego de pesquisa, é necessário registrar alguns sinais dos usuários do seu aplicativo de pesquisa. Esses sinais significam conteúdo que os usuários estão interessados e que consideram relevantes. Para análise de tráfego de pesquisa, estes incluem:

+ Eventos de pesquisa gerados pelo usuário: Apenas as consultas de pesquisa iniciadas por um usuário são interessantes. Solicitações de pesquisa usadas para preencher facetas, conteúdo adicional ou qualquer informação interna, não são importantes e distorcem e influenciam seus resultados.

+ Eventos de clique gerados pelo usuário: Em uma página de resultados de pesquisa, um evento de clique geralmente significa que um documento é um resultado relevante para uma consulta de pesquisa específica.

Ao vincular eventos de pesquisa e clique com um ID de correlação, você obterá uma compreensão mais profunda do desempenho da funcionalidade de pesquisa do seu aplicativo.

## <a name="add-search-traffic-analytics"></a>Adicionar análise de tráfego de pesquisa

Na página do [portal](https://portal.azure.com) do seu serviço de pesquisa cognitiva Azure, a página Search Traffic Analytics contém uma folha de trapaça para seguir esse padrão de telemetria. A partir desta página, você pode selecionar ou criar um recurso do Application Insights, obter a chave de instrumentação, copiar trechos que você pode adaptar para sua solução e baixar um relatório do Power BI que é construído sobre o esquema refletido no padrão.

![Pesquisar página de Análise de Tráfego no portal](media/search-traffic-analytics/azuresearch-trafficanalytics.png "Pesquisar página de Análise de Tráfego no portal")

## <a name="1---set-up-application-insights"></a>1 - Configurar insights de aplicativos

Selecione um recurso de Crimentos de aplicativos existente ou [crie um](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) se você ainda não tiver um. Se você usar a página Search Traffic Analytics, poderá copiar a chave de instrumentação que seu aplicativo precisa para se conectar ao Application Insights.

Depois de ter um recurso application insights, você pode seguir [instruções para idiomas e plataformas suportadas](https://docs.microsoft.com/azure/azure-monitor/app/platforms) para registrar seu aplicativo. O registro é simplesmente adicionar a chave de instrumentação do Application Insights ao seu código, que configura a associação. Você pode encontrar a chave no portal ou na página Search Traffic Analytics quando selecionar um recurso existente.

Um atalho que funciona para alguns tipos de projetos do Visual Studio é refletido nas etapas a seguir. Ele cria um recurso e registra seu aplicativo em apenas alguns cliques.

1. Para o desenvolvimento do Visual Studio e ASP.NET, abra sua solução e selecione**Telemetria** **project** > add insights de aplicativos .

1. Clique **em Começar**.

1. Registre seu aplicativo fornecendo uma conta Microsoft, assinatura do Azure e um recurso do Application Insights (um novo recurso é o padrão). Clique em **Registrar**.

Neste ponto, seu aplicativo está configurado para monitoramento de aplicativos, o que significa que todas as cargas de página são rastreadas com métricas padrão. Para obter mais informações sobre as etapas anteriores, consulte Ativar a [telemetria do lado do servidor do Aplicativo Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core#enable-application-insights-server-side-telemetry-visual-studio).

## <a name="2---add-instrumentation"></a>2 - Adicionar instrumentação

Esta etapa é onde você instrumenta seu próprio aplicativo de pesquisa, usando o recurso Application Insights criado na etapa acima. Há quatro etapas para esse processo, começando pela criação de um cliente de telemetria.

### <a name="step-1-create-a-telemetry-client"></a>Passo 1: Crie um cliente de telemetria

Crie um objeto que envie eventos para o Application Insights. Você pode adicionar instrumentação ao código do aplicativo do lado do servidor ou ao código do lado do cliente em execução em um navegador, expresso aqui como variantes C# e JavaScript (para outros idiomas, veja a lista completa de [plataformas e frameworks suportados.](https://docs.microsoft.com/azure/application-insights/app-insights-platforms) Escolha a abordagem que lhe dá a profundidade de informação desejada.

A telemetria do lado do servidor captura métricas na camada de aplicativos, por exemplo, em aplicativos em execução como um serviço web na nuvem ou como um aplicativo local em uma rede corporativa. A telemetria do lado do servidor captura eventos de pesquisa e clique, a posição de um documento em resultados e informações de consulta, mas sua coleta de dados será escopo para quaisquer informações disponíveis nessa camada.

No cliente, você pode ter um código adicional que manipula entradas de consulta, adiciona navegação ou inclui contexto (por exemplo, consultas iniciadas a partir de uma página inicial versus uma página do produto). Se isso descrever sua solução, você pode optar pela instrumentação do lado do cliente para que sua telemetria reflita os detalhes adicionais. Como esse detalhe adicional é coletado vai além do escopo deste padrão, mas você pode rever [insights de aplicativos para páginas da Web](https://docs.microsoft.com/azure/azure-monitor/app/javascript#explore-browserclient-side-data) para obter mais direção. 

**Como usar C#**

Para C#, a **InstrumentationKey** é encontrada na configuração do aplicativo, como appsettings.json se o seu projeto for ASP.NET. Consulte as instruções de registro se não tiver certeza do local da chave.

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

### <a name="step-2-request-a-search-id-for-correlation"></a>Passo 2: Solicite um ID de pesquisa para correlação

Para correlacionar solicitações de pesquisa com cliques, é necessário ter um ID de correlação que relacione esses dois eventos distintos. A Pesquisa Cognitiva do Azure fornece um ID de pesquisa ao solicitá-lo com um cabeçalho HTTP.

Ter o ID de pesquisa permite a correlação das métricas emitidas pelo Azure Cognitive Search para a solicitação em si, com as métricas personalizadas que você está registrando no Application Insights.  

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

**Use JavaScript (chamando APIs REST)**

```javascript
request.setRequestHeader("x-ms-azs-return-searchid", "true");
request.setRequestHeader("Access-Control-Expose-Headers", "x-ms-azs-searchid");
var searchId = request.getResponseHeader('x-ms-azs-searchid');
```

### <a name="step-3-log-search-events"></a>Passo 3: Eventos de pesquisa de log

Toda vez que uma solicitação de pesquisa é emitida por um usuário, você deve registrar isso como um evento de pesquisa com o seguinte esquema em um evento personalizado do Application Insights. Lembre-se de registrar apenas consultas de pesquisa geradas pelo usuário.

+ **SearchServiceName**: (string) nome do serviço de pesquisa
+ **SearchId**: (guid) identificador exclusivo da consulta de pesquisa (vem na resposta de pesquisa)
+ **IndexName**: (string) índice de serviço de pesquisa a ser consultado
+ **ConsultaTermos:**(string) termos de pesquisa inseridos pelo usuário
+ **Contagem de**resultados : (int) número de documentos que foram devolvidos (vem na resposta de pesquisa)
+ **ScoringProfile**: (string) nome do perfil de pontuação usado, se houver

> [!NOTE]
> Solicite a contagem de consultas geradas pelo usuário adicionando $count=true à sua consulta de pesquisa. Para obter mais informações, consulte [Documentos de pesquisa (REST)](/rest/api/searchservice/search-documents#counttrue--false).
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

### <a name="step-4-log-click-events"></a>Passo 4: Registre eventos de clique

Sempre que um usuário clica em um documento, esse é um sinal que deve ser registrado para fins de análise de pesquisa. Use os eventos personalizados do Application Insights para registrar esses eventos com o esquema a seguir:

+ **Nome do serviço:**(string) nome do serviço de pesquisa
+ **SearchId**: (guid) identificador exclusivo da consulta de pesquisa relacionada
+ **Identificador de documento DocId**: (string)
+ **Posição**: (int) classificação do documento na página de resultados da pesquisa

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

Depois de ter instrumentado seu aplicativo e verificado que seu aplicativo está corretamente conectado ao Application Insights, você baixa um modelo de relatório predefinido para analisar dados na área de trabalho do Power BI. O relatório contém gráficos e tabelas predefinidos úteis para analisar os dados adicionais capturados para análise de tráfego de pesquisa.

1. No painel de navegação esquerda do painel de navegação esquerda do Painel de Pesquisa Cognitiva do Azure, em **Configurações,** clique **em Pesquisar análises de tráfego**.

1. Na página **Análise de tráfego de pesquisa**, na etapa 3, clique em **Obter o Power BI Desktop** para instalar o Power BI.

   ![Obtenha relatórios de power bi](./media/search-traffic-analytics/get-use-power-bi.png "Obtenha relatórios de power bi")

1. Na mesma página, clique em **Baixar relatório Power BI**.

1. O relatório é aberto no Power BI Desktop, e você é solicitado a se conectar ao Application Insights e fornecer credenciais. Você pode encontrar informações de conexão nas páginas do portal Azure para o recurso Application Insights. Para credenciais, forneça o mesmo nome de usuário e senha que você usa para o login do portal.

   ![Conectar-se ao Application Insights](./media/search-traffic-analytics/connect-to-app-insights.png "Conectar-se ao Application Insights")

1. Clique em **Carregar**.

O relatório contém gráficos e tabelas que ajudam você a tomar decisões mais inteligentes a fim de melhorar o desempenho e a relevância da pesquisa.

As métricas incluem os seguintes itens:

+ Volume de pesquisa e pares de documentos de termo mais populares: termos que resultam no mesmo documento clicado, ordenado por cliques.
+ Pesquisa sem cliques: termos das principais consultas que não registraram nenhum clique

A captura de tela a seguir mostra como um relatório embutido pode parecer se você tiver usado todos os elementos do esquema.

![Painel power bi para pesquisa cognitiva do Azure](./media/search-traffic-analytics/azuresearch-powerbi-dashboard.png "Painel power bi para pesquisa cognitiva do Azure")

## <a name="next-steps"></a>Próximas etapas

Instrumente seu aplicativo de pesquisa para obter dados informativos e avançados sobre o serviço de pesquisa.

Você pode encontrar mais informações sobre o [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) e visitar a [página de preços](https://azure.microsoft.com/pricing/details/application-insights/) para saber mais sobre suas diferentes camadas de serviço.

Saiba mais sobre como criar relatórios incríveis. Consulte [Como começar com o Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/) para obter detalhes.