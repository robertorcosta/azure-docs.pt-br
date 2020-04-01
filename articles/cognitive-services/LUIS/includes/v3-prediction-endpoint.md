---
title: Como obter o ponto de extremidade de previsão V3
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 23e7f88b23ac7af7470183877eeb1be89e79779c
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80287740"
---
1. No portal do LUIS, na seção **Gerenciar** (menu superior direito), na página **Recursos do Azure** (menu à esquerda), na guia **Recursos de Previsão**, copie a **Consulta de exemplo** na parte inferior da página.

    Cole a URL em uma guia do navegador da Web.

    A URL tem o ID do aplicativo, a chave e o nome do slot. A URL de ponto de extremidade de previsão V3 é semelhante a:

    `https://REGION.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/SLOT-NAME/predict?subscription-key=YOUR-KEY&<optional-name-value-pairs>&query=YOUR_QUERY_HERE`

