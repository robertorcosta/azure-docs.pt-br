---
title: Configurar o serviço de notificação por push do Windows nos hubs de notificação do Azure | Microsoft Docs
description: Saiba como definir as configurações do serviço de notificação por push do Windows para um hub de notificação do Azure.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "80127320"
---
# <a name="configure-windows-push-notification-service-settings-in-the-azure-portal"></a>Definir as configurações do serviço de notificação por push do Windows no portal do Azure

Este artigo mostra como definir as configurações do WNS (serviço de notificação do Windows) para um hub de notificação do Azure usando o portal do Azure.  

## <a name="prerequisites"></a>Pré-requisitos
Se você ainda não criou um hub de notificação, crie um agora. Para obter mais informações, consulte [Criar um hub de notificação do Azure no portal do Azure](create-notification-hub-portal.md). 

## <a name="configure-windows-push-notification-service-wns"></a>Configurar o serviço de notificação por push do Windows (WNS)

O procedimento a seguir fornece as etapas para definir as configurações de WNS (serviço de notificação por push do Windows) para um hub de notificação: 

1. Na portal do Azure, na página **Hub de notificação** , selecione **Windows (WNS)** no menu à esquerda.
2. Insira valores para **SID do Pacote** e **Chave de Segurança**.
3. Clique em **Salvar**.

   ![Captura de tela que mostra as caixas SID do pacote e as chaves de segurança](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

## <a name="next-steps"></a>Próximas etapas
Para obter um tutorial com as instruções passo a passo para enviar notificações por push para Plataforma Universal do Windows aplicativos usando os hubs de notificação do Azure e o WNS (serviço de notificação por push do Windows), consulte [enviar notificações para aplicativos UWP usando os hubs de notificação do Azure](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).


