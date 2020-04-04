---
title: Stream codec compactaáudio com o Speech SDK - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Aprenda a transmitir áudio compactado para o serviço Speech com o Speech SDK. Disponível para C++, C#, e Java para Linux, Java no Android e Objective-C no iOS.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: amishu
zone_pivot_groups: programming-languages-set-twelve
ms.openlocfilehash: bd6b500a823bde654da4442704b75451806d6a46
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637290"
---
# <a name="use-codec-compressed-audio-input-with-the-speech-sdk"></a>Use a entrada de áudio compactada codec com o Speech SDK

O serviço de fala SDK **Compactd Audio Input Stream** API fornece uma `PullStream` maneira `PushStream`de transmitir áudio compactado para o serviço Speech usando um ou .

Streaming de áudio de entrada compactada é atualmente suportado para C#, C++, Java on Windows (os aplicativos UWP não são suportados) e Linux (Ubuntu 16.04, Ubuntu 18.04, Debian 9, RHEL 8, CentOS 8). Ele também é suportado para Java em Android e Objective-C na plataforma iOS.
* A versão 1.10.0 do Speech SDK ou posterior é necessária para OHeL 8 e CentOS 8
* A versão 1.11.0 ou posterior do Speech SDK é necessária para o Windows.

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

## <a name="prerequisites"></a>Pré-requisitos

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/cpp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/java/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-objectivec"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/objectivec/prerequisites.md)]
::: zone-end

## <a name="example-code-using-codec-compressed-audio-input"></a>Exemplo de código usando entrada de áudio compactada codec

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/csharp/examples.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/cpp/examples.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/java/examples.md)]
::: zone-end

::: zone pivot="programming-language-objectivec"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/objectivec/examples.md)]
::: zone-end

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Aprenda a reconhecer a fala](quickstarts/speech-to-text-from-microphone.md)
