---
title: Suporte de idioma – LUIS
titleSuffix: Azure Cognitive Services
description: LUIS tem uma variedade de recursos dentro do serviço. Nem todos os recursos estão na mesma paridade de idioma. Verifique se os recursos em que você está interessado têm suporte na cultura do idioma de destino. Um aplicativo LUIS é específico à cultura e não pode ser alterado após a definição.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: diberry
ms.openlocfilehash: 82efa70b30e829cfedd0b1fa7a21fd06949aa6d5
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80744151"
---
# <a name="language-and-region-support-for-luis"></a>Suporte de idioma e região para o LUIS

LUIS tem uma variedade de recursos dentro do serviço. Nem todos os recursos estão na mesma paridade de idioma. Verifique se os recursos em que você está interessado têm suporte na cultura do idioma de destino. Um aplicativo LUIS é específico à cultura e não pode ser alterado após a definição.

## <a name="multi-language-luis-apps"></a>Aplicativos LUIS com vários idiomas

Se você precisar de um aplicativo de cliente LUIS com vários idiomas, como um chatbot, terá algumas opções. Se o LUIS der suporte a todos os idiomas, desenvolva um aplicativo LUIS para cada idioma. Cada aplicativo LUIS tem uma ID de aplicativo exclusiva e um log de ponto de extremidade. Se você precisa fornecer o reconhecimento de idioma para um idioma ao qual o LUIS não oferece suporte, use a [API de Tradução da Microsoft](../Translator/translator-info-overview.md) para traduzir a declaração para um idioma com suporte, envie a declaração para o ponto de extremidade do LUIS e receba as pontuações resultantes.

## <a name="languages-supported"></a>Idiomas compatíveis

O LUIS compreende declarações nos seguintes idiomas:

