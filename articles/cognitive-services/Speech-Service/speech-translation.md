---
title: Tradução de fala com serviço de fala
titleSuffix: Azure Cognitive Services
description: O serviço de fala permite adicionar tradução de fala de fim a fim, em tempo real e em vários idiomas aos seus aplicativos, ferramentas e dispositivos. A mesma API pode ser usada para a tradução com conversão de fala em fala e de fala em texto.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: erhopf
ms.openlocfilehash: f51288da6af3580ba7592950cde4f17d7adad529
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80052614"
---
# <a name="what-is-speech-translation"></a>O que é tradução de fala?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

A tradução de fala do serviço Speech permite a tradução em tempo real, em várias línguas, de áudio para discurso e de áudio para texto de fluxos de áudio. Com o Speech SDK, seus aplicativos, ferramentas e dispositivos têm acesso a transcrições de origem e saídas de tradução para áudio fornecido. Os resultados de transcrição e tradução provisórias são retornados à medida que a fala é detectada, e os resultados finais podem ser convertidos em discurso sintetizado.

O motor de tradução da Microsoft é alimentado por duas abordagens diferentes: tradução automática estatística (SMT) e tradução de máquina neural (NMT). A SMT utiliza análiseestatística avançada para estimar as melhores traduções possíveis, dado o contexto de algumas palavras. Com o NMT, as redes neurais são usadas para fornecer traduções mais precisas e naturais usando o contexto completo das frases para traduzir palavras.

Hoje, a Microsoft usa o NMT para tradução para idiomas mais populares. Todos os [idiomas disponíveis para tradução de fala em fala](language-support.md#speech-translation) também são alimentadas por NMT. A tradução com conversão de fala em texto pode usar SMT ou NMT, dependendo do par de idiomas. Quando o idioma de destino é suportado pelo NMT, a tradução completa é alimentada por NMT. Quando o idioma-alvo não é suportado pelo NMT, a tradução é um híbrido de NMT e SMT, usando o inglês como um "pivô" entre os dois idiomas.

## <a name="core-features"></a>Características principais

Aqui estão os recursos disponíveis através do Speech SDK e REST APIs:

| Caso de uso | . | REST |
|----------|-----|------|
| Tradução de fala para texto com resultados de reconhecimento. | Sim | Não |
| Tradução de fala para fala. | Sim | Não |
| Reconhecimento provisório e resultados de tradução. | Sim | Não |

## <a name="get-started-with-speech-translation"></a>Comece com a tradução de fala

Oferecemos partidas rápidas projetadas para que você esteja executando o código em menos de 10 minutos. Esta tabela inclui uma lista de discussões de fala rápidas organizadas pelo idioma.

| Guia de Início Rápido | Plataforma | Reference API |
|------------|----------|---------------|
| [C#, .NET Core](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnetcore) | Windows | [Procurar](https://aka.ms/csspeech/csharpref) |
| [C#, .NET Framework](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnet) | Windows | [Procurar](https://aka.ms/csspeech/csharpref) |
| [C#, UWP](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=uwp) | Windows | [Procurar](https://aka.ms/csspeech/csharpref) |
| [C++](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-cpp&tabs=windows) | Windows | [Procurar](https://aka.ms/csspeech/cppref)|
| [Java](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-java&tabs=jre) | Windows, Linux, macOS | [Procurar](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>Código de exemplo

O código de exemplo para o Speech SDK está disponível no GitHub. Essas amostras cobrem cenários comuns, como a leitura de áudio de um arquivo ou fluxo, reconhecimento/tradução contínuo e de uma única tomada e trabalho com modelos personalizados.

* [Amostras de fala para texto e tradução (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)

## <a name="migration-guides"></a>Guias de migração

Se seus aplicativos, ferramentas ou produtos estiverem usando a [API de fala do tradutor,](https://docs.microsoft.com/azure/cognitive-services/translator-speech/overview)criamos guias para ajudá-lo a migrar para o serviço Speech.

* [Migre da API de fala tradutora para o serviço de fala](how-to-migrate-from-translator-speech-api.md)

## <a name="reference-docs"></a>Documentos de Referência

* [SDK de fala](speech-sdk-reference.md)
* [SDK de Dispositivos de Fala](speech-devices-sdk.md)
* [API REST: Fala-a-texto](rest-speech-to-text.md)
* [API REST: Texto-para-fala](rest-text-to-speech.md)
* [API REST: Transcrição e personalização em lote](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Próximas etapas

* [Obter gratuitamente uma chave de assinatura dos Serviços de Fala](get-started.md)
* [Obter o SDK de Fala](speech-sdk.md)
