---
title: Evitar a tradução de conteúdo – API de Tradução de Texto
titleSuffix: Azure Cognitive Services
description: Evitar a tradução de conteúdo com a API de Tradução de Texto. A API de Tradução de Texto permite que marcar conteúdo para que não seja traduzido.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: swmachan
ms.openlocfilehash: 15a36451c18d65df6667f24284f3f69f3d1c06b8
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326770"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator-text-api"></a>Como evitar a tradução de conteúdo com a API de Tradução de Texto

A API de Tradução de Texto permite que marcar conteúdo para que não seja traduzido. Por exemplo, você talvez queira marcar o código, um nome de marca ou uma palavra/frase que não precisa ser traduzida.

## <a name="methods-for-preventing-translation"></a>Métodos para evitar a tradução
1. Usar escape para uma tag do Twitter @somethingtopassthrough ou #somethingtopassthrough. Não usar escape após a tradução. This is the regular expression for valid twitter tags: `\B@[A-Za-z]+[A-Za-z0-9_]+)`. A tag should start with a "@" sign, followed by a character and then followed by one or many characters, digits or underscore. It is recommended to keep tags short and the opening tag must be preceded by a space.

2. Marcar seu conteúdo com `notranslate`. It's by design that this works only when the input textType is set as HTML

   Exemplo:

   ```html
   <span class="notranslate">This will not be translated.</span>
   <span>This will be translated. </span>
   ```
   
   ```html
   <div class="notranslate">This will not be translated.</div>
   <div>This will be translated. </div>
   ```

3. use o [dicionário dinâmico](dynamic-dictionary.md) para prescrever uma tradução específica.

4. Não passe a cadeia de caracteres para a API de Tradução de Texto para tradução.

5. Custom Translator: Use a [dictionary in Custom Translator](custom-translator/what-is-dictionary.md) to prescribe the translation of a phrase with 100% probability.


## <a name="next-steps"></a>Próximos passos
> [!div class="nextstepaction"]
> [Evitar a tradução em sua chamada à API do Tradutor](reference/v3-0-translate.md)
