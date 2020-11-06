---
title: Como trabalhar com os resultados da pesquisa
titleSuffix: Azure Cognitive Search
description: Estruturar e classificar resultados da pesquisa, obter uma contagem de documentos e adicionar navegação de conteúdo aos resultados da pesquisa no Azure Pesquisa Cognitiva.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: e583cedc04113615c50cc9906cbd11a99ff48683
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421712"
---
# <a name="how-to-work-with-search-results-in-azure-cognitive-search"></a>Como trabalhar com os resultados da pesquisa no Azure Pesquisa Cognitiva

Este artigo explica como obter uma resposta de consulta que é retornada com uma contagem total de documentos correspondentes, resultados paginados, resultados classificados e termos realçados de ocorrências.

A estrutura de uma resposta é determinada pelos parâmetros na consulta: [documento de pesquisa](/rest/api/searchservice/Search-Documents) na API REST ou na [classe SearchResults](/dotnet/api/azure.search.documents.models.searchresults-1) no SDK do .net.

## <a name="result-composition"></a>Composição de resultado

Embora um documento de pesquisa possa consistir em um grande número de campos, normalmente apenas alguns são necessários para representar cada documento no conjunto de resultados. Em uma solicitação de consulta, acrescente `$select=<field list>` para especificar quais campos aparecem na resposta. Um campo deve ser atribuído como **recuperável** no índice a ser incluído em um resultado. 

Os campos que funcionam melhor incluem aqueles que contrastam e diferenciam entre documentos, fornecendo informações suficientes para convidar uma resposta de clique por meio da parte do usuário. Em um site de comércio eletrônico, pode ser um nome de produto, uma descrição, uma marca, uma cor, um tamanho, um preço e uma classificação. Para o exemplo interno Hotéis-Sample-index, pode ser campos no exemplo a seguir:

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30 
    {  
      "search": "sandy beaches",
      "select": "HotelId, HotelName, Description, Rating, Address/City"
      "count": true
    }
