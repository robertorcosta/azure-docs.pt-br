---
title: Métricas baseadas em log e pré-agregadas no Azure Application Insights | Microsoft Docs
description: Comparação do uso de métricas baseadas em log e pré-agregadas no Azure Application Insights
ms.topic: conceptual
author: vgorbenko
ms.author: vitalyg
ms.date: 09/18/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 30487eebed361e5b010df023a9b1a44f96590b14
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81271073"
---
# <a name="log-based-and-pre-aggregated-metrics-in-application-insights"></a>Métricas baseadas em log e pré-agregadas no Application Insights

Este artigo explica a diferença entre métricas "tradicionais" do Application Insights baseadas em logs e métricas pré-agregadas que estão atualmente em visualização pública. Ambos os tipos de métricas estão disponíveis para os usuários do Application Insights, e cada um traz um valor exclusivo para o monitoramento da integridade do aplicativo, diagnóstico e análise. Os desenvolvedores que instrumentam aplicativos podem decidir qual tipo de métrica é mais adequada a um cenário específico, dependendo do tamanho do aplicativo, do volume esperado de telemetria e dos requisitos de negócios para precisão de métricas e alertas.

## <a name="log-based-metrics"></a>Métricas baseadas em log

Até recentemente, o modelo de dados de telemetria de monitoramento de aplicativos no Application Insights era baseado unicamente em um pequeno número de tipos predefinidos de eventos, como solicitações, exceções, chamadas de dependência, visualizações de página, etc. Os desenvolvedores podem usar o SDK para emitir esses eventos manualmente (escrevendo código que invoca explicitamente o SDK) ou podem contar com a coleta automática de eventos a partir de instrumentação automática. Em ambos os casos, o back-end do Application Insights armazena todos os eventos coletados como logs e as folhas do Application Insights no portal do Azure funcionam como uma ferramenta de análise e de diagnóstico para visualizar dados baseados em eventos dos logs.

O uso de logs para reter um conjunto completo de eventos pode trazer um excelente valor analítico e de diagnóstico. Por exemplo, você pode obter uma contagem exata de solicitações para determinada URL com o número de usuários distintos que fizeram essas chamadas. Ou você pode obter rastreamentos de diagnóstico detalhados, incluindo exceções e chamadas de dependência para qualquer sessão de usuário. Ter esse tipo de informação pode melhorar consideravelmente a visibilidade da integridade e do uso do aplicativo, permitindo reduzir o tempo necessário para diagnosticar problemas com um aplicativo.

