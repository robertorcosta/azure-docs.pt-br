---
title: Speech-to-Text-serviço de fala
titleSuffix: Azure Cognitive Services
description: O recurso de conversão de fala em texto permite a transcrição em tempo real de fluxos de áudio em texto. Seus aplicativos, ferramentas ou dispositivos podem consumir, exibir e tomar medidas nessa entrada de texto. Esse serviço funciona diretamente com os recursos de conversão de voz (síntese de fala) e de texto em fala.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: trbye
ms.openlocfilehash: 90a5ec8c58865c3aa1b210db51b9ffeb7169d641
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88640405"
---
# <a name="what-is-speech-to-text"></a>O que é conversão de fala em texto?

A conversão de fala em texto do serviço de fala, também conhecida como reconhecimento de fala, permite a transcrição em tempo real de fluxos de áudio em texto. Seus aplicativos, ferramentas ou dispositivos podem consumir, exibir e tomar medidas sobre esse texto como entrada de comando. Esse serviço é fornecido pela mesma tecnologia de reconhecimento usada pela Microsoft para os produtos Cortana e Office. Ele funciona diretamente com as ofertas <a href="./speech-translation.md" target="_blank">de <span class="docon docon-navigate-external x-hidden-focus"></span> tradução</a> e serviço de conversão de <a href="./text-to-speech.md" target="_blank">texto em fala <span class="docon docon-navigate-external x-hidden-focus"></span> </a> . Para obter uma lista completa de idiomas de fala para texto disponíveis, consulte [idiomas com suporte](language-support.md#speech-to-text).

O serviço de conversão de voz em texto usa o modelo de linguagem universal. Esse modelo foi treinado usando dados de propriedade da Microsoft e é implantado na nuvem. É ideal para cenários de conversação e de ditado. Ao usar a conversão de fala em texto para reconhecimento e transcrição em um ambiente exclusivo, você pode criar e treinar modelos acústicos, de linguagem e de pronúncia personalizados. A personalização é útil para lidar com ruídos de ambiente ou vocabulário específico do setor.

Com o texto de referência adicional como entrada, o serviço de conversão de fala também habilita a capacidade de [avaliação de pronúncia](rest-speech-to-text.md#pronunciation-assessment-parameters) para avaliar a pronúncia da fala e dá aos palestrantes os comentários sobre a precisão e a fluência de áudio falado. Com a avaliação de pronúncia, os aprendizes de linguagem podem praticar, obter comentários instantâneos e melhorar sua pronúncia para que eles possam falar e apresentar confiança. Os educadores podem usar a capacidade de avaliar a pronúncia de vários alto-falantes em tempo real. Atualmente, o recurso dá suporte a inglês-EUA e correlaciona altamente com as avaliações de fala conduzidas por especialistas.

> [!NOTE]
> Fala do Bing foi encerrado em 15 de outubro de 2019. Se seus aplicativos, ferramentas ou produtos estiverem usando as APIs de Fala do Bing, criamos guias para ajudá-lo a migrar para o serviço de fala.
> - [Migrar do Fala do Bing para o serviço de fala](how-to-migrate-from-bing-speech.md)

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

## <a name="get-started-with-speech-to-text"></a>Introdução à conversão de fala em texto

O serviço de fala a texto está disponível por meio do [SDK de fala](speech-sdk.md). Há vários cenários comuns disponíveis como guias de início rápido, em várias linguagens e plataformas:

 - [Início rápido: reconhecer a fala com a entrada do microfone](quickstarts/speech-to-text-from-microphone.md)
 - [Início Rápido: Reconhecer fala em um arquivo](quickstarts/speech-to-text-from-file.md)
 - [Início rápido: reconhecer a fala armazenada no armazenamento de BLOBs](quickstarts/from-blob.md)

Se você preferir usar o serviço REST de fala em texto, consulte [APIs REST](rest-speech-to-text.md).

 - [Início rápido: avaliação de pronúncia com entrada de referência](rest-speech-to-text.md#pronunciation-assessment-parameters)

## <a name="tutorials-and-sample-code"></a>Tutoriais e código de exemplo

Depois que tiver a oportunidade de usar o serviço de Fala, experimente nosso tutorial que ensina a reconhecer intenções de fala usando o SDK de Fala e o LUIS.

- [Tutorial: reconhecer tentativas de fala com o SDK de fala e o LUIS, usando C #](how-to-recognize-intents-from-speech-csharp.md)

O código de exemplo para o SDK de fala está disponível no GitHub. Esses exemplos abordam cenários comuns, como ler áudio de um arquivo ou fluxo, reconhecimento contínuo e único e trabalho com modelos personalizados.

- [Exemplos de conversão de fala em texto (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Exemplos de transcrição em lote (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
- [Exemplos de avaliação de pronúncia (REST)](rest-speech-to-text.md#pronunciation-assessment-parameters)

## <a name="customization"></a>Personalização

Além do modelo de serviço de fala padrão, você pode criar modelos personalizados. A personalização ajuda a superar as barreiras de reconhecimento de fala, como estilo de fala, vocabulário e ruído de fundo, consulte [fala personalizada](how-to-custom-speech.md). As opções de personalização variam por idioma/localidade, consulte [idiomas com suporte](supported-languages.md) para verificar o suporte.

## <a name="batch-transcription"></a>Transcrição de lote

A transcrição do lote é um conjunto de operações da API REST que permitem transcrever uma grande quantidade de áudio no armazenamento. Você pode apontar para arquivos de áudio com um URI de assinatura de acesso compartilhado (SAS) e receber resultados de transcrição de forma assíncrona. Consulte o [instruções](batch-transcription.md) para obter mais informações sobre como usar a API de transcrição do lote.

[!INCLUDE [speech-reference-doc-links](includes/speech-reference-doc-links.md)]

## <a name="next-steps"></a>Próximas etapas

- [Obter gratuitamente uma chave de assinatura dos Serviços de Fala](get-started.md)
- [Obter o SDK de Fala](speech-sdk.md)
