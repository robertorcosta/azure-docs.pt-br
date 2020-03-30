---
title: Filtros de faceta para navegação de pesquisa em aplicativos
titleSuffix: Azure Cognitive Search
description: Filtrar critérios por identidade de segurança do usuário, geolocalização ou valores numéricos para reduzir os resultados de pesquisa em consultas no Azure Cognitive Search, um serviço de pesquisa em nuvem hospedado no Microsoft Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 082575a67ea43d62f322e177cff087e5bd572c27
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72792891"
---
# <a name="how-to-build-a-facet-filter-in-azure-cognitive-search"></a>Como construir um filtro de faceta na Pesquisa Cognitiva do Azure 

A navegação facetada é usada para filtragem autodirecionada nos resultados de consulta em um aplicativo de pesquisa, onde seu aplicativo oferece controles de interface do usuário para pesquisa de escopo para grupos de documentos (por exemplo, categorias ou marcas), e o Azure Cognitive Search fornece a estrutura de dados para apoiar a experiência. Neste artigo, examine rapidamente as etapas básicas para criar uma estrutura de navegação facetada criando a experiência de pesquisa que você deseja fornecer. 

> [!div class="checklist"]
> * Selecionar os campos para filtrar e facetar
> * Definir atributos no campo
> * Criar o índice e carregar dados
> * Adicionar filtros de faceta a uma consulta
> * Manipular resultados

As facetas são dinâmicas e retornadas em uma consulta. As respostas da pesquisa trazem com elas as categorias de faceta usadas para navegar pelos resultados. Se você não estiver familiarizado com facetas, o exemplo a seguir é uma ilustração de uma estrutura de navegação por faceta.

  ![](./media/search-filters-facets/facet-nav.png)

Novo com navegação por faceta e deseja mais detalhes? Veja [como implementar a navegação facetada no Azure Cognitive Search](search-faceted-navigation.md).

## <a name="choose-fields"></a>Selecionar campos

As facetas podem ser calculadas por campos de valor único e por coleções. Campos que funcionam melhor na navegação facetada têm baixa cardinalidade: um pequeno número de valores distintos que se repetem ao longo de documentos em seu corpus de pesquisa (por exemplo, uma lista de cores, países/regiões ou marcas). 

A faceta é habilitada em campo por campo quando `facetable` você `true`cria o índice definindo o atributo para . Você geralmente também deve `filterable` definir `true` o atributo para esses campos para que seu aplicativo de pesquisa possa filtrar nesses campos com base em facetas que o usuário final seleciona. 

Ao criar um índice usando a API REST, qualquer [tipo de](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) campo `facetable` que possa ser usado na navegação facetada é marcado como padrão:

+ `Edm.String`
+ `Edm.DateTimeOffset`
+ `Edm.Boolean`
+ Tipos de campo `Edm.Int32`numéricos: , `Edm.Int64``Edm.Double`
+ Coleções dos tipos acima (por `Collection(Edm.String)` `Collection(Edm.Double)`exemplo, ou )

Você não `Edm.GeographyPoint` `Collection(Edm.GeographyPoint)` pode usar ou campos na navegação facetada. Facetas funcionam melhor em campos com baixa cardinalidade. Devido à resolução de coordenadas geográficas, é raro que qualquer conjunto de coordenadas seja igual em um determinado conjunto de dados. Dessa maneira, as facetas não têm suporte para coordenadas geográficas. Seria necessário um campo de cidade ou região para facetar por local.

## <a name="set-attributes"></a>Definir atributos

Atributos de índice que controlam como um campo é usado são adicionados às definições de campo individual no índice. No exemplo a seguir, campos com baixa cardinalidade, `category` úteis para faceting, consistem em: (hotel, motel, hostel), `tags`e `rating`. Esses campos `filterable` têm `facetable` os atributos definidos explicitamente no exemplo a seguir para fins ilustrativos. 

> [!Tip]
> Como uma prática recomendada para o desempenho e otimização de armazenamento, desative a faceta para os campos que nunca devem ser usados como uma faceta. Em particular, os campos de string para valores únicos, como `"facetable": false` um ID ou nome do produto, devem ser definidos para evitar seu uso acidental (e ineficaz) na navegação facetada.


```json
{
  "name": "hotels",  
  "fields": [
    { "name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false },
    { "name": "baseRate", "type": "Edm.Double" },
    { "name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
    { "name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene" },
    { "name": "hotelName", "type": "Edm.String", "facetable": false },
    { "name": "category", "type": "Edm.String", "filterable": true, "facetable": true },
    { "name": "tags", "type": "Collection(Edm.String)", "filterable": true, "facetable": true },
    { "name": "parkingIncluded", "type": "Edm.Boolean",  "filterable": true, "facetable": true, "sortable": false },
    { "name": "smokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true, "sortable": false },
    { "name": "lastRenovationDate", "type": "Edm.DateTimeOffset" },
    { "name": "rating", "type": "Edm.Int32", "filterable": true, "facetable": true },
    { "name": "location", "type": "Edm.GeographyPoint" }
  ]
}
```

