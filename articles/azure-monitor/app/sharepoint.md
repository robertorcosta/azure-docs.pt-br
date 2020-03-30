---
title: Monitorar um site do SharePoint com o Application Insights
description: Iniciar o monitoramento de um novo aplicativo com uma nova chave de instrumentação
ms.topic: conceptual
ms.date: 07/11/2018
ms.openlocfilehash: 395e8d667985318f4a084428c6fd4c395ee8b956
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671436"
---
# <a name="monitor-a-sharepoint-site-with-application-insights"></a>Monitorar um site do SharePoint com o Application Insights
O Azure Application Insights monitora a disponibilidade, o desempenho e o uso de seus aplicativos. Aqui você aprenderá a configurá-lo para um site do SharePoint.

## <a name="create-an-application-insights-resource"></a>Criar um recurso do Application Insights
No [Portal do Azure](https://portal.azure.com), crie um novo recurso do Application Insights. Escolha ASP.NET como o tipo de aplicativo.

![Clique em Propriedades, selecione a chave e pressione ctrl + C](./media/sharepoint/001.png)

A folha que será aberta é onde você verá os dados de uso e desempenho do seu aplicativo. Para retornar a ela na próxima vez em que fizer logon no Azure, você deverá encontrar um bloco para ela na tela inicial. Como alternativa, clique em Procurar para localizá-la.

## <a name="add-the-script-to-your-web-pages"></a>Adicione o script às suas páginas da Web

```HTML
<!-- 
To collect user behavior analytics tools about your application, 
insert the following script into each page you want to track.
Place this code immediately before the closing </head> tag,
and before any other scripts. Your first data will appear 
automatically in just a few seconds.
-->
<script type="text/javascript">
var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(n){var o={config:n,initialize:!0},t=document,e=window,i="script";setTimeout(function(){var e=t.createElement(i);e.src=n.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",t.getElementsByTagName(i)[0].parentNode.appendChild(e)});try{o.cookie=t.cookie}catch(e){}function a(n){o[n]=function(){var e=arguments;o.queue.push(function(){o[n].apply(o,e)})}}o.queue=[],o.version=2;for(var s=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];s.length;)a("track"+s.pop());var r="Track",c=r+"Page";a("start"+c),a("stop"+c);var u=r+"Event";if(a("start"+u),a("stop"+u),a("addTelemetryInitializer"),a("setAuthenticatedUserContext"),a("clearAuthenticatedUserContext"),a("flush"),o.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4},!(!0===n.disableExceptionTracking||n.extensionConfig&&n.extensionConfig.ApplicationInsightsAnalytics&&!0===n.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){a("_"+(s="onerror"));var p=e[s];e[s]=function(e,n,t,i,a){var r=p&&p(e,n,t,i,a);return!0!==r&&o["_"+s]({message:e,url:n,lineNumber:t,columnNumber:i,error:a}),r},n.autoExceptionInstrumented=!0}return o}(
{
  instrumentationKey:"INSTRUMENTATION_KEY"
}
);(window[aiName]=aisdk).queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```

Insira o &lt;script&gt; pouco antes da tag /head de cada página que você deseja rastrear. Se o seu site tiver uma página-mestre, você pode colocar o script lá. Por exemplo, em um projeto ASP.NET MVC, você deve colocá-lo em View\Shared\_Layout. cshtml

O script contém a chave de instrumentação que direciona a telemetria para o recurso do Application Insights.

### <a name="add-the-code-to-your-site-pages"></a>Adicionar o código às páginas do site
#### <a name="on-the-master-page"></a>Na página mestra
Se você puder editar a página mestra do site, que fornecerá monitoramento para todas as páginas no site.

Confira a página mestra e edite-a usando o SharePoint Designer ou qualquer outro editor.

![](./media/sharepoint/03-master.png)

Adicione o código antes da marca </head> . 

![](./media/sharepoint/04-code.png)

#### <a name="or-on-individual-pages"></a>Ou em páginas individuais
Para monitorar um conjunto limitado de páginas, adicione o script separadamente a cada página. 

Insira uma web part e insira o snippet de código nela.

![](./media/sharepoint/05-page.png)

## <a name="view-data-about-your-app"></a>Exibir dados sobre seu aplicativo
Reimplante o aplicativo.

Retorne à folha de seu aplicativo no [portal do Azure](https://portal.azure.com).

Os primeiros eventos aparecerão na Pesquisa. 

![](./media/sharepoint/09-search.png)

Se você estiver esperando mais dados, clique em Atualizar depois de alguns segundos.

## <a name="capturing-user-id"></a>Capturando a ID de usuário
O snippet de código de página da Web padrão não captura a ID de usuário do SharePoint, mas você pode fazer isso com uma pequena modificação.

1. Copie a chave de instrumentação do seu aplicativo da lista suspensa Essentials no Application Insights. 

    ![](./media/sharepoint/02-props.png)

1. Substitua a chave de instrumentação para 'XXXX' no snippet a seguir. 
2. Insira o script em seu aplicativo do SharePoint, em vez de inserir o snippet de código que você obtém do portal.

```


<SharePoint:ScriptLink ID="ScriptLink1" name="SP.js" runat="server" localizable="false" loadafterui="true" /> 
<SharePoint:ScriptLink ID="ScriptLink2" name="SP.UserProfiles.js" runat="server" localizable="false" loadafterui="true" /> 

<script type="text/javascript"> 
var personProperties; 

// Ensure that the SP.UserProfiles.js file is loaded before the custom code runs. 
SP.SOD.executeOrDelayUntilScriptLoaded(getUserProperties, 'SP.UserProfiles.js'); 

function getUserProperties() { 
    // Get the current client context and PeopleManager instance. 
    var clientContext = new SP.ClientContext.get_current(); 
    var peopleManager = new SP.UserProfiles.PeopleManager(clientContext); 

    // Get user properties for the target user. 
    // To get the PersonProperties object for the current user, use the 
    // getMyProperties method. 

    personProperties = peopleManager.getMyProperties(); 

    // Load the PersonProperties object and send the request. 
    clientContext.load(personProperties); 
    clientContext.executeQueryAsync(onRequestSuccess, onRequestFail); 
} 

// This function runs if the executeQueryAsync call succeeds. 
function onRequestSuccess() { 
var appInsights=window.appInsights||function(config){
function s(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},r=document,f=window,e="script",o=r.createElement(e),i,u;for(o.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js",r.getElementsByTagName(e)[0].parentNode.appendChild(o),t.cookie=r.cookie,t.queue=[],i=["Event","Exception","Metric","PageView","Trace"];i.length;)s("track"+i.pop());return config.disableExceptionTracking||(i="onerror",s("_"+i),u=f[i],f[i]=function(config,r,f,e,o){var s=u&&u(config,r,f,e,o);return s!==!0&&t["_"+i](config,r,f,e,o),s}),t
    }({
        instrumentationKey:"XXXX"
    });
    window.appInsights=appInsights;
    appInsights.trackPageView(document.title,window.location.href, {User: personProperties.get_displayName()});
} 

// This function runs if the executeQueryAsync call fails. 
function onRequestFail(sender, args) { 
} 
</script> 


```



## <a name="next-steps"></a>Próximas etapas
* [Testes da web](../../azure-monitor/app/monitor-web-app-availability.md) para monitorar a disponibilidade de seu site.
* [Application Insights](../../azure-monitor/app/app-insights-overview.md) para outros tipos de aplicativos.

<!--Link references-->


