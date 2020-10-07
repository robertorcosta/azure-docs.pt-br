---
title: Como obter o ponto de extremidade de previsão V3
titleSuffix: Azure Cognitive Services
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 05/05/2020
ms.openlocfilehash: a607ad71915606d6046b4c71291a49b8641bdcc9
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91545047"
---
1. No portal do LUIS, na seção **Gerenciar** (menu à direita superior), na página **Recursos do Azure** (menu à esquerda), na guia **Recursos de Previsão**, copie a **Consulta de Exemplo** na parte inferior da página.

    Cole a URL em uma guia do navegador da Web.

    A URL tem o ID do aplicativo, a chave e o nome do slot. A URL de ponto de extremidade de previsão V3 é semelhante a:

    `https://YOUR-RESOURCE-NAME.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/SLOT-NAME/predict?subscription-key=YOUR-PREDICTION-KEY&<optional-name-value-pairs>&query=YOUR_QUERY_HERE`

