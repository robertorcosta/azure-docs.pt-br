---
title: Configurar análise de aplicativo Web do ASP.NET com o Azure Application Insights | Microsoft Docs
description: Configure ferramentas de análise de desempenho, de disponibilidade e do comportamento do usuário para seu site ASP.NET, hospedado localmente ou no Azure.
ms.topic: conceptual
ms.date: 05/08/2019
ms.openlocfilehash: d3181c3d43f07c7cb920b9fe265a8420c1417a56
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82145276"
---
# <a name="set-up-application-insights-for-your-aspnet-website"></a>Configurar o Application Insights para seu site ASP.NET

Este procedimento configura seu aplicativo da Web ASP.NET para enviar telemetria para o serviço [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md). Ele funciona para aplicativos ASP.NET hospedados em seu próprio servidor IIS local ou na nuvem. Você obtém gráficos e uma linguagem de consulta eficiente que ajudarão a compreender o desempenho de seu aplicativo e como as pessoas estão usando-o, além de alertas automáticos sobre falhas ou problemas de desempenho. Muitos desenvolvedores acham esses recursos excelentes como estão, mas você também pode estender e personalizar a telemetria se for necessário.

A instalação leva apenas alguns cliques no Visual Studio. Você tem a opção de evitar cobranças limitando o volume de telemetria. Essa funcionalidade permite que você teste e depure ou monitore um site sem muitos usuários. Quando você decidir que deseja prosseguir e monitorar seu site de produção, é fácil aumentar o limite mais tarde.

## <a name="prerequisites"></a>Pré-requisitos
Para adicionar o Application Insights ao seu site ASP.NET, você precisa:

