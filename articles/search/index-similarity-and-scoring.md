---
title: Visão geral da similaridade e da Pontuação
titleSuffix: Azure Cognitive Search
description: Explica os conceitos de similaridade e pontuação e o que um desenvolvedor pode fazer para personalizar o resultado da pontuação.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 9f9cc4c29b117c83595a36c4e28b1edb428c3cde
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82254116"
---
# <a name="similarity-and-scoring-in-azure-cognitive-search"></a>Similaridade e pontuação no Azure Pesquisa Cognitiva

A pontuação refere-se à computação de uma pontuação de pesquisa para cada item retornado nos resultados da pesquisa para consultas de pesquisa de texto completo. A pontuação é um indicador de relevância de um item no contexto da operação de pesquisa atual. Quanto maior a pontuação, mais relevante será o item. Nos resultados da pesquisa, os itens são classificados de forma decrescente com base nas pontuações de pesquisa calculadas para cada item. 

Por padrão, os 50 principais são retornados na resposta, mas você pode usar o parâmetro **$Top** para retornar um número menor ou maior de itens (até 1000 em uma única resposta) e **$Skip** para obter o próximo conjunto de resultados.

A pontuação de pesquisa é calculada com base nas propriedades estatísticas dos dados e da consulta. O Azure Pesquisa Cognitiva localiza documentos que correspondem aos termos de pesquisa (alguns ou todos, dependendo do [searchmode](https://docs.microsoft.com/rest/api/searchservice/search-documents#searchmodeany--all-optional)), favorecendo documentos que contêm muitas instâncias do termo de pesquisa. A pontuação de pesquisa aumentará ainda mais se o termo for raro em todo o índice de dados, mas comum no documento. A base dessa abordagem para calcular a relevância é conhecida como *TF-IDF ou* termo Frequency-inverter frequência de documento.

Os valores de pontuação de pesquisa podem ser repetidos em todo um conjunto de resultados. Quando várias ocorrências têm a mesma pontuação de pesquisa, a ordem dos mesmos itens pontuados não é definida e não é estável. Execute a consulta novamente, e você poderá ver os itens mudarem de posição, especialmente se você estiver usando o serviço gratuito ou um serviço Faturável com várias réplicas. Se houver dois itens com uma pontuação idêntica, não há garantia de qual deles aparecerá primeiro.

Se você quiser quebrar o vínculo entre pontuações repetidas, poderá adicionar uma cláusula **$OrderBy** à primeira ordem por Pontuação e, em seguida, solicitar por outro campo classificável ( `$orderby=search.score() desc,Rating desc`por exemplo,). Para obter mais informações, consulte [$OrderBy](https://docs.microsoft.com/azure/search/search-query-odata-orderby).

> [!NOTE]
> Um `@search.score = 1.00` indica um conjunto de resultados sem pontuação ou não classificado. A pontuação é uniforme entre todos os resultados. Os resultados sem pontuação ocorrem quando o formulário de consulta é pesquisa difusa, consultas curinga ou Regex ou uma expressão de **$Filter** . 

## <a name="scoring-profiles"></a>Perfis de pontuação

Você pode personalizar a maneira como campos diferentes são classificados definindo um perfil de *Pontuação*personalizado. Os perfis de pontuação oferecem maior controle sobre a classificação de itens nos resultados da pesquisa. Por exemplo, convém aumentar itens com base em seu potencial de receita, promover itens mais recentes ou talvez aumentar itens que estejam no inventário há muito tempo. 

Um perfil de pontuação faz parte da definição de índice, composta por funções, parâmetros e campos ponderados. Para obter mais informações sobre como definir um, consulte [perfis de Pontuação](index-add-scoring-profiles.md).

## <a name="scoring-statistics"></a>Estatísticas de Pontuação

Para escalabilidade, o Azure Pesquisa Cognitiva distribui cada índice horizontalmente por meio de um processo de fragmentação, o que significa que partes de um índice são fisicamente separadas.

Por padrão, a pontuação de um documento é calculada com base nas propriedades estatísticas dos dados *dentro de um fragmento*. Essa abordagem geralmente não é um problema para uma grande corpus de dados e fornece um melhor desempenho do que ter de calcular a pontuação com base nas informações de todos os fragmentos. Dito isso, o uso dessa otimização de desempenho poderia fazer com que dois documentos muito semelhantes (ou até mesmo documentos idênticos) acabem com pontuações de relevância diferentes se acabarem em fragmentos diferentes.

Se preferir computar a pontuação com base nas propriedades estatísticas em todos os fragmentos, você pode fazer isso adicionando *scoringStatistics = global* como um [parâmetro de consulta](https://docs.microsoft.com/rest/api/searchservice/search-documents) (ou adicionar *"scoringStatistics": "global"* como um parâmetro de corpo da [solicitação de consulta](https://docs.microsoft.com/rest/api/searchservice/search-documents)).

```http
GET https://[service name].search.windows.net/indexes/[index name]/docs?scoringStatistics=global
  Content-Type: application/json
  api-key: [admin key]  
```

> [!NOTE]
> Uma chave de API de administrador é necessária para `scoringStatistics` o parâmetro.

## <a name="similarity-ranking-algorithms"></a>Algoritmos de classificação de similaridade

O Azure Pesquisa Cognitiva dá suporte a dois algoritmos de classificação de similaridade diferentes: um algoritmo de *similaridade clássico* e a implementação oficial do algoritmo *Okapi BM25* (atualmente em visualização). O algoritmo de similaridade clássico é o algoritmo padrão, mas a partir de 15 de julho, todos os novos serviços criados após essa data usam o novo algoritmo BM25. Ele será o único algoritmo disponível em novos serviços.

Por enquanto, você pode especificar qual algoritmo de classificação de similaridade você gostaria de usar. Para obter mais informações, consulte [algoritmo de classificação](index-ranking-similarity.md).

## <a name="watch-this-video"></a>Assista a este vídeo

Neste vídeo de 16 minutos, o engenheiro de software Raouf Merouche explica o processo de indexação, consulta e como criar perfis de pontuação. Ele oferece uma boa ideia do que está acontecendo nos bastidores enquanto seus documentos estão sendo indexados e recuperados.

>[!VIDEO https://channel9.msdn.com/Shows/AI-Show/Similarity-and-Scoring-in-Azure-Cognitive-Search/player]

+ 2-3 minutos abrange a indexação: processamento de texto e análise lexical.
+ 3-4 minutos abrange a indexação: índices invertidos.
+ 4-6 minutos abrange consulta: recuperação e classificação.
+ 7-16 minutos cobre os perfis de pontuação.

## <a name="see-also"></a>Confira também

 [Scoring Profiles](index-add-scoring-profiles.md) [Referência da API REST](https://docs.microsoft.com/rest/api/searchservice/) dos perfis de Pontuação   
 [Pesquisar API de documentos](https://docs.microsoft.com/rest/api/searchservice/search-documents)   
 [SDK do .NET Pesquisa Cognitiva do Azure](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)  
