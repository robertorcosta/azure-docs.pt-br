---
title: 'Início Rápido: Reconhecer fala, .NET Framework (Windows) – Serviço de Fala'
titleSuffix: Azure Cognitive Services
description: Use este guia para criar um aplicativo de console de conversão de fala em texto usando o .NET Framework para Windows e o SDK de Fala. Quando terminar, você pode usar o microfone do computador para transcrever a conversão de fala em texto em tempo real.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/28/2019
ms.author: wolfma
ms.openlocfilehash: cb140647394858fbc0a9a00ea125365d5b7a08d5
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71327033"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-net-framework-windows"></a>Início Rápido: Reconhecimento de fala com o SDK de Fala para .NET Framework (Windows)

Também há inícios rápidos disponíveis para [síntese de fala](quickstart-text-to-speech-dotnet-windows.md) e [tradução de fala](quickstart-translate-speech-dotnetframework-windows.md).

Se desejar, escolha uma linguagem de programação e/ou um ambiente diferente:<br/>
[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Use este guia para criar um aplicativo de console de conversão de fala em texto usando o .NET Framework para Windows e o SDK de Fala. Quando terminar, você pode usar o microfone do computador para transcrever a conversão de fala em texto em tempo real.

Para obter uma rápida demonstração (sem agrupar o projeto do Visual Studio por conta própria, conforme descrito neste artigo), obtenha os mais recentes [Exemplos de SDK de Fala dos Serviços Cognitivos](https://github.com/Azure-Samples/cognitive-services-speech-sdk) do GitHub.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este projeto, você precisará de:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* Uma chave de assinatura para o Serviço de Fala. [Obtenha uma gratuitamente](get-started.md).
* Acesso ao microfone do computador.

## <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Abra **Program.cs** e substitua o código gerado automaticamente por este exemplo:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. Localize a cadeia de caracteres `YourSubscriptionKey` e a substitua pela chave de assinatura dos Serviços de Fala.

1. Encontre a cadeia de caracteres `YourServiceRegion` e a substitua pela [região](regions.md) associada à sua assinatura. Por exemplo, se você está usando a assinatura de avaliação gratuita, a região é `westus`.

1. Na barra de menus, escolha **Arquivo** > **Salvar tudo**.

## <a name="build-and-run-the-app"></a>Compilar e executar o aplicativo

1. Na barra de menus, escolha **Compilar** > **Compilar Solução** para compilar o aplicativo. Agora, o código deve compilar sem erros.

1. Escolha **Depurar** > **Iniciar Depuração** (ou selecione **F5**) para iniciar o aplicativo **Olá Mundo**.

1. Fale uma frase ou uma sentença em inglês no microfone do dispositivo. O aplicativo transmite sua fala para os Serviços de Fala, que enviam o texto transcrito de volta ao aplicativo para exibição.

   ![Interface do usuário do reconhecimento de fala](media/sdk/qs-csharp-dotnet-windows-10-console-output.png)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Explorar amostras de C# no GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Consulte também

- [Treinar um modelo para Fala Personalizada](how-to-custom-speech-train-model.md)
