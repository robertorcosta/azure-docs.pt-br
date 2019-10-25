---
title: Extração de dados interna, linguagem natural, processamento de imagens
titleSuffix: Azure Cognitive Search
description: Extração de dados, linguagem natural, habilidades cognitivas de processamento de imagens adicionam semântica e estrutura ao conteúdo bruto em um pipeline de Pesquisa Cognitiva do Azure.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 435635018dc25ed2af0aec3d542c0388af8ab885
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792093"
---
# <a name="built-in-cognitive-skills-for-content-enrichment-azure-cognitive-search"></a>Habilidades cognitivas internas para o enriquecimento de conteúdo (Pesquisa Cognitiva do Azure)

Neste artigo, você aprenderá sobre as habilidades cognitivas fornecidas com o Azure Pesquisa Cognitiva. Uma *habilidade cognitiva* é uma operação que transforma o conteúdo de alguma forma. Geralmente, é um componente que extrai ou infere a estrutura e, portanto, aumenta o seu entendimento sobre os dados de entrada. Quase sempre, a saída é baseada em texto. Um *conjunto de qualificações* é o conjunto de habilidades que define o enriquecimento do pipeline. 

> [!NOTE]
> À medida que expandir o escopo aumentando a frequência de processamento, adicionando mais documentos ou adicionando mais algoritmos de IA, você precisará [anexar um recurso de Serviços Cognitivos faturável](cognitive-search-attach-cognitive-services.md). As cobranças são acumuladas ao chamar APIs em serviços cognitivas e para extração de imagem como parte do estágio de quebra de documento no Azure Pesquisa Cognitiva. Não há encargos para extração de texto em documentos.
>
> A execução de habilidades integradas é cobrada nos [preços pagos conforme o uso dos Serviços Cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services/) existentes. O preço de extração de imagem é descrito na [página de preços do Azure pesquisa cognitiva](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="built-in-skills"></a>Habilidades internas

Diversas habilidades são flexíveis na forma que consomem ou produzem. Em geral, a maioria das habilidades é baseada em modelos previamente treinados, o que significa que você não pode treinar o modelo usando seus próprios dados de treinamento. A tabela a seguir enumera e descreve as habilidades fornecidas pela Microsoft. 

| Habilidade | Descrição |
|-------|-------------|
| [Microsoft.Skills.Text.KeyPhraseSkill](cognitive-search-skill-keyphrases.md) | Essa habilidade usa um modelo pré-treinado para detectar frases importantes com base no posicionamento de termos, regras linguísticas, proximidade com outros termos e o quanto o termo é incomum nos dados de origem. |
| [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)  | Essa habilidade usa um modelo pré-treinado para detectar o idioma usado (uma ID de idioma por documento). Quando vários idiomas são usados dentro do mesmo segmentos de texto, a saída é o LCID do idioma predominantemente usado.|
| [Microsoft.Skills.Text.MergeSkill](cognitive-search-skill-textmerger.md) | Consolida o texto de uma coleção de campos em um único campo.  |
| [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md) | Essa habilidade usa um modelo pré-treinado para estabelecer entidades para um conjunto fixo de categorias: pessoas, local, organização, emails, URLs, campos datetime. |
| [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)  | Essa habilidade usa um modelo pré-treinado para classificar um sentimento positivo ou negativo em um registro por uma base de registro. O valor está entre  0 e 1. Pontuações neutras ocorrem para caso nulo quando o sentimento não puder ser detectado, e para o texto que é considerado neutro.  |
| [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md) | Divide o texto em páginas de forma que você possa enriquecer ou aumentar o conteúdo incrementalmente. |
| [Microsoft. Skills. Text. TranslationSkill](cognitive-search-skill-text-translation.md) | Essa habilidade usa um modelo pretreinado para converter o texto de entrada em uma variedade de idiomas para casos de uso de normalização ou localização. |
| [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md) | Essa habilidade usa um algoritmo de detecção de imagem para identificar o conteúdo de uma imagem e gerar uma descrição de texto. |
| [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md) | Reconhecimento de caractere óptico. |
| [Microsoft. Skills. util. ConditionalSkill](cognitive-search-skill-conditional.md) | Permite filtrar, atribuir um valor padrão e mesclar dados com base em uma condição.|
| [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md) | Saída de mapas para um tipo complexo (um tipo de dados de multi-parte que deve ser usado para um nome completo, um endereço de várias linhas ou uma combinação do sobrenome e um identificador pessoal) |
| [Microsoft.Skills.Custom.WebApiSkill](cognitive-search-custom-skill-web-api.md) | Permite a extensibilidade de um pipeline de enriquecimento de ia fazendo uma chamada HTTP para uma API Web personalizada |


Para obter orientação sobre como criar uma [habilidade personalizada](cognitive-search-custom-skill-web-api.md), consulte [como definir uma interface personalizada](cognitive-search-custom-skill-interface.md) e um [exemplo: criando uma habilidade personalizada para enriquecimento de ia](cognitive-search-create-custom-skill-example.md).

## <a name="see-also"></a>Consulte

+ [Como definir um conjunto de qualificações](cognitive-search-defining-skillset.md)
+ [Definição da interface de habilidades personalizadas](cognitive-search-custom-skill-interface.md)
+ [Tutorial: indexação aprimorada com o ia](cognitive-search-tutorial-blob.md)
