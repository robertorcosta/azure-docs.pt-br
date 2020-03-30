---
title: Atualizar versões da API REST
titleSuffix: Azure Cognitive Search
description: Revise as diferenças nas versões da API e saiba quais ações são necessárias para migrar o código existente para a versão mais recente do azure Cognitive Search Rest API.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: edb45eebc2c4eacc2f30d13988943f097a7190fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74112161"
---
# <a name="upgrade-to-the-latest-azure-cognitive-search-service-rest-api-version"></a>Atualize para a versão mais recente do serviço de pesquisa cognitiva do Azure REST API

Se você estiver usando uma versão anterior da [API Search REST,](https://docs.microsoft.com/rest/api/searchservice/)este artigo irá ajudá-lo a atualizar seu aplicativo para usar a versão de API mais recente mente disponível, 2019-05-06.

A versão 2019-05-06 da API REST contém algumas alterações em de suas versões anteriores. Elas são principalmente compatíveis com versões anteriores. Portanto, a alteração do código deve exigir apenas um mínimo de esforço, dependendo da versão que você estava usando antes. [As etapas para atualizar](#UpgradeSteps) delineiam as alterações de código necessárias para o uso de novos recursos.

> [!NOTE]
> Uma instância de serviço de pesquisa cognitiva do Azure suporta uma série de versões da API REST, incluindo as anteriores. Você pode continuar a usar essas versões de API, mas recomendamos migrar seu código para a versão mais recente para que você possa acessar novos recursos.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2019-05-06"></a>Novidades na versão 2019-05-06
A versão 2019-05-06 é a mais nova versão geralmente disponível da API REST. Os recursos que passaram para o status geralmente disponível nesta versão de API incluem:

* [Autocomplete](index-add-suggesters.md) é um recurso de digite-ahead que completa uma entrada de termo parcialmente especificada.

* [Tipos complexos](search-howto-complex-data-types.md) fornecem suporte nativo para dados estruturados de objetos no índice de pesquisa.

* [Os modos de análise JsonLines](search-howto-index-json-blobs.md), parte da indexação do Azure Blob, criam um documento de pesquisa por entidade JSON que é separado por uma nova linha.

* [O enriquecimento de IA](cognitive-search-concept-intro.md) fornece indexação que alavanca os motores de enriquecimento de IA dos Serviços Cognitivos.

Várias versões de recursos de pré-visualização coincidem com esta atualização geralmente disponível. Para revisar a lista de novos recursos de visualização, consulte [Search REST api-versão 2019-05-06-Preview](search-api-preview.md).

## <a name="breaking-changes"></a>Alterações da falha

O código existente contendo a seguinte funcionalidade será desmembrado na api-version=2019-05-06.

### <a name="indexer-for-azure-cosmos-db---datasource-is-now-type-cosmosdb"></a>Indexador do Azure Cosmos DB - datasource agora é "tipo": "cosmosdb"

Se você estiver usando um [indexador Cosmos DB,](search-howto-index-cosmosdb.md )você deve mudar `"type": "documentdb"` para `"type": "cosmosdb"`.

### <a name="indexer-execution-result-errors-no-longer-have-status"></a>Erros de resultado de execução do indexador não têm mais status

A estrutura de erro para execução do indexador anteriormente tinha um `status` elemento. Este elemento foi removido porque não estava fornecendo informações úteis.

### <a name="indexer-data-source-api-no-longer-returns-connection-strings"></a>A API de origem de dados do indexador não retorna mais as strings de conexão

Das versões da API 2019-05-06 e 2019-05-06-Preview em diante, a API de origem de dados não retorna mais as seqüências de conexão na resposta de qualquer operação REST. Nas versões anteriores da API, para fontes de dados criadas usando O POST, o Azure Cognitive Search retornou **201** seguido da resposta OData, que continha a seqüência de conexões em texto simples.

### <a name="named-entity-recognition-cognitive-skill-is-now-discontinued"></a>A habilidade cognitiva de reconhecimento de entidades está agora descontinuada

Se você chamar a habilidade [de reconhecimento de entidade](cognitive-search-skill-named-entity-recognition.md) de nome em seu código, a chamada falhará. A funcionalidade de substituição é [reconhecimento de entidades](cognitive-search-skill-entity-recognition.md). Você deve ser capaz de substituir a referência de habilidade por nenhuma outra mudança. A assinatura da API é a mesma para ambas as versões. 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Etapas da atualização
Se você estiver atualizando de uma versão GA anterior, 2017-11-11 ou 2016-09-01, provavelmente não terá que fazer nenhuma alteração no seu código, além de alterar o número da versão. As únicas situações em que talvez seja necessário alterar o código ocorrem quando:

* O código falha quando propriedades não reconhecidas são retornadas em uma resposta da API. Por padrão, o aplicativo deve ignorar propriedades que não entende.

* O código persiste solicitações de API e tenta reenviá-las à nova versão da API. Por exemplo, isso poderá acontecer se o aplicativo persistir tokens de continuação retornados da API de Pesquisa (para obter mais informações, procure `@search.nextPageParameters` na [Referência de API de Pesquisa](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)).

Se alguma dessas situações se aplicar a você, talvez seja necessário alterar o código da maneira adequada. Caso contrário, nenhuma alteração deve ser necessária a menos que você queira começar a usar os [novos recursos](#WhatsNew) da versão 2019-05-06.

Se você estiver atualizando a partir de uma versão de API de visualização, o acima também se aplica, mas você também deve estar ciente de que alguns recursos de visualização não estão disponíveis na versão 2019-05-06:

* [Consultas "Mais como esta"](search-more-like-this.md)
* [Indexação de blob CSV](search-howto-index-csv-blobs.md)
* [Suporte à API do MongoDB para indexadores Cosmos DB](search-howto-index-cosmosdb.md)

Se o seu código usar esses recursos, você não será capaz de atualizar para a versão da API 2019-05-06 sem remover o uso deles.

> [!IMPORTANT]
> As APIs em visualização servem para teste e avaliação e não devem ser usadas em ambientes de produção.
> 

### <a name="upgrading-complex-types"></a>Atualizando tipos complexos

Se o seu código usa tipos complexos com as versões mais antigas da API de visualização 2017-11-11-Preview ou 2016-09-01-Preview, existem alguns limites novos e alterados na versão 2019-05-06 das quais você precisa estar ciente:

+ Os limites da profundidade dos subcampos e o número de coleções complexas por índice foram reduzidos. Se você criou índices que excedem esses limites usando as versões de visualização da API, qualquer tentativa de atualizá-los ou recriá-los usando a versão aPI 2019-05-06 falhará. Se isso se aplica a você, você precisará redesenhar seu esquema para se encaixar dentro dos novos limites e, em seguida, reconstruir seu índice.

+ Há um novo limite na versão API 2019-05-06 sobre o número de elementos de coleções complexas por documento. Se você criou índices com documentos que excedem esses limites usando as versões de visualização da API, qualquer tentativa de reindexar esses dados usando a versão api 2019-05-06 falhará. Se isso se aplica a você, você precisará reduzir o número de elementos complexos de coleta por documento antes de reindexar seus dados.

Para obter mais informações, consulte [os limites de serviço para a pesquisa cognitiva do Azure](search-limits-quotas-capacity.md).

### <a name="how-to-upgrade-an-old-complex-type-structure"></a>Como atualizar uma estrutura de tipo antigo e complexo

Se o seu código estiver usando tipos complexos com uma das versões mais antigas da API de visualização, você pode estar usando um formato de definição de índice que se parece com isso:

```json
{
  "name": "hotels",  
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false },
    { "name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.microsoft" },
    { "name": "Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.microsoft" },
    { "name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true, "analyzer": "tagsAnalyzer" },
    { "name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true },
    { "name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true },
    { "name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address", "type": "Edm.ComplexType" },
    { "name": "Address/StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true },
    { "name": "Address/City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/PostalCode", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/Country", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Location", "type": "Edm.GeographyPoint", "filterable": true, "sortable": true },
    { "name": "Rooms", "type": "Collection(Edm.ComplexType)" }, 
    { "name": "Rooms/Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene" },
    { "name": "Rooms/Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene" },
    { "name": "Rooms/Type", "type": "Edm.String", "searchable": true },
    { "name": "Rooms/BaseRate", "type": "Edm.Double", "filterable": true, "facetable": true },
    { "name": "Rooms/BedOptions", "type": "Edm.String", "searchable": true },
    { "name": "Rooms/SleepsCount", "type": "Edm.Int32", "filterable": true, "facetable": true },
    { "name": "Rooms/SmokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true },
    { "name": "Rooms/Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "facetable": true, "analyzer": "tagsAnalyzer" }
  ]
}  
```

Um formato mais recente semelhante a uma árvore para definir campos de índice foi introduzido na versão API 2017-11-11-Preview. No novo formato, cada campo complexo tem uma coleção de campos onde seus subcampos são definidos. Na versão API 2019-05-06, este novo formato é usado exclusivamente e tentar criar ou atualizar um índice usando o formato antigo falhará. Se você tiver índices criados usando o formato antigo, você precisará usar a versão da API 2017-11-11-Preview para atualizá-los para o novo formato antes que eles possam ser gerenciados usando a versão aPI 2019-05-06.

Você pode atualizar índices "planos" para o novo formato com as seguintes etapas usando a versão aPI 2017-11-11-Preview:

1. Execute uma solicitação GET para recuperar seu índice. Se já está no novo formato, você está feito.

2. Traduza o índice do formato "plano" para o novo formato. Você terá que escrever código para isso, pois não há código de amostra disponível no momento desta escrita.

3. Execute uma solicitação PUT para atualizar o índice para o novo formato. Certifique-se de não alterar outros detalhes do índice, como a capacidade de pesquisa/filtrabilidade dos campos, uma vez que isso não é permitido pela API índice de atualização.

> [!NOTE]
> Não é possível gerenciar índices criados com o antigo formato "plano" do portal Azure. Atualize seus índices da representação "plana" para a representação "árvore" o mais rápido possível.

## <a name="next-steps"></a>Próximas etapas

Revise a documentação de referência da API Search REST. Se você encontrar problemas, peça ajuda no [StackOverflow](https://stackoverflow.com/) ou [no suporte de contato](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Referência de API REST do serviço de pesquisa](https://docs.microsoft.com/rest/api/searchservice/)

