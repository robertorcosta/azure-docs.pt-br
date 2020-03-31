---
title: Visão geral do idioma OData
titleSuffix: Azure Cognitive Search
description: Visão geral do idioma OData para filtros, selecionar e order-by para consultas de pesquisa cognitiva do Azure.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/10/2020
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
ms.openlocfilehash: f3a1be435e297ab4a9ba7f8bfbd5f3ce3451d8a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77153869"
---
# <a name="odata-language-overview-for-filter-orderby-and-select-in-azure-cognitive-search"></a>Visão geral do `$filter`idioma `$orderby`OData para , e `$select` na Pesquisa Cognitiva do Azure

O Azure Cognitive Search suporta um subconjunto da sintaxe de expressão OData para **expressões $filter,** **$orderby**e **$select.** As expressões Filter são avaliadas durante a análise da consulta, restringindo a pesquisa a campos específicos ou adicionando critérios de correspondência usados durante as verificações de índice. As expressões de ordem por ordem são aplicadas como uma etapa de pós-processamento sobre um resultado definido para classificar os documentos que são devolvidos. As expressões selecionadas determinam quais campos de documento estão incluídos no conjunto de resultados. A sintaxe dessas expressões é distinta da sintaxe de consulta [simples](query-simple-syntax.md) ou [completa](query-lucene-syntax.md) que é usada no parâmetro **de pesquisa,** embora haja alguma sobreposição na sintaxe para campos de referência.

Este artigo fornece uma visão geral da linguagem de expressão OData usada em filtros, pedidos e expressões selecionadas. A linguagem é apresentada "de baixo para cima", começando com os elementos mais básicos e construindo sobre eles. A sintaxe de nível superior para cada parâmetro é descrita em um artigo separado:

- [sintaxe $filter](search-query-odata-filter.md)
- [sintaxe $orderby](search-query-odata-orderby.md)
- [$select sintaxe](search-query-odata-select.md)

As expressões OData variam de simples a altamente complexas, mas todas compartilham elementos comuns. As partes mais básicas de uma expressão OData na Pesquisa Cognitiva do Azure são:

- **Caminhos de**campo, que se referem a campos específicos do seu índice.
- **Constantes,** que são valores literais de um determinado tipo de dados.

