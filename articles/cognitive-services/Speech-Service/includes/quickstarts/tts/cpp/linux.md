---
title: 'Início Rápido: Sintetizar fala, C++ (Linux) – Serviço de Fala'
titleSuffix: Azure Cognitive Services
description: Saiba como sintetizar fala em C++ no Linux usando o SDK de Fala
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 07/05/2019
ms.author: yinhew
ms.openlocfilehash: fc5d0e0a7f693df87e754383a123dc1cebfdd94c
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74818383"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, é preciso:

> [!div class="checklist"]
> * [Criar um Recurso de Fala do Azure](../../../../get-started.md)
> * [Configurar seu ambiente de desenvolvimento](../../../../quickstarts/setup-platform.md?tabs=linux)
> * [Criar um projeto de amostra vazio](../../../../quickstarts/create-project.md?tabs=linux)

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Crie um arquivo de origem C++ chamado `helloworld.cpp` e cole o código a seguir nele.

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/linux/text-to-speech/helloworld.cpp#code)]

1. Nesse novo arquivo, substitua a cadeia de caracteres `YourSubscriptionKey` pela sua chave de assinatura do serviço de Fala.

1. Substitua a cadeia de caracteres `YourServiceRegion` pela [região](~/articles/cognitive-services/Speech-Service/regions.md) associada à assinatura (por exemplo, `westus` para a assinatura de avaliação gratuita).

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

1. Na janela do console, é exibido um prompt, solicitando que você digite algum texto. Digite uma frase ou algumas palavras. O texto que você digitou é transmitido para o serviço de Fala e sintetizado em fala, que é reproduzido no alto-falante.

   ```text
   Type some text that you want to speak...
   > hello
   Speech synthesized to speaker for text [hello]
   Press enter to exit...
   ```

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Consulte também

- [Criar uma Voz Personalizada](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Registrar amostras de voz personalizada](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
