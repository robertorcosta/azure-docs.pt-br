---
title: Como trabalhar com resultados de pesquisa
titleSuffix: Azure Cognitive Search
description: Estruturar e classificar os resultados da pesquisa, obter uma contagem de documentos e adicionar navegação de conteúdo aos resultados de pesquisa na Pesquisa Cognitiva do Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: df80668f5e4a31d6247e9e9806e3de0667fd9036
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656006"
---
# <a name="how-to-work-with-search-results-in-azure-cognitive-search"></a>Como trabalhar com resultados de pesquisa na Pesquisa Cognitiva do Azure

Este artigo explica como obter uma resposta de consulta que vem com uma contagem total de documentos correspondentes, resultados paginados, resultados classificados e termos destacados.

A estrutura de uma resposta é determinada por parâmetros na consulta: [Documento de pesquisa](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) na API REST ou [DocumentSearchResult Class](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult-1) no .NET SDK.

## <a name="result-composition"></a>Composição de resultados

Embora um documento de pesquisa possa consistir em um grande número de campos, normalmente apenas alguns são necessários para representar cada documento no conjunto de resultados. Em uma solicitação de `$select=<field list>` consulta, apêndice para especificar quais campos aparecem na resposta. Um campo deve ser atribuído como **Recuperável** no índice a ser incluído em um resultado. 

Os campos que funcionam melhor incluem aqueles que contrastam e se diferenciam entre os documentos, fornecendo informações suficientes para convidar uma resposta de clique por parte do usuário. Em um site de e-commerce, pode ser um nome de produto, descrição, marca, cor, tamanho, preço e classificação. Para a amostra incorporada do índice de amostra de hotéis- amostra, podem ser campos no seguinte exemplo:

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2019-05-06 
    {  
      "search": "sandy beaches",
      "select": "HotelId, HotelName, Description, Rating, Address/City"
      "count": true
    }
