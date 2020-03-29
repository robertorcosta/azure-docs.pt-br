---
title: Referência de domínio pré-construída - LUIS
titleSuffix: Azure Cognitive Services
description: Referência para os domínios predefinidos, que são conjuntos predefinidos de tentativas e entidades de idioma Noções básicas sobre inteligente serviços (LUÍS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 05ad340b3856291832ba0521c7da70ad55260384
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270609"
---
# <a name="prebuilt-domain-reference-for-your-luis-app"></a>Referência de domínio predefinida para seu aplicativo LUIS
Esta referência contém informações sobre o [domínios pré-compilada](luis-how-to-use-prebuilt-domains.md), que são conjuntos predefinidos de entidades que oferece LUÍS e tentativas.

[Domínios personalizados](luis-how-to-start-new-app.md), por outro lado, iniciar sem tentativas e modelos. Você pode adicionar quaisquer tentativas de domínio pré-compilada e entidades a um modelo personalizado.

## <a name="custom-domains-per-language"></a>Domínios personalizados por idioma

A tabela abaixo resume os domínios suportados atualmente. O suporte para inglês é geralmente mais completo do que outros.

| Tipo de entidade       | EN-EUA      | ZH-CN   | DE    | FR     | ES    | IT      | PT-BR |  JP  |      Ko |        NL |    TR |
|:-----------------:|:-------:|:-------:|:-----:|:------:|:-----:|:-------:| :-------:| :-------:| :-------:| :-------:|  :-------:|
| Calendário  | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
|Comunicação  | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Email     | ✓    | ✓       | ✓   | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| HomeAutomation          | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Observações     | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Locais   | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| RestaurantReservation  | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Todo     | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Utilidades      | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Clima        | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Web    | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |

Os domínios pré-construídos não são **suportados** em:

* Canadense francês
* Híndi
* Espanhol Mexicano

## <a name="next-steps"></a>Próximas etapas

Aprenda a [entidade simples.](reference-entity-simple.md)