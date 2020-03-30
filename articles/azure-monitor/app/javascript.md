---
title: Insights do aplicativo Azure para aplicativos web JavaScript
description: Obtenha exibição de página e contagem de sessões, dados do cliente da Web, SPA (Single Page Applications, aplicações de página única) e rastreie padrões de uso. Detecte exceções e problemas de desempenho em páginas da Web do JavaScript.
ms.topic: conceptual
author: Dawgfan
ms.author: mmcc
ms.date: 09/20/2019
ms.openlocfilehash: 5414a70180a82be8253dace7d800c90c1ae6a9bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276069"
---
# <a name="application-insights-for-web-pages"></a>Application Insights para páginas da Web

Saiba mais sobre o desempenho e o uso de sua página da Web ou aplicativo. Se você adicionar [insights de aplicativos](app-insights-overview.md) ao seu script de página, você receberá tempos de cargas de página e chamadas AJAX, contagens e detalhes de exceções do navegador e falhas do AJAX, bem como usuários e contagem de sessões. Todos esses itens podem ser segmentados por página, sistema operacional cliente e versão do navegador, localização geográfica e outras dimensões. Você pode definir alertas para contagens de falhas ou carregamento de páginas lento. E inserindo chamadas de rastreamento em seu código JavaScript, você pode controlar como os diferentes recursos do seu aplicativo de página da Web são usados.

O Application Insights pode ser usado com todas as páginas da Web: basta adicionar um breve trecho de JavaScript. Se o seu serviço web for [Java](java-get-started.md) ou [ASP.NET,](asp-net.md)você pode usar os SDKs do lado do servidor em conjunto com o JavaScript SDK do lado do cliente para obter uma compreensão completa do desempenho do seu aplicativo.

## <a name="adding-the-javascript-sdk"></a>Adicionando o JavaScript SDK

