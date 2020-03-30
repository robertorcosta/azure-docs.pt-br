---
title: Configure o serviço de notificação do Microsoft Push nos hubs de notificação do Azure | Microsoft Docs
description: Saiba como configurar as configurações do Serviço de Notificação do Microsoft Push para um hub de notificação do Azure.
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
ms.openlocfilehash: 99f29e7910fe6070c6202f6a936173455f979732
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127336"
---
# <a name="configure-microsoft-push-notification-service-mpns-settings-in-the-azure-portal"></a>Configure as configurações do Microsoft Push Notification Service (MPNS) no portal Azure

Este artigo mostra como configurar as configurações do Microsoft Push Notification Service (MPNS) para um hub de notificação do Azure usando o portal Azure. 

## <a name="prerequisites"></a>Pré-requisitos
Se você ainda não criou um hub de notificação, crie um agora. Para obter mais informações, consulte [Criar um hub de notificação do Azure no portal do Azure](create-notification-hub-portal.md). 

## <a name="configure-microsoft-push-notification-service-mpns"></a>Configurar o Microsoft Push Notification Service (MPNS)

O procedimento a seguir fornece etapas para configurar as configurações do Microsoft Push Notification Service (MPNS) para um hub de notificação: 

1. No portal Azure, na página **Do Centro de Notificação,** selecione **O Windows Phone (MPNS)** no menu esquerdo.
1. Habilitar notificações por push autenticadas ou não autenticadas:

   a. Para habilitar notificações push não autenticadas, **selecione Ativar push** > **Save**.

      ![Captura de tela que mostra como habilitar as notificações por push](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

   b. Habilite as notificações por push autenticadas:
      * Na barra de ferramentas, selecione **Carregar Certificado**.
      * Selecione o ícone de arquivo e selecione o arquivo de certificado.
      * Insira a senha para o certificado.
      * Selecione **OK**.
      * Na página **Windows Phone (MPNS)**, selecione **Salvar**.

## <a name="next-steps"></a>Próximas etapas
Para obter um tutorial com instruções passo a passo para empurrar notificações para dispositivos Windows Phone usando o Azure Notification Hubs e o Microsoft Push Notification Service (MPNS), consulte [notificações push para aplicativos do Windows Phone usando hubs de notificação](notification-hubs-windows-mobile-push-notifications-mpns.md).

