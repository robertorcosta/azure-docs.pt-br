---
title: Monitoramento e gerenciamento de sessões do Azure Bastion | Microsoft Docs
description: Neste artigo, aprenda a selecionar uma sessão em andamento e desconecte-a à força ou exclua-a.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: f56a724174b81966642b2870360014597436eade
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76981082"
---
# <a name="session-monitoring-and-management-for-azure-bastion"></a>Monitoramento de sessão e gerenciamento para O Bastião do Azure

Uma vez que o serviço Bastion seja provisionado e implantado em sua rede virtual, você pode usá-lo para se conectar perfeitamente a qualquer VM nesta rede virtual. À medida que os usuários se conectam às cargas de trabalho, o Azure Bastion pode ser usado para monitorar as sessões remotas e tomar ações rápidas de gerenciamento. O monitoramento da sessão do Azure Bastion permite que você veja quais usuários estão conectados a quais VMs. Ele mostra o IP do qual o usuário se conectou, há quanto tempo está conectado e quando está conectado. A experiência de gerenciamento de sessão permite selecionar uma sessão em andamento e desconectar ou excluir uma sessão à força para desconectar o usuário da sessão em curso.

## <a name="monitor-remote-sessions"></a><a name="monitor"></a>Monitore sessões remotas

1. No [portal Azure,](https://portal.azure.com)navegue até o recurso Do Azure Bastion e selecione **Sessões** na página do Azure Bastion.

   ![sessões](./media/session-monitoring/sessions.png)
2. Na página **Sessões,** você pode ver as sessões remotas em andamento no lado direito.

   ![ver sessão](./media/session-monitoring/view-session.png)
3. Selecione **Atualizar** para ver a lista atualizada de sessões remotas. Quando você selecionar Atualizar, o Azure Bastion buscará as informações de monitoramento mais recentes e atualizá-la no portal.

   ![refresh](./media/session-monitoring/refresh.png)

>[!IMPORTANT]
> Habilite a porta 4443 para tráfego de entrada do Gateway Manager para que o monitoramento de sessão funcione.
>

## <a name="delete-or-force-disconnect-an-ongoing-remote-session"></a><a name="view"></a>Excluir ou desconectar à força uma sessão remota em curso

Você pode selecionar um conjunto de sessões e desconectá-las à força. As seguintes etapas mostram como excluir sessões remotas:

1. Navegue até o recurso do Azure Bastion e selecione **Sessões** na página do Azure Bastion.

   ![navigate](./media/session-monitoring/navigate.png)
2. Depois de selecionar Sessões, você verá uma lista de sessões remotas.

   ![sessões de lista](./media/session-monitoring/list.png)
3. Selecione uma sessão remota específica e selecione as três elipses na extremidade direita da linha de sessão e, em seguida, **selecione Excluir**.

   ![excluir](./media/session-monitoring/delete.png)
4. Quando você selecionar Excluir, a sessão remota será desconectada e o usuário será mostrado uma mensagem "Você foi desconectado" na sessão remota.

   ![desconectar](./media/session-monitoring/disconnect.png)

## <a name="next-steps"></a>Próximas etapas

Leia o [Bastião FAQ](bastion-faq.md).