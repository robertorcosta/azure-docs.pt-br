---
title: Como trabalhar com resultados de pesquisa
titleSuffix: Azure Cognitive Search
description: Estruturar e classificar os resultados da pesquisa, obter uma contagem de documentos e adicionar navegação de conteúdo aos resultados de pesquisa na Pesquisa Cognitiva do Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: 124f1ce3d30ce87d5e9d8fa027e5a7d6c0b3cb17
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481595"
---
# <a name="how-to-work-with-search-results-in-azure-cognitive-search"></a>Como trabalhar com resultados de pesquisa na Pesquisa Cognitiva do Azure
Este artigo fornece orientação sobre como implementar elementos padrão da página de resultados da pesquisa, por exemplo, contagem total, recuperação de documentos, ordens de classificação e navegação. As opções relacionadas à página que contribuem com dados ou informações para seus resultados de pesquisa são especificadas através das solicitações [de documento de pesquisa](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) enviadas ao seu serviço de pesquisa cognitiva Do Azure. 

Na API REST, as solicitações incluem um comando GET, um caminho e os parâmetros de consulta que informam ao serviço que está sendo solicitado, e como formular a resposta. No SDK do .NET, a API equivalente é a [classe DocumentSearchResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult-1).

Para gerar rapidamente uma página de pesquisa para seu cliente, explore essas opções:

