---
title: Referência de seleção oData
titleSuffix: Azure Cognitive Search
description: Sintaxe e referência de idioma para seleção explícita de campos para retornar nos resultados de pesquisa de consultas de Pesquisa Cognitiva do Azure.
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
ms.openlocfilehash: 64f15bf3d262249cdda2760c7ddf768be2590419
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113106"
---
# <a name="odata-select-syntax-in-azure-cognitive-search"></a>OData $select sintaxe na Pesquisa Cognitiva do Azure

 Você pode usar o [parâmetro OData **$select** ](query-odata-filter-orderby-syntax.md) para escolher quais campos incluir nos resultados de pesquisa da Pesquisa Cognitiva do Azure. Este artigo descreve a sintaxe de **$select** em detalhes. Para obter informações mais gerais sobre como usar **$select** ao apresentar os resultados da pesquisa, consulte [Como trabalhar com os resultados da pesquisa na Pesquisa Cognitiva do Azure](search-pagination-page-layout.md).

## <a name="syntax"></a>Sintaxe

O **parâmetro $select** determina quais campos para cada documento são devolvidos no conjunto de resultados da consulta. O seguinte EBNF ([Formulário Backus-Naur Estendido](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) define a gramática para o parâmetro **$select:**

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
select_expression ::= '*' | field_path(',' field_path)*

field_path ::= identifier('/'identifier)*
```

Um diagrama interativo de sintaxe também está disponível:

> [!div class="nextstepaction"]
> [Diagrama de sintaxe OData para Pesquisa Cognitiva Azure](https://azuresearch.github.io/odata-syntax-diagram/#select_expression)

> [!NOTE]
> Consulte [a referência de sintaxe de expressão OData para a Pesquisa Cognitiva do Azure](search-query-odata-syntax-reference.md) para a EBNF completa.

O **parâmetro $select** vem em duas formas:

1. Uma única`*`estrela , indicando que todos os campos recuperáveis devem ser devolvidos, ou
1. Uma lista separada por comma de caminhos de campo, identificando quais campos devem ser devolvidos.

Ao usar o segundo formulário, você só pode especificar campos recuperáveis na lista.

Se você listar um campo complexo sem especificar explicitamente seus subcampos, todos os subcampos recuperáveis serão incluídos no conjunto de resultados da consulta. Por exemplo, suponha `Address` que `Street` `City`seu `Country` índice tenha um campo com , e subcampos que são todos recuperáveis. Se você `Address` especificar em **$select,** os resultados da consulta incluirão todos os três subcampos.

## <a name="examples"></a>Exemplos

Inclua `HotelId`os `HotelName`campos `Rating` , e de nível superior nos `City` resultados, `Address`bem como o subcampo de :

    $select=HotelId, HotelName, Rating, Address/City

Um resultado de exemplo pode ser assim:

```json
{
  "HotelId": "1",
  "HotelName": "Secret Point Motel",
  "Rating": 4,
  "Address": {
    "City": "New York"
  }
}
```

Inclua `HotelName` o campo de nível superior nos resultados, bem `Address`como `Type` todos `BaseRate` os subcampos de `Rooms` , e os subcampos de cada objeto na coleção:

    $select=HotelName, Address, Rooms/Type, Rooms/BaseRate

Um resultado de exemplo pode ser assim:

```json
{
  "HotelName": "Secret Point Motel",
  "Rating": 4,
  "Address": {
    "StreetAddress": "677 5th Ave",
    "City": "New York",
    "StateProvince": "NY",
    "Country": "USA",
    "PostalCode": "10022"
  },
  "Rooms": [
    {
      "Type": "Budget Room",
      "BaseRate": 9.69
    },
    {
      "Type": "Budget Room",
      "BaseRate": 8.09
    }
  ]
}
```

## <a name="next-steps"></a>Próximas etapas  

- [Como trabalhar com resultados de pesquisa na Pesquisa Cognitiva do Azure](search-pagination-page-layout.md)
- [Visão geral da linguagem de expressão oData para pesquisa cognitiva do Azure](query-odata-filter-orderby-syntax.md)
- [Referência de sintaxe de expressão OData para Pesquisa Cognitiva Azure](search-query-odata-syntax-reference.md)
- [Pesquisar documentos &#40;A6Azure Cognitive Search Rest API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
