---
title: Aprovar ou negar pedidos de azure AD papéis no PIM - Azure AD | Microsoft Docs
description: Saiba como aprovar ou negar pedidos de funções azure AD no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/07/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 73ccfd1ad270072989e9b575fda538b94fd8927c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80049011"
---
# <a name="approve-or-deny-requests-for-azure-ad-roles-in-privileged-identity-management"></a>Aprovar ou negar pedidos para funções do Azure AD no Gerenciamento de Identidade Privilegiada

Com o Azure Active Directory (Azure AD) Privileged Identity Management (PIM), você pode configurar funções para exigir aprovação para ativação e escolher um ou vários usuários ou grupos como aprovadores delegados. Os aprovadores representantes têm 24 horas para aprovar as solicitações. Se a solicitação não for aprovada dentro de 24 horas, o usuário qualificado deverá enviar outra. A janela de tempo de aprovação de 24 horas não é configurável.

## <a name="determine-your-version-of-pim"></a>Determine sua versão do PIM

A partir de novembro de 2019, a parte de funções azure AD do Privileged Identity Management está sendo atualizada para uma nova versão que corresponde às experiências para funções do Azure. Isso cria recursos adicionais, bem como [alterações na API existente](azure-ad-roles-features.md#api-changes). Enquanto a nova versão está sendo lançada, quais procedimentos você segue neste artigo dependem da versão do Gerenciamento de Identidade Privilegiada que você tem atualmente. Siga as etapas nesta seção para determinar qual versão do Gerenciamento de Identidade Privilegiada você tem. Depois de conhecer sua versão do Gerenciamento de Identidade Privilegiada, você pode selecionar os procedimentos neste artigo que correspondem a essa versão.

1. Faça login no [portal Azure](https://portal.azure.com/) com um usuário que esteja na função [de administrador de funções privilegiadas.](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)
1. Abra **o Azure AD Privileged Identity Management**. Se você tiver um banner na parte superior da página de visão geral, siga as instruções na guia **Nova versão** deste artigo. Caso contrário, siga as instruções na guia **versão anterior.**

    [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

Siga os passos deste artigo para aprovar ou negar pedidos de funções ad do Azure.

# <a name="new-version"></a>[Nova versão](#tab/new)

## <a name="view-pending-requests"></a>Exibir solicitações pendentes

Como um aprovador delegado, você receberá uma notificação por e-mail quando uma solicitação de função Azure AD estiver pendente de sua aprovação. Você pode visualizar essas solicitações pendentes no Gerenciamento de Identidade Privilegiada.

1. Faça login no [portal Azure](https://portal.azure.com/).

1. Abra **o Azure AD Privileged Identity Management**.

1. Selecione **Solicitar solicitações**.

    ![Aprovar solicitações - página mostrando pedido para revisar funções do Azure AD](./media/azure-ad-pim-approval-workflow/resources-approve-pane.png)

    Na seção **Solicitações para ativações de função** você verá uma lista de solicitações aguardando a aprovação.

## <a name="approve-requests"></a>Aprovar solicitações

1. Encontre e selecione a solicitação que deseja aprovar. Uma página de aprovação ou negação é exibida.

    ![Aprovar pedidos - aprovar ou negar painel com detalhes e caixa de justificativa](./media/azure-ad-pim-approval-workflow/resources-approve-pane.png)

1. Na caixa **de Justificativa,** digite a justificativa do negócio.

1. Selecione **Aprovar**. Você receberá uma notificação do Azure de sua aprovação.

    ![A notificação de aprovação mostrando pedido foi aprovada](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png))

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
- Os admins globais e os admins de função privilegiados são notificados quando um usuário aprovado se torna ativo em sua função.

>[!NOTE]
>Um administrador global ou administrador de função privilegiada que acredita que um usuário aprovado não deve estar ativo pode remover a atribuição de função ativa no Gerenciamento de Identidade Privilegiada. Embora os administradores não sejam notificados de solicitações pendentes, a menos que sejam um aprovador, eles podem visualizar e cancelar quaisquer solicitações pendentes para todos os usuários, visualizando solicitações pendentes no Gerenciamento de Identidade Privilegiada.

# <a name="previous-version"></a>[Versão anterior](#tab/previous)

## <a name="view-pending-requests"></a>Exibir solicitações pendentes

Como um aprovador delegado, você receberá uma notificação por e-mail quando uma solicitação de função Azure AD estiver pendente de sua aprovação. Você pode visualizar essas solicitações pendentes no Gerenciamento de Identidade Privilegiada.

1. Faça login no [portal Azure](https://portal.azure.com/).

1. Abra **o Azure AD Privileged Identity Management**.

1. Clique em **funções do Microsoft Azure Active Directory**.

1. Clique em **aprovar solicitações**.

    ![Funções azure AD - Aprovar solicitações](./media/azure-ad-pim-approval-workflow/approve-requests.png)

    Você verá uma lista de solicitações aguardando sua aprovação.

## <a name="approve-requests"></a>Aprovar solicitações

1. Selecione as solicitações que você deseja aprovar e, em seguida, clique em **aprovar** abrir a aprovar selecionado no painel de solicitações.

    ![Aprovar lista de solicitações com opção Aprovar destacada](./media/azure-ad-pim-approval-workflow/pim-approve-requests-list.png)

1. No **razão da aprovação** , digite um motivo.

    ![Aprovar pedidos selecionados painel com um motivo de aprovação](./media/azure-ad-pim-approval-workflow/pim-approve-selected-requests.png)

1. Clique **em Aprovar**.

    O símbolo de status será atualizado com sua aprovação.

    ![Aprovar painel de solicitações selecionadas após o botão Aprovar clicado](./media/azure-ad-pim-approval-workflow/pim-approve-status.png)

## <a name="deny-requests"></a>Negar solicitações

1. Selecione as solicitações que você deseja negar e clique em **Negar** para abrir o painel Negar solicitações selecionadas.

    ![Aprovar lista de solicitações com opção Deny destacada](./media/azure-ad-pim-approval-workflow/pim-deny-requests-list.png)

1. Na caixa **Negar motivo**, digite um motivo.

    ![Negar pedidos selecionados painel com uma razão de negação](./media/azure-ad-pim-approval-workflow/pim-deny-selected-requests.png)

1. Selecione **Negar**.

    Símbolo de Status será atualizado com a negação.

---

## <a name="next-steps"></a>Próximas etapas

- [Notificações de e-mail em Gerenciamento de Identidade Privilegiada](pim-email-notifications.md)
- [Aprovar ou negar pedidos de funções de recursos do Azure no Gerenciamento de Identidade Privilegiada](pim-resource-roles-approval-workflow.md)
