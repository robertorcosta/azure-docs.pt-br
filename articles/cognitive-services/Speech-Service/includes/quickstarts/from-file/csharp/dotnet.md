---
title: 'Início Rápido: Reconhecer fala em um arquivo de áudio, C# (.NET) – Serviço de Fala'
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
ms.openlocfilehash: fdb747212914769b8551d9cd12f1fbc8a01245dc
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73506344"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, é preciso:

> [!div class="checklist"]
> * [Criar um Recurso de Fala do Azure](../../../../get-started.md)
> * [Configurar seu ambiente de desenvolvimento](../../../../quickstarts/setup-platform.md?tabs=dotnet)
> * [Criar um projeto de amostra vazio](../../../../quickstarts/create-project.md?tabs=dotnet)

## <a name="open-your-project-in-visual-studio"></a>Abra o projeto no Visual Studio

A primeira etapa é verificar se o projeto está aberto no Visual Studio.

1. Inicie o Visual Studio 2019.
2. Carregue o projeto e abra o `Program.cs`.

## <a name="start-with-some-boilerplate-code"></a>Comece com código de texto clichê

Vamos adicionar um código que funciona como um esqueleto para o projeto. Lembre-se de que você criou um método assíncrono chamado `RecognizeSpeechAsync()`.

````C#

using System;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;

namespace helloworld
{
    class Program
    {
        public static async Task RecognizeSpeechAsync()
        {
        }

        static void Main()
        {
            RecognizeSpeechAsync().Wait();
        }
    }
}

````

## <a name="create-a-speech-configuration"></a>Criar uma configuração de fala

Antes de inicializar um objeto `SpeechRecognizer`, é preciso criar uma configuração que use a região e a chave de assinatura. Insira esse código no método `RecognizeSpeechAsync()`.

> [!NOTE]
> Esta amostra usa o método `FromSubscription()` para criar o `SpeechConfig`. Para ver uma lista completa dos métodos disponíveis, confira a [Classe SpeechConfig](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet).

````C#
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
````

## <a name="create-an-audio-configuration"></a>Criar uma configuração de áudio

Agora, é preciso criar um objeto ````AudioConfig```` que indique o arquivo de áudio. Esse objeto é criado dentro de uma instrução using para garantir a liberação adequada de recursos não gerenciados. Insira esse código no método `RecognizeSpeechAsync()`, logo abaixo da configuração de fala.

````C#
using (var audioInput = AudioConfig.FromWavFileInput(@"whatstheweatherlike.wav"))
{
}
````

## <a name="initialize-a-speechrecognizer"></a>Inicializar um SpeechRecognizer

Agora, crie o objeto `SpeechRecognizer` usando os objetos `SpeechConfig` e `AudioConfig` criados anteriormente. Esse objeto também é criado dentro de uma instrução using para garantir a liberação adequada de recursos não gerenciados. Insira esse código no método `RecognizeSpeechAsync()`, dentro da instrução using que encapsula o objeto ````AudioConfig````.

````C#
using (var recognizer = new SpeechRecognizer(config, audioInput))
{
}
````

## <a name="recognize-a-phrase"></a>Reconhecer uma frase

No objeto `SpeechRecognizer`, chame o método `RecognizeOnceAsync()`. Dessa maneira, o serviço de fala saberá que você está enviando uma única frase para reconhecimento. Além disso, o método permite parar o reconhecimento assim que a frase é identificada.

Dentro da instrução using, adicione este código:
````C#
Console.WriteLine("Recognizing first result...");
var result = await recognizer.RecognizeOnceAsync();
````

## <a name="display-the-recognition-results-or-errors"></a>Exibir os resultados do reconhecimento (ou os erros)

Quando o serviço de fala retornar o resultado do reconhecimento, você poderá utilizá-lo. Para manter a simplicidade, vamos imprimir o resultado no console.

Dentro da instrução using, abaixo de `RecognizeOnceAsync()`, adicione este código:
````C#
if (result.Reason == ResultReason.RecognizedSpeech)
{
    Console.WriteLine($"We recognized: {result.Text}");
}
else if (result.Reason == ResultReason.NoMatch)
{
    Console.WriteLine($"NOMATCH: Speech could not be recognized.");
}
else if (result.Reason == ResultReason.Canceled)
{
    var cancellation = CancellationDetails.FromResult(result);
    Console.WriteLine($"CANCELED: Reason={cancellation.Reason}");

    if (cancellation.Reason == CancellationReason.Error)
    {
        Console.WriteLine($"CANCELED: ErrorCode={cancellation.ErrorCode}");
        Console.WriteLine($"CANCELED: ErrorDetails={cancellation.ErrorDetails}");
        Console.WriteLine($"CANCELED: Did you update the subscription info?");
    }
}
````

## <a name="check-your-code"></a>Verificar o código

Neste momento, seu código deverá ter a seguinte aparência:

````C#
//
// Copyright (c) Microsoft. All rights reserved.
// Licensed under the MIT license. See LICENSE.md file in the project root for full license information.
//

using System;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;

namespace helloworld
{
    class Program
    {
        public static async Task RecognizeSpeechAsync()
        {
            var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");

            using (var audioInput = AudioConfig.FromWavFileInput(@"whatstheweatherlike.wav"))
            {
                using (var recognizer = new SpeechRecognizer(config, audioInput))
                {
                    Console.WriteLine("Recognizing first result...");
                    var result = await recognizer.RecognizeOnceAsync();

                    if (result.Reason == ResultReason.RecognizedSpeech)
                    {
                        Console.WriteLine($"We recognized: {result.Text}");
                    }
                    else if (result.Reason == ResultReason.NoMatch)
                    {
                        Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                    }
                    else if (result.Reason == ResultReason.Canceled)
                    {
                        var cancellation = CancellationDetails.FromResult(result);
                        Console.WriteLine($"CANCELED: Reason={cancellation.Reason}");

                        if (cancellation.Reason == CancellationReason.Error)
                        {
                            Console.WriteLine($"CANCELED: ErrorCode={cancellation.ErrorCode}");
                            Console.WriteLine($"CANCELED: ErrorDetails={cancellation.ErrorDetails}");
                            Console.WriteLine($"CANCELED: Did you update the subscription info?");
                        }
                    }
                }
            }
        }

        static void Main()
        {
            RecognizeSpeechAsync().Wait();
        }
    }
}
````

## <a name="build-and-run-your-app"></a>Compilar e executar o aplicativo

Agora está tudo pronto para você compilar o aplicativo e testar o reconhecimento de fala usando o serviço de fala.

1. **Compilar o código**: na barra de menus do Visual Studio, escolha **Compilar** > **Compilar solução**.
2. **Iniciar o aplicativo**: na barra de menus, escolha **Depurar** > **Iniciar depuração** ou pressione **F5**.
3. **Iniciar reconhecimento**: a fala é enviada ao serviço de fala, transcrita como texto e renderizada no console.

   ```text
   Recognizing first result...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [footer](./footer.md)]
