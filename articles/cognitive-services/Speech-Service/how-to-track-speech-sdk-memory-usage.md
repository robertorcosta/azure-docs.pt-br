---
title: Como rastrear o uso da memória Speech SDK - Serviço de fala
titleSuffix: Azure Cognitive Services
description: O Speech Service SDK suporta inúmeras linguagens de programação para conversão de fala para texto e texto-para-fala, juntamente com a tradução de fala. Este artigo discute ferramentas de gerenciamento de memória incorporadas ao SDK.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: rhurey
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: da5103317a2215aca68cec14ba8a0951258c9b89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "75456427"
---
# <a name="how-to-track-speech-sdk-memory-usage"></a>Como rastrear o uso da memória Speech SDK

O Speech SDK é baseado em uma base de código nativa projetada em várias linguagens de programação através de uma série de camadas de interoperabilidade. Cada projeção específica do idioma tem características idiomáticas corretas para gerenciar o ciclo de vida do objeto. Além disso, o Speech SDK inclui ferramentas de gerenciamento de memória para rastrear o uso de recursos com registro de objetos e limites de objetos. 

## <a name="how-to-read-object-logs"></a>Como ler registros de objetos

Se [o registro do Speech SDK estiver ativado,](how-to-use-logging.md)as tags de rastreamento serão emitidas para permitir a observação histórica do objeto. Essas tags incluem: 

* `TrackHandle` ou `StopTracking` 
* O tipo de objeto
* O número atual de objetos que são rastreados o tipo do objeto e o número atual sendo rastreado.

Aqui está um registro de amostra: 

```terminal
(284): 8604ms SPX_DBG_TRACE_VERBOSE:  handle_table.h:90 TrackHandle type=Microsoft::CognitiveServices::Speech::Impl::ISpxRecognitionResult handle=0x0x7f688401e1a0, ptr=0x0x7f688401e1a0, total=19
```

## <a name="set-a-warning-threshold"></a>Defina um limite de aviso

Você tem a opção de criar um limite de aviso e, se esse limite for excedido (assumindo que o registro está ativado), uma mensagem de aviso será registrada. A mensagem de aviso contém um despejo de todos os objetos existentes, juntamente com sua contagem. Essas informações podem ser usadas para entender melhor os problemas. 

Para habilitar um limiar de aviso, `SpeechConfig` ele deve ser especificado em um objeto. Este objeto é verificado quando um novo reconizador é criado. Nos exemplos a seguir, vamos supor que você `SpeechConfig` criou `config`uma instância chamada :

::: zone pivot="programming-language-csharp"

```csharp
config.SetProperty("SPEECH-ObjectCountWarnThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
config->SetProperty("SPEECH-ObjectCountWarnThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-java"

```java
config.setProperty("SPEECH-ObjectCountWarnThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_config.set_property_by_name(“SPEECH-ObjectCountWarnThreshold", "10000")?
```

::: zone-end

::: zone pivot="programming-language-more"

```ObjectiveC
[config setPropertyTo:@"10000" byName:"SPEECH-ObjectCountWarnThreshold"];
```

::: zone-end

> [!TIP]
> O valor padrão desta propriedade é de 10.000.

## <a name="set-an-error-threshold"></a>Defina um limite de erro 

Usando o Speech SDK, você pode definir o número máximo de objetos permitidos em um determinado momento. Se essa configuração estiver ativada, quando o número máximo for atingido, as tentativas de criar novos objetos de reconhecimento falharão. Os objetos existentes continuarão a funcionar.

Aqui está um erro de amostra:

```terminal
Runtime error: The maximum object count of 500 has been exceeded.
The threshold can be adjusted by setting the SPEECH-ObjectCountErrorThreshold property on the SpeechConfig object.
See http://https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-object-tracking-speech-sdk for more detailed information.
Handle table dump by ojbect type:
class Microsoft::CognitiveServices::Speech::Impl::ISpxRecognitionResult 0
class Microsoft::CognitiveServices::Speech::Impl::ISpxRecognizer 0
class Microsoft::CognitiveServices::Speech::Impl::ISpxAudioConfig 0
class Microsoft::CognitiveServices::Speech::Impl::ISpxSpeechConfig 0
```

Para habilitar um limiar de erro, `SpeechConfig` ele deve ser especificado em um objeto. Este objeto é verificado quando um novo reconizador é criado. Nos exemplos a seguir, vamos supor que você `SpeechConfig` criou `config`uma instância chamada :

::: zone pivot="programming-language-csharp"

```csharp
config.SetProperty("SPEECH-ObjectCountErrorThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
config->SetProperty("SPEECH-ObjectCountErrorThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-java"

```java
config.setProperty("SPEECH-ObjectCountErrorThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_config.set_property_by_name(“SPEECH-ObjectCountErrorThreshold", "10000")?
```

::: zone-end

::: zone pivot="programming-language-more"

```objc
[config setPropertyTo:@"10000" byName:"SPEECH-ObjectCountErrorThreshold"];
```

::: zone-end

> [!TIP]
> O valor padrão desta propriedade é o valor `size_t` máximo específico da plataforma para um tipo de dados. Um reconhecimento típico consumirá entre 7 e 10 objetos internos.

## <a name="next-steps"></a>Próximas etapas

* [Obtenha sua assinatura de teste do serviço de fala](get-started.md)
* [Aprenda a reconhecer a fala usando um microfone](quickstarts/speech-to-text-from-microphone.md)