---
title: Compartilhe visualizações personalizadas com URLs parametrizados - Azure Time Series Insights | Microsoft Docs
description: Aprenda a criar URLs parametrizadas para compartilhar facilmente visualizações personalizadas do explorador no Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.topic: conceptual
ms.workload: big-data
ms.date: 04/15/2020
ms.custom: seodec18
ms.openlocfilehash: 10616c8003d9bbbe42cb70bd1bac4193044907c0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416988"
---
# <a name="share-a-custom-view-using-a-parameterized-url"></a>Compartilhar uma exibição personalizada usando uma URL parametrizada

Para compartilhar uma visualização personalizada no Time Series Insights Explorer, você pode criar programáticamente uma URL parametrizada da exibição personalizada.

O Time Series Insights Explorer suporta parâmetros de consulta de URL para especificar visualizações na experiência diretamente da URL. Por exemplo, usando apenas a URL, você pode especificar um ambiente de destino, um predicado de pesquisa e um período de tempo desejado. Quando um usuário seleciona a URL personalizada, a interface fornece um link diretamente para esse ativo no portal Time Series Insights. Aplicam-se políticas de acesso a dados.

> [!TIP]
> * Veja a demonstração gratuita [do Time Series Insights](https://insights.timeseries.azure.com/samples).
> * Leia a documentação do [Explorador de Insights da Série Do Tempo](./time-series-insights-explorer.md) que acompanha.

## <a name="environment-id"></a>ID do Ambiente

O parâmetro `environmentId=<guid>` especifica a ID do ambiente de destino. É um componente do acesso a dados FQDN, e você pode encontrá-lo no canto superior direito da visão geral do ambiente no portal Azure. É tudo o que `env.timeseries.azure.com`precede.

Um parâmetro de ID do ambiente de exemplo é `?environmentId=10000000-0000-0000-0000-100000000108`.

## <a name="time"></a>Hora

Você pode especificar valores de tempo relativos ou absolutos com uma URL parametrizada.

### <a name="absolute-time-values"></a>Valores de tempo absolutos

Para valores de tempo absolutos, use os parâmetros `from=<integer>` e `to=<integer>`.

* `from=<integer>` é um valor em milissegundos de JavaScript da hora de início para o alcance de pesquisa.
* `to=<integer>` é um valor em milissegundos de JavaScript da hora de término para o alcance de pesquisa.

> [!TIP]
> Para traduzir facilmente as datas em milissegundos JavaScript, experimente o [Epoch & Unix Timestamp Converter](https://www.freeformatter.com/epoch-timestamp-to-date-converter.html).

### <a name="relative-time-values"></a>Valores de tempo relativos

Para um valor de `relativeMillis=<value>`tempo relativo, use , onde o *valor* está em Milissegundos JavaScript do carimbo de tempo mais recente recebido da API.

Por exemplo, `&relativeMillis=3600000` exibe os 60 minutos mais recentes dos dados.

Os valores aceitos correspondem ao menu **de tempo rápido do** explorador de insights da série do tempo e incluem:

* `1800000`(Últimos 30 min.
* `3600000`(Últimos 60 min)
* `10800000`(Últimas 3 horas)
* `21600000`(Últimas 6 horas)
* `43200000`(Últimas 12 horas)
* `86400000`(Últimas 24 horas)
* `604800000`(Últimos 7 dias)
* `2592000000`(Últimas 30 horas)

### <a name="optional-parameters"></a>Parâmetros opcionais

O `timeSeriesDefinitions=<collection of term objects>` parâmetro especifica termos predicados que aparecerão em uma exibição do Time Series Insights:

| Parâmetro | URL Item | Descrição |
| --- | --- | --- |
| **name** | `\<string>` | O nome do *termo*. |
| **splitBy** | `\<string>` | O nome da coluna para *dividido por*. |
| **medidaNome** | `\<string>` | O nome da coluna de *medida*. |
| **Predicado** | `\<string>` | O cláusula *where* para filtragem do lado do servidor. |
| **useSum** | `true` | Um parâmetro opcional que especifica o uso da soma para a sua medida. |

> [!NOTE]
> Se `Events` for a medida **de useSum** selecionada, a contagem será selecionada por padrão.  
> Se `Events` não for selecionado, a média será selecionada por padrão. |

* O `multiChartStack=<true/false>` par de valor-chave permite o empilhamento no gráfico.
* O `multiChartSameScale=<true/false>` par de valor-chave habilita a mesma escala de eixo Y em termos dentro de um parâmetro opcional.  
* O `timeBucketUnit=<Unit>&timeBucketSize=<integer>` permite ajustar o controle deslizante de intervalo para fornecer uma visão mais granular ou mais suave e mais agregada do gráfico.  
* O `timezoneOffset=<integer>` parâmetro permite definir o fuso horário para que o gráfico seja visto como uma compensação para UTC.

| Par(s) | Descrição |
| --- | --- |
| `multiChartStack=false` | `true`é ativado por padrão, `false` então passe para pilhar. |
| `multiChartStack=false&multiChartSameScale=true` | O empilhamento deve estar habilitado para usar a mesma escala de eixo Y entre os termos.  É `false` por padrão, então `true` passar permite essa funcionalidade. |
| `timeBucketUnit=<Unit>&timeBucketSize=<integer>` | Unidades `days`= `hours` `minutes`, `seconds` `milliseconds`, . .  Sempre colocar em maiuscula a unidade. </br> Defina o número de unidades passando o inteiro desejado para **o timeBucketSize**.  |
| `timezoneOffset=-<integer>` | O inteiro é sempre em milissegundos. |

> [!NOTE]
> os valores **de timeBucketUnit** podem ser suavizados até 7 dias.
> os valores **de deslocamento de fuso horário** não são UTC nem hora local.

### <a name="examples"></a>Exemplos

Para adicionar definições de séries tempois a um ambiente time series insights como parâmetro de URL, adere:

```URL parameter
&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},
{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

Use as definições de séries tempovsais de exemplo para:

* O ID ambiental
* Os últimos 60 minutos de dados
* Os termos **(F1PressureID,** **F2TempStation**e **F3VibrationPL)** que compõem os parâmetros opcionais

Você pode construir a seguinte URL parametrizada para uma exibição:

```URL
https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

[![Url parametrizado do explorador de séries tempois](media/parameterized-url/share-parameterized-url.png)](media/parameterized-url/share-parameterized-url.png#lightbox)

> [!TIP]
> Veja o Explorer ao vivo usando o exemplo [de URL](https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]) acima.

A URL acima descreve e exibe a visualização parametrizada do explorador Time Series Insights. 

* Os predicados parametrizados.

  [![Predicados parametrizados do explorador da Série Do Tempo.](media/parameterized-url/share-parameterized-url-predicates.png)](media/parameterized-url/share-parameterized-url-predicates.png#lightbox)

* A visualização do gráfico completo compartilhada.

  [![A visualização do gráfico completo compartilhada.](media/parameterized-url/share-parameterized-url-full-chart.png)](media/parameterized-url/share-parameterized-url-full-chart.png#lightbox)

## <a name="next-steps"></a>Próximas etapas

* Saiba como [consultar dados usando C#](time-series-insights-query-data-csharp.md).

* Conheça o [Explorador de Insights da Série Do Tempo](./time-series-insights-explorer.md).
