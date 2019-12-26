---
title: Adicionar notificações por push ao iOS
description: Saiba como usar aplicativos móveis do Azure para enviar notificações por push para seu aplicativo iOS.
ms.assetid: fa503833-d23e-4925-8d93-341bb3fbab7d
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 2864dfca72c91acd10fd6a4ee8038040ba5defc8
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2019
ms.locfileid: "74668811"
---
# <a name="add-push-notifications-to-your-ios-app"></a>Adicionar as notificações por push ao seu aplicativo iOS

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

> [!NOTE]
> O Visual Studio App Center dá suporte a serviços de ponta a ponta e integrados essenciais para o desenvolvimento de aplicativos móveis. Os desenvolvedores podem usar os serviços de **Build**, **Teste** e **Distribuição** para configurar o pipeline de integração e entrega contínuas. Depois que o aplicativo é implantado, os desenvolvedores podem monitorar o status e o uso dos respectivos aplicativos usando os serviços de **Análise** e **Diagnóstico** e se envolver com usuários usando o serviço de **Push**. Os desenvolvedores também podem aproveitar o serviço de **Autenticação** para autenticar os respectivos usuários e o serviço de **Dados** para persistir e sincronizar dados de aplicativo na nuvem.
>
> Se desejar integrar os serviços de nuvem em seu aplicativo móvel, inscreva-se no [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) hoje mesmo.

## <a name="overview"></a>Visão Geral

Neste tutorial, você adicionará notificações por push ao projeto de [Início rápido para iOS] de forma que sempre que um registro for inserido, uma notificação por push seja enviada.

Se você não usar o projeto do servidor de início rápido baixado, precisará do pacote de extensão da notificação por push. Para saber mais, veja o guia [Trabalhar com o SDK do servidor de back-end do .NET para Aplicativos Móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

O [simulador do iOS não dá suporte a notificações por push](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html). Você precisa de um dispositivo iOS físico e uma [Associação de programa para desenvolvedores da Apple](https://developer.apple.com/programs/ios/).

## <a name="configure-hub"></a>Configurar Hub de Notificações

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a id="register"></a>Registre o aplicativo para obter notificações por push

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

## <a name="configure-azure-to-send-push-notifications"></a>Configurar o Azure para enviar notificações por push

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

## <a id="update-server"></a>Atualizar back-end para enviar notificações por push

[!INCLUDE [app-service-mobile-dotnet-backend-configure-push-apns](../../includes/app-service-mobile-dotnet-backend-configure-push-apns.md)]

## <a id="add-push"></a>Adicionar notificações por push ao aplicativo

[!INCLUDE [app-service-mobile-add-push-notifications-to-ios-app.md](../../includes/app-service-mobile-add-push-notifications-to-ios-app.md)]

## <a id="test"></a>Testar notificações por push

[!INCLUDE [Test Push Notifications in App](../../includes/test-push-notifications-in-app.md)]

## <a id="more"></a>Mais

* modelos oferecem flexibilidade para enviar envios de plataforma cruzada e pushes localizados. [Como usar a biblioteca de cliente do iOS para aplicativos móveis do Azure](app-service-mobile-ios-how-to-use-client-library.md#templates) mostra como registrar os modelos.

<!-- Anchors.  -->

<!-- Images. -->

<!-- URLs. -->
[Início rápido para iOS]: app-service-mobile-ios-get-started.md
