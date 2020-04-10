---
title: 'Início Rápido: Traduzir uma fala em fala, C# (UWP) – Serviço de Fala'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: lisaweixu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 04/04/2020
ms.author: erhopf
ms.topic: include
ms.openlocfilehash: 08001aabd279b2df64635719e9ef3e0772df62e9
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2020
ms.locfileid: "80671374"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, é preciso:

> [!div class="checklist"]
> * [Criar um Recurso de Fala do Azure](../../../../get-started.md)
> * [Configurar seu ambiente de desenvolvimento e criar um projeto vazio](../../../../quickstarts/setup-platform.md?tabs=uwp&pivots=programming-language-csharp)

## <a name="add-sample-code"></a>Adicionar código de exemplo

Agora, adicione o código XAML que define a interface do usuário do aplicativo e adicione a implementação code-behind em C#.

1. No **Gerenciador de Soluções**, abra `MainPage.xaml`.

1. Na exibição XAML do designer, insira o snippet de código XAML a seguir na tag **Grade** (entre `<Grid>` e `</Grid>`):

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/translate-speech-to-text/helloworld/MainPage.xaml#StackPanel)]

1. No **Gerenciador de Soluções**, abra o arquivo de origem code-behind `MainPage.xaml.cs`. (Ele está agrupado em `MainPage.xaml`.)

1. Substitua todo o código nele pelo snippet a seguir:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/translate-speech-to-text/helloworld/MainPage.xaml.cs#code)]

1. No manipulador `SpeechTranslationFromMicrophone_ButtonClicked` deste arquivo, localize a cadeia de caracteres `YourSubscriptionKey` e substitua-a por sua chave de assinatura.

1. No manipulador `SpeechTranslationFromMicrophone_ButtonClicked`, localize a cadeia de caracteres `YourServiceRegion` e substitua-a pela [região](~/articles/cognitive-services/Speech-Service/regions.md) associada à assinatura. (Por exemplo, use `westus` para a assinatura de avaliação gratuita.)

1. Na barra de menus, escolha **Arquivo** > **Salvar Tudo** para salvar suas alterações.

## <a name="build-and-run-the-application"></a>Compile e execute o aplicativo

Agora, você está pronto para criar e testar seu aplicativo.

1. Na barra de menus, selecione **Compilar** > **Compilar solução** para compilar o aplicativo. Agora, o código deve compilar sem erros.

1. Escolha **Depurar** > **Iniciar Depuração** (ou pressione **F5**) para iniciar o aplicativo. A janela **helloworld** é exibida.

   ![Exemplo de aplicativo de tradução de fala da UWP em C# – início rápido](~/articles/cognitive-services/Speech-Service/media/sdk/qs-translate-speech-uwp-helloworld-window.png)

1. Selecione **Habilitar Microfone** e, quando a solicitação de permissão de acesso for exibida, selecione **Sim**.

   ![Solicitação de permissão de acesso ao microfone](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. Selecione **Traduzir fala da entrada do microfone** e fale uma frase ou uma oração em inglês no microfone do dispositivo. O aplicativo transmite sua fala para os serviço de Fala, que a traduz fala em texto para outro idioma (neste caso, alemão). O serviço de Fala envia o texto traduzido de volta para o aplicativo, que exibe a tradução na janela.

   ![Interface do usuário de tradução de fala](~/articles/cognitive-services/Speech-Service/media/sdk/qs-translate-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [footer](./footer.md)]
