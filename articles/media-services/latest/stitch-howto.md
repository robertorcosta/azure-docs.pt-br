---
title: Como unir dois ou mais arquivos de vídeo com o .NET | Microsoft Docs
description: Este artigo mostra como unir dois ou mais arquivos de vídeo.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: how-to
ms.date: 03/24/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 5eacc366961d3101a7eaf8877a34ef2d462ea76b
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/25/2021
ms.locfileid: "105111418"
---
# <a name="how-to-stitch-two-or-more-video-files-with-net"></a>Como unir dois ou mais arquivos de vídeo com o .NET

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="stitch-two-or-more-video-files"></a>Costurar dois ou mais arquivos de vídeo

O exemplo a seguir ilustra como você pode gerar uma predefinição para unir dois ou mais arquivos de vídeo. O cenário mais comum é quando você deseja adicionar um cabeçalho ou um rodapé ao vídeo principal.

> [!NOTE]
> Os arquivos de vídeo editados juntos devem compartilhar Propriedades (resolução de vídeo, taxa de quadros, contagem de faixas de áudio, etc.). Você deve ter cuidado para não misturar vídeos de taxas de quadros diferentes, ou com um número diferente de faixas de áudio.

## <a name="prerequisites"></a>Pré-requisitos

Clone ou baixe os [exemplos do .net dos serviços de mídia](https://github.com/Azure-Samples/media-services-v3-dotnet/).  O código que é referenciado abaixo está localizado na [pasta EncodingWithMESCustomStitchTwoAssets](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/VideoEncoding/EncodingWithMESCustomStitchTwoAssets/Program.cs)

### <a name="net-code"></a>Código do .NET

[!code-csharp[Main](../../../media-services-v3-dotnet/VideoEncoding/EncodingWithMESCustomStitchTwoAssets/Program.cs)]
