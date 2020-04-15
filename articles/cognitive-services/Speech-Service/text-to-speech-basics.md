---
title: Noções básicas da sintetização de voz – Serviço de Fala
titleSuffix: Azure Cognitive Services
description: Saiba como usar o SDK de Fala para converter texto em fala. Neste artigo, você aprenderá sobre a construção de objeto, os formatos de saída de áudio com suporte e as opções de configuração personalizada para a sintetização de voz.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 04/06/2020
ms.author: trbye
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: b95b55588c51c4e202d7a02c6c158b26cdcd7ac7
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985959"
---
# <a name="learn-the-basics-of-speech-synthesis"></a>Conheça as noções básicas da sintetização de voz

Neste artigo, você aprende os padrões de design comuns para fazer uma síntese da conversão de texto em fala usando o SDK de Fala. Você começa fazendo a configuração e a sintetização básicas e passa para exemplos mais avançados de desenvolvimento de aplicativos personalizados, incluindo:

* Obter respostas como fluxos na memória
* Personalizar a taxa de bits e a taxa de amostragem de saída
* Enviar solicitações de sintetização usando SSML (linguagem de marcação de sintetização de voz)
* Usar vozes neurais

> [!TIP]
> Se ainda não teve a oportunidade de concluir um de nossos inícios rápidos, incentivamos você a testar e experimentar o reconhecimento de fala para você.
> * [Reconhecer fala de um microfone](quickstarts/text-to-speech.md)

::: zone pivot="programming-language-csharp"
[!INCLUDE [C# Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-csharp.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [C++ Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-cpp.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-java.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-python.md)]
::: zone-end

::: zone pivot="programming-language-more"
[!INCLUDE [More languages include](./includes/how-to/speech-to-text-basics/more.md)]
::: zone-end

## <a name="next-steps"></a>Próximas etapas

* [Introdução à Voz Personalizada](how-to-custom-voice.md)
* [Aprimorar a sintetização com SSML](speech-synthesis-markup.md)