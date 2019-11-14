---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: include file
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.date: 11/05/2019
ms.author: diberry
ms.openlocfilehash: 39bfa5b6800c65112850faa8842b915ceedb9312
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73648062"
---
Na seção **Gerenciar** (menu superior direito), na página de **Recursos do Azure** (menu à esquerda), copie a URL da **Consulta de Exemplo** e, em seguida, cole em uma nova guia do navegador. 

A URL do ponto de extremidade é semelhante ao formato a seguir, com sua própria ID do aplicativo e chave de ponto de extremidade, que substituem APP-ID e KEY-ID:

```console
https://westus.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/production/predict?subscription-key=KEY-ID&verbose=true&show-all-intents=true&log=true&query=YOUR_QUERY_HERE
```