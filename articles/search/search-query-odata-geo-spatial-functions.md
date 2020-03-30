---
title: Referência de função geoespacial OData
titleSuffix: Azure Cognitive Search
description: Sintaxe e documentação de referência para o uso de funções geoespaciais OData, geo.distance e geo.intersects, em consultas de Pesquisa Cognitiva Azure.
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
ms.openlocfilehash: 902996c1813931638012c78f81bd65c400bee7a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113166"
---
# <a name="odata-geo-spatial-functions-in-azure-cognitive-search---geodistance-and-geointersects"></a>Funções geoespaciais OData na Pesquisa `geo.distance` Cognitiva do Azure - e`geo.intersects`

O Azure Cognitive Search suporta consultas geoespaciais em `geo.distance` [expressões de filtro OData](query-odata-filter-orderby-syntax.md) através das funções e `geo.intersects` funções. A `geo.distance` função retorna a distância em quilômetros entre dois pontos, sendo um uma variável de campo ou alcance, e um sendo uma constante passada como parte do filtro. A `geo.intersects` função `true` retorna se um determinado ponto estiver dentro de um determinado polígono, onde o ponto é uma variável de campo ou intervalo e o polígono é especificado como uma constante passada como parte do filtro.

A `geo.distance` função também pode ser usada no parâmetro [ **$orderby** ](search-query-odata-orderby.md) para classificar os resultados da pesquisa por distância de um determinado ponto. A sintaxe para `geo.distance` em **$orderby** é a mesma que em **$filter**. Ao `geo.distance` utilizar em **$orderby,** o campo ao qual `Edm.GeographyPoint` se aplica deve ser do tipo e também deve ser **classificado.**

> [!NOTE]
> Ao `geo.distance` usar no parâmetro **$orderby,** o campo que você passar para a função deve conter apenas um único ponto geográfico. Em outras palavras, deve `Edm.GeographyPoint` ser `Collection(Edm.GeographyPoint)`do tipo e não. Não é possível classificar em campos de coleta em Azure Cognitive Search.

## <a name="syntax"></a>Sintaxe

A Seguinte EBNF[(Formulário Backus-Naur Estendido)](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form) `geo.distance` define `geo.intersects` a gramática das funções e funções, bem como os valores geoespaciais em que operam:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
geo_distance_call ::=
    'geo.distance(' variable ',' geo_point ')'
    | 'geo.distance(' geo_point ',' variable ')'

geo_point ::= "geography'POINT(" lon_lat ")'"

lon_lat ::= float_literal ' ' float_literal

geo_intersects_call ::=
    'geo.intersects(' variable ',' geo_polygon ')'

/* You need at least four points to form a polygon, where the first and
last points are the same. */
geo_polygon ::=
    "geography'POLYGON((" lon_lat ',' lon_lat ',' lon_lat ',' lon_lat_list "))'"

