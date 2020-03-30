---
title: Aprovar pedidos para funções de recursos do Azure no PIM - Azure AD | Microsoft Docs
description: Saiba como aprovar ou negar solicitações para funções de recurso do Azure no Azure AD PIM (Privileged Identity Management).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e779f633efccf7b594c193e165a584d22b1d653b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74021979"
---
# <a name="approve-or-deny-requests-for-azure-resource-roles-in-privileged-identity-management"></a>Aprovar ou negar pedidos de funções de recursos do Azure no Gerenciamento de Identidade Privilegiada

Com o Privd Identity Management (PIM) no Azure Active Directory (Azure AD), você pode configurar funções para exigir aprovação para ativação e escolher usuários ou grupos da organização Azure AD como aprovadores delegados. Recomendamos selecionar dois ou mais aprovadores para cada função para reduzir a carga de trabalho para o administrador de funções privilegiado. Os aprovadores representantes têm 24 horas para aprovar as solicitações. Se a solicitação não for aprovada dentro de 24 horas, o usuário qualificado deverá enviar outra. A janela de tempo de aprovação de 24 horas não é configurável.

Siga as etapas neste artigo para aprovar ou negar solicitações de funções de recursos do Azure.

## <a name="view-pending-requests"></a>Exibir solicitações pendentes

Como um aprovador delegado, você receberá uma notificação por email quando uma solicitação de função de recurso do Azure estiver aguardando a aprovação. Você pode visualizar essas solicitações pendentes no Gerenciamento de Identidade Privilegiada.

1. Faça login no [portal Azure](https://portal.azure.com/).

1. Abra **o Azure AD Privileged Identity Management**.

1. Selecione **Solicitar solicitações**.

    ![Aprovar solicitações - Página de recursos do Azure mostrando pedido de revisão](./media/pim-resource-roles-approval-workflow/resources-approve-requests.png)

    Na seção **Solicitações para ativações de função** você verá uma lista de solicitações aguardando a aprovação.

## <a name="approve-requests"></a>Aprovar solicitações

1. Encontre e selecione a solicitação que deseja aprovar. Uma página de aprovação ou negação é exibida.

    ![Aprovar pedidos - aprovar ou negar painel com detalhes e caixa de justificativa](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. Na caixa **de Justificativa,** digite a justificativa do negócio.

1. Selecione **Aprovar**. Você receberá uma notificação do Azure de sua aprovação.

    ![A notificação de aprovação mostrando pedido foi aprovada](./media/pim-resource-roles-approval-workflow/resources-approve-notification.png)

## <a name="deny-requests"></a>Negar solicitações

1. Encontre e selecione a solicitação que você deseja negar. Uma página de aprovação ou negação é exibida.

    ![Aprovar pedidos - aprovar ou negar painel com detalhes e caixa de justificativa](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. Na caixa **de Justificativa,** digite a justificativa do negócio.

1. Selecione **Negar**. Uma notificação é exibida com a negação.

## <a name="workflow-notifications"></a>Notificações de fluxo de trabalho

Veja algumas informações sobre notificações de fluxo de trabalho:

- Os aprovadores são notificados por e-mail quando uma solicitação de uma função está pendente de sua revisão. As notificações por email incluem um link direto para a solicitação no qual o aprovador pode aprovar ou negar.
- Os pedidos são resolvidos pelo primeiro aprovador que aprova ou nega.
- Quando um aprovador responde ao pedido, todos os aprovadores são notificados da ação.
- Os administradores de recursos são notificados quando um usuário aprovado se torna ativo em sua função.

>[!Note]
>Um administrador de recursos que acredita que um usuário aprovado não deve estar ativo pode remover a atribuição de função ativa no Gerenciamento de Identidade Privilegiada. Embora os administradores de recursos não sejam notificados de solicitações pendentes, a menos que sejam um aprovador, eles podem visualizar e cancelar solicitações pendentes para todos os usuários, visualizando solicitações pendentes no Gerenciamento de Identidade Privilegiada.

## <a name="next-steps"></a>Próximas etapas

- [Estender ou renovar funções de recursos do Azure no Gerenciamento de Identidade Privilegiada](pim-resource-roles-renew-extend.md)
- [Notificações de e-mail em Gerenciamento de Identidade Privilegiada](pim-email-notifications.md)
- [Aprovar ou negar pedidos para funções do Azure AD no Gerenciamento de Identidade Privilegiada](azure-ad-pim-approval-workflow.md)
