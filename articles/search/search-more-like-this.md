---
title: recurso de consulta moreLikeThis (visualização)
titleSuffix: Azure Cognitive Search
description: Descreve o recurso maisLikeThis (visualização), que está disponível nas versões de pré-visualização da API Azure Cognitive Search REST.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 95b9c76a2ff962cb2fa4bacbb1b1e9a953b7014f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873787"
---
# <a name="morelikethis-preview-in-azure-cognitive-search"></a>moreLikeThis (visualização) no Azure Cognitive Search

> [!IMPORTANT] 
> Esse recurso está atualmente em visualização pública. A funcionalidade de versão prévia é fornecida sem um Contrato de Nível de Serviço e, portanto, não é recomendada para cargas de trabalho de produção. Para obter mais informações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A [API REST versão 2019-05-06-versão prévia](search-api-preview.md) fornece esse recurso. Atualmente, não há suporte a portal ou .NET SDK.

`moreLikeThis=[key]`é um parâmetro de consulta na [API Documentos de Pesquisa](https://docs.microsoft.com/rest/api/searchservice/search-documents) que encontra documentos semelhantes ao documento especificado pela chave do documento. Quando uma solicitação de pesquisa é feita com `moreLikeThis`, uma consulta é gerada com termos de pesquisa extraídos do documento fornecido que descrevem melhor o documento. A consulta gerada é usada para fazer a solicitação de pesquisa. Por padrão, o conteúdo de todos os campos pesquisáveis é considerado, menos quaisquer campos restritos que você especificou usando o `searchFields` parâmetro. O parâmetro `moreLikeThis` não pode ser usado com o parâmetro de pesquisa, `search=[string]`.

Por padrão, o conteúdo de todos os campos pesquisáveis de nível superior são considerados. Se você quiser especificar campos específicos `searchFields` em vez disso, você pode usar o parâmetro. 

Você não `MoreLikeThis` pode usar em subcampos pesquisáveis em um [tipo complexo](search-howto-complex-data-types.md).

## <a name="examples"></a>Exemplos

Todos os exemplos a seguir usam a amostra de hotéis do [Quickstart: Crie um índice de pesquisa no portal Azure](search-get-started-portal.md).

### <a name="simple-query"></a>Consulta simples

A consulta a seguir encontra documentos cujos campos de descrição são mais `moreLikeThis` semelhantes ao campo do documento de origem conforme especificado pelo parâmetro:

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=29&searchFields=Description&api-version=2019-05-06-Preview
```

Neste exemplo, a solicitação busca hotéis semelhantes ao que tem `HotelId` 29.
Em vez de usar http `MoreLikeThis` get, você também pode invocar usando HTTP POST:

```
POST /indexes/hotels-sample-index/docs/search?api-version=2019-05-06-Preview
    {
      "moreLikeThis": "29",
      "searchFields": "Description"
    }
```

### <a name="apply-filters"></a>Aplicar filtros

`MoreLikeThis`pode ser combinado com outros parâmetros comuns de consulta, como `$filter`. Por exemplo, a consulta pode ser restrita apenas a hotéis cuja categoria seja 'Orçamento' e onde a classificação seja superior a 3.5:

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=20&searchFields=Description&$filter=(Category eq 'Budget' and Rating gt 3.5)&api-version=2019-05-06-Preview
```

### <a name="select-fields-and-limit-results"></a>Selecione campos e limite resultados

O `$top` seletor pode ser usado para limitar `MoreLikeThis` quantos resultados devem ser retornados em uma consulta. Além disso, os `$select`campos podem ser selecionados com . Aqui os três melhores hotéis são selecionados junto com seu ID, Name e Rating: 

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=20&searchFields=Description&$filter=(Category eq 'Budget' and Rating gt 3.5)&$top=3&$select=HotelId,HotelName,Rating&api-version=2019-05-06-Preview
```

## <a name="next-steps"></a>Próximas etapas

Você pode usar qualquer ferramenta de teste web para experimentar este recurso.  Recomendamos o uso do Carteiro para este exercício.

> [!div class="nextstepaction"]
> [Explore as APIs de pesquisa cognitiva do Azure usando o Carteiro](search-get-started-postman.md)
