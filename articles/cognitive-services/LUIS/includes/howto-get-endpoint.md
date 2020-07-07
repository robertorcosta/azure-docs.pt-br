---
title: incluir arquivo
description: incluir arquivo
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: include file
ms.service: cognitive-services
ms.date: 05/06/2020
ms.subservice: language-understanding
ms.topic: include
ms.author: diberry
ms.openlocfilehash: a6e6c89c34723fb9e11b0c7e4ab8c9bedb8aa9ca
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2020
ms.locfileid: "85959001"
---
Na seção **Gerenciar** (menu à direita superior), na página **Recursos do Azure** (menu à esquerda), copie a URL da **Consulta de Exemplo** e cole em uma nova guia do navegador.

A URL do ponto de extremidade é semelhante ao formato a seguir, com seu próprio subdomínio personalizado, ID do aplicativo e chave do ponto de extremidade substituindo ID_APP e ID_CHAVE:

```console
https://YOUR-CUSTOM-SUBDOMAIN.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/production/predict?subscription-key=KEY-ID&verbose=true&show-all-intents=true&log=true&query=YOUR_QUERY_HERE
```