---
title: Filter on search results
titleSuffix: Azure Cognitive Search
description: Filter by user security identity, language, geo-location, or numeric values to reduce search results on queries in Azure Cognitive Search, a hosted cloud search service on Microsoft Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f4ce3cd0db20f76aa6169f15254cf36ee64151a5
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406738"
---
# <a name="filters-in-azure-cognitive-search"></a>Filters in Azure Cognitive Search 

A *filter* provides criteria for selecting documents used in an Azure Cognitive Search query. A pesquisa não filtrada inclui todos os documentos no índice. Um filtro restringe uma consulta de pesquisa a um subconjunto de documentos. Por exemplo, um filtro poderia restringir a pesquisa de texto completo a apenas os produtos com uma marca ou cor específica, com preços acima de um certo limite.

Algumas experiências de pesquisa impõem requisitos de filtro como parte da implementação, mas você pode usar filtros sempre que desejar restringir a pesquisa com critérios *baseados em valor* (restringindo a pesquisa ao tipo de produto "livros" para a categoria "não ficção" publicados por "Simon & Schuster").

Se sua meta é a pesquisa direcionada para *estruturas* de dados específicas (restringir a pesquisa a um campo de revisões de cliente), há métodos alternativos descritos abaixo.

## <a name="when-to-use-a-filter"></a>Quando usar um filtro

Os filtros são fundamentais para as várias experiências de pesquisa, incluindo "Localizar próximo a mim", navegação facetada e filtros de segurança que mostram somente os documentos que um usuário tem permissão para ver. Se você implementar qualquer uma dessas experiências, um filtro será necessário. É o filtro anexado à consulta de pesquisa que fornece as coordenadas de localização geográfica, a categoria de faceta selecionada pelo usuário ou a identificação de segurança do solicitante.

Os cenários de exemplo incluem o seguinte:

1. Use um filtro para dividir o índice com base nos valores de dados no índice. Com um esquema com cidade tipo de residência e comodidades, você pode criar um filtro para selecionar explicitamente os documentos que atendem aos critérios (em Seattle, condomínios, orla marítima). 

   A pesquisa de texto completo com as mesmas entradas geralmente produz resultados semelhantes, mas um filtro é mais preciso porque requer uma correspondência exata do termo do filtro em relação ao conteúdo no índice. 

2. Use um filtro se a experiência de pesquisa vier com um requisito de filtro:

   * [A navegação facetada](search-faceted-navigation.md) usa um filtro para devolver a categoria de faceta selecionada pelo usuário.
   * A pesquisa de área geográfica usa um filtro para passar as coordenadas do local atual em aplicativos "Localizar próximo a mim". 
   * Filtros de segurança passam identificadores de segurança como critérios de filtro, em que uma correspondência no índice serve como um proxy para direitos de acesso ao documento.

3. Use um filtro se desejar critérios de pesquisa em um campo numérico. 

   Campos numéricos são recuperáveis no documento e podem aparecer nos resultados da pesquisa, mas não são pesquisáveis (sujeito à pesquisa de texto completo) individualmente. Se precisar de critérios de seleção com base em dados numéricos, use um filtro.

### <a name="alternative-methods-for-reducing-scope"></a>Métodos alternativos para reduzir o escopo

Se desejar um efeito de estreitamento nos resultados da pesquisa, os filtros não serão sua única opção. Estas alternativas podem ser mais adequadas, dependendo de seu objetivo:

 + O parâmetro de consulta `searchFields` marca a pesquisa a campos específicos. Por exemplo, se o índice fornece campos separados para descrições em inglês e espanhol, você pode usar searchFields para direcionar quais campos serão usados para pesquisa de texto completo. 

+ O parâmetro `$select` é usado para especificar quais campos serão incluídos em um conjunto de resultados, reduzindo efetivamente a resposta antes de enviá-la para o aplicativo de chamada. This parameter does not refine the query or reduce the document collection, but if a smaller response is your goal, this parameter is an option to consider. 

