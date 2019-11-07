---
title: 'Início Rápido: Reconhecer a fala de um microfone, C# (UWP) - Serviço de Fala'
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
ms.openlocfilehash: 0d1da9a9ef32aed1975595bb15909b9531ab2400
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73505616"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar:

> [!div class="checklist"]
> * [Crie um recurso do Azure Speech](../../../../get-started.md)
> * [Configure o ambiente de desenvolvimento](../../../../quickstarts/setup-platform.md?tabs=uwp)
> * [Crie um projeto de amostra vazio](../../../../quickstarts/create-project.md?tabs=uwp)

Se você já fez isso, ótimo. Vamos continuar.

## <a name="open-your-project-in-visual-studio"></a>Abra o projeto no Visual Studio

A primeira etapa é verificar se o projeto está aberto no Visual Studio.

## <a name="start-with-some-boilerplate-code"></a>Começar com código de texto clichê

Vamos adicionar um código que funciona como um esqueleto para o projeto.

1. No **Gerenciador de Soluções**, abra `MainPage.xaml`.

2. Na exibição XAML do designer, insira o snippet de código XAML a seguir na tag **Grade** (entre `<Grid>` e `</Grid>`):

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml#StackPanel)]

3. No **Gerenciador de Soluções**, abra o arquivo de origem code-behind `MainPage.xaml.cs`. (Ele está agrupado em `MainPage.xaml`.)

4. Substitua o código pelo seguinte código base:

   [!code-csharp[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=6-50,55-56,94-154)]

## <a name="create-a-speech-configuration"></a>Criar uma configuração de Fala

Antes de inicializar um objeto `SpeechRecognizer`, é preciso criar uma configuração que use a sua região e chave de assinatura. Insira esse código no método `RecognizeSpeechAsync()`.

> [!NOTE]
> Esta amostra usa o método `FromSubscription()` para criar o `SpeechConfig`. Para obter uma lista completa dos métodos disponíveis, confira a [Classe SpeechConfig](https://docs.microsoft.com/dotnet/api/)[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=51-53)]

## <a name="initialize-a-speechrecognizer"></a>Inicializar um SpeechRecognizer

Agora, vamos criar um `SpeechRecognizer`. Esse objeto é criado dentro de uma instrução using para garantir a liberação adequada de recursos não gerenciados. Insira esse código no método `RecognizeSpeechAsync()`, logo abaixo da configuração de Fala.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=58,59,93)]

## <a name="recognize-a-phrase"></a>Reconhecer uma frase

No objeto `SpeechRecognizer`, você chamará o método `RecognizeOnceAsync()`. Esse método permite que o Serviço de Fala saiba que você está enviando uma única expressão para reconhecimento e permite parar o reconhecimento, assim que a frase é identificada.

Dentro da instrução using, adicione este código: [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=66)]

## <a name="display-the-recognition-results-or-errors"></a>Exibir os resultados do reconhecimento (ou os erros)

Quando o serviço de fala retornar o resultado do reconhecimento, você poderá utilizá-lo. Para manter a simplicidade, vamos imprimir o resultado no painel de status.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=68-93)]

## <a name="build-and-run-the-application"></a>Compile e execute o aplicativo

Agora, você está pronto para criar e testar seu aplicativo.

1. Na barra de menus, escolha **Compilar** > **Compilar Solução** para compilar o aplicativo. Agora, o código deve compilar sem erros.

1. Escolha **Depurar** > **Iniciar Depuração** (ou pressione **F5**) para iniciar o aplicativo. A janela **helloworld** é exibida.

   ![Exemplo de aplicativo de reconhecimento de fala da UWP em C# – início rápido](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-helloworld-window.png)

1. Selecione **Habilitar Microfone** e, quando a solicitação de permissão de acesso for exibida, selecione **Sim**.

   ![Solicitação de permissão de acesso ao microfone](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. Selecione **Reconhecimento de fala com a entrada do microfone** e fale uma frase ou uma oração em inglês no microfone do dispositivo. Sua fala será transmitida para o Serviço de Fala e transcrita para texto, que será exibida na janela.

   ![Interface do usuário do reconhecimento de fala](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-11-ui-result.png)

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [footer](./footer.md)]
