---
title: Escolha um modo de reconhecimento de voz com o Speech SDK
titleSuffix: Azure Cognitive Services
description: Saiba como escolher o melhor modo de reconhecimento ao usar o Speech SDK.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: trbye
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 5fdca371e9188ef69068ddbcaa416cbb2b44054c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81402147"
---
# <a name="choose-a-speech-recognition-mode"></a>Escolha um modo de reconhecimento de voz

Ao considerar as operações de reconhecimento de fala a texto, o [Speech SDK](speech-sdk.md) fornece vários modos para processar a fala. Conceitualmente, às vezes chamado de *modo de reconhecimento.* Este artigo compara os vários modos de reconhecimento.

## <a name="recognize-once"></a>Reconheça uma vez

Se você quiser processar cada expressão uma "frase" de cada vez, use a função "reconhecer uma vez". Este método detectará uma expressão reconhecida a partir da entrada a partir do início da fala detectada até a próxima pausa. Normalmente, uma pausa marca o fim de uma frase ou linha de pensamento.

Ao final de uma declaração reconhecida, o serviço deixa de processar áudio a partir dessa solicitação. O limite máximo para reconhecimento é uma duração de sentença de 20 segundos.

::: zone pivot="programming-language-csharp"

Para obter mais `RecognizeOnceAsync` informações sobre o uso da função, consulte os [docs .NET Speech SDK](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.recognizeonceasync?view=azure-dotnet#Microsoft_CognitiveServices_Speech_SpeechRecognizer_RecognizeOnceAsync).

```csharp
var result = await recognizer.RecognizeOnceAsync();
```

::: zone-end
::: zone pivot="programming-language-cpp"

Para obter mais `RecognizeOnceAsync` informações sobre o uso da função, consulte os [docs C++ Speech SDK](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#recognizeonceasync).

```cpp
auto result = recognize->RecognizeOnceAsync().get();
```

::: zone-end
::: zone pivot="programming-language-java"

Para obter mais `recognizeOnceAsync` informações sobre o uso da função, consulte os [docs Java Speech SDK](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.SpeechRecognizer.recognizeOnceAsync?view=azure-java-stable).

```java
SpeechRecognitionResult result = recognizer.recognizeOnceAsync().get();
```

::: zone-end
::: zone pivot="programming-language-python"

Para obter mais `recognize_once` informações sobre o uso da função, consulte os [docs Python Speech SDK](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python#recognize-once------azure-cognitiveservices-speech-speechrecognitionresult).

```python
result = speech_recognizer.recognize_once()
```

::: zone-end
::: zone pivot="programming-language-more"

Para obter idiomas adicionais, consulte os [docs de referência do Speech SDK](speech-to-text.md#speech-sdk-reference-docs).

::: zone-end

## <a name="continuous"></a>Contínuo

Se você precisar de reconhecimento de longa duração, use as funções de início e de parada correspondentes para reconhecimento contínuo. A função de início iniciará e continuará processando todas as expressões até que você invoque a função stop, ou até que muito tempo em silêncio tenha passado. Ao utilizar o modo contínuo, certifique-se de registrar os vários eventos que serão acionados após a ocorrência. Por exemplo, o evento "reconhecido" é acionado quando ocorre o reconhecimento de fala. Você precisa ter um manipulador de eventos no local para lidar com o reconhecimento.

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
::: zone pivot="programming-language-more"

Para obter idiomas adicionais, consulte os [docs de referência do Speech SDK](speech-to-text.md#speech-sdk-reference-docs).

::: zone-end

## <a name="dictation"></a>Ditado

Ao usar o reconhecimento contínuo, você pode habilitar o processamento de ditado usando a função "habilitar ditado" correspondente. Esse modo fará a instância de configuração de fala interpretar as descrições das estruturas de frase, como pontuação. Por exemplo, o enunciado "Você mora na cidade ponto de interrogação" seria interpretado como o texto "Você mora na cidade?".

::: zone pivot="programming-language-csharp"

Para obter mais `EnableDictation` informações sobre o uso da função, consulte os [docs .NET Speech SDK](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.enabledictation?view=azure-dotnet#Microsoft_CognitiveServices_Speech_SpeechConfig_EnableDictation).

```csharp
// Enable diction
SpeechConfig.EnableDictation();
```

::: zone-end
::: zone pivot="programming-language-cpp"

Para obter mais `EnableDictation` informações sobre o uso da função, consulte os [docs C++ Speech SDK](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#enabledictation).

```cpp
// Enable diction
SpeechConfig->EnableDictation();
```

::: zone-end
::: zone pivot="programming-language-java"

Para obter mais `enableDictation` informações sobre o uso da função, consulte os [docs Java Speech SDK](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.SpeechConfig.enableDictation?view=azure-java-stable).

```java
// Enable diction
SpeechConfig.enableDictation();
```

::: zone-end
::: zone pivot="programming-language-python"

Para obter mais `enable_dictation` informações sobre o uso da função, consulte os [docs Python Speech SDK](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#enable-dictation--).

```python
# Enable diction
SpeechConfig.enable_dictation()
```

::: zone-end
::: zone pivot="programming-language-more"

Para obter idiomas adicionais, consulte os [docs de referência do Speech SDK](speech-to-text.md#speech-sdk-reference-docs).

::: zone-end

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Explorar amostras adicionais de SDK de fala no GitHub](https://aka.ms/csspeech/samples)