Ao mesmo tempo, a coleta de um conjunto completo de eventos pode ser impraticável (ou até mesmo impossível) para aplicações que geram um grande volume de telemetria. Para situações em que o volume de eventos é muito alto, o Application Insights implementa várias técnicas de redução de volume de telemetria, como [amostragem](https://docs.microsoft.com/azure/application-insights/app-insights-sampling) e [filtragem](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling), que reduzem o número de eventos coletados e armazenados. Infelizmente, a redução do número de eventos armazenados também reduz a precisão das métricas que, nos bastidores, precisarão executar agregações no momento da consulta dos eventos armazenados nos logs.

> [!NOTE]
> No Application Insights, as métricas que são baseadas na agregação no momento da consulta de eventos e medidas armazenadas nos logs são chamadas de métricas baseadas em log. Essas métricas normalmente têm muitas dimensões das propriedades do evento, o que as torna superiores para análise, mas a precisão dessas métricas é afetada negativamente pela amostragem e filtragem.

## <a name="pre-aggregated-metrics"></a>Métricas pré-agregadas

Além das métricas baseadas em log, no final de 2018, a equipe do Application Insights enviou uma prévia pública de métricas que são armazenadas em um repositório especializado que é otimizado para séries temporinativas. As novas métricas não são mais mantidas como eventos individuais com muitas propriedades. Em vez disso, elas são armazenadas como séries temporais pré-agregadas e somente com dimensões de chave. Isso torna as novas métricas superiores no momento da consulta: a recuperação de dados ocorre muito mais rapidamente e exige menos potência de computação. Consequentemente, isso possibilita novos cenários, como [alertas quase em tempo real sobre dimensões de métricas](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts), [painéis](https://docs.microsoft.com/azure/azure-monitor/app/overview-dashboard) mais dinâmicos e muito mais.

> [!IMPORTANT]
> As métricas baseadas em log e pré-agregadas coexistem no Application Insights. Para diferenciar os dois, no Application Insights UX, as métricas pré-agregadas são agora chamadas de "Métricas padrão (visualização)", enquanto as métricas tradicionais dos eventos foram renomeadas para "métricas baseadas em log".

Os SDKs mais recentes (SDK do [Application Insights 2.7](https://www.nuget.org/packages/Microsoft.ApplicationInsights/2.7.2) ou posterior para .NET) pré-agregam as métricas durante a coleta, antes do início das técnicas de redução de volume da telemetria. Isso significa que a precisão das novas métricas não é afetada pela amostragem e filtragem ao usar os SDKs de Insights de Aplicativos mais recentes.

Para os SDKs que não implementam a pré-agregação (ou seja, versões mais antigas dos SDKs do Application Insights ou para instrumentação do navegador), o backend application Insights ainda preenche as novas métricas agregando os eventos recebidos pelo ponto final da coleção de eventos Application Insights. Isso significa que, embora você não se beneficie do volume reduzido de dados transmitidos pelo fio, você ainda pode usar as métricas pré-agregadas e experimentar melhor desempenho e suporte do alerta dimensional quase em tempo real com SDKs que não pré-agregam métricas durante a coleta.

Vale a pena mencionar que o ponto de extremidade de coleta pré-agrega eventos antes da amostragem de ingestão, o que significa que a [amostragem de ingestão](https://docs.microsoft.com/azure/application-insights/app-insights-sampling) nunca afetará a precisão das métricas pré-agregadas, independentemente da versão de SDK usada com o aplicativo.  

## <a name="using-pre-aggregation-with-application-insights-custom-metrics"></a>Usando a pré-agregação com métricas personalizadas do Application Insights

Você pode usar a pré-agregação com métricas personalizadas. Os dois principais benefícios são a capacidade de configurar e receber alertas sobre uma dimensão de uma métrica personalizada e reduzir o volume de dados enviado do SDK para o ponto de extremidade de coleta do Application Insights.

Existem várias [maneiras de enviar métricas personalizadas do SDK do Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics). Se a versão do SDK oferecer os métodos [GetMetric e TrackValue](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#getmetric), essa será a maneira preferencial de enviar métricas personalizadas, pois nesse caso, a pré-agregação ocorrerá dentro do SDK, não apenas reduzindo o volume dos dados armazenados no Azure, mas também o volume de dados transmitidos do SDK para o Application Insights. Caso contrário, use o método [trackMetric](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackmetric), que pré-agregará os eventos de métrica durante a ingestão de dados.

## <a name="custom-metrics-dimensions-and-pre-aggregation"></a>Dimensões de métricas personalizadas e pré-agregação

Todas as métricas que você envia usando o método [trackMetric](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackmetric) ou as chamadas à API [GetMetric e TrackValue](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#getmetric) são automaticamente armazenadas em repositórios de logs e de métricas. No entanto, enquanto a versão baseada em log da métrica personalizada sempre retém todas as dimensões, a versão pré-agregada da métrica é armazenada por padrão sem dimensões. Você pode ativar a coleção de dimensões de métricas personalizadas sobre o uso e a guia [de custos estimados](https://docs.microsoft.com/azure/application-insights/app-insights-pricing) verificando "Habilitar alerta sobre dimensões métricas personalizadas": 

![Uso e custo estimado](./media/pre-aggregated-metrics-log-metrics/001-cost.png)

## <a name="why-is-collection-of-custom-metrics-dimensions-turned-off-by-default"></a>Por que a coleta de dimensões de métricas personalizadas está desativada por padrão?

A coleta de dimensões de métricas personalizadas está desativada por padrão porque, no futuro, o armazenamento de métricas personalizadas com dimensões será cobrado separadamente do Application Insights, enquanto o armazenamento das métricas personalizadas não dimensionais permanecerá gratuito (até uma cota). Saiba mais sobre as futuras alterações do modelo de preços em nossa [página de preços](https://azure.microsoft.com/pricing/details/monitor/) oficial.

## <a name="creating-charts-and-exploring-log-based-and-standard-pre-aggregated-metrics"></a>Criando gráficos e explorando métricas baseadas em log e pré-agregadas padrão

Use [o Azure Monitor Metrics Explorer](../platform/metrics-getting-started.md) para traçar gráficos de métricas pré-agregadas e baseadas em log, e para criar dashboards com gráficos. Depois de selecionar o recurso desejado do Application Insights, use o seletor de namespace para alternar entre as métricas padrão (versão prévia) e as métricas baseadas em log, ou selecione um namespace de métrica personalizado:

![Namespace da métrica](./media/pre-aggregated-metrics-log-metrics/002-metric-namespace.png)

## <a name="pricing-models-for-application-insights-metrics"></a>Modelos de preços para métricas de Insights de Aplicativos

A ingestão de métricas no Application Insights, seja baseada em log ou pré-agregada, gerará custos com base no tamanho dos dados ingeridos, conforme descrito [aqui](https://docs.microsoft.com/azure/azure-monitor/app/pricing#pricing-model). Suas métricas personalizadas, incluindo todas as suas dimensões, são sempre armazenadas no log-store application Insights; além disso, uma versão pré-agregada de suas métricas personalizadas (sem dimensões) é encaminhada para o armazenamento de métricas por padrão.

Selecionar a [opção Ativar alerta em dimensões métricas personalizadas](#custom-metrics-dimensions-and-pre-aggregation) para armazenar todas as dimensões das métricas pré-agregadas na loja métrica, pode gerar custos **adicionais** com base nos [preços de Métricas Personalizadas](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="next-steps"></a>Próximas etapas

* [Alertas quase em tempo real](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts)
* [GetMetric e TrackValue](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#getmetric)