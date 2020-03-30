---
title: Referência rápida KQL
description: Uma lista de funções úteis do KQL e suas definições com exemplos de sintaxe.
author: orspod
ms.author: orspodek
ms.reviewer: ''
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/19/2020
ms.openlocfilehash: e7196ccccb1203ce56b2e53fa358d091374cd3f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79139072"
---
# <a name="kql-quick-reference"></a>Referência rápida KQL

Este artigo mostra uma lista de funções e suas descrições para ajudá-lo a começar a usar o Kusto Query Language.

| Operador/Função                               | Descrição                           | Sintaxe                                           |
| :---------------------------------------------- | :------------------------------------ |:-------------------------------------------------|
|**Filtro/Pesquisa/Condição**                      |**_Encontre dados relevantes filtrando ou pesquisando_** |                      |
| [where](/azure/kusto/query/whereoperator)                      | Filtros em um predicado específico           | `T | where Predicate`                         |
| [onde contém/tem](/azure/kusto/query/whereoperator)        | `Contains`: Procura qualquer correspondência de substring <br> `Has`: Procura uma palavra específica (melhor desempenho)  | `T | where col1 contains/has "[search term]"`|
| [pequisa](/azure/kusto/query/searchoperator)                    | Pesquisa todas as colunas na tabela para obter o valor | `[TabularSource |] search [kind=CaseSensitivity] [in (TableSources)] SearchPredicate` |
| [Levar](/azure/kusto/query/takeoperator)                        | Retorna o número especificado de registros. Usar para testar uma consulta<br>**_Nota_** `_take`: `_limit`_ e _ são sinônimos. | `T | take NumberOfRows` |
| [Caso](/azure/kusto/query/casefunction)                        | Adiciona uma declaração de condição, semelhante a if/then/elseif em outros sistemas. | `case(predicate_1, then_1, predicate_2, then_2, predicate_3, then_3, else)` |
| [Distintas](/azure/kusto/query/distinctoperator)                | Produz uma tabela com a combinação distinta das colunas fornecidas da tabela de entrada | `distinct [ColumnName], [ColumnName]` |
| **Data/hora**                                   |**_Operações que usam funções de data e hora_**               |                          |
|[Atrás](/azure/kusto/query/agofunction)                           | Retorna o deslocamento de tempo em relação ao momento em que a consulta é executada. Por exemplo, `ago(1h)` é uma hora antes da leitura do relógio atual. | `ago(a_timespan)` |
| [format_datetime](/azure/kusto/query/format-datetimefunction)  | Retorna dados em [vários formatos de data](/azure/kusto/query/format-datetimefunction#supported-formats). | `format_datetime(datetime , format)` |
| [Bin](/azure/kusto/query/binfunction)                          | Arredonda todos os valores em um período de tempo e os agrupa | `bin(value,roundTo)` |
| **Criar/remover colunas**                   |**_Adicione ou remova colunas em uma tabela_** |                                                    |
| [Imprimir](/azure/kusto/query/printoperator)                      | Produz uma única linha com uma ou mais expressões escalares | `print [ColumnName =] ScalarExpression [',' ...]` |
| [Projeto](/azure/kusto/query/projectoperator)                  | Seleciona as colunas a serem selecionadas na ordem especificada | `T | project ColumnName [= Expression] [, ...]` <br> Ou <br> `T | project [ColumnName | (ColumnName[,]) =] Expression [, ...]` |
| [project-away](/azure/kusto/query/projectawayoperator)         | Seleciona as colunas para excluir da saída | `T | project-away ColumnNameOrPattern [, ...]` |
| [Estender](/azure/kusto/query/extendoperator)                    | Cria uma coluna calculada e adiciona-a ao conjunto de resultados | `T | extend [ColumnName | (ColumnName[, ...]) =] Expression [, ...]` |
| **Classificar e agregar conjunto de dados**                 |**_Reestruturar os dados classificando-os ou agrupando-os de forma significativa_**|                  |
| [Tipo](/azure/kusto/query/sortoperator)                        | Classifica as linhas da tabela de entrada por uma ou mais colunas em ordem ascendente ou descendente | `T | sort by expression1 [asc|desc], expression2 [asc|desc], …` |
| [Início](/azure/kusto/query/topoperator)                          | Retorna as primeiras linhas N do conjunto de dados quando o conjunto de dados é classificado usando`by` | `T | top numberOfRows by expression [asc|desc] [nulls first|last]` |
| [Resumir](/azure/kusto/query/summarizeoperator)              | Agrupa as linhas de `by` acordo com as colunas do grupo e calcula as agregações sobre cada grupo | `T | summarize [[Column =] Aggregation [, ...]] [by [Column =] GroupExpression [, ...]]` |
| [count](/azure/kusto/query/countoperator)                       | Contage de registros na tabela de entrada (por exemplo, T)<br>Este operador é abreviação para`summarize count() `| `T | count` |
| [Juntar](/azure/kusto/query/joinoperator)                        | Mescla as linhas de duas tabelas para formar uma nova tabela, combinando valores das colunas especificadas de cada tabela. Suporta uma gama completa de `flouter` `inner`tipos `innerunique` `leftanti`de `leftantisemi` `leftouter`adesão: , , , , `leftsemi`, `rightanti`, `rightantisemi`, `rightouter``rightsemi` | `LeftTable | join [JoinParameters] ( RightTable ) on Attributes` |
| [União](/azure/kusto/query/unionoperator)                      | Pega duas ou mais mesas e retorna todas as suas linhas | `[T1] | union [T2], [T3], …` |
| [Gama](/azure/kusto/query/rangeoperator)                      | Gera uma tabela com uma série aritmética de valores | `range columnName from start to stop step step` |
| **Formatar dados**                                 | **_Reestruturar os dados para a saída de forma útil_** | |
| [Pesquisa](/azure/kusto/query/lookupoperator)                    | Estende as colunas de uma tabela de fatos com valores analisados em uma tabela de dimensões | `T1 | lookup [kind = (leftouter|inner)] ( T2 ) on Attributes` |
| [mv-expand](/azure/kusto/query/mvexpandoperator)               | Transforma matrizes dinâmicas em linhas (expansão de vários valores) | `T | mv-expand Column` |
| [Analisar](/azure/kusto/query/parseoperator)                      | Avalia uma expressão de cadeia de caracteres e analisa seu valor em uma ou mais colunas calculadas. Use para estruturar dados não estruturados. | `T | parse [kind=regex  [flags=regex_flags] |simple|relaxed] Expression with * (StringConstant ColumnName [: ColumnType]) *...` |
| [make-series](/azure/kusto/query/make-seriesoperator)          | Cria série de valores agregados especificados ao longo de um eixo especificado | `T | make-series [MakeSeriesParamters] [Column =] Aggregation [default = DefaultValue] [, ...] on AxisColumn from start to end step step [by [Column =] GroupExpression [, ...]]` |
| [Deixe](/azure/kusto/query/letstatement)                         | Vincula um nome a expressões que podem se referir ao seu valor vinculado. Os valores podem ser expressões lambda para criar funções ad-hoc como parte da consulta. Use `let` para criar expressões sobre tabelas cujos resultados parecem uma nova tabela. | `let Name = ScalarExpression | TabularExpression | FunctionDefinitionExpression` |
| **Geral**                                     | **_Operações e funções diversas_** | |
| [invoke](/azure/kusto/query/invokeoperator)                    | Executa a função na tabela que recebe como entrada. | `T | invoke function([param1, param2])` |
| [avaliar pluginNome](/azure/kusto/query/evaluateoperator)     | Avalia extensões de linguagem de consulta (plugins) | `[T |] evaluate [ evaluateParameters ] PluginName ( [PluginArg1 [, PluginArg2]... )` |
| **Visualização**                               | **_Operações que exibem os dados em um formato gráfico_** | |
| [Processar](/azure/kusto/query/renderoperator) | Renderiza resultados como uma saída gráfica | `T | render Visualization [with (PropertyName = PropertyValue [, ...] )]` |
