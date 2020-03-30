---
title: Previsão de & de detecção de anomalias em séries tempois no Azure Data Explorer
description: Saiba como analisar dados de séries tempois para detecção e previsão de anomalias usando o Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/24/2019
ms.openlocfilehash: a482fef93d43f92257608b65c9c0e2ade535bcca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78194150"
---
# <a name="anomaly-detection-and-forecasting-in-azure-data-explorer"></a>Detecção e previsão de anomalias no Azure Data Explorer

O Azure Data Explorer realiza a coleta contínua de dados de telemetria de serviços de nuvem ou dispositivos IoT. Esses dados são analisados para diversos insights, como monitoramento da saúde dos serviços, processos de produção física, tendências de uso e previsão de carga. A análise é feita em séries tempois de métricas selecionadas para localizar um padrão de desvio da métrica em relação ao seu padrão normal típico de linha de base. O Azure Data Explorer contém suporte nativo para criação, manipulação e análise de várias séries tempontos. Ele pode criar e analisar milhares de séries temporinativas em segundos, permitindo soluções de monitoramento e fluxos de trabalho em tempo real.

Este artigo detalha os recursos de detecção e previsão de anomalias da série temporal do Azure Data Explorer. As funções das séries tempois aplicáveis são baseadas em um modelo de decomposição robusto e conhecido, onde cada série temporal original é decomposta em componentes sazonais, tendências e residuais. Anomalias são detectadas por outliers no componente residual, enquanto a previsão é feita extrapolando os componentes sazonais e de tendência. A implementação do Azure Data Explorer melhora significativamente o modelo básico de decomposição por detecção automática de sazonalidade, análise robusta de outlier e implementação vetorializada para processar milhares de séries tempontos em segundos.

## <a name="prerequisites"></a>Pré-requisitos

Leia [a análise da série Time no Azure Data Explorer](/azure/data-explorer/time-series-analysis) para uma visão geral dos recursos das séries tempois.

## <a name="time-series-decomposition-model"></a>Modelo de decomposição de séries tempois

A implementação nativa do Azure Data Explorer para previsão de séries tempois e detecção de anomalias usa um modelo de decomposição bem conhecido. Este modelo é aplicado a séries tempois de métricas esperadas para manifestar comportamentos periódicos e de tendência, como tráfego de serviço, batimentos cardíacos componentes e medições periódicas de IoT para prever valores métricos futuros e detectar os anômalos. O pressuposto desse processo de regressão é que, além do comportamento sazonal e de tendência anteriormente conhecido, as séries temporâneas são distribuídas aleatoriamente. Em seguida, você pode prever valores métricos futuros dos componentes sazonais e de tendência, coletivamente nomeados como linha de base, e ignorar a parte residual. Você também pode detectar valores anômalos com base na análise outlier usando apenas a porção residual.
Para criar um modelo de [`series_decompose()`](/azure/kusto/query/series-decomposefunction)decomposição, use a função . A `series_decompose()` função leva um conjunto de séries temporiais e decompõe automaticamente cada série temporal aos seus componentes sazonais, de tendência, residuais e de linha de base. 

Por exemplo, você pode decompor o tráfego de um serviço web interno usando a seguinte consulta:

