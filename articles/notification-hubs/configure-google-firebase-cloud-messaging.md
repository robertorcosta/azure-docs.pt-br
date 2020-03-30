---
title: Configure o Google Firebase Cloud Messaging nos hubs de notificação do Azure | Microsoft Docs
description: Saiba como configurar um centro de notificação do Azure com as configurações de Mensagens em Nuvem do Google Firebase.
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
ms.openlocfilehash: 1adbce654bc5c057270df9a874911731a0135034
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127463"
---
# <a name="configure-google-firebase-settings-for-a-notification-hub-in-the-azure-portal"></a>Configure as configurações do Google Firebase para um hub de notificação no portal Azure

Este artigo mostra como configurar as configurações do Google Firebase Cloud Messaging (FCM) para um hub de notificação do Azure usando o portal Azure.  

## <a name="prerequisites"></a>Pré-requisitos
Se você ainda não criou um hub de notificação, crie um agora. Para obter mais informações, consulte [Criar um hub de notificação do Azure no portal do Azure](create-notification-hub-portal.md). 

## <a name="configure-google-firebase-cloud-messaging-fcm"></a>Configure o Google Firebase Cloud Messaging (FCM)

O procedimento a seguir fornece etapas para configurar as configurações do Google Firebase Cloud Messaging (FCM) para um hub de notificação: 

1. No portal Azure, na página **do Centro de Notificação,** selecione **o Google (GCM/FCM)** no menu esquerdo. 
2. Cole a **chave de API** do projeto do FCM salva anteriormente. 
3. Selecione **Salvar**. 

   ![Captura de tela que mostra como configurar os Hubs de Notificação para Google FCM](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)

## <a name="next-steps"></a>Próximas etapas
Para obter um tutorial com instruções passo a passo para empurrar notificações para dispositivos Android usando o Azure Notification Hubs e o Google Firebase Cloud Messaging, consulte [notificações push para dispositivos Android usando Hubs de Notificação e Google FCM](notification-hubs-android-push-notification-google-fcm-get-started.md).

