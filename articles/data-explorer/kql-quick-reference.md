---
title: Referência rápida do KQL
description: Uma lista de funções KQL úteis e suas definições com exemplos de sintaxe.
author: yossi-karp
ms.author: v-yokarp
ms.reviewer: ''
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/19/2020
ms.openlocfilehash: b53890afd10a3aee131ab3897d01e6b6fdf261a6
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2020
ms.locfileid: "77429261"
---
# <a name="kql-quick-reference"></a>Referência rápida do KQL

Este artigo mostra uma lista de funções e suas descrições para ajudá-lo a começar a usar a linguagem de consulta Kusto.

| Operador/função                               | DESCRIÇÃO                           | Sintaxe                                           |
| :---------------------------------------------- | :------------------------------------ |:-------------------------------------------------|
|**Filtro/pesquisa/condição**                      |**_Localizar dados relevantes filtrando ou pesquisando_** |                      |
| [where](/azure/kusto/query/whereoperator.md)                      | Filtros em um predicado específico           | `T | where Predicate`                         |
| [onde Contains/tem](/azure/kusto/query/whereoperator.md)        | `Contains`: procura qualquer correspondência de subcadeia de caracteres <br> `Has`: procura uma palavra específica (melhor desempenho)  | `T | where col1 contains/has "[search term]"`|
| [search](/azure/kusto/query/searchoperator.md)                    | Pesquisa todas as colunas na tabela para o valor | `[TabularSource |] search [kind=CaseSensitivity] [in (TableSources)] SearchPredicate` |
| [take](/azure/kusto/query/takeoperator.md)                        | Retorna o número especificado de registros. Usar para testar uma consulta<br>**_Observação_** : `_take`_ e `_limit`_ são sinônimos. | `T | take NumberOfRows` |
| [casos](/azure/kusto/query/casefunction.md)                        | Adiciona uma instrução Condition, semelhante a if/then/ElseIf em outros sistemas. | `case(predicate_1, then_1, predicate_2, then_2, predicate_3, then_3, else)` |
| [distinct](/azure/kusto/query/distinctoperator.md)                | Produz uma tabela com a combinação distinta das colunas fornecidas da tabela de entrada | `distinct [ColumnName], [ColumnName]` |
| **Data/hora**                                   |**_Operações que usam funções de data e hora_**               |                          |
|[venceu](/azure/kusto/query/agofunction.md)                           | Retorna o deslocamento de tempo relativo à hora em que a consulta é executada. Por exemplo, `ago(1h)` é uma hora antes da leitura do relógio atual. | `ago(a_timespan)` |
| [format_datetime](/azure/kusto/query/format-datetimefunction.md)  | Retorna dados em [vários formatos de data](/azure/kusto/query/format-datetimefunction.md#supported-formats). | `format_datetime(datetime , format)` |
| [bin](/azure/kusto/query/binfunction.md)                          | Arredonda todos os valores em um período de tempo e os agrupa | `bin(value,roundTo)` |
| **Criar/remover colunas**                   |**_Adicionar ou remover colunas em uma tabela_** |                                                    |
| [imprimir](/azure/kusto/query/printoperator.md)                      | Gera uma única linha com uma ou mais expressões escalares | `print [ColumnName =] ScalarExpression [',' ...]` |
| [project](/azure/kusto/query/projectoperator.md)                  | Seleciona as colunas a serem incluídas na ordem especificada | `T | project ColumnName [= Expression] [, ...]` <br> Ou <br> `T | project [ColumnName | (ColumnName[,]) =] Expression [, ...]` |
| [project-away](/azure/kusto/query/projectawayoperator.md)         | Seleciona as colunas a serem excluídas da saída | `T | project-away ColumnNameOrPattern [, ...]` |
| [extend](/azure/kusto/query/extendoperator.md)                    | Cria uma coluna calculada e a adiciona ao conjunto de resultados | `T | extend [ColumnName | (ColumnName[, ...]) =] Expression [, ...]` |
| **Classificar e agregar conjunto de um**                 |**_Reestruturar os dados classificando-os ou agrupando-os de maneiras significativas_**|                  |
| [sort](/azure/kusto/query/sortoperator.md)                        | Classifica as linhas da tabela de entrada por uma ou mais colunas em ordem crescente ou decrescente | `T | sort by expression1 [asc|desc], expression2 [asc|desc], …` |
| [início](/azure/kusto/query/topoperator.md)                          | Retorna as primeiras N linhas do DataSet quando o conjunto de registros é classificado usando `by` | `T | top numberOfRows by expression [asc|desc] [nulls first|last]` |
| [summarize](/azure/kusto/query/summarizeoperator.md)              | Agrupa as linhas de acordo com as colunas do grupo de `by` e calcula as agregações em cada grupo | `T | summarize [[Column =] Aggregation [, ...]] [by [Column =] GroupExpression [, ...]]` |
| [contagem](/azure/kusto/query/countoperator.md)                       | Conta os registros na tabela de entrada (por exemplo, T)<br>Este operador é abreviado para `summarize count() `| `T | count` |
| [join](/azure/kusto/query/joinoperator.md)                        | Mescla as linhas de duas tabelas para formar uma nova tabela com valores correspondentes das colunas especificadas de cada tabela. Oferece suporte a uma gama completa de tipos de junção: `flouter`, `inner`, `innerunique`, `leftanti`, `leftantisemi`, `leftouter`, `leftsemi`, `rightanti`, `rightantisemi`, `rightouter`, `rightsemi` | `LeftTable | join [JoinParameters] ( RightTable ) on Attributes` |
| [union](/azure/kusto/query/unionoperator.md)                      | Usa duas ou mais tabelas e retorna todas as suas linhas | `[T1] | union [T2], [T3], …` |
| [range](/azure/kusto/query/rangeoperator.md)                      | Gera uma tabela com uma série aritmética de valores | `range columnName from start to stop step step` |
| **Formatar dados**                                 | **_Reestruturar os dados para saída de uma maneira útil_** | |
| [busca](/azure/kusto/query/lookupoperator.md)                    | Estende as colunas de uma tabela de fatos com valores pesquisados em uma tabela de dimensões | `T1 | lookup [kind = (leftouter|inner)] ( T2 ) on Attributes` |
| [MV-expandir](/azure/kusto/query/mvexpandoperator.md)               | Transforma matrizes dinâmicas em linhas (expansão de vários valores) | `T | mv-expand Column` |
| [passar](/azure/kusto/query/parseoperator.md)                      | Avalia uma expressão de cadeia de caracteres e analisa seu valor em uma ou mais colunas calculadas. Use para estruturar dados não estruturados. | `T | parse [kind=regex  [flags=regex_flags] |simple|relaxed] Expression with * (StringConstant ColumnName [: ColumnType]) *...` |
| [criar série](/azure/kusto/query/make-seriesoperator.md)          | Cria uma série de valores agregados especificados ao longo de um eixo especificado | `T | make-series [MakeSeriesParamters] [Column =] Aggregation [default = DefaultValue] [, ...] on AxisColumn from start to end step step [by [Column =] GroupExpression [, ...]]` |
| [permitindo](/azure/kusto/query/letstatement.md)                         | Associa um nome a expressões que podem se referir a seu valor associado. Os valores podem ser expressões lambda para criar funções ad hoc como parte da consulta. Use `let` para criar expressões em tabelas cujos resultados são semelhantes a uma nova tabela. | `let Name = ScalarExpression | TabularExpression | FunctionDefinitionExpression` |
| **Geral**                                     | **_Operações e funções diversas_** | |
| [invoke](/azure/kusto/query/invokeoperator.md)                    | Executa a função na tabela que recebe como entrada. | `T | invoke function([param1, param2])` |
| [avaliar pluginname](/azure/kusto/query/evaluateoperator.md)     | Avalia as extensões de linguagem de consulta (plug-ins) | `[T |] evaluate [ evaluateParameters ] PluginName ( [PluginArg1 [, PluginArg2]... )` |
| **Visualização**                               | **_Operações que exibem os dados em um formato gráfico_** | |
| [aplicar](/azure/kusto/query/renderoperator.md) | Renderiza resultados como uma saída gráfica | `T | render Visualization [with (PropertyName = PropertyValue [, ...] )]` |