```

> [!NOTE]
> Se desejar incluir arquivos de imagem em um resultado, como uma foto do produto ou logotipo, armazene-os fora do Azure Pesquisa Cognitiva, mas inclua um campo no índice para fazer referência à URL da imagem no documento de pesquisa. Os índices de exemplo que dão suporte a imagens nos resultados incluem a demonstração **realestate-Sample-US** , apresentada neste guia de [início rápido](search-create-app-portal.md)e o [aplicativo de demonstração de trabalhos da cidade de Nova York](https://aka.ms/azjobsdemo).

## <a name="paging-results"></a>Resultados da paginação

Por padrão, o mecanismo de pesquisa retorna até as primeiras 50 correspondências, conforme determinado pela pontuação de pesquisa se a consulta for de pesquisa de texto completo ou em uma ordem arbitrária para consultas de correspondência exata.

Para retornar um número diferente de documentos correspondentes, adicione `$top` `$skip` parâmetros e à solicitação de consulta. A lista a seguir explica a lógica.

+ Adicionar `$count=true` para obter uma contagem do número total de documentos correspondentes em um índice.

+ Retorne o primeiro conjunto de 15 documentos correspondentes mais uma contagem do total de correspondências: `GET /indexes/<INDEX-NAME>/docs?search=<QUERY STRING>&$top=15&$skip=0&$count=true`

+ Retorne o segundo conjunto, ignorando os primeiros 15 para obter os próximos 15: `$top=15&$skip=15` . Faça o mesmo para o terceiro conjunto de 15: `$top=15&$skip=30`

Os resultados das consultas paginadas não terão garantia de serem estáveis se o índice subjacente estiver sendo alterado. A paginação altera o valor de `$skip` para cada página, mas cada consulta é independente e opera na exibição atual dos dados conforme eles existem no índice no momento da consulta (em outras palavras, não há nenhum cache ou instantâneo de resultados, como aqueles encontrados em um banco de dados de uso geral).
 
Veja a seguir um exemplo de como você pode obter duplicatas. Suponha um índice com quatro documentos:

```text
{ "id": "1", "rating": 5 }
{ "id": "2", "rating": 3 }
{ "id": "3", "rating": 2 }
{ "id": "4", "rating": 1 }
```
 
Agora suponha que você deseja que os resultados retornassem dois de cada vez, ordenados por classificação. Você executaria essa consulta para obter a primeira página de resultados: `$top=2&$skip=0&$orderby=rating desc` , produzindo os seguintes resultados:

```text
{ "id": "1", "rating": 5 }
{ "id": "2", "rating": 3 }
```
 
No serviço, suponha que um quinto documento seja adicionado ao índice entre chamadas de consulta: `{ "id": "5", "rating": 4 }` .  Logo em seguida, você executa uma consulta para buscar a segunda página: `$top=2&$skip=2&$orderby=rating desc` e obter esses resultados:

```text
{ "id": "2", "rating": 3 }
{ "id": "3", "rating": 2 }
```
 
Observe que o documento 2 é buscado duas vezes. Isso ocorre porque o novo documento 5 tem um valor maior para classificação, então ele classifica antes do documento 2 e chega na primeira página. Embora esse comportamento possa ser inesperado, é comum que o mecanismo de pesquisa se comporta.

## <a name="ordering-results"></a>Ordenando resultados

Para consultas de pesquisa de texto completo, os resultados são classificados automaticamente por uma pontuação de pesquisa, calculados com base na frequência do termo e na proximidade em um documento, com pontuações mais altas passando para documentos com correspondências mais ou mais fortes em um termo de pesquisa. 

As pontuações de pesquisa transmitem o sentido geral de relevância, refletindo a força da correspondência em comparação com outros documentos no mesmo conjunto de resultados. As pontuações nem sempre são consistentes de uma consulta para a outra, assim, à medida que você trabalha com consultas, você pode notar pequenas discrepâncias em como os documentos de pesquisa são ordenados. Há várias explicações por que isso pode ocorrer.

| Causa | Descrição |
|-----------|-------------|
| Volatilidade de dados | O conteúdo do índice varia conforme você adiciona, modifica ou exclui documentos. As frequências de termo serão alteradas conforme as atualizações de índice forem processadas ao longo do tempo, afetando as pontuações de pesquisa de documentos correspondentes. |
| Várias réplicas | Para serviços que usam várias réplicas, as consultas são emitidas em cada réplica em paralelo. As estatísticas de índice usadas para calcular uma pontuação de pesquisa são calculadas por réplica, com os resultados mesclados e ordenados na resposta da consulta. As réplicas são, na maioria, espelhos umas das outras, mas as estatísticas podem ser diferentes devido a pequenas diferenças no estado. Por exemplo, uma réplica pode ter excluído documentos que contribuem para suas estatísticas, que foram mescladas de outras réplicas. Normalmente, as diferenças nas estatísticas por réplica são mais perceptíveis em índices menores. |
| Pontuações idênticas | Se vários documentos tiverem a mesma pontuação, qualquer um deles poderá aparecer primeiro.  |

### <a name="consistent-ordering"></a>Ordenação consistente

Considerando o Flex na ordenação de resultados, talvez você queira explorar outras opções se a consistência for um requisito de aplicativo. A abordagem mais fácil é classificar por um valor de campo, como classificação ou data. Para cenários em que você deseja classificar por um campo específico, como uma classificação ou data, você pode definir explicitamente uma [ `$orderby` expressão](query-odata-filter-orderby-syntax.md), que pode ser aplicada a qualquer campo que seja indexado como **classificável**.

Outra opção é usar um [perfil de Pontuação personalizado](index-add-scoring-profiles.md). Os perfis de Pontuação oferecem mais controle sobre a classificação de itens nos resultados da pesquisa, com a capacidade de aumentar as correspondências encontradas em campos específicos. A lógica de Pontuação adicional pode ajudar a substituir pequenas diferenças entre as réplicas, pois as pontuações de pesquisa de cada documento estão mais distantes. Recomendamos o [algoritmo de classificação](index-ranking-similarity.md) para essa abordagem.

## <a name="hit-highlighting"></a>Realce de ocorrência

Realce de ocorrências refere-se à formatação de texto (como, por exemplo, realces em negrito ou amarelo) aplicados aos termos correspondentes em um resultado, facilitando a identificação da correspondência. As instruções de realce de visita são fornecidas na [solicitação de consulta](/rest/api/searchservice/search-documents). 

Para habilitar o realce de clique, adicione `highlight=[comma-delimited list of string fields]` para especificar quais campos usarão o realce. O realce é útil para campos de conteúdo mais longos, como um campo de descrição, em que a correspondência não é imediatamente óbvia. Somente definições de campo que são atribuídas como que podem ser **pesquisadas** para realce de clique.

Por padrão, o Azure Pesquisa Cognitiva retorna até cinco destaques por campo. Você pode ajustar esse número acrescentando ao campo um traço seguido por um inteiro. Por exemplo, `highlight=Description-10` retorna até 10 destaques sobre o conteúdo correspondente no campo Descrição.

A formatação é aplicada a consultas de termo completo. O tipo de formatação é determinado pelas marcas e `highlightPreTag` `highlightPostTag` , e seu código manipula a resposta (por exemplo, aplicando uma fonte em negrito ou um plano de fundo amarelo).

No exemplo a seguir, os termos "arenoso", "areia", "praias", "praia" encontrados no campo Descrição são marcados para realce. As consultas que disparam a expansão de consulta no mecanismo, como pesquisa difusa e curinga, têm suporte limitado para realce de clique.

```http
GET /indexes/hotels-sample-index/docs/search=sandy beaches&highlight=Description?api-version=2020-06-30 
```

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30 
    {  
      "search": "sandy beaches",  
      "highlight": "Description"
    }
```

