---
title: Como especificar a linguagem de origem para a fala para texto
titleSuffix: Azure Cognitive Services
description: O Speech SDK permite que você especifique o idioma de origem ao converter a fala em texto. Este artigo descreve como usar os métodos FromConfig e SourceLanguageConfig para informar o serviço speech know the source language e fornecer um alvo de modelo personalizado.
services: cognitive-services
author: susanhu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/07/2020
ms.author: qiohu
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: f0723534d9d2187593cb73f058ffea62473b80a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235956"
---
# <a name="specify-source-language-for-speech-to-text"></a>Especifique o idioma de origem para a fala para o texto

Neste artigo, você aprenderá a especificar o idioma de origem de uma entrada de áudio passada para o Speech SDK para reconhecimento de fala. Além disso, o código de exemplo é fornecido para especificar um modelo de voz personalizado para um reconhecimento melhorado.

::: zone pivot="programming-language-csharp"

## <a name="how-to-specify-source-language-in-c"></a>Como especificar o idioma de origem em C #

Neste exemplo, a linguagem de origem é fornecida `SpeechRecognizer` explicitamente como um parâmetro usando construção.

```csharp
var recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

Neste exemplo, a linguagem de `SourceLanguageConfig`origem é fornecida usando . Então, `sourceLanguageConfig` o é passado como `SpeechRecognizer` um parâmetro para construir.

```csharp
var sourceLanguageConfig = SourceLanguageConfig.FromLanguage("de-DE");
var recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

Neste exemplo, o idioma de origem e `SourceLanguageConfig`o ponto final personalizado são fornecidos usando . Então, `sourceLanguageConfig` o é passado como `SpeechRecognizer` um parâmetro para construir.

```csharp
var sourceLanguageConfig = SourceLanguageConfig.FromLanguage("de-DE", "The Endpoint ID for your custom model.");
var recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `SpeechRecognitionLanguage`e `EndpointId` os métodos definidos são `SpeechConfig` preteridos da classe em C#. O uso desses métodos é desencorajado, e não deve `SpeechRecognizer`ser usado na construção de um .

::: zone-end

::: zone pivot="programming-language-cpp"


## <a name="how-to-specify-source-language-in-c"></a>Como especificar o idioma de origem no C++

Neste exemplo, a linguagem de origem é fornecida explicitamente `FromConfig` como um parâmetro usando o método.

```C++
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, "de-DE", audioConfig);
```

Neste exemplo, a linguagem de `SourceLanguageConfig`origem é fornecida usando . Em seguida, o `sourceLanguageConfig` é passado `FromConfig` como `recognizer`um parâmetro para quando se cria o .

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

Neste exemplo, o idioma de origem e `SourceLanguageConfig`o ponto final personalizado são fornecidos usando . O `sourceLanguageConfig` é passado como `FromConfig` um parâmetro `recognizer`para quando se cria o .

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE", "The Endpoint ID for your custom model.");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `SetSpeechRecognitionLanguage`e `SetEndpointId` são métodos depreciados `SpeechConfig` da classe em C++ e Java. O uso desses métodos é desencorajado, e não deve `SpeechRecognizer`ser usado na construção de um .

::: zone-end

::: zone pivot="programming-language-java"

## <a name="how-to-specify-source-language-in-java"></a>Como especificar a linguagem de origem em Java

Neste exemplo, a linguagem de origem é fornecida `SpeechRecognizer`explicitamente ao criar um novo .

```Java
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

Neste exemplo, a linguagem de `SourceLanguageConfig`origem é fornecida usando . Em seguida, o `sourceLanguageConfig` é passado como `SpeechRecognizer`um parâmetro ao criar um novo .

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

Neste exemplo, o idioma de origem e `SourceLanguageConfig`o ponto final personalizado são fornecidos usando . Em seguida, o `sourceLanguageConfig` é passado como `SpeechRecognizer`um parâmetro ao criar um novo .

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE", "The Endpoint ID for your custom model.");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `setSpeechRecognitionLanguage`e `setEndpointId` são métodos depreciados `SpeechConfig` da classe em C++ e Java. O uso desses métodos é desencorajado, e não deve `SpeechRecognizer`ser usado na construção de um .

::: zone-end

::: zone pivot="programming-language-python"

## <a name="how-to-specify-source-language-in-python"></a>Como especificar a linguagem de origem no Python

Neste exemplo, a linguagem de origem é fornecida `SpeechRecognizer` explicitamente como um parâmetro usando construção.

```Python
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, language="de-DE", audio_config=audio_config)
```

Neste exemplo, a linguagem de `SourceLanguageConfig`origem é fornecida usando . Então, `SourceLanguageConfig` o é passado como `SpeechRecognizer` um parâmetro para construir.

```Python
source_language_config = speechsdk.languageconfig.SourceLanguageConfig("de-DE")
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, source_language_config=source_language_config, audio_config=audio_config)
```

Neste exemplo, o idioma de origem e `SourceLanguageConfig`o ponto final personalizado são fornecidos usando . Então, `SourceLanguageConfig` o é passado como `SpeechRecognizer` um parâmetro para construir.

```Python
source_language_config = speechsdk.languageconfig.SourceLanguageConfig("de-DE", "The Endpoint ID for your custom model.")
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, source_language_config=source_language_config, audio_config=audio_config)
```

>[!Note]
> `speech_recognition_language`e `endpoint_id` as propriedades são preteridas da `SpeechConfig` classe em Python. O uso dessas propriedades é desencorajado, e não deve `SpeechRecognizer`ser usado na construção de um .

::: zone-end

::: zone pivot="programming-language-more"

## <a name="how-to-specify-source-language-in-javascript"></a>Como especificar a linguagem de origem em Javascript

O primeiro passo é `SpeechConfig`criar um:

```Javascript
var speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionkey", "YourRegion");
```

Em seguida, especifique `speechRecognitionLanguage`o idioma de origem do seu áudio com:

```Javascript
speechConfig.speechRecognitionLanguage = "de-DE";
```

Se você estiver usando um modelo personalizado para reconhecimento, `endpointId`você pode especificar o ponto final com:

```Javascript
speechConfig.endpointId = "The Endpoint ID for your custom model.";
```

## <a name="how-to-specify-source-language-in-objective-c"></a>Como especificar o idioma de origem no Objective-C

O primeiro passo é `speechConfig`criar um:

```Objective-C
SPXSpeechConfiguration *speechConfig = [[SPXSpeechConfiguration alloc] initWithSubscription:@"YourSubscriptionkey" region:@"YourRegion"];
```

Em seguida, especifique `speechRecognitionLanguage`o idioma de origem do seu áudio com:

```Objective-C
speechConfig.speechRecognitionLanguage = @"de-DE";
```

Se você estiver usando um modelo personalizado para reconhecimento, `endpointId`você pode especificar o ponto final com:

```Objective-C
speechConfig.endpointId = @"The Endpoint ID for your custom model.";
```

::: zone-end

## <a name="see-also"></a>Confira também

* Para obter uma lista de idiomas e locais suportados para falar a texto, consulte [suporte ao idioma](language-support.md).

## <a name="next-steps"></a>Próximas etapas

* [Documentação de referência do Speech SDK](speech-sdk.md)