| Linguagem |Local  |  Domínio predefinido | Entidade predefinida | Recomendações da lista de frases | **[Análise de texto](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)<br>(Sentimento e<br>Palavras-chave)|
|--|--|:--:|:--:|:--:|:--:|
| Inglês americano |`en-US` | ✔ | ✔  |✔|✔|
| Árabe (visualização - árabe padrão moderno) |`ar-AR`|-|-|-|-|
| *[Chinês](#chinese-support-notes) |`zh-CN` | ✔ | ✔ |✔|-|
| Holandês |`nl-NL` |✔|-|-|✔|
| Francês (França) |`fr-FR` |✔| ✔ |✔ |✔|
| Francês (Canadá) |`fr-CA` |-|-|-|✔|
| Alemão |`de-DE` |✔| ✔ |✔ |✔|
| Guzerate | `gu-IN`|-|-|-|-|
| Híndi | `hi-IN`|-|✔|-|-|
| Italiano |`it-IT` |✔| ✔ |✔|✔|
| *[Japonês](#japanese-support-notes) |`ja-JP` |✔| ✔ |✔|Somente frase principal|
| Coreano |`ko-KR` |✔|-|-|Somente frase principal|
| Marati | `mr-IN`|-|-|-|-|
| Português (Brasil) |`pt-BR` |✔| ✔ |✔ |nem todas as subculturas|
| Espanhol (Espanha) |`es-ES` |✔| ✔ |✔|✔|
| Espanhol (México)|`es-MX` |-|-|✔|✔|
| Tâmil | `ta-IN`|-|-|-|-|
| Télugo | `te-IN`|-|-|-|-|
| Turco | `tr-TR` |✔|✔|-|Sentimento, somente|




O suporte aos idiomas varia para [entidades predefinidas](luis-reference-prebuilt-entities.md) e [domínios predefinidos](luis-reference-prebuilt-domains.md).

[!INCLUDE [Chinese language support notes](includes/chinese-language-support-notes.md)]

### <a name="japanese-support-notes"></a>*Notas de suporte ao japonês

 - Como o LUIS não fornece análise sintática e não compreenderá a diferença entre Keigo e japonês informal, será necessário incorporar os diferentes níveis de formalidade como exemplos de treinamento para os seus aplicativos.
     - でございます não é o mesmo que です.
     - です não é o mesmo que だ.

[!INCLUDE [Text Analytics support notes](includes/text-analytics-support-notes.md)]

### <a name="speech-api-supported-languages"></a>Idiomas com suporte da API de Fala
Veja [Idiomas com suporte](../speech-service/speech-to-text.md) de Fala para idiomas no modo de ditado de Fala.

### <a name="bing-spell-check-supported-languages"></a>Idiomas com suporte para Verificação Ortográfica do Bing
Veja [idiomas com suporte](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/bing-spell-check-supported-languages) da Verificação Ortográfica do Bing para obter uma lista dos idiomas com suporte e status.

## <a name="rare-or-foreign-words-in-an-application"></a>Palavras raras ou estrangeiras em um aplicativo
Na cultura`en-us`, o LUIS aprende a distinguir mais palavras em inglês, incluindo gírias. Na cultura `zh-cn`, o LUIS aprende distinguir a maioria dos caracteres chineses. Se você usar uma palavra rara em `en-us` ou caractere em `zh-cn`, e você verá que o LUIS não parece capaz de distinguir essa palavra ou caractere, você pode adicionar essa palavra ou caractere a um [recurso de lista de frases](luis-how-to-add-features.md). Por exemplo, as palavras fora da cultura do aplicativo, ou seja, as palavras estrangeiras, devem ser adicionadas a um recurso de lista de frases.

<!--This phrase list should be marked non-interchangeable, to indicate that the set of rare words forms a class that LUIS should learn to recognize, but they are not synonyms or interchangeable with each other.-->

### <a name="hybrid-languages"></a>Idiomas híbridos
Os idiomas híbridos combinam palavras de duas culturas, como inglês e chinês. Não há suporte para esses idiomas no LUIS, pois um aplicativo tem base em uma única cultura.

## <a name="tokenization"></a>Geração de tokens
Para executar o aprendizado de máquina, o LUIS divide uma declaração em [tokens](luis-glossary.md#token) com base na cultura.

|Linguagem|  cada espaço ou caractere especial | nível do caractere|palavras compostas
|--|:--:|:--:|:--:|
|Árabe|✔|||
|Chinês||✔||
|Holandês|✔||✔|
|Inglês (en-us)|✔ |||
|Francês (fr-FR)|✔|||
|Francês (fr-CA)|✔|||
|Alemão|✔||✔|
|Guzerate|✔|||
|Híndi|✔|||
|Italiano|✔|||
|Japonês|||✔
|Coreano||✔||
|Marati|✔|||
|Português (Brasil)|✔|||
|Espanhol (es-ES)|✔|||
|Espanhol (es-MX)|✔|||
|Tâmil|✔|||
|Télugo|✔|||
|Turco|✔|||


### <a name="custom-tokenizer-versions"></a>Versões de tokenizador personalizado

As culturas a seguir têm versões de tokenizer personalizadas:

|Cultura|Versão|Finalidade|
|--|--|--|
|Alemão<br>`de-de`|1.0.0|Tokeniza palavras dividindo-as usando um tokenizador baseado em aprendizado de máquina que tenta dividir palavras compostas em seus componentes únicos.<br>Se um `Ich fahre einen krankenwagen` usuário entra como um enunciado, ele é voltado para `Ich fahre einen kranken wagen`. Permitindo a `kranken` marcação e `wagen` independentemente como entidades diferentes.|
|Alemão<br>`de-de`|1.0.2|Tokeniza palavras dividindo-as em espaços.<br> Se um `Ich fahre einen krankenwagen` usuário entra como um enunciado, ele permanece um único token. Assim `krankenwagen` é marcada como uma única entidade. |
|Holandês<br>`de-de`|1.0.0|Tokeniza palavras dividindo-as usando um tokenizador baseado em aprendizado de máquina que tenta dividir palavras compostas em seus componentes únicos.<br>Se um `Ik ga naar de kleuterschool` usuário entra como um enunciado, ele é voltado para `Ik ga naar de kleuter school`. Permitindo a `kleuter` marcação e `school` independentemente como entidades diferentes.|
|Holandês<br>`de-de`|1.0.1|Tokeniza palavras dividindo-as em espaços.<br> Se um `Ik ga naar de kleuterschool` usuário entra como um enunciado, ele permanece um único token. Assim `kleuterschool` é marcada como uma única entidade. |


### <a name="migrating-between-tokenizer-versions"></a>Migração entre versões de tokenizer
<!--
Your first choice is to change the tokenizer version in the app file, then import the version. This action changes how the utterances are tokenized but allows you to keep the same app ID.

Tokenizer JSON for 1.0.0. Notice the property value for  `tokenizerVersion`.

```JSON
{
    "luis_schema_version": "3.2.0",
    "versionId": "0.1",
    "name": "german_app_1.0.0",
    "desc": "",
    "culture": "de-de",
    "tokenizerVersion": "1.0.0",
    "intents": [
        {
            "name": "i1"
        },
        {
            "name": "None"
        }
    ],
    "entities": [
        {
            "name": "Fahrzeug",
            "roles": []
        }
    ],
    "composites": [],
    "closedLists": [],
    "patternAnyEntities": [],
    "regex_entities": [],
    "prebuiltEntities": [],
    "model_features": [],
    "regex_features": [],
    "patterns": [],
    "utterances": [
        {
            "text": "ich fahre einen krankenwagen",
            "intent": "i1",
            "entities": [
                {
                    "entity": "Fahrzeug",
                    "startPos": 23,
                    "endPos": 27
                }
            ]
        }
    ],
    "settings": []
}
```

Tokenizer JSON for version 1.0.1. Notice the property value for  `tokenizerVersion`.

```JSON
{
    "luis_schema_version": "3.2.0",
    "versionId": "0.1",
    "name": "german_app_1.0.1",
    "desc": "",
    "culture": "de-de",
    "tokenizerVersion": "1.0.1",
    "intents": [
        {
            "name": "i1"
        },
        {
            "name": "None"
        }
    ],
    "entities": [
        {
            "name": "Fahrzeug",
            "roles": []
        }
    ],
    "composites": [],
    "closedLists": [],
    "patternAnyEntities": [],
    "regex_entities": [],
    "prebuiltEntities": [],
    "model_features": [],
    "regex_features": [],
    "patterns": [],
    "utterances": [
        {
            "text": "ich fahre einen krankenwagen",
            "intent": "i1",
            "entities": [
                {
                    "entity": "Fahrzeug",
                    "startPos": 16,
                    "endPos": 27
                }
            ]
        }
    ],
    "settings": []
}
```
-->

A tokenização acontece no nível do aplicativo. Não há suporte para tokenização em nível de versão.

[Importe o arquivo como um novo aplicativo,](luis-how-to-start-new-app.md)em vez de uma versão. Esta ação significa que o novo aplicativo tem uma ID de aplicativo diferente, mas usa a versão tokenizer especificada no arquivo.
