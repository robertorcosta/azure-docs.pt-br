---
title: Classificação semântica
titleSuffix: Azure Cognitive Search
description: Saiba como o algoritmo de classificação semântica funciona no Azure Pesquisa Cognitiva.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: 1406f865d60e6715b5f6a974225dc48958e8da6d
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104775163"
---
# <a name="semantic-ranking-in-azure-cognitive-search"></a>Classificação semântica no Azure Pesquisa Cognitiva

> [!IMPORTANT]
> Os recursos de pesquisa semântica estão em visualização pública, disponíveis somente por meio da API REST de visualização. Os recursos de visualização são oferecidos no estado em que se encontram, sob [termos de uso suplementares](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)e não têm a garantia de ter a mesma implementação em disponibilidade geral. Esses recursos são faturáveis. Para obter mais informações, consulte [disponibilidade e preços](semantic-search-overview.md#availability-and-pricing).

A classificação semântica é uma extensão do pipeline de execução de consulta que melhora a precisão e a RECALL, reclassificando as principais correspondências de um conjunto de resultados inicial. A classificação semântica é apoiada por uma máquina de ponta que lê modelos de compreensão, treinados para consultas expressas em linguagem natural, em oposição à correspondência lingüística em palavras-chave. Em contraste com o [algoritmo de classificação de similaridade padrão](index-ranking-similarity.md), o classificador semântico usa o contexto e o significado de palavras para determinar a relevância.

A classificação semântica é muito demorada para o recurso e o tempo. Para concluir o processamento dentro da latência esperada de uma operação de consulta, as entradas são consolidadas e simplificadas para que o resumo e a análise possam ser concluídos o mais rápido possível.

## <a name="preparation-for-semantic-ranking"></a>Preparação para classificação semântica

Antes da Pontuação de relevância, o conteúdo deve ser reduzido a uma quantidade de parâmetros que podem ser tratados com eficiência pelo classificador semântico. A redução de conteúdo inclui a seguinte sequência de etapas.

1. A redução de conteúdo começa usando os resultados iniciais retornados pelo [algoritmo de classificação de similaridade](index-ranking-similarity.md) padrão usado para pesquisa de palavra-chave. Os resultados da pesquisa podem incluir até 1.000 correspondências, mas a classificação semântica processará apenas os 50 principais. 

   Dada a consulta, os resultados iniciais podem ser muito menores que 50, dependendo de quantas correspondências foram encontradas. Seja qual for a contagem de documentos, o conjunto de resultados inicial será o corpus do documento para classificação semântica.

1. No Corpus do documento, o conteúdo de cada campo em "searchFields" é extraído e combinado em uma cadeia de caracteres longa.

1. Todas as cadeias de caracteres que são excessivamente longas são cortadas para garantir que o comprimento geral atenda aos requisitos de entrada do modelo de resumo. Esse exercício de corte é o motivo pelo qual é importante posicionar os campos concisos primeiro em "searchFields", para garantir que eles sejam incluídos na cadeia de caracteres. Se você tiver documentos muito grandes com campos com texto pesado, qualquer coisa após o limite máximo será ignorada.

Cada documento agora é representado por uma única cadeia de caracteres longa.

> [!NOTE]
> As entradas de parâmetro para os modelos são tokens, não caracteres ou palavras. A geração de tokens é determinada em parte pela atribuição do analisador em campos pesquisáveis. Para obter informações sobre como as cadeias de caracteres são indexadas, você pode examinar a saída do token de um analisador usando a [API REST do Test Analyzer](/rest/api/searchservice/test-analyzer).
>
> Atualmente nesta visualização, cadeias de caracteres longas podem ter um máximo de 8.000 tokens de tamanho. Se a pesquisa falhar em fornecer uma resposta esperada do fundo em um documento, saber mais sobre a remoção de conteúdo ajuda você a entender o porquê. 

## <a name="summarization"></a>Resumo

Após a redução da cadeia de caracteres, agora é possível passar os parâmetros por meio da compreensão da leitura do computador e da representação de linguagem para determinar quais frases e frases melhor resumem o modelo, em relação à consulta.

As entradas de resumo são a cadeia de caracteres longa da fase de preparação. Nessa entrada, o modelo de resumo avalia o conteúdo para encontrar passagens que são mais representativas.

A saída é uma [legenda semântica](semantic-how-to-query-request.md), em texto sem formatação e com destaques. A legenda é menor que a cadeia de caracteres longa, geralmente menos de 200 palavras por documento, e é considerada a mais representativa do documento. 

Uma [resposta semântica](semantic-answers.md) também será retornada se você tiver especificado o parâmetro "respostas", se a consulta foi apresentada como uma pergunta, e se uma passagem pode ser encontrada na cadeia de caracteres longa que provavelmente fornece uma resposta para a pergunta.

## <a name="scoring-and-ranking"></a>Pontuação e classificação

Neste ponto, agora você tem legendas para cada documento. As legendas são avaliadas para fins de relevância para a consulta.

1. A pontuação é determinada pela avaliação de cada legenda para relevância conceitual e semântica, relativa à consulta fornecida.

   O diagrama a seguir fornece uma ilustração do que significa "relevância semântica". Considere o termo "capital", que poderia ser usado no contexto de finanças, legislação, geografia ou gramática. Se uma consulta incluir termos do mesmo espaço de vetor (por exemplo, "capital" e "investimento"), um documento que também inclui tokens no mesmo cluster resultará em uma pontuação maior do que não.

   :::image type="content" source="media/semantic-search-overview/semantic-vector-representation.png" alt-text="Representação de vetor para o contexto" border="true":::

1. A saída dessa fase é @search.rerankerScore atribuída a cada documento. Depois que todos os documentos são pontuados, eles são listados em ordem decrescente e incluídos na carga de resposta da consulta. A carga inclui respostas, texto sem formatação e legendas realçadas e todos os campos marcados como recuperáveis ou especificados em uma cláusula SELECT.

## <a name="next-steps"></a>Próximas etapas

A classificação semântica é oferecida em camadas padrão, em regiões específicas. Para obter mais informações sobre o disponível e a inscrição, consulte [disponibilidade e preços](semantic-search-overview.md#availability-and-pricing). Um novo tipo de consulta permite a classificação de relevância e as estruturas de resposta da pesquisa semântica. Para começar, [crie uma consulta semântica](semantic-how-to-query-request.md).

Como alternativa, examine os artigos a seguir sobre a classificação padrão. A classificação semântica depende do classificador de similaridade para retornar os resultados iniciais. Conhecer a execução e a classificação da consulta fornecerá uma ampla compreensão de como o processo inteiro funciona.

+ [Pesquisa de texto completo no Azure Pesquisa Cognitiva](search-lucene-query-architecture.md)
+ [Similaridade e pontuação no Azure Cognitive Search](index-similarity-and-scoring.md)
+ [Analisadores para processamento de texto no Azure Pesquisa Cognitiva](search-analyzers.md)