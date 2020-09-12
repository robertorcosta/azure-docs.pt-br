---
title: Consultar ponto de extremidade de contêiner de conversão de texto em fala
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/31/2020
ms.author: aahi
ms.openlocfilehash: 50926074c09ef8f438b0abb53a4eb5f1813871b3
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89320998"
---
O contêiner fornece [APIs de ponto de extremidade baseadas em REST](../rest-text-to-speech.md). Há muitos [projetos de código-fonte de exemplo](https://github.com/Azure-Samples/Cognitive-Speech-TTS) para a plataforma, estrutura e variações de linguagem disponíveis.

Com os contêineres de conversão de texto em fala padrão ou neural, você deve contar com a localidade e a voz da marca de imagem baixada. Por exemplo, se você baixou a `latest` marca, a localidade padrão é `en-US` e a `AriaRUS` voz. `{VOICE_NAME}`Em seguida, o argumento seria [`en-US-AriaRUS`](../language-support.md#standard-voices) . Consulte o exemplo SSML abaixo:

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

No entanto, para *texto personalizado para fala* , você precisará obter a **voz/modelo** do [portal de voz personalizado](https://aka.ms/custom-voice-portal). O nome do modelo personalizado é sinônimo do nome da voz. Navegue até a página de **treinamento** e copie a **voz/modelo** para usar como o `{VOICE_NAME}` argumento.
<br><br>
:::image type="content" source="../media/custom-voice/custom-voice-model-voice-name.png" alt-text="Modelo de voz personalizado-nome da voz":::

Consulte o exemplo SSML abaixo:

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="custom-voice-model">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

Vamos construir uma solicitação HTTP POST, fornecendo alguns cabeçalhos e uma carga de dados. Substitua o `{VOICE_NAME}` espaço reservado pelo seu próprio valor.

```curl
curl -s -v -X POST http://localhost:5000/speech/synthesize/cognitiveservices/v1 \
 -H 'Accept: audio/*' \
 -H 'Content-Type: application/ssml+xml' \
 -H 'X-Microsoft-OutputFormat: riff-16khz-16bit-mono-pcm' \
 -d '<speak version="1.0" xml:lang="en-US"><voice name="{VOICE_NAME}">This is a test, only a test.</voice></speak>'
```

Esse comando:

* Constrói uma solicitação HTTP POST para o `speech/synthesize/cognitiveservices/v1` ponto de extremidade.
* Especifica um `Accept` cabeçalho de `audio/*`
* Especifica um `Content-Type` cabeçalho de `application/ssml+xml` , para obter mais informações, consulte [corpo da solicitação](../rest-text-to-speech.md#request-body).
* Especifica um `X-Microsoft-OutputFormat` cabeçalho de `riff-16khz-16bit-mono-pcm` , para obter mais opções, consulte [saída de áudio](../rest-text-to-speech.md#audio-outputs).
* Envia a solicitação de [linguagem de marcação de síntese de fala (SSML)](../speech-synthesis-markup.md) de acordo `{VOICE_NAME}` com o ponto de extremidade.