> [!NOTE]
> A terminologia na Pesquisa Cognitiva do Azure difere do [padrão OData](https://www.odata.org/documentation/) em alguns aspectos. O que chamamos de **campo** no Azure Cognitive Search é chamado de **propriedade** em OData e, da mesma forma, para **caminho de campo** versus caminho de **propriedade**. Um **índice** contendo **documentos** no Azure Cognitive Search é referido de forma mais geral no OData como um conjunto **de entidades** contendo **entidades**. A terminologia da Pesquisa Cognitiva do Azure é usada ao longo desta referência.

## <a name="field-paths"></a>Caminhos de campo

A EBNF[(Extended Backus-Naur Form)](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)define a gramática dos caminhos de campo.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
field_path ::= identifier('/'identifier)*

identifier ::= [a-zA-Z_][a-zA-Z_0-9]*
```

Um diagrama interativo de sintaxe também está disponível:

> [!div class="nextstepaction"]
> [Diagrama de sintaxe OData para Pesquisa Cognitiva Azure](https://azuresearch.github.io/odata-syntax-diagram/#field_path)

> [!NOTE]
> Consulte [a referência de sintaxe de expressão OData para a Pesquisa Cognitiva do Azure](search-query-odata-syntax-reference.md) para a EBNF completa.

Um caminho de campo é composto por um ou mais **identificadores separados** por cortes. Cada identificador é uma seqüência de caracteres que deve começar com uma letra ASCII ou sublinhado, e conter apenas letras ASCII, dígitos ou sublinhados. As letras podem ser maiúsculas ou minúsculas.

Um identificador pode se referir ao nome de um campo, ou a`any` uma `all`variável de **intervalo** no contexto de uma expressão de [coleção](search-query-odata-collection-operators.md) ( ou ) em um filtro. Uma variável de intervalo é como uma variável de loop que representa o elemento atual da coleção. Para coleções complexas, essa variável representa um objeto, e é por isso que você pode usar caminhos de campo para se referir a subcampos da variável. Isso é análogo à notação de pontuação em muitas linguagens de programação.

Exemplos de caminhos de campo são mostrados na tabela a seguir:

| Caminho de campo | Descrição |
| --- | --- |
| `HotelName` | Refere-se a um campo de alto nível do índice |
| `Address/City` | Refere-se `City` ao subcampo de um campo complexo no índice; `Address` é do `Edm.ComplexType` tipo neste exemplo |
| `Rooms/Type` | Refere-se `Type` ao subcampo de um campo de coleta complexo no índice; `Rooms` é do `Collection(Edm.ComplexType)` tipo neste exemplo |
| `Stores/Address/Country` | Refere-se `Country` ao subcampo `Address` do subcampo de um campo de coleta complexo no índice; `Stores` é do `Collection(Edm.ComplexType)` `Address` tipo e `Edm.ComplexType` é de tipo neste exemplo |
| `room/Type` | Refere-se `Type` ao subcampo `room` da variável de intervalo, por exemplo, na expressão do filtro`Rooms/any(room: room/Type eq 'deluxe')` |
| `store/Address/Country` | Refere-se `Country` ao subcampo `Address` do subcampo `store` da variável de intervalo, por exemplo, na expressão do filtro`Stores/any(store: store/Address/Country eq 'Canada')` |

O significado de um caminho de campo difere dependendo do contexto. Nos filtros, um caminho de campo refere-se ao valor de uma *única instância* de um campo no documento atual. Em outros contextos, como **$orderby,** **$select,** ou em [busca em campo na sintaxe lucene completa,](query-lucene-syntax.md#bkmk_fields)um caminho de campo refere-se ao próprio campo. Essa diferença tem algumas consequências para a forma como você usa caminhos de campo em filtros.

Considere o `Address/City`caminho de campo. Em um filtro, isso se refere a uma única cidade para o documento atual, como "São Francisco". Em contraste, `Rooms/Type` refere-se ao `Type` subcampo para muitos quartos (como "padrão" para o primeiro quarto, "deluxe" para o segundo quarto, e assim por diante). Uma `Rooms/Type` vez que não se refere a `Type`uma única *instância* do subcampo, ele não pode ser usado diretamente em um filtro. Em vez disso, para filtrar o tipo de quarto, você usaria uma [expressão lambda](search-query-odata-collection-operators.md) com uma variável de alcance, como esta:

    Rooms/any(room: room/Type eq 'deluxe')

Neste exemplo, a `room` variável de `room/Type` intervalo aparece no caminho de campo. Dessa forma, `room/Type` refere-se ao tipo da sala atual no documento atual. Esta é uma única `Type` instância do subcampo, por isso pode ser usado diretamente no filtro.

### <a name="using-field-paths"></a>Usando caminhos de campo

Os caminhos de campo são usados em muitos parâmetros das APIs de descanso de pesquisa cognitiva do [Azure](https://docs.microsoft.com/rest/api/searchservice/). A tabela a seguir lista todos os lugares onde eles podem ser usados, além de quaisquer restrições ao seu uso:

| API | Nome do parâmetro | Restrições |
| --- | --- | --- |
| [Criar](https://docs.microsoft.com/rest/api/searchservice/create-index) ou [atualizar](https://docs.microsoft.com/rest/api/searchservice/update-index) índice | `suggesters/sourceFields` | Nenhum |
| [Criar](https://docs.microsoft.com/rest/api/searchservice/create-index) ou [atualizar](https://docs.microsoft.com/rest/api/searchservice/update-index) índice | `scoringProfiles/text/weights` | Só pode se referir a campos **pesquisáveis** |
| [Criar](https://docs.microsoft.com/rest/api/searchservice/create-index) ou [atualizar](https://docs.microsoft.com/rest/api/searchservice/update-index) índice | `scoringProfiles/functions/fieldName` | Só pode se referir a campos **filtrados** |
| [Pesquisar](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `search`quando `queryType` é`full` | Só pode se referir a campos **pesquisáveis** |
| [Pesquisar](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `facet` | Só pode se referir a campos **facetable** |
| [Pesquisar](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `highlight` | Só pode se referir a campos **pesquisáveis** |
| [Pesquisar](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `searchFields` | Só pode se referir a campos **pesquisáveis** |
| [Sugerir](https://docs.microsoft.com/rest/api/searchservice/suggestions) e [Autocompletar](https://docs.microsoft.com/rest/api/searchservice/autocomplete) | `searchFields` | Só pode se referir a campos que fazem parte de um [sugestionista](index-add-suggesters.md) |
| [Pesquisar,](https://docs.microsoft.com/rest/api/searchservice/search-documents) [sugerir](https://docs.microsoft.com/rest/api/searchservice/suggestions)e [completar automaticamente](https://docs.microsoft.com/rest/api/searchservice/autocomplete) | `$filter` | Só pode se referir a campos **filtrados** |
| [Pesquisar](https://docs.microsoft.com/rest/api/searchservice/search-documents) e [sugerir](https://docs.microsoft.com/rest/api/searchservice/suggestions) | `$orderby` | Só pode se referir a campos **classificáveis** |
| [Pesquisar,](https://docs.microsoft.com/rest/api/searchservice/search-documents) [sugerir](https://docs.microsoft.com/rest/api/searchservice/suggestions)e [procurar](https://docs.microsoft.com/rest/api/searchservice/lookup-document) | `$select` | Só pode se referir a campos **recuperáveis** |

## <a name="constants"></a>Constantes

As constantes em OData são valores literais de um determinado tipo [de Modelo de Dados de Entidade](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model) (EDM). Consulte [os tipos de dados suportados](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) para uma lista de tipos suportados na Pesquisa Cognitiva do Azure. Constantes de tipos de coleta não são suportadas.

A tabela a seguir mostra exemplos de constantes para cada um dos tipos de dados suportados pelo Azure Cognitive Search:

| Tipo de dados | Exemplo de constantes |
| --- | --- |
| `Edm.Boolean` | `true`, `false` |
| `Edm.DateTimeOffset` | `2019-05-06T12:30:05.451Z` |
| `Edm.Double` | `3.14159`, `-1.2e7`, `NaN`, `INF`, `-INF` |
| `Edm.GeographyPoint` | `geography'POINT(-122.131577 47.678581)'` |
| `Edm.GeographyPolygon` | `geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'` |
| `Edm.Int32` | `123`, `-456` |
| `Edm.Int64` | `283032927235` |
| `Edm.String` | `'hello'` |

### <a name="escaping-special-characters-in-string-constants"></a>Escapando de personagens especiais em constantes de cordas

As constantes de string sin00 no OData são delimitadas por aspas únicas. Se você precisar construir uma consulta com uma constante de string que pode conter aspas simples, você pode escapar das aspas incorporadas dobrando-as.

Por exemplo, uma frase com um apóstrofo não formatado como "carro de Alice" seria representada no OData como a constante `'Alice''s car'`de seqüência .

> [!IMPORTANT]
> Ao construir filtros programáticamente, é importante lembrar de escapar das constantes de cordas que vêm da entrada do usuário. Isto é para mitigar a possibilidade de ataques de [injeção,](https://wikipedia.org/wiki/SQL_injection)especialmente quando se usa filtros para implementar [o corte de segurança.](search-security-trimming-for-azure-search.md)

### <a name="constants-syntax"></a>Sintaxe de constantes

A EBNF[(Extended Backus-Naur Form)](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)define a gramática para a maioria das constantes mostradas na tabela acima. A gramática para tipos geoespaciais pode ser encontrada em [funções geoespaciais OData na Pesquisa Cognitiva do Azure](search-query-odata-geo-spatial-functions.md).

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
constant ::=
    string_literal
    | date_time_offset_literal
    | integer_literal
    | float_literal
    | boolean_literal
    | 'null'

string_literal ::= "'"([^'] | "''")*"'"

date_time_offset_literal ::= date_part'T'time_part time_zone

date_part ::= year'-'month'-'day

time_part ::= hour':'minute(':'second('.'fractional_seconds)?)?

zero_to_fifty_nine ::= [0-5]digit

digit ::= [0-9]

year ::= digit digit digit digit

month ::= '0'[1-9] | '1'[0-2]

day ::= '0'[1-9] | [1-2]digit | '3'[0-1]

hour ::= [0-1]digit | '2'[0-3]

minute ::= zero_to_fifty_nine

second ::= zero_to_fifty_nine

fractional_seconds ::= integer_literal

time_zone ::= 'Z' | sign hour':'minute

sign ::= '+' | '-'

/* In practice integer literals are limited in length to the precision of
the corresponding EDM data type. */
integer_literal ::= digit+

float_literal ::=
    sign? whole_part fractional_part? exponent?
    | 'NaN'
    | '-INF'
    | 'INF'

whole_part ::= integer_literal

fractional_part ::= '.'integer_literal

exponent ::= 'e' sign? integer_literal

boolean_literal ::= 'true' | 'false'
```

Um diagrama interativo de sintaxe também está disponível:

> [!div class="nextstepaction"]
> [Diagrama de sintaxe OData para Pesquisa Cognitiva Azure](https://azuresearch.github.io/odata-syntax-diagram/#constant)

> [!NOTE]
> Consulte [a referência de sintaxe de expressão OData para a Pesquisa Cognitiva do Azure](search-query-odata-syntax-reference.md) para a EBNF completa.

## <a name="building-expressions-from-field-paths-and-constants"></a>Construindo expressões a partir de caminhos de campo e constantes

Caminhos de campo e constantes são a parte mais básica de uma expressão OData, mas eles já são expressões completas. Na verdade, o parâmetro **$select** na Busca Cognitiva do Azure não é nada além de uma lista de caminhos de campo separados por comuma, e **$orderby** não é muito mais complicado do que **$select**. Se acontecer de você ter `Edm.Boolean` um campo de tipo em seu índice, você pode até mesmo escrever um filtro que não é nada além do caminho desse campo. As `true` constantes `false` e também são filtros válidos.

No entanto, na maioria das vezes você precisará de expressões mais complexas que se referem a mais de um campo e constante. Essas expressões são construídas de diferentes maneiras, dependendo do parâmetro.

O Seguinte EBNF ([Formulário Backus-Naur Estendido](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) define a gramática para os parâmetros **$filter,** **$orderby**e **$select.** Estes são construídos a partir de expressões mais simples que se referem a caminhos de campo e constantes:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
filter_expression ::= boolean_expression

order_by_expression ::= order_by_clause(',' order_by_clause)*

select_expression ::= '*' | field_path(',' field_path)*
```

Um diagrama interativo de sintaxe também está disponível:

> [!div class="nextstepaction"]
> [Diagrama de sintaxe OData para Pesquisa Cognitiva Azure](https://azuresearch.github.io/odata-syntax-diagram/#filter_expression)

> [!NOTE]
> Consulte [a referência de sintaxe de expressão OData para a Pesquisa Cognitiva do Azure](search-query-odata-syntax-reference.md) para a EBNF completa.

Os parâmetros **$orderby** e **$select** são listas separadas por comma de expressões mais simples. O parâmetro **$filter** é uma expressão booleana que é composta de subexpressões mais simples. Essas subexpressões são combinadas usando operadores lógicos [ `and`como, `or` `not`e, ](search-query-odata-logical-operators.md)operadores de comparação como [ `eq` `lt`, , `gt`e assim por diante](search-query-odata-comparison-operators.md), e operadores de coleta como [ `any` e `all` ](search-query-odata-collection-operators.md).

Os parâmetros **$filter,** **$orderby**e **$select** são explorados com mais detalhes nos seguintes artigos:

- [OData $filter sintaxe na Pesquisa Cognitiva do Azure](search-query-odata-filter.md)
- [OData $orderby sintaxe na Pesquisa Cognitiva do Azure](search-query-odata-orderby.md)
- [OData $select sintaxe na Pesquisa Cognitiva do Azure](search-query-odata-select.md)

## <a name="see-also"></a>Confira também  

- [Navegação facetada na Pesquisa Cognitiva do Azure](search-faceted-navigation.md)
- [Filtros na Pesquisa Cognitiva do Azure](search-filters.md)
- [Pesquisar documentos &#40;A6Azure Cognitive Search Rest API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
- [Sintaxe de consulta Lucene](query-lucene-syntax.md)
- [Sintaxe de consulta simples na Pesquisa Cognitiva do Azure](query-simple-syntax.md)
