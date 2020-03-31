---
title: Análise de tendências no Visual Studio | Microsoft Docs
description: Analisar, visualizar e explorar tendências em sua telemetria do Application Insights no Visual Studio.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 03/17/2017
ms.reviewer: mbullwin
ms.custom: vs-azure
ms.openlocfilehash: bb94d3596fde541f16edd1b7012f57b89ebf52eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670858"
---
# <a name="analyzing-trends-in-visual-studio"></a>Análise de tendências no Visual Studio
A ferramenta Application Insights Trends visualiza como eventos de telemetria importantes do seu aplicativo mudam com o tempo, ajudando a identificar rapidamente problemas e anomalias. Vinculando-o a informações mais detalhadas de diagnóstico, o Trends pode ajudá-lo a melhorar o desempenho de seu aplicativo, rastrear as causas de exceções e obter informações por meio de seus eventos personalizados.

![Janela de Tendências de exemplo](./media/visual-studio-trends/app-insights-trends-hero-750.png)

## <a name="configure-your-web-app-for-application-insights"></a>Configurar seu aplicativo Web para o Application Insights

Se você ainda não fez isso, [configure seu aplicativo Web para o Application Insights](../../azure-monitor/app/app-insights-overview.md). Isso permite que ele envie telemetria ao portal do Application Insights. A ferramenta Tendências lê a telemetria nesse local.

A ferramenta Tendências do Application Insights está disponível no Visual Studio 2015 atualização 3 e posteriores.

## <a name="open-application-insights-trends"></a>Abrir o Application Insights Trends
Para abrir a janela do Application Insights Trends:

* No botão de barra de ferramentas do Application Insights, escolha **Explorar Tendências de Telemetria**ou
* No menu de contexto do projeto, escolha **Application Insights > Explorar Tendências de Telemetria** ou
* Na barra de menu do Visual Studio, escolha **Exibir > Outras Janelas > Application Insights Trends**.

Você pode ver um prompt para selecionar um recurso. Clique em **Selecionar um recurso**, entre com uma assinatura do Azure e escolha um recurso do Application Insights na lista para o qual você gostaria de analisar tendências de telemetria.

## <a name="choose-a-trend-analysis"></a>Escolha uma análise de tendência
![Menu dos tipos comuns de análise de tendência](./media/visual-studio-trends/app-insights-trends-1-750.png)

Comece escolhendo uma das cinco análises de tendências comuns, cada uma das quais analisa dados das últimas 24 horas:

* **Investigar problemas de desempenho com as solicitações do servidor** - solicitações feitas ao serviço, agrupadas por tempos de resposta
* **Analisar erros nas suas solicitações de servidor** - solicitações feitas ao serviço, agrupadas por código de resposta HTTP
* **Examinar as exceções em seu aplicativo** - exceções de seu serviço, agrupados por tipo de exceção
* **Verificar o desempenho das dependências do aplicativo** - serviços chamados por seu serviço, agrupados por tempos de resposta
* **Inspecionar seus eventos personalizados** - eventos personalizados que você configurou para o serviço, agrupados por tipo de evento.

Essas análises pré-criadas estão disponíveis posteriormente usando o botão **Exibir tipos comuns de análise de telemetria** no canto superior esquerdo da janela de Tendências.

## <a name="visualize-trends-in-your-application"></a>Visualizar tendências em seu aplicativo
O Application Insights Trends cria uma visualização de série de tempo de telemetria de seu aplicativo. Cada visualização de série de tempo exibe um tipo de telemetria, agrupado por uma propriedade dessa telemetria, em algum intervalo de tempo. Por exemplo, talvez você queira exibir as solicitações ao servidor, agrupadas por país do qual foram originadas, nas últimas 24 horas. Neste exemplo, cada bolha ma visualização representaria uma contagem de solicitações do servidor para um país/região durante uma hora.

Use os controles na parte superior da janela para ajustar os tipos de telemetria a serem exibidos. Primeiro, escolha os tipos de telemetria em que você está interessado:

* **Tipo de telemetria** – solicitações ao servidor, exceções, dependências ou eventos personalizados
* **Intervalo de tempo** - em qualquer momento dos últimos 30 minutos até os últimos três dias
* **Agrupar por** - tipo de exceção, ID do problema, país/região e muito mais.

Em seguida, clique em **Analisar telemetria** para executar a consulta.

Para navegar entre as bolhas na visualização:

