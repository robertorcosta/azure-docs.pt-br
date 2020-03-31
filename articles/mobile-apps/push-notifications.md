---
title: A importância das notificações push em seus aplicativos móveis com o Visual Studio App Center e os Hubs de Notificação Do Azure
description: Conheça serviços como o Visual Studio App Center que você pode usar para se envolver com os usuários de seus aplicativos móveis.
author: codemillmatt
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: bc59857cdb843dfff54e69f2226f13cfe70df8f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235326"
---
# <a name="engage-with-your-application-users-by-sending-push-notifications"></a>Envolva-se com seus usuários de aplicativos enviando notificações push

Se você está construindo aplicativos de consumo ou corporativo, você quer que os usuários usem seu aplicativo ativamente. Muitas vezes você quer compartilhar notícias de última hora, atualizações de jogos, ofertas emocionantes, atualizações de produtos ou qualquer outra informação relevante com seus usuários. Para aumentar o engajamento com seus usuários e fazê-los retornar ao seu aplicativo, você precisa de uma maneira de se comunicar com seus usuários em tempo real.

As notificações push fornecem uma maneira rápida e eficiente de se comunicar com os usuários do aplicativo. Você pode entrar em contato com seus usuários no momento certo e notificá-los das informações desejadas, geralmente em um item pop-up ou caixa de diálogo em um dispositivo móvel, independentemente do que eles estão fazendo.

## <a name="value-of-push-notifications"></a>Valor das notificações push
As notificações push fornecem valor aos usuários e empresas.

As empresas podem:
- Comunique-se diretamente com os usuários enviando mensagens em plataformas suportadas no momento certo.
- Aumente o engajamento do usuário enviando atualizações e lembretes em tempo real para os usuários, encorajando-os a se envolver com o aplicativo regularmente.
- Retenha os usuários e volte a se envolver com usuários inativos que baixaram o aplicativo, mas não o utilizem para se tornarem ativos novamente.
- Aumente as taxas de conversão analisando o comportamento do usuário, segmentando os usuários e aproveitando campanhas com base em notificações push móveis.
- Promova produtos e serviços enviando mensagens push e atualizações oportunas aos usuários.
- Direcione os usuários enviando mensagens de push personalizadas.

Para usuários de aplicativos, notificações push:
- Fornecer valor e informações em tempo real.
- Lembre os usuários de usar o aplicativo.

Use os seguintes serviços para ativar notificações push em seus aplicativos móveis.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
Com [o App Center Push](/appcenter/push/), você pode enviar mensagens direcionadas para usuários de iOS, Android e Windows sem ter que gerenciar o processo de envio de notificações para dispositivos usando serviços de notificação push (PNS). Construído em cima dos Hubs de Notificação do Azure, este serviço elimina complexidades associadas ao empurrar notificações manualmente, fornecendo um painel de controle poderoso.

**Principais características**
- Envie notificações push para dispositivos móveis em uma variedade de plataformas.
- Use notificações para enviar dados para um aplicativo, exibir uma mensagem para o usuário ou desencadear uma ação pelo aplicativo.
- Use metas de notificação para: 
    - Transmitir mensagens para todos os dispositivos registrados.
    - Envie notificações ao público com base em informações do dispositivo e propriedades personalizadas.
    - Envie notificações para usuários específicos.
    - Envie notificações para dispositivos específicos.
- Faça uso da rica telemetria em pushes, dispositivos e erros que estão disponíveis no portal do App Center.
- Ganhe suporte à plataforma para iOS, Android, macOS, Xamarin, React Native, Unity e Cordova.

**Referências**
- [Inscreva-se no Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Comece com o App Center Push](/appcenter/push/)

## <a name="azure-notification-hubs"></a>Hubs de Notificação do Azure
[O Notification Hubs](/azure/notification-hubs/notification-hubs-push-notification-overview) fornece um mecanismo de push fácil de usar e dimensionado. Você pode usá-lo para enviar notificações para qualquer plataforma e de qualquer back-end na nuvem ou no local.

**Principais características**
- Envie notificações push para qualquer plataforma de qualquer back-end, na nuvem ou no local.
- Transmissão rápida empurra para milhões de dispositivos móveis com uma única chamada de API.
- Personalize as notificações push pelo cliente, idioma e localização.
- Defina e notifique dinamicamente os segmentos dos clientes.
- Dimensione instantaneamente para milhões de dispositivos móveis.
- Ganhe suporte à plataforma para iOS, Android, Windows, Kindle e Baidu.
        
**Referências**
- [Portal Azure](https://portal.azure.com) 
- [Introdução aos Hubs de Notificações do Microsoft Azure](/azure/notification-hubs/)
- [Inícios rápidos](/azure/notification-hubs/create-notification-hub-portal)
- [Amostras](/azure/notification-hubs/samples)