**\[**[**Clique para executar a consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA3WQ3WrDMAyF7/sUukvCnDXJGIOVPEULuwxqoixm/gm2+jf28JObFjbYrmyho3M+yRCD1a5jaGFAJtaW8qaqX8qqLqvnYrMySYHnvxRNWT1B07xW1U03JFEzbVYDWd9Z/KAuUtAUm9UXpLJcSnAH2+LxPZe3AO9gJ6ZbRjvDGLy9EbG/BUemOXnvLxD1AOJ1mijQtWhbyHbbOgOA9RogkqGeAaXn3g1BooVb6OiDNHpD6CjAUccDGv2JrL0TSzozuQHyPYqHdqRkDKN3aBRwkJaCQJIoQ4VsuXh2A/Xezj5SWkVBWSvI0vSoOSsWpLtEpyDwY4KTW8nnJ5ws+2+eAhSyOxjkd+HDVVcIfHplp2TYTxgYTpqnnDUbarM32gPO86PY4jjqfmGw3vGkftNlCi5xNprbWW5kYvENQQnqDh8CAAA=)**\]**

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t step dt by sid 
| where sid == 'TS1'   //  select a single time series for a cleaner visualization
| extend (baseline, seasonal, trend, residual) = series_decompose(num, -1, 'linefit')  //  decomposition of a set of time series to seasonal, trend, residual, and baseline (seasonal+trend)
| render timechart with(title='Web app. traffic of a month, decomposition', ysplit=panels)
```

![Decomposição da série temporal](media/anomaly-detection/series-decompose-timechart.png)

* A série temporal original é rotulada **como num** (em vermelho). 
* O processo começa pela detecção automática da [`series_periods_detect()`](/azure/kusto/query/series-periods-detectfunction) sazonalidade usando a função e extrai o padrão **sazonal** (em roxo).
* O padrão sazonal é subtraído da série temporal original e uma [`series_fit_line()`](/azure/kusto/query/series-fit-linefunction) regressão linear é executada usando a função para encontrar o componente **de tendência** (em azul claro).
* A função subtrai a tendência e o restante é o componente **residual** (em verde).
* Finalmente, a função adiciona os componentes sazonais e de tendência para gerar a **linha de base** (em azul).

## <a name="time-series-anomaly-detection"></a>Detecção de anomalias de série temporal

A [`series_decompose_anomalies()`](/azure/kusto/query/series-decompose-anomaliesfunction) função encontra pontos anômalos em um conjunto de séries tempontos. Esta função `series_decompose()` chama para construir o [`series_outliers()`](/azure/kusto/query/series-outliersfunction) modelo de decomposição e, em seguida, é executada no componente residual. `series_outliers()`calcula as pontuações de anomalia para cada ponto do componente residual usando o teste de cerca de Tukey. Os escores de anomalia acima de 1,5 ou abaixo de -1,5 indicam um leve aumento ou declínio da anomalia, respectivamente. Os escores de anomalia acima de 3,0 ou abaixo de -3,0 indicam uma anomalia forte. 

A seguinte consulta permite detectar anomalias no tráfego interno de serviços web:

**\[**[**Clique para executar a consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA3WR3W7CMAyF73mKI25KpRbaTmjSUJ8CpF1WoXVptPxUifmb9vBLoGO7GFeR7ePv2I4ihpamYdToBBNLTYuqKF/zosyLdbqZqagQl/8UVV68oKreimLSdVFUDZtZR9o2WnxQ48lJ8tXsCzHM7yHMUdfidFiEN4U12AXoloUe0Turp4nYTsaeaYzs/RVedgis80CObkFdI9ltywTAagV4UtQyRKiZgyLEaTGZ9taFQqtIGHI4SX8USn4KltYEJF2YTIeFMFaHPPkMvrWOMuxFoEpDaVjujmo6aq0erafmIY+7ZCiX6wx5mSGJHb3kJA1sF8jB8q69toNwjLPkYfGTseqoja//eLNkRXXyTnuIcVyCneh72cL2YQdtDQ8ZHvIkDcsfPWH+3AvPvObx0FMXD/RLhfDYW9VhtNKwj/8U69M1b2S//AbRUQMWQQIAAA==)**\]**

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t step dt by sid 
| where sid == 'TS1'   //  select a single time series for a cleaner visualization
| extend (anomalies, score, baseline) = series_decompose_anomalies(num, 1.5, -1, 'linefit')
| render anomalychart with(anomalycolumns=anomalies, title='Web app. traffic of a month, anomalies') //use "| render anomalychart with anomalycolumns=anomalies" to render the anomalies as bold points on the series charts.
```

![Detecção de anomalias de série temporal](media/anomaly-detection/series-anomaly-detection.png)

* A série temporal original (em vermelho). 
* O componente linha de base (sazonal + tendência) (em azul).
* Os pontos anômalos (em roxo) no topo da série temporal original. Os pontos anômalos se desviam significativamente dos valores de linha de base esperados.

## <a name="time-series-forecasting"></a>Previsão de séries tempois

A [`series_decompose_forecast()`](/azure/kusto/query/series-decompose-forecastfunction) função prevê valores futuros de um conjunto de séries temponíveis. Esta função `series_decompose()` exige a construção do modelo de decomposição e, em seguida, para cada série de tempo, extrapola o componente de linha de base no futuro.

