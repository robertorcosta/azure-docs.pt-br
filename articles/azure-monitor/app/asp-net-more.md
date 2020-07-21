---
title: Obtenha mais do Azure Application Insights | Microsoft Docs
description: Após começar a usar o Application Insights, veja um resumo dos recursos que você pode explorar.
ms.topic: conceptual
ms.date: 02/03/2017
ms.openlocfilehash: 6d30833840b62412ae03c761baacf54c939c9d33
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86540019"
---
# <a name="more-telemetry-from-application-insights"></a>Mais telemetria do Application Insights
Depois que você [adiciona o Application Insights a seu código ASP.NET](../../azure-monitor/app/asp-net.md), existem algumas coisas que pode fazer para obter ainda mais telemetria. 

| Ação | O que você obtém|
|---|---|
|(Servidores IIS) [Instale o Monitor de Status](https://go.microsoft.com/fwlink/?LinkId=506648) em cada máquina do servidor.<br/>(Aplicativos Web do Azure) No painel de controle do Azure para o aplicativo Web, abra a folha Application Insights.| [**Contadores de desempenho**](../../azure-monitor/app/performance-counters.md)<br/>[**Exceções**](asp-net-exceptions.md) - rastreamentos de pilha detalhados<br/>[**Dependências**](../../azure-monitor/app/asp-net-dependencies.md)|
|[Adicionar o snippet de JavaScript às suas páginas da Web](../../azure-monitor/app/javascript.md)|[Desempenho da página](../../azure-monitor/app/usage-overview.md), exceções do navegador, desempenho do AJAX. Telemetria personalizada do lado do cliente.|
|[Criar testes de disponibilidade na Web](../../azure-monitor/app/monitor-web-app-availability.md)|Obter alertas se o seu site ficar indisponível|
|[Certificar-se de que buildinfo.config](/visualstudio/debugger/diagnose-problems-after-deployment?view=vs-2015) seja gerado pelo MSBuild|[Criar anotações em gráficos de métricas](./annotations.md)
|[Gravar eventos e métricas personalizadas](../../azure-monitor/app/api-custom-events-metrics.md)|Contagem de métricas e eventos de negócios, acompanhar o uso detalhado e muito mais.|
|[Perfil de seu site ativo](https://aka.ms/AIProfilerPreview)|Intervalos de função detalhada de seu aplicativo Web ativo|
