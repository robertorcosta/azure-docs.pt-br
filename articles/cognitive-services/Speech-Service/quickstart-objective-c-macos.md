---
title: 'Início Rápido: Reconhecer fala, Objective-C – Serviço de Fala'
titleSuffix: Azure Cognitive Services
description: Saiba como reconhecer uma fala em Objective-C no macOS usando o SDK de Fala
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: chlandsi
ms.openlocfilehash: 6955fc5dd98b65d2eb94914ea39685f1f69a46ac
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72327790"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-macos-by-using-the-speech-sdk"></a>Início Rápido: Reconhecer uma fala em Objective-C no macOS usando o SDK de Fala

Também estão disponíveis inícios rápidos para [sintetização de voz](quickstart-text-to-speech-objectivec-macos.md).

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Neste artigo, você aprenderá a criar um aplicativo macOS em Objective-C usando o SDK de Fala dos Serviços Cognitivos do Azure para transcrever uma fala gravada de um microfone para texto.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, você precisará do seguinte:

* Uma [chave de assinatura](get-started.md) para o Serviço de Fala.
* Um computador macOS com [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) ou posterior e macOS 10.13 ou posterior.

## <a name="get-the-speech-sdk-for-macos"></a>Obter o SDK de Fala para macOS

[!INCLUDE [License notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

O SDK de Fala dos Serviços Cognitivos para Mac é distribuído como um quadro de estrutura. Ele pode ser usado em projetos do Xcode como um [CocoaPod](https://cocoapods.org/) ou baixados em https://aka.ms/csspeech/macosbinary e vinculados manualmente. Este artigo usa um CocoaPod.

## <a name="create-an-xcode-project"></a>Criar um projeto do Xcode

Inicie o Xcode e inicie um novo projeto selecionando **Arquivo** > **Novo** > **Projeto**. Na caixa de diálogo da seleção de modelo, selecione o modelo **Aplicativo Cocoa**.

Nas caixas de diálogo posteriores, faça as seleções a seguir.

1. Na caixa de diálogo **Opções de Projeto**:
    1. Insira um nome para o aplicativo de início rápido, por exemplo *helloworld*.
    1. Insira um nome de organização apropriado e um identificador de organização se você já tiver uma conta de desenvolvedor da Apple. Para fins de teste, use um nome como *testorg*. Para assinar o aplicativo, você precisa de um perfil de provisionamento adequado. Para obter mais informações, confira o [Site de desenvolvedor da Apple](https://developer.apple.com/).
    1. Verifique se **Objective-C** está selecionado como a linguagem do projeto.
    1. Desmarque as caixas de seleção para usar storyboards e criar um aplicativo baseado no documento. A interface do usuário simples para o aplicativo de exemplo é criada de forma programática.
    1. Desmarque todas as caixas de seleção dos testes e dos dados principais.

    ![Configurações do projeto](media/sdk/qs-objectivec-macos-project-settings.png)

1. Selecione um diretório do projeto:
    1. Escolha um diretório no qual colocar o projeto. Essa etapa cria um diretório helloworld no diretório base que contém todos os arquivos para o projeto do Xcode.
    1. Desabilite a criação de um repositório Git para este projeto de exemplo.
1. Defina os direitos de acesso de rede e de microfone. Selecione o nome do aplicativo na primeira linha da visão geral à esquerda para acessar a configuração do aplicativo. Em seguida, selecione a guia **Funcionalidades**.
    1. Habilite a configuração **Área restrita de aplicativo** para o aplicativo.
    1. Marque as caixas de seleção para acesso de **Conexões de Saída** e **Microfone**.

    ![Configurações de área restrita](media/sdk/qs-objectivec-macos-sandbox.png)

1. O aplicativo também precisa declarar o uso do microfone no arquivo `Info.plist`. Selecione o arquivo na visão geral e adicione a chave **Privacidade – Descrição de Uso do Microfone**, com um valor, como *Microfone é necessário para o reconhecimento de fala*.

    ![Configurações no Info.plist](media/sdk/qs-objectivec-macos-info-plist.png)

1. Fechar o projeto do Xcode. Você usará uma instância diferente dele depois de configurar o CocoaPods.

## <a name="install-the-sdk-as-a-cocoapod"></a>Instalar o SDK como um CocoaPod

1. Instale o gerenciador de dependência do CocoaPod conforme descrito nas [instruções de instalação](https://guides.cocoapods.org/using/getting-started.html).
1. Acesse o diretório do aplicativo de exemplo, que é helloworld. Coloque um arquivo de texto com o nome *Podfile* e o seguinte conteúdo nesse diretório:

   [!code-ruby[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-macos/helloworld/Podfile)]
1. Acesse o diretório helloworld em um terminal e execute o comando `pod install`. Esse comando gera um workspace do Xcode `helloworld.xcworkspace` que contém o aplicativo de exemplo e o SDK de Fala como uma dependência. Esse workspace será usado nas etapas a seguir.

## <a name="add-the-sample-code"></a>Adicione o código de amostra

1. Abra o workspace `helloworld.xcworkspace` no Xcode.
1. Substitua o conteúdo do arquivo `AppDelegate.m` gerado automaticamente pelo seguinte código:

   [!code-objectivec[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-macos/helloworld/helloworld/AppDelegate.m#code)]
1. Substitua a cadeia de caracteres `YourSubscriptionKey` pela chave de assinatura.
1. Substitua a cadeia de caracteres `YourServiceRegion` pela [região](regions.md) associada à sua assinatura. Por exemplo, use `westus` para a assinatura de avaliação gratuita.

## <a name="build-and-run-the-sample"></a>Criar e executar a amostra

1. Torne a saída de depuração visível selecionando **Exibição** > **Área de Depuração** > **Ativar Console**.
1. Compile e execute o código de exemplo selecionando **Produto** > **Executar** no menu. Selecione também **Reproduzir**.
1. Depois de selecionar o botão e dizer algumas palavras, você deverá ver o texto falado por você na parte inferior da tela. Ao executar o aplicativo pela primeira vez, você será solicitado a dar ao aplicativo acesso ao microfone do seu computador.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Explorar amostras de Objective-C no GitHub](https://aka.ms/csspeech/samples)
