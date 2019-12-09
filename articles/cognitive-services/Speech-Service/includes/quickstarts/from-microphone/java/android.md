---
title: 'Início Rápido: Reconhecer a fala de um microfone, Java (Android) – Serviço de Fala'
titleSuffix: Azure Cognitive Services
description: Saiba como reconhecer fala em Java no Android usando o SDK de Fala
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 11/05/2019
ms.author: wolfma
ms.openlocfilehash: e492381c4ab2f7ab41b5363ed70f9c5c6b12ce99
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74818647"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar:

> [!div class="checklist"]
> * [Criar um Recurso de Fala do Azure](../../../../get-started.md)
> * [Configurar seu ambiente de desenvolvimento](../../../../quickstarts/setup-platform.md?tabs=android)
> * Verificar se você tem acesso a um microfone para captura de áudio

## <a name="create-a-user-interface"></a>Criar uma interface do usuário

Agora criaremos uma interface do usuário básica para o aplicativo. Edite o layout da atividade principal `activity_main.xml`. Inicialmente, o layout inclui uma barra de título com o nome do aplicativo e uma TextView que contém o texto "Olá, Mundo!".

* Selecione o elemento TextView. Altere o atributo da ID no canto superior direito para `hello`.

* Na paleta no canto superior esquerdo da janela `activity_main.xml`, arraste um botão para o espaço vazio acima do texto.

* Nos atributos do botão à direita, no valor para o atributo `onClick`, digite `onSpeechButtonClicked`. Vamos escrever um método com esse nome para manipular o evento do botão. Altere o atributo da ID no canto superior direito para `button`.

* Use o ícone de varinha mágica na parte superior do designer para inferir restrições de layout.

  ![Captura de tela do ícone de varinha mágica](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-10-infer-layout-constraints.png)

O texto e a representação gráfica da sua interface do usuário agora devem ser semelhantes a isto:

![Interface do usuário](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-11-gui.png)

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java/android/from-microphone/app/src/main/res/layout/activity_main.xml)]

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Abra o arquivo de origem `MainActivity.java`. Substitua todo o código nesse arquivo pelo seguinte:

   [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/android/from-microphone/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/quickstart/MainActivity.java#code)]

   * O método `onCreate` inclui código que solicita permissões de microfone e Internet, além de inicializar a associação de plataforma nativa. Só é necessário configurar as associações de plataforma nativa uma vez. Isso deve ser feito no início, durante a inicialização do aplicativo.

   * O método `onSpeechButtonClicked` é, conforme observado anteriormente, o manipulador de clique do botão. Um pressionamento de botão dispara a transcrição de fala em texto.

1. No mesmo arquivo, substitua a cadeia de caracteres `YourSubscriptionKey` por sua chave de assinatura.

1. Substitua também a cadeia de caracteres `YourServiceRegion` pela [região](~/articles/cognitive-services/Speech-Service/regions.md) associada à sua assinatura. Por exemplo, use `westus` para a assinatura de avaliação gratuita.

## <a name="build-and-run-the-app"></a>Compilar e executar o aplicativo

1. Conecte o dispositivo Android ao PC de desenvolvimento. Verifique se você habilitou o [modo de desenvolvimento e a depuração de USB](https://developer.android.com/studio/debug/dev-options) no dispositivo.

1. Para compilar o aplicativo, selecione Ctrl+F9 ou **Compilar** > **Criar Projeto** na barra de menus.

1. Para iniciar o aplicativo, selecione Shift+F10 ou **Executar** > **Executar 'aplicativo'** .

1. Na janela de destino da implantação exibida, selecione seu dispositivo Android.

   ![Captura de tela da janela Selecionar Destino da Implantação](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-12-deploy.png)

Selecione o botão no aplicativo para iniciar uma seção de reconhecimento de fala. Os próximos 15 segundos de fala em inglês serão enviados para o serviço de Fala e transcritos. O resultado é exibido no aplicativo Android e na janela logcat no Android Studio.

![Captura de tela do aplicativo Android](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-13-gui-on-device.png)

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [footer](./footer.md)]

