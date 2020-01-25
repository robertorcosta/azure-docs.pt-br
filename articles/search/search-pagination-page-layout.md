---
title: Como trabalhar com os resultados da pesquisa
titleSuffix: Azure Cognitive Search
description: Estruturar e classificar resultados da pesquisa, obter uma contagem de documentos e adicionar navegação de conteúdo aos resultados da pesquisa no Azure Pesquisa Cognitiva.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: c32e58a43b5409fd9f8ede536167d185270c6a22
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721567"
---
# <a name="how-to-work-with-search-results-in-azure-cognitive-search"></a>Como trabalhar com os resultados da pesquisa no Azure Pesquisa Cognitiva
Este artigo fornece orientação sobre como implementar elementos padrão da página de resultados da pesquisa, por exemplo, contagem total, recuperação de documentos, ordens de classificação e navegação. As opções relacionadas à página que contribuem com dados ou informações para os resultados da pesquisa são especificadas por meio das solicitações de [documento de pesquisa](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) enviadas ao serviço de pesquisa cognitiva do Azure. 

Na API REST, as solicitações incluem um comando GET, um caminho e os parâmetros de consulta que informam ao serviço que está sendo solicitado, e como formular a resposta. No SDK do .NET, a API equivalente é a [classe DocumentSearchResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult-1).

Para gerar rapidamente uma página de pesquisa para seu cliente, explore estas opções:

