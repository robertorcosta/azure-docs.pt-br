---
title: Botão Personalizar a Leitura Avançada
titleSuffix: Azure Cognitive Services
description: Este artigo mostrará como personalizar o botão que inicia o leitor de imersão.
services: cognitive-services
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: metang
ms.openlocfilehash: 6d6757af6619edd41709a4b8f06ef615cd35bf30
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92636571"
---
# <a name="how-to-customize-the-immersive-reader-button"></a>Como personalizar o botão do leitor de imersão

Este artigo demonstra como personalizar o botão que inicia o leitor de imersão para atender às necessidades do seu aplicativo.

## <a name="add-the-immersive-reader-button"></a>Adicionar o botão leitor de imersão

O SDK do leitor de imersão fornece o estilo padrão para o botão que inicia o leitor de imersão. Use o `immersive-reader-button` atributo Class para habilitar esse estilo.

```html
<div class='immersive-reader-button'></div>
```

## <a name="customize-the-button-style"></a>Personalizar o estilo do botão

Use o `data-button-style` atributo para definir o estilo do botão. Os valores permitidos são `icon` , `text` e `iconAndText` . O valor padrão é `icon`.

### <a name="icon-button"></a>Botão de ícone

```html
<div class='immersive-reader-button' data-button-style='icon'></div>
```

Isso renderiza o seguinte:

![Botão de ícone](./media/button-icon.png)

### <a name="text-button"></a>Botão de texto

```html
<div class='immersive-reader-button' data-button-style='text'></div>
```

Isso renderiza o seguinte:

![Botão de ícone](./media/button-text.png)

### <a name="icon-and-text-button"></a>Ícone e botão de texto

```html
<div class='immersive-reader-button' data-button-style='iconAndText'></div>
```

Isso renderiza o seguinte:

![Botão de ícone](./media/button-icon-and-text.png)

## <a name="customize-the-button-text"></a>Personalizar o texto do botão

Configure o idioma e o texto ALT para o botão usando o `data-locale` atributo. O idioma padrão é inglês.

```html
<div class='immersive-reader-button' data-locale='fr-FR'></div>
```

## <a name="customize-the-size-of-the-icon"></a>Personalizar o tamanho do ícone

O tamanho do ícone do leitor de imersão pode ser configurado usando o `data-icon-px-size` atributo. Isso define o tamanho do ícone em pixels. O tamanho padrão é 20px.

```html
<div class='immersive-reader-button' data-icon-px-size='50'></div>
```

## <a name="next-steps"></a>Próximas etapas

* Explorar a [Referência do SDK da Leitura Avançada](./reference.md)