---
title: Configurar análise de aplicativo Web do ASP.NET com o Azure Application Insights | Microsoft Docs
description: Configure ferramentas de análise de desempenho, de disponibilidade e do comportamento do usuário para seu site ASP.NET, hospedado localmente ou no Azure.
ms.topic: conceptual
ms.date: 05/08/2019
ms.openlocfilehash: 4e51fb43a9e088c991af2132b6a5d2257da8d78a
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87324209"
---
# <a name="set-up-application-insights-for-your-aspnet-website"></a>Configurar o Application Insights para seu site ASP.NET

Este procedimento configura seu aplicativo da Web ASP.NET para enviar telemetria para o serviço [Azure Application Insights](./app-insights-overview.md). Ele funciona para aplicativos ASP.NET hospedados em seu próprio servidor IIS local ou na nuvem. Você obtém gráficos e uma linguagem de consulta eficiente que ajudarão a compreender o desempenho de seu aplicativo e como as pessoas estão usando-o, além de alertas automáticos sobre falhas ou problemas de desempenho. Muitos desenvolvedores acham esses recursos excelentes como estão, mas você também pode estender e personalizar a telemetria se for necessário.

A instalação leva apenas alguns cliques no Visual Studio. Você tem a opção de evitar cobranças limitando o volume de telemetria. Esta funcionalidade permite experimentar e depurar, ou monitorar um site com poucos usuários. Quando você decidir que deseja prosseguir e monitorar seu site de produção, é fácil aumentar o limite mais tarde.

## <a name="prerequisites"></a>Pré-requisitos
Para adicionar o Application Insights ao seu site ASP.NET, você precisa:

