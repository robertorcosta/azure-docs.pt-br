---
title: Referência do filtro OData
titleSuffix: Azure Cognitive Search
description: Referência de linguagem OData e sintaxe completa usada para criar expressões de filtro em consultas de Pesquisa Cognitiva do Azure.
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
ms.openlocfilehash: b966e9cfa3ef40666dbbd62135f8f964e5eb2023
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282881"
---
# <a name="odata-filter-syntax-in-azure-cognitive-search"></a>OData $filter sintaxe na Pesquisa Cognitiva do Azure

O Azure Cognitive Search usa [expressões de filtro OData](query-odata-filter-orderby-syntax.md) para aplicar critérios adicionais a uma consulta de pesquisa além de termos de pesquisa de texto completo. Este artigo descreve a sintaxe dos filtros em detalhes. Para obter informações mais gerais sobre quais filtros são e como usá-los para realizar cenários específicos de consulta, consulte [Filtros na Pesquisa Cognitiva do Azure](search-filters.md).

## <a name="syntax"></a>Sintaxe

Um filtro na linguagem OData é uma expressão booleana, que por sua vez pode ser um dos vários tipos de expressão, como mostrado pelo seguinte EBNF ([Formulário Backus-Naur Estendido](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)):

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
boolean_expression ::=
    collection_filter_expression
    | logical_expression
    | comparison_expression
    | boolean_literal
    | boolean_function_call
    | '(' boolean_expression ')'
    | variable

