---
title: Configurações do aplicativo - LUIS
description: As configurações de aplicativos para aplicativos de compreensão de linguagem do Azure Cognitive Services são armazenadas no aplicativo e no portal.
ms.topic: reference
ms.date: 04/14/2020
ms.openlocfilehash: 9e17736cd6ff5074a6eab76a6cf5bdb8acedc185
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382195"
---
# <a name="application-settings"></a>Configurações do aplicativo

Essas configurações do aplicativo são armazenadas no aplicativo [exportado](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) e [atualizadas](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) com as APIs REST. Alterar as configurações da versão do aplicativo redefine o status de treinamento do aplicativo para destreinado.

Aprenda [conceitos](luis-concept-utterance.md#utterance-normalization-for-diacritics-and-punctuation) de diacríticos e pontuação.

|Configuração|Valor padrão|Observações|
|--|--|--|
|Normalizar Punctuation|True|Remove a pontuação.|
|NormalizeDiacritics|True|Remove diacríticos.|

## <a name="diacritics-normalization"></a>Normalização diacrítica

Ative a normalização do enunciado para diacríticos para o seu arquivo do aplicativo LUIS JSON no `settings` parâmetro.

```JSON
"settings": [
    {"name": "NormalizeDiacritics", "value": "true"}
]
```

As seguintes declarações mostram como a normalização dos diacritics impacta as expressões:

|Com diacríticos definidos para falso|Com diacríticos definidos para verdade|
|--|--|
|`quiero tomar una piña colada`|`quiero tomar una pina colada`|
|||

### <a name="language-support-for-diacritics"></a>Suporte de idiomas para diacríticos

#### <a name="brazilian-portuguese-pt-br-diacritics"></a>Diacritics `pt-br` português brasileiro

|Diacritics definido para falso|Diacritics definido para verdade|
|-|-|
|`á`|`a`|
|`â`|`a`|
|`ã`|`a`|
|`à`|`a`|
|`ç`|`c`|
|`é`|`e`|
|`ê`|`e`|
|`í`|`i`|
|`ó`|`o`|
|`ô`|`o`|
|`õ`|`o`|
|`ú`|`u`|
|||

#### <a name="dutch-nl-nl-diacritics"></a>Diacritics holandeses `nl-nl`

|Diacritics definido para falso|Diacritics definido para verdade|
|-|-|
|`á`|`a`|
|`à`|`a`|
|`é`|`e`|
|`ë`|`e`|
|`è`|`e`|
|`ï`|`i`|
|`í`|`i`|
|`ó`|`o`|
|`ö`|`o`|
|`ú`|`u`|
|`ü`|`u`|
|||

#### <a name="french-fr--diacritics"></a>Diacríticos franceses `fr-`

Isso inclui subculturas francesas e canadenses.

|Diacritics definido para falso|Diacritics definido para verdade|
|--|--|
|`é`|`e`|
|`à`|`a`|
|`è`|`e`|
|`ù`|`u`|
|`â`|`a`|
|`ê`|`e`|
|`î`|`i`|
|`ô`|`o`|
|`û`|`u`|
|`ç`|`c`|
|`ë`|`e`|
|`ï`|`i`|
|`ü`|`u`|
|`ÿ`|`y`|

#### <a name="german-de-de-diacritics"></a>Diacríticos alemães `de-de`

|Diacritics definido para falso|Diacritics definido para verdade|
|--|--|
|`ä`|`a`|
|`ö`|`o`|
|`ü`|`u`|

#### <a name="italian-it-it-diacritics"></a>Diacritics italiano `it-it`

|Diacritics definido para falso|Diacritics definido para verdade|
|--|--|
|`à`|`a`|
|`è`|`e`|
|`é`|`e`|
|`ì`|`i`|
|`í`|`i`|
|`î`|`i`|
|`ò`|`o`|
|`ó`|`o`|
|`ù`|`u`|
|`ú`|`u`|

#### <a name="spanish-es--diacritics"></a>Diacritics espanhol `es-`

Isso inclui tanto espanhol quanto canadense mexicano.

|Diacritics definido para falso|Diacritics definido para verdade|
|-|-|
|`á`|`a`|
|`é`|`e`|
|`í`|`i`|
|`ó`|`o`|
|`ú`|`u`|
|`ü`|`u`|
|`ñ`|`u`|


## <a name="punctuation-normalization"></a>Normalização da pontuação

Ative a normalização do enunciado para pontuação no `settings` seu arquivo do aplicativo LUIS JSON no parâmetro.

```JSON
"settings": [
    {"name": "NormalizePunctuation", "value": "true"}
]
```

As seguintes expressões mostram como a pontuação impacta as expressões:

|Com pontuação definida como Falsa|Com pontuação definida para True|
|--|--|
|`Hmm..... I will take the cappuccino`|`Hmm I will take the cappuccino`|
|||

### <a name="punctuation-removed"></a>Pontuação removida

A pontuação a seguir é `NormalizePunctuation` removida com a verdade.

|Pontuação|
|--|
|`-`|
|`.`|
|`'`|
|`"`|
|`\`|
|`/`|
|`?`|
|`!`|
|`_`|
|`,`|
|`;`|
|`:`|
|`(`|
|`)`|
|`[`|
|`]`|
|`{`|
|`}`|
|`+`|
|`¡`|

## <a name="next-steps"></a>Próximas etapas

* Aprenda [conceitos](luis-concept-utterance.md#utterance-normalization-for-diacritics-and-punctuation) de diacríticos e pontuação.
