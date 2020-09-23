---
title: Requisitos de consulta
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/10/2020
ms.author: aahi
ms.openlocfilehash: 23da214f4ffc9e589523f7ada00a77494183eb15
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90933691"
---
Na consulta, use o parâmetro `@StartTime` para obter os dados de métrica de um carimbo de data/hora específico. Isso será substituído por uma cadeia de caracteres no formato `yyyy-MM-ddTHH:mm:ss`. 

> [!IMPORTANT]
> Verifique se apenas os dados de métrica de **um carimbo de data/hora** serão retornados pela consulta. O Assistente de Métricas executará a consulta para cada carimbo de data/hora a fim de obter os dados de métrica correspondentes. Por exemplo, uma consulta para uma métrica com granularidade *diária* deve conter apenas um carimbo de data/hora, como `2020-06-21T00:00:00Z`, ao executar a consulta uma vez. 
