---
title: Referência do operador de comparação oData
titleSuffix: Azure Cognitive Search
description: Sintaxe e documentação de referência para o uso de operadores de comparação OData (eq, ne, gt, lt, ge e le) em consultas de Pesquisa Cognitiva Do Azure.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: 62c8c93e07326e776cbe089042abc481544794bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113214"
---
# <a name="odata-comparison-operators-in-azure-cognitive-search---eq-ne-gt-lt-ge-and-le"></a>Operadores de comparação oData em `eq`Azure Cognitive Search `ne`- , , `gt`, `lt`, `ge`e`le`

A operação mais básica em uma [expressão de filtro OData](query-odata-filter-orderby-syntax.md) na Pesquisa Cognitiva do Azure é comparar um campo com um determinado valor. Dois tipos de comparação são possíveis: comparação de igualdade e comparação de faixas. Você pode usar os seguintes operadores para comparar um campo a um valor constante:

Operadores de igualdade:

- `eq`: Teste se um campo é **igual a** um valor constante
- `ne`: Teste se um campo não é **igual a** um valor constante

Operadores de alcance:

- `gt`: Teste se um campo é **maior do que** um valor constante
- `lt`: Teste se um campo é **menor que** um valor constante
- `ge`: Teste se um campo é **maior ou igual a** um valor constante
- `le`: Teste se um campo é **menor ou igual a** um valor constante

