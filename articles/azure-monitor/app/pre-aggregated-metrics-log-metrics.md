---
title: Métricas baseadas em log e pré-agregadas no Azure Application Insights | Microsoft Docs
description: Comparação do uso de métricas baseadas em log e pré-agregadas no Azure Application Insights
ms.topic: conceptual
author: vgorbenko
ms.author: vitalyg
ms.date: 09/18/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 9aba1e5b469e04c6c6d047f78cd202a073e5a769
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86516933"
---
# <a name="log-based-and-pre-aggregated-metrics-in-application-insights"></a>Métricas baseadas em log e pré-agregadas no Application Insights

Este artigo explica a diferença entre as métricas de Application Insights "tradicionais" que são baseadas em logs e métricas previamente agregadas que estão atualmente em visualização pública. Ambos os tipos de métricas estão disponíveis para os usuários do Application Insights, e cada um traz um valor exclusivo para o monitoramento da integridade do aplicativo, diagnóstico e análise. Os desenvolvedores que instrumentam aplicativos podem decidir qual tipo de métrica é mais adequada a um cenário específico, dependendo do tamanho do aplicativo, do volume esperado de telemetria e dos requisitos de negócios para precisão de métricas e alertas.

## <a name="log-based-metrics"></a>Métricas baseadas em log

Até recentemente, o modelo de dados de telemetria de monitoramento de aplicativos no Application Insights era exclusivamente baseado em um pequeno número de tipos predefinidos de eventos, como solicitações, exceções, chamadas de dependência, exibições de página etc. Os desenvolvedores podem usar o SDK para emitir esses eventos manualmente (escrevendo código que invoca explicitamente o SDK) ou podem contar com a coleção automática de eventos da instrumentação automática. Em ambos os casos, o back-end do Application Insights armazena todos os eventos coletados como logs e as folhas do Application Insights no portal do Azure funcionam como uma ferramenta de análise e de diagnóstico para visualizar dados baseados em eventos dos logs.

O uso de logs para reter um conjunto completo de eventos pode trazer um excelente valor analítico e de diagnóstico. Por exemplo, você pode obter uma contagem exata de solicitações para determinada URL com o número de usuários distintos que fizeram essas chamadas. Ou você pode obter rastreamentos de diagnóstico detalhados, incluindo exceções e chamadas de dependência para qualquer sessão de usuário. Ter esse tipo de informação pode melhorar consideravelmente a visibilidade da integridade e do uso do aplicativo, permitindo reduzir o tempo necessário para diagnosticar problemas com um aplicativo.

Ao mesmo tempo, coletar um conjunto completo de eventos pode ser impraticável (ou até mesmo impossível) para aplicativos que geram um grande volume de telemetria. Para situações em que o volume de eventos é muito alto, o Application Insights implementa várias técnicas de redução de volume de telemetria, como [amostragem](./sampling.md) e [filtragem](./api-filtering-sampling.md), que reduzem o número de eventos coletados e armazenados. Infelizmente, a redução do número de eventos armazenados também reduz a precisão das métricas que, nos bastidores, precisarão executar agregações no momento da consulta dos eventos armazenados nos logs.

> [!NOTE]
> No Application Insights, as métricas que são baseadas na agregação no momento da consulta de eventos e medidas armazenadas nos logs são chamadas de métricas baseadas em log. Essas métricas normalmente têm muitas dimensões das propriedades do evento, o que as torna superiores para análise, mas a precisão dessas métricas é afetada negativamente pela amostragem e filtragem.

## <a name="pre-aggregated-metrics"></a>Métricas pré-agregadas

Além das métricas baseadas em log, no final de 2018, a equipe de Application Insights enviou uma visualização pública das métricas que são armazenadas em um repositório especializado que é otimizado para a série temporal. As novas métricas não são mais mantidas como eventos individuais com muitas propriedades. Em vez disso, elas são armazenadas como séries temporais pré-agregadas e somente com dimensões de chave. Isso torna as novas métricas superiores no momento da consulta: a recuperação de dados ocorre muito mais rapidamente e exige menos potência de computação. Consequentemente, isso possibilita novos cenários, como [alertas quase em tempo real sobre dimensões de métricas](../platform/alerts-metric-near-real-time.md), [painéis](./overview-dashboard.md) mais dinâmicos e muito mais.

> [!IMPORTANT]
> As métricas baseadas em log e pré-agregadas coexistem no Application Insights. Para diferenciar os dois, no Application Insights UX, as métricas previamente agregadas agora são chamadas de "métricas padrão (versão prévia)", enquanto as métricas tradicionais dos eventos foram renomeadas para "métricas baseadas em log".

