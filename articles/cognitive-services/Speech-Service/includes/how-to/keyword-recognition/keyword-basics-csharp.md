---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 11/03/2020
ms.author: trbye
ms.openlocfilehash: 040ffea69f76255dcb1bfc6787cad45a95baa904
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93305884"
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
> O exemplo mostrado aqui usa o reconhecimento de palavra-chave local, pois não requer um `SpeechConfig` objeto para o contexto de autenticação e não entra em contato com o back-end. No entanto, você pode executar o reconhecimento de palavra-chave e [a verificação utilizando uma conexão de back-end contínua](https://docs.microsoft.com/azure/cognitive-services/speech-service/tutorial-voice-enable-your-bot-speech-sdk#view-the-source-code-that-enables-keyword).