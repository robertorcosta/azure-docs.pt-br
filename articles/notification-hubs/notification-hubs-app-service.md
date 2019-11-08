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
ms.openlocfilehash: 09ecc258001dd735b920c3a7cb51d8ce7443ceb4
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73686531"
---
# <a name="integration-with-app-service-mobile-apps"></a>Integração com Aplicativos Móveis do Serviço de Aplicativo

Para facilitar uma experiência integrada e unificada nos serviços do Azure, os [Aplicativos Móveis do Serviço de Aplicativo](../app-service-mobile/app-service-mobile-value-prop.md) possui suporte interno para notificações de push usando Hubs de notificação. [Aplicativos Móveis do Serviço de Aplicativo](../app-service-mobile/app-service-mobile-value-prop.md) oferecem uma plataforma de desenvolvimento de aplicativos móveis altamente escalonável, disponível globalmente para os desenvolvedores corporativos e integradores de sistema e que traz um conjunto rico de recursos para desenvolvedores de aplicativos móveis.

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
