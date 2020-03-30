---
title: Referência lógica do operador OData
titleSuffix: Azure Cognitive Search
description: Sintaxe e documentação de referência para o uso de operadores lógicos OData, e, ou, ou não, em consultas de pesquisa cognitiva do Azure.
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
ms.openlocfilehash: 2d3952f7d2adc26892cbebcd962f2ea25b86de7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113194"
---
# <a name="odata-logical-operators-in-azure-cognitive-search---and-or-not"></a>Operadores lógicos oData em Azure Cognitive Search - `and`, `or`,`not`

[Expressões do filtro OData](query-odata-filter-orderby-syntax.md) na Pesquisa Cognitiva do Azure são expressões booleanas que avaliam `true` ou `false`. Você pode escrever um filtro complexo escrevendo uma série de [filtros mais simples](search-query-odata-comparison-operators.md) e compondo-os usando os operadores lógicos da [álgebra booleana](https://en.wikipedia.org/wiki/Boolean_algebra):

- `and`: Um operador binário `true` que avalia se as subexpressões `true`esquerda e direita avaliam .
- `or`: Um operador binário `true` que avalia se uma de suas subexpressões `true`esquerda ou direita avalia .
- `not`: Um operador unary `true` que avalia se sua `false`subexpressão avalia , e vice-versa.

Estes, juntamente com os [operadores `any` `all` ](search-query-odata-collection-operators.md)de coleta e, permitem construir filtros que possam expressar critérios de pesquisa muito complexos.

## <a name="syntax"></a>Sintaxe

A Seguinte EBNF[(Extended Backus-Naur Form)](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)define a gramática de uma expressão OData que usa os operadores lógicos.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
logical_expression ::=
    boolean_expression ('and' | 'or') boolean_expression
    | 'not' boolean_expression
```

Um diagrama interativo de sintaxe também está disponível:

> [!div class="nextstepaction"]
> [Diagrama de sintaxe OData para Pesquisa Cognitiva Azure](https://azuresearch.github.io/odata-syntax-diagram/#logical_expression)

> [!NOTE]
> Consulte [a referência de sintaxe de expressão OData para a Pesquisa Cognitiva do Azure](search-query-odata-syntax-reference.md) para a EBNF completa.

Existem duas formas de expressões`and`/`or`lógicas: binária ( ), onde`not`há duas subexpressões, e unary (), onde há apenas uma. As subexpressões podem ser expressões booleanas de qualquer tipo:

- Campos ou variáveis de intervalo do tipo`Edm.Boolean`
- Funções que retornam `Edm.Boolean`valores `geo.intersects` de tipo, tais como ou`search.ismatch`
- [Expressões de comparação,](search-query-odata-comparison-operators.md)como`rating gt 4`
- [Expressões de coleção,](search-query-odata-collection-operators.md)como`Rooms/any(room: room/Type eq 'Deluxe Room')`
- Os literais `true` `false`booleanos ou .
- Outras expressões lógicas construídas usando `and`, `or`e `not`.

> [!IMPORTANT]
> Existem algumas situações em que nem todos `and` / `or`os tipos de subexpressão podem ser usados com, particularmente dentro de expressões lambda. Consulte [os operadores de coleta oData em Azure Cognitive Search](search-query-odata-collection-operators.md#limitations) para obter detalhes.

### <a name="logical-operators-and-null"></a>Operadores lógicos e`null`

A maioria das expressões booleanas, `null` como funções e comparações, não `null` pode produzir valores, e os operadores lógicos não podem ser aplicados diretamente ao literal (por exemplo, `x and null` não é permitido). No entanto, os `null`campos booleanos podem ser, `and` `or`então `not` você precisa estar ciente de como os operadores se comportam na presença de nulos. Isso é resumido na tabela `b` a seguir, `Edm.Boolean`onde está um campo do tipo :

| Expression | Resultado `b` quando é`null` |
| --- | --- |
| `b` | `false` |
| `not b` | `true` |
| `b eq true` | `false` |
| `b eq false` | `false` |
| `b eq null` | `true` |
| `b ne true` | `true` |
| `b ne false` | `true` |
| `b ne null` | `false` |
| `b and true` | `false` |
| `b and false` | `false` |
| `b or true` | `true` |
| `b or false` | `false` |

Quando um campo `b` booleano aparece sozinho em uma expressão de filtro, ele `b` se `null`comporta como se `false`tivesse sido escrito, `b eq true`então se for, a expressão avalia a . Da mesma `not b` forma, `not (b eq true)`comporta-se como , `true`por isso avalia para . Desta forma, `null` os campos se `false`comportam da mesma forma que . Isso é consistente com a forma como eles `and` se `or`comportam quando combinados com outras expressões usando e , como mostrado na tabela acima. Apesar disso, uma `false` `b eq false`comparação direta com `false`( ) ainda será avaliada para . Em outras `null` palavras, `false`não é igual a , mesmo que se comporte como ele em expressões booleanas.

## <a name="examples"></a>Exemplos

Coincidir documentos `rating` onde o campo está entre 3 e 5, inclusive:

    rating ge 3 and rating le 5

Coincidir com documentos onde `ratings` todos os elementos do campo são inferiores a 3 ou maiores que 5:

    ratings/all(r: r lt 3 or r gt 5)

Corresponda aos `location` documentos onde o campo está dentro do polígono dado, e o documento não contém o termo "público".

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))') and not search.ismatch('public')

Combine documentos para hotéis em Vancouver, Canadá, onde há uma sala de luxo com uma taxa base inferior a 160:

    Address/City eq 'Vancouver' and Address/Country eq 'Canada' and Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 160)

## <a name="next-steps"></a>Próximas etapas  

- [Filtros na Pesquisa Cognitiva do Azure](search-filters.md)
- [Visão geral da linguagem de expressão oData para pesquisa cognitiva do Azure](query-odata-filter-orderby-syntax.md)
- [Referência de sintaxe de expressão OData para Pesquisa Cognitiva Azure](search-query-odata-syntax-reference.md)
- [Pesquisar documentos &#40;A6Azure Cognitive Search Rest API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
