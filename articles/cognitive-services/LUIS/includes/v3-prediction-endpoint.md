---
title: Como obter o ponto de extremidade de previsão V3
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/05/2020
ms.author: diberry
ms.openlocfilehash: 18f5422202cf972d49349cc04b845c623cabffa3
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83589101"
---
1. No portal do LUIS, na seção **Gerenciar** (menu à direita superior), na página **Recursos do Azure** (menu à esquerda), na guia **Recursos de Previsão**, copie a **Consulta de Exemplo** na parte inferior da página.

    Cole a URL em uma guia do navegador da Web.

    A URL tem o ID do aplicativo, a chave e o nome do slot. A URL de ponto de extremidade de previsão V3 é semelhante a:

    `https://YOUR-RESOURCE-NAME.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/SLOT-NAME/predict?subscription-key=YOUR-PREDICTION-KEY&<optional-name-value-pairs>&query=YOUR_QUERY_HERE`

