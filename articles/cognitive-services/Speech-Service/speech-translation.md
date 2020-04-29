---
title: Tradução de fala com o serviço de fala
titleSuffix: Azure Cognitive Services
description: O serviço de fala permite que você adicione tradução de fala de ponta a ponta, em tempo real e em vários idiomas, a seus aplicativos, ferramentas e dispositivos. A mesma API pode ser usada para a tradução com conversão de fala em fala e de fala em texto.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: erhopf
ms.openlocfilehash: f51288da6af3580ba7592950cde4f17d7adad529
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "80052614"
---
# <a name="what-is-speech-translation"></a>O que é tradução de fala?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

A tradução de fala do serviço de fala permite a tradução de fala em tempo real, de voz a fala e de fala a texto de fluxos de áudio. Com o SDK de fala, seus aplicativos, ferramentas e dispositivos têm acesso a transcrições de origem e saídas de tradução para áudio fornecido. Os resultados provisórios de transcrição e tradução são retornados conforme a fala é detectada e os resultados de finais podem ser convertidos em fala sintetizada.

O mecanismo de tradução da Microsoft é fornecido por duas abordagens diferentes: conversão de máquina estatística (SMT) e conversão de máquina neural (NMT). O SMT usa análise estatística avançada para estimar a melhor tradução possível, dado o contexto de algumas palavras. Com o NMT, as redes neurais são usadas para fornecer traduções mais precisas e de som natural usando o contexto completo de frases para traduzir palavras.

Hoje, a Microsoft usa o NMT para tradução para as linguagens mais populares. Todos os [idiomas disponíveis para tradução de fala em fala](language-support.md#speech-translation) também são alimentadas por NMT. A tradução com conversão de fala em texto pode usar SMT ou NMT, dependendo do par de idiomas. Quando o idioma de destino tem suporte do NMT, a tradução completa é NMT. Quando o idioma de destino não tem suporte do NMT, a tradução é um híbrido de NMT e SMT, usando o inglês como uma "dinamização" entre os dois idiomas.

## <a name="core-features"></a>Principais recursos

Aqui estão os recursos disponíveis por meio do SDK de fala e APIs REST:

| Caso de uso | . | REST |
|----------|-----|------|
| Tradução de conversão de fala em texto com resultados de reconhecimento. | Sim | Não |
| Conversão de fala em fala. | Sim | Não |
| Resultados de tradução e de reconhecimento provisórios. | Sim | Não |

## <a name="get-started-with-speech-translation"></a>Introdução à tradução de fala

Oferecemos guias de início rápido projetados para que você execute códigos em menos de 10 minutos. Esta tabela inclui uma lista de guias de início rápido de tradução de fala organizados por idioma.

| Guia de Início Rápido | Plataforma | Reference API |
|------------|----------|---------------|
| [C#, .NET Core](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnetcore) | Windows | [Procurar](https://aka.ms/csspeech/csharpref) |
| [C#, .NET Framework](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnet) | Windows | [Procurar](https://aka.ms/csspeech/csharpref) |
| [C#, UWP](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=uwp) | Windows | [Procurar](https://aka.ms/csspeech/csharpref) |
| [C](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-cpp&tabs=windows) | Windows | [Procurar](https://aka.ms/csspeech/cppref)|
| [Java](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-java&tabs=jre) | Windows, Linux, macOS | [Procurar](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>Código de exemplo

O código de exemplo para o SDK de fala está disponível no GitHub. Esses exemplos abrangem cenários comuns, como a leitura de áudio de um arquivo ou fluxo, um reconhecimento/conversão de captura única e contínua e o trabalho com modelos personalizados.

* [Exemplos de conversão de fala em texto e tradução (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)

## <a name="migration-guides"></a>Guias de migração

Se seus aplicativos, ferramentas ou produtos estiverem usando o [API de tradução de fala](https://docs.microsoft.com/azure/cognitive-services/translator-speech/overview), criamos guias para ajudá-lo a migrar para o serviço de fala.

* [Migrar do API de Tradução de Fala para o serviço de fala](how-to-migrate-from-translator-speech-api.md)

## <a name="reference-docs"></a>Documentos de Referência

* [SDK da fala](speech-sdk-reference.md)
* [SDK de Dispositivos de Fala](speech-devices-sdk.md)
* [API REST: conversão de fala em texto](rest-speech-to-text.md)
* [API REST: conversão de texto em fala](rest-text-to-speech.md)
* [API REST: transcrição e personalização em lote](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Próximas etapas

* [Obter gratuitamente uma chave de assinatura dos Serviços de Fala](get-started.md)
* [Obter o SDK de Fala](speech-sdk.md)
