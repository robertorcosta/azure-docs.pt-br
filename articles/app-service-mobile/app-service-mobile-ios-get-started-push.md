---
title: Adicionar notificações por push ao iOS
description: Saiba como usar aplicativos móveis do Azure para enviar notificações por push para seu aplicativo iOS.
ms.assetid: fa503833-d23e-4925-8d93-341bb3fbab7d
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: ad58ee158a2e1842c1b41db281bdd9f04b9b2ca4
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/19/2020
ms.locfileid: "77461497"
---
# <a name="add-push-notifications-to-your-ios-app"></a>Adicionar as notificações por push ao seu aplicativo iOS

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Visão geral

Neste tutorial, você adicionará notificações por push ao projeto de [Início rápido para iOS] de forma que sempre que um registro for inserido, uma notificação por push seja enviada.

Se você não usar o projeto do servidor de início rápido baixado, precisará do pacote de extensão da notificação por push. Para saber mais, veja o guia [Trabalhar com o SDK do servidor de back-end do .NET para Aplicativos Móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md){3}{4}.

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