- Instale o [Visual Studio 2019 para Windows](https://www.visualstudio.com/downloads/) com as seguintes cargas de trabalho:
    - ASP.NET e desenvolvimento para a Web (não desmarque os componentes opcionais)
    - Desenvolvimento do Azure

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="step-1-add-the-application-insights-sdk"></a><a name="ide"></a>Etapa 1: Adicionar o SDK do Application Insights

> [!IMPORTANT]
> As capturas de tela neste exemplo são baseadas no Visual Studio 2017 versão 15.9.9 e posterior. A experiência para adicionar Application Insights varia entre versões do Visual Studio, bem como pelo tipo de modelo ASP.NET. As versões mais antigas podem ter um texto alternativo, como "configurar Application Insights".

Clique com o botão direito do mouse no nome do aplicativo Web na Gerenciador de soluções e escolha **Adicionar** > **Application insights Telemetry**

![Captura de tela do Gerenciador de Soluções, com Configurar o Application Insights realçado](./media/asp-net/add-telemetry-new.png)

(Dependendo da versão de seu SDK do Application Insights, você pode receber uma solicitação para atualizar para a versão mais recente do SDK. Se isso acontecer, selecione **Atualizar SDK**.)

![Captura de tela: uma nova versão do SDK do Microsoft Application Insights está disponível. Atualizar SDK realçado](./media/asp-net/0002-update-sdk.png)

Tela de Configuração do Application Insights:

Selecione **introdução**.

![Captura de tela de registrar seu aplicativo com o Application Insights](./media/asp-net/00004-start-free.png)

Se você deseja definir o grupo de recursos ou o local onde os dados estão armazenado, clique **configurações**. Grupos de recursos são usados para controlar o acesso aos dados. Por exemplo, se você tiver vários aplicativos que fazem parte do mesmo sistema, você pode colocar seus dados do Application Insights no mesmo grupo de recursos.

 Selecione **Registrar**.

![Captura de tela de registrar seu aplicativo com o Application Insights](./media/asp-net/00005-register-ed.png)

 Selecione **projeto** > **gerenciar pacotes** > NuGet**origem do pacote: NuGet.org** > confirmar que você tem a versão estável mais recente do SDK do Application insights.

 Telemetria será enviada para o [portal do Azure](https://portal.azure.com), durante a depuração e depois de ter publicado seu aplicativo.
> [!NOTE]
> Se você não quiser enviar telemetria para o portal durante a depuração, adicione o SDK do Application Insights ao seu aplicativo, mas não configure um recurso no portal. Você pode ver a telemetria no Visual Studio enquanto você está depurando. Posteriormente, você pode retornar a esta página de configuração, ou você poderia esperar até depois de implantar seu aplicativo e [ative telemetria em tempo de execução](../../azure-monitor/app/monitor-performance-live-website-now.md).

## <a name="step-2-run-your-app"></a><a name="run"></a>Etapa 2: executar seu aplicativo
Execute o aplicativo com F5. Abra páginas diferentes para gerar alguma telemetria.

No Visual Studio, você verá uma contagem dos eventos que foram registrados.

![Captura de tela do Visual Studio. O botão Application Insights é exibido durante a depuração.](./media/asp-net/00006-Events.png)

## <a name="step-3-see-your-telemetry"></a>Etapa 3: conferir sua telemetria
Você pode ver sua telemetria no Visual Studio ou no portal da web Application Insights. Pesquise a telemetria no Visual Studio para ajudá-lo a depurar seu aplicativo. Monitore o desempenho e o uso no portal da Web quando o sistema estiver ativo. 

### <a name="see-your-telemetry-in-visual-studio"></a>Confira sua telemetria no Visual Studio

No Visual Studio, para ver os dados do Application Insights.  Selecione **Gerenciador de soluções** > **Serviços conectados** > clique com o botão direito do mouse em **Application insights**e clique em **Pesquisar telemetria ao vivo**.

Na janela de pesquisa do Application Insights do Visual Studio, você verá os dados de seu aplicativo para a telemetria gerada no lado do servidor de seu aplicativo. Experimente os filtros e clique em qualquer evento para ver mais detalhes.

![Captura de tela dos dados da exibição da sessão de depuração na janela do Application Insights.](./media/asp-net/55.png)

> [!Tip]
> Caso você não veja os dados, verifique se o intervalo de tempo está correto e clique no ícone Pesquisa.

[Saiba mais sobre as ferramentas do Application Insights no Visual Studio](../../azure-monitor/app/visual-studio.md).

<a name="monitor"></a>
### <a name="see-telemetry-in-web-portal"></a>Conferir telemetria no portal da Web

Você também pode ver a telemetria no portal da Web do Application Insights (a menos que você opte por instalar somente o SDK). O portal tem mais gráficos, ferramentas analíticas e modos de exibição entre componentes do que o Visual Studio. O portal também fornece alertas.

Abra seu recurso do Application Insights. Entre no [Portal do Azure](https://portal.azure.com/) e encontre-o lá, ou selecione **Gerenciador de Soluções** > **Serviços Conectados** > clique com o botão direito em ** Application Insights** > **Abrir Portal do Application Insights** e ele o levará até lá.

O portal é aberto em uma exibição da telemetria do aplicativo.

![Captura de tela da página de visão geral do Application Insights](./media/asp-net/007.png)

No portal, clique em qualquer bloco ou gráfico para ver mais detalhes.

## <a name="step-4-publish-your-app"></a>Etapa 4: Publicar seu aplicativo
Publica seu aplicativo no servidor IIS ou no Azure. Observe o [Fluxo de Métricas Ativo](../../azure-monitor/app/live-stream.md) para verificar se tudo está funcionando corretamente.

Sua telemetria se baseia no portal de Application Insights, onde você pode monitorar as métricas, pesquisar sua telemetria. Você também pode usar a poderosa [Linguagem de consulta do Kusto](/azure/kusto/query/) para analisar o uso e o desempenho ou para encontrar eventos específicos.

Você também pode continuar a analisar a telemetria no [Visual Studio](../../azure-monitor/app/visual-studio.md) com ferramentas como pesquisa de diagnóstico e de [tendências](../../azure-monitor/app/visual-studio-trends.md).

> [!NOTE]
> Se seu aplicativo enviar telemetria suficiente para se aproximar das [limitações](../../azure-monitor/app/pricing.md#limits-summary), a [amostragem](../../azure-monitor/app/sampling.md) automática será ativada. A amostragem reduz a quantidade de telemetria enviada do seu aplicativo, preservando dados correlacionados para fins de diagnóstico.
>
>

## <a name="youre-all-set"></a><a name="land"></a>Você está pronto

Parabéns! Você instalou o pacote do Application Insights em seu aplicativo e o configurou para enviar telemetria para o serviço Application Insights no Azure.

O recurso do Azure que recebe a telemetria do seu aplicativo é identificado por uma *chave de instrumentação*. Você encontrará essa chave no arquivo applicationinsights.config.


## <a name="upgrade-to-future-sdk-versions"></a>Atualizar para versões futuras do SDK
Para atualizar para uma [nova versão do SDK](https://github.com/Microsoft/ApplicationInsights-dotnet-server/releases), abra o **gerenciador de pacotes do NuGet** e filtre os pacotes instalados. Selecione **Microsoft.ApplicationInsights.Web** e escolha **Atualizar**.

Se você fez todas as personalizações no ApplicationInsights.config, salve uma cópia dele antes de atualizar. Em seguida, mescle suas alterações para a nova versão.

## <a name="next-steps"></a>Próximas etapas

Há tópicos alternativos para conferir se você está interessado em:

* [Instrumentar um aplicativo Web em runtime](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Serviços de nuvem do Azure](../../azure-monitor/app/cloudservices.md)

### <a name="more-telemetry"></a>Mais telemetria

* **[Navegador e página carregam dados](../../azure-monitor/app/javascript.md)** -insira um snippet de código em suas páginas da Web.
* **[Obtenha monitoramento de dependência e de exceção mais detalhado](../../azure-monitor/app/monitor-performance-live-website-now.md)** -instale o Monitor de Status no seu servidor.
* **[Codifique eventos personalizados](../../azure-monitor/app/api-custom-events-metrics.md)** para contagem, tempo ou medidas de ações do usuário.
* **[Obter dados de log](../../azure-monitor/app/asp-net-trace-logs.md)** - correlacionar dados de log com a telemetria.

### <a name="analysis"></a>Análise

* **[Trabalhando com Application Insights no Visual Studio](../../azure-monitor/app/visual-studio.md)**<br/>Inclui informações sobre a depuração de telemetria, pesquisa de diagnóstico e análise por meio de código.
* **[Analytics](../../azure-monitor/log-query/get-started-portal.md)** - a linguagem de consulta poderosa.

### <a name="alerts"></a>Alertas

* [Testes de disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md): criar testes para verificar se seu site está visível na web.
* [Inteligente diagnóstico](../../azure-monitor/app/proactive-diagnostics.md): esses testes são executados automaticamente, portanto você não precisa fazer nada para configurá-los. Eles informam se o aplicativo tem uma taxa incomum de solicitações com falha.
* [Alertas de métrica](../../azure-monitor/app/alerts.md): Defina alertas para avisá-lo se uma métrica ultrapassar um limite. Você pode defini-los em métricas personalizadas que você codifica em seu aplicativo.

### <a name="automation"></a>Automação

* [Automatizar a criação de um recurso adicional do Application Insights](../../azure-monitor/app/powershell.md)
