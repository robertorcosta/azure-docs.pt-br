---
title: Consultas avançadas no Monitor Azure | Microsoft Docs
description: Este artigo fornece um tutorial para usar o portal do Analytics para escrever consultas no Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/15/2018
ms.openlocfilehash: 3d228c62cd2d1bcb7f4515cd698186e2ebcbe929
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670280"
---
# <a name="writing-advanced-queries-in-azure-monitor"></a>Escrever consultas avançadas no Azure Monitor

> [!NOTE]
> Você deve concluir [Comece com o Azure Monitor Log Analytics](get-started-portal.md) [e comece com as consultas](get-started-queries.md) antes de concluir esta lição.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

## <a name="reusing-code-with-let"></a>Reutilizar código com let
Use `let` para atribuir os resultados a uma variável e fazer referência a ela mais tarde na consulta:

```Kusto
// get all events that have level 2 (indicates warning level)
let warning_events=
Event
| where EventLevel == 2;
// count the number of warning events per computer
warning_events
| summarize count() by Computer 
```

Também é possível atribuir valores de constante para variáveis. Isso dá suporte a um método para configurar os parâmetros para os campos que você precisa alterar toda vez que executar a consulta. Modifique esses parâmetros conforme necessário. Por exemplo, para calcular o espaço livre em disco e a memória livre (em percentuais) em um determinado período de tempo:

```Kusto
let startDate = datetime(2018-08-01T12:55:02);
let endDate = datetime(2018-08-02T13:21:35);
let FreeDiskSpace =
Perf
| where TimeGenerated between (startDate .. endDate)
| where ObjectName=="Logical Disk" and CounterName=="Free Megabytes"
| summarize percentiles(CounterValue, 50, 75, 90, 99);
let FreeMemory =
Perf
| where TimeGenerated between (startDate .. endDate)
| where ObjectName=="Memory" and CounterName=="Available MBytes Memory"
| summarize percentiles(CounterValue, 50, 75, 90, 99);
union FreeDiskSpace, FreeMemory
```

Isso facilita alterar o início da hora de término na próxima vez que você executar a consulta.

### <a name="local-functions-and-parameters"></a>Funções e parâmetros locais
Use as instruções `let` para criar funções que podem ser usadas na mesma consulta. Por exemplo, defina uma função que usa um campo datetime (no formato UTC) e o converte em um formato dos EUA padrão. 

```Kusto
let utc_to_us_date_format = (t:datetime)
{
  strcat(getmonth(t), "/", dayofmonth(t),"/", getyear(t), " ",
  bin((t-1h)%12h+1h,1s), iff(t%24h<12h, "AM", "PM"))
};
Event 
| where TimeGenerated > ago(1h) 
| extend USTimeGenerated = utc_to_us_date_format(TimeGenerated)
| project TimeGenerated, USTimeGenerated, Source, Computer, EventLevel, EventData 
```

## <a name="print"></a>Imprimir
`print` retornará uma tabela com uma única coluna e uma única linha, mostrando o resultado de um cálculo. Isso é frequentemente usado em casos em que você precisa de um cálculo simples. Por exemplo, para localizar a hora atual em PST e adicionar uma coluna com EST:

```Kusto
print nowPst = now()-8h
| extend nowEst = nowPst+3h
```

## <a name="datatable"></a>Datatable
`datatable` permite a você definir um conjunto de dados. Você fornece um esquema e um conjunto de valores e, em seguida, redireciona a tabela para todos os outros elementos de consulta. Por exemplo, para criar uma tabela de uso de RAM e calcular seu valor médio por hora:

```Kusto
datatable (TimeGenerated: datetime, usage_percent: double)
[
  "2018-06-02T15:15:46.3418323Z", 15.5,
  "2018-06-02T15:45:43.1561235Z", 20.2,
  "2018-06-02T16:16:49.2354895Z", 17.3,
  "2018-06-02T16:46:44.9813459Z", 45.7,
  "2018-06-02T17:15:41.7895423Z", 10.9,
  "2018-06-02T17:44:23.9813459Z", 24.7,
  "2018-06-02T18:14:59.7283023Z", 22.3,
  "2018-06-02T18:45:12.1895483Z", 25.4
]
| summarize avg(usage_percent) by bin(TimeGenerated, 1h)
```

Construções de Datatable também são muito úteis durante a criação de uma tabela de pesquisa. Por exemplo, para mapear dados de tabela, como IDs de eventos da tabela _SecurityEvent_, para tipos de eventos listados em outro lugar, crie uma tabela de pesquisa com os tipos de evento usando `datatable` e faça a junção dessa datatable com os dados _SecurityEvent_:

```Kusto
let eventCodes = datatable (EventID: int, EventType:string)
[
    4625, "Account activity",
    4688, "Process action",
    4634, "Account activity",
    4672, "Access",
    4624, "Account activity",
    4799, "Access management",
    4798, "Access management",
    5059, "Key operation",
    4648, "A logon was attempted using explicit credentials",
    4768, "Access management",
    4662, "Other",
    8002, "Process action",
    4904, "Security event management",
    4905, "Security event management",
];
SecurityEvent
| where TimeGenerated > ago(1h) 
| join kind=leftouter (
  eventCodes
) on EventID
| project TimeGenerated, Account, AccountType, Computer, EventType
```

## <a name="next-steps"></a>Próximas etapas
Consulte outras lições para usar a [linguagem de consulta Kusto](/azure/kusto/query/) com os dados de log do Azure Monitor:

- [Operações da cadeia de caracteres](string-operations.md)
- [Operações de data e hora](datetime-operations.md)
- [Funções de agregação](aggregations.md)
- [Agregações avançadas](advanced-aggregations.md)
- [JSON e estruturas de dados](json-data-structures.md)
- [Junções](joins.md)
- [Gráficos](charts.md)