lon_lat_list ::= lon_lat(',' lon_lat)*
```

Um diagrama interativo de sintaxe também está disponível:

> [!div class="nextstepaction"]
> [Diagrama de sintaxe OData para Pesquisa Cognitiva Azure](https://azuresearch.github.io/odata-syntax-diagram/#geo_distance_call)

> [!NOTE]
> Consulte [a referência de sintaxe de expressão OData para a Pesquisa Cognitiva do Azure](search-query-odata-syntax-reference.md) para a EBNF completa.

### <a name="geodistance"></a>geo.distância

A `geo.distance` função tem dois `Edm.GeographyPoint` parâmetros `Edm.Double` de tipo e retorna um valor que é a distância entre eles em quilômetros. Isso difere de outros serviços que suportam operações geoespaciais OData, que normalmente retornam distâncias em metros.

Um dos parâmetros deve `geo.distance` ser uma constante de ponto de geografia, e o outro deve ser um caminho de `Collection(Edm.GeographyPoint)`campo (ou uma variável de intervalo no caso de um filtro iterado sobre um campo de tipo ). A ordem desses parâmetros não importa.

A constante do ponto de `geography'POINT(<longitude> <latitude>)'`geografia é da forma, onde a longitude e a latitude são constantes numéricas.

> [!NOTE]
> Ao `geo.distance` usar em um filtro, você deve comparar a distância `lt` `le`retornada pela função com uma constante utilização, ou `gt` `ge`. Os operadores `eq` e `ne` não são compatíveis ao comparar as distâncias. Por exemplo, este é `geo.distance`um `$filter=geo.distance(location, geography'POINT(-122.131577 47.678581)') le 5`uso correto de : .

### <a name="geointersects"></a>geo.intersects

A `geo.intersects` função pega uma `Edm.GeographyPoint` variável `Edm.GeographyPolygon` de tipo `Edm.Boolean`  --  `true` e uma constante e retorna um se `false` o ponto estiver dentro dos limites do polígono, caso contrário.

O polígono é uma superfície bidimensional armazenada como uma seqüência de pontos que definem um anel delimitador (veja os [exemplos](#examples) abaixo). O polígono precisa estar fechado, o que significa que os conjuntos de primeiro e último pontos devem ser os mesmos. [Os pontos em um polígono devem estar no sentido anti-horário](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

### <a name="geo-spatial-queries-and-polygons-spanning-the-180th-meridian"></a>Consultas geoespaciais e polígonos abrangendo o 180º meridiano

Para muitas bibliotecas de consulta geoespacial formular uma consulta que inclui o 180º meridiano (perto da linha de data) está fora dos limites ou requer uma solução, como dividir o polígono em dois, um em cada lado do meridiano.

Na Pesquisa Cognitiva do Azure, as consultas geoespaciais que incluem longitude de 180 graus funcionarão como esperado se a forma de consulta for `geo.intersects(location, geography'POLYGON((179 65, 179 66, -179 66, -179 65, 179 65))'`retangular e suas coordenadas se alinharem a um layout de grade ao longo da longitude e latitude (por exemplo, ). Caso contrário, para formas não retangulares ou desalinhadas, considere a abordagem de polígono dividido.  

### <a name="geo-spatial-functions-and-null"></a>Funções geoespaciais e`null`

Como todos os outros campos não-coleta no Azure Cognitive Search, campos de tipo `Edm.GeographyPoint` podem conter `null` valores. Quando o Azure `geo.intersects` Cognitive Search avalia `null`um campo que `false`é, o resultado será sempre . O comportamento `geo.distance` deste caso depende do contexto:

- Em filtros, `geo.distance` `null` de um `null`campo resulta em . Isso significa que o `null` documento não corresponderá porque, `false`em comparação com qualquer valor não nulo, avalia.
- Ao classificar os resultados `geo.distance` usando `null` **$orderby,** de um campo resulta na distância máxima possível. Documentos com esse campo serão menores do que `asc` todos os outros quando a direção do `desc`tipo for usada (o padrão) e maior do que todas as outras quando a direção é .

## <a name="examples"></a>Exemplos

### <a name="filter-examples"></a>Exemplos de filtro

Encontre todos os hotéis dentro de 10 quilômetros de um `Edm.GeographyPoint`determinado ponto de referência (onde a localização é um campo de tipo ):

    geo.distance(location, geography'POINT(-122.131577 47.678581)') le 10

Encontre todos os hotéis dentro de um determinado viewport descrito como `Edm.GeographyPoint`um polígono (onde a localização é um campo de tipo ). Observe que o polígono está fechado (o primeiro e o último pontos devem ser o mesmo) e [os pontos devem estar listados no sentido anti-horário](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')

### <a name="order-by-examples"></a>Exemplos de Order-by

Classificar hotéis descendo, `rating`depois subindo por distância das coordenadas dadas:

    rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

Classifique os hotéis `search.score` `rating`em ordem decrescente por e , e, em seguida, em ordem ascendente por distância das coordenadas dadas de modo que entre dois hotéis com classificações idênticas, o mais próximo é listado primeiro:

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Próximas etapas  

- [Filtros na Pesquisa Cognitiva do Azure](search-filters.md)
- [Visão geral da linguagem de expressão oData para pesquisa cognitiva do Azure](query-odata-filter-orderby-syntax.md)
- [Referência de sintaxe de expressão OData para Pesquisa Cognitiva Azure](search-query-odata-syntax-reference.md)
- [Pesquisar documentos &#40;A6Azure Cognitive Search Rest API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
