---
title: 'Insights de aplicativos: idiomas, plataformas e integrações | Microsoft Docs'
description: Idiomas, plataformas e integrações disponíveis para insights de aplicativos
ms.topic: conceptual
ms.date: 07/18/2019
ms.reviewer: olegan
ms.openlocfilehash: cbda252fece638393ee7757dc165c4b009a0195e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136716"
---
# <a name="supported-languages"></a>Idiomas com suporte

* [C#|VB (.NET)](../../azure-monitor/app/asp-net.md)
* [Java](../../azure-monitor/app/java-get-started.md)
* [Javascript](../../azure-monitor/app/javascript.md)
* [Node.js](../../azure-monitor/app/nodejs.md)
* [Python](../../azure-monitor/app/opencensus-python.md)

## <a name="supported-platforms-and-frameworks"></a>Plataformas e estruturas compatíveis

### <a name="instrumentation-for-already-deployed-applications-codeless-agent-based"></a>Instrumentação para aplicativos já implantados (sem código, baseado em agentes)
* [Conjuntos de escalade máquinas virtuais Azure VM e Azure](../../azure-monitor/app/azure-vm-vmss-apps.md)
* [Serviço de aplicativo do Azure](../../azure-monitor/app/azure-web-apps.md)
* [ASP.NET – para aplicativos que já estejam ao vivo](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Azure Cloud Services](../../azure-monitor/app/cloudservices.md), incluindo funções web e trabalhador
* [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-monitoring)
### <a name="instrumentation-through-code-sdks"></a>Instrumentação através de código (SDKs)
* [ASP.NET](../../azure-monitor/app/asp-net.md)
* [ASP.NET Core](../../azure-monitor/app/asp-net-core.md)
* [Android](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [iOS](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Java EE](../../azure-monitor/app/java-get-started.md)
* [Node.js](https://www.npmjs.com/package/applicationinsights)
* [Python](../../azure-monitor/app/opencensus-python.md)
* [Aplicativo Universal do Windows](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Funções de trabalho, serviços e aplicativos da área de trabalho do Windows](../../azure-monitor/app/windows-desktop.md)

## <a name="logging-frameworks"></a>Estruturas de registro em log
* [ILogger](https://docs.microsoft.com/azure/azure-monitor/app/ilogger)
* [Log4Net, NLog ou System.Diagnostics.Trace](../../azure-monitor/app/asp-net-trace-logs.md)
* [Java, Log4J ou Logback](../../azure-monitor/app/java-trace-logs.md)
* [Plug-in LogStash](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-output-applicationinsights)
* [Azure Monitor](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)

## <a name="export-and-data-analysis"></a>Exportação e análise de dados
* [Bi de Potência](https://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx)
* [Análise de fluxo](../../azure-monitor/app/export-power-bi.md)

## <a name="unsupported-sdks"></a>SDKs sem suporte
Sabemos que existem vários outros SDKs apoiados pela comunidade. No entanto, o Azure Monitor só fornece suporte ao usar os SDKs suportados listados nesta página. Estamos constantemente avaliando oportunidades para expandir nosso suporte para outros idiomas, então siga nossa página [de Anúncios do GitHub](https://github.com/microsoft/ApplicationInsights-Announcements/issues) para receber as últimas notícias do SDK. 
