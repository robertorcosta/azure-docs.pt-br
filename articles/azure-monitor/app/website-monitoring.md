---
title: 'Início Rápido: Monitorar sites com o Application Insights do Azure Monitor'
description: Neste início rápido, saiba como configurar o monitoramento do site do lado do cliente/navegador com o Application Insights do Azure Monitor.
ms.topic: quickstart
ms.date: 07/15/2019
ms.custom: mvc
ms.openlocfilehash: b47f3ce1ebed12d14dffd68e87dd013bb86218ea
ms.sourcegitcommit: c8a0fbfa74ef7d1fd4d5b2f88521c5b619eb25f8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82801630"
---
# <a name="quickstart-start-monitoring-your-website-with-azure-monitor-application-insights"></a>Início Rápido: Iniciar o monitoramento do seu site com o Application Insights do Azure Monitor

Neste início rápido, você aprenderá a adicionar o SDK do JavaScript do Application Insights de software livre ao seu site. Você também aprenderá a entender melhor a experiência do lado do cliente/navegador para os visitantes do seu site.

Com o Application Insights do Azure Monitor, você pode monitorar facilmente o site quanto à sua disponibilidade, desempenho e uso. Você também pode identificar e diagnosticar erros rapidamente em seu aplicativo sem esperar que um usuário os relate. O Application Insights fornece monitoramento do lado do servidor e funcionalidades de monitoramento do lado do cliente/navegador.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Um site ao qual você pode adicionar o SDK do JavaScript do Application Insights.

## <a name="enable-application-insights"></a>Habilitar o Application Insights

O Application Insights pode coletar dados de telemetria de qualquer aplicativo conectado à Internet, executado localmente ou na nuvem. Execute as seguinte etapas para ver esses dados:

