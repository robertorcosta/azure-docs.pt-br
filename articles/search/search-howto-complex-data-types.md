---
title: Como modelar tipos de dados complexos
titleSuffix: Azure Cognitive Search
description: Estruturas de dados aninhados ou hierárquicas podem ser modeladas em um índice de pesquisa cognitiva do Azure usando tipos de dados ComplexType e Collections.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
tags: complex data types; compound data types; aggregate data types
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 2edd62825de08becf22f2f953a63a7f89f55e0a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283050"
---
# <a name="how-to-model-complex-data-types-in-azure-cognitive-search"></a>Como modelar tipos de dados complexos no Azure Cognitive Search

Conjuntos de dados externos usados para preencher um índice de pesquisa cognitiva do Azure podem vir em muitas formas. Às vezes incluem subestruturas hierárquicas ou aninhadas. Exemplos podem incluir vários endereços para um único cliente, várias cores e tamanhos para um único SKU, vários autores de um único livro e assim por diante. Em termos de modelagem, você pode ver essas estruturas referidas como tipos de dados *complexos,* *compostos,* *compostos*ou *agregados.* O termo Azure Cognitive Search usa para este conceito é **do tipo complexo**. Na Pesquisa Cognitiva do Azure, tipos complexos são modelados usando **campos complexos.** Um campo complexo é um campo que contém crianças (subcampos) que podem ser de qualquer tipo de dados, incluindo outros tipos complexos. Isso funciona de forma semelhante aos tipos de dados estruturados em uma linguagem de programação.

Campos complexos representam um único objeto no documento ou uma matriz de objetos, dependendo do tipo de dados. Campos de `Edm.ComplexType` tipo representam objetos `Collection(Edm.ComplexType)` únicos, enquanto campos de tipo representam matrizes de objetos.

O Azure Cognitive Search suporta nativamente tipos e coleções complexas. Esses tipos permitem que você modele quase qualquer estrutura JSON em um índice de Pesquisa Cognitiva Do Azure. Nas versões anteriores do Azure Cognitive Search APIs, apenas conjuntos de linhas achatadas poderiam ser importados. Na versão mais recente, seu índice agora pode corresponder mais de perto aos dados de origem. Em outras palavras, se seus dados de origem tiverem tipos complexos, seu índice também pode ter tipos complexos.

Para começar, recomendamos o [conjunto de dados hotéis](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md), que você pode carregar no assistente de dados **Importação** no portal Azure. O assistente detecta tipos complexos na fonte e sugere um esquema de índice baseado nas estruturas detectadas.

> [!Note]
> O suporte para tipos complexos está geralmente disponível em `api-version=2019-05-06`. 
>
> Se sua solução de pesquisa for construída em soluções de trabalho anteriores de conjuntos de dados achatados em uma coleção, você deverá alterar seu índice para incluir tipos complexos como suportado na versão mais recente da API. Para obter mais informações sobre como atualizar as versões da API, consulte [Atualizar para a versão mais recente da API REST](search-api-migration.md) ou atualizar para a versão mais recente do [.NET SDK](search-dotnet-sdk-migration-version-9.md).

## <a name="example-of-a-complex-structure"></a>Exemplo de uma estrutura complexa

O documento JSON a seguir é composto por campos simples e campos complexos. Campos complexos, `Address` `Rooms`como e , têm subcampos. `Address`tem um único conjunto de valores para esses subcampos, uma vez que é um único objeto no documento. Em contraste, `Rooms` tem vários conjuntos de valores para seus subcampos, um para cada objeto na coleção.

```json
{
  "HotelId": "1",
  "HotelName": "Secret Point Motel",
  "Description": "Ideally located on the main commercial artery of the city in the heart of New York.",
  "Address": {
    "StreetAddress": "677 5th Ave",
    "City": "New York",
    "StateProvince": "NY"
  },
  "Rooms": [
    {
      "Description": "Budget Room, 1 Queen Bed (Cityside)",
      "Type": "Budget Room",
      "BaseRate": 96.99
    },
    {
      "Description": "Deluxe Room, 2 Double Beds (City View)",
      "Type": "Deluxe Room",
      "BaseRate": 150.99
    },
  ]
}
```

## <a name="creating-complex-fields"></a>Criando campos complexos

