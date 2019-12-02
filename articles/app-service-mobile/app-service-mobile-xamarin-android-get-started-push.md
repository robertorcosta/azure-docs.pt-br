---
title: Adicionar notificações por push ao aplicativo Xamarin.Android
description: Saiba como usar o serviço de Azure App e os hubs de notificação do Azure para enviar notificações por push para seu aplicativo Xamarin. Android.
ms.assetid: 6f7e8517-e532-4559-9b07-874115f4c65b
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: c9dc4c825d65287f152522868a2b9e6a38ea70bb
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2019
ms.locfileid: "74668742"
---
# <a name="add-push-notifications-to-your-xamarinandroid-app"></a>Adicionar notificações por push ao aplicativo Xamarin.Android

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

> [!NOTE]
> O Visual Studio App Center dá suporte a serviços de ponta a ponta e integrados essenciais para o desenvolvimento de aplicativos móveis. Os desenvolvedores podem usar os serviços de **Build**, **Teste** e **Distribuição** para configurar o pipeline de integração e entrega contínuas. Depois que o aplicativo é implantado, os desenvolvedores podem monitorar o status e o uso dos respectivos aplicativos usando os serviços de **Análise** e **Diagnóstico** e se envolver com usuários usando o serviço de **Push**. Os desenvolvedores também podem aproveitar o serviço de **Autenticação** para autenticar os respectivos usuários e o serviço de **Dados** para persistir e sincronizar dados de aplicativo na nuvem.
>
> Se desejar integrar os serviços de nuvem em seu aplicativo móvel, inscreva-se no [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) hoje mesmo.

## <a name="overview"></a>Visão Geral

Neste tutorial, você adicionará notificações por push ao projeto de [Início rápido do Xamarin.Android](app-service-mobile-windows-store-dotnet-get-started.md) de forma que sempre que um registro for inserido, uma notificação por push seja enviada.

Se você não usar o projeto do servidor de início rápido baixado, precisará do pacote de extensão da notificação por push. Para saber mais, consulte o guia [Trabalhar com o SDK do servidor de back-end do .NET para Aplicativos Móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial exige a configuração:

* Uma conta ativa do Google. Você pode se inscrever em uma conta do Google em [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302).
* [Componente do cliente Google Cloud Messaging](https://components.xamarin.com/view/GCMClient/).

## <a name="configure-hub"></a>Configurar um novo hub de notificação

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a id="register"></a>Habilitar mensagens de nuvem Firebase

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-azure-to-send-push-requests"></a>Configurar o Azure para enviar notificações por push

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

## <a id="update-server"></a>Atualizar o projeto de servidor para enviar notificações por push

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a id="configure-app"></a>Configurar o projeto para notificações por push

[!INCLUDE [mobile-services-xamarin-android-push-configure-project](../../includes/mobile-services-xamarin-android-push-configure-project.md)]

## <a id="add-push"></a>Adicionar código de notificações por push ao seu aplicativo

[!INCLUDE [app-service-mobile-xamarin-android-push-add-to-app](../../includes/app-service-mobile-xamarin-android-push-add-to-app.md)]

## <a name="test"></a>Testar notificações por push no seu aplicativo

Você pode testar o aplicativo usando um dispositivo virtual no emulador. Há etapas de configuração adicionais exigidas durante a execução em um emulador.

1. O dispositivo virtual deve ter APIs do Google definidas como o destino no gerenciador de Dispositivo Virtual Android (AVD).

    ![](./media/app-service-mobile-xamarin-android-get-started-push/google-apis-avd-settings.png)

2. Adicione uma conta do Google para o dispositivo Android clicando em **Aplicativos** > **Configurações** > **Adicionar conta** e então siga os prompts.

    ![](./media/app-service-mobile-xamarin-android-get-started-push/add-google-account.png)

3. Execute o aplicativo todolist como feito anteriormente s e insira um novo item de tarefa pendente. Dessa vez, um ícone de notificação é exibido na área de notificação. Você pode abrir a gaveta de notificações para exibir o texto completo da notificação.

    ![](./media/app-service-mobile-xamarin-android-get-started-push/android-notifications.png)

<!-- URLs. -->
[Xamarin.Android quick start]: app-service-mobile-xamarin-android-get-started.md
[Google Cloud Messaging Client Component]: https://components.xamarin.com/view/GCMClient/
[Azure Mobile Services Component]: https://components.xamarin.com/view/azure-mobile-services/
