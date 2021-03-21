---
title: Configurar a tradução
titleSuffix: Azure Cognitive Services
description: Este artigo mostrará como configurar as várias opções de tradução.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: how-to
ms.date: 06/29/2020
ms.author: metang
ms.openlocfilehash: bb90cb3a86acb6a94fa92c33d78ec596659e105f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102608692"
---
# <a name="how-to-configure-translation"></a>Como configurar a tradução

Este artigo demonstra como configurar as várias opções de tradução no leitor de imersão.

## <a name="configure-translation-language"></a>Configurar idioma de tradução

O `options` parâmetro contém todos os sinalizadores que podem ser usados para configurar a tradução. Defina o `language` parâmetro para o idioma para o qual você deseja traduzir. Consulte o [suporte ao idioma](./language-support.md) para obter a lista completa de idiomas com suporte.

```typescript
const options = {
    translationOptions: {
        language: 'fr-FR'
    }
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="automatically-translate-the-document-on-load"></a>Traduzir automaticamente o documento na carga

Defina `autoEnableDocumentTranslation` como `true` para habilitar a tradução automática de todo o documento quando o leitor de imersão for carregado.

```typescript
const options = {
    translationOptions: {
        autoEnableDocumentTranslation: true
    }
};
```

## <a name="automatically-enable-word-translation"></a>Habilitar automaticamente a tradução automática

Defina `autoEnableWordTranslation` como `true` para habilitar a tradução de uma única palavra.

```typescript
const options = {
    translationOptions: {
        autoEnableWordTranslation: true
    }
};
```

## <a name="next-steps"></a>Próximas etapas

* Explorar a [Referência do SDK da Leitura Avançada](./reference.md)