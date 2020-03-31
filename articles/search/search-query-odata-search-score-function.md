---
title: Referência da função oData search.score
titleSuffix: Azure Cognitive Search
description: Sintaxe e documentação de referência para usar a função search.score em consultas de Pesquisa Cognitiva do Azure.
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
ms.openlocfilehash: 2439d4f03184f8dbb85b229b3908dff95013b4bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113127"
---
# <a name="odata-searchscore-function-in-azure-cognitive-search"></a>Função `search.score` OData na Pesquisa Cognitiva do Azure

Quando você envia uma consulta para o Azure Cognitive Search sem o parâmetro [ **$orderby,** ](search-query-odata-orderby.md)os resultados que voltarem serão classificados em ordem decrescente por pontuação de relevância. Mesmo quando você usa **$orderby,** a pontuação de relevância será usada para quebrar empates por padrão. No entanto, às vezes é útil usar a pontuação de relevância como um critério de classificação inicial, e alguns outros critérios como o desempate. A `search.score` função permite que você faça isso.

## <a name="syntax"></a>Sintaxe

A sintaxe para `search.score` em **$orderby** é `search.score()`. A função `search.score` não aceita nenhum parâmetro. Ele pode ser `asc` usado `desc` com o especificador ou ordem de classificação, assim como qualquer outra cláusula no parâmetro **$orderby.** Ele pode aparecer em qualquer lugar da lista de critérios de tipo.

## <a name="example"></a>Exemplo

Classifique os hotéis `search.score` `rating`em ordem decrescente por e , e, em seguida, em ordem ascendente por distância das coordenadas dadas de modo que entre dois hotéis com classificações idênticas, o mais próximo é listado primeiro:

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Próximas etapas  

- [Visão geral da linguagem de expressão oData para pesquisa cognitiva do Azure](query-odata-filter-orderby-syntax.md)
- [Referência de sintaxe de expressão OData para Pesquisa Cognitiva Azure](search-query-odata-syntax-reference.md)
- [Pesquisar documentos &#40;Azure Cognitive Search EST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