Os SDKs mais recentes (SDK do [Application Insights 2.7](https://www.nuget.org/packages/Microsoft.ApplicationInsights/2.7.2) ou posterior para .NET) pré-agregam as métricas durante a coleta, antes do início das técnicas de redução de volume da telemetria. Isso significa que a precisão das novas métricas não é afetada por amostragem e filtragem ao usar os SDKs de Application Insights mais recentes.

Para os SDKs que não implementam a pré-autenticação (ou seja, versões mais antigas de SDKs de Application Insights ou instrumentação de navegador), o back-end Application Insights ainda preenche as novas métricas agregando os eventos recebidos pelo ponto de extremidade de coleta de eventos Application Insights. Isso significa que, embora você não se beneficie do volume reduzido de dados transmitidos pela rede, ainda poderá usar as métricas previamente agregadas e experimentar um melhor desempenho e suporte do alerta dimensional quase em tempo real com SDKs que não agregam métricas durante a coleta.

Vale a pena mencionar que o ponto de extremidade de coleta pré-agrega eventos antes da amostragem de ingestão, o que significa que a [amostragem de ingestão](./sampling.md) nunca afetará a precisão das métricas pré-agregadas, independentemente da versão de SDK usada com o aplicativo.  

## <a name="using-pre-aggregation-with-application-insights-custom-metrics"></a>Usando a pré-agregação com métricas personalizadas do Application Insights

Você pode usar a pré-agregação com métricas personalizadas. Os dois principais benefícios são a capacidade de configurar e receber alertas sobre uma dimensão de uma métrica personalizada e reduzir o volume de dados enviado do SDK para o ponto de extremidade de coleta do Application Insights.

Existem várias [maneiras de enviar métricas personalizadas do SDK do Application Insights](./api-custom-events-metrics.md). Se a versão do SDK oferecer os métodos [GetMetric e TrackValue](./api-custom-events-metrics.md#getmetric), essa será a maneira preferencial de enviar métricas personalizadas, pois nesse caso, a pré-agregação ocorrerá dentro do SDK, não apenas reduzindo o volume dos dados armazenados no Azure, mas também o volume de dados transmitidos do SDK para o Application Insights. Caso contrário, use o método [trackMetric](./api-custom-events-metrics.md#trackmetric), que pré-agregará os eventos de métrica durante a ingestão de dados.

## <a name="custom-metrics-dimensions-and-pre-aggregation"></a>Dimensões de métricas personalizadas e pré-agregação

Todas as métricas que você envia usando o método [trackMetric](./api-custom-events-metrics.md#trackmetric) ou as chamadas à API [GetMetric e TrackValue](./api-custom-events-metrics.md#getmetric) são automaticamente armazenadas em repositórios de logs e de métricas. No entanto, enquanto a versão baseada em log da métrica personalizada sempre retém todas as dimensões, a versão pré-agregada da métrica é armazenada por padrão sem dimensões. Você pode ativar a coleta de dimensões de métricas personalizadas na guia [uso e custo estimado](./pricing.md) marcando "Habilitar alertas em dimensões de métricas personalizadas": 

![Uso e custo estimado](./media/pre-aggregated-metrics-log-metrics/001-cost.png)

## <a name="why-is-collection-of-custom-metrics-dimensions-turned-off-by-default"></a>Por que a coleta de dimensões de métricas personalizadas está desativada por padrão?

A coleta de dimensões de métricas personalizadas está desativada por padrão porque, no futuro, o armazenamento de métricas personalizadas com dimensões será cobrado separadamente do Application Insights, enquanto o armazenamento das métricas personalizadas não dimensionais permanecerá gratuito (até uma cota). Saiba mais sobre as futuras alterações do modelo de preços em nossa [página de preços](https://azure.microsoft.com/pricing/details/monitor/) oficial.

## <a name="creating-charts-and-exploring-log-based-and-standard-pre-aggregated-metrics"></a>Criando gráficos e explorando métricas baseadas em log e pré-agregadas padrão

Use [Azure Monitor Metrics Explorer](../platform/metrics-getting-started.md) para plotar gráficos de métricas previamente agregadas e baseadas em log e para criar painéis com gráficos. Depois de selecionar o recurso desejado do Application Insights, use o seletor de namespace para alternar entre as métricas padrão (versão prévia) e as métricas baseadas em log, ou selecione um namespace de métrica personalizado:

![Namespace da métrica](./media/pre-aggregated-metrics-log-metrics/002-metric-namespace.png)

## <a name="pricing-models-for-application-insights-metrics"></a>Modelos de preços para métricas de Application Insights

A ingestão de métricas em Application Insights, seja com base em log ou agregada, gerará custos com base no tamanho dos dados ingeridos, conforme descrito [aqui](./pricing.md#pricing-model). Suas métricas personalizadas, incluindo todas as suas dimensões, são sempre armazenadas no repositório de logs de Application Insights; Além disso, uma versão previamente agregada de suas métricas personalizadas (sem dimensões) é encaminhada para o repositório de métricas por padrão.

A seleção da opção [habilitar alertas em dimensões de métricas personalizadas](#custom-metrics-dimensions-and-pre-aggregation) para armazenar todas as dimensões das métricas previamente agregadas no repositório de métricas pode gerar custos **adicionais** com base nos [preços das métricas personalizadas](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="next-steps"></a>Próximas etapas

* [Alertas quase em tempo real](../platform/alerts-metric-near-real-time.md)
* [GetMetric e TrackValue](./api-custom-events-metrics.md#getmetric)
