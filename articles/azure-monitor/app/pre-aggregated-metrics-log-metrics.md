---
title: Métricas baseadas em log e pré-agregadas no Azure Application Insights | Microsoft Docs
description: Comparação do uso de métricas baseadas em log e pré-agregadas no Azure Application Insights
ms.topic: conceptual
author: vgorbenko
ms.author: vitalyg
ms.date: 09/18/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 65abc9c7153aaf2973d5927400e27467066098f9
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78384484"
---
# <a name="log-based-and-pre-aggregated-metrics-in-application-insights"></a>Métricas baseadas em log e pré-agregadas no Application Insights

Este artigo explica a diferença entre as métricas “tradicionais” do Application Insights que são baseadas em logs e as métricas pré-agregadas que atualmente estão em versão prévia pública. Ambos os tipos de métricas estão disponíveis para os usuários do Application Insights, e cada um traz um valor exclusivo para o monitoramento da integridade do aplicativo, diagnóstico e análise. Os desenvolvedores que instrumentam aplicativos podem decidir qual tipo de métrica é mais adequada a um cenário específico, dependendo do tamanho do aplicativo, do volume esperado de telemetria e dos requisitos de negócios para precisão de métricas e alertas.

## <a name="log-based-metrics"></a>Métricas baseadas em log

Até recentemente, o modelo de dados de telemetria de monitoramento de aplicativos no Application Insights era exclusivamente baseado em um pequeno número de tipos predefinidos de eventos, como solicitações, exceções, chamadas de dependência, exibições de página etc. Os desenvolvedores podem usar o SDK para emitir esses eventos manualmente (escrevendo código que invoca explicitamente o SDK) ou podem contar com a coleção automática de eventos da instrumentação automática. Em ambos os casos, o back-end do Application Insights armazena todos os eventos coletados como logs e as folhas do Application Insights no portal do Azure funcionam como uma ferramenta de análise e de diagnóstico para visualizar dados baseados em eventos dos logs.

O uso de logs para reter um conjunto completo de eventos pode trazer um excelente valor analítico e de diagnóstico. Por exemplo, você pode obter uma contagem exata de solicitações para determinada URL com o número de usuários distintos que fizeram essas chamadas. Ou você pode obter rastreamentos de diagnóstico detalhados, incluindo exceções e chamadas de dependência para qualquer sessão de usuário. Ter esse tipo de informação pode melhorar consideravelmente a visibilidade da integridade e do uso do aplicativo, permitindo reduzir o tempo necessário para diagnosticar problemas com um aplicativo. 

