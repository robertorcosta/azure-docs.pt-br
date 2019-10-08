---
title: 'Início Rápido: Traduzir fala, C# (.NET Framework Windows) – Serviço de Fala'
titleSuffix: Azure Cognitive Services
description: Neste início rápido, você criará um aplicativo .NET Framework para capturar a fala do usuário, traduzi-la para outro idioma e produzir o texto para a linha de comando. Este guia foi projetado para usuários do Windows.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/28/2019
ms.author: erhopf
ms.openlocfilehash: bb710a3e3adb13aa3999c13043c8bb93f6b885f1
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71327354"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-net-framework-windows"></a>Início Rápido: Traduzir fala com o SDK de Fala para .NET Framework (Windows)

Também há inícios rápidos disponíveis para [reconhecimento de fala](quickstart-csharp-dotnet-windows.md) e [síntese de fala](quickstart-text-to-speech-dotnet-windows.md).

Neste início rápido, você criará um aplicativo .NET Framework que captura a fala do usuário do microfone do computador, a traduz e transcreve o texto traduzido para a linha de comando em tempo real. Este aplicativo pode ser executado no Windows de 32 ou 64 bits e é criado com o [pacote NuGet do SDK de Fala](https://aka.ms/csspeech/nuget) e o Microsoft Visual Studio 2019.

Para obter uma lista completa dos idiomas disponíveis para tradução de fala, confira [suporte ao idioma](language-support.md).

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* Uma chave de assinatura do Azure para o Serviço de Fala. [Obtenha uma gratuitamente](get-started.md).

## <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Abra **Program.cs** e substitua todo o código nele pelo seguinte.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. Localize a cadeia de caracteres `YourSubscriptionKey` e a substitua pela chave de assinatura.

1. Encontre a cadeia de caracteres `YourServiceRegion` e a substitua pela [região](regions.md) associada à sua assinatura. Por exemplo, se você está usando a assinatura de avaliação gratuita, a região é `westus`.

1. Na barra de menus, escolha **Arquivo** > **Salvar tudo**.

## <a name="build-and-run-the-application"></a>Compile e execute o aplicativo

1. Na barra de menus, escolha **Compilar** > **Compilar Solução** para compilar o aplicativo. Agora, o código deve compilar sem erros.

1. Escolha **Depurar** > **Iniciar Depuração** (ou selecione **F5**) para iniciar o aplicativo **Olá Mundo**.

1. Fale uma frase ou uma sentença em inglês no microfone do dispositivo. O aplicativo transmite sua fala para os serviço de Fala, que a traduz fala em texto para outro idioma (neste caso, alemão). O serviço de Fala envia o texto traduzido de volta para o aplicativo, que exibe a tradução na janela.

   ![Interface do usuário de tradução de fala](media/sdk/qs-translate-csharp-dotnetcore-windows-output.png)

## <a name="next-steps"></a>Próximas etapas

Amostras adicionais, por exemplo, como ler fala de um arquivo de áudio e produzir texto traduzido como fala sintetizada, estão disponíveis no GitHub.

> [!div class="nextstepaction"]
> [Explorar amostras de C# no GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Consulte também

- [Treinar um modelo para Fala Personalizada](how-to-custom-speech-train-model.md)
