---
title: 'Início Rápido: Reconhecer uma fala de um arquivo de áudio, C++ (Linux) – Serviço de Fala'
titleSuffix: Azure Cognitive Services
description: Saiba como reconhecer fala em C++ no Linux usando o SDK de Fala
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: wolfma
ms.openlocfilehash: 4d03c0f8e8aa387bae68dda6aeb974efe155366d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73506320"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, é preciso:

> [!div class="checklist"]
> * [Criar um Recurso de Fala do Azure](../../../../get-started.md)
> * [Configurar seu ambiente de desenvolvimento](../../../../quickstarts/setup-platform.md?tabs=linux)
> * [Criar um projeto de amostra vazio](../../../../quickstarts/create-project.md?tabs=linux)

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Crie um arquivo de origem C++ chamado `helloworld.cpp` e cole o código a seguir nele.

   ````C++

    // Creates an instance of a speech config with specified subscription key and service region.
    // Replace with your own subscription key and service region (e.g., "westus").
    auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");

    // Creates a speech recognizer using a WAV file. The default language is "en-us".
    // Replace with your own audio file name.
    auto audioInput = AudioConfig::FromWavFileInput("whatstheweatherlike.wav");
    auto recognizer = SpeechRecognizer::FromConfig(config, audioInput);
    cout << "Recognizing first result...\n";

    // Starts speech recognition, and returns after a single utterance is recognized. The end of a
    // single utterance is determined by listening for silence at the end or until a maximum of 15
    // seconds of audio is processed.  The task returns the recognition text as result.
    // Note: Since RecognizeOnceAsync() returns only a single utterance, it is suitable only for single
    // shot recognition like command or query.
    // For long-running multi-utterance recognition, use StartContinuousRecognitionAsync() instead.
    auto result = recognizer->RecognizeOnceAsync().get();

    // Checks result.
    if (result->Reason == ResultReason::RecognizedSpeech)
    {
        cout << "RECOGNIZED: Text=" << result->Text << std::endl;
    }
    else if (result->Reason == ResultReason::NoMatch)
    {
        cout << "NOMATCH: Speech could not be recognized." << std::endl;
    }
    else if (result->Reason == ResultReason::Canceled)
    {
        auto cancellation = CancellationDetails::FromResult(result);
        cout << "CANCELED: Reason=" << (int)cancellation->Reason << std::endl;

        if (cancellation->Reason == CancellationReason::Error)
        {
            cout << "CANCELED: ErrorCode=" << (int)cancellation->ErrorCode << std::endl;
            cout << "CANCELED: ErrorDetails=" << cancellation->ErrorDetails << std::endl;
            cout << "CANCELED: Did you update the subscription info?" << std::endl;
        }
    }

   ````

1. Nesse novo arquivo, substitua a cadeia de caracteres `YourSubscriptionKey` pela sua chave de assinatura dos Serviços de Fala.

1. Substitua a cadeia de caracteres `YourServiceRegion` pela [região](~/articles/cognitive-services/Speech-Service/regions.md) associada à assinatura (por exemplo, `westus` para a assinatura de avaliação gratuita).

1. Substitua a cadeia de caracteres `whatstheweatherlike.wav` pelo nome do seu arquivo.

## <a name="build-the-app"></a>Compilar o aplicativo

> [!NOTE]
> Insira os comandos abaixo como uma _única linha de comando_. A maneira mais fácil de fazer isso é copiar o comando usando o **Copiar** ao lado de cada comando e, em seguida, colá-lo no prompt de shell.

* Em um sistema **x64** (64 bits), execute o comando a seguir para compilar o aplicativo.

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x64" -l:libasound.so.2
  ```

* Em um sistema **x86** (32 bits), execute o comando a seguir para compilar o aplicativo.

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x86" -l:libasound.so.2
  ```

* Em um sistema **ARM64** (64 bits), execute o comando a seguir para compilar o aplicativo.

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/arm64" -l:libasound.so.2
  ```

## <a name="run-the-app"></a>Execute o aplicativo

1. Defina o caminho da biblioteca do carregador para apontar para a biblioteca do SDK de Fala.

   * Em um sistema **x64** (64 bits), digite o comando a seguir.

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x64"
     ```

   * Em um sistema **x86** (32 bits), digite este comando.

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x86"
     ```

   * Em um sistema **ARM64** (64 bits), insira o comando a seguir.

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/arm64"
     ```

1. Execute o aplicativo.

   ```sh
   ./helloworld
   ```

1. O arquivo de áudio é transmitido para os Serviços de Fala e o primeiro enunciado no arquivo é convertido em texto, que aparece na mesma janela.

   ```text
   Recognizing first result...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [footer](./footer.md)]