/* This can be a range variable in the case of a lambda, or a field path. */
variable ::= identifier | field_path
```

Um diagrama interativo de sintaxe também está disponível:

> [!div class="nextstepaction"]
> [Diagrama de sintaxe OData para Pesquisa Cognitiva Azure](https://azuresearch.github.io/odata-syntax-diagram/#boolean_expression)

> [!NOTE]
> Consulte [a referência de sintaxe de expressão OData para a Pesquisa Cognitiva do Azure](search-query-odata-syntax-reference.md) para a EBNF completa.

Os tipos de expressões booleanas incluem:

- As expressões `any` do `all`filtro de coleta usando ou . Estes aplicam critérios de filtro aos campos de coleta. Para obter mais informações, consulte [os operadores de coleta de OData na Pesquisa Cognitiva do Azure](search-query-odata-collection-operators.md).
- Expressões lógicas que combinam outras expressões booleanas usando os operadores, `and` `or`e `not`. Para obter mais informações, consulte [operadores lógicos OData em Azure Cognitive Search](search-query-odata-logical-operators.md).
- Expressões de comparação, que comparam campos ou `eq`variáveis `ne` `gt`de `lt` `ge`intervalo `le`com valores constantes usando os operadores, e . Para obter mais informações, consulte [os operadores de comparação OData na Pesquisa Cognitiva do Azure](search-query-odata-comparison-operators.md). Expressões de comparação também são usadas para comparar distâncias entre coordenadas geoespaciais usando a `geo.distance` função. Para obter mais informações, consulte [as funções geoespaciais oData na Pesquisa Cognitiva do Azure](search-query-odata-geo-spatial-functions.md).
- Os literais `true` `false`booleanos e. Essas constantes podem ser úteis às vezes quando geram filtros programáticamente, mas de outra forma não tendem a ser usadas na prática.
- Chamadas para funções booleanas, incluindo:
  - `geo.intersects`, que testa se um determinado ponto está dentro de um determinado polígono. Para obter mais informações, consulte [as funções geoespaciais oData na Pesquisa Cognitiva do Azure](search-query-odata-geo-spatial-functions.md).
  - `search.in`, que compara uma variável de campo ou intervalo com cada valor em uma lista de valores. Para obter mais informações, consulte [a função OData `search.in` na Pesquisa Cognitiva do Azure](search-query-odata-search-in-function.md).
  - `search.ismatch`e `search.ismatchscoring`, que executam operações de pesquisa de texto completo em um contexto de filtro. Para obter mais informações, consulte [as funções de pesquisa de texto completo do OData na Pesquisa Cognitiva do Azure](search-query-odata-full-text-search-functions.md).
- Caminhos de campo ou `Edm.Boolean`variáveis de intervalo do tipo . Por exemplo, se o seu índice `IsEnabled` tiver um campo booleano `true`chamado e você quiser devolver `IsEnabled`todos os documentos onde este campo está, sua expressão de filtro pode ser apenas o nome .
- Expressões booleanas entre parênteses. O uso de parênteses pode ajudar a determinar explicitamente a ordem das operações em um filtro. Para obter mais informações sobre a precedência padrão dos operadores OData, consulte a próxima seção.

### <a name="operator-precedence-in-filters"></a>Precedência do operador em filtros

Se você escrever uma expressão de filtro sem parênteses em torno de suas subexpressões, o Azure Cognitive Search irá avaliá-lo de acordo com um conjunto de regras de precedência do operador. Essas regras são baseadas em quais operadores são usados para combinar subexpressões. A tabela a seguir lista grupos de operadores em ordem da maior e menor precedência:

| Agrupar | Operador(s) |
| --- | --- |
| Operadores lógicos | `not` |
| Operadores de comparação | `eq`, `ne`, `gt`, `lt`, `ge`, `le` |
| Operadores lógicos | `and` |
| Operadores lógicos | `or` |

Um operador mais alto na tabela acima "ligará mais fortemente" aos seus operadores do que outros operadores. Por exemplo, `and` é de `or`precedência maior do que , e os operadores de comparação são de maior precedência do que qualquer um deles, de modo que as duas expressões a seguir são equivalentes:

    Rating gt 0 and Rating lt 3 or Rating gt 7 and Rating lt 10
    ((Rating gt 0) and (Rating lt 3)) or ((Rating gt 7) and (Rating lt 10))

O `not` operador tem a maior precedência de todas - ainda maior do que os operadores de comparação. É por isso que se você tentar escrever um filtro como este:

    not Rating gt 5

Você receberá esta mensagem de erro:

    Invalid expression: A unary operator with an incompatible type was detected. Found operand type 'Edm.Int32' for operator kind 'Not'.

Esse erro acontece porque o operador `Rating` está associado apenas `Edm.Int32`ao campo, que é do tipo , e não com toda a expressão de comparação. A solução é colocar o `not` operand de parênteses:

    not (Rating gt 5)

<a name="bkmk_limits"></a>

### <a name="filter-size-limitations"></a>Limitações de tamanho de filtro

Existem limites para o tamanho e complexidade das expressões de filtro que você pode enviar para a Pesquisa Cognitiva do Azure. Os limites se baseiam aproximadamente no número de cláusulas na sua expressão de filtro. Uma boa orientação é que se você tem centenas de cláusulas, você corre o risco de exceder o limite. Recomendamos projetar seu aplicativo de forma que ele não gere filtros de tamanho ilimitado.

> [!TIP]
> O uso [da `search.in` função](search-query-odata-search-in-function.md) em vez de longas disjunções de comparações de igualdade pode ajudar a evitar o limite de cláusula do filtro, uma vez que uma chamada de função conta como uma única cláusula.

## <a name="examples"></a>Exemplos

Encontre todos os hotéis com pelo menos um quarto com uma taxa básica inferior a $200 que são classificados em ou acima de 4:

    $filter=Rooms/any(room: room/BaseRate lt 200.0) and Rating ge 4

Encontre todos os hotéis além do "Sea View Motel" que foram renovados desde 2010:

    $filter=HotelName ne 'Sea View Motel' and LastRenovationDate ge 2010-01-01T00:00:00Z

Encontre todos os hotéis que foram reformados em 2010 ou posterior. O literato de data-hora inclui informações de fuso horário para o Horário Padrão do Pacífico:  

    $filter=LastRenovationDate ge 2010-01-01T00:00:00-08:00

Encontre todos os hotéis que tenham estacionamento incluído e onde todos os quartos não são fumantes:

    $filter=ParkingIncluded and Rooms/all(room: not room/SmokingAllowed)

 \- OU -  

    $filter=ParkingIncluded eq true and Rooms/all(room: room/SmokingAllowed eq false)

Localizar todos os hotéis marcados como Luxo ou que incluem estacionamento e têm uma classificação de 5:  

    $filter=(Category eq 'Luxury' or ParkingIncluded eq true) and Rating eq 5

Encontre todos os hotéis com a tag "wifi" em pelo menos `Collection(Edm.String)` um quarto (onde cada quarto tem etiquetas armazenadas em um campo):  

    $filter=Rooms/any(room: room/Tags/any(tag: tag eq 'wifi'))

Encontre todos os hotéis com qualquer quarto:  

    $filter=Rooms/any()

Encontre todos os hotéis que não possuem quartos:

    $filter=not Rooms/any()

Encontre todos os hotéis dentro de 10 `Location` quilômetros de `Edm.GeographyPoint`um determinado ponto de referência (onde é um campo de tipo ):

    $filter=geo.distance(Location, geography'POINT(-122.131577 47.678581)') le 10

Encontre todos os hotéis dentro de um determinado `Location` viewport descrito como um polígono (onde é um campo do tipo Edm.GeographyPoint). O polígono deve ser fechado, o que significa que os conjuntos de primeiro e último ponto devem ser os mesmos. Além disso, [os pontos devem ser listados em ordem anti-horário](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

    $filter=geo.intersects(Location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')

Encontre todos os hotéis onde o campo "Descrição" é nulo. O campo será nulo se nunca foi definido, ou se foi explicitamente definido como nulo:  

    $filter=Description eq null

Encontre todos os hotéis com nome igual ao 'Sea View motel' ou 'Budget hotel'). Essas frases contêm espaços, e o espaço é um delimitador padrão. Você pode especificar um delimitador alternativo em aspas únicas como o terceiro parâmetro de seqüência:  

    $filter=search.in(HotelName, 'Sea View motel,Budget hotel', ',')

Encontre todos os hotéis com nome igual ao 'Sea View motel' ou 'Budget hotel' separados por '|'):  

    $filter=search.in(HotelName, 'Sea View motel|Budget hotel', '|')

Encontre todos os hotéis onde todos os quartos tenham a tag 'wifi' ou 'tub':

    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub'))

Encontre uma correspondência em frases dentro de uma coleção, como "racks de toalhas aquecidas" ou "secador de cabelo incluído" nas etiquetas.

    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))

Localizar documentos com as palavras "orla marítima". Essa consulta de filtro é idêntica a uma [solicitação de pesquisa](https://docs.microsoft.com/rest/api/searchservice/search-documents) com `search=waterfront`.

    $filter=search.ismatchscoring('waterfront')

Localizar documentos com a palavra "hostel" e classificação maior ou igual a 4, ou documentos com a palavra "motel" e classificação igual a 5. Esta solicitação não poderia ser `search.ismatchscoring` expressa sem a função, uma `or`vez que combina pesquisa de texto completo com operações de filtro usando .

    $filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5

Localizar documentos sem a palavra "luxo".

    $filter=not search.ismatch('luxury')

Localizar documentos com a frase "vista para o mar" ou classificação igual a 5. A consulta `search.ismatchscoring` será executada apenas em relação aos campos `HotelName` e `Description`. Documentos que correspondem apenas à segunda cláusula da disjunção serão `Rating` devolvidos também - hotéis com igual a 5. Esses documentos serão devolvidos com pontuação igual a zero para deixar claro que eles não corresponderam a nenhuma das partes marcadas da expressão.

    $filter=search.ismatchscoring('"ocean view"', 'Description,HotelName') or Rating eq 5

Encontre hotéis onde os termos "hotel" e "aeroporto" não são mais do que cinco palavras separadas na descrição, e onde todos os quartos não fumam. Essa consulta usa a [linguagem de consulta Lucene completa](query-lucene-syntax.md).

    $filter=search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and not Rooms/any(room: room/SmokingAllowed)

## <a name="next-steps"></a>Próximas etapas  

- [Filtros na Pesquisa Cognitiva do Azure](search-filters.md)
- [Visão geral da linguagem de expressão oData para pesquisa cognitiva do Azure](query-odata-filter-orderby-syntax.md)
- [Referência de sintaxe de expressão OData para Pesquisa Cognitiva Azure](search-query-odata-syntax-reference.md)
- [Pesquisar documentos &#40;A6Azure Cognitive Search Rest API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
