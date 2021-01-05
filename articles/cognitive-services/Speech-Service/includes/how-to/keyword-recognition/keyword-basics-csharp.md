---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 01/04/2021
ms.author: trbye
ms.openlocfilehash: 786f9587ab223cf87a48cd791f366049b94af59b
ms.sourcegitcommit: aeba98c7b85ad435b631d40cbe1f9419727d5884
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97866103"
---
Primeiro, carregue o arquivo de modelo de palavra-chave usando a `FromFile()` função estática, que retorna um `KeywordRecognitionModel` . Use o caminho para o `.table` arquivo que você baixou do Speech Studio. Além disso, você cria um `AudioConfig` usando o microfone padrão e, em seguida, instancia um novo `KeywordRecognizer` usando a configuração de áudio.

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

var keywordModel = KeywordRecognitionModel.FromFile("your/path/to/Activate_device.table");
using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
using var keywordRecognizer = new KeywordRecognizer(audioConfig);
```

Em seguida, a execução de reconhecimento de palavra-chave é feita com uma chamada para `RecognizeOnceAsync()` passando o objeto de modelo. Isso inicia uma sessão de reconhecimento de palavra-chave que dura até que a palavra-chave seja reconhecida. Portanto, você geralmente usa esse padrão de design em aplicativos multi-threaded, ou em casos de uso em que você pode estar aguardando uma palavra de ativação indefinidamente.

```csharp
KeywordRecognitionResult result = await keywordRecognizer.RecognizeOnceAsync(keywordModel);
```

> [!NOTE]
> O exemplo mostrado aqui usa o reconhecimento de palavra-chave local, pois não requer um `SpeechConfig` objeto para o contexto de autenticação e não entra em contato com o back-end. No entanto, você pode executar o reconhecimento de palavra-chave e [a verificação utilizando uma conexão de back-end direta](../../../tutorial-voice-enable-your-bot-speech-sdk.md#view-the-source-code-that-enables-keyword).

### <a name="continuous-recognition"></a>Reconhecimento contínuo

Outras classes no SDK de fala dão suporte a reconhecimento contínuo (tanto para reconhecimento de fala quanto de intenção) com reconhecimento de palavra-chave. Isso permite que você use o mesmo código que normalmente usaria para o reconhecimento contínuo, com a capacidade de fazer referência a um `.table` arquivo para seu modelo de palavra-chave.

Para conversão de fala em texto, siga o mesmo padrão de design mostrado no guia de [início rápido](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started-speech-to-text?tabs=script%2Cbrowser%2Cwindowsinstall&pivots=programming-language-csharp#continuous-recognition) para configurar o reconhecimento contínuo. Em seguida, substitua a chamada para `recognizer.StartContinuousRecognitionAsync()` por `recognizer.StartKeywordRecognitionAsync(KeywordRecognitionModel)` e transmita o `KeywordRecognitionModel` objeto. Para interromper o reconhecimento contínuo com a palavra-chave que está sendo parada, use `recognizer.StopKeywordRecognitionAsync()` em vez de `recognizer.StopContinuousRecognitionAsync()` .

O reconhecimento de intenção usa um padrão idêntico com as [`StartKeywordRecognitionAsync`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.intent.intentrecognizer.startkeywordrecognitionasync?view=azure-dotnet#Microsoft_CognitiveServices_Speech_Intent_IntentRecognizer_StartKeywordRecognitionAsync_Microsoft_CognitiveServices_Speech_KeywordRecognitionModel_) [`StopKeywordRecognitionAsync`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.intent.intentrecognizer.stopkeywordrecognitionasync?view=azure-dotnet#Microsoft_CognitiveServices_Speech_Intent_IntentRecognizer_StopKeywordRecognitionAsync) funções e.