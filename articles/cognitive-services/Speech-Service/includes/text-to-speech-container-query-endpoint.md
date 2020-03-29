---
title: Ponto final do contêiner de consulta texto-para-fala
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 8460ddca5cff2b3da540b5fa8cf66e0687892789
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "73491172"
---
O contêiner fornece [APIs de ponto final baseadas em REST](../rest-text-to-speech.md). Existem muitos [projetos de código fonte de amostra](https://azure.microsoft.com/resources/samples/cognitive-speech-tts/) para variações de plataforma, framework e idioma disponíveis.

Com o contêiner *Texto-para-voz Padrão,* você deve confiar na localização e na voz da tag de imagem que você baixou. Por exemplo, se você `latest` baixou a `en-US` tag, `JessaRUS` o local padrão é e a voz. O `{VOICE_NAME}` argumento seria [`en-US-JessaRUS`](../language-support.md#standard-voices)então. Veja o exemplo do SSML abaixo:

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="en-US-JessaRUS">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

No entanto, para *o texto-para-voz personalizado* você precisará obter a **voz / modelo** a partir do portal de [voz personalizado](https://aka.ms/custom-voice-portal). O nome do modelo personalizado é sinônimo de nome de voz. Navegue até a página **Treinamento** e copie a `{VOICE_NAME}` **voz/modelo** para usar como argumento.
<br><br>
:::image type="content" source="../media/custom-voice/custom-voice-model-voice-name.png" alt-text="Modelo de voz personalizado - nome de voz":::

Veja o exemplo do SSML abaixo:

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="custom-voice-model">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

Vamos construir uma solicitação HTTP POST, fornecendo alguns cabeçalhos e uma carga de dados. Substitua `{VOICE_NAME}` o espaço reservado pelo seu próprio valor.

```curl
curl -s -v -X POST http://localhost:5000/speech/synthesize/cognitiveservices/v1 \
 -H 'Accept: audio/*' \
 -H 'Content-Type: application/ssml+xml' \
 -H 'X-Microsoft-OutputFormat: riff-16khz-16bit-mono-pcm' \
 -d '<speak version="1.0" xml:lang="en-US"><voice name="{VOICE_NAME}">This is a test, only a test.</voice></speak>'
```

Esse comando:

* Constrói uma solicitação HTTP `speech/synthesize/cognitiveservices/v1` POST para o ponto final.
* Especifica um `Accept` cabeçalho de`audio/*`
* Especifica um `Content-Type` cabeçalho de `application/ssml+xml`, para obter mais informações, consulte o corpo de [solicitação](../rest-text-to-speech.md#request-body).
* Especifica um `X-Microsoft-OutputFormat` cabeçalho de `riff-16khz-16bit-mono-pcm`, para mais opções ver saída de [áudio](../rest-text-to-speech.md#audio-outputs).
* Envia a solicitação [ssml (Speech Synthesis Markup Language,](../speech-synthesis-markup.md) linguagem de marcação de síntese de fala) dada `{VOICE_NAME}` a até o ponto final.