> [!Note]
> Essa definição de índice é copiada do [Criar um índice de pesquisa cognitiva do Azure usando a API REST](https://docs.microsoft.com/azure/search/search-create-index-rest-api). Ela é idêntica, exceto por diferenças superficiais nas definições de campo. Os `filterable` `facetable` atributos são explicitamente `category` `tags`adicionados `smokingAllowed`em `rating` campos `parkingIncluded`e campos. Na `filterable` prática, `facetable` e seria habilitado por padrão nesses campos ao usar a API REST. Ao usar o SDK .NET, esses atributos devem ser ativados explicitamente.

## <a name="build-and-load-an-index"></a>Criar e carregar um índice

Uma etapa intermediária (e talvez óbvia) é que você precisa [criar e preencher o índice](https://docs.microsoft.com/azure/search/search-get-started-dotnet#1---create-index) antes de formular uma consulta. Mencionamos essa etapa aqui para fins de integridade. Uma maneira de determinar se o índice está disponível é verificando a lista de índices no [portal](https://portal.azure.com).

## <a name="add-facet-filters-to-a-query"></a>Adicionar filtros de faceta a uma consulta

No código do aplicativo, construa uma consulta que especifica todas as partes de uma consulta válida, incluindo expressões de pesquisa, facetas, filtros, perfis de pontuação – tudo o que for usado para formular uma solicitação. O exemplo a seguir cria uma solicitação que cria a navegação por faceta com base no tipo de acomodação, classificação e outras comodidades.

```csharp
var sp = new SearchParameters()
{
    ...
    // Add facets
    Facets = new[] { "category", "rating", "parkingIncluded", "smokingAllowed" }.ToList()
};
```

### <a name="return-filtered-results-on-click-events"></a>Retornar resultados filtrados em eventos de clique

Quando o usuário final clica em um valor de faceta, o manipulador para o evento de clique deve usar uma expressão de filtro para perceber a intenção do usuário. Dada `category` uma faceta, clicar na categoria `$filter` "motel" é implementado com uma expressão que seleciona acomodações desse tipo. Quando um usuário clica em "motel" para indicar que apenas motéis `$filter=category eq 'motel'`devem ser mostrados, a próxima consulta que o aplicativo envia inclui .

O seguinte snippet de código adiciona a categoria ao filtro se um usuário seleciona um valor usando a faceta de categoria.

```csharp
if (!String.IsNullOrEmpty(categoryFacet))
    filter = $"category eq '{categoryFacet}'";
```

Se o usuário clicar em um valor de `tags`faceta para um campo de coleta como , por exemplo, o valor "pool", seu aplicativo deve usar a seguinte sintaxe de filtro:`$filter=tags/any(t: t eq 'pool')`

## <a name="tips-and-workarounds"></a>Dicas e soluções alternativas

### <a name="initialize-a-page-with-facets-in-place"></a>Inicializar uma página com facetas aplicadas

Se desejar inicializar uma página com facetas aplicadas, você poderá enviar uma consulta como parte da inicialização da página para propagar a página com uma estrutura de faceta inicial.

### <a name="preserve-a-facet-navigation-structure-asynchronously-of-filtered-results"></a>Preservar uma estrutura de navegação por faceta de maneira assíncrona dos resultados filtrados

Um dos desafios com a navegação de facetas no Azure Cognitive Search é que existem facetas apenas para resultados atuais. Na prática, é comum manter um conjunto estático de facetas para que o usuário possa navegar na ordem inversa, recolhendo etapas para explorar os caminhos alternativos por meio do conteúdo da pesquisa. 

Embora esse seja um caso de uso comum, não é algo que a estrutura de navegação por faceta atualmente forneça por padrão. Os desenvolvedores que desejam facetas estáticas normalmente contornam a limitação emitindo duas consultas filtradas: uma com escopo para os resultados e a outra usada para criar uma lista estática de facetas para fins de navegação.

## <a name="see-also"></a>Confira também

+ [Filtros na Pesquisa Cognitiva do Azure](search-filters.md)
+ [Criar API REST do índice](https://docs.microsoft.com/rest/api/searchservice/create-index)
+ [API REST para pesquisar documentos](https://docs.microsoft.com/rest/api/searchservice/search-documents)
