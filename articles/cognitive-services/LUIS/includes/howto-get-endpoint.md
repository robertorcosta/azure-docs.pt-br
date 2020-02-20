---
title: incluir arquivo
description: incluir arquivo
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: include file
ms.service: cognitive-services
ms.date: 02/14/2020
ms.subservice: language-understanding
ms.topic: include
ms.author: diberry
ms.openlocfilehash: 5b1b361778de145a5e32a07bb0164ff2293d9a1a
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77279586"
---
Na seção **Gerenciar** (menu superior direito), na página de **Recursos do Azure** (menu à esquerda), copie a URL da **Consulta de Exemplo** e, em seguida, cole em uma nova guia do navegador.

A URL do ponto de extremidade é semelhante ao formato a seguir, com sua própria ID do aplicativo e chave de ponto de extremidade, que substituem APP-ID e KEY-ID:

```console
https://westus.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/production/predict?subscription-key=KEY-ID&verbose=true&show-all-intents=true&log=true&query=YOUR_QUERY_HERE
```