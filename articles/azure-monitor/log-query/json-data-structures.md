---
title: Trabalhar com cadeias de caracteres nas consultas de log no Azure Monitor | Microsoft Docs
description: Este artigo fornece um tutorial para usar o Azure Monitor Log Analytics no portal Azure para consultar e analisar dados de log no Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: 8be4f318149590ff08b73fda719e99a17220ec2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670144"
---
# <a name="working-with-json-and-data-structures-in-azure-monitor-log-queries"></a>Trabalhar com JSON e estruturas de dados em consultas de log do Azure Monitor

> [!NOTE]
> Você deve concluir [Comece com o Azure Monitor Log Analytics](get-started-portal.md) e [comece com as consultas de log do Azure Monitor](get-started-queries.md) antes de concluir esta lição.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Objetos aninhados são objetos que contêm outros objetos em uma matriz ou um mapa de pares de valores-chave. Esses objetos são representados como strings JSON. Este artigo descreve como o JSON é usado para recuperar dados e analisar objetos aninhados.

## <a name="working-with-json-strings"></a>Trabalhando com cadeias de caracteres JSON
Use `extractjson` para acessar um elemento específico de JSON em um caminho conhecido. Essa função requer uma expressão de caminho que usa as convenções a seguir.

- _$_ para se referir à pasta raiz
- Use o colchete ou a notação de ponto para se referir a índices e elementos, conforme ilustrado nos exemplos a seguir.


Use parênteses para índices e pontos para separar elementos:

```Kusto
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report
| extend status = extractjson("$.hosts[0].status", hosts_report)
```

Este é o mesmo resultado usando apenas a notação de colchetes:

```Kusto
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report 
| extend status = extractjson("$['hosts'][0]['status']", hosts_report)
```

Se houver apenas um elemento, você poderá usar apenas a notação de ponto:

```Kusto
let hosts_report='{"location":"North_DC", "status":"running", "rate":5}';
print hosts_report 
| extend status = hosts_report.status
```


## <a name="working-with-objects"></a>Trabalhando com objetos

### <a name="parsejson"></a>parsejson
Para acessar vários elementos em sua estrutura JSON, é mais fácil acessá-la como um objeto dinâmico. Use `parsejson` para converter dados de texto para um objeto dinâmico. Quando convertido em um tipo dinâmico, funções adicionais podem ser usadas para analisar os dados.

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend status0=hosts_object.hosts[0].status, rate1=hosts_object.hosts[1].rate
```



### <a name="arraylength"></a>arraylength
Use `arraylength` para contar o número de elementos em uma matriz:

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend hosts_num=arraylength(hosts_object.hosts)
```

### <a name="mvexpand"></a>mvexpand
Use `mvexpand` para dividir as propriedades de um objeto em linhas separadas.

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| mvexpand hosts_object.hosts[0]
```

![mvexpand](media/json-data-structures/mvexpand.png)

### <a name="buildschema"></a>buildschema
Use `buildschema` para obter o esquema que admite todos os valores de um objeto:

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```

A saída é um esquema no formato JSON:
```json
{
    "hosts":
    {
        "indexer":
        {
            "location": "string",
            "rate": "int",
            "status": "string"
        }
    }
}
```
Esta saída descreve os nomes dos campos do objeto e seus tipos de dados correspondentes. 

Objetos aninhados podem ter diferentes esquemas, como no exemplo a seguir:

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"status":"stopped", "rate":"3", "range":100}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```


![Criar esquema](media/json-data-structures/buildschema.png)

## <a name="next-steps"></a>Próximas etapas
Confira outras lições para usar consultas de log no Azure Monitor:

- [Operações da cadeia de caracteres](string-operations.md)
- [Operações de data e hora](datetime-operations.md)
- [Funções de agregação](aggregations.md)
- [Agregações avançadas](advanced-aggregations.md)
- [Gravação de consulta avançada](advanced-query-writing.md)
- [Junções](joins.md)
- [Gráficos](charts.md)
