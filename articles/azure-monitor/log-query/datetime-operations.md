---
title: Trabalhar com valores de data e hora em consultas de log do Azure Monitor | Microsoft Docs
description: Descreve como trabalhar com dados de data e hora em consultas de log do Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: ea7c98a1b5b4059c5fea0cf1e8ea2ff5ef08d9d1
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77655371"
---
# <a name="working-with-date-time-values-in-azure-monitor-log-queries"></a>Trabalhar com valores de data e hora em consultas de log do Azure Monitor

> [!NOTE]
> Você deve concluir [Introdução ao portal do Analytics](get-started-portal.md) e [Introdução às consultas](get-started-queries.md) antes de concluir esta lição.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Este artigo descreve como trabalhar com dados de data e hora em consultas de log do Azure Monitor.


## <a name="date-time-basics"></a>Noções básicas de data/hora
A linguagem de consulta Kusto tem dois tipos de dados principais associados a datas e horas: datetime e timespan. Todas as datas são expressas em UTC. Embora haja suporte para vários formatos de datetime, o formato ISO8601 é preferencial. 

Intervalos de tempo (timespans) são expressos como um decimal seguido por uma unidade de tempo:

|abreviação   | unidade de tempo    |
|:---|:---|
|d           | dia          |
|h           | hour         |
|m           | minute       |
|s           | second       |
|ms          | milissegundo  |
|microssegundo | microssegundo  |
|tique        | nanossegundo   |

Datetimes pode ser criados ao converter uma cadeia de caracteres usando o operador `todatetime`. Por exemplo, para examinar as pulsações da VM enviadas em um período de tempo específico, use o operador `between` para especificar um intervalo de tempo.

```Kusto
Heartbeat
| where TimeGenerated between(datetime("2018-06-30 22:46:42") .. datetime("2018-07-01 00:57:27"))
```

Outro cenário comum é comparar uma datetime à data e hora atual. Por exemplo, para ver todas as pulsações durante os últimos dois minutos, você pode usar o operador `now` junto com um timespan que representa dois minutos:

```Kusto
Heartbeat
| where TimeGenerated > now() - 2m
```

Um atalho também está disponível para esta função:
```Kusto
Heartbeat
| where TimeGenerated > now(-2m)
```

No entanto o método mais curto e mais legível é usar o operador `ago`:
```Kusto
Heartbeat
| where TimeGenerated > ago(2m)
```

Suponha que, em vez de saber a hora de início e de término, você sabe a hora de início e a duração. É possível reescrever a consulta da seguinte maneira:

```Kusto
let startDatetime = todatetime("2018-06-30 20:12:42.9");
let duration = totimespan(25m);
Heartbeat
| where TimeGenerated between(startDatetime .. (startDatetime+duration) )
| extend timeFromStart = TimeGenerated - startDatetime
```

## <a name="converting-time-units"></a>Converter unidades de tempo
Talvez você queira expressar um datetime ou timespan em uma unidade de tempo diferente do padrão. Por exemplo, se estiver examinando os eventos de erro dos últimos 30 minutos e precisar de uma coluna calculada que mostra há quanto tempo o evento ocorreu:

```Kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated 
```

A coluna `timeAgo` contém valores, como: "00:09:31.5118992", que significa que elas são formatadas como hh:mm:ss.fffffff. Se você deseja formatar esses valores para o `numver` de minutos transcorridos desde a hora de início, divida esse valor por "1 minuto":

```Kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated
| extend timeAgoMinutes = timeAgo/1m 
```


## <a name="aggregations-and-bucketing-by-time-intervals"></a>Agregações e particionamento por intervalos de tempo
Outro cenário muito comum é a necessidade de obter estatísticas durante certo período de tempo em um intervalo de agregação específico. Para esse cenário, um operador `bin` pode ser usado como parte de uma cláusula de resumo.

Use a consulta a seguir para obter o número de eventos que ocorreram a cada 5 minutos durante a última meia hora:

```Kusto
Event
| where TimeGenerated > ago(30m)
| summarize events_count=count() by bin(TimeGenerated, 5m) 
```

Essa consulta produz a seguinte tabela:  

|TimeGenerated(UTC)|events_count|
|--|--|
|2018-08-01T09:30:00.000|54|
|2018-08-01T09:35:00.000|41|
|2018-08-01T09:40:00.000|42|
|2018-08-01T09:45:00.000|41|
|2018-08-01T09:50:00.000|41|
|2018-08-01T09:55:00.000|16|

Outra maneira de criar buckets de resultados é usar funções, tais como `startofday`:

```Kusto
Event
| where TimeGenerated > ago(4d)
| summarize events_count=count() by startofday(TimeGenerated) 
```

Essa consulta produz os seguintes resultados:

|timestamp|count_|
|--|--|
|2018-07-28T00:00:00.000|7\.136|
|2018-07-29T00:00:00.000|12.315|
|2018-07-30T00:00:00.000|16.847|
|2018-07-31T00:00:00.000|12.616|
|2018-08-01T00:00:00.000|5\.416|


## <a name="time-zones"></a>Fusos horários
Como todos os valores de datetime são expressos em UTC, costuma ser útil convertê-los no fuso horário local. Por exemplo, use este cálculo para converter horas UTC em horas PST:

```Kusto
Event
| extend localTimestamp = TimeGenerated - 8h
```

## <a name="related-functions"></a>Funções relacionadas

| Categoria | Função |
|:---|:---|
| Converter tipos de dados | [ToDateTime](/azure/kusto/query/todatetimefunction)  [ToTimeSpan](/azure/kusto/query/totimespanfunction)  |
| Valor arredondado para o tamanho do compartimento | [bin](/azure/kusto/query/binfunction) |
| Obter uma data ou hora específica | [há](/azure/kusto/query/agofunction) [agora](/azure/kusto/query/nowfunction)   |
| Obter parte do valor | [datetime_part](/azure/kusto/query/datetime-partfunction) [GetMonth](/azure/kusto/query/getmonthfunction) [MonthOfYear](/azure/kusto/query/monthofyearfunction) [getyear](/azure/kusto/query/getyearfunction) [DayOfMonth](/azure/kusto/query/dayofmonthfunction) [DayOfWeek](/azure/kusto/query/dayofweekfunction) [DayOfYear](/azure/kusto/query/dayofyearfunction) [WeekOfYear](/azure/kusto/query/weekofyearfunction) |
| Obter um valor de data relativo  | [endofday](/azure/kusto/query/endofdayfunction) [endofweek](/azure/kusto/query/endofweekfunction) [ENDOFMONTH](/azure/kusto/query/endofmonthfunction) [ENDOFYEAR](/azure/kusto/query/endofyearfunction) [startofday](/azure/kusto/query/startofdayfunction) [startofweek](/azure/kusto/query/startofweekfunction) [STARTOFMONTH](/azure/kusto/query/startofmonthfunction) [STARTOFYEAR](/azure/kusto/query/startofyearfunction) |

## <a name="next-steps"></a>Próximas etapas
Consulte outras lições para usar a [linguagem de consulta Kusto](/azure/kusto/query/) com os dados de log do Azure Monitor:

- [Operações de cadeia de caracteres](string-operations.md)
- [Funções de agregação](aggregations.md)
- [Agregações avançadas](advanced-aggregations.md)
- [JSON e estruturas de dados](json-data-structures.md)
- [Gravação de consulta avançada](advanced-query-writing.md)
- [Junções](joins.md)
- [Gráficos](charts.md)