Para obter mais informações sobre um parâmetro, consulte [Pesquisar Documentos > Solicitar > Parâmetros de consulta](https://docs.microsoft.com/rest/api/searchservice/search-documents#request).


## <a name="how-filters-are-executed"></a>How filters are executed

At query time, a filter parser accepts criteria as input, converts the expression into atomic Boolean expressions represented as a tree, and then evaluates the filter tree over filterable fields in an index.

Filtering occurs in tandem with search, qualifying which documents to include in downstream processing for document retrieval and relevance scoring. When paired with a search string, the filter effectively reduces the recall set of the subsequent search operation. Quando usado sozinho (por exemplo, quando a cadeia de caracteres de consulta estiver vazio em `search=*`), os critérios de filtro são a única entrada. 

## <a name="defining-filters"></a>Definir filtros
Filters are OData expressions, articulated using a [subset of OData V4 syntax supported in Azure Cognitive Search](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search). 

You can specify one filter for each **search** operation, but the filter itself can include multiple fields, multiple criteria, and if you use an **ismatch** function, multiple full-text search expressions. In a multi-part filter expression, you can specify predicates in any order (subject to the rules of operator precedence). Não há nenhum ganho significativo no desempenho se você tentar reorganizar predicados em uma determinada sequência.

One of the limits on a filter expression is the maximum size limit of the request. Toda a solicitação, incluindo o filtro, pode ter um máximo de 16 MB para POST ou 8 KB para GET. There is also a limit on the number of clauses in your filter expression. Uma boa regra prática é que, se você tiver centenas de cláusulas, você correrá o risco de atingir o limite. É recomendável criar seu aplicativo de forma que ele não gere filtros de tamanho ilimitado.

Os exemplos a seguir representam as definições de filtro de um modelo em várias APIs.

```http
# Option 1:  Use $filter for GET
GET https://[service name].search.windows.net/indexes/hotels/docs?api-version=2019-05-06&search=*&$filter=Rooms/any(room: room/BaseRate lt 150.0)&$select=HotelId, HotelName, Rooms/Description, Rooms/BaseRate

# Option 2: Use filter for POST and pass it in the request body
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2019-05-06
{
    "search": "*",
    "filter": "Rooms/any(room: room/BaseRate lt 150.0)",
    "select": "HotelId, HotelName, Rooms/Description, Rooms/BaseRate"
}
```

```csharp
    parameters =
        new SearchParameters()
        {
            Filter = "Rooms/any(room: room/BaseRate lt 150.0)",
            Select = new[] { "HotelId", "HotelName", "Rooms/Description" ,"Rooms/BaseRate"}
        };

    var results = searchIndexClient.Documents.Search("*", parameters);
```

## <a name="filter-usage-patterns"></a>Filter usage patterns

The following examples illustrate several usage patterns for filter scenarios. Para obter mais ideias, consulte [Sintaxe de expressão do OData > Exemplos](https://docs.microsoft.com/azure/search/search-query-odata-filter#examples).

+ **$filter** autônomo, sem uma cadeia de consulta, útil quando a expressão de filtro puder qualificar totalmente documentos de interesse. Sem uma cadeia de caracteres de consulta, não há nenhuma análise linguística ou lexical, nenhuma pontuação e nenhuma classificação. Notice the search string is just an asterisk, which means "match all documents".

   ```
   search=*&$filter=Rooms/any(room: room/BaseRate ge 60 and room/BaseRate lt 300) and Address/City eq 'Honolulu'
   ```

+ A combinação de cadeia de caracteres de consulta e **$filter**, em que o filtro cria o subconjunto e a cadeia de caracteres de consulta fornece as entradas do termo de pesquisa de texto completo no subconjunto filtrado. The addition of terms (walking distance theaters) introduces search scores in the results, where documents that best match the terms are ranked higher. Using a filter with a query string is the most common usage pattern.

   ```
  search=walking distance theaters&$filter=Rooms/any(room: room/BaseRate ge 60 and room/BaseRate lt 300) and Address/City eq 'Seattle'&$count=true
   ```

+ Consultas composta, separadas por "or", cada uma com seus próprios critérios de filtro (por exemplo, “beagles" em "dog" ou "siamese" em "cat"). Expressions combined with `or` are evaluated individually, with the union of documents matching each expression sent back in the response. This usage pattern is achieved through the `search.ismatchscoring` function. You can also use the non-scoring version, `search.ismatch`.

   ```
   # Match on hostels rated higher than 4 OR 5-star motels.
   $filter=search.ismatchscoring('hostel') and Rating ge 4 or search.ismatchscoring('motel') and Rating eq 5

   # Match on 'luxury' or 'high-end' in the description field OR on category exactly equal to 'Luxury'.
   $filter=search.ismatchscoring('luxury | high-end', 'Description') or Category eq 'Luxury'&$count=true
   ```

  It is also possible to combine full-text search via `search.ismatchscoring` with filters using `and` instead of `or`, but this is functionally equivalent to using the `search` and `$filter` parameters in a search request. For example, the following two queries produce the same result:

  ```
  $filter=search.ismatchscoring('pool') and Rating ge 4

  search=pool&$filter=Rating ge 4
  ```

Confira estes artigos para obter orientação abrangente sobre casos de uso específicos:

+ [Filtros da faceta](search-filters-facets.md)
+ [Filtros da linguagem](search-filters-language.md)
+ [Restrições de segurança](search-security-trimming-for-azure-search.md) 

## <a name="field-requirements-for-filtering"></a>Requisitos de campo para filtragem

In the REST API, filterable is *on* by default for simple fields. Campos que podem ser filtrados aumentam o tamanho do índice. Certifique-se de definir `"filterable": false` para os campos que não planeja usar em um filtro. Para obter mais informações sobre como configurar definições de campo, consulte [Criar índice](https://docs.microsoft.com/rest/api/searchservice/create-index).

No SDK .NET, filtrável está *desabilitado* por padrão. You can make a field filterable by setting the [IsFilterable property](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.isfilterable?view=azure-dotnet) of the corresponding [Field](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field?view=azure-dotnet) object to `true`. You can also do this declaratively by using the [IsFilterable attribute](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.isfilterableattribute). In the example below, the attribute is set on the `BaseRate` property of a model class that maps to the index definition.

```csharp
    [IsFilterable, IsSortable, IsFacetable]
    public double? BaseRate { get; set; }
```

### <a name="making-an-existing-field-filterable"></a>Making an existing field filterable

You can't modify existing fields to make them filterable. Instead, you need to add a new field, or rebuild the index. For more information about rebuilding an index or repopulating fields, see [How to rebuild an Azure Cognitive Search index](search-howto-reindex.md).

## <a name="text-filter-fundamentals"></a>Conceitos básicos do filtro de texto

Text filters match string fields against literal strings that you provide in the filter. Unlike full-text search, there is no lexical analysis or word-breaking for text filters, so comparisons are for exact matches only. For example, assume a field *f* contains "sunny day", `$filter=f eq 'Sunny'` does not match, but `$filter=f eq 'sunny day'` will. 

Cadeias de caracteres de texto diferenciam minúsculas e maiúsculas. There is no lower-casing of upper-cased words: `$filter=f eq 'Sunny day'` will not find "sunny day".

### <a name="approaches-for-filtering-on-text"></a>Approaches for filtering on text

| Abordagem | Descrição | Quando usar |
|----------|-------------|-------------|
| [`search.in`](search-query-odata-search-in-function.md) | A function that matches a field against a delimited list of strings. | Recommended for [security filters](search-security-trimming-for-azure-search.md) and for any filters where many raw text values need to be matched with a string field. The **search.in** function is designed for speed and is much faster than explicitly comparing the field against each string using `eq` and `or`. | 
| [`search.ismatch`](search-query-odata-full-text-search-functions.md) | Uma função que permite a combinação de operações de pesquisa de texto completo com operações de filtro estritamente booliano na mesma expressão de filtro. | Use **search.ismatch** (or its scoring equivalent, **search.ismatchscoring**) when you want multiple search-filter combinations in one request. Você também pode usá-la para um filtro de *contém* para filtrar em uma cadeia de caracteres parcial dentro de uma cadeia de caracteres maior. |
| [`$filter=field operator string`](search-query-odata-comparison-operators.md) | Uma expressão definida pelo usuário é composta por campos, operadores e valores. | Use this when you want to find exact matches between a string field and a string value. |

## <a name="numeric-filter-fundamentals"></a>Conceitos básicos do filtro numérico

Campos numéricos não são `searchable` no contexto da pesquisa de texto completo. Somente cadeias de caracteres estão sujeitas à pesquisa de texto completo. Por exemplo, se você inserir 99,99 como um termo de pesquisa, você não receberá itens com preços de $ 99,99. Em vez disso, você verá os itens que têm o número 99 nos campos de cadeia de caracteres do documento. Portanto, se você tiver dados numéricos, a suposição é que você os usará para filtros, incluindo intervalos, facetas, grupos e assim por diante. 

Documentos que contêm campos numéricos (preço, tamanho, SKU, ID) fornecem esses valores nos resultados da pesquisa se o campo estiver marcado como `retrievable`. O ponto aqui é que a pesquisa de texto completo em si não é aplicável a tipos de campos numéricos.

## <a name="next-steps"></a>Próximos passos

Primeiro, tente o **Search Explorer** no portal para enviar consultas com parâmetros **$filter**. O [índice real-estate-sample](search-get-started-portal.md) fornece resultados interessantes para as seguintes consultas filtradas quando você o cola na barra de pesquisa:

```
# Geo-filter returning documents within 5 kilometers of Redmond, Washington state
# Use $count=true to get a number of hits returned by the query
# Use $select to trim results, showing values for named fields only
# Use search=* for an empty query string. The filter is the sole input

search=*&$count=true&$select=description,city,postCode&$filter=geo.distance(location,geography'POINT(-122.121513 47.673988)') le 5

# Numeric filters use comparison like greater than (gt), less than (lt), not equal (ne)
# Include "and" to filter on multiple fields (baths and bed)
# Full text search is on John Leclerc, matching on John or Leclerc

search=John Leclerc&$count=true&$select=source,city,postCode,baths,beds&$filter=baths gt 3 and beds gt 4

# Text filters can also use comparison operators
# Wrap text in single or double quotes and use the correct case
# Full text search is on John Leclerc, matching on John or Leclerc

search=John Leclerc&$count=true&$select=source,city,postCode,baths,beds&$filter=city gt 'Seattle'
```

Para trabalhar com mais exemplos, consulte [Sintaxe de expressão de filtro OData > Exemplos](https://docs.microsoft.com/azure/search/search-query-odata-filter#examples).

## <a name="see-also"></a>Consulte

+ [How full text search works in Azure Cognitive Search](search-lucene-query-architecture.md)
+ [API REST para pesquisar documentos](https://docs.microsoft.com/rest/api/searchservice/search-documents)
+ [Sintaxe de consulta simples](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Sintaxe de consulta Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [Tipos de dados com suporte](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)
