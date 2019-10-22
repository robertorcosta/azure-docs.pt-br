---
title: 'Início Rápido: Reconhecer fala, Objective-C – Serviço de Fala'
titleSuffix: Azure Cognitive Services
description: Saiba como reconhecer uma fala em Objective-C no iOS usando o SDK de Fala
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: chlandsi
ms.openlocfilehash: f943f47cdef901f80aa455d3d1e02a753e0f06e4
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72327768"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-ios-by-using-the-speech-sdk"></a>Início Rápido: Reconhecer uma fala em Objective-C no iOS usando o SDK de Fala

Também estão disponíveis inícios rápidos para [sintetização de voz](quickstart-text-to-speech-objectivec-ios.md).

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Neste artigo, você aprenderá a criar um aplicativo iOS em Objective-C usando o SDK de Fala dos Serviços Cognitivos do Azure para transcrever uma fala em texto de um microfone ou de um arquivo com o áudio gravado.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, você precisará do seguinte:

* Uma [chave de assinatura](get-started.md) para o Serviço de Fala.
* Um computador macOS com [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) ou posterior.
* O destino definido como iOS versão 9.3 ou posterior.

## <a name="get-the-speech-sdk-for-ios"></a>Obter o SDK de Fala para iOS

[!INCLUDE [License notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Atualmente, o SDK de Fala dos Serviços Cognitivos para iOS é distribuído como uma estrutura Cocoa.
Ele pode ser baixado [neste site](https://aka.ms/csspeech/iosbinary). Baixe o arquivo para seu diretório inicial.

## <a name="create-an-xcode-project"></a>Criar um projeto do Xcode

Inicie o Xcode e inicie um novo projeto selecionando **Arquivo** > **Novo** > **Projeto**.
Na caixa de diálogo da seleção de modelo, selecione o modelo **Aplicativo de Exibição Única do iOS**.

Nas caixas de diálogo posteriores, faça as seleções a seguir.

1. Na caixa de diálogo **Opções de Projeto**:
    1. Insira um nome para o aplicativo de início rápido, por exemplo *helloworld*.
    1. Insira um nome de organização apropriado e um identificador da organização, caso já tenha uma conta de desenvolvedor da Apple. Para fins de teste, use um nome como *testorg*. Para assinar o aplicativo, você precisa de um perfil de provisionamento adequado. Para obter mais informações, confira o [Site de desenvolvedor da Apple](https://developer.apple.com/).
    1. Verifique se **Objective-C** está selecionado como a linguagem do projeto.
    1. Desmarque todas as caixas de seleção dos testes e dos dados principais.

    ![Configurações do projeto](media/sdk/qs-objectivec-project-settings.png)

1. Selecione um diretório do projeto:
   1. Escolha seu diretório base no qual colocar o projeto. Essa etapa cria um diretório helloworld no diretório base que contém todos os arquivos para o projeto do Xcode.
   1. Desabilite a criação de um repositório Git para este projeto de exemplo.
   1. Ajuste os caminhos para o SDK na tela de configurações do projeto.
      1. Na guia **Geral** no cabeçalho **Binários Inseridos**, adicione a biblioteca do SDK como uma estrutura selecionando **Adicionar binários inseridos** > **Adicionar outros**. Acesse o diretório base e selecione o arquivo `MicrosoftCognitiveServicesSpeech.framework`. Essa ação adiciona a biblioteca do SDK ao cabeçalho **Estrutura e Bibliotecas Vinculadas** automaticamente.
         ![Estrutura adicionada](media/sdk/qs-objectivec-framework.png)
      1. Acesse a guia **Configurações de Build** e selecione a configuração **Todas**.
      1. Adicione o diretório $(SRCROOT)/.. aos **Caminhos de Pesquisa da Estrutura** no título **Caminhos de Pesquisa**.

      ![Configuração Caminho de Pesquisa da Estrutura](media/sdk/qs-objectivec-framework-search-paths.png)

## <a name="set-up-the-ui"></a>Configurar a interface do usuário

O aplicativo de exemplo tem uma interface do usuário muito simples. Ela tem dois botões para iniciar o reconhecimento de fala da entrada do microfone ou do arquivo e um rótulo de texto para exibir o resultado. A interface do usuário é configurada no `Main.storyboard` como parte do projeto. Abra a exibição XML do storyboard clicando com o botão direito do mouse na entrada `Main.storyboard` da árvore do projeto e selecionando **Abrir como** > **Código-Fonte**.

Substitua o XML gerado automaticamente por este código:

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-ios/helloworld/helloworld/Base.lproj/Main.storyboard)]

## <a name="add-the-sample-code"></a>Adicione o código de amostra

1. Baixe o [arquivo WAV de exemplo](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-speech-sdk/f9807b1079f3a85f07cbb6d762c6b5449d536027/samples/cpp/windows/console/samples/whatstheweatherlike.wav) clicando com o botão direito do mouse no link e selecionando **Salvar destino como**.
   Adicione o arquivo wav ao projeto como um recurso arrastando-o de uma janela do Finder para o nível raiz da exibição do Projeto.
   Selecione **Concluir** na caixa de diálogo a seguir sem alterar as configurações.
1. Substitua o conteúdo do arquivo `ViewController.m` gerado automaticamente pelo seguinte código:

   [!code-objectivec[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-ios/helloworld/helloworld/ViewController.m#code)]
1. Substitua a cadeia de caracteres `YourSubscriptionKey` pela chave de assinatura.
1. Substitua a cadeia de caracteres `YourServiceRegion` pela [região](regions.md) associada à sua assinatura. Por exemplo, use `westus` para a assinatura de avaliação gratuita.
1. Adicione a solicitação de acesso do microfone. Clique com o botão direito do mouse na entrada `Info.plist` da árvore do projeto e selecione **Abrir como** > **Código-fonte**. Adicione as linhas a seguir à seção `<dict>` e, em seguida, salve o arquivo.

    ```xml
    <key>NSMicrophoneUsageDescription</key>
    <string>Need microphone access for speech recognition from microphone.</string>
    ```

## <a name="build-and-run-the-sample"></a>Criar e executar a amostra

1. Torne a saída de depuração visível selecionando **Exibição** > **Área de Depuração** > **Ativar Console**.
1. Escolha o simulador de iOS ou um dispositivo iOS conectado ao computador de desenvolvimento como o destino para o aplicativo da lista no menu **Produto** > **Destino**.
1. Compile e execute o código de exemplo no simulador do iOS selecionando **Produto** > **Executar** no menu. Também é possível selecionar o botão **Reproduzir**.
1. Depois de selecionar o botão **Reconhecer (Arquivo)** no aplicativo, você deverá ver o conteúdo do arquivo de áudio "Como está o clima?" na parte inferior da tela.

   ![Aplicativo iOS simulado](media/sdk/qs-objectivec-simulated-app.png)

1. Depois de selecionar o botão **Reconhecer (Microfone)** no aplicativo e dizer algumas palavras, você deverá ver o texto falado por você na parte inferior da tela.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Explorar amostras de Objective-C no GitHub](https://aka.ms/csspeech/samples)
