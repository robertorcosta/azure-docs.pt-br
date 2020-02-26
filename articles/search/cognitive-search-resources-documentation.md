---
title: Links de documentação para enriquecimento de IA
titleSuffix: Azure Cognitive Search
description: Uma lista com anotações de artigos, tutoriais, exemplos e postagens de blog relacionados às cargas de trabalho de enriquecimento de IA na Pesquisa Cognitiva do Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 9f8a14c348f4f8eddcd60c6946c831c90262757b
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588987"
---
# <a name="documentation-resources-for-ai-enrichment-in-azure-cognitive-search"></a>Recursos de documentação para enriquecimento de IA na Pesquisa Cognitiva do Azure

O enriquecimento de IA é uma funcionalidade da indexação da Pesquisa Cognitiva do Azure que localiza informações latentes em fontes que não são texto e em texto não diferenciado, transformando-as em conteúdo pesquisável de texto completo na Pesquisa Cognitiva do Azure.

Os artigos a seguir são a documentação completa sobre o enriquecimento de IA.

## <a name="getting-started"></a>Introdução
+ [Introdução à IA na Pesquisa Cognitiva do Azure](cognitive-search-concept-intro.md)
+ [Início rápido: criar um conconhecimento de cognitiva no portal do Azure](cognitive-search-quickstart-blob.md)
+ [Tutorial: indexação aprimorada com o ia](cognitive-search-tutorial-blob.md)
+ [Exemplo: criando uma habilidade personalizada para o enriquecimento de ia](cognitive-search-create-custom-skill-example.md)

## <a name="how-to-guidance"></a>Diretrizes
+ [Como definir um conjunto de qualificações](cognitive-search-defining-skillset.md)
+ [Como referenciar anotações em um conjunto de qualificações](cognitive-search-concept-annotations-syntax.md)
+ [Como mapear campos a um índice](cognitive-search-output-field-mapping.md)
+ [Como processar e extrair informações de imagens](cognitive-search-concept-image-scenarios.md)
+ [Como recompilar um índice da Pesquisa Cognitiva do Azure](search-howto-reindex.md)
+ [Como definir uma interface de habilidades personalizadas](cognitive-search-custom-skill-interface.md)
+ [Dicas de solução de problemas](cognitive-search-concept-troubleshooting.md)

## <a name="reference"></a>Referência

+ [Habilidades internas](cognitive-search-predefined-skills.md)
  + [Microsoft.Skills.Text.KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md)
  + [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)
  + [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md)
  + [Microsoft.Skills.Text.MergeSkill](cognitive-search-skill-textmerger.md)
  + [Microsoft.Skills.Text.PIIDetectionSkill](cognitive-search-skill-pii-detection.md)
  + [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md)
  + [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)
  + [Microsoft.Skills.Text.TranslationSkill](cognitive-search-skill-text-translation.md)
  + [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md)
  + [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md)
  + [Microsoft.Skills.Util.ConditionalSkill](cognitive-search-skill-conditional.md)
  + [Microsoft.Skills.Util.DocumentExtractionSkill](cognitive-search-skill-document-extraction.md)
  + [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md)

+ Habilidades personalizadas
  + [Microsoft.Skills.Custom.WebApiSkill](cognitive-search-custom-skill-web-api.md)

+ [Habilidades preteridas](cognitive-search-skill-deprecated.md)
  + [Microsoft.Skills.Text.NamedEntityRecognitionSkill](cognitive-search-skill-named-entity-recognition.md)

+ [REST API](https://docs.microsoft.com/rest/api/searchservice/)
  + [Criar Conjunto de Qualificações (api-version=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
  + [Criar Indexador (api-version=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

## <a name="see-also"></a>Confira também

+ [API REST da Pesquisa Cognitiva do Azure](https://docs.microsoft.com/rest/api/searchservice/)
+ [Indexadores na Pesquisa Cognitiva do Azure](search-indexer-overview.md)
+ [O que é a Pesquisa Cognitiva do Azure?](search-what-is-azure-search.md)
