---
title: Requisitos de consulta
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 09/30/2020
ms.author: aahi
ms.openlocfilehash: 518865f78c170f1fbe4e65b96dc149c1b449a88b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91631351"
---
Na consulta, use o parâmetro `@StartTime` para obter dados de métrica de um carimbo de data/hora específico. O Assistente de Métricas substitui o parâmetro por uma cadeia de caracteres de formato `yyyy-MM-ddTHH:mm:ss` quando executa a consulta.

> [!IMPORTANT]
> A consulta deve retornar no máximo um registro para cada combinação de dimensão, em cada carimbo de data/hora. E todos os registros retornados pela consulta precisam ter os mesmos carimbos de data/hora. O Assistente de Métricas executará essa consulta para cada carimbo de data/hora para ingerir seus dados. Confira a seção [Perguntas frequentes sobre consultas](../faq.md#how-do-i-write-a-valid-query-for-ingesting-my-data) para ver mais informações e exemplos. 