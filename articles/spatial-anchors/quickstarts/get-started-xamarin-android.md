---
title: 'Início Rápido: criar um aplicativo Android do Xamarin'
description: Neste Início Rápido, você aprenderá a criar um aplicativo do Android com o Xamarin usando as Âncoras Espaciais.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 07/22/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 94906d645ec38a24d54536ee8aa93e7418c8dc35
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75465183"
---
# <a name="quickstart-create-a-xamarin-android-app-with-azure-spatial-anchors"></a>Início Rápido: Criar um aplicativo para Xamarin Android com as Âncoras Espaciais do Azure

Este Início Rápido aborda como criar um aplicativo para Android com o Xamarin usando as [Âncoras Espaciais do Azure](../overview.md). As Âncoras Espaciais do Azure são um serviço de desenvolvedor multiplataforma para você criar experiências de realidade misturada usando objetos que persistem sua localização em todos os dispositivos ao longo do tempo. Quando tiver terminado, você terá um aplicativo do Android que pode salvar e lembrar de uma âncora espacial.

Você aprenderá a:

> [!div class="checklist"]
> * Criar uma conta de Âncoras Espaciais
> * Configurar o identificador e a chave de conta das Âncoras Espaciais
> * Implantar e executar um dispositivo Android

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisites

Para concluir este início rápido, certifique-se de que:
- Um computador com Windows ou macOS:
  - Se estiver usando o Windows:
    - Uma versão atualizada do <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019 16.2 ou superior</a>.
    - <a href="https://git-scm.com/download/win" target="_blank">Git para Windows</a>.
    - <a href="https://git-lfs.github.com/">Git LFS</a>.
  - Se estiver usando o macOS:
    - Uma versão atualizada do <a href="https://docs.microsoft.com/visualstudio/mac/installation?view=vsmac-2019" target="_blank">Visual Studio para Mac 8.1+</a>.
    - <a href="https://git-scm.com/download/mac" target="_blank">Git para macOS</a>.
    - <a href="https://git-lfs.github.com/">Git LFS</a>.
- A versão mais recente do Xamarin.Android instalada e em execução na plataforma de sua escolha. Para obter um guia para instalar o Xamarin.Android, consulte os guias de [Instalação do Xamarin.Android](https://docs.microsoft.com/xamarin/android/get-started/installation/index).
- Um dispositivo Android <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">habilitado para desenvolvedor</a> e <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">compatível com ARCore</a>.
  - Drivers de dispositivo adicionais podem ser necessários para que seu computador se comunique com seu dispositivo Android. Para saber mais, clique [aqui](https://developer.android.com/studio/run/device.html).
- Seu aplicativo deve ser destinado ao ARCore **1.8**.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Abrir o projeto de exemplo

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Abra o `Xamarin/SampleXamarin.sln` no Visual Studio.

## <a name="configure-account-identifier-and-key"></a>Configurar a chave e o identificador da conta

A próxima etapa é configurar o aplicativo para usar o identificador e a chave de conta. Você os copiou em um editor de texto ao [configurar o recurso Âncoras Espaciais](#create-a-spatial-anchors-resource).

Abra o `Xamarin/SampleXamarin.Common/AccountDetails.cs`.

Localize o campo `SpatialAnchorsAccountKey` e substitua `Set me` pela chave de conta.

Localize o campo `SpatialAnchorsAccountId` e substitua `Set me` pelo identificador de conta.

## <a name="deploy-the-app-to-your-android-device"></a>Implantar o aplicativo em seu dispositivo Android

Ligue o dispositivo Android, entre nele e conecte-o ao computador usando um cabo USB.

Defina o projeto de inicialização como **SampleXamarin.Android**, altere a **Configuração da Solução** para **Versão** e selecione o dispositivo no qual você deseja implantar no menu suspenso do seletor de dispositivos.

# <a name="windowstabdeploy-windows"></a>[Windows](#tab/deploy-windows)

![Configuração do Visual Studio](./media/get-started-xamarin-android/visual-studio-windows-configuration.png)

Selecione **Depurar** > **Iniciar depuração** para implantar e iniciar seu aplicativo.

# <a name="macostabdeploy-macos"></a>[macOS](#tab/deploy-macos)

![Configuração do Visual Studio](./media/get-started-xamarin-android/visual-studio-macos-configuration.jpg)

Selecione **Executar** > **Iniciar sem Depuração** para implantar e iniciar o aplicativo.

---

No aplicativo, selecione **Básico** para executar a demonstração e siga as instruções para posicionar e relembrar uma âncora.

> ![Captura de tela 1](./media/get-started-xamarin-android/screenshot-1.jpg)
> ![Captura de tela 2](./media/get-started-xamarin-android/screenshot-2.jpg)
> ![Captura de tela 3](./media/get-started-xamarin-android/screenshot-3.jpg)

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Tutorial: Compartilhar as Âncoras Espaciais nos dispositivos](../tutorials/tutorial-share-anchors-across-devices.md)
