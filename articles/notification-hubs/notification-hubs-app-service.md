---
title: Integração com Aplicativos Móveis do Serviço de Aplicativo
description: Saiba como os Hubs de Notificação do Azure funcionam com os Aplicativos Móveis do Serviço de Aplicativo do Azure.
author: sethmanheim
manager: femila
editor: jwargo
services: notification-hubs
documentationcenter: ''
ms.assetid: 83132dff-a01d-4b31-a426-b57496852b81
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: overview
ms.custom: mvc
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 8c3bc90b282092ede0e924d32b50b67e5c4e22b8
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72244496"
---
# <a name="integration-with-app-service-mobile-apps"></a>Integração com Aplicativos Móveis do Serviço de Aplicativo

Para facilitar uma experiência integrada e unificada nos serviços do Azure, os [Aplicativos Móveis do Serviço de Aplicativo](../app-service-mobile/app-service-mobile-value-prop.md) possui suporte interno para notificações de push usando Hubs de notificação. [Aplicativos Móveis do Serviço de Aplicativo](../app-service-mobile/app-service-mobile-value-prop.md) oferecem uma plataforma de desenvolvimento de aplicativos móveis altamente escalonável, disponível globalmente para os desenvolvedores corporativos e integradores de sistema e que traz um conjunto rico de recursos para desenvolvedores de aplicativos móveis.

O Visual Studio App Center fornece uma solução móvel mais moderna para os desenvolvedores dos dias atuais. Ele dá suporte a serviços integrados de ponta a ponta que são essenciais para o desenvolvimento de aplicativos móveis. Os desenvolvedores podem usar os serviços de **Build**, **Teste** e **Distribuição** para configurar o pipeline de integração e entrega contínuas. Depois que o aplicativo é implantado, os desenvolvedores podem monitorar o status e o uso dos respectivos aplicativos usando os serviços de **Análise** e **Diagnóstico** e se envolver com usuários usando o serviço de **Push**. Os desenvolvedores também podem aproveitar o serviço de **Autenticação** para autenticar os respectivos usuários e o serviço de **Dados** para persistir e sincronizar dados de aplicativo na nuvem.

> [!NOTE]
> Se desejar integrar os serviços de nuvem em seu aplicativo móvel, inscreva-se no [App Center](https://appcenter.ms/signup?utm_source=NotificationHubs&utm_medium=Azure&utm_campaign=docs) hoje mesmo.

Os desenvolvedores de aplicativos móveis podem utilizar Hubs de notificação com o fluxo de trabalho a seguir:

1. Recuperar o identificador de PNS do dispositivo
2. Registrar o dispositivo com Hubs de Notificação por meio da conveniente API de registro do SDK do cliente de Aplicativos Móveis

    > [!NOTE]
    > Observe que os aplicativos móveis removem imediatamente todas as marcas nos registros para fins de segurança. Trabalhar com Hubs de notificação do seu back-end diretamente para associar marcas a dispositivos.

3. Enviar notificações do seu back-end de aplicativo com Hubs de notificação

Aqui estão alguns recursos convenientes para desenvolvedores com essa integração:

- **SDKs de cliente de Aplicativos Móveis**:  Estes SDKs de várias plataformas fornecem APIs simples para registro e se comunicam com o hub de notificação vinculado automaticamente com o aplicativo móvel. Os desenvolvedores não precisam se aprofundar nas credenciais de Hubs de Notificação e trabalhar com um serviço adicional.
  - *Enviar por push para um usuário*: Os SDKs marcam automaticamente o dispositivo fornecido com a ID de usuário autenticada de Aplicativos Móveis para habilitar o envio por push para o cenário do usuário.
  - *Enviar por push para o dispositivo*: Os SDKs usam automaticamente a ID de instalação de Aplicativos Móveis como GUID para se registrar com Hubs de Notificação, poupando os desenvolvedores da dificuldade de manter vários GUIDs de serviço.
- **Modelo de instalação**:  Os Aplicativos Móveis funcionam com o modelo de push mais recente dos Hubs de Notificação para representar todas as propriedades de push associadas a um dispositivo em uma instalação de JSON que se alinha com Serviços de Notificação por Push e é fácil de usar.
- **Flexibilidade**:  Os desenvolvedores sempre podem optar por trabalhar com Hubs de Notificação diretamente até mesmo com a integração em vigor.
- **Experiência integrada no [portal do Azure](https://portal.azure.com)** :  O Push como um recurso é representado visualmente em aplicativos móveis e os desenvolvedores podem trabalhar facilmente com o hub de notificação associado por meio de aplicativos móveis.
