---
title: Referência do operador de coleta de oData
titleSuffix: Azure Cognitive Search
description: Ao criar expressões de filtro em consultas de pesquisa cognitiva do Azure, use operadores "qualquer" e "todos" em expressões lambda quando o filtro estiver em um campo de coleta ou complexo.
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
ms.openlocfilehash: 54ddc8222816831b5b436297bbb1b40d03230f0c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113240"
---
# <a name="odata-collection-operators-in-azure-cognitive-search---any-and-all"></a>Operadores de coleta de OData `any` em Azure Cognitive Search - e`all`

Ao escrever uma [expressão de filtro OData](query-odata-filter-orderby-syntax.md) para usar com o Azure Cognitive Search, muitas vezes é útil filtrar em campos de coleta. Você pode conseguir `any` isso `all` usando os operadores e operadores.

## <a name="syntax"></a>Sintaxe

A Seguinte EBNF[(Extended Backus-Naur Form)](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)define a gramática `any` `all`de uma expressão OData que usa ou .

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
collection_filter_expression ::=
    field_path'/all(' lambda_expression ')'
    | field_path'/any(' lambda_expression ')'
    | field_path'/any()'

lambda_expression ::= identifier ':' boolean_expression
```

Um diagrama interativo de sintaxe também está disponível:

> [!div class="nextstepaction"]
> [Diagrama de sintaxe OData para Pesquisa Cognitiva Azure](https://azuresearch.github.io/odata-syntax-diagram/#collection_filter_expression)

> [!NOTE]
> Consulte [a referência de sintaxe de expressão OData para a Pesquisa Cognitiva do Azure](search-query-odata-syntax-reference.md) para a EBNF completa.

Existem três formas de expressão que filtram coleções.

- Os dois primeiros iteram sobre um campo de coleta, aplicando um predicado dado na forma de uma expressão lambda a cada elemento da coleção.
  - Uma expressão `all` `true` usando retorna se o predicado for verdadeiro para cada elemento da coleção.
  - Uma expressão `any` `true` usando retorna se o predicado for verdadeiro para pelo menos um elemento da coleção.
- A terceira forma de `any` filtro de coleta usa sem uma expressão lambda para testar se um campo de coleta está vazio. Se a coleção tiver algum `true`elemento, ela retorna. Se a coleção estiver `false`vazia, ela retorna.

Uma **expressão lambda** em um filtro de coleção é como o corpo de um loop em uma linguagem de programação. Define uma variável, chamada **variável de intervalo,** que contém o elemento atual da coleção durante a iteração. Também define outra expressão booleana que é o critério de filtro para aplicar à variável de alcance para cada elemento da coleção.

## <a name="examples"></a>Exemplos

Coincidir com `tags` documentos cujo campo contém exatamente a string "wifi":

    tags/any(t: t eq 'wifi')

Coincidir documentos onde cada `ratings` elemento do campo cai entre 3 e 5, inclusive:

    ratings/all(r: r ge 3 and r le 5)

Coincidir com documentos onde qualquer uma `locations` das coordenadas geográficas no campo está dentro do polígono dado:

    locations/any(loc: geo.intersects(loc, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))

Coincidir com `rooms` documentos onde o campo está vazio:

    not rooms/any()

Combine documentos onde para `rooms/amenities` todos os cômodos, o campo contém "tv" e `rooms/baseRate` é inferior a 100:

    rooms/all(room: room/amenities/any(a: a eq 'tv') and room/baseRate lt 100.0)

## <a name="limitations"></a>Limitações

Nem todas as características das expressões do filtro estão disponíveis dentro do corpo de uma expressão lambda. As limitações diferem dependendo do tipo de dados do campo de coleta que você deseja filtrar. A tabela a seguir resume as limitações.

[!INCLUDE [Limitations on OData lambda expressions in Azure Cognitive Search](../../includes/search-query-odata-lambda-limitations.md)]

Para obter mais detalhes sobre essas limitações, bem como exemplos, consulte [filtros de coleta de solução de problemas na Pesquisa Cognitiva do Azure](search-query-troubleshoot-collection-filters.md). Para obter informações mais detalhadas sobre por que essas limitações existem, consulte [Entendendo filtros de coleta no Azure Cognitive Search](search-query-understand-collection-filters.md).

## <a name="next-steps"></a>Próximas etapas  

- [Filtros na Pesquisa Cognitiva do Azure](search-filters.md)
- [Visão geral da linguagem de expressão oData para pesquisa cognitiva do Azure](query-odata-filter-orderby-syntax.md)
- [Referência de sintaxe de expressão OData para Pesquisa Cognitiva Azure](search-query-odata-syntax-reference.md)
- [Pesquisar documentos &#40;A6Azure Cognitive Search Rest API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
