---
title: Texto-para-fala - Serviço de fala
titleSuffix: Azure Cognitive Services
description: O recurso texto-para-fala no serviço Speech permite que seus aplicativos, ferramentas ou dispositivos convertam texto em uma fala sintetizada natural como humana. Escolha vozes predefinidas ou crie sua própria voz personalizada.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: trbye
ms.openlocfilehash: 1a8b458eb004b44d0045f36b18d88e11e019c4d2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399496"
---
# <a name="what-is-text-to-speech"></a>O que é conversão de texto em fala?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

O serviço Text-to-speech do serviço Speech permite que seus aplicativos, ferramentas ou dispositivos convertam texto em discurso sintetizado por humanos. Escolha entre vozes padrão e neurais ou crie uma voz personalizada exclusiva para o seu produto ou marca. Mais de 75 vozes padrão estão disponíveis em mais de 45 idiomas e locais, e 5 vozes neurais estão disponíveis em um número seleto de idiomas e locais. Para obter uma lista completa de vozes, idiomas e locais suportados, consulte [idiomas suportados.](language-support.md#text-to-speech)

> [!NOTE]
> Bing Speech foi desativado em 15 de outubro de 2019. Se seus aplicativos, ferramentas ou produtos estiverem usando as APIs de bing speech ou Custom Speech, criamos guias para ajudá-lo a migrar para o serviço Speech.
> - [Migrar do Bing Speech para o serviço de fala](how-to-migrate-from-bing-speech.md)

## <a name="core-features"></a>Características principais

* Síntese da fala - Use o [Speech SDK](quickstarts/text-to-speech-audio-file.md) ou [rest API](rest-text-to-speech.md) para converter texto-a-fala usando vozes padrão, neurais ou personalizadas.

* Síntese assíncrona de áudio longo - Use a [API de áudio longo](long-audio-api.md) para sintetizar os arquivos texto-a-fala de forma sincronizada por mais de 10 minutos (por exemplo, livros de áudio ou palestras). Ao contrário da síntese realizada usando o Speech SDK ou a API REST de fala para texto, as respostas não são retornadas em tempo real. A expectativa é que as solicitações sejam enviadas de forma assíncrona, que as respostas sejam pesquisadas e que o áudio sintetizado seja baixado quando disponibilizado no serviço. Apenas vozes neurais personalizadas são suportadas.

* Vozes padrão - Criadas utilizando técnicas de Síntese Paramétrica Estatística e/ou Síntese de Concatenação. Essas vozes são altamente inteligíveis e soam naturais. Você pode facilmente habilitar seus aplicativos para falar em mais de 45 idiomas, com uma ampla gama de opções de voz. Essas vozes fornecem alta precisão de pronúncia, incluindo suporte para abreviaturas, expansões de sigla, interpretações de data/hora, polifones e muito mais. Para obter uma lista completa de vozes padrão, consulte [idiomas suportados](language-support.md#text-to-speech).

* Vozes neurais - Redes neurais profundas são usadas para superar os limites da síntese da fala tradicional no que diz respeito ao estresse e entonação na linguagem falada. A previsão de prosódia e a síntese de voz são realizadas simultaneamente, o que resulta em saídas mais fluidas e naturais. Vozes neurais podem ser usadas para tornar as interações com chatbots e assistentes de voz mais naturais e envolventes, converter textos digitais, como e-books, em audiobooks e melhorar os sistemas de navegação no carro. Com a prosódia natural humana e a clara articulação das palavras, as vozes neurais reduzem significativamente a fadiga auditiva quando você interage com sistemas de IA. Para obter uma lista completa de vozes neurais, consulte [as línguas suportadas.](language-support.md#text-to-speech)

* Linguagem de marcação de síntese de fala (SSML) - Uma linguagem de marcação baseada em XML usada para personalizar saídas de voz para texto. Com o SSML, você pode ajustar o tom, adicionar pausas, melhorar a pronúncia, acelerar ou diminuir a taxa de fala, aumentar ou diminuir o volume e atribuir várias vozes a um único documento. Ver [SSML](speech-synthesis-markup.md).

## <a name="get-started"></a>Introdução

O serviço texto-para-fala está disponível através do [Speech SDK](speech-sdk.md). Existem vários cenários comuns disponíveis como quickstarts, em vários idiomas e plataformas:

* [Sintetizar fala em um arquivo de áudio](quickstarts/text-to-speech-audio-file.md)
* [Sintetizar fala para um locutor](quickstarts/text-to-speech.md)
* [Sincronicamente sintetizar áudio de forma longa](quickstarts/text-to-speech/async-synthesis-long-form-audio.md)

Se preferir, o serviço texto-para-fala é acessível via [REST](rest-text-to-speech.md).

## <a name="sample-code"></a>Código de exemplo

O código de exemplo para texto-para-fala está disponível no GitHub. Essas amostras cobrem a conversão de texto para fala nas linguagens de programação mais populares.

- [Amostras de texto para fala (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Exemplos de conversão de texto em fala (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="customization"></a>Personalização

Além das vozes padrão e neurais, você pode criar e afinar vozes personalizadas exclusivas ao seu produto ou marca. Tudo o que é preciso para começar são um punhado de arquivos de áudio e as transcrições associadas. Para obter mais informações, consulte [Comece com a Voz Personalizada](how-to-custom-voice.md)

## <a name="pricing-note"></a>Nota de preços

Ao usar o serviço texto-para-fala, você é cobrado por cada caractere que é convertido em fala, incluindo pontuação. Embora o documento SSML em si não seja faturado, elementos opcionais que são usados para ajustar a forma como o texto é convertido em fala, como fonemas e pitch, são contados como caracteres faturados. Aqui está uma lista do que é cobrado:

- Texto aprovado para o serviço de texto-para-fala no órgão SSML da solicitação
- Toda a marcação no campo de texto do órgão `<speak>` de `<voice>` solicitação no formato SSML, exceto e tags
- Letras, pontuação, espaços, guias, marcação e todos os caracteres do espaço branco
- Cada ponto de código definido no Unicode

Para obter informações detalhadas, consulte [Preços](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

> [!IMPORTANT]
> Cada personagem chinês, japonês e coreano é contado como dois caracteres para faturamento.

## <a name="reference-docs"></a>Documentos de Referência

- [SDK da fala](speech-sdk.md)
- [API REST: conversão de texto em fala](rest-text-to-speech.md)

## <a name="next-steps"></a>Próximas etapas

- [Obtenha uma assinatura gratuita do serviço de fala](get-started.md)
- [Obter o SDK de Fala](speech-sdk.md)
