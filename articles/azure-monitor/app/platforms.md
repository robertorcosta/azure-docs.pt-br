---
title: 'Application Insights: linguagens, plataformas e integrações | Microsoft Docs'
description: Linguagens, plataformas e integrações disponíveis para Application Insights
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 07/18/2019
ms.reviewer: olegan
ms.openlocfilehash: 01cebc3a86808d549f1a7dc3adfd2883bc289076
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73061444"
---
# <a name="supported-languages"></a>Idiomas com suporte

* [C#|VB (.NET)](../../azure-monitor/app/asp-net.md)
* [Java](../../azure-monitor/app/java-get-started.md)
* [JavaScript](../../azure-monitor/app/javascript.md)
* [Node.JS](../../azure-monitor/app/nodejs.md)
* [Python (versão prévia)](../../azure-monitor/app/opencensus-python.md)

## <a name="supported-platforms-and-frameworks"></a>Plataformas e estruturas compatíveis

### <a name="instrumentation-for-already-deployed-applications-codeless-agent-based"></a>Instrumentação para aplicativos já implantados (sem código, baseados em agente)
* [Conjuntos de dimensionamento de máquinas virtuais do Azure e VM do Azure](../../azure-monitor/app/azure-vm-vmss-apps.md)
* [Serviço de aplicativo do Azure](../../azure-monitor/app/azure-web-apps.md)
* [ASP.NET – para aplicativos que já estejam ao vivo](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Serviços de nuvem do Azure](../../azure-monitor/app/cloudservices.md), incluindo funções Web e de trabalho
* [Funções do Azure](https://docs.microsoft.com/azure/azure-functions/functions-monitoring)
### <a name="instrumentation-through-code-sdks"></a>Instrumentação por meio de código (SDKs)
* [ASP.NET](../../azure-monitor/app/asp-net.md)
* [ASP.NET Core](../../azure-monitor/app/asp-net-core.md)
* [Android](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [iOS](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Java EE](../../azure-monitor/app/java-get-started.md)
* [Node.JS](https://www.npmjs.com/package/applicationinsights)
* [Python (versão prévia)](../../azure-monitor/app/opencensus-python.md)
* [Aplicativo Universal do Windows](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Funções de trabalho, serviços e aplicativos da área de trabalho do Windows](../../azure-monitor/app/windows-desktop.md)

## <a name="logging-frameworks"></a>Estruturas de registro em log
* [ILogger](https://docs.microsoft.com/azure/azure-monitor/app/ilogger)
* [Log4Net, NLog ou System.Diagnostics.Trace](../../azure-monitor/app/asp-net-trace-logs.md)
* [Java, Log4J ou Logback](../../azure-monitor/app/java-trace-logs.md)
* [Plug-in LogStash](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-output-applicationinsights)
* [Azure Monitor](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)

## <a name="export-and-data-analysis"></a>Análise de dados e exportação
* [Power BI](https://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx)
* [Análise de fluxo](../../azure-monitor/app/export-power-bi.md)

## <a name="unsupported-sdks"></a>SDKs sem suporte
Estamos cientes de que existem vários outros SDKs com suporte da Comunidade. No entanto, Azure Monitor só fornece suporte ao usar os SDKs com suporte listados nesta página. Estamos constantemente avaliando as oportunidades de expandir nosso suporte para outras linguagens, então siga nossa página de [anúncios do GitHub](https://github.com/microsoft/ApplicationInsights-Announcements/issues) para receber as notícias mais recentes do SDK. 
