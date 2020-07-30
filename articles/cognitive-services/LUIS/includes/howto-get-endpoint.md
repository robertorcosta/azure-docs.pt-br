---
title: incluir arquivo
description: incluir arquivo
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.date: 05/06/2020
ms.subservice: language-understanding
ms.topic: include
ms.author: diberry
ms.openlocfilehash: 01d102de24185eba789e041d8534d6fdce8c260b
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87375323"
---
Na seção **Gerenciar** (menu à direita superior), na página **Recursos do Azure** (menu à esquerda), copie a URL da **Consulta de Exemplo** e cole em uma nova guia do navegador.

A URL do ponto de extremidade é semelhante ao formato a seguir, com seu próprio subdomínio personalizado, ID do aplicativo e chave do ponto de extremidade substituindo ID_APP e ID_CHAVE:

```console
https://YOUR-CUSTOM-SUBDOMAIN.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/production/predict?subscription-key=KEY-ID&verbose=true&show-all-intents=true&log=true&query=YOUR_QUERY_HERE
```