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
ms.openlocfilehash: 2a98e2a97a9154d9e256a4662bb292896c6d1e77
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588763"
---
Na seção **Gerenciar** (menu à direita superior), na página **Recursos do Azure** (menu à esquerda), copie a URL da **Consulta de Exemplo** e cole em uma nova guia do navegador.

A URL do ponto de extremidade é semelhante ao formato a seguir, com seu próprio subdomínio personalizado, ID do aplicativo e chave do ponto de extremidade substituindo ID_APP e ID_CHAVE:

```console
https://YOUR-CUSTOM-SUBDMAIN.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/production/predict?subscription-key=KEY-ID&verbose=true&show-all-intents=true&log=true&query=YOUR_QUERY_HERE
```