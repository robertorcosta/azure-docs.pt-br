---
title: Escolha um modo de reconhecimento de fala com o SDK de fala
titleSuffix: Azure Cognitive Services
description: Saiba como escolher o melhor modo de reconhecimento ao usar o SDK de fala.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: trbye
zone_pivot_groups: programming-languages-set-two-with-js
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 1955e29eb1bef7fe6c8f57fa5d411c5c98faf58b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91322760"
---
# <a name="choose-a-speech-recognition-mode"></a>Escolher um modo de reconhecimento de fala

Ao considerar as operações de reconhecimento de fala em texto, o [SDK de fala](speech-sdk.md) fornece vários modos de processamento de fala. Conceitualmente, às vezes chamado de *modo de reconhecimento*. Este artigo compara os vários modos de reconhecimento.

## <a name="recognize-once"></a>Reconhecer uma vez

Se você quiser processar cada expressão uma "frase" por vez, use a função "reconhecer uma vez". Esse método detectará um expressão reconhecido a partir da entrada, começando no início da fala detectada até a próxima pausa. Normalmente, uma pausa marca o final de uma frase ou linha de pensamento.

No final de um expressão reconhecido, o serviço para de processar o áudio dessa solicitação. O limite máximo de reconhecimento é uma duração de sentença de 20 segundos.

::: zone pivot="programming-language-csharp"

