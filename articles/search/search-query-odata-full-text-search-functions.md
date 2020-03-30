---
title: Referência da função de pesquisa de texto completo do OData
titleSuffix: Azure Cognitive Search
description: Funções de pesquisa de texto completo oData, search.ismatch e search.ismatchscoring, nas consultas de pesquisa cognitiva do Azure.
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
ms.openlocfilehash: 06eb29f2f3245d3f4fd047fb86b2b57fb1f0989e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72793360"
---
# <a name="odata-full-text-search-functions-in-azure-cognitive-search---searchismatch-and-searchismatchscoring"></a>OData funções de pesquisa de texto completo `search.ismatch` no Azure Cognitive Search - e`search.ismatchscoring`

O Azure Cognitive Search suporta pesquisa de texto completo no `search.ismatch` contexto `search.ismatchscoring` das expressões do [filtro OData](query-odata-filter-orderby-syntax.md) através das funções e funções. Essas funções permitem combinar a pesquisa de texto completo com a filtragem booleana `search` estrita de maneiras que não são possíveis apenas usando o parâmetro de nível superior da [API de pesquisa](https://docs.microsoft.com/rest/api/searchservice/search-documents).

> [!NOTE]
> As `search.ismatch` `search.ismatchscoring` funções e funções são suportadas apenas em filtros na [API de pesquisa](https://docs.microsoft.com/rest/api/searchservice/search-documents). Eles não são suportados nas APIs [de sugerir](https://docs.microsoft.com/rest/api/searchservice/suggestions) ou [autocompletar.](https://docs.microsoft.com/rest/api/searchservice/autocomplete)

## <a name="syntax"></a>Sintaxe

A seguinte EBNF[(Formulário Backus-Naur Estendido)](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form) `search.ismatch` define `search.ismatchscoring` a gramática das funções:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_is_match_call ::=
    'search.ismatch'('scoring')?'(' search_is_match_parameters ')'

search_is_match_parameters ::=
    string_literal(',' string_literal(',' query_type ',' search_mode)?)?

query_type ::= "'full'" | "'simple'"

search_mode ::= "'any'" | "'all'"
```

Um diagrama interativo de sintaxe também está disponível:

> [!div class="nextstepaction"]
> [Diagrama de sintaxe OData para Pesquisa Cognitiva Azure](https://azuresearch.github.io/odata-syntax-diagram/#search_is_match_call)

> [!NOTE]
> Consulte [a referência de sintaxe de expressão OData para a Pesquisa Cognitiva do Azure](search-query-odata-syntax-reference.md) para a EBNF completa.

### <a name="searchismatch"></a>search.ismatch

A `search.ismatch` função avalia uma consulta de pesquisa de texto completo como parte de uma expressão de filtro. Os documentos que correspondem à consulta da pesquisa serão retornados no conjunto de resultados. As seguintes sobrecargas desta função estão disponíveis:

- `search.ismatch(search)`
- `search.ismatch(search, searchFields)`
- `search.ismatch(search, searchFields, queryType, searchMode)`

Os parâmetros são definidos na tabela a seguir:

| Nome do parâmetro | Type | Descrição |
| --- | --- | --- |
| `search` | `Edm.String` | A consulta de pesquisa (em sintaxe de consulta de Lucene [simples](query-simple-syntax.md) ou [completa).](query-lucene-syntax.md) |
| `searchFields` | `Edm.String` | Lista separada por comma de campos pesquisáveis para pesquisa; padrão para todos os campos pesquisáveis no índice. Ao [fielded search](query-lucene-syntax.md#bkmk_fields) usar a `search` pesquisa em campo no parâmetro, os especificadores de campo na consulta Lucene anulam quaisquer campos especificados neste parâmetro. |
| `queryType` | `Edm.String` | `'simple'`ou; `'full'` padrão para `'simple'`. Especifica qual idioma de consulta foi usado no parâmetro `search`. |
| `searchMode` | `Edm.String` | `'any'`ou `'all'`, padrão `'any'`para . Indica se algum ou todos os `search` termos de pesquisa no parâmetro devem ser combinados para contar o documento como uma correspondência. Ao usar os [operadores Booleanos Lucene](query-lucene-syntax.md#bkmk_boolean) no `search` parâmetro, eles terão precedência sobre este parâmetro. |

Todos os parâmetros acima são equivalentes aos [parâmetros correspondentes](https://docs.microsoft.com/rest/api/searchservice/search-documents)de solicitação de pesquisa na API de pesquisa .

A `search.ismatch` função retorna um `Edm.Boolean`valor de tipo, que permite compô-lo com outras subexpressões de filtro usando os [operadores lógicos booleanos](search-query-odata-logical-operators.md).

> [!NOTE]
> A Busca Cognitiva Do Azul `search.ismatch` `search.ismatchscoring` não suporta o uso ou dentro de expressões lambda. Isso significa que não é possível gravar filtros sobre coleções de objetos que podem correlacionar correspondências de pesquisa de texto completo com correspondências de filtros estritas no mesmo objeto. Para obter mais detalhes sobre essa limitação, bem como exemplos, consulte [Filtros de coleta de solução de problemas na Pesquisa Cognitiva do Azure](search-query-troubleshoot-collection-filters.md). Para obter informações mais detalhadas sobre por que essa limitação existe, consulte [Entendendo filtros de coleta no Azure Cognitive Search](search-query-understand-collection-filters.md).


### <a name="searchismatchscoring"></a>search.ismatchscoring

A `search.ismatchscoring` função, `search.ismatch` como a `true` função, retorna para documentos que correspondem à consulta de pesquisa de texto completo passada como parâmetro. A diferença entre eles é que a pontuação de relevância dos documentos que correspondem à consulta `search.ismatchscoring` também contribui para a pontuação geral do documento, enquanto que no caso de `search.ismatch`, a pontuação de documento não se altera. As seguintes sobrecargas dessa função estão disponíveis com parâmetros idênticos aos de `search.ismatch`:

- `search.ismatchscoring(search)`
- `search.ismatchscoring(search, searchFields)`
- `search.ismatchscoring(search, searchFields, queryType, searchMode)`

Tanto `search.ismatch` as `search.ismatchscoring` funções quanto as funções podem ser usadas na mesma expressão do filtro.

## <a name="examples"></a>Exemplos

Localizar documentos com as palavras "orla marítima". Essa consulta de filtro é idêntica a uma [solicitação de pesquisa](https://docs.microsoft.com/rest/api/searchservice/search-documents) com `search=waterfront`.

    search.ismatchscoring('waterfront')

Localizar documentos com a palavra "hostel" e classificação maior ou igual a 4, ou documentos com a palavra "motel" e classificação igual a 5. Observe que essa solicitação não pode ser expressa sem a função `search.ismatchscoring`.

    search.ismatchscoring('hostel') and Rating ge 4 or search.ismatchscoring('motel') and Rating eq 5

Localizar documentos sem a palavra "luxo".

    not search.ismatch('luxury')

Localizar documentos com a frase "vista para o mar" ou classificação igual a 5. A consulta `search.ismatchscoring` será executada apenas em relação aos campos `HotelName` e `Rooms/Description`.

Documentos que correspondem apenas à segunda cláusula da disjunção serão `Rating` devolvidos também - hotéis com igual a 5. Para deixar claro que esses documentos não correspondem a nenhuma das partes pontuadas da expressão, eles serão devolvidos com pontuação igual a zero.

    search.ismatchscoring('"ocean view"', 'Rooms/Description,HotelName') or Rating eq 5

Encontre documentos onde os termos "hotel" e "aeroporto" estão dentro de 5 palavras um do outro na descrição do hotel, e onde fumar não é permitido em pelo menos alguns dos quartos. Essa consulta usa a [linguagem de consulta Lucene completa](query-lucene-syntax.md).

    search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and Rooms/any(room: not room/SmokingAllowed)

## <a name="next-steps"></a>Próximas etapas  

- [Filtros na Pesquisa Cognitiva do Azure](search-filters.md)
- [Visão geral da linguagem de expressão oData para pesquisa cognitiva do Azure](query-odata-filter-orderby-syntax.md)
- [Referência de sintaxe de expressão OData para Pesquisa Cognitiva Azure](search-query-odata-syntax-reference.md)
- [Pesquisar documentos &#40;A6Azure Cognitive Search Rest API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