Você pode usar os operadores de intervalo em combinação com os [operadores lógicos](search-query-odata-logical-operators.md) para testar se um campo está dentro de uma determinada faixa de valores. Veja os [exemplos](#examples) mais tarde neste artigo.

> [!NOTE]
> Se preferir, você pode colocar o valor constante no lado esquerdo do operador e o nome do campo no lado direito. Para operadores de alcance, o significado da comparação é invertido. Por exemplo, se o valor constante `gt` estiver à esquerda, testará se o valor constante é maior que o campo. Você também pode usar os operadores de comparação para `geo.distance`comparar o resultado de uma função, como, com um valor. Para funções booleanas como `search.ismatch`, `true` comparando `false` o resultado com ou é opcional.

## <a name="syntax"></a>Sintaxe

O Seguinte EBNF ([Formulário Backus-Naur Estendido](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) define a gramática de uma expressão OData que usa os operadores de comparação.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
comparison_expression ::=
    variable_or_function comparison_operator constant |
    constant comparison_operator variable_or_function

variable_or_function ::= variable | function_call

comparison_operator ::= 'gt' | 'lt' | 'ge' | 'le' | 'eq' | 'ne'
```

Um diagrama interativo de sintaxe também está disponível:

> [!div class="nextstepaction"]
> [Diagrama de sintaxe OData para Pesquisa Cognitiva Azure](https://azuresearch.github.io/odata-syntax-diagram/#comparison_expression)

> [!NOTE]
> Consulte [a referência de sintaxe de expressão OData para a Pesquisa Cognitiva do Azure](search-query-odata-syntax-reference.md) para a EBNF completa.

Existem duas formas de expressões de comparação. A única diferença entre eles é se a constante aparece no lado esquerdo ou direito do operador. A expressão do outro lado do operador deve ser uma **variável** ou uma chamada de função. Uma variável pode ser um nome de campo ou uma variável de intervalo no caso de uma [expressão lambda](search-query-odata-collection-operators.md).

## <a name="data-types-for-comparisons"></a>Tipos de dados para comparações

Os tipos de dados em ambos os lados de um operador de comparação devem ser compatíveis. Por exemplo, se o lado esquerdo `Edm.DateTimeOffset`é um campo de tipo, então o lado direito deve ser uma constante de data-hora. Os tipos de dados numéricos são mais flexíveis. Você pode comparar variáveis e funções de qualquer tipo numérico com constantes de qualquer outro tipo numérico, com algumas limitações, conforme descrito na tabela a seguir.

| Tipo de função variável ou de função | Tipo de valor constante | Limitações |
| --- | --- | --- |
| `Edm.Double` | `Edm.Double` | A comparação está sujeita a [regras especiais para `NaN` ](#special-case-nan) |
| `Edm.Double` | `Edm.Int64` | Constante é convertida `Edm.Double`em, resultando em uma perda de precisão para valores de grande magnitude |
| `Edm.Double` | `Edm.Int32` | n/d |
| `Edm.Int64` | `Edm.Double` | Comparações `NaN` `-INF`com `INF` , ou não são permitidas |
| `Edm.Int64` | `Edm.Int64` | n/d |
| `Edm.Int64` | `Edm.Int32` | Constante é convertida `Edm.Int64` para antes da comparação |
| `Edm.Int32` | `Edm.Double` | Comparações `NaN` `-INF`com `INF` , ou não são permitidas |
| `Edm.Int32` | `Edm.Int64` | n/d |
| `Edm.Int32` | `Edm.Int32` | n/d |

Para comparações que não são permitidas, `Edm.Int64` como `NaN`comparar um campo de tipo com , a API Azure Cognitive Search REST retornará um erro "HTTP 400: Bad Request".

> [!IMPORTANT]
> Embora as comparações numéricas de tipo sejam flexíveis, recomendamos comparações de escrita em filtros para que o valor constante seja do mesmo tipo de dados da variável ou função a que está sendo comparado. Isso é especialmente importante ao misturar valores de ponto flutuante e inteiro, onde conversões implícitas que perdem precisão são possíveis.

<a name="special-case-nan"></a>

### <a name="special-cases-for-null-and-nan"></a>Casos especiais `null` para e`NaN`

Ao usar operadores de comparação, é importante lembrar que todos os campos de `null`não coleta na Pesquisa Cognitiva Do Azure podem potencialmente ser . A tabela a seguir mostra todos os resultados possíveis `null`para uma expressão de comparação onde ambos os lados podem ser:

| Operador | Resultado quando apenas o campo ou variável é`null` | Resultado quando apenas a constante é`null` | Resultado quando tanto o campo ou variável e a constante são`null` |
| --- | --- | --- | --- |
| `gt` | `false` | HTTP 400: Erro de solicitação ruim | HTTP 400: Erro de solicitação ruim |
| `lt` | `false` | HTTP 400: Erro de solicitação ruim | HTTP 400: Erro de solicitação ruim |
| `ge` | `false` | HTTP 400: Erro de solicitação ruim | HTTP 400: Erro de solicitação ruim |
| `le` | `false` | HTTP 400: Erro de solicitação ruim | HTTP 400: Erro de solicitação ruim |
| `eq` | `false` | `false` | `true` |
| `ne` | `true` | `true` | `false` |

Em resumo, `null` é igual apenas a si mesmo, e não é menor ou maior do que qualquer outro valor.

Se o seu índice `Edm.Double` tiver `NaN` campos de tipo e você carregar valores para esses campos, você precisará dar conta disso ao escrever filtros. A Pesquisa Cognitiva Do Azul implementa o padrão IEEE 754 para manipulação de `NaN` valores, e as comparações com tais valores produzem resultados não óbvios, como mostrado na tabela a seguir.

| Operador | Resultado quando pelo menos um operand é`NaN` |
| --- | --- |
| `gt` | `false` |
| `lt` | `false` |
| `ge` | `false` |
| `le` | `false` |
| `eq` | `false` |
| `ne` | `true` |

Em resumo, `NaN` não é igual a qualquer valor, incluindo a si mesmo.

### <a name="comparing-geo-spatial-data"></a>Comparando dados geoespaciais

Você não pode comparar diretamente `Edm.GeographyPoint` um campo de tipo com `geo.distance` um valor constante, mas você pode usar a função. Esta função retorna um `Edm.Double`valor de tipo, para que você possa compará-lo com uma constante numérica para filtrar com base na distância de coordenadas geoespaciais constantes. Veja os [exemplos](#examples) abaixo.

### <a name="comparing-string-data"></a>Comparando dados de seqüência

As cordas podem ser comparadas em `eq` `ne` filtros para correspondências exatas usando os operadores e. Essas comparações são sensíveis a maiúsculas e minúsculas.

## <a name="examples"></a>Exemplos

Coincidir documentos `Rating` onde o campo está entre 3 e 5, inclusive:

    Rating ge 3 and Rating le 5

Coincidir com `Location` documentos onde o campo está a menos de 2 quilômetros da latitude e longitude dada:

    geo.distance(Location, geography'POINT(-122.031577 47.578581)') lt 2.0

Coincidir documentos `LastRenovationDate` onde o campo é maior ou igual a 1º de janeiro de 2015, meia-noite UTC:

    LastRenovationDate ge 2015-01-01T00:00:00.000Z

Coincidir com `Details/Sku` documentos onde `null`o campo não é:

    Details/Sku ne null

Combine documentos para hotéis onde pelo menos um quarto tenha `Rooms/Type` o tipo "Deluxe Room", onde a seqüência do campo corresponde exatamente ao filtro:

    Rooms/any(room: room/Type eq 'Deluxe Room')

## <a name="next-steps"></a>Próximas etapas  

- [Filtros na Pesquisa Cognitiva do Azure](search-filters.md)
- [Visão geral da linguagem de expressão oData para pesquisa cognitiva do Azure](query-odata-filter-orderby-syntax.md)
- [Referência de sintaxe de expressão OData para Pesquisa Cognitiva Azure](search-query-odata-syntax-reference.md)
- [Pesquisar documentos &#40;A6Azure Cognitive Search Rest API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
