---
title: 'Início Rápido: Reconhecer a fala de um microfone, C++ (macOS) – Serviço de Fala'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/17/2019
ms.author: wolfma
ms.openlocfilehash: eb2032ce9fd6efed4a45aa30b469cb67926f17c9
ms.sourcegitcommit: dfa543fad47cb2df5a574931ba57d40d6a47daef
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2020
ms.locfileid: "77446308"
---
## <a name="prerequisites"></a>Prerequisites

Antes de começar:

> [!div class="checklist"]
> * [Criar um Recurso de Fala do Azure](../../../../get-started.md)
> * [Configurar seu ambiente de desenvolvimento](../../../../quickstarts/setup-platform.md?tabs=macos)
> * [Criar um projeto de amostra vazio](../../../../quickstarts/create-project.md?tabs=macos)
> * Verificar se você tem acesso a um microfone para captura de áudio

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Crie um arquivo de origem C++ chamado `helloworld.cpp` e cole o código a seguir nele.

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/macos/from-microphone/helloworld.cpp#code)]

1. Nesse novo arquivo, substitua a cadeia de caracteres `YourSubscriptionKey` pela sua chave de assinatura do serviço de Fala.

1. Substitua a cadeia de caracteres `YourServiceRegion` pelo **Identificador de região** da [região](https://aka.ms/speech/sdkregion) associada à assinatura (por exemplo, `westus` para a assinatura de avaliação gratuita).

> [!NOTE]
> O SDK de Fala usará como padrão o reconhecimento do uso de en-us como o idioma; confira [Especificar o idioma de origem para conversão de fala em texto](../../../../how-to-specify-source-language.md) para obter informações sobre como escolher o idioma de origem.

## <a name="build-the-app"></a>Compilar o aplicativo

> [!NOTE]
> Insira os comandos abaixo como uma _única linha de comando_. A maneira mais fácil de fazer isso é copiar o comando usando o **Copiar** ao lado de cada comando e, em seguida, colá-lo no prompt de shell.

* Execute o comando a seguir para compilar o aplicativo.

  ```sh
  g++ helloworld.cpp -o helloworld --std=c++14 -F${SPEECHSDK_ROOT} -framework MicrosoftCognitiveServicesSpeech
  ```

## <a name="run-the-app"></a>Executar o aplicativo

1. Defina o caminho da biblioteca do carregador para apontar para a biblioteca do SDK de Fala.

    ```sh
    export DYLD_FRAMEWORK_PATH="$DYLD_FRAMEWORK_PATH:$SPEECHSDK_ROOT"
    ```

1. Execute o aplicativo.

   ```sh
   ./helloworld
   ```

1. Será exibido um prompt na janela do console solicitando que você diga algo. Fale uma frase ou expressão em inglês. Sua fala será transmitida para o serviço de Fala e transcrita para texto, que aparecerá na mesma janela.

   ```text
   Say something...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [footer](./footer.md)]