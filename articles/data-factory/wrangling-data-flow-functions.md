---
title: Disputas funções de transformação de fluxo de dados na Fábrica de Dados do Azure
description: Uma visão geral das funções de fluxo de dados disponíveis na Fábrica de Dados do Azure
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: e2517ec4a02a5d61fb3ce1d9ca9ffa2b5f4e8bf8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74287036"
---
# <a name="transformation-functions-in-wrangling-data-flow"></a>Funções de transformação na disputa do fluxo de dados

A disputa do fluxo de dados na Fábrica de Dados Azure permite que você faça preparação ágil de dados sem código e disputas em escala de nuvem. A disputa do fluxo de dados integra-se ao [Power Query Online](https://docs.microsoft.com/powerquery-m/power-query-m-reference) e disponibiliza funções de Consulta de Energia M para a disputa de dados via execução de faíscas. 

Atualmente, nem todas as funções de M do Power Query são suportadas para disputas de dados, apesar de estarem disponíveis durante a autoria. Ao construir seus fluxos de dados de disputa, você será solicitado com a seguinte mensagem de erro se uma função não for suportada:

`The wrangling data flow is invalid. Expression.Error: The transformation logic isn't supported. Please try a simpler expression`

Abaixo está uma lista de funções m de consulta de energia suportadas.

## <a name="column-management"></a>Gestão de Colunas

* Seleção: [Table.SelectColunas](https://docs.microsoft.com/powerquery-m/table-selectcolumns)
* Remoção: [Tabela.RemoveColunas](https://docs.microsoft.com/powerquery-m/table-removecolumns)
* Renomeação: [Table.RenameColumns](https://docs.microsoft.com/powerquery-m/table-renamecolumns), [Table.PrefixColumns,](https://docs.microsoft.com/powerquery-m/table-prefixcolumns) [Table.TransformColumnNames](https://docs.microsoft.com/powerquery-m/table-transformcolumnnames)
* Reordenação: [Table.ReorderColunas](https://docs.microsoft.com/powerquery-m/table-reordercolumns)

## <a name="row-filtering"></a>Filtragem de linhas

Use a função M [Table.SelectRows](https://docs.microsoft.com/powerquery-m/table-selectrows) para filtrar as seguintes condições:

* Igualdade e desigualdade
* Comparações numéricas, de texto e de data (mas não de DateTime)
* Informações numéricas como [Number.IsEven](https://docs.microsoft.com/powerquery-m/number-iseven)/[Odd](https://docs.microsoft.com/powerquery-m/number-iseven)
* Contenção de texto usando [Text.Contains,](https://docs.microsoft.com/powerquery-m/text-contains) [Text.StartsWith](https://docs.microsoft.com/powerquery-m/text-startswith)ou [Text.EndsWith](https://docs.microsoft.com/powerquery-m/text-endswith)
* Intervalos de datas, incluindo todas as [funções](https://docs.microsoft.com/powerquery-m/date-functions)'IsIn' Date ) 
* Combinações destes usando e, ou, ou não condições

## <a name="adding-and-transforming-columns"></a>Adicionando e Transformando Colunas

As seguintes funções M adicionam ou transformam colunas: [Table.AddColumn,](https://docs.microsoft.com/powerquery-m/table-addcolumn) [Table.TransformColumns,](https://docs.microsoft.com/powerquery-m/table-transformcolumns) [Table.ReplaceValue,](https://docs.microsoft.com/powerquery-m/table-replacevalue) [Table.DuplicateColumn](https://docs.microsoft.com/powerquery-m/table-duplicatecolumn). Abaixo estão as funções de transformação suportadas.

* Aritmética numérica
* Concatenação de texto
* Aritmética de Data e Hora (Operadores aritméticos, [Date.AddDays,](https://docs.microsoft.com/powerquery-m/date-adddays) [Date.AddMonths](https://docs.microsoft.com/powerquery-m/date-addmonths), [Date.AddQuarters,](https://docs.microsoft.com/powerquery-m/date-addquarters) [Date.AddWeeks](https://docs.microsoft.com/powerquery-m/date-addweeks), [Date.AddYears](https://docs.microsoft.com/powerquery-m/date-addyears))
* Durações podem ser usadas para data e hora aritmética, mas devem ser transformadas em outro tipo antes de serem escritas em um dissipador (Operadores de Aritmética, [#duration,](https://docs.microsoft.com/powerquery-m/sharpduration) [Duração.Dias,](https://docs.microsoft.com/powerquery-m/duration-days) [Duração.Horas,](https://docs.microsoft.com/powerquery-m/duration-hours) [Duração,Minutos,](https://docs.microsoft.com/powerquery-m/duration-minutes) [Duração.Segundos,](https://docs.microsoft.com/powerquery-m/duration-seconds) [Duração.TotalDays,](https://docs.microsoft.com/powerquery-m/duration-totaldays) [Duração.TotalHours,](https://docs.microsoft.com/powerquery-m/duration-totalhours) [Duração.Minutes,](https://docs.microsoft.com/powerquery-m/duration-totalminutes) [Duração.TotalSeconds](https://docs.microsoft.com/powerquery-m/duration-totalseconds))    
* A maioria das funções numéricas padrão, científicas e trigonométricas (Todas as funções [Operações,](https://docs.microsoft.com/powerquery-m/number-functions#operations) [Arredondamento](https://docs.microsoft.com/powerquery-m/number-functions#rounding)e [Trigonometria](https://docs.microsoft.com/powerquery-m/number-functions#trigonometry) *exceto* Número.Fatorial, Número.Permutações e Número.Combinações)
* Substituição[(Replacer.ReplaceText,](https://docs.microsoft.com/powerquery-m/replacer-replacetext) [Replacer.ReplaceValue,](https://docs.microsoft.com/powerquery-m/replacer-replacevalue) [Text.Replace,](https://docs.microsoft.com/powerquery-m/text-replace) [Text.Remove.Remove)](https://docs.microsoft.com/powerquery-m/text-remove)
* Extração de texto posicional ([Text.PositionOf](https://docs.microsoft.com/powerquery-m/text-positionof), [Text.Length,](https://docs.microsoft.com/powerquery-m/text-length) [Text.Start,](https://docs.microsoft.com/powerquery-m/text-start) [Text.End](https://docs.microsoft.com/powerquery-m/text-end), [Text.Middle,](https://docs.microsoft.com/powerquery-m/text-middle) [Text.ReplaceRange](https://docs.microsoft.com/powerquery-m/text-replacerange), [Text.RemoveRange](https://docs.microsoft.com/powerquery-m/text-removerange))
* Formatação básica do texto ([Text.Lower](https://docs.microsoft.com/powerquery-m/text-lower), [Text.Upper,](https://docs.microsoft.com/powerquery-m/text-upper) [Text.Trim](https://docs.microsoft.com/powerquery-m/text-trim)/[Start](https://docs.microsoft.com/powerquery-m/text-trimstart)/[End](https://docs.microsoft.com/powerquery-m/text-trimend), [Text.PadStart](https://docs.microsoft.com/powerquery-m/text-padstart)/[End](https://docs.microsoft.com/powerquery-m/text-padend), [Text.Reverse](https://docs.microsoft.com/powerquery-m/text-reverse))
* Funções de data/hora ([Data.Day](https://docs.microsoft.com/powerquery-m/date-day), [Data.Mês,](https://docs.microsoft.com/powerquery-m/date-month) [Data.Year](https://docs.microsoft.com/powerquery-m/date-year) [Time.Hour](https://docs.microsoft.com/powerquery-m/time-hour), [Hora, Minuto,](https://docs.microsoft.com/powerquery-m/time-minute) [Hora,Segundo](https://docs.microsoft.com/powerquery-m/time-second), [Data.DayOfWeek](https://docs.microsoft.com/powerquery-m/date-dayofweek), [Data.DayOfYear](https://docs.microsoft.com/powerquery-m/date-dayofyear), [Data.DaysInMonth](https://docs.microsoft.com/powerquery-m/date-daysinmonth))
* Se expressões (mas as filiais devem ter tipos correspondentes)
* Filtros de linha como uma coluna lógica
* Número, texto, lógica, data e data-hora constantes

<a name="mergingjoining-tables"></a>Tabelas de fusão/união
----------------------
* A consulta de energia gerará uma junta aninhada (Table.NestedJoin; os usuários também podem escrever manualmente [Table.AddJoinColumn](https://docs.microsoft.com/powerquery-m/table-addjoincolumn)).
    Em seguida, os usuários devem expandir a coluna de adesão aninhada para uma junta não aninhada (Table.ExpandTableColumn, não suportada em nenhum outro contexto).
* A função M [Table.Join](https://docs.microsoft.com/powerquery-m/table-join) pode ser escrita diretamente para evitar a necessidade de uma etapa adicional de expansão, mas o usuário deve garantir que não haja nomes de colunas duplicadas entre as tabelas unidas
* Tipos de adesão suportados: [Interno,](https://docs.microsoft.com/powerquery-m/joinkind-inner) [LeftOuter,](https://docs.microsoft.com/powerquery-m/joinkind-leftouter) [RightOuter,](https://docs.microsoft.com/powerquery-m/joinkind-rightouter) [FullOuter](https://docs.microsoft.com/powerquery-m/joinkind-fullouter)
* Tanto [value.equals](https://docs.microsoft.com/powerquery-m/value-equals) quanto [Value.NullableEquals](https://docs.microsoft.com/powerquery-m/value-nullableequals) são suportados como comparadores de igualdade chave

## <a name="group-by"></a>Agrupar por

Use [Table.Group](https://docs.microsoft.com/powerquery-m/table-group) para agregar valores.
* Deve ser usado com uma função de agregação
* Funções de agregação suportadas: [Table.RowCount,](https://docs.microsoft.com/powerquery-m/table-rowcount) [List.Sum,](https://docs.microsoft.com/powerquery-m/list-sum) [List.Count,](https://docs.microsoft.com/powerquery-m/list-count) [List.Average,](https://docs.microsoft.com/powerquery-m/list-average) [List.Min,](https://docs.microsoft.com/powerquery-m/list-min) [List.Max,](https://docs.microsoft.com/powerquery-m/list-max) [List.StandardDeviation,](https://docs.microsoft.com/powerquery-m/list-standarddeviation) [List.First](https://docs.microsoft.com/powerquery-m/list-first), [List.Last](https://docs.microsoft.com/powerquery-m/list-last)

## <a name="sorting"></a>Classificação

Use [Table.Sort](https://docs.microsoft.com/powerquery-m/table-sort) para classificar valores.

## <a name="reducing-rows"></a>Redução de linhas

Manter e remover topo, manter faixa (funções M correspondentes, apenas contagens de suporte, não condições: [Table.FirstN](https://docs.microsoft.com/powerquery-m/table-firstn), [Table.Skip,](https://docs.microsoft.com/powerquery-m/table-skip) [Table.RemoveFirstN](https://docs.microsoft.com/powerquery-m/table-removefirstn), [Table.Range,](https://docs.microsoft.com/powerquery-m/table-range) [Table.MinN,](https://docs.microsoft.com/powerquery-m/table-minn) [Table.MaxN](https://docs.microsoft.com/powerquery-m/table-maxn))

## <a name="known-unsupported-functions"></a>Funções não suportadas conhecidas

| Função | Status |
| -- | -- |
| Table.PromoteHeaders | Sem suporte. O mesmo resultado pode ser alcançado definindo "Primeira linha como cabeçalho" no conjunto de dados. |
| Table.CombineColumns | Este é um cenário comum que não é suportado diretamente, mas pode ser alcançado adicionando uma nova coluna que concatena duas colunas dadas.  Por exemplo, Table.AddColumn (RemoveEmailColumn, "Name", cada [Primeiro Nome] & " & [Sobrenome]) |
| Table.TransformColumnTypes | Isso é suportado na maioria dos casos. Os seguintes cenários não são suportados: transformando string para tipo de moeda, transformando string para tipo de tempo, transformando string para tipo de porcentagem. |
| Table.NestedJoin | Apenas fazer uma adesão resultará em um erro de validação. As colunas devem ser expandidas para que funcione. |
| Table.Distinct | Não é suportado o suporte de linhas duplicadas. |
| Table.RemoveLastN | Não há suporte para remover as linhas inferiores. |
| Table.RowCount | Não suportado, mas pode ser alcançado com uma coluna de adição com todas as células vazias (a coluna de condição pode ser usada) e, em seguida, usando o grupo por nessa coluna. Table.Group é suportado. | 
| Manuseio de erro de nível de linha | O manuseio de erro de nível de linha não é suportado no momento. Por exemplo, para filtrar valores não numéricos de uma coluna, uma abordagem seria transformar a coluna de texto em um número. Cada célula que não se transformar estará em estado de erro e precisará ser filtrada. Este cenário não é possível na disputa do fluxo de dados. |
| Table.Transpose | Sem suporte |
| Table.Pivot | Sem suporte |

## <a name="next-steps"></a>Próximas etapas

Aprenda a [criar um fluxo de dados de disputa.](wrangling-data-flow-tutorial.md)