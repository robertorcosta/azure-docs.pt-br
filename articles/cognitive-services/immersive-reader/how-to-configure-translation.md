---
title: Configurar a tradução
titleSuffix: Azure Cognitive Services
description: Este artigo mostrará como configurar as várias opções de tradução.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: metan
ms.openlocfilehash: ea066fab713100309103a040d309bac5b984fb99
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85486894"
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