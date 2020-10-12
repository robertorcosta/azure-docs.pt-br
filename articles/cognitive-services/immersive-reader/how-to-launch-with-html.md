---
title: Iniciar a Leitura Avançada com conteúdo de HTML
titleSuffix: Azure Cognitive Services
description: Este artigo mostrará como iniciar o leitor de imersão com conteúdo HTML.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: metan
ms.custom: devx-track-js
ms.openlocfilehash: f7f55d1d349fc7d02c2ac53f7a50f9d649688cbd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91326755"
---
# <a name="how-to-launch-the-immersive-reader-with-html-content"></a>Como iniciar o leitor de imersão com conteúdo HTML

Este artigo demonstra como iniciar o leitor de imersão com conteúdo HTML.

## <a name="prepare-the-html-content"></a>Preparar o conteúdo HTML

Coloque o conteúdo que você deseja renderizar no leitor de imersão dentro de um elemento de contêiner. Certifique-se de que o elemento contêiner tenha um exclusivo `id` . O leitor de imersão fornece suporte para elementos HTML básicos, consulte a [referência](./reference.md#html-support) para obter mais informações.

```html
<div id='immersive-reader-content'>
    <b>Bold</b>
    <i>Italic</i>
    <u>Underline</u>
    <strike>Strikethrough</strike>
    <code>Code</code>
    <sup>Superscript</sup>
    <sub>Subscript</sub>
    <ul><li>Unordered lists</li></ul>
    <ol><li>Ordered lists</li></ol>
</div>
```

## <a name="get-the-html-content-in-javascript"></a>Obter o conteúdo HTML em JavaScript

Use o `id` do elemento contêiner para obter o conteúdo HTML em seu código JavaScript.

```javascript
const htmlContent = document.getElementById('immersive-reader-content').innerHTML;
```

## <a name="launch-the-immersive-reader-with-your-html-content"></a>Inicie o leitor de imersão com seu conteúdo HTML

Ao chamar `ImmersiveReader.launchAsync` , defina a propriedade da parte `mimeType` como `text/html` para habilitar o processamento de HTML.

```javascript
const data = {
    chunks: [{
        content: htmlContent,
        mimeType: 'text/html'
    }]
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, data, YOUR_OPTIONS);
```

## <a name="next-steps"></a>Próximas etapas

* Explorar a [Referência do SDK da Leitura Avançada](./reference.md)