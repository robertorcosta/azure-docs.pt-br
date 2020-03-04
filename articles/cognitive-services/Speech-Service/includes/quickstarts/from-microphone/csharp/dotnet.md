---
title: 'Início Rápido: Reconhecer a fala de um microfone, C# (.NET) – Serviço de Fala'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/17/2019
ms.author: erhopf
ms.openlocfilehash: 9c16e2e1726fcbf1e0b55ee0e9808a607b611ea6
ms.sourcegitcommit: dfa543fad47cb2df5a574931ba57d40d6a47daef
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2020
ms.locfileid: "77445613"
---
## <a name="prerequisites"></a>Prerequisites

Antes de começar:

> [!div class="checklist"]
> * [Criar um Recurso de Fala do Azure](../../../../get-started.md)
> * [Configurar seu ambiente de desenvolvimento](../../../../quickstarts/setup-platform.md?tabs=dotnet)
> * [Criar um projeto de amostra vazio](../../../../quickstarts/create-project.md?tabs=dotnet)
> * Verificar se você tem acesso a um microfone para captura de áudio

## <a name="open-your-project-in-visual-studio"></a>Abra o projeto no Visual Studio

A primeira etapa é verificar se o projeto está aberto no Visual Studio.

1. Inicie o Visual Studio 2019.
2. Carregue o projeto e abra o `Program.cs`.

## <a name="start-with-some-boilerplate-code"></a>Comece com código de texto clichê

Vamos adicionar um código que funciona como um esqueleto para o projeto. Lembre-se de que você criou um método assíncrono chamado `RecognizeSpeechAsync()`.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-microphone/helloworld/Program.cs?range=5-15,43-52)]

## <a name="create-a-speech-configuration"></a>Criar uma configuração de Fala

Antes de inicializar um objeto `SpeechRecognizer`, é preciso criar uma configuração que use a região e a chave da assinatura (escolha o **Identificador de região** da [região](https://aka.ms/speech/sdkregion)). Insira esse código no método `RecognizeSpeechAsync()`.

> [!NOTE]
> Esta amostra usa o método `FromSubscription()` para criar o `SpeechConfig`. Para ver uma lista completa dos métodos disponíveis, confira a [Classe SpeechConfig](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet).
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-microphone/helloworld/Program.cs?range=16)]
> O SDK de Fala usará como padrão o reconhecimento do uso de en-us como o idioma; confira [Especificar o idioma de origem para conversão de fala em texto](../../../../how-to-specify-source-language.md) para obter informações sobre como escolher o idioma de origem.

## <a name="initialize-a-speechrecognizer"></a>Inicializar um SpeechRecognizer

Agora, vamos criar um `SpeechRecognizer`. Esse objeto é criado dentro de uma instrução using para garantir a liberação adequada de recursos não gerenciados. Insira esse código no método `RecognizeSpeechAsync()`, logo abaixo da configuração de Fala.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-microphone/helloworld/Program.cs?range=17-19,42)]

## <a name="recognize-a-phrase"></a>Reconhecer uma frase

No objeto `SpeechRecognizer`, chame o método `RecognizeOnceAsync()`. Esse método permite que o Serviço de Fala saiba que você está enviando uma única expressão para reconhecimento e permite parar o reconhecimento, assim que a frase é identificada.

Dentro da instrução using, adicione este código: [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-microphone/helloworld/Program.cs?range=20)]

## <a name="display-the-recognition-results-or-errors"></a>Exibir os resultados do reconhecimento (ou os erros)

Quando o Serviço de Fala retornar o resultado do reconhecimento, você poderá utilizá-lo. Para manter a simplicidade, vamos imprimir o resultado no console.

Dentro da instrução using, abaixo de `RecognizeOnceAsync()`, adicione este código: [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-microphone/helloworld/Program.cs?range=22-41)]

## <a name="check-your-code"></a>Verificar o código

Neste momento, seu código deverá se parecer com o seguinte: [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-microphone/helloworld/Program.cs)]

## <a name="build-and-run-your-app"></a>Compilar e executar o aplicativo

Agora está tudo pronto para você compilar o aplicativo e testar o reconhecimento de fala usando o serviço de fala.

1. **Compilar o código**: na barra de menus do Visual Studio, escolha **Compilar** > **Compilar Solução**.
2. **Iniciar o aplicativo**: na barra de menus, escolha **Depurar** > **Iniciar Depuração** ou pressione **F5**.
3. **Iniciar o reconhecimento**: ele solicitará que você fale uma frase em inglês. Sua fala é enviada ao Serviço de Fala, transcrita como texto e renderizada no console.

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [footer](./footer.md)]
