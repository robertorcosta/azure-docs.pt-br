---
title: Referência de ordem por oData
titleSuffix: Azure Cognitive Search
description: Documentação de referência de idiomas e sintaxe para o uso de pedidos em consultas de pesquisa cognitiva do Azure.
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
ms.openlocfilehash: 99ec639b88f3334530243242aadfa0ab52a40df0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113151"
---
# <a name="odata-orderby-syntax-in-azure-cognitive-search"></a>OData $orderby sintaxe na Pesquisa Cognitiva do Azure

 Você pode usar o [parâmetro OData **$orderby** ](query-odata-filter-orderby-syntax.md) para aplicar uma ordem de classificação personalizada para resultados de pesquisa no Azure Cognitive Search. Este artigo descreve a sintaxe de **$orderby** em detalhes. Para obter informações mais gerais sobre como usar **$orderby** ao apresentar os resultados da pesquisa, consulte [Como trabalhar com os resultados da pesquisa na Pesquisa Cognitiva do Azure](search-pagination-page-layout.md).

## <a name="syntax"></a>Sintaxe

O parâmetro **$orderby** aceita uma lista separada por comma de até 32 **cláusulas de pedido.** A sintaxe de uma cláusula de ordem é descrita pela seguinte EBNF ([Formulário Backus-Naur Estendido](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)):

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
order_by_clause ::= (field_path | sortable_function) ('asc' | 'desc')?

sortable_function ::= geo_distance_call | 'search.score()'
```

Um diagrama interativo de sintaxe também está disponível:

> [!div class="nextstepaction"]
> [Diagrama de sintaxe OData para Pesquisa Cognitiva Azure](https://azuresearch.github.io/odata-syntax-diagram/#order_by_clause)

> [!NOTE]
> Consulte [a referência de sintaxe de expressão OData para a Pesquisa Cognitiva do Azure](search-query-odata-syntax-reference.md) para a EBNF completa.

Cada cláusula tem critérios de classificação,`asc` opcionalmente seguidos por uma direção de tipo (para ascendente ou `desc` descendente). Se você não especificar uma direção, o padrão está subindo. Os critérios de classificação podem `sortable` ser o caminho [`geo.distance`](search-query-odata-geo-spatial-functions.md) de [`search.score`](search-query-odata-search-score-function.md) um campo ou uma chamada para as funções ou para as funções.

Se vários documentos tiverem os `search.score` mesmos critérios de classificação e a função não `Rating` for usada (por exemplo, se você classificar por um campo numérico e três documentos todos tiverem uma classificação de 4), os laços serão quebrados por pontuação de documento em ordem decrescente. Quando as pontuações dos documentos são as mesmas (por exemplo, quando não há consulta de pesquisa de texto completo especificada na solicitação), então o pedido relativo dos documentos vinculados é indeterminado.

Você pode especificar vários critérios de classificação. A ordem das expressões determina a ordem de classificação final. Por exemplo, para classificar descendo por pontuação, seguido por `$orderby=search.score() desc,Rating desc`Rating, a sintaxe seria .

A sintaxe para `geo.distance` em **$orderby** é a mesma que em **$filter**. Ao usar `geo.distance` em **$orderby**, o campo ao qual a operação se aplica deve ser do tipo `Edm.GeographyPoint` e também deve ser `sortable`.

A sintaxe para `search.score` em **$orderby** é `search.score()`. A `search.score` função não tem parâmetros.

## <a name="examples"></a>Exemplos

Organizar os hotéis em ordem crescente por taxa base:

    $orderby=BaseRate asc

Organizar os hotéis em ordem decrescente por classificação e, em seguida, em ordem crescente por taxa base (lembre-se de que a ordem crescente é o padrão):

    $orderby=Rating desc,BaseRate

Classificar hotéis descendo por classificação, em seguida, subindo por distância das coordenadas dadas:

    $orderby=Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc

Classifique os hotéis em ordem decrescente por search.score e rating, e depois em ordem ascendente por distância das coordenadas dadas. Entre dois hotéis com pontuações e classificações de relevância idênticas, o mais próximo é listado primeiro:

    $orderby=search.score() desc,Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Próximas etapas  

- [Como trabalhar com resultados de pesquisa na Pesquisa Cognitiva do Azure](search-pagination-page-layout.md)
- [Visão geral da linguagem de expressão oData para pesquisa cognitiva do Azure](query-odata-filter-orderby-syntax.md)
- [Referência de sintaxe de expressão OData para Pesquisa Cognitiva Azure](search-query-odata-syntax-reference.md)
- [Pesquisar documentos &#40;A6Azure Cognitive Search Rest API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