1. Primeiro você precisa de um recurso de Criações de Aplicativos. Se você ainda não tiver uma chave de recursos e instrumentação, siga [a criação de novas instruções de recurso](create-new-resource.md).
2. Copie a chave de instrumentação do recurso onde deseja que sua telemetria JavaScript seja enviada.
3. Adicione o App Insights JavaScript SDK à sua página ou aplicativo da Web através de uma das duas opções a seguir:
    * [configuração npm](#npm-based-setup)
    * [JavaScript Snippet](#snippet-based-setup)

> [!IMPORTANT]
> Use apenas um método para adicionar o JavaScript SDK ao seu aplicativo. Se você usar a configuração NPM, não use o Snippet e vice-versa.

> [!NOTE]
> A configuração do NPM instala o JavaScript SDK como uma dependência do seu projeto, permitindo o IntelliSense, enquanto o Snippet busca o SDK em tempo de execução. Ambos suportam as mesmas características. No entanto, os desenvolvedores que desejam mais eventos e configuração personalizados geralmente optam pela configuração do NPM, enquanto os usuários que procuram uma rápida ativação de análises web fora da caixa optam pelo Snippet.

### <a name="npm-based-setup"></a>configuração baseada em npm

```js
import { ApplicationInsights } from '@microsoft/applicationinsights-web'

const appInsights = new ApplicationInsights({ config: {
  instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE'
  /* ...Other Configuration Options... */
} });
appInsights.loadAppInsights();
appInsights.trackPageView(); // Manually call trackPageView to establish the current user/session/pageview
```

### <a name="snippet-based-setup"></a>Configuração baseada em snippet

Se o seu aplicativo não usar npm, você pode instrumentalar diretamente suas páginas da Web com o Application Insights colando este trecho na parte superior de cada uma de suas páginas. De preferência, ele deve ser `<head>` o primeiro script em sua seção para que ele possa monitorar quaisquer problemas potenciais com todas as suas dependências. Se você estiver usando o Aplicativo Blazor Server, adicione `_Host.cshtml` o `<head>` trecho na parte superior do arquivo na seção.

```html
<script type="text/javascript">
var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(n){var o={config:n,initialize:!0},t=document,e=window,i="script";setTimeout(function(){var e=t.createElement(i);e.src=n.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",t.getElementsByTagName(i)[0].parentNode.appendChild(e)});try{o.cookie=t.cookie}catch(e){}function a(n){o[n]=function(){var e=arguments;o.queue.push(function(){o[n].apply(o,e)})}}o.queue=[],o.version=2;for(var s=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];s.length;)a("track"+s.pop());var r="Track",c=r+"Page";a("start"+c),a("stop"+c);var u=r+"Event";if(a("start"+u),a("stop"+u),a("addTelemetryInitializer"),a("setAuthenticatedUserContext"),a("clearAuthenticatedUserContext"),a("flush"),o.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4},!(!0===n.disableExceptionTracking||n.extensionConfig&&n.extensionConfig.ApplicationInsightsAnalytics&&!0===n.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){a("_"+(s="onerror"));var p=e[s];e[s]=function(e,n,t,i,a){var r=p&&p(e,n,t,i,a);return!0!==r&&o["_"+s]({message:e,url:n,lineNumber:t,columnNumber:i,error:a}),r},n.autoExceptionInstrumented=!0}return o}(
{
  instrumentationKey:"INSTRUMENTATION_KEY"
}
);(window[aiName]=aisdk).queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```

### <a name="sending-telemetry-to-the-azure-portal"></a>Envio de telemetria para o portal Azure

Por padrão, o Aplicativo Insights JavaScript SDK coleta automaticamente uma série de itens de telemetria que são úteis para determinar a saúde do seu aplicativo e a experiência do usuário subjacente. Eles incluem:

- **Exceções não capturadas** em seu aplicativo, incluindo informações sobre
    - Rastreamento de pilha
    - Detalhes de exceção e mensagem que acompanha o erro
    - Número de erro da coluna de & de linha
    - URL onde o erro foi levantado
- **Solicitações de dependência de rede** feitas pelo seu aplicativo **XHR** e **Fetch** (a coleta de rebusca é desativada por padrão) solicitações, incluem informações sobre
    - Url da fonte de dependência
    - Método de & de comando usado para solicitar a dependência
    - Duração da solicitação
    - Código de resultado e status de sucesso da solicitação
    - ID (se houver) do usuário que fizer a solicitação
    - Contexto de correlação (se houver) quando a solicitação é feita
- **Informações do usuário** (por exemplo, Localização, rede, IP)
- **Informações do dispositivo** (por exemplo, Navegador, Sistema Operacional, versão, idioma, resolução, modelo)
- **Informações da sessão**

### <a name="telemetry-initializers"></a>Iniciadores de telemetria
Iniciadores de telemetria são usados para modificar o conteúdo da telemetria coletada antes de serem enviados do navegador do usuário. Eles também podem ser usados para evitar que certas `false`telemetrias sejam enviadas, retornando . Várias iniciais de telemetria podem ser adicionadas à instância do Application Insights e são executadas por ordem de adicioná-los.

O argumento `addTelemetryInitializer` de entrada é um [`ITelemetryItem`](https://github.com/microsoft/ApplicationInsights-JS/blob/master/API-reference.md#addTelemetryInitializer) retorno de chamada `boolean` `void`que toma um argumento e retorna a ou . Se `false`retornar, o item de telemetria não é enviado, caso não seja enviado para o próximo inicializador de telemetria, se houver, ou seja enviado para o ponto final da coleta de telemetria.

Um exemplo de uso de iniciadores de telemetria:
```ts
var telemetryInitializer = (envelope) => {
  envelope.data.someField = 'This item passed through my telemetry initializer';
};
appInsights.addTelemetryInitializer(telemetryInitializer);
appInsights.trackTrace({message: 'This message will use a telemetry initializer'});

appInsights.addTelemetryInitializer(() => false); // Nothing is sent after this is executed
appInsights.trackTrace({message: 'this message will not be sent'}); // Not sent
```

## <a name="configuration"></a>Configuração
A maioria dos campos de configuração são nomeados de modo que eles podem ser padrão para falso. Todos os campos `instrumentationKey`são opcionais, exceto .

| Nome | Padrão | Descrição |
|------|---------|-------------|
| instrumentationKey | null | **Obrigatório**<br>Chave de instrumentação que você obteve do portal Azure. |
| accountId | null | Um ID de conta opcional, se o aplicativo agrupar os usuários em contas. Sem espaços, vírgulas, ponto e vírgula, iguais ou barras verticais |
| sessionRenewalMs | 1800000 | Uma sessão é registrada se o usuário estiver inativo por esse período de tempo em milissegundos. O padrão é 30 minutos |
| sessionExpirationMs | 86400000 | Uma sessão é registrada se ela tiver continuado por esse período de tempo em milissegundos. O padrão é de 24 horas |
| maxBatchSizeInBytes | 10000 | Tamanho máximo do lote de telemetria. Se um lote exceder esse limite, ele é imediatamente enviado e um novo lote é iniciado |
| maxBatchInterval | 15000 | Quanto tempo para telemetria em lote para antes de enviar (milissegundos) |
| desativarDesaExceçãoRastreando | false | Se for verdade, as exceções não são coletadas automaticamente. O padrão é false. |
| desabilitação Telemetria | false | Se for verdade, a telemetria não é coletada ou enviada. O padrão é false. |
| habilitarDebug | false | Se for verdade, os dados de depuração **interna** são lançados como uma exceção **em vez** de serem registrados, independentemente das configurações de registro do SDK. O padrão é false. <br>***Nota:*** A ativação dessa configuração resultará em telemetria descartada sempre que ocorrer um erro interno. Isso pode ser útil para identificar rapidamente problemas com sua configuração ou uso do SDK. Se você não quiser perder a telemetria durante `consoleLoggingLevel` `telemetryLoggingLevel` a `enableDebug`depuração, considere usar ou em vez de . |
| loggingLevelConsole | 0 | Registra erros **internos** do Application Insights para console. <br>0: desligado, <br>1: Apenas erros críticos, <br>2: Tudo (erros & avisos) |
| loglevelTelemetry | 1 | Envia erros **internos** do Application Insights como telemetria. <br>0: desligado, <br>1: Apenas erros críticos, <br>2: Tudo (erros & avisos) |
| diagnósticoLogInterval | 10000 | (interna) Intervalo de votação (em ms) para fila de registro interno |
| amostragemPorcentagem | 100 | Porcentagem de eventos que serão enviados. O padrão é 100, o que significa que todos os eventos são enviados. Defina isso se desejar preservar sua tampa de dados para aplicativos de grande escala. |
| autoTrackPageVisitTime | false | Se for verdade, em uma exibição de página, o tempo de exibição da página instrumentada anterior é rastreado e enviado como telemetria e um novo temporizador é iniciado para a exibição de página atual. O padrão é false. |
| desativarAjaxTracking | false | Se for verdade, as chamadas do Ajax não são coletadas automaticamente. O padrão é false. |
| desativarFetchTracking | true | Se for verdade, as solicitações do Fetch não são coletadas automaticamente. O padrão é verdadeiro |
| overridePageViewDuração | false | Se for verdade, o comportamento padrão do trackPageView será alterado para gravar o fim do intervalo de duração da exibição da página quando o trackPageView for chamado. Se for fornecida uma duração falsa e sem duração personalizada para rastrear o PageView, o desempenho da exibição da página será calculado usando a API de tempo de navegação. O padrão é false. |
| maxAjaxCallsPerView | 500 | Padrão 500 - controla quantas chamadas do Ajax serão monitoradas por exibição de página. Programado para -1 para monitorar todas as chamadas (ilimitadas) do Ajax na página. |
| desativarDataLossAnalysis | true | Se forem falsos, os buffers de remetente de telemetria interna serão verificados na inicialização para itens ainda não enviados. |
| desativarCorantes | false | Se for falso, o SDK adicionará dois cabeçalhos ('Solicitar-Id' e 'Request-Context') a todas as solicitações de dependência para correlaciona-las com solicitações correspondentes no lado do servidor. O padrão é false. |
| correlaçãoHeaderExcluídoDomínios |  | Desativar cabeçalhos de correlação para domínios específicos |
| correlaçãoHeaderDomains |  | Habilite cabeçalhos de correlação para domínios específicos |
| desativar FlushOnBeforeUnload | false | Padrão falso. Se for verdade, o método flush não será chamado quando o evento onBeforeUnload for acionado |
| habilitarBuffer de armazenamento de armazenamento de sessão | true | Padrão verdadeiro. Se for verdade, o buffer com toda telemetria não enviada é armazenado no armazenamento da sessão. O buffer é restaurado na carga da página |
| isCookieUseDisabled | false | Padrão falso. Se for verdade, o SDK não armazenará ou lerá quaisquer dados de cookies.|
| CookieDomínio | null | Domínio de cookies personalizados. Isso é útil se você quiser compartilhar cookies do Application Insights entre subdomínios. |
| isRetryDisabled | false | Padrão falso. Se for falso, tente novamente em 206 (sucesso parcial), 408 (tempo hámenos), 429 (muitas solicitações), 500 (erro interno do servidor), 503 (serviço indisponível) e 0 (offline, somente se detectado) |
| isArmazenamentoUseDisabledDisabled | false | Se for verdade, o SDK não armazenará ou lerá quaisquer dados do armazenamento local e de sessão. O padrão é false. |
| isBeaconApiDisabled | true | Se for falso, o SDK enviará toda a telemetria usando a [API do Beacon](https://www.w3.org/TR/beacon) |
| onunloadDisableBeacon | false | Padrão falso. quando a guia estiver fechada, o SDK enviará toda a telemetria restante usando a [API beacon](https://www.w3.org/TR/beacon) |
| sdkExtensão | null | Define o nome da extensão sdk. Apenas caracteres alfabéticos são permitidos. O nome de extensão é adicionado como um prefixo à tag 'ai.internal.sdkVersion' (por exemplo, 'ext_javascript:2.0.0'). O padrão é nulo. |
| éBrowserLinkTrackingAtivadoAtivado | false | O padrão é false. Se for verdade, o SDK acompanhará todas as solicitações [do Browser Link.](https://docs.microsoft.com/aspnet/core/client-side/using-browserlink) |
| appId | null | O AppId é usado para a correlação entre as dependências AJAX que acontecem no lado do cliente com as solicitações do lado do servidor. Quando a API beacon está ativada, ela não pode ser usada automaticamente, mas pode ser configurada manualmente na configuração. A inadimplência é nula |
| habilitarCorsCor | false | Se for verdade, o SDK adicionará dois cabeçalhos ('Solicitante-Id' e 'Request-Context') a todas as solicitações do CORS para correlacionar as dependências AJAX de saída com as solicitações correspondentes no lado do servidor. O padrão é falso |
| nomePrefix | não definido | Um valor opcional que será usado como postfixo de nome para localArmazenamento e nome do cookie.
| habilitarAutoRouteTracking | false | Acompanhe automaticamente as alterações de rota em SPA (Single Page Applications, aplicações de página única). Se for verdade, cada alteração de rota enviará uma nova Pageview para o Application Insights. As alterações`example.com/foo#bar`de rota hash () também são registradas como novas visualizações de página.
| habilitarRequestHeaderTracking | false | Se for verdade, os cabeçalhos de solicitação do AJAX & Fetch são rastreados, o padrão é falso.
| habilitarResponseHeaderTracking | false | Se for verdade, os cabeçalhos de resposta do AJAX & Fetch são rastreados, o padrão é falso.
| distributedTracingMode | `DistributedTracingModes.AI` | Define o modo de rastreamento distribuído. Se AI_AND_W3C modo ou modo W3C estiver definido, os cabeçalhos de contexto de rastreamento W3C (traceparent/tracestate) serão gerados e incluídos em todas as solicitações de saída. AI_AND_W3C é fornecida para retrocompatibilidade com quaisquer serviços instrumentados de aplicativos legados.

## <a name="single-page-applications"></a>Aplicativos de página única

Por padrão, este SDK **não** lidará com a alteração de rota baseada em estado que ocorre em aplicativos de página única. Para habilitar o rastreamento automático de alterações `enableAutoRouteTracking: true` de rota para o aplicativo de uma única página, você pode adicionar à configuração de configuração.

Atualmente, oferecemos um [plugin React](#react-extensions) separado que você pode inicializar com este SDK. Ele também realizará o rastreamento de mudança de rota para você, bem como [coletará outra telemetria específica react](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-js/README.md).

## <a name="react-extensions"></a>Extensões de reação

| Extensões |
|---------------|
| [React](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-js/README.md)|
| [React Native](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-native/README.md)|

## <a name="explore-browserclient-side-data"></a>Explorar dados do navegador/lado do cliente

Os dados do lado do navegador/cliente podem ser visualizados indo para **Métricas** e adicionando métricas individuais que você está interessado:

![](./media/javascript/page-view-load-time.png)

Você também pode visualizar seus dados do JavaScript SDK através da experiência do Navegador no portal.

Selecione **Navegador** e escolha **Falhas** ou **Desempenho**.

![](./media/javascript/browser.png)

### <a name="performance"></a>Desempenho

![](./media/javascript/performance-operations.png)

### <a name="dependencies"></a>Dependências

![](./media/javascript/performance-dependencies.png)

### <a name="analytics"></a>Análise

Para consultar sua telemetria coletada pelo JavaScript SDK, selecione o botão **Exibir em Logs (Analytics).** Adicionando `where` uma `client_Type == "Browser"`declaração de , você só verá dados do JavaScript SDK e qualquer telemetria do lado do servidor coletada por outros SDKs será excluída.
 
```kusto
// average pageView duration by name
let timeGrain=5m;
let dataset=pageViews
// additional filters can be applied here
| where timestamp > ago(1d)
| where client_Type == "Browser" ;
// calculate average pageView duration for all pageViews
dataset
| summarize avg(duration) by bin(timestamp, timeGrain)
| extend pageView='Overall'
// render result in a chart
| render timechart
```

### <a name="source-map-support"></a>Suporte ao mapa de origem

O painel de chamadas minizado da sua telemetria de exceção pode ser desminibitado no portal Azure. Todas as integrações existentes no painel Detalhes de exceção funcionarão com o pilha de chamadas recém-não mapeado.

#### <a name="link-to-blob-storage-account"></a>Link para conta de armazenamento Blob

Você pode vincular seu recurso Application Insights ao seu próprio contêiner Azure Blob Storage a pilhas de chamadas automaticamente inminividas. Para começar, consulte [suporte automático do mapa de origem](./source-map-support.md).

### <a name="drag-and-drop"></a>Arrastar e soltar

1. Selecione um item de telemetria de exceção no portal Azure para exibir seus "detalhes de transação de ponta a ponta"
2. Identifique quais mapas de origem correspondem a esta pilha de chamadas. O mapa de origem deve corresponder ao arquivo de origem de um quadro de pilha, mas sufixado com`.map`
3. Arraste e solte os mapas de origem na pilha de chamadas no portal Azure![](https://i.imgur.com/Efue9nU.gif)

### <a name="application-insights-web-basic"></a>Insights de aplicativos Web Basic

Para uma experiência leve, você pode instalar a versão básica do Application Insights
```
npm i --save @microsoft/applicationinsights-web-basic
```
Esta versão vem com o número mínimo de recursos e funcionalidades e depende de você para construí-la como achar melhor. Por exemplo, ele não executa nenhuma coleção automática (exceções não capturadas, AJAX, etc.). As APIs para enviar certos tipos `trackTrace`de `trackException`telemetria, como , etc., não estão incluídas nesta versão, então você precisará fornecer seu próprio invólucro. A única API disponível é `track`. Uma [amostra](https://github.com/Azure-Samples/applicationinsights-web-sample1/blob/master/testlightsku.html) está localizada aqui.

## <a name="examples"></a>Exemplos

Para exemplos executáveis, consulte [Amostras javaScript SDK do App Insights](https://github.com/topics/applicationinsights-js-demo)

## <a name="upgrading-from-the-old-version-of-application-insights"></a>Atualizando da versão antiga do Application Insights

Alterações na versão SDK V2:
- Para permitir melhores assinaturas de API, algumas das chamadas da API, como trackPageView e trackException, foram atualizadas. A execução no Internet Explorer 8 e versões anteriores do navegador não é suportada.
- O envelope de telemetria tem alterações no nome do campo e na estrutura devido a atualizações de esquema de dados.
- Mudou-se `context.operation` para. `context.telemetryTrace` Alguns campos também`operation.id` --> `telemetryTrace.traceID`foram alterados ( ).
  - Para atualizar manualmente o ID de exibição de `appInsights.properties.context.telemetryTrace.traceID = Util.generateW3CId()`página atual (por exemplo, em aplicativos SPA), use .
    > [!NOTE]
    > Para manter o identificador de rastreamento `Util.newId()`único, `Util.generateW3CId()`onde você usou anteriormente, agora use . Ambos acabam sendo a ID da operação.

Se você estiver usando os insights atuais do aplicativo PRODUCTION SDK (1.0.20) e quiser ver se o novo SDK funciona em tempo de execução, atualize a URL dependendo do cenário atual de carregamento do SDK.

- Baixe via cenário CDN: Atualize o trecho de código que você usa atualmente para apontar para a seguinte URL:
   ```
   "https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js"
   ```

- cenário npm: `downloadAndSetup` Ligue para baixar o script completo do ApplicationInsights do CDN e inicializá-lo com a chave de instrumentação:

   ```ts
   appInsights.downloadAndSetup({
     instrumentationKey: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx",
     url: "https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js"
     });
   ```

Teste em ambiente interno para verificar se a telemetria de monitoramento está funcionando como esperado. Se tudo funcionar, atualize suas assinaturas de API apropriadamente para a versão SDK V2 e implante em seus ambientes de produção.

## <a name="sdk-performanceoverhead"></a>Desempenho/sobrecarga do SDK

Com apenas 25 KB gzipped, e levando apenas ~15 ms para inicializar, o Application Insights adiciona uma quantidade insignificante de tempo de carga ao seu site. Usando o trecho, componentes mínimos da biblioteca são rapidamente carregados. Enquanto isso, o script completo é baixado em segundo plano.

Enquanto o script está baixando do CDN, todo o rastreamento da sua página está na fila. Uma vez que o script baixado termina de inicializar assíncronamente, todos os eventos que estavam na fila são rastreados. Como resultado, você não perderá nenhuma telemetria durante todo o ciclo de vida de sua página. Esse processo de configuração fornece à sua página um sistema de análise perfeito, invisível para seus usuários.

> Resumo:
> - **25 KB** gzipped
> - **15 ms** tempo total de inicialização
> - **Rastreamento zero** perdido durante o ciclo de vida da página

## <a name="browser-support"></a>Suporte ao navegador

![Chrome](https://raw.githubusercontent.com/alrra/browser-logos/master/src/chrome/chrome_48x48.png) | ![Firefox](https://raw.githubusercontent.com/alrra/browser-logos/master/src/firefox/firefox_48x48.png) | ![IE](https://raw.githubusercontent.com/alrra/browser-logos/master/src/edge/edge_48x48.png) | ![Opera](https://raw.githubusercontent.com/alrra/browser-logos/master/src/opera/opera_48x48.png) | ![Safari](https://raw.githubusercontent.com/alrra/browser-logos/master/src/safari/safari_48x48.png)
--- | --- | --- | --- | --- |
Chrome Última ✔ |  Firefox última ✔ | IE 9+ & Edge ✔ | Ópera Última ✔ | Safari última ✔ |

## <a name="open-source-sdk"></a>SDK do código-fonte aberto

O Application Insights JavaScript SDK é de código aberto para visualizar o código-fonte ou para contribuir para que o projeto visite o [repositório oficial do GitHub.](https://github.com/Microsoft/ApplicationInsights-JS)

## <a name="next-steps"></a><a name="next"></a> Próximas etapas
* [Acompanhar uso](usage-overview.md)
* [Eventos e métricas personalizados](api-custom-events-metrics.md)
* [Build-measure-learn](usage-overview.md)
