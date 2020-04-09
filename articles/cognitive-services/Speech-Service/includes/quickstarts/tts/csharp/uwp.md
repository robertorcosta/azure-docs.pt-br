---
title: 'Início Rápido: Sintetizar fala, C# (UWP) – Serviço de Fala'
titleSuffix: Azure Cognitive Services
description: Neste artigo, você criará um aplicativo UWP (Plataforma Universal do Windows) em C# usando o SDK de Fala dos Serviços Cognitivos. Você pode sintetizar em tempo real uma fala a partir de texto no alto-falante do seu dispositivo. O aplicativo é criado com o Pacote NuGet do SDK de Fala e o Microsoft Visual Studio 2019.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/04/2020
ms.author: yinhew
ms.openlocfilehash: 1633586a77465d9d5f605b17727f2531430d1cbd
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2020
ms.locfileid: "80671558"
---
> [!NOTE]
> A Plataforma Universal do Windows permite desenvolver aplicativos que executam em qualquer dispositivo com suporte para Windows 10, incluindo PCs, Xbox, Surface Hub e outros dispositivos.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, é preciso:

> [!div class="checklist"]
> * [Criar um Recurso de Fala do Azure](../../../../get-started.md)
> * [Configurar seu ambiente de desenvolvimento e criar um projeto vazio](../../../../quickstarts/setup-platform.md?tabs=uwp&pivots=programming-language-csharp)

## <a name="add-sample-code"></a>Adicionar código de exemplo

Agora, adicione o código XAML que define a interface do usuário do aplicativo e adicione a implementação code-behind em C#.

1. No **Gerenciador de Soluções**, abra `MainPage.xaml`.

1. Na exibição XAML do designer, insira o snippet de código XAML a seguir na tag **Grade** (entre `<Grid>` e `</Grid>`):

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/text-to-speech/helloworld/MainPage.xaml#StackPanel)]

1. No **Gerenciador de Soluções**, abra o arquivo de origem code-behind `MainPage.xaml.cs`. (Ele está agrupado em `MainPage.xaml`.)

1. Substitua todo o código nele pelo snippet a seguir:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/text-to-speech/helloworld/MainPage.xaml.cs#code)]

1. No manipulador `Speak_ButtonClicked` do arquivo de origem, localize a cadeia de caracteres `YourSubscriptionKey` e substitua-a por sua chave de assinatura.

1. No manipulador `Speak_ButtonClicked`, localize a cadeia de caracteres `YourServiceRegion` e substitua-a pela [região](~/articles/cognitive-services/Speech-Service/regions.md) associada à assinatura. (Por exemplo, use `westus` para a assinatura de avaliação gratuita.)

1. Na barra de menus, escolha **Arquivo** > **Salvar Tudo** para salvar suas alterações.

## <a name="build-and-run-the-application"></a>Compile e execute o aplicativo

Agora, você está pronto para criar e testar seu aplicativo.

1. Na barra de menus, escolha **Compilar** > **Compilar Solução** para compilar o aplicativo. Agora, o código deve compilar sem erros.

1. Escolha **Depurar** > **Iniciar Depuração** (ou pressione **F5**) para iniciar o aplicativo. A janela **helloworld** é exibida.

   ![Exemplo de aplicativo de síntese de fala da UWP em C# – início rápido](~/articles/cognitive-services/Speech-Service/media/sdk/qs-text-to-speech-uwp-helloworld-window.png)

1. Digite algum texto na caixa de texto e clique em **Fala**. Seu texto é transmitido para o serviço de Fala e sintetizado em fala. Depois, é reproduzido no alto-falante.

    ![Interface do usuário de síntese de fala](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Confira também

- [Criar uma Voz Personalizada](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Registrar amostras de voz personalizada](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
