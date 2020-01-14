---
title: 'Início Rápido: Criar um aplicativo do Unity para iOS'
description: Neste Início Rápido, você aprenderá a criar um aplicativo do iOS com o Unity usando as Âncoras Espaciais.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 3cbf0fa4a32145add0a5475f22ea0d936584c9bf
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75465203"
---
# <a name="quickstart-create-a-unity-ios-app-with-azure-spatial-anchors"></a>Início Rápido: criar um aplicativo Unity para iOS com as Âncoras Espaciais do Azure

Este início rápido aborda como criar um aplicativo Unity para iOS usando as [Âncoras Espaciais do Azure](../overview.md). As Âncoras Espaciais do Azure são um serviço de desenvolvedor multiplataforma para você criar experiências de realidade misturada usando objetos que persistem sua localização em todos os dispositivos ao longo do tempo. Quando terminar, você terá um aplicativo do ARKit iOS criado com o Unity que pode salvar e fazer recall de uma âncora espacial.

Você aprenderá a:

> [!div class="checklist"]
> * Criar uma conta de Âncoras Espaciais
> * Preparar as configurações de build do Unity
> * Configurar o identificador e a chave de conta das Âncoras Espaciais
> * Exportar o projeto do Xcode
> * Implantar e executar em um dispositivo iOS

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisites

Para concluir este início rápido, certifique-se de que:

- Você tenha um computador macOS com o <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2019.1+</a>, a versão mais recente do <a href="https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12" target="_blank">Xcode</a> e o <a href="https://cocoapods.org" target="_blank">CocoaPods</a> instalados.
- Git instalado via Homebrew. Insira o seguinte comando em uma única linha do Terminal: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`. Em seguida, execute `brew install git` e `brew install git-lfs`.
- Um dispositivo iOS <a href="https://developer.apple.com/documentation/arkit/verifying_device_support_and_user_permission" target="_blank">compatível com ARKit</a> habilitado para desenvolvedor.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>Baixe e abra o projeto de exemplo do Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

[!INCLUDE [iOS Unity Build Settings](../../../includes/spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-account-identifier-and-key"></a>Configurar a chave e o identificador da conta

No painel **Projeto**, navegue para `Assets/AzureSpatialAnchors.Examples/Scenes` e abra o arquivo de cena `AzureSpatialAnchorsBasicDemo.unity`.

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

Salve a cena selecionando **Arquivo** -> **Salvar**.

## <a name="export-the-xcode-project"></a>Exportar o projeto do Xcode

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

[!INCLUDE [Configure Xcode](../../../includes/spatial-anchors-unity-ios-xcode.md)]

Siga as instruções no aplicativo para colocar uma âncora e fazer recall dela.

Ao concluir, interrompa o aplicativo pressionando **Parar** no Xcode.

## <a name="troubleshooting"></a>solução de problemas

### <a name="rendering-issues"></a>Problemas de renderização

Ao executar o aplicativo, se a câmera não for exibida como a tela de fundo (por exemplo, é exibida uma tela em branco, azul ou outras texturas), você provavelmente precisará reimportar os ativos no Unity. Interrompa o aplicativo. No menu superior do Unity, escolha **Ativos -> Reimportar todos**. Em seguida, execute o aplicativo novamente.

### <a name="cocoapods-issues-on-macos-catalina-1015"></a>Problemas do CocoaPods no macOS Catalina (10.15)

Se você atualizou recentemente para o macOS Catalina (10.15) e tinha o CocoaPods instalado anteriormente, o CocoaPods pode estar em um estado incompleto e falhar ao configurar corretamente seus arquivos de projeto `.xcworkspace` e pods. Para resolver esse problema, será preciso reinstalar o CocoaPods executando os seguintes comandos:

```shell
brew update
brew install cocoapods --build-from-source
brew link --overwrite cocoapods
```

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Tutorial: Compartilhar as Âncoras Espaciais nos dispositivos](../tutorials/tutorial-share-anchors-across-devices.md)
