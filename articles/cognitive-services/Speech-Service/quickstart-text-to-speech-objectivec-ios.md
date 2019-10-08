---
title: 'Início Rápido: Sintetizar fala, Objective-C – Serviço de Fala'
titleSuffix: Azure Cognitive Services
description: Saiba como sintetizar fala em Objective-C no iOS usando o SDK de Fala
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 09/20/2019
ms.author: yulili
ms.openlocfilehash: 9d7fb4dfef2ac3ab14a8a10eb4d9a7afb91ac71e
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803686"
---
# <a name="quickstart-synthesize-speech-in-objective-c-on-ios-using-the-speech-sdk"></a>Início Rápido: Sintetizar fala em Objective-C no iOS usando o SDK de Fala

Também estão disponíveis inícios rápidos para [reconhecimento de fala](quickstart-objectivec-ios.md).

Neste artigo, você aprenderá a criar um aplicativo iOS em Objective-C usando o SDK de Fala dos Serviços Cognitivos para sintetizar fala com base em texto.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, aqui está uma lista de pré-requisitos:

* Uma [chave de assinatura](get-started.md) para o Serviço de Fala
* Um computador macOS com [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) ou posterior
* O destino definido para o iOS versão 9.3 ou posterior

## <a name="get-the-speech-sdk-for-ios"></a>Obter o SDK de Fala para iOS

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Observe que este tutorial não funcionará com uma versão do SDK anterior a 1.7.0.

Atualmente, o SDK de Fala dos Serviços Cognitivos para iOS é distribuído como um Cocoa Framework.
Ele pode ser usado em projetos do Xcode como um [CocoaPod](https://cocoapods.org/) ou baixados de https://aka.ms/csspeech/iosbinary e vinculados manualmente. Este guia usa um CocoaPod.

## <a name="create-an-xcode-project"></a>Criar um projeto do Xcode

Inicie o Xcode e iniciar um novo projeto clicando em **Arquivo** > **Novo** > **Projeto**.
Na caixa de diálogo de seleção de modelo, escolha o modelo "Aplicativo de Exibição Única do iOS ".

Nas caixas de diálogo que seguem, faça as seguintes seleções:

1. Caixa de diálogo Opções de Projeto
    1. Insira um nome para o aplicativo de início rápido, por exemplo `helloworld`.
    1. Insira um nome de organização apropriado e o identificador de organização, se você já tiver uma conta de desenvolvedor da Apple. Para fins de teste, você pode escolher qualquer nome, como `testorg`. Para assinar o aplicativo, você precisa de um perfil de provisionamento adequado. Veja o [site do desenvolvedor da Apple](https://developer.apple.com/) para obter detalhes.
    1. Verifique se Objective-C é escolhido como o idioma para o projeto.
    1. Desabilite todas as caixas de seleção para testes e dados principais.
    ![Configurações do projeto](media/sdk/qs-objectivec-project-settings.png)
1. Selecionar diretório do projeto
    1. Escolha seu diretório base no qual colocar o projeto. Isso cria um diretório `helloworld` no diretório base que contém todos os arquivos para o projeto do Xcode.
    1. Desabilite a criação de um repositório Git para este projeto de exemplo.

## <a name="install-the-sdk-as-a-cocoapod"></a>Instalar o SDK como um CocoaPod

1. Instale o gerenciador de dependência do CocoaPod conforme descrito nas [instruções de instalação](https://guides.cocoapods.org/using/getting-started.html).
1. Navegue até o diretório de seu aplicativo de exemplo (`helloworld`). Colocar um arquivo de texto com o nome `Podfile` e o seguinte conteúdo nesse diretório:  
   [!code-ruby[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/objectivec-ios/helloworld/Podfile)]
1. Navegue até o diretório `helloworld` em um terminal e execute o comando `pod install`. Isso gerará um workspace do Xcode `helloworld.xcworkspace` que contém o aplicativo de exemplo e o SDK de fala como uma dependência. Este workspace será usado nas seções a seguir.

## <a name="add-the-sample-code"></a>Adicione o código de amostra

1. Abra o workspace `helloworld.xcworkspace` no Xcode.
1. Substitua o conteúdo do arquivo `AppDelegate.m` gerado automaticamente por: [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/objectivec-ios/helloworld/helloworld/AppDelegate.m#code)]
1. Substitua o conteúdo do arquivo `ViewController.m` gerado automaticamente por:  
   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/objectivec-ios/helloworld/helloworld/ViewController.m#code)]
1. Substitua a cadeia de caracteres `YourSubscriptionKey` pela chave de assinatura.
1. Substitua a cadeia de caracteres `YourServiceRegion` pela [região](regions.md) associada à assinatura (por exemplo, `westus` para a assinatura de avaliação gratuita).

## <a name="build-and-run-the-sample"></a>Criar e executar a amostra

1. Torne a saída de depuração visível (**Exibição** > **Área de Depuração** > **Ativar Console**).
1. Escolha o simulador de iOS ou um dispositivo iOS conectado ao computador de desenvolvimento como o destino para o aplicativo da lista no menu **Produto** > **Destino**.
1. Compile e execute o exemplo de código no simulador do iOS selecionando **Produto** > **Executar** no menu ou clicando no botão **Reproduzir**.

   ![Aplicativo do iOS simulado](media/sdk/qs-objectivec-simulated-app-tts.png)

1. Depois de inserir algum texto e clicar no botão no aplicativo, você deverá ouvir o áudio sintetizado ser reproduzido.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Explorar amostras de Objective-C no GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Consulte também

- [Personalizar fontes de voz](how-to-customize-voice-font.md)
- [Amostras de memorando de voz](record-custom-voice-samples.md)
