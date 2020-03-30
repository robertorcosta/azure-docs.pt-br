---
title: Solução de problemas Filtros de coleta de OData
titleSuffix: Azure Cognitive Search
description: Aprenda abordagens para resolver erros do filtro de coleta de OData nas consultas de pesquisa cognitiva do Azure.
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
ms.openlocfilehash: e82fa00226c964d5ba774cdf06f5b0f3898bdc55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113084"
---
# <a name="troubleshooting-odata-collection-filters-in-azure-cognitive-search"></a>Solução de problemas Filtros de coleta de OData na Pesquisa Cognitiva do Azure

Para [filtrar](query-odata-filter-orderby-syntax.md) os campos de coleta no Azure Cognitive Search, você pode usar os [ `any` operadores e `all` operadores](search-query-odata-collection-operators.md) juntamente com **expressões lambda**. Uma expressão lambda é um subfiltro que é aplicado a cada elemento de uma coleção.

Nem todas as características das expressões do filtro estão disponíveis dentro de uma expressão lambda. Quais recursos estão disponíveis difere dependendo do tipo de dados do campo de coleta que você deseja filtrar. Isso pode resultar em um erro se você tentar usar um recurso em uma expressão lambda que não é suportado nesse contexto. Se você estiver encontrando tais erros ao tentar escrever um filtro complexo sobre campos de coleta, este artigo irá ajudá-lo a solucionar o problema.

## <a name="common-collection-filter-errors"></a>Erros comuns do filtro de coleta

A tabela a seguir lista erros que você pode encontrar ao tentar executar um filtro de coleta. Esses erros acontecem quando você usa um recurso de expressões de filtro que não é suportado dentro de uma expressão lambda. Cada erro dá algumas orientações sobre como você pode reescrever seu filtro para evitar o erro. A tabela também inclui um link para a seção relevante deste artigo que fornece mais informações sobre como evitar esse erro.