* Clique para selecionar uma bolha, o que atualiza os filtros na parte inferior da janela, resumindo apenas os eventos que ocorreram durante um período específico
* Clique duas vezes em uma bolha para navegar até a ferramenta de pesquisa e ver todos os eventos de telemetria individuais que ocorreram durante esse período
* Ctrl+clique uma bolha para anular a seleção na visualização.

> [!TIP]
> As ferramentas Tendências e Pesquisa trabalham juntas para ajudá-lo a identificar as causas dos problemas em seu serviço entre milhares de eventos de telemetria. Por exemplo, se uma tarde os clientes observarem que seu aplicativo está respondendo menos, comece com Tendências. Analise as solicitações feitas ao serviço nas últimas horas, agrupadas por tempo de resposta. Verifique se há um cluster excepcionalmente alto de solicitações lentas. Em seguida, clique duas vezes nessa bolha para ir para a ferramenta Pesquisa, com filtragem desses eventos de solicitação. Em Pesquisa, você pode explorar o conteúdo dessas solicitações e navegar até o código envolvido para resolver o problema.
> 
> 

## <a name="filter"></a>Filtrar
Descubra tendências mais específicas com os controles de filtro na parte inferior da janela. Para aplicar um filtro, clique em seu nome. Você pode alternar rapidamente entre diferentes filtros para descobrir tendências que podem estar ocultas em uma dimensão específica da telemetria. Se você aplicar um filtro em uma dimensão, como tipo de exceção, os filtros em outras dimensões permanecerão clicáveis mesmo que pareçam acinzentados. Para desaplicar um filtro, clique nele novamente. Ctrl+clique para selecionar vários filtros na mesma dimensão.

![Filtros de tendência](./media/visual-studio-trends/TrendsFiltering-750.png)

E se você desejar aplicar vários filtros? 

1. Aplique o filtro primeiro. 
2. Clique no botão **Aplicar filtros selecionados e consultar novamente** junto ao nome da dimensão do primeiro filtro. Isso consultará novamente a telemetria somente para os eventos que correspondem ao primeiro filtro. 
3. Aplique um segundo filtro. 
4. Repita o processo para identificar tendências em subconjuntos específicos da telemetria. Por exemplo, solicitações ao servidor denominadas "GET Home/Index" *e* que veio da Alemanha *e* que recebeu um código de resposta 500. 

Para cancelar a aplicação de um desses filtros, clique no botão **Remover filtros selecionados e consultar novamente** para a dimensão.

![Vários filtros](./media/visual-studio-trends/TrendsFiltering2-750.png)

## <a name="find-anomalies"></a>Encontrar anomalias
A ferramenta Tendências pode realçar bolhas de eventos anômalos em comparação com outras bolhas da mesma série de tempo. No menu suspenso Tipo de Exibição, escolha **Contagens no recipiente de tempo (realçar anomalias)** ou **Percentuais no recipiente de tempo (realçar anomalias)**. Bolhas vermelhas são anomalias. Anomalias são definidas como bolhas com contagens/percentuais que excedem 2,1 vezes o desvio padrão das contagens/percentuais que ocorreram nos últimos dois períodos (48 horas se você estiver exibindo as últimas 24 horas etc.).

![Pontos coloridos indicam anomalias](./media/visual-studio-trends/TrendsAnomalies-750.png)

> [!TIP]
> O realce de anomalias é particularmente útil para localizar exceções na série de tempo de bolhas pequenas que podem parecer ter o mesmo tamanho.  
> 
> 

## <a name="next-steps"></a><a name="next"></a>Próximas etapas
|  |  |
| --- | --- |
| **[Trabalhando com insights de aplicativos no Visual Studio](../../azure-monitor/app/visual-studio.md)**<br/>Pesquisar telemetria, ver dados em CodeLens e configurar o Application Insights. Tudo no Visual Studio. |![Clique com o botão direito no projeto e escolha Application Insights, Pesquisar.](./media/visual-studio-trends/34.png) |
| **[Adicionar mais dados](../../azure-monitor/app/asp-net-more.md)**<br/>Monitorar o uso, a disponibilidade, as dependências e as exceções. Integrar rastreamentos de estruturas de logs. Escrever telemetria personalizada. |![Visual Studio](./media/visual-studio-trends/64.png) |
| **[Trabalhando com o portal do Application Insights](../../azure-monitor/app/overview-dashboard.md)**<br/>Painéis, poderosas ferramentas de diagnóstico e análise, alertas, um mapa de dependências em tempo real de seu aplicativo e a exportação de telemetria. |![Visual Studio](./media/visual-studio-trends/62.png) |