1. Entre no [portal do Azure](https://portal.azure.com/).
1. Selecione **Criar um recurso** > **Ferramentas de Gerenciamento** > **Application Insights**.

   > [!NOTE]
   >Se esta for a primeira vez que você cria um recurso do Application Insights, confira [Criar um recurso do Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource).
1. Quando a caixa de configuração for exibida, use a seguinte tabela para preencher os campos de entrada:

    | Configurações        | Valor           | Descrição  |
   | ------------- |:-------------|:-----|
   | **Nome**      | Valor Globalmente Exclusivo | O nome que identifica o aplicativo que você está monitorando. |
   | **Grupo de recursos**     | myResourceGroup      | O nome do novo grupo de recursos no qual hospedar dados do Application Insights. é possível criar um grupo de recursos ou usar um grupo existente. |
   | **Localidade** | Leste dos EUA | Escolha uma localização perto de você ou de onde seu aplicativo está hospedado. |
1. Selecione **Criar**.

## <a name="create-an-html-file"></a>Criar um arquivo HTML

1. No computador local, crie um arquivo chamado ``hello_world.html``. Para este exemplo, crie o arquivo na raiz da unidade C para que fique semelhante a ``C:\hello_world.html``.
1. Copie e cole o seguinte script em ``hello_world.html``:

    ```html
    <!DOCTYPE html>
    <html>
    <head>
    <title>Azure Monitor Application Insights</title>
    </head>
    <body>
    <h1>Azure Monitor Application Insights Hello World!</h1>
    <p>You can use the Application Insights JavaScript SDK to perform client/browser-side monitoring of your website. To learn about more advanced JavaScript SDK configurations, visit the <a href="https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md" title="API Reference">API reference</a>.</p>
    </body>
    </html>
    ```

## <a name="configure-application-insights-sdk"></a>Configurar o SDK do Application Insights

1. Selecione **Visão Geral** > **Essentials** e copie a **Chave de Instrumentação** do aplicativo.

   ![Formulário Novo recurso do Application Insights](media/website-monitoring/instrumentation-key-001.png)

1. Adicione o seguinte script ao arquivo ``hello_world.html`` antes da marca de fechamento ``</head>``:

   ```javascript
   <script type="text/javascript">
      var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t}(
      {
         instrumentationKey:"INSTRUMENTATION_KEY"
      }
      );window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
   </script>
   ```

1. Edite ``hello_world.html`` e adicione a chave de instrumentação.

1. Abra ``hello_world.html`` em uma sessão do navegador local. Essa ação cria uma exibição de página única. Você pode atualizar o navegador para gerar várias exibições de página de teste.

## <a name="monitor-your-website-in-the-azure-portal"></a>Monitorar o site no portal do Azure

1. Reabra a página **Visão Geral** do Application Insights no portal do Azure para ver detalhes sobre o aplicativo em execução no momento. A página **Visão geral** é o local em que você recuperou sua chave de instrumentação.

   Os quatro gráficos padrão da página Visão geral limitam-se aos dados do aplicativo do lado do servidor. Como estamos instrumentando as interações do lado do cliente/navegador com o SDK do JavaScript, esta exibição específica não se aplica, a menos que também tenhamos um SDK do lado do servidor instalado.

1. Selecione **Análise** ![ícone do Mapa do aplicativo](media/website-monitoring/006.png).  Essa ação abre o **Analytics**, que fornece uma linguagem de consulta avançada para analisar todos os dados coletados pelo Application Insights. Para ver dados relacionados às solicitações de navegador do lado do cliente, execute a seguinte consulta:

    ```kusto
    // average pageView duration by name
    let timeGrain=1s;
    let dataset=pageViews
    // additional filters can be applied here
    | where timestamp > ago(15m)
    | where client_Type == "Browser" ;
    // calculate average pageView duration for all pageViews
    dataset
    | summarize avg(duration) by bin(timestamp, timeGrain)
    | extend pageView='Overall'
    // render result in a chart
    | render timechart
    ```

   ![Grafo de análise de solicitações de usuário durante um período de tempo](./media/website-monitoring/analytics-query.png)

1. Volte para a página **Visão geral**. Sob o cabeçalho **Investigar**, selecione **Navegador** e escolha **Desempenho**.  Métricas relacionadas ao desempenho do site serão exibidas. Há uma exibição correspondente para a análise de falhas e exceções em seu site. Você pode selecionar **Amostras** para acessar os [detalhes da transação de ponta a ponta](../../azure-monitor/app/transaction-diagnostics.md).

   ![Grafo de métricas do servidor](./media/website-monitoring/browser-performance.png)

1. No menu principal do Application Insights, sob o cabeçalho **Uso**, selecione [**Usuários**](../../azure-monitor/app/usage-segmentation.md) para começar a explorar as [ferramentas de análise de comportamento do usuário](../../azure-monitor/app/usage-overview.md). Como estamos testando em um só computador, veremos dados de apenas um usuário. Para um site ativo, a distribuição de usuários poderá ser semelhante a esta:

     ![Gráfico de usuário](./media/website-monitoring/usage-users.png)

1. Para um site mais complexo com várias páginas, use a ferramenta [**Fluxos dos Usuários**](../../azure-monitor/app/usage-flows.md) para acompanhar o caminho que os visitantes percorrem nas várias partes do site.

   ![Visualização de Fluxos dos Usuários](./media/website-monitoring/user-flows.png)

Para conhecer configurações mais avançadas do monitoramento de sites, confira a [referência da API do SDK de JavaScript](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md).

## <a name="clean-up-resources"></a>Limpar os recursos

Se planejar continuar trabalhando em outros inícios rápidos ou tutoriais, não limpe os recursos criados neste início rápido. Caso contrário, use as etapas a seguir para excluir todos os recursos criados por esse início rápido no Portal do Azure.

> [!NOTE]
> Se você tiver usado um grupo de recursos existente, as instruções a seguir não funcionarão. Em vez disso, apenas exclua o recurso individual do Application Insights. Tenha em mente que quando você exclui um grupo de recursos, todos os recursos subjacentes que são membros desse grupo também são excluídos.

1. No menu à esquerda no portal do Azure, selecione **Grupos de recursos** e, em seguida, escolha **myResourceGroup** ou o nome de seu grupo de recursos temporário.
1. Na página do grupo de recursos, selecione **Excluir**, insira **myResourceGroup** na caixa de texto e, em seguida, selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Localizar e diagnosticar problemas de desempenho](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)