- Instale o [Visual Studio 2019 para Windows](https://www.visualstudio.com/downloads/) com as cargas de trabalho a seguir:
    - Desenvolvimento Web e ASP.NET (não desmarque os componentes opcionais)
    - Desenvolvimento do Azure

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="step-1-add-the-application-insights-sdk"></a><a name="ide"></a> Etapa 1: Adicionar o SDK do Application Insights

> [!IMPORTANT]
> As capturas de tela neste exemplo são baseadas no Visual Studio 2017 versão 15.9.9 e posterior. Em outras versões do Visual Studio e dependendo do tipo de modelo ASP.NET, a experiência de adicionar o Application Insights será diferente. Versões mais antigas podem ter textos alternativos, como “Configurar o Application Insights”.

Clique com o botão direito no nome de seu aplicativo Web no Gerenciador de Soluções e escolha **Adicionar** > **Telemetria do Application Insights**

![Captura de tela do Gerenciador de Soluções, com Configurar o Application Insights realçado](./media/asp-net/add-telemetry-new.png)

(Dependendo da versão de seu SDK do Application Insights, você pode receber uma solicitação para atualizar para a versão mais recente do SDK. Se isso acontecer, selecione **Atualizar SDK**.)

![Captura de tela: Uma nova versão do SDK do Microsoft Application Insights está disponível. Atualizar SDK realçado](./media/asp-net/0002-update-sdk.png)

Tela de Configuração do Application Insights:

Selecione **Introdução**.

![Captura de tela de registrar seu aplicativo com o Application Insights](./media/asp-net/00004-start-free.png)

Se você deseja definir o grupo de recursos ou o local onde os dados estão armazenado, clique **configurações**. Grupos de recursos são usados para controlar o acesso aos dados. Por exemplo, se você tiver vários aplicativos que fazem parte do mesmo sistema, você pode colocar seus dados do Application Insights no mesmo grupo de recursos.

 Selecione **Registrar**.

![Captura de tela de registrar seu aplicativo com o Application Insights](./media/asp-net/00005-register-ed.png)

 Selecione **Projeto** > **Gerenciar pacotes do NuGet** > **Origem do pacote: nuget.org** > Confirme que você tem a versão estável mais recente do SKD do Application Insights.

 Telemetria será enviada para o [portal do Azure](https://portal.azure.com), durante a depuração e depois de ter publicado seu aplicativo.
> [!NOTE]
> Se você não quiser enviar telemetria para o portal durante a depuração, adicione o SDK do Application Insights ao seu aplicativo, mas não configure um recurso no portal. Você pode ver a telemetria no Visual Studio enquanto você está depurando. Posteriormente, você pode retornar a esta página de configuração, ou você poderia esperar até depois de implantar seu aplicativo e [ative telemetria em tempo de execução](./status-monitor-v2-overview.md).

## <a name="step-2-run-your-app"></a><a name="run"></a> Etapa 2: Executar seu aplicativo
Execute o aplicativo com F5. Abra páginas diferentes para gerar alguma telemetria.

No Visual Studio, você verá uma contagem dos eventos que foram registrados.

![Captura de tela do Visual Studio. O botão Application Insights é exibido durante a depuração.](./media/asp-net/00006-Events.png)

## <a name="step-3-see-your-telemetry"></a>Etapa 3: Consulte a telemetria
Você pode ver sua telemetria no Visual Studio ou no portal da web Application Insights. Pesquise a telemetria no Visual Studio para ajudá-lo a depurar seu aplicativo. Monitore o desempenho e o uso no portal da Web quando o sistema estiver ativo. 

### <a name="see-your-telemetry-in-visual-studio"></a>Confira sua telemetria no Visual Studio

No Visual Studio, para ver os dados do Application Insights.  Selecione **Gerenciador de Soluções** > **Serviços Conectados** > clique com o botão direito em **Application Insights** e, em seguida, clique em **Pesquisar Telemetria em Tempo Real**.

Na janela de pesquisa do Application Insights do Visual Studio, você verá os dados de seu aplicativo para a telemetria gerada no lado do servidor de seu aplicativo. Experimente os filtros e clique em qualquer evento para ver mais detalhes.

![Captura de tela dos dados da exibição da sessão de depuração na janela do Application Insights.](./media/asp-net/55.png)

> [!Tip]
> Caso você não veja os dados, verifique se o intervalo de tempo está correto e clique no ícone Pesquisa.

[Saiba mais sobre as ferramentas do Application Insights no Visual Studio](./visual-studio.md).

<a name="monitor"></a>
### <a name="see-telemetry-in-web-portal"></a>Conferir telemetria no portal da Web

Você também pode ver a telemetria no portal da Web do Application Insights (a menos que você opte por instalar somente o SDK). O portal tem mais gráficos, ferramentas analíticas e modos de exibição entre componentes do que o Visual Studio. O portal também fornece alertas.

Abra seu recurso do Application Insights. Entre no [Portal do Azure](https://portal.azure.com/) e encontre-o lá, ou selecione **Gerenciador de Soluções** > **Serviços Conectados** > clique com o botão direito em  **Application Insights** > **Abrir Portal do Application Insights** e ele o levará até lá.

O portal é aberto em uma exibição da telemetria do aplicativo.

![Captura de tela da página de visão geral do Application Insights](./media/asp-net/007.png)

No portal, clique em qualquer bloco ou gráfico para ver mais detalhes.

## <a name="step-4-publish-your-app"></a>Etapa 4: Publicar seu aplicativo
Publica seu aplicativo no servidor IIS ou no Azure. Observe o [Fluxo de Métricas Ativo](./live-stream.md) para verificar se tudo está funcionando corretamente.

A telemetria se acumula no portal do Application Insights, onde você pode monitorar as métricas, pesquisar a telemetria. Você também pode usar a poderosa [Linguagem de consulta do Kusto](/azure/kusto/query/) para analisar o uso e o desempenho ou para encontrar eventos específicos.

Você também pode continuar a analisar a telemetria no [Visual Studio](./visual-studio.md) com ferramentas como pesquisa de diagnóstico e de [tendências](./visual-studio-trends.md).

> [!NOTE]
> Se seu aplicativo enviar telemetria suficiente para se aproximar das [limitações](./pricing.md#limits-summary), a [amostragem](./sampling.md) automática será ativada. A amostragem reduz a quantidade de telemetria enviada do seu aplicativo, preservando dados correlacionados para fins de diagnóstico.
>
>

## <a name="youre-all-set"></a><a name="land"></a> Você está pronto

Parabéns! Você instalou o pacote do Application Insights em seu aplicativo e o configurou para enviar telemetria para o serviço Application Insights no Azure.

O recurso do Azure que recebe a telemetria do seu aplicativo é identificado por uma *chave de instrumentação*. Você encontrará essa chave no arquivo applicationinsights.config.


## <a name="upgrade-to-future-sdk-versions"></a>Atualizar para versões futuras do SDK
Para atualizar para uma [nova versão do SDK](https://github.com/Microsoft/ApplicationInsights-dotnet-server/releases), abra o **gerenciador de pacotes do NuGet** e filtre os pacotes instalados. Selecione **Microsoft.ApplicationInsights.Web** e escolha **Atualizar**.

Se você fez todas as personalizações no ApplicationInsights.config, salve uma cópia dele antes de atualizar. Em seguida, mescle suas alterações para a nova versão.

## <a name="next-steps"></a>Próximas etapas

Há tópicos alternativos para conferir se você está interessado em:

* [Instrumentar um aplicativo Web em runtime](./monitor-performance-live-website-now.md)
* [Serviços de Nuvem do Azure](./cloudservices.md)

### <a name="more-telemetry"></a>Mais telemetria

* **[Navegador e página carregam dados](./javascript.md)** -insira um snippet de código em suas páginas da Web.
* **[Obtenha monitoramento de dependência e de exceção mais detalhado](./monitor-performance-live-website-now.md)** -instale o Monitor de Status no seu servidor.
* **[Eventos personalizados de código](./api-custom-events-metrics.md)** para contagem, tempo ou medição de ações do usuário.
* **[Obter dados de log](./asp-net-trace-logs.md)** - correlacionar dados de log com a telemetria.

### <a name="analysis"></a>Análise

* **[Trabalhar com o Application Insights no Visual Studio](./visual-studio.md)**<br/>Inclui informações sobre a depuração de telemetria, pesquisa de diagnóstico e análise por meio de código.
* **[Analytics](../log-query/get-started-portal.md)** - a linguagem de consulta poderosa.

### <a name="alerts"></a>Alertas

* [Testes de disponibilidade](./monitor-web-app-availability.md): Crie testes para garantir que o site esteja visível na Web.
* [Diagnóstico inteligente](./proactive-diagnostics.md): Esses testes são executados automaticamente, portanto, nenhuma configuração adicional será necessária. Eles informam se o aplicativo tem uma taxa incomum de solicitações com falha.
* [Alertas de métrica](../platform/alerts-log.md): Definir alertas para avisar se uma métrica ultrapassar um limite. Você pode defini-los em métricas personalizadas que você codifica em seu aplicativo.

### <a name="automation"></a>Automação

* [Automatizar a criação de um recurso adicional do Application Insights](./powershell.md)

