---
title: 'Início Rápido: Reconhecer fala, Unity – Serviço de Fala'
titleSuffix: Azure Cognitive Services
description: Use este guia para criar um aplicativo de conversão de fala em texto com o Unity e o SDK de Fala para Unity. Quando terminar, você pode usar o microfone do computador para transcrever a conversão de fala em texto em tempo real.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 09/17/2019
ms.author: jhakulin
ms.openlocfilehash: a7ac6831fd21fcc0dc425b57f5d73d8c328f9350
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803488"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-unity"></a>Início Rápido: Reconhecer fala com o SDK de Fala para Unity

Guias de início rápido também estão disponíveis para [conversão de texto em fala](quickstart-text-to-speech-csharp-unity.md).

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Use este guia para criar um aplicativo de conversão de fala em texto com o [Unity](https://unity3d.com/) e o SDK de Fala para Unity.
Quando terminar, você pode falar com o dispositivo para transcrever a conversão de fala em texto em tempo real.
Se você é novo no Unity, sugerimos que você estude o [Manual do usuário do Unity](https://docs.unity3d.com/Manual/UnityManual.html) antes de desenvolver o aplicativo.

> [!NOTE]
> O SDK de Fala para Unity dá suporte à Área de Trabalho do Windows (x86 e x64) ou à Plataforma Universal do Windows (x86, x64, ARM/ARM64), ao Android (x86, ARM32/64) e ao iOS (simulador x64, ARM32 e ARM64)

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este projeto, você precisará de:

- [Unity 2018.3 ou posterior](https://store.unity.com/) com o [Unity 2019.1 adicionando suporte ao UWP ARM64](https://blogs.unity3d.com/2019/04/16/introducing-unity-2019-1/#universal).
- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/). A versão 15.9 ou superior do Visual Studio 2017 também é aceitável.
- Para obter suporte ao Windows ARM64, instale as [ferramentas de build opcionais para o ARM64 e o SDK do Windows 10 para ARM64](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/).
- Uma chave de assinatura para o Serviço de Fala. [Obtenha uma gratuitamente](get-started.md).
- Acesso ao microfone do computador.

## <a name="create-a-unity-project"></a>Criar um projeto do Unity

1. Abra o Unity. Se você estiver usando o Unity pela primeira vez, a janela **Hub do Unity** *<version number>* será exibida. (Você também pode abrir o Hub do Unity diretamente para chegar a essa janela.)

   [![Janela do Hub do Unity](media/sdk/qs-csharp-unity-hub.png)](media/sdk/qs-csharp-unity-hub.png#lightbox)
1. Selecione **Novo**. A janela **Criar um novo projeto com o Unity** *<version number>* é exibida.

   [![Criar um novo projeto no Hub do Unity](media/sdk/qs-csharp-unity-create-a-new-project.png)](media/sdk/qs-csharp-unity-create-a-new-project.png#lightbox)
1. Em **Nome do Projeto**, digite **csharp-unity**.
1. Em **Modelos**, se **3D** ainda não estiver selecionado, selecione-o.
1. Em **Local**, selecione ou crie uma pasta na qual salvar o projeto.
1. Selecione **Criar**.

Após alguns instantes, a janela do Editor do Unity é exibida.

## <a name="install-the-speech-sdk"></a>Instalar o SDK de Fala

Para instalar o SDK de Fala para o Unity, siga estas etapas:

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

1. Baixe e abra o [SDK de Fala para Unity](https://aka.ms/csspeech/unitypackage), que é empacotado como um pacote de ativos do Unity (.unitypackage). Quando o pacote de ativos é aberto, a caixa de diálogo **Importar Pacote do Unity** é exibida.

   [![Caixa de diálogo Importar pacote do Unity no Editor do Unity](media/sdk/qs-csharp-unity-01-import.png)](media/sdk/qs-csharp-unity-01-import.png#lightbox)
1. Verifique se todos os arquivos estão selecionados e selecione **Importar**. Depois de alguns instantes, o pacote de ativos do Unity é importado para o projeto.

Para obter mais informações sobre como importar pacotes de ativos para o Unity, confira a [documentação do Unity](https://docs.unity3d.com/Manual/AssetPackages.html).

## <a name="add-ui"></a>Adicionar a interface do usuário

Agora, vamos adicionar uma interface do usuário mínima à nossa cena. Essa interface do usuário consiste em um botão para disparar o reconhecimento de fala e em um campo de texto para exibir o resultado. Na [janela **Hierarquia**](https://docs.unity3d.com/Manual/Hierarchy.html), é mostrada uma cena de exemplo criada pelo Unity com o novo projeto.

1. Na parte superior da janela **Hierarquia**, selecione **Criar** > **IU** > **Botão**.

   Essa ação cria três objetos de jogo que você pode ver na Janela **Hierarquia**: um objeto **Botão**, um objeto **Tela** contendo o botão e um objeto **EventSystem**.

   [![Editor de ambiente do Unity](media/sdk/qs-csharp-unity-editor-window.png)](media/sdk/qs-csharp-unity-editor-window.png#lightbox)

1. [Navegue pela **exibição** Cena](https://docs.unity3d.com/Manual/SceneViewNavigation.html) para ter uma exibição adequada da tela e do botão na [exibição **Cena**](https://docs.unity3d.com/Manual/UsingTheSceneView.html).

1. Na [janela **Inspetor**](https://docs.unity3d.com/Manual/UsingTheInspector.html) (por padrão, à direita), defina as propriedades **Pos X** e **Pos Y** para **0**, de modo que o botão seja centralizado no meio da tela.

1. Na janela **Hierarquia**, selecione **Criar** > **IU** > **Texto** para criar um objeto **Texto**.

1. Na janela **Inspetor**, defina as propriedades **Pos X** e **Pos Y** para **0** e **120** e defina as propriedades **Largura** e **Altura** para **240** e **120**. Esses valores asseguram que o campo de texto e o botão não se sobreponham.

Quando você terminar, a exibição **Cena** deverá ser semelhante a esta captura de tela:

[![Exibição Cena no Editor do Unity](media/sdk/qs-csharp-unity-02-ui-inline.png)](media/sdk/qs-csharp-unity-02-ui-inline.png#lightbox)

## <a name="add-the-sample-code"></a>Adicione o código de amostra

Para adicionar o código de script de exemplo ao projeto do Unity, siga estas etapas:

1. Na [janela do projeto](https://docs.unity3d.com/Manual/ProjectView.html), selecione **Criar** > **Script C#** para adicionar um novo script C#.

   [![Janela do projeto no Editor do Unity](media/sdk/qs-csharp-unity-project-window.png)](media/sdk/qs-csharp-unity-project-window.png#lightbox)
1. Nomeie o script `HelloWorld`.

1. Clique duas vezes em `HelloWorld` para editar o script recém-criado.

   > [!NOTE]
   > Para configurar o editor de código a ser usado pelo Unity para edição, selecione **Editar** > **Preferências** e vá para as preferências de **Ferramentas Externas**. Para obter mais informações, veja o [Manual do Usuário do Unity](https://docs.unity3d.com/Manual/Preferences.html).

1. Substitua o código existente pelo código a seguir:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-unity/Assets/Scripts/HelloWorld.cs#code)]

1. Localize e substitua a cadeia de caracteres `YourSubscriptionKey` pela chave de assinatura do Serviço de Fala.

1. Localize e substitua a cadeia de caracteres `YourServiceRegion` pela [região](regions.md) associada à assinatura. Por exemplo, se você estiver usando a versão de avaliação gratuita, a região é `westus`.

1. Salve as alterações no script.

Agora, retorne ao Editor do Unity e adicione o script como um componente a um dos objetos do jogo:

1. Na janela **Hierarquia**, selecione o objeto **Tela**.

1. Na janela **Inspetor**, selecione o botão **Adicionar Componente**.

   [![Janela Inspetor no Editor do Unity](media/sdk/qs-csharp-unity-inspector-window.png)](media/sdk/qs-csharp-unity-inspector-window.png#lightbox)

1. Na lista suspensa, pesquise pelo script `HelloWorld` que criamos acima e adicione-o. Uma seção **Olá, Mundo (Script)** aparece na janela **Inspetor**, listando duas propriedades não inicializadas, **Texto de Saída** e **Botão Iniciar Reconhecimento**. Essas propriedades de componente do Unity correspondem às propriedades públicas da classe `HelloWorld`.

1. Escolha o seletor de objetos da propriedade **Botão Iniciar Reconhecimento** (o pequeno ícone de círculo à direita da propriedade) e escolha o objeto **Botão** criado anteriormente.

1. Escolha o seletor de objetos da propriedade **Texto de Saída** e escolha o objeto **Texto** que você criou anteriormente.

   > [!NOTE]
   > O botão também tem um objeto de texto aninhado. Lembre-se de não o escolher acidentalmente para a saída de texto (ou renomeie um dos objetos de texto usando o campo **Nome** na janela **Inspetor** para evitar essa confusão).

## <a name="run-the-application-in-the-unity-editor"></a>Executar o aplicativo no Editor do Unity

Agora você está pronto para executar o aplicativo no Editor do Unity.

1. Na barra de ferramentas do Editor do Unity (abaixo da barra de menus), pressione o botão **Reproduzir** (um triângulo apontando para a direita).

1. Vá para a [exibição **Jogo**](https://docs.unity3d.com/Manual/GameView.html) e aguarde que o objeto **Texto** exiba a mensagem **Clique no botão para reconhecer a fala**. (Ele exibe **Novo Texto** quando o aplicativo não foi iniciado ou não está pronto para responder.)

1. Selecione o botão e fale uma frase ou uma sentença em inglês no microfone do computador. Sua fala será transmitida para o Serviço de Fala e transcrita para texto, que aparecerá na exibição **Jogo**.

   [![Exibição Jogo no Editor do Unity](media/sdk/qs-csharp-unity-03-output-inline.png)](media/sdk/qs-csharp-unity-03-output-inline.png#lightbox)

1. Verifique a [janela **Console**](https://docs.unity3d.com/Manual/Console.html) para ver se há mensagens de depuração. Se a janela **Console** não for exibida, vá para a barra de menus e selecione **Janela** > **Geral** > **Console** para exibi-la.

1. Quando terminar de reconhecer a fala, selecione o botão **Reproduzir** na barra de ferramentas do Editor do Unity para interromper o aplicativo.

## <a name="additional-options-to-run-this-application"></a>Opções adicionais para execução desse aplicativo

Esse aplicativo também pode ser implantado como um aplicativo Android, um aplicativo independente do Windows ou um aplicativo UWP.
Para mais informações, confira nosso [repositório de exemplo](https://aka.ms/csspeech/samples). A pasta `quickstart/csharp-unity` descreve a configuração para esses destinos adicionais.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Explorar amostras de C# no GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Consulte também

- [Treinar um modelo para Fala Personalizada](how-to-custom-speech-train-model.md)
