---
title: 'Início Rápido: Reconhecer fala, Swift – Serviço de Fala'
titleSuffix: Azure Cognitive Services
description: Saiba como reconhecer fala em Swift no iOS usando o SDK de Fala
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: chlandsi
ms.openlocfilehash: 0a62e670587f271eb9a1beba034886c5f95976a7
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72327740"
---
# <a name="quickstart-recognize-speech-in-swift-on-ios-by-using-the-speech-sdk"></a>Início Rápido: Reconhecer fala em Swift no iOS usando o SDK de Fala

Também estão disponíveis inícios rápidos para [sintetização de voz](quickstart-text-to-speech-swift-ios.md).

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Neste artigo, você aprenderá a criar um aplicativo iOS em Swift usando o SDK de Fala dos Serviços Cognitivos do Azure para transcrever fala gravada por um microfone em texto.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, você precisará do seguinte:

* Uma [chave de assinatura](get-started.md) para o Serviço de Fala.
* Um computador macOS com [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) ou posterior e [CocoaPods](https://cocoapods.org/) instalado.

## <a name="get-the-speech-sdk-for-ios"></a>Obter o SDK de Fala para iOS

[!INCLUDE [License notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Este tutorial não funcionará com uma versão do SDK anterior à 1.6.0.

O SDK de Fala dos Serviços Cognitivos para iOS é distribuído como um quadro de estrutura. Ele pode ser usado em projetos do Xcode como um [CocoaPod](https://cocoapods.org/) ou baixados em https://aka.ms/csspeech/macosbinary e vinculados manualmente. Este artigo usa um CocoaPod.

## <a name="create-an-xcode-project"></a>Criar um projeto do Xcode

Inicie o Xcode e inicie um novo projeto selecionando **Arquivo** > **Novo** > **Projeto**.
Na caixa de diálogo da seleção de modelo, selecione o modelo **Aplicativo de Exibição Única do iOS**.

Nas caixas de diálogo posteriores, faça as seleções a seguir.

1. Na caixa de diálogo **Opções de Projeto**:
    1. Insira um nome para o aplicativo de início rápido, por exemplo *helloworld*.
    1. Insira um nome de organização apropriado e um identificador de organização se você já tiver uma conta de desenvolvedor da Apple. Para fins de teste, use um nome como *testorg*. Para assinar o aplicativo, você precisa de um perfil de provisionamento adequado. Para obter mais informações, confira o [Site de desenvolvedor da Apple](https://developer.apple.com/).
    1. Verifique se o **Swift** está escolhido como a linguagem do projeto.
    1. Desabilite as caixas de seleção para usar storyboards e criar um aplicativo baseado no documento. A interface do usuário simples para o aplicativo de exemplo é criada de forma programática.
    1. Desmarque todas as caixas de seleção dos testes e dos dados principais.
1. Selecione um diretório do projeto:
    1. Escolha um diretório no qual colocar o projeto. Essa etapa cria, no diretório escolhido, um diretório helloworld que contém todos os arquivos para o projeto do Xcode.
    1. Desabilite a criação de um repositório Git para este projeto de exemplo.
1. O aplicativo também precisa declarar o uso do microfone no arquivo `Info.plist`. Selecione o arquivo na visão geral e adicione a chave **Privacidade – Descrição de Uso do Microfone**, com um valor, como *Microfone é necessário para o reconhecimento de fala*.

    ![Configurações no Info.plist](media/sdk/qs-swift-ios-info-plist.png)

1. Fechar o projeto do Xcode. Você usará uma instância diferente dele depois de configurar o CocoaPods.

## <a name="add-the-sample-code"></a>Adicione o código de amostra

1. Coloque um novo arquivo de cabeçalho com o nome `MicrosoftCognitiveServicesSpeech-Bridging-Header.h` no diretório helloworld dentro do projeto helloworld. Cole o código a seguir dentro dele:

   [!code-objectivec[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift-ios/helloworld/helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h#code)]

1. Adicione o caminho relativo `helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h` ao cabeçalho ponte das configurações de projeto do Swift para o destino helloworld no campo **Cabeçalho ponte de Objective-C**.

   ![Propriedades do cabeçalho](media/sdk/qs-swift-ios-bridging-header.png)

1. Substitua o conteúdo do arquivo `AppDelegate.swift` gerado automaticamente pelo seguinte código:

   [!code-swift[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift-ios/helloworld/helloworld/AppDelegate.swift#code)]
1. Substitua o conteúdo do arquivo `ViewController.swift` gerado automaticamente pelo seguinte código:

   [!code-swift[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift-ios/helloworld/helloworld/ViewController.swift#code)]
1. Em `ViewController.swift`, substitua a cadeia de caracteres `YourSubscriptionKey` pela chave de assinatura.
1. Substitua a cadeia de caracteres `YourServiceRegion` pela [região](regions.md) associada à sua assinatura. Por exemplo, use `westus` para a assinatura de avaliação gratuita.

## <a name="install-the-sdk-as-a-cocoapod"></a>Instalar o SDK como um CocoaPod

1. Instale o gerenciador de dependência do CocoaPod conforme descrito nas [instruções de instalação](https://guides.cocoapods.org/using/getting-started.html).
1. Acesse o diretório do aplicativo de exemplo, que é helloworld. Coloque um arquivo de texto com o nome *Podfile* e o seguinte conteúdo nesse diretório:

   [!code-ruby[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift-ios/helloworld/Podfile)]
1. Acesse o diretório helloworld em um terminal e execute o comando `pod install`. Esse comando gera um workspace do Xcode `helloworld.xcworkspace` que contém o aplicativo de exemplo e o SDK de Fala como uma dependência. Esse workspace será usado nas etapas a seguir.

## <a name="build-and-run-the-sample"></a>Criar e executar a amostra

1. Abra o workspace `helloworld.xcworkspace` no Xcode.
1. Torne a saída de depuração visível selecionando **Exibição** > **Área de Depuração** > **Ativar Console**.
1. Escolha o simulador de iOS ou um dispositivo iOS conectado ao computador de desenvolvimento como o destino para o aplicativo da lista no menu **Produto** > **Destino**.
1. Compile e execute o código de exemplo no simulador do iOS selecionando **Produto** > **Executar** no menu. Também é possível selecionar o botão **Reproduzir**.
1. Depois que selecionar o botão **Reconhecer** no aplicativo e disser algumas palavras, você deverá ver o texto que falou na parte inferior da tela.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Explorar nossos exemplos no GitHub](https://aka.ms/csspeech/samples)
