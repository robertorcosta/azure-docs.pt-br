---
title: Configurar a análise de aplicativo Web para ASP.NET com insights Aplicativo Azure | Microsoft Docs
description: Configure as ferramentas de desempenho, disponibilidade e análise de comportamento do usuário para seu site do ASP.NET, hospedado localmente ou no Azure.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/08/2019
ms.openlocfilehash: a72bb5dd02776fe8410bb515e4e17a292d12048f
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72677665"
---
# <a name="set-up-application-insights-for-your-aspnet-website"></a>Configurar Application Insights para seu site do ASP.NET

Este procedimento configura seu aplicativo Web ASP.NET para enviar telemetria para o serviço de [informações de aplicativo Azure](../../azure-monitor/app/app-insights-overview.md) . Ele funciona para aplicativos ASP.NET hospedados em seu próprio servidor IIS local ou na nuvem. Você obtém gráficos e uma linguagem de consulta avançada que o ajudará a entender o desempenho do seu aplicativo e como as pessoas o estão usando, além de alertas automáticos sobre falhas ou problemas de desempenho. Muitos desenvolvedores acham esses recursos excelentes como estão, mas você também pode estender e personalizar a telemetria se precisar.

A instalação leva apenas alguns cliques no Visual Studio. Você tem a opção de evitar cobranças limitando o volume de telemetria. Essa funcionalidade permite que você teste e depure ou monitore um site sem muitos usuários. Quando você decide que deseja continuar e monitorar seu site de produção, é fácil aumentar o limite posteriormente.

## <a name="prerequisites"></a>Pré-requisitos
Para adicionar Application Insights ao seu site do ASP.NET, você precisa:

