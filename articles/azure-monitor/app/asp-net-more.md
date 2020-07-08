---
title: Obtenha mais do Azure Application Insights | Microsoft Docs
description: Após começar a usar o Application Insights, veja um resumo dos recursos que você pode explorar.
ms.topic: conceptual
ms.date: 02/03/2017
ms.openlocfilehash: 3c4a9a736f34d4f7dbfeb004d0837f1f2efa55dc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "77666017"
---
# <a name="more-telemetry-from-application-insights"></a>Mais telemetria do Application Insights
Depois que você [adiciona o Application Insights a seu código ASP.NET](../../azure-monitor/app/asp-net.md), existem algumas coisas que pode fazer para obter ainda mais telemetria. 

| Ação | O que você obtém|
|---|---|
|(Servidores IIS) [Instale o Monitor de Status](https://go.microsoft.com/fwlink/?LinkId=506648) em cada máquina do servidor.<br/>(Aplicativos Web do Azure) No painel de controle do Azure para o aplicativo Web, abra a folha Application Insights.| [**Contadores de desempenho**](../../azure-monitor/app/performance-counters.md)<br/>[**Exceções**](asp-net-exceptions.md) - rastreamentos de pilha detalhados<br/>[**Dependências**](../../azure-monitor/app/asp-net-dependencies.md)|
|[Adicionar o snippet de JavaScript às suas páginas da Web](../../azure-monitor/app/javascript.md)|[Desempenho da página](../../azure-monitor/app/usage-overview.md), exceções do navegador, desempenho do AJAX. Telemetria personalizada do lado do cliente.|
|[Criar testes de disponibilidade na Web](../../azure-monitor/app/monitor-web-app-availability.md)|Obter alertas se o seu site ficar indisponível|
|[Certificar-se de que buildinfo.config](https://msdn.microsoft.com/library/dn449058.aspx) seja gerado pelo MSBuild|[Criar anotações em gráficos de métricas](https://docs.microsoft.com/azure/azure-monitor/app/annotations)
|[Gravar eventos e métricas personalizadas](../../azure-monitor/app/api-custom-events-metrics.md)|Contagem de métricas e eventos de negócios, acompanhar o uso detalhado e muito mais.|
|[Perfil de seu site ativo](https://aka.ms/AIProfilerPreview)|Intervalos de função detalhada de seu aplicativo Web ativo|






