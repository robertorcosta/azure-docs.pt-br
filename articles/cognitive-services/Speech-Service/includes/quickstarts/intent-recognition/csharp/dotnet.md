---
title: 'Início Rápido: Reconhecer fala, intenções e entidades, C# – Serviço de fala'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/28/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: a54d01aa78ad77c9328f79f31d21da570e7f5676
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73505920"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, é preciso:

> [!div class="checklist"]
> * [Criar um Azure Speech Resource](../../../../get-started.md)
> * [Criar um aplicativo LUIS (Reconhecimento vocal) para obter um ponto de extremidade](../../../../quickstarts/create-luis.md)
> * [Configurar o ambiente de desenvolvimento](../../../../quickstarts/setup-platform.md?tabs=dotnet)
> * [Criar um projeto de exemplo vazio](../../../../quickstarts/create-project.md?tabs=dotnet)

## <a name="open-your-project-in-visual-studio"></a>Abra o projeto no Visual Studio

A primeira etapa é verificar se o projeto está aberto no Visual Studio.

1. Inicie o Visual Studio 2019.
2. Carregue o projeto e abra `Program.cs`.

## <a name="start-with-some-boilerplate-code"></a>Começar com código de texto clichê

Vamos adicionar um código que funciona como um esqueleto para o projeto. Lembre-se de criar um método assíncrono chamado `RecognizeIntentAsync()`.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=7-17,77-86)]

## <a name="create-a-speech-configuration"></a>Criar uma configuração de fala

Antes de inicializar um objeto `IntentRecognizer`, você precisa criar uma configuração que usa a sua região e chave de ponto de extremidade LUIS. Insira esse código no método `RecognizeIntentAsync()`.

Este exemplo usa o método `FromSubscription()` para criar o `SpeechConfig`. Para obter uma lista completa dos métodos disponíveis, confira a [Classe SpeechConfig](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet).

> [!NOTE]
> É importante usar a chave de ponto de extremidade LUIS, e não as chaves de início ou de criação, pois apenas a chave do ponto de extremidade é válida para o reconhecimento de fala para intenção. Confira o artigo [Criar um aplicativo LUIS e obter uma chave de ponto de extremidade](~/articles/cognitive-services/Speech-Service/quickstarts/create-luis.md) para ver instruções sobre como obter a chave correta.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=26)]

## <a name="initialize-a-intentrecognizer"></a>Inicializar um IntentRecognizer

Agora, vamos criar um `IntentRecognizer`. Esse objeto é criado dentro de uma instrução using a fim de garantir a liberação adequada de recursos não gerenciados. Insira esse código no método `RecognizeIntentAsync()`, logo abaixo da configuração de fala.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=28-30,76)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>Adicionar um LanguageUnderstandingModel e intenções

Agora é preciso associar um `LanguageUnderstandingModel` ao reconhecedor de intenção e adicionar as intenções que deseja reconhecer.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=31-35)]

## <a name="recognize-an-intent"></a>Reconhecer uma intenção

No objeto `IntentRecognizer`, chame o método `RecognizeOnceAsync()`. Esse método permite que o serviço de fala saiba que você está enviando uma única expressão para reconhecimento e permite parar o reconhecimento assim que a frase é identificada.

Dentro da instrução using, adicione este código: [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=46)]

## <a name="display-the-recognition-results-or-errors"></a>Exibir os resultados do reconhecimento (ou os erros)

Quando o serviço de fala retornar o resultado do reconhecimento, você poderá utilizá-lo. Para manter a simplicidade, vamos imprimir o resultado no console.

Dentro da instrução using, abaixo de `RecognizeOnceAsync()`, adicione este código: [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=48-75)]

## <a name="check-your-code"></a>Verificar o código

Neste momento, seu código deverá se parecer com o seguinte: (Adicionamos alguns comentários nesta versão) [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=5-86)]

## <a name="build-and-run-your-app"></a>Compilar e executar seu aplicativo

Agora está tudo pronto para você criar seu aplicativo e testar o reconhecimento de fala usando o Serviço de fala.

1. **Compilar o código** – na barra de menus do Visual Studio, escolha **Compilar** > **Compilar Solução**.
2. **Iniciar seu aplicativo** – na barra de menus, escolha **Depurar** > **Iniciar Depuração** ou pressione **F5**.
3. **Iniciar o reconhecimento** – será solicitado que você fale uma expressão em inglês. Sua fala é enviada ao serviço de fala, transcrita como texto e renderizada no console.

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [footer](./footer.md)]