Para obter mais informações sobre como usar a `RecognizeOnceAsync` função, consulte o [.net Speech SDK docs](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.recognizeonceasync?view=azure-dotnet#Microsoft_CognitiveServices_Speech_SpeechRecognizer_RecognizeOnceAsync).

```csharp
var result = await recognizer.RecognizeOnceAsync();
```

::: zone-end
::: zone pivot="programming-language-cpp"

Para obter mais informações sobre como usar a `RecognizeOnceAsync` função, consulte os [documentos do SDK de fala do C++](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#recognizeonceasync).

```cpp
auto result = recognize->RecognizeOnceAsync().get();
```

::: zone-end
::: zone pivot="programming-language-java"

Para obter mais informações sobre como usar a `recognizeOnceAsync` função, consulte os [documentos do SDK de fala do Java](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.SpeechRecognizer.recognizeOnceAsync?view=azure-java-stable).

```java
SpeechRecognitionResult result = recognizer.recognizeOnceAsync().get();
```

::: zone-end
::: zone pivot="programming-language-python"

Para obter mais informações sobre como usar a `recognize_once` função, consulte os [documentos do SDK de fala do Python](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python#recognize-once------azure-cognitiveservices-speech-speechrecognitionresult).

```python
result = speech_recognizer.recognize_once()
```

::: zone-end

::: zone pivot="programming-language-javascript"

Para obter mais informações sobre como usar a `recognizeOnceAsync` função, consulte o [SDK de fala para documentos do JavaScript](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest#recognizeonceasync--e--speechrecognitionresult-----void---e--string-----void-).

```JavaScript
recognizer.recognizeOnceAsync((result)=>{}, (error)=>{}));
```

::: zone-end

::: zone pivot="programming-language-more"

Para idiomas adicionais, consulte os [documentos de referência do SDK de fala](speech-to-text.md#speech-sdk-reference-docs).

::: zone-end

## <a name="continuous"></a>Contínuo

Se você precisar de um reconhecimento de execução longa, use as funções de início e de parada correspondentes para o reconhecimento contínuo. A função start será iniciada e continuará processando todos os declarações até que você invoque a função STOP ou até que haja muito tempo no silêncio. Ao usar o modo contínuo, não se esqueça de registrar-se nos vários eventos que serão acionados na ocorrência. Por exemplo, o evento "reconhecido" é acionado quando ocorre o reconhecimento de fala. Você precisa ter um manipulador de eventos em vigor para lidar com o reconhecimento.

::: zone pivot="programming-language-csharp"

```csharp
// Subscribe to event
recognizer.Recognized += (s, e) => 
{
    if (e.Result.Reason == ResultReason.RecognizedSpeech)
    {
        // Do something with the recognized text
        // e.Result.Text
    }
};

// Start continuous speech recognition
await recognizer.StartContinuousRecognitionAsync();

// Stop continuous speech recognition
await recognizer.StopContinuousRecognitionAsync();
```

::: zone-end
::: zone pivot="programming-language-cpp"

```cpp
// Connect to event
recognizer->Recognized.Connect([] (const SpeechRecognitionEventArgs& e)
{
    if (e.Result->Reason == ResultReason::RecognizedSpeech)
    {
        // Do something with the recognized text
        // e.Result->Text
    }
});

// Start continuous speech recognition
recognizer->StartContinuousRecognitionAsync().get();

// Stop continuous speech recognition
recognizer->StopContinuousRecognitionAsync().get();
```

::: zone-end
::: zone pivot="programming-language-java"

```java
recognizer.recognized.addEventListener((s, e) -> {
    if (e.getResult().getReason() == ResultReason.RecognizedSpeech) {
        // Do something with the recognized text
        // e.getResult().getText()
    }
});

// Start continuous speech recognition
recognizer.startContinuousRecognitionAsync().get();

// Stop continuous speech recognition
recognizer.stopContinuousRecognitionAsync().get();
```

::: zone-end
::: zone pivot="programming-language-python"

```python
def recognized_cb(evt):
    if evt.result.reason == speechsdk.ResultReason.RecognizedSpeech:
        # Do something with the recognized text
        # evt.result.text

speech_recognizer.recognized.connect(recognized_cb)

# Start continuous speech recognition
speech_recognizer.start_continuous_recognition()

# Stop continuous speech recognition
speech_recognizer.stop_continuous_recognition()
```

::: zone-end

::: zone pivot="programming-language-javascript"

```JavaScript
recognizer.recognized = (s, e) => {
    if (e.result.reason == ResultReason.RecognizedSpeech) {
        // Do something with the recognized text
        // e.getResult().getText()
    }
});

// Start continuous speech recognition
recognizer.startContinuousRecognitionAsync(()=>{}, (error)=>{});

// Stop continuous speech recognition
recognizer.stopContinuousRecognitionAsync(()=>{}, (error)=>{});
```

::: zone-end

::: zone pivot="programming-language-more"

Para idiomas adicionais, consulte os [documentos de referência do SDK de fala](speech-to-text.md#speech-sdk-reference-docs).

::: zone-end

## <a name="dictation"></a>Ditado

Ao usar o reconhecimento contínuo, você pode habilitar o processamento de ditado usando a função "habilitar ditado" correspondente. Esse modo fará a instância de configuração de fala interpretar as descrições das estruturas de frase, como pontuação. Por exemplo, o enunciado "Você mora na cidade ponto de interrogação" seria interpretado como o texto "Você mora na cidade?".

::: zone pivot="programming-language-csharp"

Para obter mais informações sobre como usar a `EnableDictation` função, consulte o [.net Speech SDK docs](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.enabledictation?view=azure-dotnet#Microsoft_CognitiveServices_Speech_SpeechConfig_EnableDictation).

```csharp
// Enable diction
SpeechConfig.EnableDictation();
```

::: zone-end
::: zone pivot="programming-language-cpp"

Para obter mais informações sobre como usar a `EnableDictation` função, consulte os [documentos do SDK de fala do C++](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#enabledictation).

```cpp
// Enable diction
SpeechConfig->EnableDictation();
```

::: zone-end
::: zone pivot="programming-language-java"

Para obter mais informações sobre como usar a `enableDictation` função, consulte os [documentos do SDK de fala do Java](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.SpeechConfig.enableDictation?view=azure-java-stable).

```java
// Enable diction
SpeechConfig.enableDictation();
```

::: zone-end
::: zone pivot="programming-language-python"

Para obter mais informações sobre como usar a `enable_dictation` função, consulte os [documentos do SDK de fala do Python](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#enable-dictation--).

```python
# Enable diction
SpeechConfig.enable_dictation()
```

::: zone-end

::: zone pivot="programming-language-javascript"

Para obter mais informações sobre como usar a `enableDictation` função, consulte o [SDK de fala para documentos do JavaScript](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest#enabledictation--).

```JavaScript
// Enable diction
speechConfig.enableDictation();
```

::: zone-end

::: zone pivot="programming-language-more"

Para idiomas adicionais, consulte os [documentos de referência do SDK de fala](speech-to-text.md#speech-sdk-reference-docs).

::: zone-end

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Explore os exemplos adicionais do SDK de fala no GitHub](https://aka.ms/csspeech/samples)
