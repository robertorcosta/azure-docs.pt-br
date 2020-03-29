---
title: Configurações do aplicativo - LUIS
titleSuffix: Azure Cognitive Services
description: As configurações de aplicativos para aplicativos de compreensão de linguagem do Azure Cognitive Services são armazenadas no aplicativo e no portal.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 11/12/2019
ms.author: diberry
ms.openlocfilehash: d1ead09f6248a6ad14646371aa70b42b57cf8e3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270818"
---
# <a name="application-settings"></a>Configurações do aplicativo

Essas configurações do aplicativo são armazenadas no aplicativo [exportado](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) e [atualizadas](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) com as APIs REST. Alterar as configurações da versão do aplicativo redefine o status de treinamento do aplicativo para destreinado.

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
