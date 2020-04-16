---
title: Como usar a detecção automática de linguagem para a fala para texto
titleSuffix: Azure Cognitive Services
description: O Speech SDK suporta detecção automática de linguagem para fala a texto. Ao usar esse recurso, o áudio fornecido é comparado com uma lista fornecida de idiomas, e a correspondência mais provável é determinada. O valor retornado pode então ser usado para selecionar o modelo de idioma usado para fala a texto.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: trbye
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: fefbe793fa4a6b90ba9bf8d468d42dcbd315759c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81402198"
---
# <a name="automatic-language-detection-for-speech-to-text"></a>Detecção automática de linguagem para fala a texto

A detecção automática de idiomas é usada para determinar a correspondência mais provável para áudio passado para o Speech SDK quando comparada com uma lista de idiomas fornecidos. O valor retornado pela detecção automática de idiomas é então usado para selecionar o modelo de idioma para fala a texto, fornecendo-lhe uma transcrição mais precisa. Para ver quais idiomas estão disponíveis, consulte [suporte ao idioma](language-support.md).

Neste artigo, você aprenderá a `AutoDetectSourceLanguageConfig` usar `SpeechRecognizer` para construir um objeto e recuperar a linguagem detectada.

> [!IMPORTANT]
> Este recurso só está disponível para o Speech SDK para C#, C++, Java e Python.

## <a name="automatic-language-detection-with-the-speech-sdk"></a>Detecção automática de linguagem com o SDK de fala

Atualmente, a detecção automática de idiomas tem um limite de dois idiomas por detecção. Tenha essa limitação em `AudoDetectSourceLanguageConfig` mente ao construir seu objeto. Nas amostras abaixo, você criará um `AutoDetectSourceLanguageConfig`, em `SpeechRecognizer`seguida, usá-lo para construir um .

> [!TIP]
> Você também pode especificar um modelo personalizado para usar ao executar a fala para o texto. Para obter mais informações, consulte [Use um modelo personalizado para detecção automática de linguagem](#use-a-custom-model-for-automatic-language-detection).

Os seguintes trechos ilustram como usar a detecção automática de linguagem em seus aplicativos:

::: zone pivot="programming-language-csharp"

```csharp
var autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.FromLanguages(
        new string[] { "en-US", "de-DE" });

using (var recognizer = new SpeechRecognizer(
    speechConfig,
    autoDetectSourceLanguageConfig,
    audioConfig))
{
    var speechRecognitionResult = await recognizer.RecognizeOnceAsync();
    var autoDetectSourceLanguageResult =
        AutoDetectSourceLanguageResult.FromResult(speechRecognitionResult);
    var detectedLanguage = autoDetectSourceLanguageResult.Language;
}
```

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
auto autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig::FromLanguages({ "en-US", "de-DE" });

auto recognizer = SpeechRecognizer::FromConfig(
    speechConfig,
    autoDetectSourceLanguageConfig,
    audioConfig);

speechRecognitionResult = recognizer->RecognizeOnceAsync().get();
auto autoDetectSourceLanguageResult =
    AutoDetectSourceLanguageResult::FromResult(speechRecognitionResult);
auto detectedLanguage = autoDetectSourceLanguageResult->Language;
```

::: zone-end

::: zone pivot="programming-language-java"

```java
AutoDetectSourceLanguageConfig autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.fromLanguages(Arrays.asList("en-US", "de-DE"));

SpeechRecognizer recognizer = new SpeechRecognizer(
    speechConfig,
    autoDetectSourceLanguageConfig,
    audioConfig);

Future<SpeechRecognitionResult> future = recognizer.recognizeOnceAsync();
SpeechRecognitionResult result = future.get(30, TimeUnit.SECONDS);
AutoDetectSourceLanguageResult autoDetectSourceLanguageResult =
    AutoDetectSourceLanguageResult.fromResult(result);
String detectedLanguage = autoDetectSourceLanguageResult.getLanguage();

recognizer.close();
speechConfig.close();
autoDetectSourceLanguageConfig.close();
audioConfig.close();
result.close();
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
auto_detect_source_language_config = \
        speechsdk.languageconfig.AutoDetectSourceLanguageConfig(languages=["en-US", "de-DE"])
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, 
        auto_detect_source_language_config=auto_detect_source_language_config, 
        audio_config=audio_config)
result = speech_recognizer.recognize_once()
auto_detect_source_language_result = speechsdk.AutoDetectSourceLanguageResult(result)
detected_language = auto_detect_source_language_result.language
```

::: zone-end

## <a name="use-a-custom-model-for-automatic-language-detection"></a>Use um modelo personalizado para detecção automática de linguagem

Além da detecção de linguagem usando modelos de serviço de fala, você pode especificar um modelo personalizado para reconhecimento aprimorado. Se um modelo personalizado não for fornecido, o serviço usará o modelo de idioma padrão.

Os trechos abaixo ilustram como especificar um modelo personalizado em sua chamada para o serviço Speech. Se a linguagem `en-US`detectada for, o modelo padrão será usado. Se a linguagem `fr-FR`detectada for, então o ponto final para o modelo personalizado será usado:

::: zone pivot="programming-language-csharp"

```csharp
var sourceLanguageConfigs = new SourceLanguageConfig[]
{
    SourceLanguageConfig.FromLanguage("en-US"),
    SourceLanguageConfig.FromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR")
};
var autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.FromSourceLanguageConfigs(
        sourceLanguageConfigs);
```

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
std::vector<std::shared_ptr<SourceLanguageConfig>> sourceLanguageConfigs;
sourceLanguageConfigs.push_back(
    SourceLanguageConfig::FromLanguage("en-US"));
sourceLanguageConfigs.push_back(
    SourceLanguageConfig::FromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR"));

auto autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig::FromSourceLanguageConfigs(
        sourceLanguageConfigs);
```

::: zone-end

::: zone pivot="programming-language-java"

```java
List sourceLanguageConfigs = new ArrayList<SourceLanguageConfig>();
sourceLanguageConfigs.add(
    SourceLanguageConfig.fromLanguage("en-US"));
sourceLanguageConfigs.add(
    SourceLanguageConfig.fromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR"));

AutoDetectSourceLanguageConfig autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.fromSourceLanguageConfigs(
        sourceLanguageConfigs);
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
 en_language_config = speechsdk.languageconfig.SourceLanguageConfig("en-US")
 fr_language_config = speechsdk.languageconfig.SourceLanguageConfig("fr-FR", "The Endpoint Id for custom model of fr-FR")
 auto_detect_source_language_config = speechsdk.languageconfig.AutoDetectSourceLanguageConfig(
        sourceLanguageConfigs=[en_language_config, fr_language_config])
```

::: zone-end

## <a name="next-steps"></a>Próximas etapas

- [Documentação de referência do Speech SDK](speech-sdk.md)
