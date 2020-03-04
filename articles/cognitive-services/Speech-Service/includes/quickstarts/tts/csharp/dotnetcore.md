---
title: 'Início Rápido: Sintetizar fala, C# (.NET Core) – Serviço de Fala'
titleSuffix: Azure Cognitive Services
description: Saiba como sintetizar fala em C# no .NET Core no Windows usando o SDK de Fala
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 6/24/2019
ms.author: yinhew
ms.openlocfilehash: 6e732237b22bec6e2a66d44f12ac25e5f9adca3f
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74818194"
---
> [!NOTE]
> O .NET Core é uma plataforma .NET multiplataforma de software livre que implementa a especificação [.NET Standard](https://docs.microsoft.com/dotnet/standard/net-standard).

## <a name="prerequisites"></a>Prerequisites

Antes de começar, é preciso:

> [!div class="checklist"]
> * [Criar um Recurso de Fala do Azure](../../../../get-started.md)
> * [Configurar seu ambiente de desenvolvimento](../../../../quickstarts/setup-platform.md?tabs=dotnetcore)
> * [Criar um projeto de amostra vazio](../../../../quickstarts/create-project.md?tabs=dotnetcore)
## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Abra `Program.cs` e substitua todo o código nesse arquivo pelo seguinte.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnetcore/text-to-speech/helloworld/Program.cs#code)]

1. No mesmo arquivo, substitua a cadeia de caracteres `YourSubscriptionKey` por sua chave de assinatura.

1. Substitua também a cadeia de caracteres `YourServiceRegion` pela [região](~/articles/cognitive-services/Speech-Service/regions.md) associada à assinatura (por exemplo, `westus` para a assinatura de avaliação gratuita).

1. Salve as alterações no projeto.

## <a name="build-and-run-the-app"></a>Compilar e executar o aplicativo

1. Construa o aplicativo. Na barra de menus, escolha **Compilar** > **Compilar Solução**. O código deve compilar sem erros.

    ![Captura de tela do aplicativo do Visual Studio, com a opção Compilar Solução realçada](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-05-build.png "Compilar com êxito")

1. Inicie o aplicativo. Na barra de menus, escolha **Depurar**  > **Iniciar Depuração** ou pressione **F5**.

    ![Captura de tela do aplicativo do Visual Studio, com a opção Iniciar Depuração realçada](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-06-start-debugging.png "Iniciar o aplicativo na depuração")

1. Uma janela do console aparece, solicitando que você digite algum texto. Digite uma frase ou algumas palavras. O texto que você digitou é transmitido para o serviço de Fala e sintetizado em fala, que é reproduzido no alto-falante.

    ![Captura de tela da saída do console após a síntese bem-sucedida](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-dotnet-windows-console-output.png "Saída do console após uma síntese bem-sucedida")

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Consulte também

- [Criar uma Voz Personalizada](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Registrar amostras de voz personalizada](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
