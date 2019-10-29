---
title: Compartilhar exibições personalizadas do Azure Time Series Insights com URLs parametrizadas | Microsoft Docs
description: Este artigo descreve como desenvolver URLs parametrizadas no Azure Time Series Insights para que uma exibição do cliente possa ser facilmente compartilhada.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.topic: conceptual
ms.workload: big-data
ms.date: 10/18/2019
ms.custom: seodec18
ms.openlocfilehash: e62455a0c8412a579c0fab9d7fabd6016f47dbf3
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/28/2019
ms.locfileid: "72991105"
---
# <a name="share-a-custom-view-using-a-parameterized-url"></a>Compartilhar uma exibição personalizada usando uma URL parametrizada

Para compartilhar uma exibição personalizada no Time Series Insights Explorer, você pode criar programaticamente uma URL com parâmetros da exibição personalizada.

O Time Series Insights Explorer dá suporte a parâmetros de consulta de URL para especificar exibições na experiência diretamente da URL. Por exemplo, usando apenas a URL, você pode especificar um ambiente de destino, um predicado de pesquisa e um período de tempo desejado. Quando um usuário seleciona a URL personalizada, a interface fornece um link diretamente para esse ativo no portal de Time Series Insights. Aplicam-se políticas de acesso a dados.

> [!TIP]
> * Veja a [demonstração de time Series insights](https://insights.timeseries.azure.com/samples)gratuita.
> * Leia a documentação do [Time Series insights Explorer](./time-series-insights-explorer.md) que acompanha.

## <a name="environment-id"></a>ID do Ambiente

O parâmetro `environmentId=<guid>` especifica a ID do ambiente de destino. É um componente do FQDN de acesso a dados, e você pode encontrá-lo no canto superior direito da visão geral do ambiente na portal do Azure. É tudo o que precede `env.timeseries.azure.com`.

Um parâmetro de ID do ambiente de exemplo é `?environmentId=10000000-0000-0000-0000-100000000108`.

## <a name="time"></a>Tempo

Você pode especificar valores de tempo relativos ou absolutos com uma URL parametrizada.

### <a name="absolute-time-values"></a>Valores de tempo absolutos

Para valores de tempo absolutos, use os parâmetros `from=<integer>` e `to=<integer>`.

* `from=<integer>` é um valor em milissegundos de JavaScript da hora de início para o alcance de pesquisa.
* `to=<integer>` é um valor em milissegundos de JavaScript da hora de término para o alcance de pesquisa.

Para identificar os milissegundos de JavaScript para uma data, veja [Conversor de carimbo de data/hora Epoch & Unix](https://www.freeformatter.com/epoch-timestamp-to-date-converter.html).

### <a name="relative-time-values"></a>Valores de tempo relativos

Para um valor de tempo relativo, use `relativeMillis=<value>`, onde *value* está em milissegundos de JavaScript dos dados mais recentes no back-end.

Por exemplo, `&relativeMillis=3600000` exibe os 60 minutos mais recentes dos dados.

Os valores aceitos correspondem ao menu de **tempo rápido** do time Series insights Explorer e incluem:

* `1800000` (últimos 30 minutos)
* `3600000` (últimas 60 min)
* `10800000` (últimas 3 horas)
* `21600000` (últimas 6 horas)
* `43200000` (últimas 12 horas)
* `86400000` (últimas 24 horas)
* `604800000` (últimos 7 dias)
* `2592000000` (últimas 30 horas)

### <a name="optional-parameters"></a>Parâmetros opcionais

O parâmetro `timeSeriesDefinitions=<collection of term objects>` especifica os termos de uma exibição Time Series Insights:

| . | Item de URL | Descrição |
| --- | --- | --- |
| **name** | `\<string>` | O nome do *termo*. |
| **splitBy** | `\<string>` | O nome da coluna para *dividido por*. |
| **measureName** | `\<string>` | O nome da coluna de *medida*. |
| **predicado** | `\<string>` | O cláusula *where* para filtragem do lado do servidor. |
| **Uso** | `true` | Um parâmetro opcional que especifica o uso da soma para sua medida. </br>  Observe que, se `Events` for a medida selecionada, Count será selecionado por padrão.  </br>  Se `Events` não for selecionado, a média será selecionada por padrão. |

* O par `multiChartStack=<true/false>` chave-valor habilita o empilhamento no gráfico.
* O par `multiChartSameScale=<true/false>` chave-valor habilita a mesma escala do eixo Y entre os termos dentro de um parâmetro opcional.  
* O `timeBucketUnit=<Unit>&timeBucketSize=<integer>` permite que você ajuste o controle deslizante de intervalo para fornecer uma exibição mais granular ou mais suave do gráfico.  
* O parâmetro `timezoneOffset=<integer>` permite que você defina o fuso horário do gráfico a ser exibido como um deslocamento para UTC.

| Par (es) | Descrição |
| --- | --- |
| `multiChartStack=false` | o `true` é habilitado por padrão, assim, passe `false` para Stack. |
| `multiChartStack=false&multiChartSameScale=true` | O empilhamento deve estar habilitado para usar a mesma escala de eixo Y entre os termos.  É `false` por padrão, então a passagem de ' true ' habilita essa funcionalidade. |
| `timeBucketUnit=<Unit>&timeBucketSize=<integer>` | Unidades = dias, horas, minutos, segundos e milissegundos.  Sempre colocar em maiuscula a unidade. </br> Defina o número de unidades passando o inteiro desejado ao timeBucketSize.  Observe que é possível suavizar até sete dias.  |
| `timezoneOffset=-<integer>` | O inteiro é sempre em milissegundos. </br> Observe que essa funcionalidade é um pouco diferente do que habilitamos no Time Series Insights Explorer, no qual podemos permitir que você escolha local (horário do navegador) ou UTC. |

### <a name="examples"></a>Exemplos

Para adicionar definições de série temporal a um ambiente de Time Series Insights como um parâmetro de URL, acrescente:

```plaintext
&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},
{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

Use as definições de série temporal de exemplo para:

* A ID do ambiente
* Os últimos 60 minutos de dados
* Os termos (F1PressureID, F2TempStation e F3VibrationPL) que compõem os parâmetros opcionais

Você pode construir a seguinte URL com parâmetros para uma exibição:

```plaintext
https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

> [!TIP]
> Consulte o Gerenciador ao vivo [usando a URL](https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]).

A URL acima descreve e compila a exibição do Time Series Insights Explorer:

[![termos do Time Series Insights Explorer](media/parameterized-url/url1.png)](media/parameterized-url/url1.png#lightbox)

A exibição completa (incluindo o gráfico):

[exibição de gráfico de![](media/parameterized-url/url2.png)](media/parameterized-url/url2.png#lightbox)

## <a name="next-steps"></a>Próximos passos

* Saiba como [consultar dados usando C# ](time-series-insights-query-data-csharp.md)o.

* Saiba mais sobre o [Time Series insights Explorer](./time-series-insights-explorer.md).
