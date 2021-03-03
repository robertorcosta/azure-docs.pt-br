---
title: Estruturar uma resposta semântica
titleSuffix: Azure Cognitive Search
description: Descreve o algoritmo de classificação semântica no Pesquisa Cognitiva e como estruturar ' respostas semânticas ' e ' legendas semânticas ' a partir de um conjunto de resultados.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/02/2021
ms.custom: references_regions
ms.openlocfilehash: febbfd0f3def8e681107ef78d9478463b1c2a872
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679056"
---
# <a name="semantic-ranking-and-responses-in-azure-cognitive-search"></a>Classificação semântica e respostas no Azure Pesquisa Cognitiva

> [!IMPORTANT]
> O algoritmo de classificação semântica e as respostas/legendas semânticas estão em visualização pública, disponível apenas por meio da API REST de visualização. Os recursos de visualização são oferecidos no estado em que se encontram, sob [termos de uso complementares](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A classificação semântica melhora a precisão dos resultados da pesquisa, reclassificando as principais correspondências usando um modelo de classificação semântica treinado para consultas expressas em linguagem natural em vez de palavras-chave.

Este artigo descreve o algoritmo de classificação semântica e como uma resposta semântica é moldada. Uma resposta inclui legendas, em texto sem formatação e com destaques, e respostas (opcional).

+ As legendas semânticas são passagens de texto relevantes para a consulta extraída dos resultados da pesquisa. Eles podem ajudar a resumir um resultado quando campos de conteúdo individuais são muito grandes para a página resultados. As legendas apresentam destaques semânticos, permitindo que os usuários obtenham rapidamente resultados de consulta para encontrar os documentos mais relevantes, melhorando assim a experiência geral do usuário.

+ As respostas semânticas usam modelos de aprendizado de máquina do Bing para formular respostas para consultas que se parecem com perguntas. As respostas são selecionadas em uma lista de passagens mais relevantes para a consulta, conforme extraído dos principais documentos no conjunto de resultados da consulta. As respostas são retornadas como um objeto independente e de nível superior na carga de resposta de consulta que você pode escolher para renderizar nas páginas de pesquisa, juntamente com os resultados da pesquisa.

## <a name="prerequisites"></a>Pré-requisitos

+ Consultas formuladas usando o tipo de consulta semântica. Para obter mais informações, consulte [criar uma consulta semântica](semantic-how-to-query-request.md).

## <a name="understanding-a-semantic-response"></a>Compreendendo uma resposta semântica

Uma resposta semântica inclui novas propriedades para pontuações, legendas e respostas. Uma resposta semântica é criada a partir da resposta padrão, usando os primeiros 50 resultados retornados pelo [mecanismo de pesquisa de texto completo](search-lucene-query-architecture.md), que são então reclassificados usando o classificador semântico. Se mais de 50 forem especificados, os resultados adicionais serão retornados, mas eles não serão reclassificados semanticamente.

Assim como acontece com todas as consultas, uma resposta é composta de todos os campos marcados como recuperáveis ou apenas dos campos listados na instrução SELECT. Ele também inclui um campo "responder" e "legendas".

+ Para cada resultado semântico, por padrão, há uma "resposta", retornada como um campo distinto que você pode escolher para renderizar em uma página de pesquisa. Você pode especificar até cinco. A formulação da resposta é automatizada: lendo todos os documentos nos resultados iniciais, executando o resumo de extração, seguido pela compreensão da leitura do computador e, por fim, promovendo uma resposta direta para a pergunta do usuário no campo de resposta.

+ Uma "legenda" é um resumo baseado em extração de conteúdo de documento, retornado em texto sem formatação ou com destaques. As legendas são incluídas automaticamente e não podem ser suprimidas. Os destaques são aplicados usando a compreensão da leitura do computador para identificar quais cadeias de caracteres devem ser enfatizadas. Os destaques denotam sua atenção às passagens mais relevantes, para que você possa examinar rapidamente uma página de resultados para localizar o documento certo.

Um conjunto de resultados classificado semanticamente ordenado resulta no @search.rerankerScore valor. Se você adicionar uma cláusula OrderBy, um erro HTTP 400 será retornado porque essa cláusula não tem suporte em uma consulta semântica.

## <a name="example"></a>Exemplo

O @search.rerankerScore existe junto com o padrão @search.score e é usado para reclassificar os resultados.

Dada a seguinte consulta:

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview
{
    "search": "newer hotel near the water with a great restaurant",
    "queryType": "semantic",
    "queryLanguage": "en-us",
    "answers": "none",
    "searchFields": "HotelName,Category,Description",
    "select": "HotelId,HotelName,Description,Category",
    "count": true
}
```

Você pode esperar ver o resultado a seguir, representativo de uma resposta semântica. Esses resultados mostram apenas as três principais respostas, conforme classificadas por " @search.rerankerScore ". Observe como o Resort Oceanside agora é classificado em primeiro lugar. Na classificação padrão de " @search.score ", esse resultado seria retornado em um segundo lugar, após o final das trilhas.

```http
{
    "@odata.count": 31,
    "@search.answers": [],
    "value": [
        {
            "@search.score": 1.8920634,
            "@search.rerankerScore": 1.1091284966096282,
            "@search.captions": [
                {
                    "text": "Oceanside Resort. Budget. New Luxury Hotel.  Be the first to stay. Bay views from every room, location near the piper, rooftop pool, waterfront dining & more.",
                    "highlights": "<em>Oceanside Resort.</em> Budget. New Luxury Hotel.  Be the first to stay.<em> Bay views</em> from every room, location near the piper, rooftop pool, waterfront dining & more."
                }
            ],
            "HotelId": "18",
            "HotelName": "Oceanside Resort",
            "Description": "New Luxury Hotel.  Be the first to stay. Bay views from every room, location near the piper, rooftop pool, waterfront dining & more.",
            "Category": "Budget"
        },
        {
            "@search.score": 2.5204072,
            "@search.rerankerScore": 1.0731962407007813,
            "@search.captions": [
                {
                    "text": "Trails End Motel. Luxury. Only 8 miles from Downtown.  On-site bar/restaurant, Free hot breakfast buffet, Free wireless internet, All non-smoking hotel. Only 15 miles from airport.",
                    "highlights": "<em>Trails End Motel.</em> Luxury. Only 8 miles from Downtown.  On-site bar/restaurant, Free hot breakfast buffet, Free wireless internet, All non-smoking hotel. Only 15 miles from airport."
                }
            ],
            "HotelId": "40",
            "HotelName": "Trails End Motel",
            "Description": "Only 8 miles from Downtown.  On-site bar/restaurant, Free hot breakfast buffet, Free wireless internet, All non-smoking hotel. Only 15 miles from airport.",
            "Category": "Luxury"
        },
        {
            "@search.score": 1.4104348,
            "@search.rerankerScore": 1.06992666143924,
            "@search.captions": [
                {
                    "text": "Winter Panorama Resort. Resort and Spa. Newly-renovated with large rooms, free 24-hr airport shuttle & a new restaurant. Rooms/suites offer mini-fridges & 49-inch HDTVs.",
                    "highlights": "<em>Winter Panorama Resort</em>. Resort and Spa. Newly-renovated with large rooms, free 24-hr airport shuttle & a new restaurant. Rooms/suites offer mini-fridges & 49-inch HDTVs."
                }
            ],
            "HotelId": "12",
            "HotelName": "Winter Panorama Resort",
            "Description": "Newly-renovated with large rooms, free 24-hr airport shuttle & a new restaurant. Rooms/suites offer mini-fridges & 49-inch HDTVs.",
            "Category": "Resort and Spa"
        }
```

## <a name="next-steps"></a>Próximas etapas

+ [Visão geral da pesquisa semântica](semantic-search-overview.md)
+ [Algoritmo de similaridade](index-ranking-similarity.md)
+ [Criar uma consulta semântica](semantic-how-to-query-request.md)
+ [Criar uma consulta básica](search-query-create.md)