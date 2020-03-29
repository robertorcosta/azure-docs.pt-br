---
title: Conteúdo adulto, atrevido e sangrento - Visão computacional
titleSuffix: Azure Cognitive Services
description: Conceitos relacionados à detecção de conteúdo adulto em imagens usando a APi de Visão Computacional.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: ee18916a59bb081d65494f46e7aba7c29c7177cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "71718526"
---
# <a name="detect-adult-content"></a>Detectar conteúdo adulto

O Computer Vision pode detectar material adulto em imagens para que os desenvolvedores possam restringir a exibição dessas imagens em seu software. Os sinalizadores de conteúdo são aplicados com uma pontuação entre zero e um para que os desenvolvedores possam interpretar os resultados de acordo com suas próprias preferências.

> [!NOTE]
> Grande parte dessa funcionalidade é oferecida pelo serviço [Azure Content Moderator.](https://docs.microsoft.com/azure/cognitive-services/content-moderator/overview) Confira essa alternativa para soluções para cenários de moderação de conteúdo mais rigorosos, como a moderação de texto e fluxos de trabalho de análise humana.

## <a name="content-flag-definitions"></a>Definições de sinalizador de conteúdo

Dentro da classificação "adulto" estão várias categorias diferentes:

- As imagens **adultas** são definidas como aquelas que são explicitamente de natureza sexual e muitas vezes retratam nudez e atos sexuais.
- As imagens **picantes** são definidas como imagens que são sexualmente sugestivas na natureza e muitas vezes contêm conteúdo menos sexualmente explícito do que imagens marcadas como **adultas**.
- **Imagens sangrentas** são definidas como aquelas que retratam gore.

## <a name="use-the-api"></a>Usar a API

Você pode detectar conteúdo adulto com a API [Analisar imagem.](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) Quando você adiciona `Adult` o valor do parâmetro de consulta **visualFeatures,** a&mdash;`isAdultContent` `isRacyContent`API `isGoryContent` &mdash;retorna três propriedades booleanas e em sua resposta JSON. O método também&mdash;`adultScore`devolve `racyScore`propriedades `goreScore` &mdash;correspondentes, e que representam escores de confiança entre zero e um para cada categoria respectiva.

- [Quickstart: Analise uma imagem (.NET SDK)](./quickstarts-sdk/csharp-analyze-sdk.md)
- [Quickstart: Analise uma imagem (Rest API)](./quickstarts/csharp-analyze.md)