- Instale o [Visual Studio 2019 para Windows](https://www.visualstudio.com/downloads/) com as seguintes cargas de trabalho:
    - ASP.NET e desenvolvimento para a Web (não desmarque os componentes opcionais)
    - Desenvolvimento do Azure

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="ide"></a>Etapa 1: Adicionar o SDK do Application Insights

> [!IMPORTANT]
> As capturas de tela neste exemplo são baseadas no Visual Studio 2017 versão 15.9.9 e posterior. A experiência para adicionar Application Insights varia entre versões do Visual Studio, bem como pelo tipo de modelo ASP.NET. As versões mais antigas podem ter um texto alternativo, como "configurar Application Insights".

Clique com o botão direito do mouse no nome do aplicativo Web na Gerenciador de Soluções e escolha **adicionar**  > **Application insights Telemetry**

![Captura de tela de Gerenciador de Soluções, com configurar Application Insights realçado](./media/asp-net/add-telemetry-new.png)

(Dependendo da versão do SDK do Application Insights, você poderá ser solicitado a atualizar para a versão mais recente do SDK. Se solicitado, selecione **Atualizar SDK**.)

![Captura de tela: uma nova versão do SDK do Microsoft Application Insights está disponível. Atualização do SDK realçada](./media/asp-net/0002-update-sdk.png)

Tela de configuração do Application Insights:

Selecione **introdução**.

![Captura de tela de registrar seu aplicativo com Application Insights página](./media/asp-net/00004-start-free.png)

Se você quiser definir o grupo de recursos ou o local onde os dados estão armazenados, clique em **definir configurações**. Os grupos de recursos são usados para controlar o acesso aos dados. Por exemplo, se você tiver vários aplicativos que formam parte do mesmo sistema, poderá colocar seus Application Insights dados no mesmo grupo de recursos.

 Selecione **registrar**.

![Captura de tela de registrar seu aplicativo com Application Insights página](./media/asp-net/00005-register-ed.png)

 Selecione **projeto**  > **gerenciar pacotes NuGet**  > **origem do pacote: NuGet.org** > confirmar que você tem a versão estável mais recente do SDK do Application insights.

 Telemetria será enviada para o [portal do Azure](https://portal.azure.com), durante a depuração e depois de ter publicado seu aplicativo.
> [!NOTE]
> Se você não quiser enviar telemetria ao portal enquanto estiver Depurando, basta adicionar o SDK do Application Insights ao seu aplicativo, mas não configurar um recurso no Portal. Você pode ver a telemetria no Visual Studio enquanto estiver depurando. Posteriormente, você pode retornar a esta página de configuração ou pode esperar até que tenha implantado seu aplicativo e [alternar a telemetria em tempo de execução](../../azure-monitor/app/monitor-performance-live-website-now.md).

## <a name="run"></a>Etapa 2: executar seu aplicativo
Execute seu aplicativo com F5. Abra páginas diferentes para gerar uma telemetria.

No Visual Studio, você verá uma contagem dos eventos que foram registrados.

![Captura de tela do Visual Studio. O botão de Application Insights mostra durante a depuração.](./media/asp-net/00006-Events.png)

## <a name="step-3-see-your-telemetry"></a>Etapa 3: ver sua telemetria
Você pode ver sua telemetria no Visual Studio ou no portal da web Application Insights. Pesquise a telemetria no Visual Studio para ajudá-lo a depurar seu aplicativo. Monitore o desempenho e o uso no portal da Web quando o seu sistema estiver ativo. 

### <a name="see-your-telemetry-in-visual-studio"></a>Veja sua telemetria no Visual Studio

No Visual Studio, para exibir dados de Application Insights.  Selecione **Gerenciador de Soluções**  > **Serviços conectados** > clique com o botão direito do mouse **Application insights**e clique em **Pesquisar telemetria ao vivo**.

Na janela de pesquisa do Visual Studio Application Insights, você verá os dados do seu aplicativo para a telemetria gerada no lado do servidor do seu aplicativo. Experimente os filtros e clique em qualquer evento para ver mais detalhes.

![Captura de tela dos dados da exibição da sessão de depuração na janela Application Insights.](./media/asp-net/55.png)

> [!Tip]
> Se você não vir nenhum dado, verifique se o intervalo de tempo está correto e clique no ícone de pesquisa.

[Saiba mais sobre as ferramentas do Application Insights no Visual Studio](../../azure-monitor/app/visual-studio.md).

<a name="monitor"></a>
### <a name="see-telemetry-in-web-portal"></a>Consulte telemetria no portal da Web

Você também pode ver a telemetria no portal da Web do Application Insights (a menos que tenha optado por instalar apenas o SDK). O portal tem mais gráficos, ferramentas analíticas e exibições entre componentes do que o Visual Studio. O portal também fornece alertas.

Abra seu recurso do Application Insights. Entre no [portal do Azure](https://portal.azure.com/) e encontre-o lá ou selecione **Gerenciador de soluções**  > **Serviços conectados** > clique com o botão direito do mouse **Application insights**  > **abrir o Portal Application insights** e deixá-lo levá-lo existi.

O portal é aberto em uma exibição da telemetria do aplicativo.

![Captura de tela da página de visão geral do Application Insights](./media/asp-net/007.png)

No portal, clique em qualquer bloco ou gráfico para ver mais detalhes.

## <a name="step-4-publish-your-app"></a>Etapa 4: publicar seu aplicativo
Publique seu aplicativo no servidor IIS ou no Azure. Observe o [Fluxo de Métricas Ativo](../../azure-monitor/app/metrics-explorer.md#live-metrics-stream) para verificar se tudo está funcionando corretamente.

Sua telemetria se baseia no portal de Application Insights, onde você pode monitorar as métricas, pesquisar sua telemetria. Você também pode usar a [linguagem de consulta Kusto](/azure/kusto/query/) poderosa para analisar o uso e o desempenho ou para encontrar eventos específicos.

Você também pode continuar a analisar sua telemetria no [Visual Studio](../../azure-monitor/app/visual-studio.md), com ferramentas como pesquisa de diagnóstico e [tendências](../../azure-monitor/app/visual-studio-trends.md).

> [!NOTE]
> Se seu aplicativo enviar telemetria suficiente para abordar os [limites de limitação](../../azure-monitor/app/pricing.md#limits-summary), os comutadores de [amostragem](../../azure-monitor/app/sampling.md) automáticos no. A amostragem reduz a quantidade de telemetria enviada do seu aplicativo, preservando dados correlacionados para fins de diagnóstico.
>
>

## <a name="land"></a>Você está pronto

Parabéns! Você instalou o pacote de Application Insights em seu aplicativo e o configurou para enviar telemetria para o serviço de Application Insights no Azure.

O recurso do Azure que recebe a telemetria do aplicativo é identificado por uma *chave de instrumentação*. Você encontrará essa chave no arquivo ApplicationInsights. config.


## <a name="upgrade-to-future-sdk-versions"></a>Atualizar para versões futuras do SDK
Para atualizar para uma [nova versão do SDK](https://github.com/Microsoft/ApplicationInsights-dotnet-server/releases), abra o **Gerenciador de pacotes NuGet**e filtre os pacotes instalados. Selecione **Microsoft. ApplicationInsights. Web**e escolha **Atualizar**.

Se você tiver feito personalizações em ApplicationInsights. config, salve uma cópia dela antes de atualizar. Em seguida, mescle as alterações na nova versão.

## <a name="video"></a>Vídeo

* Vídeo passo a passo externo sobre como [configurar Application insights com um aplicativo .net do zero](https://www.youtube.com/watch?v=blnGAVgMAfA).

## <a name="next-steps"></a>Próximos passos

Há tópicos alternativos a serem examinados se você estiver interessado em:

* [Instrumentação de um aplicativo Web em tempo de execução](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Serviços de nuvem do Azure](../../azure-monitor/app/cloudservices.md)

### <a name="more-telemetry"></a>Mais telemetria

* **[Navegador e dados de carregamento de página](../../azure-monitor/app/javascript.md)** – Insira um trecho de código em suas páginas da Web.
* **[Obtenha uma dependência mais detalhada e monitoramento de exceção](../../azure-monitor/app/monitor-performance-live-website-now.md)** -instale status monitor em seu servidor.
* **[Codifique eventos personalizados](../../azure-monitor/app/api-custom-events-metrics.md)** para contagem, tempo ou medidas de ações do usuário.
* **[Obter dados de log](../../azure-monitor/app/asp-net-trace-logs.md)** -correlacionar dados de log com sua telemetria.

### <a name="analysis"></a>Análise

* **[Trabalhando com o Application Insights no Visual Studio](../../azure-monitor/app/visual-studio.md)**<br/>Inclui informações sobre a depuração com telemetria, pesquisa de diagnóstico e Drill-through para código.
* **[Análise](../../azure-monitor/log-query/get-started-portal.md)** -a linguagem de consulta avançada.

### <a name="alerts"></a>Alertas

* [Testes de disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md): criar testes para verificar se seu site está visível na web.
* [Diagnóstico inteligente](../../azure-monitor/app/proactive-diagnostics.md): esses testes são executados automaticamente, de modo que você não precisa fazer nada para configurá-los. Eles informam se seu aplicativo tem uma taxa incomum de solicitações com falha.
* [Alertas de métrica](../../azure-monitor/app/alerts.md): Defina alertas para avisá-lo se uma métrica ultrapassar um limite. Você pode defini-los em métricas personalizadas que você codifica em seu aplicativo.

### <a name="automation"></a>Automação

* [Automatizar a criação de um recurso de Application Insights](../../azure-monitor/app/powershell.md)
