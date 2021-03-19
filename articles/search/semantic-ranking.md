---
title: Classificação semântica
titleSuffix: Azure Cognitive Search
description: Descreve o algoritmo de classificação semântica no Pesquisa Cognitiva.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: 01c4d6475ec23b8a55d91e18f49cab27760aa907
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104604280"
---
# <a name="semantic-ranking-in-azure-cognitive-search"></a>Classificação semântica no Azure Pesquisa Cognitiva

> [!IMPORTANT]
> Os recursos de pesquisa semântica estão em visualização pública, disponíveis somente por meio da API REST de visualização. Os recursos de visualização são oferecidos no estado em que se encontram, sob [termos de uso suplementares](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)e não têm a garantia de ter a mesma implementação em disponibilidade geral. Para obter mais informações, consulte [disponibilidade e preços](semantic-search-overview.md#availability-and-pricing).

A classificação semântica é uma extensão do pipeline de execução de consulta que melhora a precisão e a RECALL, reclassificando as principais correspondências de um conjunto de resultados inicial. A classificação semântica é apoiada por uma máquina aprofundada que lê modelos de compreensão, treinados para consultas expressas em linguagem natural, em oposição à correspondência lingüística em palavras-chave. Em contraste com o [algoritmo de classificação de similaridade padrão](index-ranking-similarity.md), o classificador semântico usa o contexto e o significado de palavras para determinar a relevância.

## <a name="how-semantic-ranking-works"></a>Como funciona a classificação semântica

A classificação semântica é muito demorada para o recurso e o tempo. Para concluir o processamento dentro da latência esperada de uma operação de consulta, o modelo usa como entrada apenas os principais documentos 50 retornados do algoritmo de [classificação de similaridade](index-ranking-similarity.md)padrão. Os resultados da classificação inicial podem incluir mais de 50 correspondências, mas apenas a primeira 50 será reclassificada semanticamente. 

Para classificação semântica, o modelo usa a compreensão de leitura do computador e o aprendizado de transferência para pontuar novamente os documentos com base em quão bem cada um corresponde à intenção da consulta.

### <a name="preparation-passage-extraction-phase"></a>Fase de preparação (extração de passagens)

Para cada documento nos resultados iniciais, há um exercício de extração de passagem que identifica as passagens de chave. Esse é um exercício downsizing que reduz o conteúdo para uma quantia que pode ser processada rapidamente.

1. Para cada um dos documentos 50, cada campo no parâmetro searchFields é avaliado em ordem consecutiva. O conteúdo de cada campo é consolidado em uma cadeia de caracteres longa. 

1. A cadeia de caracteres longa é cortada para garantir que o comprimento geral não tenha mais de 8.000 tokens. Por esse motivo, é recomendável posicionar os campos concisos primeiro para que eles sejam incluídos na cadeia de caracteres. Se você tiver documentos muito grandes com campos com texto pesado, qualquer coisa após o limite de token será ignorada.

1. Cada documento agora é representado por uma única cadeia de caracteres longa que tem até 8.000 tokens. Essas cadeias de caracteres são enviadas para o modelo de resumo, o que reduzirá ainda mais a cadeia de caracteres. O modelo de resumo avalia a cadeia de caracteres longa para frases-chave ou passagens que resumem melhor o documento ou respondem à pergunta.

1. A saída dessa fase é uma legenda (e, opcionalmente, uma resposta). A legenda tem no máximo 128 tokens por documento e é considerada a mais representativa do documento.

### <a name="scoring-and-ranking-phases"></a>Fases de Pontuação e classificação

Nesta fase, todas as legendas de 50 são avaliadas para avaliar a relevância.

1. A pontuação é determinada pela avaliação de cada legenda para relevância conceitual e semântica, relativa à consulta fornecida.

   O diagrama a seguir fornece uma ilustração do que significa "relevância semântica". Considere o termo "capital", que poderia ser usado no contexto de finanças, legislação, geografia ou gramática. Se uma consulta incluir termos do mesmo espaço de vetor (por exemplo, "capital" e "investimento"), um documento que também inclui tokens no mesmo cluster resultará em uma pontuação maior do que não.

   :::image type="content" source="media/semantic-search-overview/semantic-vector-representation.png" alt-text="Representação de vetor para o contexto" border="true":::

1. A saída dessa fase é @search.rerankerScore atribuída a cada documento. Depois que todos os documentos são pontuados, eles são listados em ordem decrescente e incluídos na carga de resposta da consulta.

## <a name="next-steps"></a>Próximas etapas

A classificação semântica é oferecida em camadas padrão, em regiões específicas. Para obter mais informações e se inscrever, consulte [disponibilidade e preços](semantic-search-overview.md#availability-and-pricing). Um novo tipo de consulta permite a classificação de relevância e as estruturas de resposta da pesquisa semântica. Para começar, [crie uma consulta semântica](semantic-how-to-query-request.md).

Como alternativa, examine qualquer um dos artigos a seguir para obter informações relacionadas.

+ [Visão geral da pesquisa semântica](semantic-search-overview.md)
+ [Retornar uma resposta semântica](semantic-answers.md)