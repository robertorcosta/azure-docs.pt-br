---
title: Exemplos de código – Content Moderator, .NET
titleSuffix: Azure Cognitive Services
description: Use o Content Moderator em seus aplicativos .NET por meio do SDK.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: sample
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 23ef7b515f5391a30f6d1fa9fce1dcb856643c69
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72757273"
---
# <a name="content-moderator-net-sdk-samples"></a>Amostras do SDK do .NET do Content Moderator

A lista a seguir inclui links para exemplos de código criados usando o SDK do Content Moderator do Azure para .NET.

## <a name="moderation"></a>Moderação

- **Moderação de imagem**: [Avalie uma imagem em relação a conteúdo para adulto e sexual, texto e rostos](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageModeration/Program.cs). Consulte o [início rápido](image-moderation-quickstart-dotnet.md).
- **Imagens personalizadas**: [Modere com listas de imagem personalizadas](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageListManagement/Program.cs). Consulte o [início rápido](image-lists-quickstart-dotnet.md).

> [!NOTE]
> Há um limite máximo de **cinco listas de imagens**, e cada lista **não deve exceder 10 mil imagens**.
>

- **Moderação de texto**: [Tela de texto para conteúdo ofensivo e dados pessoais](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TextModeration/Program.cs). Consulte o [início rápido](text-moderation-quickstart-dotnet.md).
- **Termos personalizados**: [Modere com listas de termos personalizadas](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TermListManagement/Program.cs). Consulte o [início rápido](term-lists-quickstart-dotnet.md).

> [!NOTE]
> Há um limite máximo de **5 listas de termos** com cada lista para **não exceder 10.000 termos**.
>

- **Moderação de vídeo**: [Examine um vídeo em relação a conteúdo para adulto e sexual e obtenha resultados](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoModeration/Program.cs). Consulte o [início rápido](video-moderation-api.md).

## <a name="review"></a>Análise

- **Trabalhos de imagem**: [Inicie um trabalho de moderação que examina e cria revisões](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageJobs/Program.cs). Consulte o [início rápido](moderation-jobs-quickstart-dotnet.md).
- **Análises de imagem**: [Crie revisões com interação humana](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageReviews/Program.cs). Consulte o [início rápido](moderation-reviews-quickstart-dotnet.md).
- **Revisões de vídeo**: [Crie revisões de vídeo com interação humana](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoReviews/Program.cs). Consulte o [início rápido](video-reviews-quickstart-dotnet.md)
- **Revisões de transcrição de vídeo**: [Crie revisões de transcrição de vídeo com interação humana](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoTranscriptReviews/Program.cs) Confira o [início rápido](video-reviews-quickstart-dotnet.md)

Veja todos os exemplos de .NET nos [Exemplos amostras de .NET do Content Moderator no GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator).