A seguinte consulta permite prever o tráfego de serviços web da próxima semana:

**\[**[**Clique para executar a consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA22QzW6DMBCE73mKuQFqKISqitSIW98gkXpEDl5iK9hG9uanUR++dqE99YRGO8x845EYRtuO0UIKJtaG8qbebMt6U9avxW41Joe4/+doyvoFTfNW14tPJlOjZqGc1w9n263crSQZ1xlxpi6Q1xSa1ReSLGcJezGtuJ7y+C3gLA6xZM/CTBi8MwshuxnkaUlGYJpS5/ETQUvEzJsiTz+ibZEd9psMQFUBgUbqGSLe7GkkpBVYygfn46EfSVjyuOpwEaN+CNbOxki6M1mZTNSLkAbOv3WSemcmF6j7vSX8dcTUlvOFsZJcFDHFx4wYnmp7JTzjplnlrHmkNvugI8Q0PYO9GAbdww0RyDjLav1XHLnBimAjEG5E5zQ7vRP284x36hOOTtxZ8Q3The8P2QEAAA==)**\]**

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
let horizon=7d;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t+horizon step dt by sid 
| where sid == 'TS1'   //  select a single time series for a cleaner visualization
| extend forecast = series_decompose_forecast(num, toint(horizon/dt))
| render timechart with(title='Web app. traffic of a month, forecasting the next week by Time Series Decomposition')
```

![Previsão de séries tempois](media/anomaly-detection/series-forecasting.png)

* Métrica original (em vermelho). Os valores futuros estão faltando e definidos como 0, por padrão.
* Extrapolar o componente de linha de base (em azul) para prever os valores da próxima semana.

## <a name="scalability"></a>Escalabilidade

A sintaxe do idioma do Azure Data Explorer permite que uma única chamada processe várias séries tempor. Sua implementação otimizada única permite um desempenho rápido, o que é fundamental para a detecção e previsão de anomalias eficazes ao monitorar milhares de contadores em cenários quase em tempo real.

A consulta a seguir mostra o processamento de três séries temporinhas simultaneamente:

**\[**[**Clique para executar a consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA21Qy26DMBC85yvmFlChcUirSI34ikTqETl4KVawjfDmqX587UCaHuqLtePxPLYjhtG2YpRQkom1oaQQy3Uulrl4TzezLjLk5T9GkYsViuJDiImnIqlox6F1g745W67VZqbIuMrIA1WeBk2+mH0jjvk4wh5NKU9fSbhTOItdMNmyND2awZkpIbsxyMukDM/UR8/9FV6rIEkXJqvgmsYTl7X0lISHspzvtqt5hjdxPxkeYBHA4gGKFMBiAUilIAfWja617CY1NG4ASX/FSfuj7PRNsg4ZXANz7Fj3HSGuBmOjZ5hYbcSqIBwbZpNk+iQFcQpx4/omrqLamd55qh5v41d22nIybWChOI0qQ9Cg4e5ftyE6zprbhDV3VM4/aQ/Z96/gQTahU4wsYZzlNvs11vYL3BJsCIQz0eHed/W30jz9AUEBI0ktAgAA)**\]**

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
let horizon=7d;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t+horizon step dt by sid
| extend offset=case(sid=='TS3', 4000000, sid=='TS2', 2000000, 0)   //  add artificial offset for easy visualization of multiple time series
| extend num=series_add(num, offset)
| extend forecast = series_decompose_forecast(num, toint(horizon/dt))
| render timechart with(title='Web app. traffic of a month, forecasting the next week for 3 time series')
```

![Escalabilidade de séries tempois](media/anomaly-detection/series-scalability.png)

## <a name="summary"></a>Resumo

Este documento detalha as funções nativas do Azure Data Explorer para detecção e previsão de anomalias em séries tempo. Cada série temporal original é decomposta em componentes sazonais, tendências e residuais para detectar anomalias e/ou previsões. Essas funcionalidades podem ser usadas para cenários de monitoramento quase em tempo real, como detecção de falhas, manutenção preditiva e previsão de demanda e carga.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [os recursos de aprendizado de máquina](/azure/data-explorer/machine-learning-clustering) no Azure Data Explorer.