| Mensagem de erro | Situação | Para obter mais informações, consulte |
| --- | --- | --- |
| A função 'ismatch' não tem parâmetros vinculados à variável 's'. Apenas as referências de campo vinculadas são suportadas dentro de expressões lambda ('qualquer' ou 'todas'). Por favor, mude seu filtro para que a função 'ismatch' esteja fora da expressão lambda e tente novamente. | Usando `search.ismatch` `search.ismatchscoring` ou dentro de uma expressão lambda | [Regras para filtrar coleções complexas](#bkmk_complex) |
| Expressão lambda inválida. Encontrei um teste para igualdade ou desigualdade onde o oposto era esperado em uma expressão lambda que itera sobre um campo de tipo Collection (Edm.String). Para 'any', use expressões da forma 'x eq y' ou 'search.in(...)'. Para 'todos', use expressões da forma 'x ne y', 'not (x eq y)' ou 'não search.in(...)'. | Filtragem em um campo de tipo`Collection(Edm.String)` | [Regras para filtrar coleções de cordas](#bkmk_strings) |
| Expressão lambda inválida. Encontrei uma forma sem suporte de expressão booleana complexa. Para 'any', use expressões que são 'ORs de ANDs', também conhecidas como Forma Normal Disjuntiva. Por exemplo: 'a e b) ou (c e d)' quando a, b, c e d são subexpressões de comparação ou igualdade. Para 'todos', use expressões que são 'ANDs de ORs', também conhecidas como Forma Normal Conjuntiva. Por exemplo: 'a ou b) e (c ou d)' quando a, b, c e d são subexpressões de comparação ou desigualdade. Exemplos de expressões de comparação: 'x gt 5', 'x le 2'. Exemplo de expressão de igualdade: 'x eq 5'. Exemplo de expressão de desigualdade: 'x ne 5'. | Filtragem em campos `Collection(Edm.DateTimeOffset)` `Collection(Edm.Double)`de `Collection(Edm.Int32)`tipo, , ou`Collection(Edm.Int64)` | [Regras para filtrar coleções comparáveis](#bkmk_comparables) |
| Expressão lambda inválida. Encontrei um uso não suportado de geo.distance() ou geo.intersects() em uma expressão lambda que itera sobre um campo de coleção tipo (Edm.GeographyPoint). Para 'any', certifique-se de comparar geo.distance() usando os operadores 'lt' ou 'le' e certifique-se de que qualquer uso de geo.intersects() não seja negado. Para 'todos', certifique-se de comparar geo.distance() usando os operadores 'gt' ou 'ge' e certifique-se de que qualquer uso de geo.intersects() seja negado. | Filtragem em um campo de tipo`Collection(Edm.GeographyPoint)` | [Regras para filtrar coleções do GeographyPoint](#bkmk_geopoints) |
| Expressão lambda inválida. Expressões booleanas complexas não são suportadas em expressões lambda que iteram sobre campos de tipo Coleção (Edm.GeographyPoint). Para 'qualquer', por favor, junte-se a subexpressões com 'ou'; 'e' não é suportado. Para 'todos', por favor, junte-se a subexpressões com 'e'; 'ou' não é suportado. | Filtragem em campos `Collection(Edm.String)` de tipo ou`Collection(Edm.GeographyPoint)` | [Regras para filtrar coleções de cordas](#bkmk_strings) <br/><br/> [Regras para filtrar coleções do GeographyPoint](#bkmk_geopoints) |
| Expressão lambda inválida. Encontrei um operador de comparação (um de 'lt', 'le', 'gt' ou 'ge'). Somente operadores de igualdade são permitidos em expressões lambda que iteram sobre campos do tipo Collection (Edm.String). Para 'any', use expressões da forma 'x eq y'. Para 'todos', use expressões da forma 'x ne y' ou 'not (x eq y)'. | Filtragem em um campo de tipo`Collection(Edm.String)` | [Regras para filtrar coleções de cordas](#bkmk_strings) |

<a name="bkmk_examples"></a>

## <a name="how-to-write-valid-collection-filters"></a>Como escrever filtros de coleta válidos

As regras para a gravação de filtros de coleta válidos são diferentes para cada tipo de dados. As seções a seguir descrevem as regras mostrando exemplos de quais recursos de filtro são suportados e quais não são:

- [Regras para filtrar coleções de cordas](#bkmk_strings)
- [Regras para filtrar coleções booleanas](#bkmk_bools)
- [Regras para filtrar coleções do GeographyPoint](#bkmk_geopoints)
- [Regras para filtrar coleções comparáveis](#bkmk_comparables)
- [Regras para filtrar coleções complexas](#bkmk_complex)

<a name="bkmk_strings"></a>

## <a name="rules-for-filtering-string-collections"></a>Regras para filtrar coleções de cordas

Dentro das expressões lambda para coleções de cordas, os `eq` `ne`únicos operadores de comparação que podem ser usados são e .

> [!NOTE]
> A Busca Cognitiva do Azure não suporta os `lt` / `le` / `gt` / `ge` operadores para cordas, seja dentro ou fora de uma expressão lambda.

O corpo `any` de um só pode testar a `all` igualdade enquanto o corpo de um só pode testar a desigualdade.

Também é possível combinar múltiplas expressões `or` através do `any`corpo `and` de um , `all`e através do corpo de um . Uma `search.in` vez que a função é `or`equivalente à combinação de verificações `any`de igualdade com , também é permitido no corpo de um . Por outro `not search.in` lado, é permitido `all`no corpo de um .

Por exemplo, essas expressões são permitidas:

- `tags/any(t: t eq 'books')`
- `tags/any(t: search.in(t, 'books, games, toys'))`
- `tags/all(t: t ne 'books')`
- `tags/all(t: not (t eq 'books'))`
- `tags/all(t: not search.in(t, 'books, games, toys'))`
- `tags/any(t: t eq 'books' or t eq 'games')`
- `tags/all(t: t ne 'books' and not (t eq 'games'))`

enquanto essas expressões não são permitidas:

- `tags/any(t: t ne 'books')`
- `tags/any(t: not search.in(t, 'books, games, toys'))`
- `tags/all(t: t eq 'books')`
- `tags/all(t: search.in(t, 'books, games, toys'))`
- `tags/any(t: t eq 'books' and t ne 'games')`
- `tags/all(t: t ne 'books' or not (t eq 'games'))`

<a name="bkmk_bools"></a>

## <a name="rules-for-filtering-boolean-collections"></a>Regras para filtrar coleções booleanas

O `Edm.Boolean` tipo suporta `eq` apenas `ne` os operadores e operadores. Como tal, não faz muito sentido permitir a combinação de tais cláusulas que verificam a mesma variável de alcance, uma `and` / `or` vez que isso sempre levaria a tautologies ou contradições.

Aqui estão alguns exemplos de filtros em coleções booleanas que são permitidos:

- `flags/any(f: f)`
- `flags/all(f: f)`
- `flags/any(f: f eq true)`
- `flags/any(f: f ne true)`
- `flags/all(f: not f)`
- `flags/all(f: not (f eq true))`

Ao contrário das coleções de cordas, as coleções booleanas não têm limites sobre qual operador pode ser usado em que tipo de expressão lambda. Ambos `eq` `ne` podem ser usados `any` no `all`corpo de ou .

Expressões como as seguintes não são permitidas para coleções booleanas:

- `flags/any(f: f or not f)`
- `flags/any(f: f or f)`
- `flags/all(f: f and not f)`
- `flags/all(f: f and f eq true)`

<a name="bkmk_geopoints"></a>

## <a name="rules-for-filtering-geographypoint-collections"></a>Regras para filtrar coleções do GeographyPoint

Valores `Edm.GeographyPoint` de tipo em uma coleção não podem ser comparados diretamente entre si. Em vez disso, devem ser `geo.distance` `geo.intersects` usados como parâmetros para as funções. A `geo.distance` função, por sua vez, deve ser comparada `gt`a `ge`um valor de distância usando um dos operadores `lt`de comparação, `le`ou . Essas regras também se aplicam aos campos Edm.GeographyPoint não-coleções.

Como coleções de `Edm.GeographyPoint` cordas, as coleções têm algumas regras para como as funções geoespaciais podem ser usadas e combinadas nos diferentes tipos de expressões lambda:

- Quais operadores de comparação `geo.distance` que você pode usar com a função depende do tipo de expressão lambda. Para `any`, você `lt` pode `le`usar apenas ou . Para `all`, você `gt` pode `ge`usar apenas ou . Você pode negar expressões `geo.distance`envolvendo, mas você terá que`geo.distance(...) lt x` `not (geo.distance(...) ge x)` mudar `geo.distance(...) le x` `not (geo.distance(...) gt x)`o operador de comparação (torna-se e se torna ).
- No corpo de `all`um, a `geo.intersects` função deve ser negada. Por outro lado, no `any`corpo `geo.intersects` de um , a função não deve ser negada.
- No corpo de `any`uma expressões geoespaciais podem `or`ser combinadas usando . No corpo de `all`um , tais expressões `and`podem ser combinadas usando .

As limitações acima existem por razões semelhantes à limitação da igualdade/desigualdade nas coleções de cordas. Consulte [Entendendo os filtros de coleta de OData no Azure Cognitive Search](search-query-understand-collection-filters.md) para obter um olhar mais aprofundado sobre essas razões.

Aqui estão alguns exemplos `Edm.GeographyPoint` de filtros em coleções que são permitidos:

- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') lt 10)`
- `locations/any(l: not (geo.distance(l, geography'POINT(-122 49)') ge 10) or geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') ge 10 and not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`

Expressões como as seguintes `Edm.GeographyPoint` não são permitidas para coleções:

- `locations/any(l: l eq geography'POINT(-122 49)')`
- `locations/any(l: not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') gt 10)`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') lt 10)`
- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') lt 10 and geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') le 10 or not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`

<a name="bkmk_comparables"></a>

## <a name="rules-for-filtering-comparable-collections"></a>Regras para filtrar coleções comparáveis

Esta seção se aplica a todos os seguintes tipos de dados:

- `Collection(Edm.DateTimeOffset)`
- `Collection(Edm.Double)`
- `Collection(Edm.Int32)`
- `Collection(Edm.Int64)`

Tipos como `Edm.Int32` `Edm.DateTimeOffset` e suportam todos os `eq`seis `ne` `lt`operadores de comparação: , , , `le`, `gt`e `ge`. Expressões lambda sobre coleções desses tipos podem conter expressões simples usando qualquer um desses operadores. Isso se aplica `any` `all`a ambos e . Por exemplo, esses filtros são permitidos:

- `ratings/any(r: r ne 5)`
- `dates/any(d: d gt 2017-08-24T00:00:00Z)`
- `not margins/all(m: m eq 3.5)`

No entanto, há limitações sobre como tais expressões de comparação podem ser combinadas em expressões mais complexas dentro de uma expressão lambda:

- Regras `any`para:
  - Simples expressões de desigualdade não podem ser combinadas com outras expressões. Por exemplo, essa expressão é permitida:
    - `ratings/any(r: r ne 5)`

    mas essa expressão não é:
    - `ratings/any(r: r ne 5 and r gt 2)`

    e, embora essa expressão seja permitida, ela não é útil porque as condições se sobrepõem:
    - `ratings/any(r: r ne 5 or r gt 7)`
  - Expressões de comparação `eq` `lt`simples `le` `gt`envolvendo `ge` , , `and` / `or`, , ou podem ser combinadas com . Por exemplo: 
    - `ratings/any(r: r gt 2 and r le 5)`
    - `ratings/any(r: r le 5 or r gt 7)`
  - As expressões de `and` comparação combinadas com (conjunções) podem ser ainda mais combinadas usando `or`. Esta forma é conhecida na lógica booleana como "[Forma Normal Disjuntiva](https://en.wikipedia.org/wiki/Disjunctive_normal_form)" (DNF). Por exemplo: 
    - `ratings/any(r: (r gt 2 and r le 5) or (r gt 7 and r lt 10))`
- Regras `all`para:
  - Expressões simples de igualdade não podem ser combinadas com outras expressões. Por exemplo, essa expressão é permitida:
    - `ratings/all(r: r eq 5)`

    mas essa expressão não é:
    - `ratings/all(r: r eq 5 or r le 2)`

    e, embora essa expressão seja permitida, ela não é útil porque as condições se sobrepõem:
    - `ratings/all(r: r eq 5 and r le 7)`
  - Expressões de comparação `ne` `lt`simples `le` `gt`envolvendo `ge` , , `and` / `or`, , ou podem ser combinadas com . Por exemplo: 
    - `ratings/all(r: r gt 2 and r le 5)`
    - `ratings/all(r: r le 5 or r gt 7)`
  - Expressões de comparação `or` combinadas com (disjunções) `and`podem ser ainda mais combinadas usando . Esta forma é conhecida na lógica booleana como "[Forma Normal Conjuntiva](https://en.wikipedia.org/wiki/Conjunctive_normal_form)" (CNF). Por exemplo: 
    - `ratings/all(r: (r le 2 or gt 5) and (r lt 7 or r ge 10))`

<a name="bkmk_complex"></a>

## <a name="rules-for-filtering-complex-collections"></a>Regras para filtrar coleções complexas

Expressões lambda sobre coleções complexas suportam uma sintaxe muito mais flexível do que expressões lambda sobre coleções de tipos primitivos. Você pode usar qualquer construção de filtro dentro de uma expressão lambda que você pode usar fora de um, com apenas duas exceções.

Primeiro, as `search.ismatch` funções e `search.ismatchscoring` não são suportadas dentro de expressões lambda. Para obter mais informações, consulte [Entendendo filtros de coleta OData na Pesquisa Cognitiva do Azure](search-query-understand-collection-filters.md).

Em segundo lugar, não é permitido referenciar campos que não estão *vinculados* à variável de intervalo (as chamadas *variáveis livres).* Por exemplo, considere as duas expressões equivalentes do filtro OData:

1. `stores/any(s: s/amenities/any(a: a eq 'parking')) and details/margin gt 0.5`
1. `stores/any(s: s/amenities/any(a: a eq 'parking' and details/margin gt 0.5))`

A primeira expressão será permitida, enquanto a `details/margin` segunda forma será rejeitada `s`porque não está vinculada à variável de intervalo .

Esta regra também se estende a expressões que têm variáveis vinculadas em um escopo externo. Tais variáveis são livres em relação ao escopo em que aparecem. Por exemplo, a primeira expressão é permitida, enquanto `s/name` a segunda expressão equivalente não é `a`permitida porque é livre em relação ao escopo da variável de intervalo :

1. `stores/any(s: s/amenities/any(a: a eq 'parking') and s/name ne 'Flagship')`
1. `stores/any(s: s/amenities/any(a: a eq 'parking' and s/name ne 'Flagship'))`

Essa limitação não deve ser um problema na prática, pois é sempre possível construir filtros de tal forma que expressões lambda contenham apenas variáveis vinculadas.

## <a name="cheat-sheet-for-collection-filter-rules"></a>Folha de trapaça para regras de filtro de coleta

A tabela a seguir resume as regras para a construção de filtros válidos para cada tipo de dados de coleta.

[!INCLUDE [Limitations on OData lambda expressions in Azure Cognitive Search](../../includes/search-query-odata-lambda-limitations.md)]

Para exemplos de como construir filtros válidos para cada caso, consulte [Como gravar filtros de coleta válidos](#bkmk_examples).

Se você escrever filtros com frequência e entender as regras dos primeiros princípios ajudaria mais do que memorizá-los, consulte [Entendendo filtros de coleta de OData na Pesquisa Cognitiva do Azure](search-query-understand-collection-filters.md).

## <a name="next-steps"></a>Próximas etapas  

- [Entendendo os filtros de coleta de OData na Pesquisa Cognitiva do Azure](search-query-understand-collection-filters.md)
- [Filtros na Pesquisa Cognitiva do Azure](search-filters.md)
- [Visão geral da linguagem de expressão oData para pesquisa cognitiva do Azure](query-odata-filter-orderby-syntax.md)
- [Referência de sintaxe de expressão OData para Pesquisa Cognitiva Azure](search-query-odata-syntax-reference.md)
- [Pesquisar documentos &#40;A6Azure Cognitive Search Rest API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
