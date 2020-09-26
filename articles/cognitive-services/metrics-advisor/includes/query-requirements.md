---
title: Requisitos de consulta
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 09/10/2020
ms.author: aahi
ms.openlocfilehash: 68b5946cb21bef1c8979a94c8780682cfe9defa6
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91376652"
---
Na consulta, use o parâmetro `@StartTime` para obter os dados de métrica de um carimbo de data/hora específico. Isso será substituído por uma cadeia de caracteres no formato `yyyy-MM-ddTHH:mm:ss`. 

> [!IMPORTANT]
> Verifique se apenas os dados de métrica de **um carimbo de data/hora** serão retornados pela consulta. O Assistente de Métricas executará a consulta para cada carimbo de data/hora a fim de obter os dados de métrica correspondentes. Por exemplo, uma consulta para uma métrica com granularidade *diária* deve conter apenas um carimbo de data/hora, como `2020-06-21T00:00:00Z`, ao executar a consulta uma vez. 
