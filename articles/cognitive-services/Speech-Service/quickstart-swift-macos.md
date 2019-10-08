---
title: 'Início Rápido: Reconhecer fala, Swift – Serviço de Fala'
titleSuffix: Azure Cognitive Services
description: Saiba como reconhecer fala em Swift no macOS usando o SDK de Fala
services: cognitive-services
author: cbasoglu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 06/28/2019
ms.author: cbasoglu
ms.openlocfilehash: 5ccfecc2bc61940f72844f43ec81826536cfe89a
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803222"
---
# <a name="quickstart-recognize-speech-in-swift-on-macos-using-the-speech-sdk"></a>Início Rápido: Reconhecer fala em Swift no macOS usando o SDK de Fala

Também estão disponíveis inícios rápidos para [sintetização de voz](quickstart-text-to-speech-swift-macos.md).

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Neste artigo, você aprenderá a criar um aplicativo macOS em Swift usando o SDK de Fala dos Serviços Cognitivos para transcrever uma fala de um microfone.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, aqui está uma lista de pré-requisitos:

* Uma [chave de assinatura](get-started.md) para o Serviço de Fala.
* Um computador macOS com [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) ou posterior e [CocoaPods](https://cocoapods.org/) instalado.

## <a name="get-the-speech-sdk-for-macos"></a>Obter o SDK de Fala para macOS

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Observe que este tutorial não funcionará com uma versão do SDK anterior a 1.6.0.

O SDK de Fala dos Serviços Cognitivos para macOS é distribuído como um quadro de estrutura.
Ele pode ser usado em projetos do Xcode como um [CocoaPod](https://cocoapods.org/) ou baixados de https://aka.ms/csspeech/macosbinary e vinculados manualmente. Este guia usa um CocoaPod.

## <a name="create-an-xcode-project"></a>Criar um projeto do Xcode

Inicie o Xcode e iniciar um novo projeto clicando em **Arquivo** > **Novo** > **Projeto**.
Na caixa de diálogo de seleção de modelo, escolha o modelo "Aplicativo Cocoa".

Nas caixas de diálogo que seguem, faça as seguintes seleções:

1. Caixa de diálogo Opções de Projeto
    1. Insira um nome para o aplicativo de início rápido, por exemplo `helloworld`.
    1. Insira um nome de organização apropriado e um identificador de organização, se você já tiver uma conta de desenvolvedor da Apple. Para fins de teste, você pode escolher qualquer nome, como `testorg`. Para assinar o aplicativo, você precisa de um perfil de provisionamento adequado. Veja o [site do desenvolvedor da Apple](https://developer.apple.com/) para obter detalhes.
    1. Verifique se Swift está escolhido como a linguagem do projeto.
    1. Desabilite as caixas de seleção para usar storyboards e criar um aplicativo baseado no documento. A interface de usuário simples para o aplicativo de exemplo será criada por meio de programação.
    1. Desabilite todas as caixas de seleção para testes e dados principais.
1. Selecionar diretório do projeto
    1. Escolha um diretório no qual colocar o projeto. Isso cria um diretório `helloworld` no diretório escolhido que contém todos os arquivos para o projeto do Xcode.
    1. Desabilite a criação de um repositório Git para este projeto de exemplo.
1. Defina os direitos de acesso de rede e de microfone. Clique no nome do aplicativo na primeira linha na visão geral à esquerda para acessar a configuração do aplicativo e, em seguida, escolha a guia "Recursos".
    1. Habilite a configuração de "Área restrita de aplicativo" para o aplicativo.
    1. Habilite as caixas de seleção para acesso de "Conexões de saída" e "Microfone".
    ![Configurações de área restrita](media/sdk/qs-swift-macos-sandbox.png)
1. O aplicativo também precisa declarar o uso do microfone no arquivo `Info.plist`. Clique no arquivo na visão geral e adicione a chave "Privacidade - Descrição de uso do microfone", com um valor, como "Microfone é necessário para o reconhecimento de fala".
    ![Configurações no Info.plist](media/sdk/qs-swift-macos-info-plist.png)
1. Fechar o projeto do Xcode. Você usará uma instância diferente dele depois de configurar o CocoaPods.

## <a name="add-the-sample-code"></a>Adicione o código de amostra

1. Coloque um novo arquivo de cabeçalho com o nome `MicrosoftCognitiveServicesSpeech-Bridging-Header.h` no diretório `helloworld` dentro do projeto helloworld e cole o seguinte código nele:

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift-macos/helloworld/helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h#code)]
1. Adicione o caminho relativo `helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h` ao cabeçalho ponte das configurações de projeto do Swift para o destino helloworld no campo *Cabeçalho ponte de Objective-C* nas ![propriedades do cabeçalho](media/sdk/qs-swift-macos-bridging-header.png)
1. Substitua o conteúdo do arquivo `AppDelegate.swift` gerado automaticamente por:

   [!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift-macos/helloworld/helloworld/AppDelegate.swift#code)]
1. Em `AppDelegate.swift`, substitua a cadeia de caracteres `YourSubscriptionKey` pela chave de assinatura.
1. Substitua a cadeia de caracteres `YourServiceRegion` pela região associada à assinatura (por exemplo, `westus` para a assinatura de avaliação gratuita).

## <a name="install-the-sdk-as-a-cocoapod"></a>Instalar o SDK como um CocoaPod

1. Instale o gerenciador de dependência do CocoaPod conforme descrito nas [instruções de instalação](https://guides.cocoapods.org/using/getting-started.html).
1. Navegue até o diretório de seu aplicativo de exemplo (`helloworld`). Colocar um arquivo de texto com o nome `Podfile` e o seguinte conteúdo nesse diretório:

   [!code-ruby[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift-macos/helloworld/Podfile)]
1. Navegue até o diretório `helloworld` em um terminal e execute o comando `pod install`. Isso gerará um workspace do Xcode `helloworld.xcworkspace` que contém o aplicativo de exemplo e o SDK de fala como uma dependência. Este workspace será usado nas seções a seguir.

## <a name="build-and-run-the-sample"></a>Criar e executar a amostra

1. Abra o workspace `helloworld.xcworkspace` no Xcode.
1. Torne a saída de depuração visível (**Exibição** > **Área de Depuração** > **Ativar Console**).
1. Compile e execute o exemplo de código selecionando **Produto** > **Executar** no menu ou clicando no botão **Reproduzir**.
1. Depois que você clicar no botão "Reconhecer" no aplicativo e diga algumas palavras, você deve ver o texto falado por você na parte inferior da janela do aplicativo.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Explorar nossos exemplos no GitHub](https://aka.ms/csspeech/samples)

