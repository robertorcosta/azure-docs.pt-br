---
title: Configurações do aplicativo-LUIS
description: As configurações de aplicativos para aplicativos de reconhecimento de linguagem de serviços cognitivas do Azure são armazenadas no aplicativo e no Portal.
ms.topic: reference
ms.date: 04/14/2020
ms.openlocfilehash: 9e17736cd6ff5074a6eab76a6cf5bdb8acedc185
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "81382195"
---
# <a name="application-settings"></a>Configurações do aplicativo

Essas configurações de aplicativo são armazenadas no aplicativo [exportado](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) e [atualizadas](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) com as APIs REST. Alterar as configurações de versão do aplicativo redefine o status de treinamento do aplicativo como não treinado.

Aprenda [conceitos](luis-concept-utterance.md#utterance-normalization-for-diacritics-and-punctuation) de sinais diacríticos e pontuação.

|Configuração|Valor padrão|Observações|
|--|--|--|
|NormalizePunctuation|verdadeiro|Remove a pontuação.|
|NormalizeDiacritics|verdadeiro|Remove diacríticos.|

## <a name="diacritics-normalization"></a>Normalização de diacríticos

Ative a `settings` normalização de expressão para diacríticos para o arquivo de aplicativo JSON Luis no parâmetro.

```JSON
"settings": [
    {"name": "NormalizeDiacritics", "value": "true"}
]
```

O declarações a seguir mostra como a normalização de diacríticos afeta o declarações:

|Com sinais diacríticos definidos como false|Com sinais diacríticos definidos como true|
|--|--|
|`quiero tomar una piña colada`|`quiero tomar una pina colada`|
|||

### <a name="language-support-for-diacritics"></a>Suporte a idiomas para sinais diacríticos

#### <a name="brazilian-portuguese-pt-br-diacritics"></a>Diacríticos do `pt-br` Português do Brasil

|Sinais diacríticos definidos como false|Sinais diacríticos definidos como true|
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

#### <a name="dutch-nl-nl-diacritics"></a>Diacríticos `nl-nl` holandeses

|Sinais diacríticos definidos como false|Sinais diacríticos definidos como true|
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

#### <a name="french-fr--diacritics"></a>Diacríticos `fr-` franceses

Isso inclui as subculturas francesa e canadense.

|Sinais diacríticos definidos como false|Sinais diacríticos definidos como true|
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

#### <a name="german-de-de-diacritics"></a>Diacríticos `de-de` em alemão

|Sinais diacríticos definidos como false|Sinais diacríticos definidos como true|
|--|--|
|`ä`|`a`|
|`ö`|`o`|
|`ü`|`u`|

#### <a name="italian-it-it-diacritics"></a>Diacríticos `it-it` em Italiano

|Sinais diacríticos definidos como false|Sinais diacríticos definidos como true|
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

#### <a name="spanish-es--diacritics"></a>Diacríticos `es-` em espanhol

Isso inclui espanhol e Canadá mexicano.

|Sinais diacríticos definidos como false|Sinais diacríticos definidos como true|
|-|-|
|`á`|`a`|
|`é`|`e`|
|`í`|`i`|
|`ó`|`o`|
|`ú`|`u`|
|`ü`|`u`|
|`ñ`|`u`|


## <a name="punctuation-normalization"></a>Normalização de Pontuação

Ative a `settings` normalização de expressão para pontuação em seu arquivo de aplicativo JSON Luis no parâmetro.

```JSON
"settings": [
    {"name": "NormalizePunctuation", "value": "true"}
]
```

O declarações a seguir mostra como a pontuação afeta declarações:

|Com pontuação definida como false|Com pontuação definida como true|
|--|--|
|`Hmm..... I will take the cappuccino`|`Hmm I will take the cappuccino`|
|||

### <a name="punctuation-removed"></a>Pontuação removida

A pontuação a seguir é removida com `NormalizePunctuation` é definido como true.

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

* Aprenda [conceitos](luis-concept-utterance.md#utterance-normalization-for-diacritics-and-punctuation) de sinais diacríticos e pontuação.
