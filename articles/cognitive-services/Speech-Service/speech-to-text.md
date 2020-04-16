---
title: Fala-a-texto - Serviço de fala
titleSuffix: Azure Cognitive Services
description: O recurso de fala para texto permite a transcrição em tempo real de fluxos de áudio em texto. Seus aplicativos, ferramentas ou dispositivos podem consumir, exibir e agir nesta entrada de texto. Este serviço funciona perfeitamente com o texto-para-fala (síntese de fala) e recursos de tradução de fala.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: trbye
ms.openlocfilehash: e0aea7e55381e9571b156701699f5f45315bb384
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399695"
---
# <a name="what-is-speech-to-text"></a>O que é conversão de fala em texto?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

O serviço de fala para texto, também conhecido como reconhecimento de fala, permite a transcrição em tempo real de fluxos de áudio em texto. Seus aplicativos, ferramentas ou dispositivos podem consumir, exibir e agir neste texto como entrada de comando. Este serviço é alimentado pela mesma tecnologia de reconhecimento que a Microsoft usa para produtos Cortana e Office. Funciona perfeitamente com as ofertas de serviços de <a href="./speech-translation.md" target="_blank">tradução <span class="docon docon-navigate-external x-hidden-focus"></span> </a> e <a href="./text-to-speech.md" target="_blank">texto-para-fala. <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Para obter uma lista completa de idiomas de voz para texto disponíveis, consulte [idiomas suportados](language-support.md#speech-to-text).

O serviço de fala para texto é padrão para usar o modelo de linguagem Universal. Este modelo foi treinado usando dados de propriedade da Microsoft e é implantado na nuvem. É ideal para cenários de conversação e ditado. Ao usar o discurso-para-texto para reconhecimento e transcrição em um ambiente único, você pode criar e treinar modelos personalizados de acústica, linguagem e pronúncia. A personalização é útil para lidar com o ruído ambiente ou o vocabulário específico da indústria.

> [!NOTE]
> Bing Speech foi desativado em 15 de outubro de 2019. Se seus aplicativos, ferramentas ou produtos estiverem usando as APIs do Bing Speech, criamos guias para ajudá-lo a migrar para o serviço Speech.
> - [Migrar do Bing Speech para o serviço de fala](how-to-migrate-from-bing-speech.md)

## <a name="get-started-with-speech-to-text"></a>Comece com o discurso-para-texto

O serviço de fala-texto está disponível através do [Speech SDK](speech-sdk.md). Existem vários cenários comuns disponíveis como quickstarts, em vários idiomas e plataformas:

 - [Quickstart: Reconheça a fala com entrada de microfone](quickstarts/speech-to-text-from-microphone.md)
 - [Quickstart: Reconheça a fala de um arquivo](quickstarts/speech-to-text-from-file.md)
 - [Quickstart: Reconheça a fala armazenada no armazenamento de bolhas](quickstarts/from-blob.md)

Se preferir usar o serviço DE REPOUSO de fala para texto, consulte [REST APIs](rest-speech-to-text.md).

## <a name="tutorials-and-sample-code"></a>Tutoriais e código de amostra

Depois que tiver a oportunidade de usar o serviço de Fala, experimente nosso tutorial que ensina a reconhecer intenções de fala usando o SDK de Fala e o LUIS.

- [Tutorial: Reconheça intenções da fala com o Speech SDK e LUIS, usando C #](how-to-recognize-intents-from-speech-csharp.md)

O código de exemplo para o Speech SDK está disponível no GitHub. Esses exemplos abordam cenários comuns, como ler áudio de um arquivo ou fluxo, reconhecimento contínuo e único e trabalho com modelos personalizados.

- [Amostras de fala para texto (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Exemplos de transcrição em lote (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)

## <a name="customization"></a>Personalização

Além do modelo padrão de serviço de fala, você pode criar modelos personalizados. A personalização ajuda a superar barreiras de reconhecimento de fala, como estilo de fala, vocabulário e ruído de fundo, ver [Discurso Personalizado](how-to-custom-speech.md). As opções de personalização variam de acordo com o idioma/local, veja [idiomas suportados](supported-languages.md) para verificar o suporte.

[!INCLUDE [speech-reference-doc-links](includes/speech-reference-doc-links.md)]

## <a name="next-steps"></a>Próximas etapas

- [Obter gratuitamente uma chave de assinatura dos Serviços de Fala](get-started.md)
- [Obter o SDK de Fala](speech-sdk.md)
