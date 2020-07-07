---
title: Configurar o serviço de notificação por push da Microsoft nos hubs de notificação do Azure | Microsoft Docs
description: Saiba como definir as configurações do serviço de notificação por push da Microsoft para um hub de notificação do Azure.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "80127336"
---
# <a name="configure-microsoft-push-notification-service-mpns-settings-in-the-azure-portal"></a>Definir as configurações do serviço de notificação por push da Microsoft (MPNS) no portal do Azure

Este artigo mostra como definir as configurações do MPNS (serviço de notificação por push da Microsoft) para um hub de notificação do Azure usando o portal do Azure. 

## <a name="prerequisites"></a>Pré-requisitos
Se você ainda não criou um hub de notificação, crie um agora. Para obter mais informações, consulte [Criar um hub de notificação do Azure no portal do Azure](create-notification-hub-portal.md). 

## <a name="configure-microsoft-push-notification-service-mpns"></a>Configurar o serviço de notificação por push da Microsoft (MPNS)

O procedimento a seguir fornece as etapas para definir as configurações do serviço de notificação por push da Microsoft (MPNS) para um hub de notificação: 

1. Na portal do Azure, na página **Hub de notificação** , selecione **Windows Phone (MPNS)** no menu à esquerda.
1. Habilitar notificações por push autenticadas ou não autenticadas:

   a. Para habilitar as notificações por push não autenticadas, selecione **Habilitar envio por push não autenticado** > **Salvar**.

      ![Captura de tela que mostra como habilitar as notificações por push](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

   b. Habilite as notificações por push autenticadas:
      * Na barra de ferramentas, selecione **Carregar Certificado**.
      * Selecione o ícone de arquivo e selecione o arquivo de certificado.
      * Insira a senha para o certificado.
      * Selecione **OK**.
      * Na página **Windows Phone (MPNS)** , selecione **Salvar**.

## <a name="next-steps"></a>Próximas etapas
Para obter um tutorial com as instruções passo a passo para enviar notificações por push para Windows Phone dispositivos usando os hubs de notificação do Azure e o MPNS (serviço de notificação por push da Microsoft), consulte [notificações por push para Windows Phone aplicativos usando os hubs de notificação](notification-hubs-windows-mobile-push-notifications-mpns.md).

