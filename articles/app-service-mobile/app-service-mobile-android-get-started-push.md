---
title: Adicionar notificações por push ao aplicativo Android com os Aplicativos Móveis | Microsoft Docs
description: Saiba como usar os Aplicativos Móveis para enviar notificações por push ao seu aplicativo Android.
services: app-service\mobile
documentationcenter: android
manager: crdun
editor: ''
author: elamalani
ms.assetid: 9058ed6d-e871-4179-86af-0092d0ca09d3
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 3185447e4e465fe49849bb4280f2af9bbe852e19
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72027690"
---
# <a name="add-push-notifications-to-your-android-app"></a>Adicionar notificações por push ao aplicativo Android

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

> [!NOTE]
> O Visual Studio App Center dá suporte a serviços de ponta a ponta e integrados central ao desenvolvimento de aplicativos móveis. Os desenvolvedores podem usar **Compilar**, **testar** e **distribuir** serviços para configurar o pipeline de integração e entrega contínua. Depois que o aplicativo é implantado, os desenvolvedores podem monitorar o status e o uso de seus aplicativos usando os serviços de **análise** e **diagnóstico** e se envolver com os usuários usando o serviço de **envio por push** . Os desenvolvedores também podem aproveitar a **autenticação** para autenticar seus usuários e o serviço de **dados** para manter e sincronizar dados de aplicativos na nuvem.
> Se você estiver procurando integrar os serviços de nuvem em seu aplicativo móvel, Inscreva-se com App Center [app Center](https://appcenter.ms/signup?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) hoje.

## <a name="overview"></a>Visão geral

Neste tutorial, você adicionará notificações por push ao projeto de [início rápido do Android] para que, sempre que um registro for inserido, uma notificação por push seja enviada.

Se você não usar o projeto baixado do início rápido do servidor, deverá adicionar o pacote de extensão de notificação por push ao seu projeto. Para saber mais, veja [Trabalhar com o SDK do servidor de back-end do .NET para Aplicativos Móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="prerequisites"></a>Pré-requisitos

Você precisará do seguinte:

* Uma IDE, dependendo do back-end do projeto:

  * [Android Studio](https://developer.android.com/sdk/index.html), se esse aplicativo tiver um back-end do Node.js.
  * [Visual Studio Community 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934) ou posterior, se esse aplicativo tiver um back-end do Microsoft .NET.
* Android 2.3 ou posterior, Google Repository revisão 27 ou posterior e Google Play Services 9.0.2 ou posterior para o Firebase Cloud Messaging.
* Conclua o [início rápido do Android].

## <a name="create-a-project-that-supports-firebase-cloud-messaging"></a>Criar um projeto que ofereça suporte ao Firebase Cloud Messaging

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-a-notification-hub"></a>Configurar um hub de notificação

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="configure-azure-to-send-push-notifications"></a>Configurar o Azure para enviar notificações por push

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

## <a name="enable-push-notifications-for-the-server-project"></a>Em seguida, habilite as notificações por push para o projeto de servidor

[!INCLUDE [app-service-mobile-dotnet-backend-configure-push-google](../../includes/app-service-mobile-dotnet-backend-configure-push-google.md)]

## <a name="add-push-notifications-to-your-app"></a>Adicionar notificações de push para seu aplicativo

Nesta seção, você atualizará seu aplicativo de cliente Android para manipular notificações por push.

### <a name="verify-android-sdk-version"></a>Verificar a versão do SDK do Android

[!INCLUDE [app-service-mobile-verify-android-sdk-version](../../includes/app-service-mobile-verify-android-sdk-version.md)]

A próxima etapa é instalar os serviços do Google Play. O Firebase Cloud Messaging tem alguns requisitos mínimos no nível de API para desenvolvimento e teste, com os quais a propriedade **minSdkVersion** no manifesto deve estar em conformidade.

Se estiver testando com um dispositivo antigo, confira [Adicionar o Firebase ao seu projeto do Android] para determinar o valor mínimo que pode ser definido e defina-o adequadamente.

### <a name="add-firebase-cloud-messaging-to-the-project"></a>Adicionar o Firebase Cloud Messaging ao projeto

[!INCLUDE [Add Firebase Cloud Messaging](../../includes/app-service-mobile-add-firebase-cloud-messaging.md)]

### <a name="add-code"></a>Incluir código

[!INCLUDE [app-service-mobile-android-getting-started-with-push](../../includes/app-service-mobile-android-getting-started-with-push.md)]

## <a name="test-the-app-against-the-published-mobile-service"></a>Testar o aplicativo no serviço móvel publicado

Você pode testar o aplicativo anexando um telefone Android com um cabo USB diretamente ou usando um dispositivo virtual no emulador.

## <a name="next-steps"></a>Próximas etapas

Agora que você concluiu este tutorial, considere continuar com um dos seguintes tutoriais:

* [Adicionar autenticação ao aplicativo Android](app-service-mobile-android-get-started-users.md).
  Saiba como adicionar autenticação ao projeto de início rápido da lista de tarefas pendentes no Android usando um provedor de identidade com suporte.
* [Habilitar a sincronização offline para o aplicativo móvel Android](app-service-mobile-android-get-started-offline-data.md).
  Saiba como adicionar suporte offline ao aplicativo usando um back-end dos Aplicativos Móveis. Com a sincronização offline, os usuários podem interagir com um aplicativo móvel &mdash; exibindo, adicionando ou modificando dados &mdash; mesmo quando não há nenhuma conexão de rede.

<!-- URLs -->
[início rápido do Android]: app-service-mobile-android-get-started.md
[Adicionar o Firebase ao seu projeto do Android]: https://firebase.google.com/docs/android/setup
