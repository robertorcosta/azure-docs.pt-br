---
title: Alterar tipo de preço do namespace de Hubs de Notificação | Microsoft Docs
description: Saiba como alterar o nível de preços de um namespace do Azure Notification Hubs.
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 01/28/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/28/2019
ms.openlocfilehash: 51d0880b7f56d523a01fbc993993b3caf7328134
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76261068"
---
# <a name="change-pricing-tier-of-an-azure-notification-hubs-namespace"></a>Alterar tipo de preço de um namespace de hubs de notificações do Microsoft Azure
Os Hubs de Notificação é oferecido em três tipos: **gratuito**, **básico** e **standard**. Este artigo mostra como alterar o tipo de preço de um namespace de Hubs de Notificação do Microsoft Azure. 

## <a name="overview"></a>Visão geral
Em Hubs de Notificação do Microsoft Azure, um **hub** é o menor recurso/entidade. Ele geralmente é mapeado para um aplicativo e pode armazenar um certificado para cada Sistema de Notificação de Plataforma compatível com o aplicativo. O aplicativo pode ser híbrido ou nativo e um aplicativo multiplataforma.

Um **namespace** é uma coleção de hubs de notificações. Geralmente, cada namespace é composto por hubs relacionados e usados para um fim específico. Por exemplo, você poderia ter três namespaces diferentes para fins de desenvolvimento, teste e produção, respectivamente. 

É possível associar um tipo de preço no nível do namespace. Os Hubs de Notificação é compatível com três tipos: **gratuito**, **básico** e **standard**. É possível usar a camada para um namespace que atenda aos seus requisitos. As seções a seguir mostram como alterar o tipo de preço de um namespace de Hubs de Notificação. 

## <a name="use-azure-portal"></a>Usar o portal do Azure 
Ao usar o portal do Azure, é possível alterar o tipo de preço de um namespace na página do namespace ou em uma página de hub.  Quando você o altera em uma página de hub, você realmente o altera no nível do namespace. Ele altera o tipo de preço do namespace e todos os hubs no namespace. 

### <a name="change-tier-on-the-namespace-page"></a>Alterar a camada na página do namespace
O procedimento a seguir fornece as etapas para alterar o tipo de preço de um namespace na página do namespace. Quando você altera o tipo de um namespace, isso se aplica a todos os hubs no namespace.

1. Faça login no [portal Azure](https://portal.azure.com).
2. Selecione **Todos os serviços** no menu esquerdo. 
3. Selecione **Namespaces do Hub de Notificação** na seção **Internet das Coisas**. Se você selecionar a estrela (`*`) ao lado do texto, ela será adicionada à barra de navegação esquerda em **FAVORITOS**. Isso ajuda você a acessar a página de namespaces mais rápido na próxima vez. Após adicioná-la aos FAVORITOS, selecione **Namespaces do Hub de Notificação**. 

    ![Todos os serviços -> Namespaces do Hub de Notificação](./media/change-pricing-tier/all-services-nhub.png)
1. Na página **Namespaces do Hub de Notificação**, selecione o namespace para o qual você deseja alterar o tipo de preço. 
2. Na página **Namespace do Hub de Notificação** do namespace, é possível ver o tipo de preço atual dele na seção **Essentials**. Na imagem a seguir, é possível ver que o tipo de preço do namespace é **Gratuito**. 

    ![Tipo de preço atual na página do namespace](./media/change-pricing-tier/pricing-tier-before.png)
1. Na página **Namespace de Hub de Notificação** do namespace, selecione **Tipo de Preço** na seção **Gerenciar**. 

    ![Selecionar o tipo de preço na página do namespace](./media/change-pricing-tier/namespace-select-pricing-menu.png)
6. Altere o tipo de preço e clique no botão **Selecionar**.    
7. Você verá o status da ação de alteração de tipo nos **alertas**. 
8. Alterne para a página **Visão geral**. Confirme que a nova camada é mostrada no campo **Tipo de Preço** na seção **Essentials**.     
1. Esta etapa é opcional. Selecione qualquer hub no namespace. Confirme que você vê o mesmo tipo de preço na seção **Essentials**. Você deve ver o mesmo tipo de preço para todos os hubs no namespace. 

### <a name="change-tier-on-the-hub-page"></a>Alterar o tipo na página do hub
O procedimento a seguir fornece as etapas para alterar o tipo de preço de um namespace na página do hub. Mesmo que você siga essas etapas começando da página do hub, na verdade, você alterará o tipo de preço do namespace e todos os hubs no namespace. 

1. Faça login no [portal Azure](https://portal.azure.com).
2. Selecione **Todos os serviços** no menu esquerdo.
3. Selecione **Hubs de Notificação** na seção **Internet das Coisas**. 
4. Selecione o **hub** de notificação. 
5. Selecione **Tipo de Preço** no menu esquerdo. 
6. Altere o tipo de preço e clique no botão **Selecionar**. Esta ação altera a configuração do tipo de preço do namespace que contém o hub. Assim, você verá o novo tipo de preço na página do namespace e todas as páginas do hub. 

## <a name="use-rest-api"></a>Usar a API REST
É possível usar as seguintes APIs REST do Provedor de Recursos para obter o tipo de preço atual e atualizá-lo. 

### <a name="get-current-pricing-tier-for-a-namespace"></a>Obter o tipo de preço atual para um namespace
Para obter o **tipo do namespace atual**, envie um comando GET, como mostrado no exemplo a seguir: 

```REST
GET: https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/notificationhubplan
```

### <a name="update-pricing-tier-for-a-namespace"></a>Atualizar tipo de preço para um namespace
Para **atualizar o tipo do namespace**, envie um comando PUT, como mostrado no exemplo a seguir: 

```REST
PUT: https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/notificationhubplan
Body: <NotificationHubPlan xmlns:i="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"><SKU>Standard</SKU></NotificationHubPlan>
```



## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre esses tipos e preços, confira [Notification Hubs pricing](https://azure.microsoft.com/pricing/details/notification-hubs/) (Preços dos Hubs de Notificação).
