---
title: Configure o Baidu Cloud Push nos hubs de notificação do Azure | Microsoft Docs
description: Saiba como configurar as configurações do Baidu para um hub de notificação do Azure.
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: 759e35ba353f470ea3abc5f5d4182fa2b2ea0e73
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "71212508"
---
# <a name="deprecated-configure-baidu-cloud-push-settings-for-a-notification-hub-in-the-azure-portal"></a>Depreciado: Configure as configurações do Baidu Cloud Push para um hub de notificação no portal Azure

Este artigo mostra como configurar as configurações do Baidu Cloud Push para um hub de notificação do Azure usando o portal Azure.

> [!IMPORTANT]
> Este tutorial é preterido. 

## <a name="prerequisites"></a>Pré-requisitos
Se você ainda não criou um hub de notificação, crie um agora. Para obter mais informações, consulte [Criar um hub de notificação do Azure no portal do Azure](create-notification-hub-portal.md). 

## <a name="configure-baidu-cloud-push"></a>Configurar o push da nuvem Baidu
O procedimento a seguir fornece etapas para configurar as configurações do Baidu Cloud Push para um hub de notificação:

1. No portal Azure, na página **do Centro de Notificação,** selecione **Baidu (Android China)** no menu esquerdo. 
2. Insira a **chave de API** obtida do console do Baidu no projeto de push da nuvem Baidu. 
3. Insira a **chave secreta** obtida do console do Baidu no projeto de push da nuvem Baidu. 
4. Selecione **Salvar**. 

    ![Captura de tela de Hubs de Notificação que mostra a configuração para notificações por push do Baidu (Android China)](./media/notification-hubs-baidu-get-started/AzureNotificationServicesBaidu.png)

## <a name="next-steps"></a>Próximas etapas
Para obter um tutorial com instruções passo a passo para empurrar notificações para a Baidu usando o Azure Notification Hubs e o Baidu Cloud Push, consulte [Iniciar com hubs de notificação usando o Baidu](notification-hubs-baidu-china-android-notifications-get-started.md).
