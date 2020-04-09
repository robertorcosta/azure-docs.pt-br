---
title: Limitações de contêineres - LUIS
titleSuffix: Azure Cognitive Services
description: As línguas de contêiner LUIS que são suportadas.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 7fe773b35c5aba31b2fea66bd2be7b2745eac3ee
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879234"
---
# <a name="language-understanding-luis-container-limitations"></a>Limitações de contêiner de compreensão de linguagem (LUIS)

Os contêineres LUIS têm algumas limitações notáveis. De dependências não suportadas, a um subconjunto de idiomas suportados, este artigo detalha essas restrições.

## <a name="supported-dependencies-for-latest-container"></a>Dependências suportadas `latest` para contêiner

O mais recente contêiner LUIS, lançado em [//build/ 2019,](https://news.microsoft.com/build2019/)suportará:

* [Novos domínios pré-construídos](luis-reference-prebuilt-domains.md): esses domínios focados em empresas incluem entidades, enunciados de exemplo e padrões. Amplie esses domínios para seu próprio uso.

## <a name="unsupported-dependencies-for-latest-container"></a>Dependências não suportadas para `latest` contêiner

Para [exportar para contêiner,](luis-container-howto.md#export-packaged-app-from-luis)você deve remover dependências não suportadas do seu aplicativo LUIS. Quando você tenta exportar para o contêiner, o portal LUIS relata esses recursos sem suporte que você precisa remover.

Você pode usar um aplicativo LUIS se ele **não inclui** nenhuma das seguintes dependências:

Configurações de aplicativo sem suporte|Detalhes|
|--|--|
|Culturas de contêiner sem suporte| Holandês`nl-NL`( )<br>Japonês`ja-JP`( )<br>O alemão só é suportado com o [tokenizador 1.0.2](luis-language-support.md#custom-tokenizer-versions).|
|Entidades sem suporte para todas as culturas|Entidade predefinida [KeyPhrase](luis-reference-prebuilt-keyphrase.md) para todas as culturas|
|Entidades sem suporte`en-US`para a cultura inglesa ( )|Entidades predefinidas [GeographyV2](luis-reference-prebuilt-geographyV2.md)|
|Preparação da fala|Não há suporte para dependências externas no contêiner.|
|Análise de sentimento|Não há suporte para dependências externas no contêiner.|
|Verificação Ortográfica do Bing|Não há suporte para dependências externas no contêiner.|

## <a name="languages-supported"></a>Idiomas compatíveis

Os contêineres LUIS suportam um subconjunto das [línguas suportadas](luis-language-support.md#languages-supported) pelo próprio LUIS. Os contêineres LUIS são capazes de compreender expressões nas seguintes línguas:

| Linguagem | Local | Domínio predefinido | Entidade predefinida | Recomendações da lista de frases | **[Análise de texto](../text-analytics/language-support.md)<br>(Sentimento e<br>Palavras-chave)|
|--|--|:--:|:--:|:--:|:--:|
| Inglês americano | `en-US` | ✔️ | ✔️ | ✔️ | ✔️ |
| *[Chinês](#chinese-support-notes) |`zh-CN` | ✔️ | ✔️ | ✔️ | ❌ |
| Francês (França) |`fr-FR` | ✔️ | ✔️ | ✔️ | ✔️ |
| Francês (Canadá) |`fr-CA` | ❌ | ❌ | ❌ | ✔️ |
| Alemão |`de-DE` | ✔️ | ✔️ | ✔️ | ✔️ |
| Híndi | `hi-IN`| ❌ | ❌ | ❌ | ❌ |
| Italiano |`it-IT` | ✔️ | ✔️ | ✔️ | ✔️ |
| Coreano |`ko-KR` | ✔️ | ❌ | ❌ | *Apenas frase-chave* |
| Português (Brasil) |`pt-BR` | ✔️ | ✔️ | ✔️ | nem todas as subculturas |
| Espanhol (Espanha) |`es-ES` | ✔️ | ✔️ |✔️|✔️|
| Espanhol (México)|`es-MX` | ❌ | ❌ |✔️|✔️|
| Turco | `tr-TR` |✔️| ❌ | ❌ | *Apenas sentimento* |

[!INCLUDE [Chinese language support notes](includes/chinese-language-support-notes.md)]

[!INCLUDE [Text Analytics support notes](includes/text-analytics-support-notes.md)]