+ Use o [gerador de aplicativos](search-create-app-portal.md) no portal para criar uma página HTML com uma barra de pesquisa, navegação facetada e área de resultados.
+ Siga o tutorial [criar seu primeiro aplicativo C# em](tutorial-csharp-create-first-app.md) para criar um cliente funcional.

Vários exemplos de código incluem uma interface de front-end da Web, que pode ser encontrada aqui: [aplicativo de demonstração de trabalhos de cidade de Nova York](https://azjobsdemo.azurewebsites.net/), [código de exemplo de JavaScript com um site de demonstração ao vivo](https://github.com/liamca/azure-search-javascript-samples)e [CognitiveSearchFrontEnd](https://github.com/LuisCabrer/CognitiveSearchFrontEnd).

> [!NOTE]
> Uma solicitação válida inclui diversos elementos, como uma URL de serviço e o caminho, o verbo HTTP, `api-version` etc. Para resumir, recortamos os exemplos para destacar apenas a sintaxe relevante para a paginação. Para obter mais informações sobre a sintaxe de solicitação, consulte [APIs REST do Azure pesquisa cognitiva](https://docs.microsoft.com/rest/api/searchservice).
>

## <a name="total-hits-and-page-counts"></a>Total de ocorrências e contagens de página

Mostrar o número total de resultados retornados por uma consulta e, em seguida, retornar esses resultados em pedaços menores, é fundamental para praticamente todas as páginas de pesquisa.

![][1]

No Azure Pesquisa Cognitiva, você usa os parâmetros `$count`, `$top`e `$skip` para retornar esses valores. O exemplo a seguir mostra uma solicitação de exemplo para total de ocorrências em um índice chamado "online-Catalog", retornado como `@odata.count`:

    GET /indexes/online-catalog/docs?$count=true

Recupera documentos em grupos de 15 e também mostra o total de ocorrências, começando na primeira página:

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=0&$count=true

A paginação de resultados exige `$top` e `$skip`, sendo que `$top` especifica quantos itens devem retornar em um lote, e `$skip` especifica quantos itens ignorar. No exemplo a seguir, cada página mostra os próximos 15 itens, indicados por saltos incrementais no parâmetro `$skip` .

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=0&$count=true

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=15&$count=true

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=30&$count=true

## <a name="layout"></a>Layout

Talvez você queira mostrar em uma página de resultados da pesquisa uma imagem em miniatura, um subconjunto dos campos e um link para uma página completa sobre um produto.

 ![][2]

No Azure Pesquisa Cognitiva, você usaria `$select` e uma [solicitação de API de pesquisa](https://docs.microsoft.com/rest/api/searchservice/search-documents) para implementar essa experiência.

Para retornar um subconjunto dos campos para um layout lado a lado:

    GET /indexes/online-catalog/docs?search=*&$select=productName,imageFile,description,price,rating

Arquivos de imagem e de mídia não são diretamente pesquisáveis e devem ser armazenados em outra plataforma de armazenamento, por exemplo, o armazenamento do Blob do Azure, para reduzir os custos. No índice e nos documentos, defina um campo que armazena o endereço da URL do conteúdo externo. Em seguida, use o campo como uma referência de imagem. A URL da imagem deve estar no documento.

Para recuperar uma página de descrição de produto em um evento **onClick** , use [Pesquisar Documento](https://docs.microsoft.com/rest/api/searchservice/Lookup-Document) para passar a chave do documento que será recuperada. O tipo de dados da chave é `Edm.String`. Neste exemplo, é *246810*.

    GET /indexes/online-catalog/docs/246810

## <a name="sort-by-relevance-rating-or-price"></a>Classificar por relevância, por classificação ou por preço

Por padrão, as ordens de classificação normalmente usam a opção de relevância, mas é comum disponibilizar ordens de classificação alternativas para que os clientes possam reorganizar rapidamente esses resultados em uma ordem de classificação diferente.

 ![][3]

No Azure Pesquisa Cognitiva, a classificação é baseada na expressão `$orderby`, para todos os campos que são indexados como `"Sortable": true.` uma cláusula `$orderby` é uma expressão OData. Para saber informações sobre sintaxe, confira [Sintaxe de expressão do OData para cláusulas filter e order-by](query-odata-filter-orderby-syntax.md).

A relevância está fortemente associada a perfis de pontuação. Você pode usar a pontuação padrão, que depende de análise de texto e estatísticas para classificar todos os resultados, com pontuações mais altas sendo atribuídas a documentos com mais correspondências, ou correspondências mais sólidas, de um termo de pesquisa.

As ordens de classificação alternativas normalmente são associadas a eventos **onClick** que remetem a um método que cria a ordem de classificação. Por exemplo, este elemento de página:

 ![][4]

Você criaria um método que aceitasse a opção de classificação selecionada como entrada, e retornaria uma lista ordenada dos critérios associados a essa opção.

 ![][5]

> [!NOTE]
> Embora a pontuação padrão seja suficiente para muitos cenários, recomendamos basear a relevância em um perfil personalizado de pontuação. Um perfil personalizado de pontuação permite um aumento dos itens mais úteis para o seu negócio. Confira [Adicionar perfis de pontuação](index-add-scoring-profiles.md) para saber mais.
>

## <a name="hit-highlighting"></a>Realce de ocorrência

Você pode aplicar formatação aos termos correspondentes nos resultados da pesquisa, facilitando a identificação da correspondência. As instruções de realce de visita são fornecidas na [solicitação de consulta](https://docs.microsoft.com/rest/api/searchservice/search-documents). 

A formatação é aplicada a consultas de termo completo. As consultas em termos parciais, como pesquisa difusa ou pesquisa de curinga que resultam em expansão de consulta no mecanismo, não podem usar o realce de clique.

```http
POST /indexes/hotels/docs/search?api-version=2019-05-06 
    {  
      "search": "something",  
      "highlight": "Description"  
    }
```



## <a name="faceted-navigation"></a>Navegação facetada

A navegação de pesquisa é comum em uma página de resultados, e normalmente fica na lateral ou na parte superior de uma página. No Pesquisa Cognitiva do Azure, a navegação facetada fornece pesquisa autodirigida com base em filtros predefinidos. Consulte [navegação facetada no Azure pesquisa cognitiva](search-faceted-navigation.md) para obter detalhes.

## <a name="filters-at-the-page-level"></a>Filtros no nível da página

Se o design da solução incluísse páginas de pesquisa dedicadas para tipos específicos de conteúdo (por exemplo, um aplicativo de varejo online que tenha departamentos listados na parte superior da página), você poderá inserir uma [expressão de filtro](search-filters.md) ao lado de um evento **onclick** para abrir uma página em um Estado previamente filtrado.

Você pode enviar um filtro com ou sem uma expressão de pesquisa. Por exemplo, a seguinte solicitação filtrará o nome da marca, retornando somente os documentos que correspondem a ele.

    GET /indexes/online-catalog/docs?$filter=brandname eq 'Microsoft' and category eq 'Games'

Consulte [Pesquisar documentos (API de pesquisa cognitiva do Azure)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) para obter mais informações sobre `$filter` expressões.

## <a name="see-also"></a>Consulte Também

- [API REST da Pesquisa Cognitiva do Azure](https://docs.microsoft.com/rest/api/searchservice)
- [Operações de índice](https://docs.microsoft.com/rest/api/searchservice/Index-operations)
- [Operações de documento.](https://docs.microsoft.com/rest/api/searchservice/Document-operations)
- [Navegação facetada no Azure Pesquisa Cognitiva](search-faceted-navigation.md)

<!--Image references-->
[1]: ./media/search-pagination-page-layout/Pages-1-Viewing1ofNResults.PNG
[2]: ./media/search-pagination-page-layout/Pages-2-Tiled.PNG
[3]: ./media/search-pagination-page-layout/Pages-3-SortBy.png
[4]: ./media/search-pagination-page-layout/Pages-4-SortbyRelevance.png
[5]: ./media/search-pagination-page-layout/Pages-5-BuildSort.png
