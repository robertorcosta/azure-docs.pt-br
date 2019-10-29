---
title: 'Início Rápido: Reconhecer fala, Java (Android) – Serviço de Fala'
titleSuffix: Azure Cognitive Services
description: Saiba como reconhecer fala em Java no Android usando o SDK de Fala
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: wolfma
ms.openlocfilehash: 2fb7bc0851868f6551ed57d742c4005550caec99
ms.sourcegitcommit: 9a4296c56beca63430fcc8f92e453b2ab068cc62
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/20/2019
ms.locfileid: "72675553"
---
# <a name="quickstart-recognize-speech-in-java-on-android-by-using-the-speech-sdk"></a>Início Rápido: Reconhecer fala em Java no Android usando SDK de Fala

Guias de início rápido também estão disponíveis para a [sintetização de voz](quickstart-text-to-speech-java-android.md) e o [assistente virtual que tem como prioridade o uso de voz](quickstart-virtual-assistant-java-android.md).

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Neste artigo, você aprenderá a desenvolver um aplicativo Java para Android usando o SDK de Fala dos Serviços Cognitivos do Azure para converter uma fala em texto.

O aplicativo baseia-se no Pacote do Maven do SDK de Fala e no Android Studio 3.3. Atualmente, o SDK de Fala é compatível com dispositivos Android que têm processadores compatíveis com Intel x86/x64 e ARM de 32 ou 64 bits.

> [!NOTE]
> Para o SDK dos Dispositivos de Fala e o dispositivo Roobo, confira [SDK dos Dispositivos de Fala](speech-devices-sdk.md).

## <a name="prerequisites"></a>Pré-requisitos

Você precisará de uma chave de assinatura dos Serviços de Fala para concluir este início rápido. Obtenha uma gratuitamente. Para obter mais informações, confira [Experimentar os Serviços de Fala gratuitamente](get-started.md).

## <a name="create-and-configure-a-project"></a>Criar e configurar um projeto

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-android-create-proj.md)]

## <a name="create-a-user-interface"></a>Criar uma interface do usuário

Agora criaremos uma interface do usuário básica para o aplicativo. Edite o layout da atividade principal `activity_main.xml`. Inicialmente, o layout inclui uma barra de título com o nome do aplicativo e uma TextView que contém o texto "Olá, Mundo!".

* Selecione o elemento TextView. Altere o atributo da ID no canto superior direito para `hello`.

* Na paleta no canto superior esquerdo da janela `activity_main.xml`, arraste um botão para o espaço vazio acima do texto.

* Nos atributos do botão à direita, no valor para o atributo `onClick`, digite `onSpeechButtonClicked`. Vamos escrever um método com esse nome para manipular o evento do botão. Altere o atributo da ID no canto superior direito para `button`.

* Use o ícone de varinha mágica na parte superior do designer para inferir restrições de layout.

  ![Captura de tela do ícone de varinha mágica](media/sdk/qs-java-android-10-infer-layout-constraints.png)

O texto e a representação gráfica da sua interface do usuário agora devem ser semelhantes a isto:

![Interface do usuário](media/sdk/qs-java-android-11-gui.png)

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java-android/app/src/main/res/layout/activity_main.xml)]

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Abra o arquivo de origem `MainActivity.java`. Substitua todo o código nesse arquivo pelo seguinte:

   [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java-android/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/quickstart/MainActivity.java#code)]

   * O método `onCreate` inclui código que solicita permissões de microfone e Internet, além de inicializar a associação de plataforma nativa. Só é necessário configurar as associações de plataforma nativa uma vez. Isso deve ser feito no início, durante a inicialização do aplicativo.

   * O método `onSpeechButtonClicked` é, conforme observado anteriormente, o manipulador de clique do botão. Um pressionamento de botão dispara a transcrição de fala em texto.

1. No mesmo arquivo, substitua a cadeia de caracteres `YourSubscriptionKey` por sua chave de assinatura.

1. Substitua também a cadeia de caracteres `YourServiceRegion` pela [região](regions.md) associada à sua assinatura. Por exemplo, use `westus` para a assinatura de avaliação gratuita.

## <a name="build-and-run-the-app"></a>Compilar e executar o aplicativo

1. Conecte o dispositivo Android ao PC de desenvolvimento. Verifique se você habilitou o [modo de desenvolvimento e a depuração de USB](https://developer.android.com/studio/debug/dev-options) no dispositivo.

1. Para compilar o aplicativo, selecione Ctrl+F9 ou **Compilar** > **Criar Projeto** na barra de menus.

1. Para iniciar o aplicativo, selecione Shift+F10 ou **Executar** > **Executar 'aplicativo'** .

1. Na janela de destino da implantação exibida, selecione seu dispositivo Android.

   ![Captura de tela da janela Selecionar Destino da Implantação](media/sdk/qs-java-android-12-deploy.png)

Selecione o botão no aplicativo para iniciar uma seção de reconhecimento de fala. Os próximos 15 segundos de fala em inglês serão enviados para os Serviços de Fala e serão transcritos. O resultado é exibido no aplicativo Android e na janela logcat no Android Studio.

![Captura de tela do aplicativo Android](media/sdk/qs-java-android-13-gui-on-device.png)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Explorar amostras de Java no GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Consulte também

- [Personalizar modelos acústicos](how-to-customize-acoustic-models.md)
- [Personalizar modelos de linguagem](how-to-customize-language-model.md)
