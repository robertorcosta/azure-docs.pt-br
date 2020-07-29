---
title: Agregações nas consultas de log do Azure Monitor | Microsoft Docs
description: Descreve as funções de agregação nas consultas de log do Azure Monitor que oferecem maneiras úteis de analisar seus dados.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: d164c53e7e2be55f3cede389901a256ba388808d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "77670297"
---
# <a name="aggregations-in-azure-monitor-log-queries"></a>Agregações nas consultas de log do Azure Monitor

> [!NOTE]
> Você deve concluir [Introdução ao portal do Analytics](get-started-portal.md) e [Introdução às consultas](get-started-queries.md) antes de concluir esta lição.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Este artigo descreve as funções de agregação nas consultas de log do Azure Monitor que oferecem maneiras úteis de analisar seus dados. These functions all work with the `summarize` operator that produces a  table with aggregated results of the input table.

## <a name="counts"></a>Counts

### <a name="count"></a>count
Conte o número de linhas no conjunto de resultados depois que os filtros forem aplicados. O exemplo a seguir retorna o número total de linhas na tabela _Perf_ dos últimos 30 minutos. O resultado é retornado em uma coluna chamada *count_*, a menos que você atribua um nome específico a ele:


```Kusto
Perf
| where TimeGenerated > ago(30m) 
| summarize count()
```

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| summarize num_of_records=count() 
```

Uma visualização de cronograma pode ser útil para ver uma tendência ao longo do tempo:

```Kusto
Perf 
| where TimeGenerated > ago(30m) 
| summarize count() by bin(TimeGenerated, 5m)
| render timechart
```

A saída deste exemplo mostra a linha de tendência de contagem de registros perf em intervalos de 5 minutos:

![Tendência de contagem](media/aggregations/count-trend.png)


### <a name="dcount-dcountif"></a>DCount, dcountif
Use `dcount`e`dcountif` para contar valores distintos em uma coluna específica. A consulta a seguir avalia quantos computadores distintos enviam pulsações na última hora:

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize dcount(Computer)
```

Para contar apenas os computadores Linux que enviaram heartbeats, use `dcountif`:

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize dcountif(Computer, OSType=="Linux")
```

### <a name="evaluating-subgroups"></a>Avaliando subgrupos
Para executar uma contagem ou outras agregações em subgrupos em seus dados, use a `by` palavra-chave. Por exemplo, para contar o número de computadores Linux distintos que enviaram pulsações em cada país/região:

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize distinct_computers=dcountif(Computer, OSType=="Linux") by RemoteIPCountry
```

|RemoteIPCountry  | distinct_computers  |
------------------|---------------------|
|Estados Unidos    | 19                  |
|Canadá           | 3                   |
|Irlanda          | 0                   |
|Reino Unido   | 0                   |
|Países Baixos      | 2                   |


Para analisar subgrupos ainda menores de seus dados, adicione nomes de coluna adicionais à seção`by`. Por exemplo, talvez você queira contar os computadores distintos de cada país/região por OSType:

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize distinct_computers=dcountif(Computer, OSType=="Linux") by RemoteIPCountry, OSType
```

## <a name="percentiles-and-variance"></a>Percentis e Variação
Ao avaliar valores numéricos, uma prática comum é calculá-los usando `summarize avg(expression)`. As médias são afetadas por valores extremos que caracterizam apenas alguns casos. Para resolver esse problema, você pode usar funções menos sensíveis, como `median` ou `variance`.

### <a name="percentile"></a>Percentil
Para encontrar o valor mediano, use a função `percentile` com um valor para especificar o percentil:

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 50) by Computer
```

Você também pode especificar diferentes percentis para obter um resultado agregado para cada um:

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 25, 50, 75, 90) by Computer
```

Isso pode mostrar que algumas CPUs de computadores têm valores medianos semelhantes, mas enquanto algumas estão em torno da mediana, outros computadores relataram valores de CPU muito menores e mais altos, o que significa que experimentaram picos.

### <a name="variance"></a>Variance
Para avaliar diretamente a variância de um valor, use os métodos de desvio padrão e variância:

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), variance(CounterValue) by Computer
```

Uma boa maneira de analisar a estabilidade do uso da CPU é combinar stdev com o cálculo mediano:

```Kusto
Perf
| where TimeGenerated > ago(130m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), percentiles(CounterValue, 50) by Computer
```

Consulte outras lições para usar a [linguagem de consulta Kusto](/azure/kusto/query/) com os dados de log do Azure Monitor:

- [Operações de cadeia de caracteres](string-operations.md)
- [Operações de data e hora](datetime-operations.md)
- [Agregações avançadas](advanced-aggregations.md)
- [JSON e estruturas de dados](json-data-structures.md)
- [Gravação de consulta avançada](advanced-query-writing.md)
- [Junções](joins.md)
- [Gráficos](charts.md)