### <a name="new-behavior-starting-july-15"></a>Novo comportamento (a partir de 15 de julho)

Os serviços criados após 15 de julho de 2020 fornecerão uma experiência de realce diferente. Os serviços criados antes dessa data não serão alterados no comportamento de realce. 

Com o novo comportamento:

* Somente frases que correspondam à consulta de frase completa serão retornadas. A consulta "super campeonato" retornará destaques como este:

    ```html
    '<em>super bowl</em> is super awesome with a bowl of chips'
    ```
  Observe que o termo *campeonato de chips* não tem nenhum realce porque ele não corresponde à frase completa.

Quando você estiver escrevendo um código de cliente que implementa o realce de acesso, lembre-se dessa alteração. Observe que isso não afetará você a menos que você crie um serviço de pesquisa completamente novo.

## <a name="next-steps"></a>Próximas etapas

Para gerar rapidamente uma página de pesquisa para seu cliente, considere estas opções:

+ O [gerador de aplicativos](search-create-app-portal.md), no portal, cria uma página HTML com uma barra de pesquisa, navegação facetada e área de resultados que inclui imagens.
+ [Criar seu primeiro aplicativo em C#](tutorial-csharp-create-first-app.md) é um tutorial que cria um cliente funcional. O código de exemplo demonstra consultas paginadas, realce de visita e classificação.

Vários exemplos de código incluem uma interface de front-end da Web, que pode ser encontrada aqui: [aplicativo de demonstração de trabalhos de cidade de Nova York](https://aka.ms/azjobsdemo), [código de exemplo de JavaScript com um site de demonstração ao vivo](https://github.com/liamca/azure-search-javascript-samples)e [CognitiveSearchFrontEnd](https://github.com/LuisCabrer/CognitiveSearchFrontEnd).