---
title: Requisitos de consulta
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 09/30/2020
ms.author: mbullwin
ms.openlocfilehash: 777e3261bd6f842861ed6622b78589eedaaf65d7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92043180"
---
Na consulta, use o parâmetro `@StartTime` para obter dados de métrica de um carimbo de data/hora específico. O Assistente de Métricas substitui o parâmetro por uma cadeia de caracteres de formato `yyyy-MM-ddTHH:mm:ss` quando executa a consulta.

> [!IMPORTANT]
> A consulta deve retornar no máximo um registro para cada combinação de dimensão, em cada carimbo de data/hora. E todos os registros retornados pela consulta precisam ter os mesmos carimbos de data/hora. O Assistente de Métricas executará essa consulta para cada carimbo de data/hora para ingerir seus dados. Confira a seção [Perguntas frequentes sobre consultas](../faq.md#how-do-i-write-a-valid-query-for-ingesting-my-data) para ver mais informações e exemplos. 