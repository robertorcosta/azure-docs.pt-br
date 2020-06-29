---
title: Armazenar preferências do usuário
titleSuffix: Azure Cognitive Services
description: Este artigo mostrará como armazenar as preferências do usuário.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: metan
ms.openlocfilehash: beb053551dc1fa28672c488b31dfb29ca3b53651
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/27/2020
ms.locfileid: "85486892"
---
# <a name="how-to-store-user-preferences"></a>Como armazenar as preferências do usuário

Este artigo demonstra como armazenar as preferências do usuário. Essa funcionalidade é destinada como um meio alternativo de armazenar as preferências do usuário no caso em que o uso de cookies não é desejável ou viável.

## <a name="how-to-enable-storing-user-preferences"></a>Como habilitar o armazenamento de preferências do usuário

O `options` parâmetro contém o `onPreferencesChanged` retorno de chamada. Essa função será chamada sempre que o usuário alterar suas preferências (por exemplo, altera o tamanho do texto, a cor do tema, a fonte e assim por diante). O `value` parâmetro contém uma cadeia de caracteres, que representa as preferências atuais do usuário. Essa cadeia de caracteres pode ser armazenada usando qualquer mecanismo que você preferir.

```typescript
const options = {
    onPreferencesChanged: (value: string) => {
        // Store user preferences here
    }
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="how-to-load-user-preferences-into-the-immersive-reader"></a>Como carregar as preferências do usuário no leitor de imersão

Passe as preferências do usuário para o leitor de imersão usando o `preferences` parâmetro. Um exemplo trivial para armazenar e carregar as preferências do usuário é o seguinte:

```typescript
let userPreferences = null;
const options = {
    preferences: userPreferences,
    onPreferencesChanged: (value: string) => {
        userPreferences = value;
    }
};
```

## <a name="next-steps"></a>Próximas etapas

* Explorar a [Referência do SDK da Leitura Avançada](./reference.md)