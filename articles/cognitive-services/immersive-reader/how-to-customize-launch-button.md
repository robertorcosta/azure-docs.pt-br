---
title: Personalize o botão Leitor Imersivo
titleSuffix: Azure Cognitive Services
description: Este artigo mostrará como personalizar o botão que lança o Leitor Imersivo.
services: cognitive-services
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: metan
ms.openlocfilehash: 6461cc60a237cf834226b07db5e1ea6fbbea7a92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "75946205"
---
# <a name="how-to-customize-the-immersive-reader-button"></a>Como personalizar o botão Leitor Imersivo

Este artigo demonstra como personalizar o botão que lança o Leitor Imersivo para atender às necessidades do seu aplicativo.

## <a name="add-the-immersive-reader-button"></a>Adicione o botão Leitor Imersivo

O Immersive Reader SDK fornece estilo padrão para o botão que lança o Leitor Imersivo. Use `immersive-reader-button` o atributo de classe para habilitar esse estilo.

```html
<div class='immersive-reader-button'></div>
```

## <a name="customize-the-button-style"></a>Personalize o estilo do botão

Use `data-button-style` o atributo para definir o estilo do botão. Os valores `icon` `text`permitidos `iconAndText`são, e . O valor padrão é `icon`.

### <a name="icon-button"></a>Botão de ícone

```html
<div class='immersive-reader-button' data-button-style='icon'></div>
```

Isso torna o seguinte:

![Botão de ícone](./media/button-icon.png)

### <a name="text-button"></a>Botão de texto

```html
<div class='immersive-reader-button' data-button-style='text'></div>
```

Isso torna o seguinte:

![Botão de ícone](./media/button-text.png)

### <a name="icon-and-text-button"></a>Ícone e botão de texto

```html
<div class='immersive-reader-button' data-button-style='iconAndText'></div>
```

Isso torna o seguinte:

![Botão de ícone](./media/button-icon-and-text.png)

## <a name="customize-the-button-text"></a>Personalize o texto do botão

Configure o idioma e o texto `data-locale` alt para o botão usando o atributo. O idioma padrão é inglês.

```html
<div class='immersive-reader-button' data-locale='fr-FR'></div>
```

## <a name="customize-the-size-of-the-icon"></a>Personalize o tamanho do ícone

O tamanho do ícone Leitor Imersivo `data-icon-px-size` pode ser configurado usando o atributo. Isso define o tamanho do ícone em pixels. O tamanho padrão é 20px.

```html
<div class='immersive-reader-button' data-icon-px-size='50'></div>
```

## <a name="next-steps"></a>Próximas etapas

* Explorar a [Referência do SDK da Leitura Avançada](./reference.md)