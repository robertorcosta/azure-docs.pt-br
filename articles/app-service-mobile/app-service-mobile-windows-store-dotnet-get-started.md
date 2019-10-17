---
title: Criar um UWP (Plataforma Universal do Windows) que usa os Aplicativos Móveis do Azure | Microsoft Docs
description: Siga este tutorial para começar a usar os back-ends de aplicativo móvel do Azure para desenvolvimento de aplicativos UWP (Plataforma Universal do Windows) em C#, Visual Basic ou JavaScript.
services: app-service\mobile
documentationcenter: windows
author: elamalani
manager: crdun
editor: ''
ms.assetid: 47124296-2908-4d92-85e0-05c4aa6db916
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 3ddd5ef197740315add2960971c978addb7fe62a
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388561"
---
# <a name="create-a-windows-app-with-an-azure-backend"></a>Criar um aplicativo do Windows com um back-end do Azure

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> O Visual Studio App Center dá suporte a serviços de ponta a ponta e integrados central ao desenvolvimento de aplicativos móveis. Os desenvolvedores podem usar **Compilar**, **testar** e **distribuir** serviços para configurar o pipeline de integração e entrega contínua. Depois que o aplicativo é implantado, os desenvolvedores podem monitorar o status e o uso de seus aplicativos usando os serviços de **análise** e **diagnóstico** e se envolver com os usuários usando o serviço de **envio por push** . Os desenvolvedores também podem aproveitar a **autenticação** para autenticar seus usuários e o serviço de **dados** para manter e sincronizar dados de aplicativos na nuvem.
>
> Se você estiver procurando integrar os serviços de nuvem em seu aplicativo móvel, Inscreva-se com o [app Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) hoje mesmo.

## <a name="overview"></a>Visão Geral

Este tutorial mostra como adicionar um serviço de back-end baseado na nuvem a um aplicativo UWP (Plataforma Universal do Windows). Para saber mais, confira [O que são Aplicativos Móveis](app-service-mobile-value-prop.md). A seguir, há capturas de tela do aplicativo concluído:

![Aplicativo de área de trabalho concluído](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed-desktop.png)

A conclusão desse tutorial é um pré-requisito para todos os outros tutoriais de Aplicativos Móveis para aplicativos UWP.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará do seguinte:

* Uma conta ativa do Azure. Se você não tem uma conta, você pode se inscrever para uma avaliação do Azure e obter até 10 aplicativos móveis gratuitos que você pode continuar a usar mesmo após o fim do seu período de avaliação. Para obter detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* Windows 10.
* Visual Studio Community 2017.
* Familiaridade com o desenvolvimento de aplicativos UWP. Visite a [documentação do UWP](https://docs.microsoft.com/windows/uwp/) para saber como [se preparar](https://docs.microsoft.com/windows/uwp/get-started/get-set-up) para criar aplicativos UWP.

## <a name="create-a-new-azure-mobile-app-backend"></a>Criar um novo back-end de aplicativo móvel do Azure

Siga estas etapas para criar um novo back-end de aplicativo móvel.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Criar uma conexão de banco de dados e configurar o projeto de cliente e servidor
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-client-project"></a>Executar o projeto do cliente

1. Abra o projeto UWP.

2. Vá para a [portal do Azure](https://portal.azure.com/) e navegue até o aplicativo móvel que você criou. Na folha `Overview`, procure a URL que é o ponto de extremidade público para seu aplicativo móvel. Exemplo – o sitename para o nome do meu aplicativo "test123" será https://test123.azurewebsites.net.

3. Abra o arquivo `App.xaml.cs` nesta pasta-Windows-UWP-cs/ZUMOAPPNAME/. O nome do aplicativo é `ZUMOAPPNAME`.

4. Na classe `App`, substitua o parâmetro `ZUMOAPPURL` pelo ponto de extremidade público acima.

    `public static MobileServiceClient MobileService = new MobileServiceClient("ZUMOAPPURL");`

    ficará
    
    `public static MobileServiceClient MobileService = new MobileServiceClient("https://test123.azurewebsites.net");`

5. Pressione a tecla F5 para implantar e executar o aplicativo.

6. No aplicativo, digite um texto significativo, como *Concluir o tutorial*, na caixa de texto **Inserir um TodoItem** e clique em **Salvar**.

    ![Área de trabalho completa do início rápido do Windows](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-startup.png)

    Isso envia uma solicitação POST para o novo back-end de aplicativo móvel hospedado no Azure.