```

> [!NOTE]
> Se quiser incluir arquivos de imagem em um resultado, como uma foto ou logotipo do produto, armazene-os fora da Pesquisa Cognitiva do Azure, mas inclua um campo em seu índice para referenciar a URL de imagem no documento de pesquisa. Os índices de amostra que suportam imagens nos resultados incluem a demonstração **realestate-sample-us,** destaque neste [quickstart](search-create-app-portal.md), e o [aplicativo de demonstração New York City Jobs](https://aka.ms/azjobsdemo).

## <a name="paging-results"></a>Resultados da paginação

Por padrão, o mecanismo de pesquisa retorna até as primeiras 50 partidas, conforme determinado pela pontuação da pesquisa se a consulta for pesquisa de texto completa ou em uma ordem arbitrária para consultas exatas de correspondência.

Para retornar um número diferente `$top` de `$skip` documentos correspondentes, adicione e parâmetros à solicitação de consulta. A lista a seguir explica a lógica.

+ Adicione `$count=true` para obter uma contagem do número total de documentos correspondentes dentro de um índice.

+ Retorne o primeiro conjunto de 15 documentos correspondentes mais uma contagem de correspondências totais:`GET /indexes/<INDEX-NAME>/docs?search=<QUERY STRING>&$top=15&$skip=0&$count=true`

+ Retorne o segundo set, pulando os 15 primeiros `$top=15&$skip=15`para obter os próximos 15: . Faça o mesmo para o terceiro conjunto de 15:`$top=15&$skip=30`

Os resultados das consultas paginadas não são garantidos para serem estáveis se o índice subjacente estiver mudando. A paginação `$skip` altera o valor de cada página, mas cada consulta é independente e opera na visão atual dos dados como ela existe no índice no momento da consulta (em outras palavras, não há cache ou instantâneo de resultados, como aqueles encontrados em um banco de dados de propósito geral).
 
A seguir está um exemplo de como você pode obter duplicatas. Assuma um índice com quatro documentos:

    { "id": "1", "rating": 5 }
    { "id": "2", "rating": 3 }
    { "id": "3", "rating": 2 }
    { "id": "4", "rating": 1 }
 
Agora suponha que você quer resultados devolvidos dois de cada vez, ordenadopor classificação. Você executaria esta consulta para obter `$top=2&$skip=0&$orderby=rating desc`a primeira página de resultados: , produzindo os seguintes resultados:

    { "id": "1", "rating": 5 }
    { "id": "2", "rating": 3 }
 
No serviço, suponha que um quinto documento seja `{ "id": "5", "rating": 4 }`adicionado ao índice entre as chamadas de consulta: .  Pouco depois, você executa uma consulta para `$top=2&$skip=2&$orderby=rating desc`buscar a segunda página: , e obter esses resultados:

    { "id": "2", "rating": 3 }
    { "id": "3", "rating": 2 }
 
Observe que o documento 2 é buscado duas vezes. Isso porque o novo documento 5 tem um valor maior para classificação, por isso classifica antes do documento 2 e pousa na primeira página. Embora esse comportamento possa ser inesperado, é típico de como um mecanismo de busca se comporta.

## <a name="ordering-results"></a>Ordenando resultados

Para consultas completas de pesquisa de texto, os resultados são automaticamente classificados por um score de pesquisa, calculado com base na frequência de termo e proximidade em um documento, com pontuações mais altas indo para documentos com mais ou mais respostas em um termo de pesquisa. 

As pontuações de pesquisa transmitem um senso geral de relevância, refletindo a força da correspondência em comparação com outros documentos no mesmo conjunto de resultados. As pontuações nem sempre são consistentes de uma consulta para a próxima, então, à medida que você trabalha com consultas, você pode notar pequenas discrepâncias na forma como os documentos de pesquisa são ordenados. Há várias explicações para o porquê disso ocorrer.

| Causa | Descrição |
|-----------|-------------|
| Volatilidade de dados | O conteúdo do índice varia conforme você adiciona, modifica ou exclui documentos. As freqüências de termo serão mudadas à medida que as atualizações de índice são processadas ao longo do tempo, afetando os escores de pesquisa dos documentos correspondentes. |
| Múltiplas réplicas | Para serviços que usam várias réplicas, as consultas são emitidas contra cada réplica em paralelo. As estatísticas de índice utilizadas para calcular um escore de pesquisa são calculadas em uma base por réplica, com resultados mesclados e ordenados na resposta de consulta. As réplicas são principalmente espelhos um do outro, mas as estatísticas podem diferir devido a pequenas diferenças de estado. Por exemplo, uma réplica pode ter excluído documentos que contribuíram para suas estatísticas, que foram mescladas a partir de outras réplicas. Normalmente, as diferenças nas estatísticas por réplica são mais perceptíveis em índices menores. |
| Pontuações idênticas | Se vários documentos tiverem a mesma pontuação, qualquer um deles pode aparecer primeiro.  |

### <a name="consistent-ordering"></a>Ordem consistente

Dado o flex no pedido de resultados, você pode querer explorar outras opções se a consistência é um requisito de aplicação. A abordagem mais fácil é classificar por um valor de campo, como classificação ou data. Para cenários em que você deseja classificar por um campo específico, como [ `$orderby` ](query-odata-filter-orderby-syntax.md)uma classificação ou data, você pode definir explicitamente uma expressão , que pode ser aplicada a qualquer campo que seja indexado como **Sortable**.

Outra opção é usar um [perfil de pontuação personalizado.](index-add-scoring-profiles.md) Os perfis de pontuação dão mais controle sobre o ranking de itens nos resultados de pesquisa, com a capacidade de impulsionar partidas encontradas em campos específicos. A lógica adicional de pontuação pode ajudar a substituir pequenas diferenças entre as réplicas porque as pontuações de pesquisa de cada documento estão mais distantes. Recomendamos o [algoritmo de classificação](index-ranking-similarity.md) para esta abordagem.

## <a name="hit-highlighting"></a>Realce de ocorrência

O destaque de acerto refere-se à formatação de texto (como destaques em negrito ou amarelo) aplicada ao termo correspondente em um resultado, facilitando a marcação da partida. As instruções de destaque de acerto são fornecidas na [solicitação de consulta](https://docs.microsoft.com/rest/api/searchservice/search-documents). O mecanismo de pesquisa inclui o `highlightPreTag` termo `highlightPostTag`correspondente em tags e, e seu código lida com a resposta (por exemplo, aplicando uma fonte em negrito).

A formatação é aplicada a consultas de termos inteiros. No exemplo a seguir, os termos "arenoso", "areia", "praias", "praia" encontradas no campo Descrição são marcados para destaque. Consultas em termos parciais, como pesquisa difusa ou pesquisa curinga que resultam em expansão de consulta no motor, não podem usar o destaque de hit.

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2019-05-06 
    {  
      "search": "sandy beaches",  
      "highlight": "Description"
    }
```

> [!IMPORTANT]
> Os serviços criados após 15 de julho de 2020 proporcionarão uma experiência de destaque diferente. Os serviços criados antes dessa data não mudarão em seu comportamento de destaque. Com esta mudança, apenas frases que correspondam à consulta de frasecompleta completa serão devolvidas. Além disso, será possível especificar o tamanho do fragmento devolvido para o destaque.
>
> Quando estiver escrevendo o código do cliente que implementa o destaque de hits, esteja ciente dessa alteração. Observe que isso não irá impactá-lo a menos que você crie um serviço de pesquisa completamente novo.

## <a name="next-steps"></a>Próximas etapas

Para gerar rapidamente uma página de pesquisa para seu cliente, considere essas opções:

+ [O Application Generator](search-create-app-portal.md), no portal, cria uma página HTML com uma barra de pesquisa, navegação facetada e área de resultados que inclui imagens.
+ [Criar seu primeiro aplicativo em C#](tutorial-csharp-create-first-app.md) é um tutorial que constrói um cliente funcional. O código de amostra demonstra consultas paginadas, renomeação e classificação.

Várias amostras de código incluem uma interface front-end web, que você pode encontrar aqui: [aplicativo de demonstração do New York City Jobs,](https://aka.ms/azjobsdemo) [código de amostra JavaScript com um site de demonstração ao vivo](https://github.com/liamca/azure-search-javascript-samples)e [CognitiveSearchFrontEnd](https://github.com/LuisCabrer/CognitiveSearchFrontEnd).