Ao mesmo tempo, a coleta de um conjunto completo de eventos pode ser impraticável (ou até mesmo impossível) para aplicativos que geram muita telemetria. Para situações em que o volume de eventos é muito alto, o Application Insights implementa várias técnicas de redução de volume de telemetria, como [amostragem](https://docs.microsoft.com/azure/application-insights/app-insights-sampling) e [filtragem](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling), que reduzem o número de eventos coletados e armazenados. Infelizmente, a redução do número de eventos armazenados também reduz a precisão das métricas que, nos bastidores, precisarão executar agregações no momento da consulta dos eventos armazenados nos logs.

> [!NOTE]
> No Application Insights, as métricas que são baseadas na agregação no momento da consulta de eventos e medidas armazenadas nos logs são chamadas de métricas baseadas em log. Essas métricas normalmente têm muitas dimensões das propriedades do evento, o que as torna superiores para análise, mas a precisão dessas métricas é afetada negativamente pela amostragem e filtragem.

## <a name="pre-aggregated-metrics"></a>Métricas pré-agregadas

Além das métricas baseadas em log, no segundo semestre de 2018, a equipe do Application Insights enviou uma versão prévia pública das métricas que são armazenadas em um repositório especializado otimizado para série temporal. As novas métricas não são mais mantidas como eventos individuais com muitas propriedades. Em vez disso, elas são armazenadas como séries temporais pré-agregadas e somente com dimensões de chave. Isso torna as novas métricas superiores no momento da consulta: a recuperação de dados ocorre muito mais rapidamente e exige menos potência de computação. Consequentemente, isso possibilita novos cenários, como [alertas quase em tempo real sobre dimensões de métricas](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts), [painéis](https://docs.microsoft.com/azure/azure-monitor/app/overview-dashboard) mais dinâmicos e muito mais.

> [!IMPORTANT]
> As métricas baseadas em log e pré-agregadas coexistem no Application Insights. Para diferenciar as duas, na experiência do usuário do Application Insights, as métricas pré-agregadas agora são chamadas “Métricas padrão (versão prévia)”, enquanto as métricas tradicionais dos eventos foram renomeadas para “Métricas baseadas em log”.

Os SDKs mais recentes (SDK do [Application Insights 2.7](https://www.nuget.org/packages/Microsoft.ApplicationInsights/2.7.2) ou posterior para .NET) pré-agregam as métricas durante a coleta, antes do início das técnicas de redução de volume da telemetria. Isso significa que a precisão das novas métricas não é afetada pela amostragem e filtragem durante o uso dos últimos SDKs do Application Insights.

Para os SDKs que não implementam a pré-agregação (ou seja, as versões mais antigas dos SDKs do Application Insights ou para a instrumentação de navegador), o back-end do Application Insights ainda popula as novas métricas agregando os eventos recebidos pelo ponto de extremidade de coleta de eventos do Application Insights. Isso significa que embora você não se beneficie do volume reduzido de dados transmitidos eletronicamente, ainda poderá usar as métricas pré-agregadas e experimentar um melhor desempenho e o suporte quase em tempo real de alertas dimensionais com SDKs que não pré-agregam métricas durante a coleta.

Vale a pena mencionar que o ponto de extremidade de coleta pré-agrega eventos antes da amostragem de ingestão, o que significa que a [amostragem de ingestão](https://docs.microsoft.com/azure/application-insights/app-insights-sampling) nunca afetará a precisão das métricas pré-agregadas, independentemente da versão de SDK usada com o aplicativo.  

## <a name="using-pre-aggregation-with-application-insights-custom-metrics"></a>Usando a pré-agregação com métricas personalizadas do Application Insights

Você pode usar a pré-agregação com métricas personalizadas. Os dois principais benefícios são a capacidade de configurar e receber alertas sobre uma dimensão de uma métrica personalizada e reduzir o volume de dados enviado do SDK para o ponto de extremidade de coleta do Application Insights.

Existem várias [maneiras de enviar métricas personalizadas do SDK do Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics). Se a versão do SDK oferecer os métodos [GetMetric e TrackValue](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#getmetric), essa será a maneira preferencial de enviar métricas personalizadas, pois nesse caso, a pré-agregação ocorrerá dentro do SDK, não apenas reduzindo o volume dos dados armazenados no Azure, mas também o volume de dados transmitidos do SDK para o Application Insights. Caso contrário, use o método [trackMetric](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackmetric), que pré-agregará os eventos de métrica durante a ingestão de dados.

## <a name="custom-metrics-dimensions-and-pre-aggregation"></a>Dimensões de métricas personalizadas e pré-agregação

Todas as métricas que você envia usando o método [trackMetric](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackmetric) ou as chamadas à API [GetMetric e TrackValue](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#getmetric) são automaticamente armazenadas em repositórios de logs e de métricas. No entanto, enquanto a versão baseada em log da métrica personalizada sempre retém todas as dimensões, a versão pré-agregada da métrica é armazenada por padrão sem dimensões. Você pode ativar a coleta de dimensões de métricas personalizadas na guia [uso e custo estimado](https://docs.microsoft.com/azure/application-insights/app-insights-pricing) marcando a opção “Habilitar alertas sobre dimensões de métricas personalizadas”: 

![Uso e custo estimado](./media/pre-aggregated-metrics-log-metrics/001-cost.png)

## <a name="why-is-collection-of-custom-metrics-dimensions-turned-off-by-default"></a>Por que a coleta de dimensões de métricas personalizadas está desativada por padrão?

A coleta de dimensões de métricas personalizadas está desativada por padrão porque, no futuro, o armazenamento de métricas personalizadas com dimensões será cobrado separadamente do Application Insights, enquanto o armazenamento das métricas personalizadas não dimensionais permanecerá gratuito (até uma cota). Saiba mais sobre as futuras alterações do modelo de preços em nossa [página de preços](https://azure.microsoft.com/pricing/details/monitor/) oficial.

## <a name="creating-charts-and-exploring-log-based-and-standard-pre-aggregated-metrics"></a>Criando gráficos e explorando métricas baseadas em log e pré-agregadas padrão

Use [Azure Monitor Metrics Explorer](../platform/metrics-getting-started.md) para plotar gráficos de métricas previamente agregadas e baseadas em log e para criar painéis com gráficos. Depois de selecionar o recurso desejado do Application Insights, use o seletor de namespace para alternar entre as métricas padrão (versão prévia) e as métricas baseadas em log, ou selecione um namespace de métrica personalizado:

![Namespace da métrica](./media/pre-aggregated-metrics-log-metrics/002-metric-namespace.png)

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

* [Alertas quase em tempo real](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts)
* [GetMetric e TrackValue](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#getmetric)
