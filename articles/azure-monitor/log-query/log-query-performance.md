---
title: Escrever consultas de log eficientes no Azure Monitor | Microsoft Docs
description: Referências a recursos para aprender a escrever consultas no Log Analytics.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/17/2019
ms.openlocfilehash: 9281abaf7afd97573211ff20f15fa97b19724218
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75365284"
---
# <a name="writing-efficient-log-queries-in-azure-monitor"></a>Escrever consultas de log eficientes no Azure Monitor
Este artigo fornece recomendações para escrever consultas de log eficientes no Azure Monitor. Usando essas estratégias, você pode garantir que suas consultas sejam executadas rapidamente e com o mínimo de sobrecarga.

## <a name="scope-your-query"></a>Defina o escopo da sua consulta
Fazer sua consulta processar mais dados do que você realmente precisa pode levar a uma consulta de execução longa e geralmente resulta em dados demais em seus resultados para uma análise eficiente. Em alguns casos extremos, a consulta pode até mesmo atingir o tempo limite e falhar.

### <a name="specify-your-data-source"></a>Especificar a fonte de dados
A primeira etapa ao escrever uma consulta eficiente é limitar o escopo às fontes de dados necessárias. Especificar uma tabela é sempre melhor do que executar uma pesquisa de texto ampla, como `search *`. Para consultar uma tabela específica, inicie a sua consulta com o nome da tabela da seguinte maneira:

``` Kusto
requests | ...
```

Você pode usar [pesquisar](/azure/kusto/query/searchoperator) para pesquisar um valor em várias colunas em tabelas específicas usando uma consulta semelhante à seguinte:

``` Kusto
search in (exceptions) "The server was not found"

search in (exceptions, customEvents) "timeout"
```

Use [união](/azure/kusto/query/unionoperator) para consultar várias tabelas como a seguinte:

``` Kusto
union requests, traces | ...
```

### <a name="specify-a-time-range"></a>Especifique um intervalo de tempo
Você também deve limitar sua consulta ao intervalo de tempo dos dados de que você precisa. Por padrão, a consulta incluirá os dados coletados nas últimas 24 horas. Você pode alterar essa opção no [Seletor de intervalo de tempo](get-started-portal.md#select-a-time-range) ou adicioná-la explicitamente à sua consulta. É melhor adicionar o filtro de tempo imediatamente após o nome da tabela para que o restante da sua consulta processe apenas os dados dentro do intervalo:

``` Kusto
requests | where timestamp > ago(1h)

requests | where timestamp between (ago(1h) .. ago(30m))
```
   
### <a name="get-only-the-latest-records"></a>Obter apenas os registros mais recentes

Para retornar somente os registros mais recentes, use o operador *top* operador como a seguinte consulta que retorna os 10 registros mais recentes registrados em log na tabela *rastreamentos*:

``` Kusto
traces | top 10 by timestamp
```

   
### <a name="filter-records"></a>Filtrar registros
Para examinar apenas os logs que correspondem a uma determinada condição, use o operador *where* como na seguinte consulta, que retorna somente registros em que o valor _severityLevel_ é maior que 0:

``` Kusto
traces | where severityLevel > 0
```



## <a name="string-comparisons"></a>Comparações de cadeias de caracteres
Ao [avaliar cadeias de caracteres](/azure/kusto/query/datatypes-string-operators), você normalmente deve usar `has`, em vez de `contains`, ao procurar tokens completos. `has` é mais eficiente, pois não precisa procurar subcadeias de caracteres.

## <a name="returned-columns"></a>Colunas retornadas

Use [projeto](/azure/kusto/query/projectoperator) para restringir o conjunto de colunas processadas somente àquelas de que você precisa:

``` Kusto
traces 
| project timestamp, severityLevel, client_City 
| ...
```

Embora seja possível usar [estender](/azure/kusto/query/extendoperator) para calcular valores e criar suas próprias colunas, geralmente será mais eficiente filtrar em uma coluna de tabela.

Por exemplo, a primeira consulta abaixo que filtra em _operation\_Name_ seria mais eficiente do que a segundo, que cria uma nova coluna _assinatura_ e filtra nela:

``` Kusto
customEvents 
| where split(operation_Name, "/")[2] == "acb"

customEvents 
| extend subscription = split(operation_Name, "/")[2] 
| where subscription == "acb"
```

## <a name="using-joins"></a>Usando junções
Ao usar o operador [join](/azure/kusto/query/joinoperator), escolha a tabela com menos linhas para estar no lado esquerdo da consulta.


## <a name="next-steps"></a>Próximos passos
Para saber mais sobre as práticas recomendadas de consulta, veja [Práticas recomendadas de consulta](/azure/kusto/query/best-practices).
