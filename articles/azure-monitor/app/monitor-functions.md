---
title: Monitorar aplicativos em execução no Azure Functions com Application Insights – Azure Monitor | Microsoft Docs
description: O Azure Monitor integra-se perfeitamente ao seu aplicativo em execução no Azure Functions e permite que você monitore o desempenho e identifique os problemas com seus aplicativos rapidamente.
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 06/26/2020
ms.openlocfilehash: 093448ad0280ada039f1d4e5abd0e83e4be19b03
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85482098"
---
# <a name="monitoring-azure-functions-with-azure-monitor-application-insights"></a>Monitoramento do Azure Functions com o Azure Monitor Application Insights

O [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) oferece integração interna com o Azure Application Insights para monitorar funções. 

O Application Insights coleta dados de log, desempenho e erro e detecta automaticamente anomalias de desempenho. O Application Insights inclui ferramentas de análise poderosas para ajudar você a diagnosticar problemas e a entender como suas funções são usadas. Quando você tiver a visibilidade dos dados do aplicativo, poderá melhorar continuamente o desempenho e a usabilidade. Você pode até mesmo usar Application Insights durante o desenvolvimento do projeto de aplicativo de funções local. 

A instrumentação do Application Insights necessária é incorporada ao Azure Functions. A única coisa de que você precisa é de uma chave de instrumentação válida para conectar seu aplicativo de funções a um recurso do Application Insights. A chave de instrumentação deve ser adicionada às configurações do aplicativo quando o recurso do aplicativo de funções é criado no Azure. Se seu aplicativo de funções ainda não tiver essa chave, você poderá defini-la manualmente. Para obter mais informações, leia mais sobre o [monitoramento do Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-monitoring?tabs=cmd).

## <a name="distributed-tracing-for-java-applications-on-windows-public-preview"></a>Rastreamento distribuído para aplicativos Java no Windows (visualização pública)

> [!IMPORTANT]
> Este recurso está atualmente em visualização pública para Java Azure Functions no Windows, não há suporte para o rastreamento distribuído para Java Azure Functions no Linux. Para o plano de consumo, ele tem um início frio de 8-9 segundos.

Se seus aplicativos forem escritos em Java, você poderá exibir dados mais ricos de seus aplicativos de funções, incluindo solicitações, dependências, logs e métricas. Os dados adicionais também permitem que você veja e diagnostique transações de ponta a ponta e veja o mapa do aplicativo, que agrega muitas transações para mostrar uma exibição topológica de como os sistemas interagem e quais são as taxas médias de desempenho e de erro.

O diagnóstico de ponta a ponta e o mapa do aplicativo fornecem visibilidade de uma única transação/solicitação. Juntos, esses dois recursos são muito úteis para encontrar a causa raiz de problemas de confiabilidade e gargalos de desempenho de acordo com a solicitação.

### <a name="how-to-enable-distributed-tracing-for-java-function-apps"></a>Como habilitar o rastreamento distribuído para aplicativos de funções Java?

Navegue até a folha Visão Geral do aplicativo de funções, vá para configurações. Em Configurações do Aplicativo, clique em "+ Nova configuração de aplicativo". Adicione as duas configurações de aplicativo a seguir com valores abaixo e clique em salvar no canto superior esquerdo. PRONTO.

```
XDT_MicrosoftApplicationInsights_Java -> 1
ApplicationInsightsAgent_EXTENSION_VERSION -> ~2
```

## <a name="next-steps"></a>Próximas etapas

* Leia mais instruções e informações sobre [Monitoramento do Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-monitoring)
* Obtenha uma visão geral de [Rastreamento distribuído](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing)
* Veja o que o [Mapa do Aplicativo](https://docs.microsoft.com/azure/azure-monitor/app/app-map?tabs=net) pode fazer por sua empresa
* Leia sobre [solicitações e dependências para aplicativos Java](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent)
* Saiba mais sobre o [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) e o [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)