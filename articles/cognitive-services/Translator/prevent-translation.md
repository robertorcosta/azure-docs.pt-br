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
ms.date: 11/18/2019
ms.author: swmachan
ms.openlocfilehash: dd3684cbd7c03851bfcc75293a9690f77b4652b2
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184811"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator-text-api"></a>Como evitar a tradução de conteúdo com a API de Tradução de Texto

A API de Tradução de Texto permite que marcar conteúdo para que não seja traduzido. Por exemplo, você talvez queira marcar o código, um nome de marca ou uma palavra/frase que não precisa ser traduzida.

## <a name="methods-for-preventing-translation"></a>Métodos para evitar a tradução
1. Usar escape para uma tag do Twitter @somethingtopassthrough ou #somethingtopassthrough. Não usar escape após a tradução. Esta é a expressão regular para as marcas válidas do Twitter: `\B@[A-Za-z]+[A-Za-z0-9_]+)`. Uma marca deve começar com um sinal de "@", seguido por um caractere e seguida por um ou vários caracteres, dígitos ou sublinhado. É recomendável manter as marcas curtas e a marca de abertura deve ser precedida por um espaço.

2. Marcar seu conteúdo com `notranslate`.

   Exemplo:

   ```html
   <div class="notranslate">This will not be translated.</div>
   <div>This will be translated. </div>
   ```

3. use o [dicionário dinâmico](dynamic-dictionary.md) para prescrever uma tradução específica.

4. Não passe a cadeia de caracteres para a API de Tradução de Texto para tradução.

5. Tradutor personalizado: Use um [dicionário no Tradutor personalizado](custom-translator/what-is-dictionary.md) para prescrever a tradução de uma frase com uma probabilidade de 100%.


## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Evitar a tradução em sua chamada à API do Tradutor](reference/v3-0-translate.md)
