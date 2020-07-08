---
title: Monitorar aplicativos no serviço Kubernetes do Azure (AKS) com Application Insights – Azure Monitor | Microsoft Docs
description: O Azure Monitor integra-se perfeitamente ao seu aplicativo em execução no Kubernetes e permite que você identifique os problemas com seus aplicativos rapidamente.
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 05/13/2020
ms.openlocfilehash: 76f9f922697ef6be6c959ea7f9bafd0872dba6ff
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.contentlocale: pt-BR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83773765"
---
# <a name="zero-instrumentation-application-monitoring-for-kubernetes---azure-monitor-application-insights"></a>Monitoramento de aplicativo de instrumentação zero para aplicativos Kubernetes - Application Insights do Azure Monitor

> [!IMPORTANT]
>  No momento, você pode habilitar o monitoramento para os aplicativos Java em execução no Kubernetes sem instrumentar seu código - use o [agente independente do Java](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent). Embora a solução para habilitar o monitoramento de aplicativos diretamente funcione para outras linguagens, use os SDKs para monitorar seus aplicativos em execução no AKS: [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core), [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net), [Node.js](https://docs.microsoft.com/azure/azure-monitor/app/nodejs), [JavaScript](https://docs.microsoft.com/azure/azure-monitor/app/javascript) e [Python](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python).

## <a name="application-monitoring-without-instrumenting-the-code"></a>Monitoramento de aplicativos sem instrumentação do código
No momento, somente o Java permite habilitar o monitoramento de aplicativos sem instrumentar o código. Para monitorar aplicativos em outros idiomas, use os SDKs. 

## <a name="java"></a>Java
Uma vez habilitado, o agente Java coletará automaticamente uma infinidade de solicitações, dependências, logs e métricas das bibliotecas e estruturas mais amplamente usadas.

Siga as [instruções detalhadas](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent) para monitorar os aplicativos Java em execução em aplicativos Kubernetes e em outros ambientes. 

## <a name="other-languages"></a>Outros idiomas

Para os aplicativos em outros idiomas, atualmente recomendamos usar os SDKs:
* [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)
* [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net)
* [Node.js](https://docs.microsoft.com/azure/azure-monitor/app/nodejs) 
* [JavaScript](https://docs.microsoft.com/azure/azure-monitor/app/javascript)
* [Python](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python)

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre o [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) e o [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)
* Tenha uma visão geral do [Rastreamento distribuído](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing) e veja o que o [Mapa do aplicativo](https://docs.microsoft.com/azure/azure-monitor/app/app-map?tabs=net) pode fazer por sua empresa