---
title: 'Início Rápido: Criar um aplicativo Xamarin.iOS'
description: Neste Início Rápido, você aprenderá a criar um aplicativo do iOS com o Xamarin usando as Âncoras Espaciais.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 07/22/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 625a1a256ec5f1668c97e74c37e567a4ceb3612f
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/27/2020
ms.locfileid: "83871149"
---
# <a name="quickstart-create-a-xamarin-ios-app-with-azure-spatial-anchors"></a>Início Rápido: Criar um aplicativo Xamarin para iOS com as Âncoras Espaciais do Azure

Este Início Rápido aborda como criar um aplicativo para iOS com o Xamarin usando as [Âncoras Espaciais do Azure](../overview.md). As Âncoras Espaciais do Azure são um serviço de desenvolvedor multiplataforma para você criar experiências de realidade misturada usando objetos que persistem sua localização em todos os dispositivos ao longo do tempo. Quando tiver terminado, você terá um aplicativo para iOS que pode salvar e lembrar de uma âncora espacial.

Você aprenderá a:

> [!div class="checklist"]
> * Criar uma conta de Âncoras Espaciais
> * Configurar o identificador e a chave de conta das Âncoras Espaciais
> * Implantar e executar em um dispositivo iOS

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, certifique-se de que:
- Um Mac que executa o macOS High Sierra (10.13) ou superior com:
  - A versão mais recente do Xcode e do SDK do iOS instalado da [App Store](https://itunes.apple.com/us/app/xcode/id497799835?mt=12).
  - Uma versão atualizada do <a href="https://docs.microsoft.com/visualstudio/mac/installation?view=vsmac-2019" target="_blank">Visual Studio para Mac 8.1+</a>.
  - <a href="https://git-scm.com/download/mac" target="_blank">Git para macOS</a>.
  - <a href="https://git-lfs.github.com/">Git LFS</a>.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Abrir o projeto de exemplo

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Abra o `Xamarin/SampleXamarin.sln` no Visual Studio.

## <a name="configure-account-identifier-and-key"></a>Configurar a chave e o identificador da conta

A próxima etapa é configurar o aplicativo para usar o identificador e a chave de conta. Você os copiou em um editor de texto ao [configurar o recurso Âncoras Espaciais](#create-a-spatial-anchors-resource).

Abra o `Xamarin/SampleXamarin.Common/AccountDetails.cs`.

Localize o campo `SpatialAnchorsAccountKey` e substitua `Set me` pela chave de conta.

Localize o campo `SpatialAnchorsAccountId` e substitua `Set me` pelo identificador de conta.

Abra `Xamarin/SampleXamarin.iOS/ViewControllers/DemoViewControllerBase.cs`

Localize `StartSession()` e adicione a seguinte linha, substituindo em seu domínio de conta anterior: `this.cloudSession.Configuration.AccountDomain = "MyAccountDomain";`.

## <a name="deploy-the-app-to-your-ios-device"></a>Implantar o aplicativo em seu dispositivo iOS

Ligue o dispositivo do iOS, entre nele e conecte-o ao computador usando um cabo USB.

Defina o projeto de inicialização como **SampleXamarin.iOS**, altere a **Configuração da Solução** para **Versão** e selecione o dispositivo no qual você deseja implantar no menu suspenso do seletor de dispositivos.

![Configuração do Visual Studio](./media/get-started-xamarin-iOS/visual-studio-macos-configuration.jpg)

Selecione **Executar** > **Iniciar sem Depuração** para implantar e iniciar o aplicativo.

No aplicativo, selecione **Básico** para executar a demonstração e siga as instruções para posicionar e relembrar uma âncora.

> ![Captura de tela 1](./media/get-started-xamarin-ios/screenshot-1.jpg)
> ![Captura de tela 2](./media/get-started-xamarin-ios/screenshot-2.jpg)
> ![Captura de tela 3](./media/get-started-xamarin-ios/screenshot-3.jpg)

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Tutorial: Compartilhar as Âncoras Espaciais nos dispositivos](../tutorials/tutorial-share-anchors-across-devices.md)
