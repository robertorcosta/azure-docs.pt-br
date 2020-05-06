---
title: 'Início Rápido: Sintetizar fala, C# (Unity) – Serviço de Fala'
titleSuffix: Azure Cognitive Services
description: Use este guia para criar um aplicativo de conversão de texto em fala com o Unity e o SDK de Fala para Unity. Quando terminar, você poderá sintetizar em tempo real uma fala a partir de texto no alto-falante do seu dispositivo.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/04/2020
ms.author: yinhew
ms.openlocfilehash: 0934738c557ac6d26867546783797cf5d2b75056
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81275452"
---
> [!NOTE]
> O Unity dá suporte à Área de Trabalho do Windows (x86 e x64) ou à Plataforma Universal do Windows (x86, x64, ARM/ARM64), ao Android (x86, ARM32/64) e ao iOS (simulador x64, ARM32 e ARM64).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, é preciso:

> [!div class="checklist"]
> * [Criar um Recurso de Fala do Azure](../../../../get-started.md)
> * [Configurar seu ambiente de desenvolvimento e criar um projeto vazio](../../../../quickstarts/setup-platform.md?tabs=unity&pivots=programming-language-csharp)

## <a name="add-a-ui"></a>Adicionar uma interface do usuário

Adicionamos uma interface do usuário mínima à nossa cena, que consiste em um campo de entrada para inserir o texto para síntese, um botão para disparar a sintetização de voz e um campo de texto para exibir o resultado.

