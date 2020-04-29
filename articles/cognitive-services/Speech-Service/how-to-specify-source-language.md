---
title: Como especificar o idioma de origem de fala para texto
titleSuffix: Azure Cognitive Services
description: O SDK de fala permite que você especifique o idioma de origem ao converter a fala em texto. Este artigo descreve como usar os métodos FromConfig e SourceLanguageConfig para permitir que o serviço de fala saiba o idioma de origem e forneça um destino de modelo personalizado.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "80235956"
---
# <a name="specify-source-language-for-speech-to-text"></a>Especificar o idioma de origem de fala para texto

Neste artigo, você aprenderá a especificar o idioma de origem para uma entrada de áudio passada para o SDK de fala para reconhecimento de fala. Além disso, o código de exemplo é fornecido para especificar um modelo de fala personalizado para um reconhecimento aprimorado.

::: zone pivot="programming-language-csharp"

## <a name="how-to-specify-source-language-in-c"></a>Como especificar o idioma de origem em C #

Neste exemplo, o idioma de origem é fornecido explicitamente como um parâmetro usando `SpeechRecognizer` Construct.

```csharp
var recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

Neste exemplo, o idioma de origem é fornecido usando `SourceLanguageConfig`. Em seguida, `sourceLanguageConfig` o é passado como um parâmetro `SpeechRecognizer` a ser construído.

```csharp
var sourceLanguageConfig = SourceLanguageConfig.FromLanguage("de-DE");
var recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

Neste exemplo, o idioma de origem e o ponto de extremidade personalizado `SourceLanguageConfig`são fornecidos usando. Em seguida, `sourceLanguageConfig` o é passado como um parâmetro `SpeechRecognizer` a ser construído.

```csharp
var sourceLanguageConfig = SourceLanguageConfig.FromLanguage("de-DE", "The Endpoint ID for your custom model.");
var recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `SpeechRecognitionLanguage`e `EndpointId` os `SpeechConfig` métodos set são preteridos da classe em C#. O uso desses métodos não é recomendado e não deve ser usado durante a construção de um `SpeechRecognizer`.

::: zone-end

::: zone pivot="programming-language-cpp"


## <a name="how-to-specify-source-language-in-c"></a>Como especificar o idioma de origem em C++

Neste exemplo, o idioma de origem é fornecido explicitamente como um parâmetro usando o `FromConfig` método.

```C++
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, "de-DE", audioConfig);
```

Neste exemplo, o idioma de origem é fornecido usando `SourceLanguageConfig`. Em seguida, `sourceLanguageConfig` o é passado como um parâmetro `FromConfig` para ao criar `recognizer`o.

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

Neste exemplo, o idioma de origem e o ponto de extremidade personalizado `SourceLanguageConfig`são fornecidos usando. O `sourceLanguageConfig` é passado como um parâmetro para `FromConfig` ao criar o `recognizer`.

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE", "The Endpoint ID for your custom model.");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `SetSpeechRecognitionLanguage`e `SetEndpointId` são métodos preteridos da `SpeechConfig` classe em C++ e Java. O uso desses métodos não é recomendado e não deve ser usado durante a construção de um `SpeechRecognizer`.

::: zone-end

::: zone pivot="programming-language-java"

## <a name="how-to-specify-source-language-in-java"></a>Como especificar o idioma de origem em Java

Neste exemplo, o idioma de origem é fornecido explicitamente ao criar um novo `SpeechRecognizer`.

```Java
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

Neste exemplo, o idioma de origem é fornecido usando `SourceLanguageConfig`. Em seguida, `sourceLanguageConfig` o é passado como um parâmetro ao criar um `SpeechRecognizer`novo.

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

Neste exemplo, o idioma de origem e o ponto de extremidade personalizado `SourceLanguageConfig`são fornecidos usando. Em seguida, `sourceLanguageConfig` o é passado como um parâmetro ao criar um `SpeechRecognizer`novo.

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE", "The Endpoint ID for your custom model.");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `setSpeechRecognitionLanguage`e `setEndpointId` são métodos preteridos da `SpeechConfig` classe em C++ e Java. O uso desses métodos não é recomendado e não deve ser usado durante a construção de um `SpeechRecognizer`.

::: zone-end

::: zone pivot="programming-language-python"

## <a name="how-to-specify-source-language-in-python"></a>Como especificar o idioma de origem no Python

Neste exemplo, o idioma de origem é fornecido explicitamente como um parâmetro usando `SpeechRecognizer` Construct.

```Python
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, language="de-DE", audio_config=audio_config)
```

Neste exemplo, o idioma de origem é fornecido usando `SourceLanguageConfig`. Em seguida, `SourceLanguageConfig` o é passado como um parâmetro `SpeechRecognizer` a ser construído.

```Python
source_language_config = speechsdk.languageconfig.SourceLanguageConfig("de-DE")
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, source_language_config=source_language_config, audio_config=audio_config)
```

Neste exemplo, o idioma de origem e o ponto de extremidade personalizado `SourceLanguageConfig`são fornecidos usando. Em seguida, `SourceLanguageConfig` o é passado como um parâmetro `SpeechRecognizer` a ser construído.

```Python
source_language_config = speechsdk.languageconfig.SourceLanguageConfig("de-DE", "The Endpoint ID for your custom model.")
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, source_language_config=source_language_config, audio_config=audio_config)
```

>[!Note]
> `speech_recognition_language`as `endpoint_id` Propriedades e são preteridas `SpeechConfig` da classe no Python. O uso dessas propriedades não é recomendado e não deve ser usado ao construir um `SpeechRecognizer`.

::: zone-end

::: zone pivot="programming-language-more"

## <a name="how-to-specify-source-language-in-javascript"></a>Como especificar o idioma de origem em JavaScript

A primeira etapa é criar um `SpeechConfig`:

```Javascript
var speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionkey", "YourRegion");
```

Em seguida, especifique o idioma de origem do seu `speechRecognitionLanguage`áudio com:

```Javascript
speechConfig.speechRecognitionLanguage = "de-DE";
```

Se você estiver usando um modelo personalizado para reconhecimento, poderá especificar o ponto de extremidade `endpointId`com:

```Javascript
speechConfig.endpointId = "The Endpoint ID for your custom model.";
```

## <a name="how-to-specify-source-language-in-objective-c"></a>Como especificar o idioma de origem no Objective-C

A primeira etapa é criar um `speechConfig`:

```Objective-C
SPXSpeechConfiguration *speechConfig = [[SPXSpeechConfiguration alloc] initWithSubscription:@"YourSubscriptionkey" region:@"YourRegion"];
```

Em seguida, especifique o idioma de origem do seu `speechRecognitionLanguage`áudio com:

```Objective-C
speechConfig.speechRecognitionLanguage = @"de-DE";
```

Se você estiver usando um modelo personalizado para reconhecimento, poderá especificar o ponto de extremidade `endpointId`com:

```Objective-C
speechConfig.endpointId = @"The Endpoint ID for your custom model.";
```

::: zone-end

## <a name="see-also"></a>Confira também

* Para obter uma lista de idiomas com suporte e localidades de fala para texto, consulte [suporte a idiomas](language-support.md).

## <a name="next-steps"></a>Próximas etapas

* [Documentação de referência do SDK de fala](speech-sdk.md)
