---
title: Algoritmo de Similaridade ranking
titleSuffix: Azure Cognitive Search
description: Como definir o algoritmo de similaridade para tentar novo algoritmo de similaridade para classificação
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/13/2020
ms.openlocfilehash: c327440649300533c94c2a1956e3c45f433c9780
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409967"
---
# <a name="ranking-algorithm-in-azure-cognitive-search"></a>Algoritmo de classificação na Pesquisa Cognitiva do Azure

> [!IMPORTANT]
> A partir de 15 de julho de 2020, os serviços de pesquisa recém-criados usarão a função de classificação BM25, que tem se mostrado na maioria dos casos para fornecer rankings de pesquisa que se alinham melhor com as expectativas dos usuários do que o ranking padrão atual.  Além do ranking superior, o BM25 também permite opções de configuração para ajustar resultados com base em fatores como o tamanho do documento.  
>
> Com esta mudança, você provavelmente verá pequenas mudanças na ordem dos resultados de sua pesquisa.   Para aqueles que querem testar o impacto dessa mudança, disponibilizamos na API 2019-05-06-Preview uma capacidade de habilitar a pontuação bm25 em novos índices.  

Este artigo descreve como você pode atualizar um serviço criado antes de 15 de julho de 2020 para usar o novo algoritmo de classificação BM25.

O Azure Cognitive Search usará a implementação oficial lucene do algoritmo Okapi BM25, *BM25Similarity*, que substituirá a implementação *classicsimilaridade* anteriormente usada. Como o algoritmo ClassicSimilarity mais antigo, BM25Similarity é uma função de recuperação semelhante a TF-IDF que usa a freqüência de termo (TF) e a freqüência de documento inverso (IDF) como variáveis para calcular pontuações de relevância para cada par de consultas de documentos, que é então usado para o ranking. Embora conceitualmente semelhante ao antigo algoritmo de Similaridade Clássica, o BM25 tem sua raiz na recuperação probabilística de informações para melhorá-lo. O BM25 também oferece opções avançadas de personalização, como permitir que o usuário decida como a relevância é dimensionada com a freqüência de termos compatíveis.

## <a name="how-to-test-bm25-today"></a>Como testar o BM25 hoje

Quando você cria um novo índice, você pode definir uma propriedade de "similaridade". Você precisará usar a versão *2019-05-06-Preview,* como mostrado abaixo.

```
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=2019-05-06-Preview
```

```json  
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

Para serviços criados antes de 15 de julho de 2020: Se a semelhança for omitida ou definida como nula, o índice usará o antigo algoritmo clássico de similaridade.

Para serviços criados após 15 de julho de 2020: Se a semelhança for omitida ou definida como nula, o índice usará o novo algoritmo de similaridade BM25.

Você também pode definir explicitamente o valor de similaridade como um dos dois valores a seguir: *"#Microsoft.Azure.Search.ClassicSimilarity"* ou *"#Microsoft.Azure.Search.BM25Similarity"*.


## <a name="bm25-similarity-parameters"></a>Parâmetros de similaridade BM25

A similaridade BM25 adiciona dois parâmetros personalizáveis do usuário para controlar a pontuação de relevância calculada:

### <a name="k1"></a>k1

O parâmetro *k1* controla a função de dimensionamento entre a freqüência de termo de cada termo correspondente à pontuação final de relevância de um par de consulta de documento.

Um valor de zero representa um "modelo binário", onde a contribuição de um único termo de correspondência é a mesma para todos os documentos correspondentes, independentemente de quantas vezes esse termo aparece no texto, enquanto um valor k1 maior permite que a pontuação continue a aumentar à medida que mais casos do mesmo termo são encontrados no documento. Por padrão, o Azure Cognitive Search usa um valor de 1,2 para o parâmetro k1. Usar um valor k1 mais alto pode ser importante nos casos em que esperamos que vários termos sejam parte de uma consulta de pesquisa. Nesses casos, podemos querer favorecer documentos que correspondam a muitos dos diferentes termos de consulta que estão sendo pesquisados sobre documentos que só correspondem a um único, várias vezes. Por exemplo, ao consultar o índice de documentos contendo os termos "Apollo Spaceflight", podemos querer baixar a pontuação de um artigo sobre mitologia grega que contém o termo "Apollo" algumas dezenas de vezes, sem menções de "Vôo Espacial", em comparação com outro artigo que menciona explicitamente "Apollo" e "Spaceflight" um punhado de vezes apenas. 
 
### <a name="b"></a>b

O parâmetro *b* controla como o comprimento de um documento afeta o escore de relevância.

Um valor de 0,0 significa que o comprimento do documento não influenciará a pontuação, enquanto um valor de 1,0 significa que o impacto da frequência de termo no escore de relevância será normalizado pelo comprimento do documento. O valor padrão usado na Pesquisa Cognitiva do Azure para o parâmetro b é 0,75. A normalização da freqüência de termo pelo comprimento do documento é útil nos casos em que queremos penalizar documentos mais longos. Em alguns casos, documentos mais longos (como um romance completo), são mais propensos a conter muitos termos irrelevantes, em comparação com documentos muito mais curtos.

### <a name="setting-k1-and-b-parameters"></a>Definição de parâmetros k1 e b

Para personalizar os valores b ou k1, basta adicioná-los como propriedades ao objeto de similaridade ao usar BM25:

```json
    "similarity": {
        "@odata.type": "#Microsoft.Azure.Search.BM25Similarity",
        "b" : 0.5,
        "k1" : 1.3
    }
```

O algoritmo de similaridade só pode ser definido na hora da criação do índice. Isso significa que o algoritmo de similaridade que está sendo usado não pode ser alterado para os índices existentes. Os parâmetros *"b"* e *"k1"* podem ser modificados ao atualizar uma definição de índice existente que usa BM25. Alterar esses valores em um índice existente levará o índice off-line por pelo menos alguns segundos, fazendo com que suas solicitações de indexação e consulta falhem. Por causa disso, você precisará definir o parâmetro "allowIndexDowntime=true" na seqüência de consulta da sua solicitação de atualização:

```http
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=[api-version]&allowIndexDowntime=true
```


## <a name="see-also"></a>Confira também  

 [Azure Cognitive Search REST](https://docs.microsoft.com/rest/api/searchservice/)   
 [Adicione perfis de pontuação ao seu índice](index-add-scoring-profiles.md)    
 [Criar índice &#40;Api de pesquisa cognitiva do Azure resto&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)   
  [Azure Cognitive Search .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)  
