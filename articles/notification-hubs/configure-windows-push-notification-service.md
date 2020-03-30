---
title: Configure o Serviço de Notificação do Windows Push nos hubs de notificação do Azure | Microsoft Docs
description: Saiba como configurar as configurações do Serviço de Notificação do Windows Push para um hub de notificação do Azure.
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
ms.openlocfilehash: 73304e191242725c80204efb132c26aede9ce7e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127320"
---
# <a name="configure-windows-push-notification-service-settings-in-the-azure-portal"></a>Configure as configurações do Serviço de Notificação do Windows Push no portal Azure

Este artigo mostra como configurar as configurações do Serviço de Notificação do Windows (WNS) para um hub de notificação do Azure usando o portal Azure.  

## <a name="prerequisites"></a>Pré-requisitos
Se você ainda não criou um hub de notificação, crie um agora. Para obter mais informações, consulte [Criar um hub de notificação do Azure no portal do Azure](create-notification-hub-portal.md). 

## <a name="configure-windows-push-notification-service-wns"></a>Configurar o Serviço de Notificação do Windows Push (WNS)

O procedimento a seguir fornece etapas para configurar as configurações do Windows Push Notification Service (WNS) para um hub de notificação: 

1. No portal Azure, na página **Do Centro de Notificação,** selecione **Windows (WNS)** no menu esquerdo.
2. Insira valores para **SID do Pacote** e **Chave de Segurança**.
3. Selecione **Salvar**.

   ![Captura de tela que mostra as caixas SID do pacote e as chaves de segurança](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

## <a name="next-steps"></a>Próximas etapas
Para obter um tutorial com instruções passo a passo para empurrar notificações para aplicativos da Universal Windows Platform usando o Azure Notification Hubs e o Windows Push Notification Service (WNS), consulte [Enviar notificações aos aplicativos UWP usando os Hubs de Notificação do Azure](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).


