---
title: Conceitos de fluxo de entrada de áudio do SDK de Fala
titleSuffix: Azure Cognitive Services
description: Uma visão geral dos recursos da API de fluxo de entrada de áudio do SDK de Fala.
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: fmegen
ms.openlocfilehash: 3039276a49e7bb41660d114e78ca047a3f77f279
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "74109942"
---
# <a name="about-the-speech-sdk-audio-input-stream-api"></a>Sobre a API de fluxo de entrada de áudio do SDK de Fala

A API de **fluxo** de entrada de áudio do Speech SDK fornece uma maneira de transmitir áudio para os reconhecedores em vez de usar o microfone ou as APIs do arquivo de entrada.

As etapas a seguir são necessárias ao usar fluxos de entrada de áudio:

- Identificar o formato do fluxo de áudio. O formato deve ter suporte no SDK de Fala e no serviço de Fala. Atualmente, apenas a configuração a seguir tem suporte:

  Exemplos de áudio no formato PCM, um canal, 16 mil amostras por segundo, 32 mil bytes por segundo, dois alinhamentos de bloco (16 bits, incluindo preenchimento para uma amostra), 16 bits por amostra.

  O código correspondente no SDK para criar o formato de áudio tem esta aparência:

  ```csharp
  byte channels = 1;
  byte bitsPerSample = 16;
  int samplesPerSecond = 16000;
  var audioFormat = AudioStreamFormat.GetWaveFormatPCM(samplesPerSecond, bitsPerSample, channels);
  ```

- Garanta que seu código possa fornecer os dados de áudio RAW de acordo com estas especificações. Se os dados de origem do áudio não corresponderem aos formatos com suporte, o áudio deverá ser transcodificado no formato exigido.

- Crie sua própria classe de fluxo de entrada de áudio derivada de `PullAudioInputStreamCallback`. Implemente os membros `Read()` e `Close()`. A assinatura de função exata é dependente de idioma, mas o código será semelhante a este exemplo de código:

  ```csharp
   public class ContosoAudioStream : PullAudioInputStreamCallback {
      ContosoConfig config;

      public ContosoAudioStream(const ContosoConfig& config) {
          this.config = config;
      }

      public int Read(byte[] buffer, uint size) {
          // returns audio data to the caller.
          // e.g. return read(config.YYY, buffer, size);
      }

      public void Close() {
          // close and cleanup resources.
      }
   };
  ```

- Crie uma configuração de áudio com base em seu formato e fluxo de entrada de áudio. Passe sua configuração de fala normal e a configuração de entrada de áudio ao criar seu reconhecedor. Por exemplo: 

  ```csharp
  var audioConfig = AudioConfig.FromStreamInput(new ContosoAudioStream(config), audioFormat);

  var speechConfig = SpeechConfig.FromSubscription(...);
  var recognizer = new SpeechRecognizer(speechConfig, audioConfig);

  // run stream through recognizer
  var result = await recognizer.RecognizeOnceAsync();

  var text = result.GetText();
  ```

## <a name="next-steps"></a>Próximas etapas

- [Obtenha sua assinatura de avaliação de Fala](https://azure.microsoft.com/try/cognitive-services/)
- [Veja como reconhecer fala em C#](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
