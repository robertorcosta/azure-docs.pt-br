---
title: Referência de função search.in OData
titleSuffix: Azure Cognitive Search
description: Sintaxe e documentação de referência para o uso da função search.in nas consultas de Pesquisa Cognitiva do Azure.
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
ms.openlocfilehash: b43c46599cbacaf40bc9583e364d088fa27a3ac9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113111"
---
# <a name="odata-searchin-function-in-azure-cognitive-search"></a>Função `search.in` OData na Pesquisa Cognitiva do Azure

Um cenário comum nas [expressões do filtro OData](query-odata-filter-orderby-syntax.md) é verificar se um único campo em cada documento é igual a um dos muitos valores possíveis. Por exemplo, é assim que alguns aplicativos implementam [o corte de segurança](search-security-trimming-for-azure-search.md) -- verificando um campo contendo um ou mais IDs principais contra uma lista de IDs principais que representam o usuário que emite a consulta. Uma maneira de escrever uma consulta como [`eq`](search-query-odata-comparison-operators.md) [`or`](search-query-odata-logical-operators.md) esta é usar os operadores e operadores:

    group_ids/any(g: g eq '123' or g eq '456' or g eq '789')

No entanto, há uma maneira mais `search.in` curta de escrever isso, usando a função:

    group_ids/any(g: search.in(g, '123, 456, 789'))

> [!IMPORTANT]
> Além de ser mais curto `search.in` e fácil de ler, o uso também proporciona benefícios de [desempenho](#bkmk_performance) e evita certas [limitações de tamanho dos filtros](search-query-odata-filter.md#bkmk_limits) quando há centenas ou mesmo milhares de valores para incluir no filtro. Por essa razão, recomendamos `search.in` fortemente o uso em vez de uma disjunção mais complexa de expressões de igualdade.

> [!NOTE]
> A versão 4.01 do padrão OData introduziu recentemente o `search.in` [ `in` operador,](https://docs.oasis-open.org/odata/odata/v4.01/cs01/part2-url-conventions/odata-v4.01-cs01-part2-url-conventions.html#_Toc505773230)que tem comportamento semelhante ao da função no Azure Cognitive Search. No entanto, o Azure Cognitive Search não suporta `search.in` este operador, então você deve usar a função em vez disso.

## <a name="syntax"></a>Sintaxe

A seguinte EBNF[(Formulário Backus-Naur Estendido)](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form) `search.in` define a gramática da função:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_in_call ::=
    'search.in(' variable ',' string_literal(',' string_literal)? ')'
```

Um diagrama interativo de sintaxe também está disponível:

> [!div class="nextstepaction"]
> [Diagrama de sintaxe OData para Pesquisa Cognitiva Azure](https://azuresearch.github.io/odata-syntax-diagram/#search_in_call)

> [!NOTE]
> Consulte [a referência de sintaxe de expressão OData para a Pesquisa Cognitiva do Azure](search-query-odata-syntax-reference.md) para a EBNF completa.

A `search.in` função testa se um determinado campo de string ou variável de intervalo é igual a uma de uma determinada lista de valores. A igualdade entre a variável e cada valor da lista é determinada de `eq` forma sensível a casos, da mesma forma que para o operador. Portanto, uma expressão como `search.in(myfield, 'a, b, c')` é equivalente a `myfield eq 'a' or myfield eq 'b' or myfield eq 'c'`, exceto que `search.in` produzirá um desempenho muito melhor.

Há duas sobrecargas `search.in` da função:

- `search.in(variable, valueList)`
- `search.in(variable, valueList, delimiters)`

Os parâmetros são definidos na tabela a seguir:

| Nome do parâmetro | Type | Descrição |
| --- | --- | --- |
| `variable` | `Edm.String` | Uma referência de campo de string (ou uma variável `search.in` de intervalo `any` `all` sobre um campo de coleta de strings no caso em que é usado dentro de uma ou expressão). |
| `valueList` | `Edm.String` | Uma seqüência contendo uma lista delimitada `variable` de valores para corresponder ao parâmetro. Se `delimiters` o parâmetro não for especificado, os delimitadores padrão serão espaço e comuma. |
| `delimiters` | `Edm.String` | Uma seqüência onde cada caractere é tratado `valueList` como um separador ao analisar o parâmetro. O valor padrão deste `' ,'` parâmetro é o que significa que quaisquer valores com espaços e/ou comparsas entre eles serão separados. Se você precisar usar separadores que não sejam espaços e commas porque seus valores incluem esses caracteres, você pode especificar delimitadores alternativos, como `'|'` neste parâmetro. |

<a name="bkmk_performance"></a>

### <a name="performance-of-searchin"></a>Desempenho de`search.in`

Se você usar `search.in`, espere um tempo de resposta abaixo de um segundo quando o segundo parâmetro contiver uma lista de centenas ou milhares de valores. Não há um limite explícito sobre o número `search.in`de itens para os seus itens, embora você ainda esteja limitado pelo tamanho máximo da solicitação. No entanto, a latência aumenta à medida em que cresce o número de valores.

## <a name="examples"></a>Exemplos

Encontre todos os hotéis com nome igual ao 'Sea View motel' ou 'Budget hotel'. As frases contêm espaços, que é um delimitador padrão. Você pode especificar um delimitador alternativo em aspas únicas como o terceiro parâmetro de seqüência:  

    search.in(HotelName, 'Sea View motel,Budget hotel', ',')

Encontre todos os hotéis com nome igual ao 'Sea View motel' ou 'Budget hotel' separados por '|'):

    search.in(HotelName, 'Sea View motel|Budget hotel', '|')

Encontre todos os hotéis com quartos que tenham a tag 'wifi' ou 'tub':

    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub')))

Encontre uma correspondência em frases dentro de uma coleção, como "racks de toalhas aquecidas" ou "secador de cabelo incluído" nas etiquetas.

    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))

Encontre todos os hotéis sem a tag 'motel' ou 'cabin':

    Tags/all(tag: not search.in(tag, 'motel, cabin'))

## <a name="next-steps"></a>Próximas etapas  

- [Filtros na Pesquisa Cognitiva do Azure](search-filters.md)
- [Visão geral da linguagem de expressão oData para pesquisa cognitiva do Azure](query-odata-filter-orderby-syntax.md)
- [Referência de sintaxe de expressão OData para Pesquisa Cognitiva Azure](search-query-odata-syntax-reference.md)
- [Pesquisar documentos &#40;A6Azure Cognitive Search Rest API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