Como em qualquer definição de índice, você pode usar o portal, [a API REST](https://docs.microsoft.com/rest/api/searchservice/create-index)ou [o .NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) para criar um esquema que inclua tipos complexos. 

O exemplo a seguir mostra um esquema de índice JSON com campos simples, coleções e tipos complexos. Observe que dentro de um tipo complexo, cada subcampo tem um tipo e pode ter atributos, assim como os campos de nível superior fazem. O esquema corresponde aos dados de exemplo acima. `Address`é um campo complexo que não é uma coleção (um hotel tem um endereço). `Rooms`é um complexo campo de coleta (um hotel tem muitos quartos).

```json
{
  "name": "hotels",
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false },
    { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
    { "name": "Address", "type": "Edm.ComplexType",
      "fields": [
        { "name": "StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true },
        { "name": "City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
        { "name": "StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true }
      ]
    },
    { "name": "Rooms", "type": "Collection(Edm.ComplexType)",
      "fields": [
        { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
        { "name": "Type", "type": "Edm.String", "searchable": true },
        { "name": "BaseRate", "type": "Edm.Double", "filterable": true, "facetable": true }
      ]
    }
  ]
}
```

## <a name="updating-complex-fields"></a>Atualização de campos complexos

Todas as regras de [reindexação](search-howto-reindex.md) que se aplicam aos campos em geral ainda se aplicam a campos complexos. Reintegrar algumas das principais regras aqui, adicionar um campo não requer uma reconstrução de índice, mas a maioria das modificações exigem.

### <a name="structural-updates-to-the-definition"></a>Atualizações estruturais para a definição

Você pode adicionar novos subcampos a um campo complexo a qualquer momento sem a necessidade de uma reconstrução de índice. Por exemplo, a adição `Address` de "ZipCode" ou "Comodidades" é `Rooms` permitida, assim como adicionar um campo de nível superior a um índice. Os documentos existentes têm um valor nulo para novos campos até que você preencha explicitamente esses campos atualizando seus dados.

Observe que dentro de um tipo complexo, cada subcampo tem um tipo e pode ter atributos, assim como os campos de nível superior fazem

### <a name="data-updates"></a>Atualizações de dados

A atualização dos documentos existentes `upload` em um índice com a ação funciona da mesma forma para campos complexos e simples -- todos os campos são substituídos. No `merge` entanto, (ou `mergeOrUpload` quando aplicado a um documento existente) não funciona da mesma forma em todos os campos. Especificamente, `merge` não suporta elementos de fusão dentro de uma coleção. Essa limitação existe para coleções de tipos primitivos e coleções complexas. Para atualizar uma coleção, você precisará recuperar o valor total da coleção, fazer alterações e, em seguida, incluir a nova coleção na solicitação de API do Índice.

## <a name="searching-complex-fields"></a>Pesquisando campos complexos

As expressões de pesquisa de forma livre funcionam como esperado com tipos complexos. Se qualquer campo ou subcampo pesquisável em qualquer lugar em um documento corresponder, então o documento em si é compatível.

As consultas ficam mais matizadas quando você tem vários termos e operadores, e alguns termos têm nomes de campo especificados, como é possível com a [sintaxe Lucene](query-lucene-syntax.md). Por exemplo, esta consulta tenta corresponder a dois termos, "Portland" e "OR", contra dois subcampos do campo Endereço:

    search=Address/City:Portland AND Address/State:OR

Consultas como esta não estão *relacionadas* para pesquisa de texto completo, ao contrário dos filtros. Nos filtros, as consultas sobre subcampos de uma coleção complexa estão correlacionadas usando variáveis de intervalo [ `any` em ou `all` ](search-query-odata-collection-operators.md). A consulta de Lucene acima devolve documentos contendo "Portland, Maine" e "Portland, Oregon", juntamente com outras cidades do Oregon. Isso acontece porque cada cláusula se aplica a todos os valores de seu campo em todo o documento, portanto não há conceito de um "subdocumento atual". Para obter mais informações sobre isso, consulte [Entendendo filtros de coleta OData na Pesquisa Cognitiva do Azure](search-query-understand-collection-filters.md).

## <a name="selecting-complex-fields"></a>Selecionando campos complexos

O `$select` parâmetro é usado para escolher quais campos são devolvidos nos resultados da pesquisa. Para usar este parâmetro para selecionar subcampos específicos de um campo complexo, inclua o`/`campo pai e o subcampo separados por uma barra ().

    $select=HotelName, Address/City, Rooms/BaseRate

Os campos devem ser marcados como recuperáveis no índice se você quiser que eles em resultados de pesquisa. Somente campos marcados como Recuperáveis `$select` podem ser usados em uma declaração.

## <a name="filter-facet-and-sort-complex-fields"></a>Filtre, faceta e classifique campos complexos

A mesma [sintaxe de caminho OData](query-odata-filter-orderby-syntax.md) usada para filtrar e fazer pesquisas em campo também pode ser usada para faceting, classificação e seleção de campos em uma solicitação de pesquisa. Para tipos complexos, aplicam-se regras que regem quais subcampos podem ser marcados como classificadores ou tabelas faciais. Para obter mais informações sobre essas regras, consulte a [referência de API de índice de criação](/rest/api/searchservice/create-index).

### <a name="faceting-sub-fields"></a>Subcampos de faceta

Qualquer subcampo pode ser marcado como facetable, a menos que seja do tipo `Edm.GeographyPoint` ou `Collection(Edm.GeographyPoint)`.

As contagens de documentos devolvidas nos resultados da faceta são calculadas para o documento pai (um hotel), não os subdocumentos em uma coleção complexa (quartos). Por exemplo, suponha que um hotel tenha 20 quartos do tipo "suíte". Dado este parâmetro `facet=Rooms/Type`de faceta, a contagem de facetas será uma para o hotel, não 20 para os quartos.

### <a name="sorting-complex-fields"></a>Triagem de campos complexos

As operações de classificação aplicam-se a documentos (Hotéis) e não a subdocumentos (Quartos). Quando você tem uma coleção de tipos complexos, como Rooms, é importante perceber que você não pode classificar em quartos em tudo. Na verdade, você não pode classificar em qualquer coleção.

As operações de classificação funcionam quando os campos têm um único valor por documento, seja o campo é um campo simples ou um subcampo em um tipo complexo. Por exemplo, `Address/City` é permitido ser classificado porque há apenas um `$orderby=Address/City` endereço por hotel, por isso classificará hotéis por cidade.

### <a name="filtering-on-complex-fields"></a>Filtragem em campos complexos

Você pode se referir a subcampos de um campo complexo em uma expressão de filtro. Basta usar a mesma [sintaxe](query-odata-filter-orderby-syntax.md) de caminho OData usada para faceting, classificação e seleção de campos. Por exemplo, o seguinte filtro retornará todos os hotéis no Canadá:

    $filter=Address/Country eq 'Canada'

Para filtrar em um campo de coleta complexo, você pode usar uma **expressão lambda** com os [ `any` operadores . `all` ](search-query-odata-collection-operators.md) Nesse caso, a variável de **alcance** da expressão lambda é um objeto com subcampos. Você pode consultar esses subcampos com a sintaxe de caminho OData padrão. Por exemplo, o seguinte filtro retornará todos os hotéis com pelo menos um quarto de luxo e todos os quartos para não fumantes:

    $filter=Rooms/any(room: room/Type eq 'Deluxe Room') and Rooms/all(room: not room/SmokingAllowed)

Como acontece com campos simples de nível superior, subcampos simples de campos complexos só `true` podem ser incluídos em filtros se tiverem o atributo **filtravel** definido na definição do índice. Para obter mais informações, consulte a [referência a API de índice de criação](/rest/api/searchservice/create-index).

## <a name="next-steps"></a>Próximas etapas

Experimente o [conjunto de dados hotéis](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md) no assistente de dados **Importação.** Você precisará das informações de conexão Cosmos DB fornecidas na readme para acessar os dados.

Com essas informações em mãos, o primeiro passo no assistente é criar uma nova fonte de dados Azure Cosmos DB. Mais adiante no assistente, quando você chegar à página de índice de destino, você verá um índice com tipos complexos. Crie e carregue esse índice e execute consultas para entender a nova estrutura.

> [!div class="nextstepaction"]
> [Quickstart: assistente de portal para importação, indexação e consultas](search-get-started-portal.md)
