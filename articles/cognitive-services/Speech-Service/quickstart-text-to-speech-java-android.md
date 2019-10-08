---
title: 'Início Rápido: Sintetizar fala, Java (Android) – Serviço de Fala'
titleSuffix: Azure Cognitive Services
description: Saiba como sintetizar fala em Java no Android usando o SDK de Fala
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 09/19/2019
ms.author: yulili
ms.openlocfilehash: 17fb0c1a4ba5495506ceb658eb61d0ac2959caf8
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803758"
---
# <a name="quickstart-synthesize-speech-in-java-on-android-by-using-the-speech-sdk"></a>Início Rápido: Sintetizar fala em Java no Android usando SDK de Fala

Guias de início rápido também estão disponíveis para o [reconhecimento de fala](quickstart-java-android.md) e o [assistente virtual que tem como prioridade o uso de voz](quickstart-virtual-assistant-java-android.md).

Neste artigo, você aprenderá a desenvolver um aplicativo Java para Android usando SDK de Fala dos Serviços Cognitivos para sintetizar fala com base em texto.
O aplicativo baseia-se no Pacote Maven do SDK de Fala, versão 1.7.0, bem como no Android Studio 3.3.
O SDK de Fala é atualmente compatível com dispositivos Android que têm processadores ARM de 32/64 bits e Intel x86/x64 compatíveis.

> [!NOTE]
> Para o SDK dos Dispositivos de Fala e o dispositivo Roobo, confira [SDK dos Dispositivos de Fala](speech-devices-sdk.md).

## <a name="prerequisites"></a>Pré-requisitos

Você precisa de uma chave de assinatura dos Serviços de Fala para concluir este Início Rápido. Obtenha uma gratuitamente. Consulte [Experimentar os Serviços de Fala gratuitamente](get-started.md) para obter mais detalhes.

## <a name="create-and-configure-a-project"></a>Criar e configurar um projeto

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-android-create-proj.md)]

## <a name="create-user-interface"></a>Criar interface do usuário

Vamos criar uma interface do usuário básica para o aplicativo. Edite o layout da atividade principal `activity_main.xml`. Inicialmente, o layout inclui uma barra de título com o nome do aplicativo e um TextView contendo o texto "Olá, Mundo!".

1. Clique no elemento TextView. Altere o atributo da ID desse elemento no canto superior direito para `outputMessage` e arraste-o para a tela inferior. Exclua o texto contido nele.

1. Na Paleta, no canto superior esquerdo da janela `activity_main.xml`, arraste um botão para o espaço vazio acima do texto.

1. Nos atributos do botão à direita, no valor para o atributo `onClick`, digite `onSpeechButtonClicked`. Vamos escrever um método com esse nome para manipular o evento do botão.  Altere o atributo da ID no canto superior direito para `button`.

1. Arraste um texto sem formatação para o espaço acima do botão; altere o atributo de ID dele para `speakText` e altere o atributo de texto para `Hi there!`.

1. Use o ícone de varinha mágica na parte superior do designer para inferir restrições de layout.


    ![Captura de tela do ícone de varinha mágica](media/sdk/qs-java-android-10-infer-layout-constraints.png)

O texto e a representação gráfica da sua interface do usuário agora devem ser semelhantes a isto:

![](media/sdk/qs-java-android-11-2-tts-gui.png)

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/java-android/app/src/main/res/layout/activity_main.xml)]

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Abra o arquivo de origem `MainActivity.java`. Substitua todo o código nesse arquivo pelo seguinte.

   [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/java-android/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/quickstart/MainActivity.java#code)]

   * O método `onSpeechButtonClicked` é, conforme observado anteriormente, o manipulador de clique do botão. Um pressionamento de botão dispara a síntese de fala.

1. No mesmo arquivo, substitua a cadeia de caracteres `YourSubscriptionKey` por sua chave de assinatura.

1. Substitua também a cadeia de caracteres `YourServiceRegion` pela [região](regions.md) associada à assinatura (por exemplo, `westus` para a assinatura de avaliação gratuita).

## <a name="build-and-run-the-app"></a>Compilar e executar o aplicativo

1. Conecte o dispositivo Android ao PC de desenvolvimento. Verifique se você habilitou o [modo de desenvolvimento e depuração de USB](https://developer.android.com/studio/debug/dev-options) no dispositivo. Como alternativa, crie um [Android Emulator](https://developer.android.com/studio/run/emulator).

1. Para compilar o aplicativo, pressione Ctrl+F9 ou escolha **Construir** > **Criar um Projeto** na barra de menus.

1. Para inicializar o aplicativo, pressione Shift+F10 ou escolha **Executar** > **Executar 'aplicativo'** .

1. Na janela de destino da implantação exibida, escolha seu dispositivo Android ou Android Emulator.

   ![Captura de tela da janela Selecionar Destino da Implantação](media/sdk/qs-java-android-12-deploy.png)

Insira um texto e pressione o botão no aplicativo para iniciar uma seção de síntese de fala. Você ouvirá o áudio sintetizado do alto-falante padrão e verá as informações de `speech synthesis succeeded` na tela.

![Captura de tela do aplicativo Android](media/sdk/qs-java-android-13-2-gui-on-device-tts.png)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Explorar amostras de Java no GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Consulte também

- [Personalizar fontes de voz](how-to-customize-voice-font.md)
- [Amostras de memorando de voz](record-custom-voice-samples.md)
