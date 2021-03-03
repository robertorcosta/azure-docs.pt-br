---
title: Configurar algoritmo de similaridade de classificação
titleSuffix: Azure Cognitive Search
description: Como definir o algoritmo de similaridade para experimentar o novo algoritmo de similaridade para classificação
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 9f806b512ae8e118fca8f32115c8be3b493fd681
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101677782"
---
# <a name="configure-ranking-algorithms-in-azure-cognitive-search"></a>Configurar algoritmos de classificação no Azure Pesquisa Cognitiva

O Azure Pesquisa Cognitiva dá suporte a dois algoritmos de classificação de similaridade:

+ Um algoritmo de *similaridade clássico* , usado por todos os serviços de pesquisa até 15 de julho de 2020.
+ Uma implementação do algoritmo *Okapi BM25* , usada em todos os serviços de pesquisa criados após 15 de julho.

A classificação BM25 é o novo padrão porque tende a produzir classificações de pesquisa que se alinham melhor às expectativas do usuário. Ele também habilita as opções de configuração para ajustar os resultados com base em fatores como o tamanho do documento. Para novos serviços criados após 15 de julho de 2020, BM25 é usado automaticamente e é o único algoritmo de similaridade. Se você tentar definir a similaridade para ClassicSimilarity em um novo serviço, um erro HTTP 400 será retornado porque esse algoritmo não é suportado pelo serviço.

Para serviços mais antigos criados antes de 15 de julho de 2020, a similaridade clássica permanece o algoritmo padrão. Os serviços mais antigos podem definir propriedades em um índice de pesquisa para invocar BM25, conforme explicado abaixo. Se você estiver alternando de clássico para BM25, você pode esperar algumas diferenças de como os resultados da pesquisa são ordenados.

> [!NOTE]
> A pesquisa semântica é um algoritmo de reclassificação semântica adicional que reduz a lacuna entre as expectativas e os resultados ainda mais. Ao contrário dos outros algoritmos, é um recurso complementar que itera em um conjunto de resultados existente. Para usar o algoritmo de pesquisa semântica de visualização, você deve criar um novo serviço e deve especificar um [tipo de consulta semântica](semantic-how-to-query-request.md). Para obter mais informações, consulte [visão geral da pesquisa semântica](semantic-search-overview.md).

## <a name="create-a-search-index-for-bm25-scoring"></a>Criar um índice de pesquisa para a pontuação de BM25

Se você estiver executando um serviço de pesquisa criado antes de 15 de julho de 2020, poderá definir a propriedade de similaridade como BM25Similarity ou ClassicSimilarity na definição do índice. Se a propriedade de similaridade for omitida ou definida como NULL, o índice usará o algoritmo clássico.

O algoritmo de similaridade só pode ser definido no momento da criação do índice. No entanto, depois que um índice é criado com BM25, você pode atualizar o índice existente para definir ou modificar os parâmetros de BM25.

| Biblioteca do cliente | Propriedade de similaridade |
|----------------|---------------------|
| .NET  | [SearchIndex. similaridade](/dotnet/api/azure.search.documents.indexes.models.searchindex.similarity) |
| Java | [SearchIndex. setsimilaridade](/java/api/com.azure.search.documents.indexes.models.searchindex.setsimilarity) |
| JavaScript | [SearchIndex. similaridade](/javascript/api/@azure/search-documents/searchindex#similarity) |
| Python | [Propriedade de similaridade em SearchIndex](/python/api/azure-search-documents/azure.search.documents.indexes.models.searchindex) |

### <a name="rest-example"></a>Exemplo REST

Você também pode usar a [API REST](/rest/api/searchservice/create-index), como mostra o exemplo a seguir:

```http
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=2020-06-30
{
    "name": "indexName",
    "fields": [
        {
            "name": "id",
            "type": "Edm.String",
            "key": true
        },
        {
            "name": "name",
            "type": "Edm.String",
            "searchable": true,
            "analyzer": "en.lucene"
        },
        ...
    ],
    "similarity": {
        "@odata.type": "#Microsoft.Azure.Search.BM25Similarity"
    }
}
```

## <a name="bm25-similarity-parameters"></a>Parâmetros de similaridade de BM25

A similaridade BM25 adiciona dois parâmetros personalizáveis do usuário para controlar a pontuação de relevância calculada. Você pode definir parâmetros BM25 durante a criação do índice ou como uma atualização de índice se o algoritmo BM25 tiver sido especificado durante a criação do índice.

| Propriedade | Tipo | Descrição |
|----------|------|-------------|
| k1 | número | Controla a função de dimensionamento entre a frequência de termos de cada termo correspondente à pontuação de relevância final de um par de consulta de documento. Os valores geralmente são 0,0 a 3,0, com 1,2 como o padrão. </br></br>Um valor de 0,0 representa um "modelo binário", em que a contribuição de um único termo de correspondência é a mesma para todos os documentos correspondentes, independentemente de quantas vezes esse termo aparece no texto, enquanto um valor K1 maior permite que a pontuação continue aumentando conforme mais instâncias do mesmo termo forem encontradas no documento. </br></br>Usar um valor K1 mais alto pode ser importante nos casos em que esperamos que vários termos façam parte de uma consulta de pesquisa. Nesses casos, poderemos preferir favorecer documentos que correspondam a muitos dos diferentes termos de consulta sendo pesquisados em documentos que correspondem apenas a um único, várias vezes. Por exemplo, ao consultar o índice em busca de documentos que contenham os termos "Apollo spaceflight", podemos querer diminuir a pontuação de um artigo sobre o grego Mythology que contém o termo "Apollo", em algumas dúzias de vezes, sem mencionar "spaceflight", em comparação com outro artigo que menciona explicitamente "Apollo" e "spaceflight" a algumas vezes. |
| b | número | Controla como o comprimento de um documento afeta a pontuação de relevância. Os valores estão entre 0 e 1, com 0,75 como o padrão. </br></br>Um valor de 0,0 significa que o comprimento do documento não influenciará a pontuação, enquanto um valor de 1,0 significa que o impacto da frequência do termo na pontuação de relevância será normalizado pelo comprimento do documento. </br></br>Normalizar a frequência de termo pelo comprimento do documento é útil nos casos em que desejamos penalizar documentos mais longos. Em alguns casos, documentos mais longos (como um romance completo) são mais prováveis de conter muitos termos irrelevantes, em comparação com documentos muito menores. |

### <a name="setting-k1-and-b-parameters"></a>Definindo os parâmetros K1 e b

Para definir ou modificar os valores b ou K1, adicione-os ao objeto de similaridade BM25. A configuração ou a alteração desses valores em um índice existente colocará o índice offline por pelo menos alguns segundos, fazendo com que a indexação ativa e as solicitações de consulta falhem. Consequentemente, você deve definir o parâmetro "allowIndexDowntime = true" da solicitação de atualização:

```http
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=2020-06-30&allowIndexDowntime=true
{
    "similarity": {
        "@odata.type": "#Microsoft.Azure.Search.BM25Similarity",
        "b" : 0.5,
        "k1" : 1.3
    }
}
```

## <a name="see-also"></a>Confira também  

+ [Referência da API REST](/rest/api/searchservice/)
+ [Adicionar perfis de pontuação ao índice](index-add-scoring-profiles.md)
+ [Criar API de índice](/rest/api/searchservice/create-index)
+ [SDK do .NET do Azure Cognitive Search](/dotnet/api/overview/azure/search)