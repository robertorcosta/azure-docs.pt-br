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
ms.date: 04/02/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/28/2019
ms.openlocfilehash: 855a050afa14144f8963f24398c6b7b3939ef562
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656478"
---
# <a name="change-pricing-tier-of-an-azure-notification-hubs-namespace"></a>Alterar tipo de preço de um namespace de hubs de notificações do Microsoft Azure

Os Hubs de Notificação é oferecido em três tipos: **gratuito**, **básico** e **standard**. Este artigo mostra como alterar o tipo de preço de um namespace de Hubs de Notificação do Microsoft Azure.

## <a name="overview"></a>Visão geral

Em Hubs de Notificação do Microsoft Azure, um hub é o menor recurso/entidade. Ele geralmente mapeia para um aplicativo e pode conter um certificado para cada Sistema de Notificação de Plataforma (PNS) que suportamos para o aplicativo. O aplicativo pode ser um híbrido, ou um nativo e um aplicativo multiplataforma.

Um **namespace** é uma coleção de hubs de notificações. Geralmente, cada namespace é composto por hubs relacionados e usados para um fim específico. Por exemplo, você pode ter três nomes diferentes para fins de desenvolvimento, teste e produção, respectivamente.

Você pode associar um namespace com os níveis de preços **gratuitos,** **básicos**ou **padrão.** É possível usar a camada para um namespace que atenda aos seus requisitos. As seções a seguir mostram como alterar o tipo de preço de um namespace de Hubs de Notificação.

## <a name="use-azure-portal"></a>Usar o portal do Azure

Ao usar o portal Azure, você pode alterar a camada de preços para um namespace na página namespace ou em uma página do hub. Quando você o altera em uma página de hub, você realmente o altera no nível do namespace. Ele altera o tipo de preço do namespace e todos os hubs no namespace.

### <a name="change-tier-on-the-namespace-page"></a>Alterar a camada na página do namespace

O procedimento a seguir mostra como alterar a camada de preços para um namespace na página namespace. Quando você altera o nível para um namespace, ele se aplica a todos os hubs nesse namespace.

1. Entre no [portal do Azure](https://portal.azure.com).
2. Selecione **Todos os serviços** no menu esquerdo.
3. Selecione **Namespaces do Hub de Notificação** na seção **Internet das Coisas**. Se você selecionar o`*`asterisco ( ) ao lado do texto, ele será adicionado à barra de navegação esquerda em **FAVORITES**. Ele ajuda você a acessar a página namespaces mais rapidamente daqui para frente. Depois de adicioná-lo a FAVORITOS, selecione **''''''''''''''''''''''''''''''''**

    ![Todos os serviços -> Namespaces do Hub de Notificação](./media/change-pricing-tier/all-services-nhub.png)

4. Na página **Namespaces do Hub de Notificação**, selecione o namespace para o qual você deseja alterar o tipo de preço.
5. Na página **Namespace do Hub de Notificação** do namespace, é possível ver o tipo de preço atual dele na seção **Essentials**. Na imagem a seguir, é possível ver que o tipo de preço do namespace é **Gratuito**.

    ![Tipo de preço atual na página do namespace](./media/change-pricing-tier/pricing-tier-before.png)

6. Na página **Namespace de Hub de Notificação** do namespace, selecione **Tipo de Preço** na seção **Gerenciar**.

    ![Selecionar o tipo de preço na página do namespace](./media/change-pricing-tier/namespace-select-pricing-menu.png)

7. Altere o nível de preços e clique no botão **Selecionar.**
8. Você pode ver o status da ação de alteração de camada nos **alertas**.
9. Alterne para a página **Visão geral**. Confirme que a nova camada é mostrada no campo **Tipo de Preço** na seção **Essentials**.
10. Esta etapa é opcional. Selecione qualquer hub no namespace. Confirme que você vê o mesmo tipo de preço na seção **Essentials**. Você deve ver o mesmo nível de preços para todos os hubs no namespace.

### <a name="change-tier-on-the-hub-page"></a>Alterar o tipo na página do hub

O procedimento a seguir mostra como alterar a camada de preços para um namespace na página do hub. Mesmo que você siga essas etapas começando da página do hub, na verdade, você alterará o tipo de preço do namespace e todos os hubs no namespace.

1. Entre no [portal do Azure](https://portal.azure.com).
2. Selecione **Todos os serviços** no menu esquerdo.
3. Selecione **Hubs de Notificação** na seção **Internet das Coisas**.
4. Selecione o **hub** de notificação.
5. Selecione **Tipo de Preço** no menu esquerdo.
6. Altere o tipo de preço e clique no botão **Selecionar**. Esta ação altera a configuração do tipo de preço do namespace que contém o hub. Assim, você verá o novo tipo de preço na página do namespace e todas as páginas do hub.

> [!NOTE]
> Todas as alterações de nível de preços são efetivas imediatamente.

## <a name="use-rest-api"></a>Usar a API REST

É possível usar as seguintes APIs REST do Provedor de Recursos para obter o tipo de preço atual e atualizá-lo.

### <a name="get-current-pricing-tier-for-a-namespace"></a>Obter o tipo de preço atual para um namespace

Para obter o nível de namespace atual, envie um comando GET, conforme mostrado no exemplo a seguir:

```REST
GET: https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/notificationhubplan
```

### <a name="update-pricing-tier-for-a-namespace"></a>Atualizar tipo de preço para um namespace

Para atualizar o nível namespace, envie um comando PUT, conforme mostrado no exemplo a seguir:

```REST
PUT: https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/notificationhubplan
Body: <NotificationHubPlan xmlns:i="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"><SKU>Standard</SKU></NotificationHubPlan>
```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre esses tipos e preços, confira [Notification Hubs pricing](https://azure.microsoft.com/pricing/details/notification-hubs/) (Preços dos Hubs de Notificação).
