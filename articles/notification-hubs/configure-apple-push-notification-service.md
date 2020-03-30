---
title: Configure o serviço de notificação do Apple Push nos hubs de notificação do Azure | Microsoft Docs
description: Saiba como configurar um hub de notificação do Azure com as configurações do Apple Push Notification Service (APNS).
services: notification-hubs
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: eb1122ba3de0002507589d3e607d1e39d905c308
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127526"
---
# <a name="configure-apple-push-notification-service-settings-for-a-notification-hub-in-the-azure-portal"></a>Configure as configurações do Serviço de Notificação do Apple Push para um hub de notificação no portal Azure

Este artigo mostra como configurar as configurações do Apple Push Notification Service (APNS) para um hub de notificação do Azure usando o portal Azure. 

## <a name="prerequisites"></a>Pré-requisitos
Se você ainda não criou um hub de notificação, crie um agora. Para obter mais informações, consulte [Criar um hub de notificação do Azure no portal do Azure](create-notification-hub-portal.md). 

## <a name="configure-apple-push-notification-service"></a>Configurar o serviço de notificação push da Apple

O procedimento a seguir fornece etapas para configurar as configurações do Apple Push Notification Service (APNS) para um hub de notificação:

1. No portal Azure, na página **Do Centro de Notificação,** selecione **Apple (APNS)** no menu esquerdo.

1. Para o **Modo de Autenticação**, selecione **Certificado** ou **Token**.

   a. Se você selecionar **Certificado**:
   * Selecione o ícone de arquivo, e em seguida, o arquivo *.p12* que você deseja carregar.
   * Digite uma senha.
   * Selecione o modo **Sandbox**. Ou, para enviar notificação por push aos usuários que compraram seu aplicativo da loja, selecione o modo **Produção**.

     ![Captura de tela de uma configuração de certificado APN no portal do Azure](./media/configure-apple-push-notification-service/notification-hubs-apple-config-cert.png)

   b. Se você selecionar **Token**:

   * Digite os valores para **ID de chave,** **ID do pacote,** **ID da equipe**e **Token**.
   * Selecione o modo **Sandbox**. Ou, para enviar notificação por push aos usuários que compraram seu aplicativo da loja, selecione o modo **Produção**.

     ![Captura de tela de uma configuração de token APNS no portal do Azure](./media/configure-apple-push-notification-service/notification-hubs-apple-config-token.png)

## <a name="next-steps"></a>Próximas etapas
Para obter um tutorial com instruções passo a passo para empurrar notificações para dispositivos iOS, consulte o seguinte artigo: [Push notifications para dispositivos iOS usando Hubs](notification-hubs-ios-apple-push-notification-apns-get-started.md) de Notificação e APNS