+ Use o [gerador de aplicativos](search-create-app-portal.md) no portal para criar uma página HTML com uma barra de pesquisa, navegação facetada e área de resultados.
+ Siga o [tutorial Criar seu primeiro aplicativo em C#](tutorial-csharp-create-first-app.md) para criar um cliente funcional.

Várias amostras de código incluem uma interface front-end web, que você pode encontrar aqui: [aplicativo de demonstração do New York City Jobs,](https://aka.ms/azjobsdemo) [código de amostra JavaScript com um site de demonstração ao vivo](https://github.com/liamca/azure-search-javascript-samples)e [CognitiveSearchFrontEnd](https://github.com/LuisCabrer/CognitiveSearchFrontEnd).

> [!NOTE]
> Uma solicitação válida inclui diversos elementos, como uma URL de serviço e o caminho, o verbo HTTP, `api-version` etc. Para resumir, recortamos os exemplos para destacar apenas a sintaxe relevante para a paginação. Para obter mais informações sobre a sintaxe de solicitação, consulte [APIs de pesquisa cognitiva do Azure](https://docs.microsoft.com/rest/api/searchservice).
>

## <a name="total-hits-and-page-counts"></a>Total de ocorrências e contagens de página

Mostrar o número total de resultados retornados por uma consulta e, em seguida, retornar esses resultados em pedaços menores, é fundamental para praticamente todas as páginas de pesquisa.

![][1]

Na Pesquisa Cognitiva do Azure, você usa os `$count` `$top`parâmetros e `$skip` parâmetros para retornar esses valores. O exemplo a seguir mostra uma solicitação de amostra para acertos `@odata.count`totais em um índice chamado "catálogo on-line", retornado como :

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

No Azure Cognitive Search, `$select` você usaria uma [solicitação de API de pesquisa](https://docs.microsoft.com/rest/api/searchservice/search-documents) para implementar essa experiência.

Para retornar um subconjunto dos campos para um layout lado a lado:

    GET /indexes/online-catalog/docs?search=*&$select=productName,imageFile,description,price,rating

Arquivos de imagem e de mídia não são diretamente pesquisáveis e devem ser armazenados em outra plataforma de armazenamento, por exemplo, o armazenamento do Blob do Azure, para reduzir os custos. No índice e nos documentos, defina um campo que armazena o endereço da URL do conteúdo externo. Em seguida, use o campo como uma referência de imagem. A URL da imagem deve estar no documento.

Para recuperar uma página de descrição de produto em um evento **onClick** , use [Pesquisar Documento](https://docs.microsoft.com/rest/api/searchservice/Lookup-Document) para passar a chave do documento que será recuperada. O tipo de dados da chave é `Edm.String`. Neste exemplo, é *246810*.

    GET /indexes/online-catalog/docs/246810

## <a name="sort-by-relevance-rating-or-price"></a>Classificar por relevância, por classificação ou por preço

Por padrão, as ordens de classificação normalmente usam a opção de relevância, mas é comum disponibilizar ordens de classificação alternativas para que os clientes possam reorganizar rapidamente esses resultados em uma ordem de classificação diferente.

 ![][3]

Na Pesquisa Cognitiva do Azure, `$orderby` a classificação é baseada na `"Sortable": true.` `$orderby` expressão, para todos os campos indexados como Uma cláusula é uma expressão OData. Para saber informações sobre sintaxe, confira [Sintaxe de expressão do OData para cláusulas filter e order-by](query-odata-filter-orderby-syntax.md).

A relevância está fortemente associada a perfis de pontuação. Você pode usar a pontuação padrão, que depende de análise de texto e estatísticas para classificar todos os resultados, com pontuações mais altas sendo atribuídas a documentos com mais correspondências, ou correspondências mais sólidas, de um termo de pesquisa.

As ordens de classificação alternativas normalmente são associadas a eventos **onClick** que remetem a um método que cria a ordem de classificação. Por exemplo, este elemento de página:

 ![][4]

Você criaria um método que aceitasse a opção de classificação selecionada como entrada, e retornaria uma lista ordenada dos critérios associados a essa opção.

 ![][5]

> [!NOTE]
> Embora a pontuação padrão seja suficiente para muitos cenários, recomendamos basear a relevância em um perfil personalizado de pontuação. Um perfil personalizado de pontuação permite um aumento dos itens mais úteis para o seu negócio. Consulte [Adicionar perfis de pontuação](index-add-scoring-profiles.md) para obter mais informações.
>

## <a name="hit-highlighting"></a>Realce de ocorrência

Você pode aplicar a formatação aos termos correspondentes nos resultados de pesquisa, facilitando a correspondência. As instruções de destaque de acerto são fornecidas na [solicitação de consulta](https://docs.microsoft.com/rest/api/searchservice/search-documents). 

A formatação é aplicada a consultas de termos inteiros. Consultas em termos parciais, como pesquisa difusa ou pesquisa curinga que resultam em expansão de consulta no motor, não podem usar o destaque de hit.

```http
POST /indexes/hotels/docs/search?api-version=2019-05-06 
    {  
      "search": "something",  
      "highlight": "Description"  
    }
```

> [!IMPORTANT]
> Os serviços criados após 15 de julho de 2020 proporcionarão uma experiência de destaque diferente. Os serviços criados antes dessa data não mudarão em seu comportamento de destaque. Com esta mudança, apenas frases que correspondam à consulta de frasecompleta completa serão devolvidas. Além disso, será possível especificar o tamanho do fragmento devolvido para o destaque.
>
> Quando estiver escrevendo o código do cliente que implementa o destaque de hits, esteja ciente dessa alteração. Observe que isso não irá impactá-lo a menos que você crie um serviço de pesquisa completamente novo.

## <a name="faceted-navigation"></a>Navegação facetada

A navegação de pesquisa é comum em uma página de resultados, e normalmente fica na lateral ou na parte superior de uma página. No Azure Cognitive Search, a navegação facetada fornece pesquisa autodirecionada com base em filtros predefinidos. Consulte [navegação facetada em Azure Cognitive Search](search-faceted-navigation.md) para obter detalhes.

## <a name="filters-at-the-page-level"></a>Filtros no nível da página

Se o design da solução incluiu páginas de pesquisa dedicadas para tipos específicos de conteúdo (por exemplo, um aplicativo de varejo online que tem departamentos listados na parte superior da página), você pode inserir uma expressão de [filtro](search-filters.md) ao lado de um evento **onClick** para abrir uma página em um estado pré-filtrado.

Você pode enviar um filtro com ou sem uma expressão de pesquisa. Por exemplo, a seguinte solicitação filtrará o nome da marca, retornando somente os documentos que correspondem a ele.

    GET /indexes/online-catalog/docs?$filter=brandname eq 'Microsoft' and category eq 'Games'

Consulte [documentos de pesquisa (Azure Cognitive Search API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) para obter mais informações sobre `$filter` expressões.

## <a name="see-also"></a>Consulte também

- [API REST da Pesquisa Cognitiva do Azure](https://docs.microsoft.com/rest/api/searchservice)
- [Operações de Índice](https://docs.microsoft.com/rest/api/searchservice/Index-operations)
- [Operações de documentos](https://docs.microsoft.com/rest/api/searchservice/Document-operations)
- [Navegação facetada na pesquisa cognitiva do Azure](search-faceted-navigation.md)

<!--Image references-->
[1]: ./media/search-pagination-page-layout/Pages-1-Viewing1ofNResults.PNG
[2]: ./media/search-pagination-page-layout/Pages-2-Tiled.PNG
[3]: ./media/search-pagination-page-layout/Pages-3-SortBy.png
[4]: ./media/search-pagination-page-layout/Pages-4-SortbyRelevance.png
[5]: ./media/search-pagination-page-layout/Pages-5-BuildSort.png