* Na [janela Hierarquia](https://docs.unity3d.com/Manual/Hierarchy.html) (por padrão, à esquerda), é mostrada uma cena de exemplo criada pelo Unity com o novo projeto.
* Selecione o botão **Criar** na parte superior da janela **Hierarquia** e selecione **Interface do Usuário** > **Campo de Entrada**.
* Essa opção cria três objetos de jogo que podem ser vistos na janela **Hierarquia**: um objeto **Campo de Entrada** aninhado em um objeto **Tela** e um objeto **EventSystem**.
* [Navegue pela exibição de Cena](https://docs.unity3d.com/Manual/SceneViewNavigation.html) para ter uma exibição adequada da tela e do campo de entrada na [exibição de Cena](https://docs.unity3d.com/Manual/UsingTheSceneView.html).
* Selecione o objeto **Campo de Entrada** na janela **Hierarquia** para exibir as configurações dele na [janela Inspetor](https://docs.unity3d.com/Manual/UsingTheInspector.html) (por padrão, à direita).
* Defina as propriedades **Pos X** e **Pos Y** como **0**, de modo que o campo de entrada fique centralizado no meio da tela.
* Selecione o botão **Criar** na parte superior da janela **Hierarquia** novamente. Selecione **Interface do Usuário** > **Botão** para criar um botão.
* Selecione o objeto **Botão** na janela **Hierarquia** para exibir as configurações dele na [janela Inspetor](https://docs.unity3d.com/Manual/UsingTheInspector.html) (por padrão, à direita).
* Defina as propriedades **Pos X** e **Pos Y** como **0** e **-48**. Defina as propriedades **Largura** e **Altura** como **160** e **30** para garantir que o botão e o campo de entrada não se sobreponham.
* Selecione o botão **Criar** na parte superior da janela **Hierarquia** novamente. Selecione **Interface do Usuário** > **Texto** para criar um campo de texto.
* Selecione o objeto **Texto** na janela **Hierarquia** para exibir as configurações dele na [janela Inspetor](https://docs.unity3d.com/Manual/UsingTheInspector.html) (por padrão, à direita).
* Defina as propriedades **Pos X** e **Pos Y** como **0** e **80**. Defina as propriedades **Largura** e **Altura** como **320** e **80** para garantir que o campo de texto e o campo de entrada não se sobreponham.
* Selecione o botão **Criar** na parte superior da janela **Hierarquia** novamente. Selecione **Áudio** > **Fonte de Áudio** para criar uma fonte de áudio.

Quando você terminar, a interface do usuário deverá ser semelhante a esta captura de tela:

[![Captura de tela da interface do usuário do Início Rápido no Editor do Unity](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-unity-ui-inline.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-unity-ui-expanded.png#lightbox)

## <a name="add-the-sample-code"></a>Adicione o código de amostra

1. Na [janela Projeto](https://docs.unity3d.com/Manual/ProjectView.html) (por padrão, no canto inferior esquerdo), selecione o botão **Criar** e, em seguida, **Script C#** . Nomeie o script `HelloWorld`.

1. Edite o script clicando duas vezes nele.

   > [!NOTE]
   > Configure qual editor de código é iniciado selecionando **Editar** > **Preferências**. Para obter mais informações, veja o [Manual do Usuário do Unity](https://docs.unity3d.com/Manual/Preferences.html).

1. Substitua todo o código pelo seguinte:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/unity/text-to-speech/Assets/Scripts/HelloWorld.cs#code)]

1. Localize e substitua a cadeia de caracteres `YourSubscriptionKey` pela sua chave de assinatura do serviço de Fala.

1. Localize e substitua a cadeia de caracteres `YourServiceRegion` pela [região](~/articles/cognitive-services/Speech-Service/regions.md) associada à assinatura. Por exemplo, a região será `westus` se você usar a avaliação gratuita.

1. Salve as alterações no script.

1. De volta ao Editor do Unity, adicione o script como um componente a um dos objetos de jogo.

   * Selecione o objeto **Tela** na janela **Hierarquia** para abrir a configuração na [janela Inspetor](https://docs.unity3d.com/Manual/UsingTheInspector.html) (por padrão, à direita).
   * Selecione o botão **Adicionar Componente** na janela **Inspetor**. Em seguida, pesquise o script `HelloWorld` que criamos anteriormente e adicione-o.
   * O componente HelloWorld tem quatro propriedades não inicializadas, **Texto de Saída**, **Campo de Entrada**, **Botão de Fala** e **Fonte de Áudio**, que correspondem às propriedades públicas da classe `HelloWorld`.
     Para conectá-las, selecione o seletor de objetos (o ícone de círculo pequeno à direita da propriedade). Selecione os objetos de texto e botão criados anteriormente.

     > [!NOTE]
     > O campo de entrada e o botão também têm um objeto de texto aninhado. Verifique se você não o selecionou acidentalmente para a saída de texto. Outra opção é renomear os objetos de texto que usam o campo **Nome** na janela **Inspetor** para evitar essa confusão.

## <a name="run-the-application-in-the-unity-editor"></a>Executar o aplicativo no Editor do Unity

* Selecione o botão **Reproduzir** na barra de ferramentas do Editor do Unity que está abaixo da barra de menus.
* Depois que o aplicativo for iniciado, insira um texto no campo de entrada e selecione o botão. Seu texto é transmitido para o serviço de Fala e sintetizado em fala. Depois, é reproduzido no alto-falante.

  [![Captura de tela do início rápido em execução na janela Jogo do Unity](~/articles/cognitive-services/speech-service/media/sdk/qs-tts-csharp-unity-output-inline.png)](~/articles/cognitive-services/speech-service/media/sdk/qs-tts-csharp-unity-output-expanded.png#lightbox)

* Verifique a [janela Console](https://docs.unity3d.com/Manual/Console.html) para ver se há mensagens de depuração.

## <a name="additional-options-to-run-this-application"></a>Opções adicionais para execução desse aplicativo

Esse aplicativo também pode ser implantado no Android como um aplicativo independente do Windows ou um aplicativo UWP.
Confira o [repositório de amostras](https://aka.ms/csspeech/samples) na pasta quickstart/csharp-unity que descreve a configuração para esses destinos adicionais.

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [Speech synthesis basics](../../text-to-speech-next-steps.md)]

## <a name="see-also"></a>Confira também

- [Criar uma Voz Personalizada](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Registrar amostras de voz personalizada](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
