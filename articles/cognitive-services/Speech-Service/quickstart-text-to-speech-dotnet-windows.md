---
title: 'Início Rápido: Sintetizar fala, .NET Framework (Windows) – Serviço de Fala'
titleSuffix: Azure Cognitive Services
description: Use este guia para criar um aplicativo de console de conversão de fala em texto usando o .NET Framework para Windows e o SDK de Fala. Quando terminar, você pode sintetizar fala do texto e ouvir a fala no seu alto-falante em tempo real.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/27/2019
ms.author: yinhew
ms.openlocfilehash: 1a411455e4a6dea22e092cdfc8e70ee23b656435
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71327444"
---
# <a name="quickstart-synthesize-speech-with-the-speech-sdk-for-net-framework-windows"></a>Início Rápido: Sintetizar fala com o SDK de Fala para .NET Framework (Windows)

Também há inícios rápidos disponíveis para [reconhecimento de fala](quickstart-csharp-dotnet-windows.md) e [tradução de fala](quickstart-translate-speech-dotnetframework-windows.md).

Use este guia para criar um aplicativo de console de conversão de fala em texto usando o .NET Framework para Windows e o SDK de Fala. Quando terminar, você pode sintetizar fala do texto e ouvir a fala no seu alto-falante em tempo real.

Para obter uma rápida demonstração (sem agrupar o projeto do Visual Studio por conta própria, conforme descrito neste artigo), obtenha os mais recentes [Exemplos de SDK de Fala dos Serviços Cognitivos](https://github.com/Azure-Samples/cognitive-services-speech-sdk) do GitHub.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este projeto, você precisará de:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* Uma chave de assinatura para o Serviço de Fala. [Obtenha uma gratuitamente](get-started.md).
* Um alto-falante (ou um fone de ouvido) disponíveis.

## <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Abra **Program.cs** e substitua o código gerado automaticamente por este exemplo:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. Localize a cadeia de caracteres `YourSubscriptionKey` e a substitua pela chave de assinatura dos Serviços de Fala.

1. Encontre a cadeia de caracteres `YourServiceRegion` e a substitua pela [região](regions.md) associada à sua assinatura. Por exemplo, se você está usando a assinatura de avaliação gratuita, a região é `westus`.

1. Na barra de menus, escolha **Arquivo** > **Salvar tudo**.

## <a name="build-and-run-the-application"></a>Compile e execute o aplicativo

1. Na barra de menus, escolha **Compilar** > **Compilar Solução** para compilar o aplicativo. Agora, o código deve compilar sem erros.

1. Escolha **Depurar** > **Iniciar Depuração** (ou selecione **F5**) para iniciar o aplicativo **Olá Mundo**.

1. Insira uma frase ou sentença em inglês. O aplicativo transmite o texto para os Serviços de Fala, que enviam a fala sintetizada para o aplicativo para reprodução em seu alto-falante.

   ![Interface do usuário de síntese de fala](media/sdk/qs-tts-csharp-dotnet-windows-console-output.png)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Explorar amostras de C# no GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Consulte também

- [Criar uma Voz Personalizada](how-to-custom-voice-create-voice.md)
- [Registrar amostras de voz personalizada](record-custom-voice-samples.md)
