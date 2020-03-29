---
title: Definir política de cookies de leitor imersivo
titleSuffix: Azure Cognitive Services
description: Este artigo mostrará como definir a política de cookies para o Leitor Imersivo.
services: cognitive-services
author: pasta
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: pasta
ms.openlocfilehash: 6de651f1eb51ea1bc941c2af675c8a8d5b0f9cd7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "75946106"
---
# <a name="how-to-set-the-cookie-policy-for-the-immersive-reader"></a>Como definir a política de cookies para o Leitor Imersivo

O Immersive Reader desativará o uso de cookies por padrão. Se você habilitar o uso de cookies, o Immersive Reader poderá usar cookies para manter as preferências do usuário e rastrear o uso do recurso. Se você habilitar o uso de cookies no Immersive Reader, considere os requisitos da Política de Conformidade de Cookies da UE. É responsabilidade do aplicativo host obter qualquer consentimento necessário do usuário de acordo com a Política de Conformidade de Cookies da UE.

A política de cookies pode ser definida através das [opções](../reference.md#options)Immersive Reader . Consulte [CookiePolicy enum](../reference.md#cookiepolicy-enum) para obter mais informações.

## <a name="enable-cookie-usage"></a>Habilite o uso de cookies

```javascript
var options = {
    'cookiePolicy': ImmersiveReader.CookiePolicy.Enable
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="disable-cookie-usage"></a>Desativar o uso de cookies

```javascript
var options = {
    'cookiePolicy': ImmersiveReader.CookiePolicy.Disable
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="next-steps"></a>Próximas etapas

* Confira o [Início rápido do Node.js](../quickstart-nodejs.md) para ver o que mais você pode fazer com o SDK de Leitura Avançada usando Node.js
* Confira o [tutorial do Python](../tutorial-python.md) para ver o que mais você pode fazer com o SDK de Leitura Avançada usando Python
* Veja o [tutorial swift](../tutorial-ios-picture-immersive-reader.md) para ver o que mais você pode fazer com o Leitor Imersivo SDK usando Swift
* Explore o [SDK da Leitura Avançada](https://github.com/microsoft/immersive-reader-sdk) e a [Referência de SDK da Leitura Avançada](../reference.md)